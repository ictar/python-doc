原文：[Managing static files (e.g. images, JavaScript, CSS)](https://docs.djangoproject.com/en/1.9/howto/static-files/)

---

网站通常需要提供额外的文件，例如图片，JavaScript, 或者CSS。在Django中，我们将这些文件称之为“静态文件”。Django提供了[`django.contrib.staticfiles`](https://docs.djangoproject.com/en/1.9/ref/contrib/staticfiles/#module-django.contrib.staticfiles "django.contrib.staticfiles:An app for handling static files." )来帮助你管理它们。

本文描述了你可以如何提供这些静态文件。

## 配置静态文件

  1. 确保在你的[`INSTALLED_APPS`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-INSTALLED_APPS)中包含了`django.contrib.staticfiles`。

  2. 在你的settings文件中，定义[`STATIC_URL`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-STATIC_URL)，例如：

  ```python  
  STATIC_URL = '/static/'
  ```  

  3. 在你的模板中，要么像`/static/my_app/myexample.jpg`这样硬编码url，要么，更好的做法是，使用[`static`](https://docs.djangoproject.com/en/1.9/ref/contrib/staticfiles/#std:templatetag-staticfiles-static)模板标签通过使用配置的[`STATICFILES_STORAGE`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-STATICFILES_STORAGE) 存储(当你想要转到一个内容分发网络（CDN）来提供静态文件时，这会容易得多)，为给定的相对路径建立url。

    ```html
    {% load staticfiles %}
    <img src="{% static "my_app/myexample.jpg" %}" alt="My image"/>
    ```

  4. 将你的静态文件保存在你的应用中的名为`static`的文件夹下。例如，`my_app/static/my_app/myimage.jpg`。

>提供文件

>除了这些配置步骤外，你还会需要实际的提供静态文件。

>部署期间，如果你使用[`django.contrib.staticfiles`](https://docs.djangoproject.com/en/1.9/ref/contrib/staticfiles/#module-django.contrib.staticfiles "django.contrib.staticfiles: An app for handling static files." )，那么当设置[`DEBUG`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-DEBUG)为`True`时，[`runserver`](https://docs.djangoproject.com/en/1.9/ref/django-admin/#django-admin-runserver)将会自动完成 (见[`django.contrib.staticfiles.views.serve()`](https://docs.djangoproject.com/en/1.9/ref/contrib/staticfiles/#django.contrib.staticfiles.views.serve "django.contrib.staticfiles.views.serve" ))。

>这种方法是**非常低效的**，并且可能**不安全**，所以**不适用于生产**。

>见[_部署静态文件_](https://docs.djangoproject.com/en/1.9/howto/static-files/deployment/)，以获得在生产环境中提供静态文件的正确策略。

你的工程也有可能拥有一些不特定于某个应用的静态文件。除了使用你的应用中的`static/`目录，你还可以在你的settings文件中定义目录列表([`STATICFILES_DIRS`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-STATICFILES_DIRS))，Django将会查找该列表以搜索静态文件。例如：
    
  ```python  
    STATICFILES_DIRS = [
        os.path.join(BASE_DIR, "static"),
        '/var/www/static/',
    ]
  ```  

见[`STATICFILES_FINDERS`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-STATICFILES_FINDERS)设置项的文档，以获得`staticfiles`如何查找你的文件的相关细节。

>静态文件名字空间

>现在，直接把我们的静态文件放到`my_app/static/` (而不是创建另一个`my_app`子目录)中也许可以蒙混过关，但这其实是一个糟糕的想法。Django将会使用它所找到的第一个名字匹配的静态文件，而如果另一个不同的应用中有相同名字的静态文件，那么Django将无法区分它们。我们需要能够让Django指向正确的静态文件，而最简单的方式就是使用名字空间。也就是说，将那些静态文件放到另一个由应用自身命名的目录中。

## 在部署期间提供静态文件

If you use [`django.contrib.staticfiles`](https://docs.djangoproject.com/en/1.9/ref/contrib/staticfiles/#module-django.contrib.staticfiles"django.contrib.staticfiles: An app for handling static files." ) as explained above, [`runserver`](https://docs.djangoproject.com/en/1.9/ref/django-admin/#django-admin-runserver) will do this automatically when[`DEBUG`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-DEBUG) is set to `True`. If you don't have `django.contrib.staticfiles` in [`INSTALLED_APPS`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-INSTALLED_APPS), you can still manually serve static files using the [`django.contrib.staticfiles.views.serve()`](https://docs.djangoproject.com/en/1.9/ref/contrib/staticfiles/#django.contrib.staticfiles.views.serve"django.contrib.staticfiles.views.serve" ) view.

This is not suitable for production use! For some common deployment
strategies, see [_部署静态文件_](https://docs.djangoproject.com/en/1.9/howto/static-files/deployment/)。

例如，如果定义[`STATIC_URL`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-STATIC_URL)为`/static/`，那么你可以通过添加下面的代码段到你的urls.py中来做到这点：

    
    
    from django.conf import settings
    from django.conf.urls.static import static
    
    urlpatterns = [
        # ... the rest of your URLconf goes here ...
    ] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
    

>Note

>This helper function works only in debug mode and only if the given prefix is local (e.g. `/static/`) and not a URL (e.g. `http://static.example.com/`).

>Also this helper function only serves the actual
[`STATIC_ROOT`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-STATIC_ROOT) folder; it doesn't perform static files discovery like [`django.contrib.staticfiles`](https://docs.djangoproject.com/en/1.9/ref/contrib/staticfiles/#module-django.contrib.staticfiles "django.contrib.staticfiles:An app for handling static files." ).

## 在开发过程中提供用户上传的文件

During development, you can serve user-uploaded media files from
[`MEDIA_ROOT`](https://docs.djangoproject.com/en/1.9/ref/settings/#std
:setting-MEDIA_ROOT) using the [`django.contrib.staticfiles.views.serve()`](ht
tps://docs.djangoproject.com/en/1.9/ref/contrib/staticfiles/#django.contrib.st
aticfiles.views.serve "django.contrib.staticfiles.views.serve" ) view.

This is not suitable for production use! For some common deployment
strategies, see [_Deploying static
files_](https://docs.djangoproject.com/en/1.9/howto/static-files/deployment/).

For example, if your
[`MEDIA_URL`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-
MEDIA_URL) is defined as `/media/`, you can do this by adding the following
snippet to your urls.py:

    
    
    from django.conf import settings
    from django.conf.urls.static import static
    
    urlpatterns = [
        # ... the rest of your URLconf goes here ...
    ] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
    

Note

This helper function works only in debug mode and only if the given prefix is
local (e.g. `/media/`) and not a URL (e.g. `http://media.example.com/`).

## 测试

When running tests that use actual HTTP requests instead of the built-in
testing client (i.e. when using the built-in [`LiveServerTestCase`](https://do
cs.djangoproject.com/en/1.9/topics/testing/tools/#django.test.LiveServerTestCa
se "django.test.LiveServerTestCase" )) the static assets need to be served
along the rest of the content so the test environment reproduces the real one
as faithfully as possible, but `LiveServerTestCase` has only very basic static
file-serving functionality: It doesn't know about the finders feature of the
`staticfiles` application and assumes the static content has already been
collected under
[`STATIC_ROOT`](https://docs.djangoproject.com/en/1.9/ref/settings/#std
:setting-STATIC_ROOT).

Because of this, `staticfiles` ships its own [`django.contrib.staticfiles.test
ing.StaticLiveServerTestCase`](https://docs.djangoproject.com/en/1.9/ref/contr
ib/staticfiles/#django.contrib.staticfiles.testing.StaticLiveServerTestCase
"django.contrib.staticfiles.testing.StaticLiveServerTestCase" ), a subclass of
the built-in one that has the ability to transparently serve all the assets
during execution of these tests in a way very similar to what we get at
development time with `DEBUG = True`, i.e. without having to collect them
using [`collectstatic`](https://docs.djangoproject.com/en/1.9/ref/contrib/stat
icfiles/#django-admin-collectstatic) first.

## 部署

[`django.contrib.staticfiles`](https://docs.djangoproject.com/en/1.9/ref/contr
ib/staticfiles/#module-django.contrib.staticfiles "django.contrib.staticfiles:
An app for handling static files." ) provides a convenience management command
for gathering static files in a single directory so you can serve them easily.

  1. Set the [`STATIC_ROOT`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-STATIC_ROOT) setting to the directory from which you'd like to serve these files, for example:
    
        STATIC_ROOT = "/var/www/example.com/static/"
    

  2. Run the [`collectstatic`](https://docs.djangoproject.com/en/1.9/ref/contrib/staticfiles/#django-admin-collectstatic) management command:
    
        $ python manage.py collectstatic
    

This will copy all files from your static folders into the
[`STATIC_ROOT`](https://docs.djangoproject.com/en/1.9/ref/settings/#std
:setting-STATIC_ROOT) directory.

  3. Use a web server of your choice to serve the files. [_Deploying static files_](https://docs.djangoproject.com/en/1.9/howto/static-files/deployment/) covers some common deployment strategies for static files.

## 了解更多

This document has covered the basics and some common usage patterns. For
complete details on all the settings, commands, template tags, and other
pieces included in [`django.contrib.staticfiles`](https://docs.djangoproject.com/en/1.9/ref/contrib/staticfiles/#module-django.contrib.staticfiles"django.contrib.staticfiles: An app for handling static files." ), see [_thestaticfilesreference_](https://docs.djangoproject.com/en/1.9/ref/contrib/staticfiles/).

