原文：[Django, ELB health checks and continuous delivery](http://tech.octopus.energy/2016/05/05/django-elb-health-checks.html)

---

May 5, 2016 • [David Winterbottom](https://twitter.com/codeinthehole), Head of
Engineering

A robust means of deploying web applications with Amazon Web Services is to
use an [Elastic Load Balancer](https://aws.amazon.com/elasticloadbalancing/)
(ELB) to balance requests between an “Auto Scaling Group” (ASG) of EC2
instances. As well as horizontally scaling, this set-up allows automated
canary (aka blue-green) deployments, where new application versions are
deployed as a new ASG which replaces the existing EC2 instances with new; a
so-called “immutable infrastructure” approach.

Such a procedure relies on ELB “health check” requests to test that the new
EC2 instances are ready to take production traffic (and the old instances can
be terminated). For canary deployments, it’s important that the health check
is accurate: false positives lead to broken applications being brought into
production causing errors, downtime and other sadness.

At [Octopus Energy](https://octopus.energy), we deploy Django applications in
this way as part of a continuous delivery pipeline coordinated by Hashicorp’s
excellent [Atlas](https://www.hashicorp.com/atlas.html) service:

  1. CircleCI, our continuous integration service, packages up the application when the tests pass on master and uploads the tarball to Atlas.

  2. Atlas then employs Packer with a set of uploaded configuration (e.g. Puppet manifests and modules) to create a new Amazon Machine Image (AMI).

  3. Atlas then deploys this AMI into production using Terraform. Terraform brings the new AMI into production as described above, creating a new ASG and launch configuration that uses the new AMI.

Evolving this process over the last few months has highlighted the importance
of getting the health check right. Below are some tips. An example Django
application run with uWSGI and NGINX is used but most of the advice translates
to other frameworks and HTTP servers.

While this article is nominally about health checks, the TLDR is that you can
build great things with Hashicorp’s products. Specifically, if you use AWS and
haven’t checked out Terraform before - do that today.

# A health-check Django view

Our ELB health check is configured in Terraform as:

[code]

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
[/code]

In other words: an EC2 instance is considered healthy when two HTTP requests
to `/health` return a 200 status (within five seconds).

Let’s start simple:

[code]

    # urls.py
    from . import views
    
    urlpatterns = (
        url(r'^health', views.health),
    )
[/code]

and

[code]

    # views.py
    from django import http
    
    def health(request):
        return http.HttpResponse()
[/code]

It would have been easier to use NGINX to respond to the health-check request
directly without troubling uWSGI. But we get considerably more value by going
a layer deeper and getting the Django application to respond. Several classes
of problem are prevented by doing this since health checks fail when uWSGI
can’t start the Python application.

## Unhealthy instances can’t run the Python application

As per the [12-factor app guidelines](http://12factor.net/), our EC2 instances
are stateless and read their configuration from environment variables. These
are set by Upstart, sourcing a configuration file managed by consul-template:

[code]

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
[/code]

We ensure the Python application cannot start with missing/invalid
configuration using simple wrapper functions in `settings.py`:

[code]

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
[/code]

If the `SECRET_KEY` environmental variable isn’t defined in Consul, uWSGI
won’t be able to start the Python application and health checks will fail.
This practice ensures canary deployments fail if configuration is missing.

Assuming uWSGI can start the Python application, let’s example the set-up that
allows Django to respond successfully to the health check.

# NGINX

We terminate TLS on the ELB and proxy requests to port 80 of the EC2 instance.
For normal user requests, we use the `X_FORWARDED_PROTO` header to ensure TLS
is used. However, we don’t want this for health-check requests so we use a
separate `location` directive:

[code]

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
[/code]

Here we use a separate log file as we don’t want the health check requests
being included in the main access file which we forward as a JSON event stream
to Loggly (this is super-useful).

# Allowed hosts

ELB health-check requests use the private IP address of the EC2 instance as
the host header so we need to ensure such requests are correctly handled by
the Django application.

For NGINX, this isn’t a problem as we proxy to the Django application in the
catch-all virtualhost (the first one defined).

For the Django application to respond correctly, the private IP address must
be in the `ALLOWED_HOSTS` setting or Django will return a “400 Bad Request”
response. Since webservers are ephemeral, this setting needs to be set
dynamically, normally by calling the AWS internal metadata service during
start-up. You can make such a request in the EC2 “user-data” and write the
value to a config file, or call the metadata service when `settings.py` is
imported. The former may look something like:

[code]

    # userdata.sh
     
    echo "Writing EC2 metadata to files in /etc/aws/"
    mkdir -p /etc/aws/
    ec2metadata --local-ipv4 > /etc/aws/ipv4
[/code]

and

[code]

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
[/code]

At this point, the simple health-check view defined above will happily respond
to requests. Let’s now extend the implementation of the health-check view.

## Check pages render correctly

You can use the Django test client to run a simple smoke test on your site.
For example, checking the homepage loads.

[code]

    # check.py
    import logging
    import httplib
    
    logger = logging.getLogger('health')
    
    def page_response(path, expected_status=httplib.OK):
        """
        Make an internal (fake) HTTP request to check a page returns the expected
        status code.
        """"
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
[/code]

We can use this helper in our view function:

[code]

    # views.py
    import httplib
    
    from django import http
    
    from . import check
    
    def health(request):
        if not check.page_response('/'):
            return http.HttpResponse(status=httplib.SERVICE_UNAVAILABLE)
        return http.HttpResponse()
[/code]

## Check migrations have applied successfully

As shown above, we attempt to apply migrations when Upstart starts the Django
application. Should any of these migrations fails, we don’t want to bring that
machine into production. Hence we check for unapplied migrations as part of
the health check:

[code]

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
[/code]

Our extended health check view function now looks like:

[code]

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
[/code]

And there you have it: an effective health check view for Django applications.

Octopus Energy is a modern, green energy supplier for the UK.

We're looking for [strong Python developers to join our
team](http://tech.octopus.energy/2015/11/23/tech-jobs.html). Email us at
[talent@octoenergy.com](mailto:talent@octoenergy.com) if you're interested.

[ ![](http://tech.octopus.energy/assets/img/rss.svg)
](http://tech.octopus.energy/feed.xml) [
![](http://tech.octopus.energy/assets/img/github.svg)
](https://github.com/octoenergy)

