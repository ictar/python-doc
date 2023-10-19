原文：[How to deploy with WSGI](https://docs.djangoproject.com/en/1.9/howto/deployment/wsgi/)

Django主要的部署平台是[WSGI](http://www.wsgi.org), 它是Web服务器和应用程序的Python标准。

Django的[`startproject`](https://docs.djangoproject.com/en/1.9/ref/django-admin/#django-admin-startproject)管理命令为你建立了一个简单的默认WSGI配置，你可以根据项目需要进行调整，并用于任何WSGI兼容的应用程序服务器。

Django包含了以下WSGI服务器的入门文档：

*   [How to use Django with Apache and mod_wsgi](https://docs.djangoproject.com/en/1.9/howto/deployment/wsgi/modwsgi/)
*   [Authenticating against Django’s user database from Apache](https://docs.djangoproject.com/en/1.9/howto/deployment/wsgi/apache-auth/)
*   [How to use Django with Gunicorn](https://docs.djangoproject.com/en/1.9/howto/deployment/wsgi/gunicorn/)
*   [How to use Django with uWSGI](https://docs.djangoproject.com/en/1.9/howto/deployment/wsgi/uwsgi/)

## `application`对象[¶](#the-application-object "Permalink to this headline")

使用WSGI进行部署的关键概念是`application`可调用对象，应用服务器用它来与你的代码进行通信。它通常作为服务器可访问的一个Python模块中的一个名为`application`的对象被提供。

[`startproject`](https://docs.djangoproject.com/en/1.9/ref/django-admin/#django-admin-startproject)命令创建一个文件`<project_name>/wsgi.py`，它包含这样的`application`可调用对象。

它同时用于Django的开发服务器和生产中的WSGI部署。

WSGI服务器从它们的配置中获取`application`可调用对象的路径。Django的内置服务器，即[`runserver`](https://docs.djangoproject.com/en/1.9/ref/django-admin/#django-admin-runserver)命令，从[`WSGI_APPLICATION`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-WSGI_APPLICATION)设置项中读取它。默认情况下，它被设置为`<project_name>.wsgi.application`, 它指向`<project_name>/wsgi.py`中的`application`可调用对象。


## 配置settings模块[¶](#configuring-the-settings-module "Permalink to this headline")

当WSGI服务器加载应用程序时，Django需要导入settings模块（定义你整个应用程序的地方）。

Django使用[`DJANGO_SETTINGS_MODULE`](https://docs.djangoproject.com/en/1.9/topics/settings/#envvar-DJANGO_SETTINGS_MODULE)环境变量来查找相应的设置模块。他必须包含settings模块的虚线路径。你可以对开发以及生产环境使用不同的值；这都取决于如何安排你的设置。

若未设置该变量，`wsgi.py`会默认将它设置为`mysite.settings`, 其中，`mysite`是项目名称。这就是[`runserver`](https://docs.djangoproject.com/en/1.9/ref/django-admin/#django-admin-runserver)默认情况下如何发现默认的设置文件。

>注意

>由于环境变量是进程范围的，当你在同一个进程中运行多个Django站点时，它将无法正常工作。在使用mod_wsgi时会出现这个问题。

>要避免这个问题，在每个站点的守护进程中使用mod_wsgi的守护进程模式，或者通过强制`wsgi.py`文件中的项为`os.environ["DJANGO_SETTINGS_MODULE"] = "mysite.settings"`来覆盖环境中的值。



## 应用WSGI中间件[¶](#applying-wsgi-middleware "Permalink to this headline")

要应用[WSGI middleware](https://www.python.org/dev/peps/pep-3333/#middleware-components-that-play-both-sides)，你可以简单的包装application对象。例如，你可以在`wsgi.py`底部增加这些行：

```python
from helloworld.wsgi import HelloWorldApplication
application = HelloWorldApplication(application)
```

如果你想将一个Django应用与一个其它框架的WSGI应用合并，也可以使用一个稍后委托给Django WSGI application的自定义WSGIapplication来替换该Django WSGI application。


>注意

>一些第三方WSGI中间件在处理一个请求后并不在response对象上调用`close`。在这些情况下，[`request_finished`](https://docs.djangoproject.com/en/1.9/ref/signals/#django.core.signals.request_finished "django.core.signals.request_finished")信号不会被发生。这可能导致到数据库和内存缓存服务器的空闲连接。
