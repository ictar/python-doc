原文：[Django, ELB health checks and continuous delivery](http://tech.octopus.energy/2016/05/05/django-elb-health-checks.html)

---

使用Amazon Web Services部署web应用的一个强大的手段是使用一个[弹性负载均衡器(Elastic Load Balancer)](https://aws.amazon.com/elasticloadbalancing/) (ELB)来平衡EC2实例的“Auto Scaling Group” (ASG)之间的请求的平衡。和水平扩展一样，这种设置允许自动canary (又名blue-green)部署，其中，新应用版本作为一个新的ASG进行部署，这会用新的替换现存的EC2实例；一种所谓的“不可改变的基础设施”的做法。

这样的过程依赖于ELB “健康检查”请求来测试新的EC2实例准备好了接收生产流量（这样，老的实例就可以终止了）。对于canary部署，健康检查的准确性是非常重要的：误报导致带入生产的挂掉的应用引发问题，宕机时间和其他忧伤的事情。

在[Octopus Energy](https://octopus.energy)上，我们以这种方式部署了Django应用, 作为由Hashicorp出色的[Atlas](https://www.hashicorp.com/atlas.html)服务协作的连续的输送管道的一部分：

  1. CircleCI，我们的持续集成服务，当主应用上的测试通过时，会打包应用，然后上传压缩包到Atlas上。

  2. 然后，使用一组上传配置（例如，Puppet清单和模块），Atlas采用Packer来创建一个新的Amazon Machine Image (AMI)。

  3. 接着，使用Terraform，Atlas部署该AMI到生产。如上所述，Terraform将新的AMI带到生产，创建一个新的ASG，然后加载使用这个新的AMI的配置。


过去几个月里，该过程的不断发展，已经凸显了正确进行健康检查的重要性。下面是一些提示。使用和uWSGI和NGINX一起运行的一个样例Django应用，但大多数的建议转化为其他框架和HTTP服务器。

虽然本文名义上是关于健康检查，但是TLDR是那种你可以使用Hashicorp的产品构建伟大的东西。特别是，如果你使用AWS，并且之前没有使用Terraform —— 那么，今天是时候了。

# 一个健康检查Django视图

我们的ELB健康检查在Terraform中配置如下：

```python

    resource "aws_elb" "web" {
    
        ...
    
        health_check {
            # Where to make health check requests to
            target = "HTTP:80/health"
    
            # How often to make health check requests (in seconds)
            interval = 15
    
            # Number of checks before instance is declared healthy
            healthy_threshold = 2
    
            # Number of checks before instance is declared unhealthy
            unhealthy_threshold = 10
    
            # Number of seconds to wait for a healthcheck response
            timeout = 5
        }
    }
```

换句话说，当两个到`/health`的HTTP请求返回一个200状态（5s内），那么一个EC2实例被认为是健康的。

让我们从简单的开始：

```python

    # urls.py
    from . import views
    
    urlpatterns = (
        url(r'^health', views.health),
    )
```

和

```python

    # views.py
    from django import http
    
    def health(request):
        return http.HttpResponse()
```

使用NGINX直接返回健康检查请求的响应，而不用麻烦uWSGI，这样本来是更容易的。但是，通过更深入一层，并且让Django应用来响应，我们会获得更多好处。通过这样做，可以避免一些类问题，因为当uWSGI无法启动该Python应用时，健康检查失败。

## 不健康的实例不能运行Python应用

按照[12因子应用指导原则](http://12factor.net/)，我们的EC2实例是无状态的，并且从环境变量中读取它们的配置。这由Upstart设置，获取由consul模板管理的配置文件：

```python

    # /etc/init/uwsgi
    
    # Ensure the uWSGI process doesn't start until 
    # the consul-template process has started.
    start on started consul-template
    stop on runlevel [06]
    
    respawn
    
    # This is the file consul-template manages
    env ENV_FILE="/etc/application/env-vars"
    env UWSGI_INI_FILE="/etc/uwsgi.ini"
    env VENV_ROOT="/opt/venv"
    
    script
        # Set environment variables 
        source $ENV_FILE
    
        # Apply migrations. Piping the output to logger ensures it get 
        # is included in /var/log/syslog and hence gets forwarded to Loggly.
        sudo -u www-data django-admin migrate --noinput --no-color | logger -t migrations
    
        # Start uWSGI
        exec $VENV_ROOT/bin/uwsgi --ini $UWSGI_INI_FILE
    end script
```

在`settings.py`中使用简单的包装函数，我们确保在缺失/无效配置下，Python应用无法启动：

```python

    # settings.py
    
    import os
    
    def value_from_env(key, default=None):
        """
        Return a env variable, raising an exception if it is not defined
        """
        value = os.environ.get(key, default)
        if value is None:
            error_msg = (
                "No '%s' env variable found. This needs to be set in Consul's "
                "key-value store."
            )
            raise RuntimeError(error_msg % key)
        return value
    
    # Example config look-up
    SECRET_KEY = value_from_env("SECRET_KEY")
```

如果在Consul中，`SECRET_KEY`环境变量无法定义，那么uWSGI将不能够启动Python应用，而健康检查将会失败。这种做法可以保证如果配置缺失，那么canary部署失败。

假设uWSGI可以启动Python应用，让我们将允许Django成功对健康检查进行响应这种设置作为例子。

# NGINX

我们在ELB和到EC2实例的80端口的代理请求终止TLS。对于正常的用户请求，使用`X_FORWARDED_PROTO`头来确保使用TLS。然而，对于健康检查请求，我们不想要这样，所以使用单独的`location`指令：

```python

    # /etc/nginx/sites-enabled/default
    
    # uWSGI is configured to use this socket 
    upstream public {
        server unix:///tmp/uwsgi.sock;
    }
    
    server {
        listen 80 default_server;
    
        server_name localhost octopus.energy;
    
        charset utf-8;
    
        # Allow healthchecks to be made from ELB without X_FORWARDED_PROTO header
        location /health {
            access_log /var/log/nginx/health.log;
    
            uwsgi_pass public; 
            include /etc/nginx/uwsgi_params;
        }
    
        location / {
            # Ensure non-TLS requests are redirected
            if ($http_x_forwarded_proto != 'https') {
                rewrite ^ https://$host$request_uri? permanent;
            } 
    
            uwsgi_pass public; 
            include /etc/nginx/uwsgi_params;
        }
    }
```

这里，我们使用一个单独的日志文件，因为我们不想让健康检查的请求包含在主访问文件中，因此我们作为一个JSON事件流转发到Loggly（这超级有用）。

# 允许的主机

ELB健康检查请求使用EC2实例的私有IP地址作为主机头，因此我们需要确保这样的请求正确被Django应用处理。

对于NGINX，这不是个问题，因为我们代理到包罗万象的虚拟主机中的Django程序（定义的第一个）。

要让Django应用正确响应，私有IP地址必须在`ALLOWED_HOSTS`设置中，否则Django将会反返回一个“400 Bad Request”响应。由于Web服务器是短暂的，这个设置需要动态设置，通常通过在启动过程中调用AWS内部元数据服务。你可以在EC2 “user-data”中进行这样一个请求，然后将值写入到一个配置文件中，或者当导入`settings.py`时，调用该元数据服务。前者可能看起来像这样：

```python

    # userdata.sh
     
    echo "Writing EC2 metadata to files in /etc/aws/"
    mkdir -p /etc/aws/
    ec2metadata --local-ipv4 > /etc/aws/ipv4
```

和

```python

    # settings.py
    
    def value_from_file(filepath, default=None):
        """
        Return a string value from a local file
        """
        if os.path.exists(filepath):
            with open(filepath, "r") as f:
                return f.read().strip()
        return default
    
    # Read local IP address from file created by EC2 user-data script
    AWS_LOCAL_IP = value_from_file("/etc/aws/ipv4")
    
    ALLOWED_HOSTS = [AWS_LOCAL_IP, "octopus.energy"]
```

在这一点上，上面定义的简单的健康检查视图将会愉快地响应请求。现在，让我们扩展健康检查视图的实现。

## 检查网页正确渲染

你可以使用Django测试客户端来在你的网站上运行一个简单的烟雾测试。例如，检查主页负载。

```python

    # check.py
    import logging
    import httplib
    
    logger = logging.getLogger('health')
    
    def page_response(path, expected_status=httplib.OK):
        """
        Make an internal (fake) HTTP request to check a page returns the expected
        status code.
        """
        try:
            response = client.get(path)
        except Exception as e:
            logger.error("Error from %s: %s", path, e)
            return False
    
        result = response.status_code == expected_status
        if not result:
            # Log healthcheck errors to Loggly so we can debug failing deployments
            # where the new instances fail the healthcheck.
            logger.error("Response from %s was %s, not %s",
                         path, response.status_code, status)
        return result
```

我们可以在我们的视图函数中使用这个辅助器：

```python

    # views.py
    import httplib
    
    from django import http
    
    from . import check
    
    def health(request):
        if not check.page_response('/'):
            return http.HttpResponse(status=httplib.SERVICE_UNAVAILABLE)
        return http.HttpResponse()
```

## 检查迁移成功应用

如上所示，当Upstart启动Django应用时，我们试图应用迁移。如果任何这些迁移失败，我们就不想把这台机器放入生产。因此，我们检查未应用的迁移作为健康检查的一部分：

```python

    # check.py
    import logging
    
    from django.db import DEFAULT_DB_ALIAS, connections
    from django.db.migrations.loader import MigrationLoader
    
    logger = logging.getLogger('health')
    
    
    def migrations_have_applied():
        """
        Check if there are any migrations that haven't been applied yet
        """
        connection = connections[DEFAULT_DB_ALIAS]
        loader = MigrationLoader(connection)
        graph = loader.graph
    
        # Count unapplied migrations
        num_unapplied_migrations = 0
        for app_name in loader.migrated_apps:
            for node in graph.leaf_nodes(app_name):
                for plan_node in graph.forwards_plan(node):
                    if plan_node not in loader.applied_migrations:
                        num_unapplied_migrations += 1
    
        return num_unapplied_migrations == 0
```

我们扩展的健康检查视图函数现在看起来是这样的：

```python

    # views.py
    import httplib
    
    from django import http
    
    from . import check
    
    def health(request):
        if not check.page_response('/'):
            return http.HttpResponse(status=httplib.SERVICE_UNAVAILABLE)
        if not check.migrations_have_applied():
            return http.HttpResponse(status=httplib.SERVICE_UNAVAILABLE)
        return http.HttpResponse()
```

好了，就这样：用于Ddjango应用的有效的健康检查视图。
