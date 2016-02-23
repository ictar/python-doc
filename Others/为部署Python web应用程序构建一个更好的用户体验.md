原文：[Building a better user experience for deploying Python web applications.](http://blog.dscpl.com.au/2016/02/building-better-user-experience-for.html)

---
我又一次错过了一个在PyCon US谈话的机会。我想谈的题目与这篇博客文章是一样的。由于它不被接受，所以我想我可能会转而使用一篇博文来一瞥我一直在做的Python Web应用程序部署的最近的工作，而如果它被接受，我本来会在我的谈话中描述一下相关内容。

对于那些可能已经跟随我在过去一直在做的，使用Apache和mod_wsgi来为运行着的Python web应用程序创建和运用Docker图像的人，这是工作的进展，扩大范围，并使它在Docker容器外可用。

# 使用Django示范

为了说明这是怎么一回事，下面是一个简单的演示。其中，创建了一个新的Django web应用程序项目，并让它运行。
```py
> $ django-admin startproject mydjangosite
> $ cd mydjangosite/
> $ python manage.py runserver
> Performing system checks...
> System check identified no issues (0 silenced).
> You have unapplied migrations; your app may not work properly until they are applied.
> Run 'python manage.py migrate' to apply them.
> February 18, 2016 - 01:22:25
> Django version 1.9.2, using settings 'mydjangosite.settings'
> Starting development server at http://127.0.0.1:8000/
> Quit the server with CONTROL-C.
```

这里没有什么特别的，而如果我们访问URL“http://127.0.0.1:8000/admin”，我们将看到Django管理界面的登录页面。由于我们使用的是内置Django开发服务器，所以登录页面的样式看起来正确的，因为开发服务器自动处理静态文件资产，例如样式表。

正如你应该都希望知道的，Django开发服务器不应该被用于生产系统。但至少对于开发，由于它处理静态文件资产，同时还提供动态代码加载，因此开发服务器是很方便的。虽然开发服务器的使用会隐藏只会发生在生产环境中（其中可能使用一个多进程和/或多线程结构）的某些问题。

建立一个生产级Web服务器通常被视为是一个很大的麻烦，人们可以与之斗争。因此，让我们看看我们是否能够使之更容易一些。

# 简化的web应用程序包装

为了创建上面的Django应用程序，首先需要安装Django。这很简单，只是以便‘django-admin’程序可用。试想一下，虽然我并不需要它，因为我曾手工创建该项目的骨架，或已签出一个现有的Django项目库。为了强调这一点，让我们使用'virtualenvwrapper“创建一个新的Python虚拟环境。其中，我要安装一个名为“WarpDrive”的Python包。

```sh
> $ mkvirtualenv warpdrive
> New python executable in /Users/graham/Python/warpdrive/bin/python
> Installing setuptools, pip, wheel...done.
> virtualenvwrapper.user_scripts creating /Users/graham/Python/warpdrive/bin/predeactivate
> virtualenvwrapper.user_scripts creating /Users/graham/Python/warpdrive/bin/postdeactivate
> virtualenvwrapper.user_scripts creating /Users/graham/Python/warpdrive/bin/preactivate
> virtualenvwrapper.user_scripts creating /Users/graham/Python/warpdrive/bin/postactivate
> virtualenvwrapper.user_scripts creating /Users/graham/Python/warpdrive/bin/get_env_details
> 
> (warpdrive) $ pip install warpdrive
> Collecting warpdrive
> Installing collected packages: warpdrive
> Successfully installed warpdrive-0.14.6
```

我们仍然需要Django，但我们绝对不希望在命令行上将其作为参数传递给'pip'以进行手动安装。相反，我们应该为"pip"在‘requirements.txt’文件中任何需要的Python包。因此，我们将创建一个“requirements.txt”，并在文件中只列出”Django。

即使到现在，我仍然没有真的想要手动运行‘pip’来建立一个Python Web应用程序项目，这样的话，它可以比安装必需的Python包更频繁的运行。例如，当与一个生产级WSGI服务器一起使用Django时，它一般会需要运行'python manage.py collectstatic'。这些措施虽然可能被用户遗忘。更好的方法是自动完成这样的步骤，并在必要时，将这些手动步骤记录在特殊的构建脚本中，这样，在为运行一个Python Web应用程序设置环境时，就可以自动运行。这就是‘warpdrive’发挥的作用。

现在，虽然我创建了一个Python虚拟环境，并安装了‘warpdrive’，但是它很干净，所以我安装了‘warpdrive’并显示它，否则我只是安装了一个空的Python。

我现在要做的事是为这个特定的Python Web应用程序建立一个单独的Python虚拟环境，但是让‘warpdrive’为我创建并设置它。

```py
> (warpdrive) $ eval "$(warpdrive activate mydjangosite)" 
> (warpdrive+mydjangosite) $ warpdrive build
>  ----->; Installing dependencies with pip
> Collecting Django (from -r requirements.txt (line 1))
>  Downloading Django-1.9.2-py2.py3-none-any.whl (6.6MB)
>  100% |████████████████████████████████| 6.6MB 1.4MB/s
> Installing collected packages: Django
> Successfully installed Django-1.9.2
> Collecting mod-wsgi
> Installing collected packages: mod-wsgi
> Successfully installed mod-wsgi-4.4.22
>  ----->; Collecting static files for Django
> Copying ‘.../django/contrib/admin/static/admin/css/base.css’
> ...
> 
> 56 static files copied to '/Users/graham/.warpdrive/warpdrive+mydjangosite/home/django_static_root'.
```

这里的第一步是使用‘warpdrive activate’创建一个新的Python虚拟环境，并将其用于当前的shell。第二步是使用‘warpdrive build’来设置我们的环境。

‘warpdrive build’命令在这里做一些事情，但主要的事情是，它安装在‘requirements.txt’ 文件中列出的所有Python包，安装‘mod_wsgi-express’，最后运行‘python manage.py collectstatic’。

你可能注意到，我们实际上并没有指定Django管理命令‘collectstatic’应该被执行。这是因为‘warpdrive’本身知道了Python Web应用程序可能会启动的各种方式，包括当你运行一个Django web应用程序时检测的特殊支持。知道你正在使用Django，它就会自动为你运行‘collectstatic’。

敏锐的人甚至可能会注意到，我们没有修改Django的设置模块，并指定‘STATIC_ROOT’从而使得“collectstatic”知道从哪里拷贝静态文件资产。再次，这是‘warpdrive’的聪明之处，它意识到当运行‘collectstatic'时，并没有为它定义‘STATIC_ROOT’，因此它会用它自己的‘STATIC_ROOT’值来代替。

当你接着更改静态文件资产或修改‘requirements.txt’文件时，你只需要重新运行‘warpdrive build’来刷新当前环境。

随着Web应用程序的环境的构建，现在我们可以启动它了。要做到这一点，我们将使用‘warpdrive start'。

```py
> (warpdrive+mydjangosite) $ warpdrive start
>  ----->; Configuring for server type of auto
>  ----->; Running server script start-mod_wsgi
>  ----->; Executing server command ' mod_wsgi-express start-server --log-to-terminal --startup-log --port 8080 --application-type module --entry-point mydjangosite.wsgi --callable-object application --url-alias /static/ /Users/graham/.warpdrive/warpdrive+mydjangosite/home/django_static_root/'
> Server URL : http://localhost:8080/
> Server Root : /tmp/mod_wsgi-localhost:8080:502
> Server Conf : /tmp/mod_wsgi-localhost:8080:502/httpd.conf
> Error Log File : /dev/stderr (warn)
> Startup Log File : /dev/stderr
> Request Capacity : 5 (1 process * 5 threads)
> Request Timeout : 60 (seconds)
> Queue Backlog : 100 (connections)
> Queue Timeout : 45 (seconds)
> Server Capacity : 20 (event/worker), 20 (prefork)
> Server Backlog : 500 (connections)
> Locale Setting : en_AU.UTF-8
> [Thu Feb 18 12:58:37.279748 2016] [mpm_prefork:notice] [pid 9456] AH00163: Apache/2.4.16 (Unix) mod_wsgi/4.4.22 Python/2.7.10 configured -- resuming normal operations
> [Thu Feb 18 12:58:37.280085 2016] [core:notice] [pid 9456] AH00094: Command line: 'httpd (mod_wsgi-express) -f /tmp/mod_wsgi-localhost:8080:502/httpd.conf -E /dev/stderr -D FOREGROUND'
```

与之前不同，这次，Django开发服务器并没有运行。相反，‘warpdrive’会运行‘mod_wsgi-express’。在此过程中，已经从Django应用本身自动决定WSGI应用程序入口点是什么，静态文件资产挂载在哪里，以及确定静态文件资产位于何处。因此，我们用于Django管理页面的样式表可以工作，即使你已经忘了在Django配置文件中设置“STATIC_ROOT”，因为‘warpdrive’会检测到。

没有真正额外的工作，我们已经有了自己生产级别的WSGI服务器，从而可以更加自信，当我们真正部署我们的Django应用程序时，我们有更多的东西可比较。理论上，这甚至可以被用作生产部署的基础，如果是的话，这意味着你的本地环境将尽可能接近实际生产平台。

至于其他配置或安装步骤，‘warpdrive’支持执行钩子脚本（可作为构建和部署阶段的一部分执行）的各种机制。这意味着你可以捕捉设置步骤，并在适当情况下，同时在本地环境和生产环境上触发它们。也可提供附加的WSGI服务器选项或环境变量来覆盖或定制结构，诸如调谐正在使用的进程和线程的数目。

一个有关本地开发的特定环境变量是“MOD_WSGI_RELOAD_ON_CHANGES”。运行‘warpdrive start’时定义这个变量，而恢复内置的Django开发服务器的自动代码加载功能，意味着你也可以在开发过程中容易地使用‘warpdrive’。


# 那个名为Docker的酷小孩

你可能会说，但我用Docker，所以这是将怎么帮助我呢。

这是没有问题的，‘warpdrive’实际上产生于我一直在使用Docker做的所有工作。你可以在技术上建立自己的Docker基本图形，并假设它满足关于特定的可用系统包的一些要求，从你的‘Dockerfile’中触发‘warpdrive build’，以及从‘CMD’中触发‘warpdrive start’。

虽然较容易的方法是使用Docker基本图形，也就是我所创建的已经包含所有必需的基本包和整合‘warpdrive’的那个。

虽然必须自己创建Docker图形仍然很痛苦，尤其是从头做起，而你不知道在此过程中所有的陷阱。

为了使这一切变得更容易，我有的‘warpdrive’和Docker基本图像已经启用S2I。

最有可能的是，你没有听说过S2I，但它所代表的是“从源到图像”。它由一些托管服务实现，加强了构建包的概念，但重新想象和现代化了Docker的使用。

你可以在这里读到更多关于“从源到图像”的内容：

*   [https://github.com/openshift/source-to-image](https://github.com/openshift/source-to-image)

我的Django Web应用程序已运行，要为它创建一个Docker图像，我现在所要做的是运行‘warpdrive s2i’。

```py
> (warpdrive+mydjangosite) $ warpdrive s2i
> --->; Installing application source
> --->; Building application from source
> ----->; Installing dependencies with pip
> Collecting Django (from -r requirements.txt (line 1))
> Downloading Django-1.9.2-py2.py3-none-any.whl (6.6MB)
> Installing collected packages: Django
> Successfully installed Django-1.9.2
> ----->; Collecting static files for Django
> Copying ‘.../django/contrib/admin/static/admin/img/icon-yes.svg’
> ...
> 
> 56 static files copied to '/home/warpdrive/django_static_root'.
> --->; Fix permissions on application source
> (warpdrive+mydjangosite) $ docker images | grep mydjangosite
> warpdrive-mydjangosite latest 8d7fd16f7ab8 20 seconds ago 819.6 MB
```

这样做的结果是结合我的Django web应用程序及其所需的Docker图像，它叫做warpdrive-mydjangosite'。和以前一样，作为Docker图像生成阶段的一部分，‘collectstatic’被自动运行。

那么，运行Docker图像只是执行‘docker run’，并暴露适当的端口的问题。

```py
> (warpdrive+mydjangosite) $ docker run -p 8080:8080 warpdrive-mydjangosite
> --->; Executing the start up script
>  ----->; Configuring for server type of auto
>  ----->; Running server script start-mod_wsgi
>  ----->; Executing server command ' mod_wsgi-express start-server --log-to-terminal --startup-log --port 8080 --application-type module --entry-point mydjangosite.wsgi --callable-object application --url-alias /static/ /home/warpdrive/django_static_root/'
> [Thu Feb 18 03:08:22.406961 2016] [mpm_event:notice] [pid 19:tid 139789921310464] AH00489: Apache/2.4.18 (Unix) mod_wsgi/4.4.22 Python/2.7.11 configured -- resuming normal operations
> [Thu Feb 18 03:08:22.407345 2016] [core:notice] [pid 19:tid 139789921310464] AH00094: Command line: 'httpd (mod_wsgi-express) -f /tmp/mod_wsgi-localhost:8080:1001/httpd.conf -E /dev/stderr -D MOD_WSGI_MPM_ENABLE_EVENT_MODULE -D MOD_WSGI_MPM_EXISTS_EVENT_MODULE -D MOD_WSGI_MPM_EXISTS_WORKER_MODULE -D MOD_WSGI_MPM_EXISTS_PREFORK_MODULE -D FOREGROUND'
```

然后，你可以进一步测试在Docker上下文中运行的web应用程序，而如果结果令你满意，你可以推送该Docker图像到你的托管平台，并运行它。

# 部署到OpenShift 3

如果使用基于Docker和Kubernetes的OpenShift最新版本，部署更容易。这是因为你不需要自己通过单独的步骤去创建Docker图像并将其上传到Docker注册表。这是因为OpenShift本身知道从源到图像，并可以直接从一个Gi​​t仓库部署Web应用程序。

要部署同样的应用程序到OpenShift，所有我需要做的就是提交我的修改，并推动它们到了我的Git仓库，然后运行：

```py
> (warpdrive+mydjangosite) $ oc new-app grahamdumpleton/warp0-debian8-python27~https://github.com/GrahamDumpleton/django-hello-world-v1.git
> -->; Found Docker image d148eec (8 hours old) from Docker Hub for "grahamdumpleton/warp0-debian8-python27"
> Python 2.7 (Warp Drive)
>  -----------------------
>  S2I builder for Python web applications.
> Tags: builder, python, python27, warpdrive, warpdrive-python27
> * An image stream will be created as "warp0-debian8-python27:latest" that will track the source image
>  * A source build using source code from https://github.com/GrahamDumpleton/django-hello-world-v1.git will be created
>  * The resulting image will be pushed to image stream "django-hello-world-v1:latest"
>  * Every time "warp0-debian8-python27:latest" changes a new build will be triggered
>  * This image will be deployed in deployment config "django-hello-world-v1"
>  * Port 8080/tcp will be load balanced by service "django-hello-world-v1"
>  * Other containers can access this service through the hostname "django-hello-world-v1"
> -->; Creating resources with label app=django-hello-world-v1 ...
>  imagestream "django-hello-world-v1" created
>  buildconfig "django-hello-world-v1" created
>  deploymentconfig "django-hello-world-v1" created
>  service "django-hello-world-v1" created
> (warpdrive+mydjangosite) $ oc expose service django-hello-world-v1
> route "django-hello-world-v1" exposed
```

OpenShift将在必要的时候自动下载我的​​带有S2I支持的Docker基本图像S2I支持，以及包含我的应用程序源代码的Git仓库，引发S2I构建过程创建最终的Docker图像，然后进行部署。然后，我们只需要运行最后一步来实际上使Web应用程序可以被公开访问，这样我们就做完了。

# 备选的PaaS提供商

‘warpdrive’可以与其他PaaS提供商一起用吗？

答案是肯定的，只要在构建和部署阶段，他们不完全锁定你，并且不要过多搞砸了Python环境。我还没有为此调整‘warpdrive’，并且也许不会，但我已经部署所有这些工作的以前迭代到OpenShift 2和Heroku上了。

最终的结果是，我们在这里有一个可能性，就是使得一个部署可以与多个主机服务一起工作，但仍然可以在你的本地开​​发平台上使用。


# 备选的web服务器

在我们的样例应用中，我们使用了Django，但是如果使用给一个备选的WSGI框架，你只需要在你的项目的顶级目录中的‘wsgi.py’文件中应用一个WSGI应用入口点。

默认情况下，‘warpdrive’的‘auto’模式将使用‘mod_wsgi-express’来处理任何WSGI应用，包括Django特定应用。这是因为，‘mod_wsgi-express’的主要目的是为这种类型的部署步骤所构建的。因此，这是可用的最佳选项。

当配置恰当时，大多数WSGI服务器的性能或多或少是相同的。如果你仍然因为某个WSGI服务器的特性能够更好的满足你的web应用的特殊需求而希望使用一个不同的WSGI服务器，那么你可以仍旧使用那个备选的WSGI服务器。要做到这点，你只需要重写‘auto’模式，然后配置你想要使用的WSGI服务器。

所支持的备选WSGI服务器是‘gunicorn’, ‘uwsgi’和‘waitress’。在选择这些时，你只需要确保在用于“pip”的‘requirements.txt’文件中列出它们。一旦你这样做了，‘warpdrive’将会为你用那个WSGI服务器取而代之，运用所需的最小集选项来使它们监听HTTP连接的正确端口，并将在终端保存日志。其他任何确保WSGI服务器可以在一个Docker容器中正常工作的必选项也将在需要的时候应用。

与指定这些任何一个备选WSGI服务器相同，你也可以显式指定应该使用的‘mod_wsgi’。虽然要注意，覆盖部署机制并且不使用‘auto’意味着该WSGI服务器的配置完全取决于你。所以，如果显式指定‘mod_wsgi’或备选WSGI服务器，你会需要告诉它如何使用你的Django应用，反之，‘auto’模式会帮你做好这一切。

对于那些实际上不想使用WSGI服务器，而想要使用其他诸如Tornado web服务器的人，你可以应用一个‘app.py’文件来代替。如果这个文件存在了，那么它会获得优先权，而‘warpdrive’将把它作为一个Python脚本执行，从而运行你的Python web应用。然后，你的web应用只需要监听正确的HTTP端口即可。

想要更多关于启动的控制权，你也可以运用一个‘app.sh’文件，在其中执行任何必要的步骤或者设置特殊的环境变量。关于这点唯一必要条件是，在shell脚本中运行实际的web应用的最后的命令要使用‘exec’，这样的话，该web应用才能替代shell进程。这是要确保当在Docker下运行时，信号正确工作。例如，当想要安装和运行Jupyter Notebook时，你可能会使用一个‘app.sh’文件

如果其他Python web框架的特殊知识具有一个唯一且常用的部署方法，那么它们也可以被添加。例如，‘warpdrive’也将识别一个‘paste.ini’文件（它可能被基于Paste的web应用使用），然后配置和启动‘mod_wsgi-express’来运行它。

# 你应该用它吗?

现在，‘warpdrive’就是我的玩具。

由于用户对项目开发者的要求，将新的开源项目带到公众面前是危险的。

所以现在，你可能不想要使用它，因为我仍然想要可以灵活地根据我想要的对它的工作方式进行修改。另外，我真的不想要成堆的用户用简单的问题纠缠我。

我曾经说它已准备好使用吗?可能会，可能不会。这取决于是否有任何兴趣。令人吃惊的是，过去，从这整个概念的某些部分中，我已经获得了一些既成事实(原文是：I have gotten a fait bit of push back from some quarters on this whole concept in the past.想不到确切的译法，SOS！T_T)。这很可能是少数人，他们认为他们已经知道自己如何做这一切了，但是这样消极的反应并不有助于将此想法公布于众并使之可用。

如果你受我所呈现的东西所启发，认为它有价值，并且你可能使用的它，那么至少在Twitter上关注我（@GrahamDumpleton），然后在Twitter上让我知道你的想法。谢谢。
