原文：[Building a better user experience for deploying Python web applications.](http://blog.dscpl.com.au/2016/02/building-better-user-experience-for.html)

---
Yet again I missed out on a getting a talk into PyCon US. The title of my proposed talk was the same as this blog post. Since it wasn’t accepted, I thought I might instead use a blog post to give a sneak peek at some of the more recent work I have been doing on Python web application deployment, which I otherwise would have described a bit about in my talk if it had been accepted.

For those who may have been following what I have been doing in the past with creating and supplying Docker images for running Python web applications using Apache and mod_wsgi, this is a progression of that work, expanding on the scope and making it usable beyond Docker containers.

# Demonstration using Django

To illustrate what it is all about a simple demonstration is in order. For that lets create a new Django web application project and get it running.
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

Nothing special here and if we go to the URL ‘http://127.0.0.1:8000/admin' we will be presented with the login page for the Django admin interface. As we are using the builtin Django development server the styling for the login page will look correct as the development server automatically worries about static file assets such as style sheets.

As you should all hopefully know, the Django development server should not be used for a production system. For development though at least, the development server can be handy due to the fact that it does handle static file assets and also offers automatic code reloading. Use of the development server can though hide certain problems that will only occur in a production environment where a multi process and/or multi threaded configuration may be used.

Setting up a production grade web server is often viewed as being a lot of trouble and people can struggle with it. Lets therefore see if we can make that a bit easier.

# Simplified web application wrapper

In order to create that Django application above I first needed to have Django installed. This was simply so that the ‘django-admin’ program was available. Imagine though that I didn’t need that as I had created the project skeleton by hand, or had checked out an existing Django project repository. To emphasise this, lets use ‘virtualenvwrapper’ to create a fresh Python virtual environment. Into this I am going to install a single Python package called ‘warpdrive’.

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

We will still need Django, but we definitely don’t want to install that manually as an argument to ‘pip’ on the command line. Instead we should list any such required Python packages in a ‘requirements.txt’ file for ‘pip’. We will therefore create a ‘requirements.txt’ file listing only ‘Django’ in it.

Even now I still don’t really want to run ‘pip’ by hand as setting up a Python web application project so it can be run is often more than just installing required Python packages. For example, when using Django with a production grade WSGI server, it would generally be necessary to run ‘python manage.py collectstatic’. These are steps though that can be forgotten by users. A better approach would be to automate such steps, and where necessary, record such manual steps in special build scripts that would be automatically run when setting up the environment for running a Python web application. This is where ‘warpdrive’ comes into play.

Now although I create a Python virtual environment and installed ‘warpdrive’, that was purely so that I had ‘warpdrive’ installed and show that otherwise I had an empty Python installation.

What I am now going to do is build a separate Python virtual environment for this specific Python web application, but have ‘warpdrive’ create it and set it up for me.
```py
> (warpdrive) $ eval "$(warpdrive activate mydjangosite)"&nbsp;
> (warpdrive+mydjangosite) $ warpdrive build
>  -----&gt; Installing dependencies with pip
> Collecting Django (from -r requirements.txt (line 1))
>  Downloading Django-1.9.2-py2.py3-none-any.whl (6.6MB)
>  100% |████████████████████████████████| 6.6MB 1.4MB/s
> Installing collected packages: Django
> Successfully installed Django-1.9.2
> Collecting mod-wsgi
> Installing collected packages: mod-wsgi
> Successfully installed mod-wsgi-4.4.22
>  -----&gt; Collecting static files for Django
> Copying ‘.../django/contrib/admin/static/admin/css/base.css’
> ...
> 
> 56 static files copied to '/Users/graham/.warpdrive/warpdrive+mydjangosite/home/django_static_root'.
```
The first step here was to use ‘warpdrive activate’ to create a fresh Python virtual environment and use it for the current shell. The second step was to use ‘warpdrive build’ to setup our environment.

The ‘warpdrive build’ command is doing a few things here, but the main things are that it installed all Python packages listed in the ‘requirements.txt’ file, installed ‘mod_wsgi-express’ and finally ran ‘python manage.py collectstatic’.

You may note that we didn’t actually specify that the Django management command ‘collectstatic’ should be executed. This is because ‘warpdrive’ itself knows about various ways that Python web applications may be launched, including special support for detecting when you are running a Django web application. Knowing that you are using Django it will automatically run ‘collectstatic’ for you.

The keen eyed may even notice that we didn’t modify the Django settings module and specify ‘STATIC_ROOT’ so that ‘collectstatic’ knew where to copy static file assets. Again this is the smarts of ‘warpdrive’ kicking in, with it realising that it wasn’t defined and supplying its own value of ‘STATIC_ROOT’ instead when ‘collectstatic' is run.

As you go along and make changes to static file assets or modify the ‘requirements.txt’ file, you simply need to re-run ‘warpdrive build’ to refresh the current environment.

With the environment for the web application built, we can now start it up. To do this we are going to use ‘warpdrive start'.
```py
> (warpdrive+mydjangosite) $ warpdrive start
>  -----&gt; Configuring for server type of auto
>  -----&gt; Running server script start-mod_wsgi
>  -----&gt; Executing server command ' mod_wsgi-express start-server --log-to-terminal --startup-log --port 8080 --application-type module --entry-point mydjangosite.wsgi --callable-object application --url-alias /static/ /Users/graham/.warpdrive/warpdrive+mydjangosite/home/django_static_root/'
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
Unlike before, this time the Django development server is not being run. Instead ‘warpdrive’ is running ‘mod_wsgi-express’. In doing that it has automatically determined from the Django application itself what the WSGI application entry point is, where static files assets are mounted, as well as determine where the static file assets are located. Our style sheets for the Django admin page therefore work, even if you had forgot to set up ‘STATIC_ROOT’ in the Django settings file as ‘warpdrive’ would have detected that.

With no real extra work we have got ourselves a production grade WSGI server and can thus be more confident that we have something more comparable to when we really deploy our Django application. Notionally this could even be used as the basis of your production deployment and if it was, it means that your local environment is going to be as close as possible to the actual production platform.

As far as additional configuration or setup steps, ‘warpdrive’ supports various mechanisms for supplying hook scripts which can be executed as part of the build and deployment phases. This means you can capture setup steps and have them triggered on both a local environment and production where appropriate. Additional WSGI server options or environment variables can also be supplied to override or customise the configuration, such as tuning the number of processes and threads being used.

One specific environment variable relevant to local development is ‘MOD_WSGI_RELOAD_ON_CHANGES’. Define this when running ‘warpdrive start’ and you get back the automatic code reloading feature of the builtin Django development server, meaning you can just as readily use ‘warpdrive' during development also.

# That cool kid called Docker

You may be saying, but I use Docker, so how is this going to help me.

This is no problem and ‘warpdrive’ actually grew out of all the work I have been doing with Docker. You could technically create your own Docker base image and provided it satisfies a few requirements around certain system packages being available, trigger ‘warpdrive build’ &nbsp;from your ‘Dockerfile’ and ‘warpdrive start’ from the ‘CMD’.

The easier path though would be to use Docker base images which I have created which already incorporate all the required base packages and integrate ‘warpdrive’ already.

Having to create Docker images yourself can still be a pain though, especially when doing it from scratch and you aren’t aware of all the traps and pitfalls in doing that.

To make it all easier, ‘warpdrive’ and the Docker base images I have are S2I enabled.

Most probably wouldn’t have heard of S2I, but what it stands for is ’Source to Image’. It is effectively the concept of build packs as implemented by some hosting services, but re-imagined and modernised to use Docker.

You can read more about Source to Image at:

*   [https://github.com/openshift/source-to-image](https://github.com/openshift/source-to-image)

Having already shown that my Django web application runs, all I now need to do to create a Docker image for it is to run ‘warpdrive s2i’.
```py
> (warpdrive+mydjangosite) $ warpdrive s2i
> ---&gt; Installing application source
> ---&gt; Building application from source
> -----&gt; Installing dependencies with pip
> Collecting Django (from -r requirements.txt (line 1))
> Downloading Django-1.9.2-py2.py3-none-any.whl (6.6MB)
> Installing collected packages: Django
> Successfully installed Django-1.9.2
> -----&gt; Collecting static files for Django
> Copying ‘.../django/contrib/admin/static/admin/img/icon-yes.svg’
> ...
> 
> 56 static files copied to '/home/warpdrive/django_static_root'.
> ---&gt; Fix permissions on application source
> (warpdrive+mydjangosite) $ docker images | grep mydjangosite
> warpdrive-mydjangosite latest 8d7fd16f7ab8 20 seconds ago 819.6 MB
```
The result of this is a Docker image incorporating my Django web application and all it needs, called ‘warpdrive-mydjangosite'. As before, ‘collectstatic’ was automatically run as part of the build phase for the Docker image.

Running the Docker image is then just a matter of executing ‘docker run’ and exposing the appropriate port.
```py
> (warpdrive+mydjangosite) $ docker run -p 8080:8080 warpdrive-mydjangosite
> ---&gt; Executing the start up script
>  -----&gt; Configuring for server type of auto
>  -----&gt; Running server script start-mod_wsgi
>  -----&gt; Executing server command ' mod_wsgi-express start-server --log-to-terminal --startup-log --port 8080 --application-type module --entry-point mydjangosite.wsgi --callable-object application --url-alias /static/ /home/warpdrive/django_static_root/'
> [Thu Feb 18 03:08:22.406961 2016] [mpm_event:notice] [pid 19:tid 139789921310464] AH00489: Apache/2.4.18 (Unix) mod_wsgi/4.4.22 Python/2.7.11 configured -- resuming normal operations
> [Thu Feb 18 03:08:22.407345 2016] [core:notice] [pid 19:tid 139789921310464] AH00094: Command line: 'httpd (mod_wsgi-express) -f /tmp/mod_wsgi-localhost:8080:1001/httpd.conf -E /dev/stderr -D MOD_WSGI_MPM_ENABLE_EVENT_MODULE -D MOD_WSGI_MPM_EXISTS_EVENT_MODULE -D MOD_WSGI_MPM_EXISTS_WORKER_MODULE -D MOD_WSGI_MPM_EXISTS_PREFORK_MODULE -D FOREGROUND'
```
You can then test further your web application running in the context of Docker and if happy, push the Docker image up to your hosting platform and run it.

# Deploying to OpenShift 3

If using the latest version of OpenShift based on Docker and Kubernetes deployment is even easier. This is because you don’t need to go through the separate step yourself of creating the Docker image and uploading it to a Docker registry. This is because OpenShift itself is aware of Source to Image and can deploy web applications direct from a Git repository.

To deploy this same application to OpenShift, all I would need to do is commit my changes and push them up to my Git repository and run:
```py
> (warpdrive+mydjangosite) $ oc new-app grahamdumpleton/warp0-debian8-python27~https://github.com/GrahamDumpleton/django-hello-world-v1.git
> --&gt; Found Docker image d148eec (8 hours old) from Docker Hub for "grahamdumpleton/warp0-debian8-python27"
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
> --&gt; Creating resources with label app=django-hello-world-v1 ...
>  imagestream "django-hello-world-v1" created
>  buildconfig "django-hello-world-v1" created
>  deploymentconfig "django-hello-world-v1" created
>  service "django-hello-world-v1" created
> (warpdrive+mydjangosite) $ oc expose service django-hello-world-v1
> route "django-hello-world-v1" exposed
```
OpenShift will automatically download my Docker base image with S2I support as necessary, and the Git repository containing my application source code, trigger the S2I build process to create the final Docker image and then deploy it. We then just need to run one final step to actually make the web application publicly accessible and we are done.

# Alternate PaaS providers

Could ‘warpdrive’ be used with other PaaS providers?

The answer there is yes, provided they don’t lock you out completely from the build and deployment phases, and don’t screw up the Python environment too much. I haven’t tweaked ‘warpdrive’ for this, and probably won't, but I have deployed previous iterations of all this work to OpenShift 2 and Heroku.

The end result is that we have the possibility here of having one deployment story that can work with multiple hosting services, but which can still also be used on your local development platform.

# Alternate web servers

In our sample application we used Django, but if using an alternate WSGI framework you just need to supply a WSGI application entrypoint in a ‘wsgi.py’ file in the top directory of your project.

By default the ‘auto’ mode of ‘warpdrive’ will use ‘mod_wsgi-express’ to host any WSGI application, including Django specific applications. This is because ‘mod_wsgi-express’ was largely purpose built for this type of deployment setup. It is therefore the best option available.

The performance of most WSGI servers is more or less the same when configured properly. If you still wish to use a different WSGI server because the characteristics of that WSGI server better suit some unique requirement of your web application, you can still use that alternate WSGI server. To do this you just need to override the ‘auto’ mode and say what WSGI server you want to use.

Alternate WSGI servers which are supported are ‘gunicorn’, ‘uwsgi’ and ‘waitress’. When these are selected you just need to ensure that they are also listed in the ‘requirements.txt’ file for ‘pip’. So long as you do that, ‘warpdrive’ will start up that WSGI server for you instead, supplying a minimal set of options required to get them to listen on the correct port for HTTP connections and log to the terminal. Any other required options to ensure the WSGI server behaves properly inside of a Docker container will also be supplied if necessary.

As well as specifying any of these alternate WSGI servers, you can also specify explicitly that ‘mod_wsgi’ should be used. Do be aware though that overriding the deployment mechanism and not using ‘auto’, means that the configuration of the WSGI server is then entirely up to you. So if specifying ‘mod_wsgi’ or an alternate WSGI server explicitly, you would then need to tell it how to host your Django application, whereas with ‘auto’ mode that is all done for you.

For those who don’t want to actually use a WSGI server, but instead for example want to use the Tornado web server, you can instead supply an ‘app.py’ file. If this file exists then that will take precedence and ‘warpdrive’ will execute it as a Python script to run your Python web application. Your web application then just needs to listen on the appropriate HTTP port.

Need even more control over startup, you can also supply an ‘app.sh’ file and so as necessary easily preform any last minute steps or set special environment variables. The only requirement at this point is that the final command in the shell script to run the actual web application use ‘exec’ so that the web application replaces the shell process. This is to ensure signals works properly when things are run under Docker. You might use an ‘app.sh’ file for example when wishing to setup and run Jupyter Notebook.

Special knowledge for other Python web frameworks could also be added if they have a unique and commonly used method of deployment. For example, ‘warpdrive’ will also recognise a ‘paste.ini’ file as might be used by Paste based web applications and configure and launch ‘mod_wsgi-express’ to run it.

# Should you use this?

Right now ‘warpdrive’ is my play thing.

Bringing new Open Source projects into the open and making them public is a dangerous exercise due to the demands that users put on developers of the projects.

So right now you probably don’t want to use it because I still want the flexibility to make any sorts of changes I want to how it works. Plus I don’t really want hoards of users pestering me with simple questions.

Will I ever say it is ready to use? Maybe, maybe not. That really depends on whether there is any interest. Surprisingly I have gotten a fait bit of push back from some quarters on this whole concept in the past. This may well be a vocal minority who think they already known how to do everything themselves, but such negative reactions aren’t always encouraging to the idea of declaring it public and usable.

If you are intrigued by what I have presented, think it has merit and might be something you would use, then at least follow me on Twitter (@GrahamDumpleton) and let me know on Twitter what you think. Thanks.
