# 编写你的第一个Django APP，第一部分

我们通过一个例子来学习。
通过这篇教程，我们会告诉你如何编写一个基础的投票程序。
它包括以下两个部分：
- 一个公共站点供人们查看投票项并给它们投票。
- 一个管理站供你添加，修改和删除投票项。

我们假设你已经[安装](https://docs.djangoproject.com/en/1.11/intro/install/)了Django。你可以在终端命令行中运行下面的命令来确定Django是否已经安装和安装了哪个版本：
```bash
$ python -m django --version
```
如果Django已经安装了，你应该能看到你安装的版本，否则，你会看到一条错误信息：“No module named django”。
这个教程是按照Django1.11和Python3.4及之后的版本编写的。如果Django的版本不符合，你可以通过本页面右下角的版本切换来参考符合你的版本的Django教程，或者把Django更新到最新版本。如果你仍在使用Python2.7，你需要按照评论里的描述稍微修改一下示例代码。
>在哪里获取帮助：如果你在阅读本教程时遇到问题，请向[Django用户](https://docs.djangoproject.com/en/1.11/internals/mailing-lists/#django-users-mailing-list)发送一条消息，或访问[#django on irc.freenode.net](irc://irc.freenode.net/django)来和其他可能帮助到你的用户交流。

## 创建一个项目
如果这是你第一次使用Django，你需要注意一些初始化设置。即，你需要自动生成一些创建Django[项目](https://docs.djangoproject.com/en/1.11/glossary/#term-project)的代码——一组Django示例的设置，包括数据库配置，Django的特殊选项，和应用的特殊设置。
从命令行进入你想要储存代码的文件夹，然后运行下面的命令：
```bash
$ django-admin startproject mysite
```
这回创建一个名为`mysite`的文件夹在当前文件夹下，如果它不起作用，参考[运行django-admin的问题](https://docs.djangoproject.com/en/1.11/faq/troubleshooting/#troubleshooting-django-admin)。
>**注意**
>你需要避免给项目取Python和Django自带内容的名字。尤其是应该避免使用类似`Django`（和Django本身冲突）或`test`（和一个Python自带库的名字冲突）。
>
>---
>**代码应该放在哪里？**
>如果你的环境是原始的旧PHP（没有使用现代化的框架），你可能习惯把代码放在Web服务器的文档根目录下（类似`/var/www/`这样的地方）。而使用Django的话，不要这么做。把任何Python代码放在Web服务器的根目录都不是个好主意，因为这需要承担用户可能通过网页查看到你的代码的风险，不利于安全。
>把你的代码放在根目录之外的某个文件夹，例如`/home/mycode`。

我们来看一下`startproject`创建了哪些东西：
```
mysite/
    manage.py
    mysite/
        __init__.py
        settings.py
        urls.py
        wsgi.py
```
这些文件是：
- 外部的`mysite/`根目录只是一个项目的容器。它的名字对Django没有影响，你可以随便修改这个名字；
- `manage.py`：一个命令行工具，使你能够用多种不同的方式和Django项目交互。你可以在[django-admin和manage.py](https://docs.djangoproject.com/en/1.11/ref/django-admin/)阅读所有有关`manage.py`的细节；
- 内部的`mysite/`目录是真正的你的项目的Python包。它的名字是一个Python包名，你会需要使用它来导入包内的所有东西。（如`mysite.urls`）。
- `mysite/__init__.py`：一个空文件，它的作用是告诉Python这个文件夹应该被看做一个Python包。如果你是一个Python初学者，可以在Python的官方文档里阅读[更多有关包的信息](https://docs.python.org/3/tutorial/modules.html#tut-packages)；
- `mysite/settings.py`：该Django项目的设置和配置。[Django设置](https://docs.djangoproject.com/en/1.11/topics/settings/)会告诉你设置是怎么工作的。
- `mysite/urls.py`：该Django文件的URL声明；你的Django驱动的站点的“一组内容”，你可以在[URL调度程序](https://docs.djangoproject.com/en/1.11/topics/http/urls/)来阅读更多有关URLs的东西。
- `mysite/wsgi.py`：一个提供给WSGI兼容web服务器来运行你的程序的接入点，查阅[如何使用WSGI部署](https://docs.djangoproject.com/en/1.11/howto/deployment/wsgi/)来获取详细信息。

## 开发服务器
我们来确认一下你的Django程序是否工作。切换到外层的`mysite`目录（如果你还没有做这一步的话），然后运行下面的命令：
```
$ python manage.py runserver
```
你会看见下面的输出：
```
Performing system checks...

System check identified no issues (0 silenced).

You have unapplied migrations; your app may not work properly until they are applied.
Run 'python manage.py migrate' to apply them.

May 06, 2017 - 15:50:53
Django version 1.11, using settings 'mysite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```
>**注意**
>现在暂且忽略有关未应用的数据库迁移的警告信息，稍后我们会处理数据库。

现在你已经运行了一个Django开发服务器，一个完全用Python语言编写的轻量级的Web服务器，我们在Django里包括了这个以便你可以快速开发，而不用处理生产服务器的配置——像Apache那样——直到你准备好发布产品。
现在需要注意：**不要**在任何类似于生产环境中使用此服务器。它仅仅是在开发时使用的。（Django仅仅是个Web框架，而不是Web服务器）。
现在服务器运行起来了，在你的浏览器中访问http://127.0.0.1:8000/。你会看到一个赏心悦目的浅蓝色的“Welcome to Django”的页面，它工作了！

>**改变端口**
>默认地，[`runserver`](https://docs.djangoproject.com/en/1.11/ref/django-admin/#django-admin-runserver)命令使用内部IP的8000端口启动开发服务器。
>如果你想要改变服务器的端口，就把它作为一个命令行参数传递。举个例子，这个命令会在8080端口启动服务器：
>```
>$ python manage.py runserver 8080
>```
>如果你想要改变服务器的IP，把它和端口一起传递，例如，要监听所有可用的公共IP（这在你使用Vagrant或在互联网的其他电脑上展示你的作品时十分有用），使用：
>```
>$ python manage.py runserver 0:8000
>```
>`0`是`0.0.0.0`的缩写，完整的开发服务器的文档可以在[runserver](https://docs.djangoproject.com/en/1.11/ref/django-admin/#django-admin-runserver)参考中找到。
>
>---
>**自动重启服务器**
>开发服务器会根据需要自动为每个请求重新加载Python代码。你不需要因为代码改动而重启服务器来使其生效。然而，一些像添加文件这样的改动并不会触发重启，所以你需要手动重启服务器。

## 创建投票（Polls）应用
现在你的环境——一个“项目”——已经设置好了，你现在可以开始做一些事情了。
你在Django里面编写的每个应用都包括一个遵循特定约定的Python包。Django能够自动生成应用的基础文件夹结构，所以你可以专注于写代码而不是创建文件夹。
>项目vs.应用
>项目和应用之间的区别是什么？一个应用是一个会做些什么的Web应用——例如：一个博客系统，一个公共记录数据库，或者一个简单的投票应用。一个项目是配置和特定网站应用的集合。一个项目可以包含多个应用，一个应用可以在多个项目中。

你的应用可以在任何你的Python可以检索到的路径上。在这个教程中，我们在`manage.py`的同一个文件夹下创建投票应用以便它可以被自己的顶层模块调用而不是`mysite`的子模块。
为了创建你的应用，确定你在`manage.py`的同级文件夹下，然后输入以下命令：
```
$ python manage.py startapp polls
```
这回创建一个叫做`polls`的文件夹，看起来像下面这样：
```
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
```
这个文件夹结构将装载这个投票应用。

## 编写你的第一个视图
哦们来编写第一个视图文件。打开文件`polls/views.py`，写下下面的代码：
```python
# polls/views.py

from django.http import HttpResponse


def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```
这是Django里可实现的最简单的视图。我们需要把它映射到URL上来调用这个视图——因此我们需要一个URL配置。
为了在`polls`文件夹下创建一个URL配置文件，新建一个文件命名为`urls.py`。你的应用文件夹看起来应该向下面这样：
```
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    urls.py
    views.py
```
在`polls/urls.py`文件中包含以下代码：
```python
# polls/urls.py

from django.conf.urls import url

from . import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
]
```
下一步是指定根URL配置到`polls.urls`模块。在`mysite/urls.py`中，添加一个`import`语句来导入`django.conf.urls.include`，然后在`urlpatterns`列表里插入一个`include()`，就像下面这样：
```python
# mysite/urls.py

from django.conf.urls import include, url
from django.contrib import admin

urlpatterns = [
    url(r'^polls/', include('polls.urls')),
    url(r'^admin/', admin.site.urls),
]
```
`include()`方法允许引用其他的URL配置，注意其中的正则表达式没有`$`（字符串结尾匹配符）而是一个尾随斜杠。当Django遇到`include()`方法，它会排除与该点匹配的任何部分，并将剩余的字符串发送到引用的URLconf进行进一步处理。
`include()`背后的理念是使URLs可以“即插即用”。因为`polls`本身的URL配置在他们自己的URL配置文件`polls/urls.py`中，可以被放在“/polls/”文件夹，或“/fun_polls/”文件夹，或“/content/polls/”文件夹，或任何其他的路径，而应用仍然能运行。
>什么时候使用`include()`
>你应该总是在你需要引入其他的URL匹配模式时使用使用`include()`，`admin.site.urls`是唯一的例外。
>
>---
>不符合你看到的？
>如果你看到了`include(admin.site.urls)`erbushi `admin.site.urls`，你可能使用了与本教程不符合的Django版本，你可以选择切换到旧版本的教程或新版本的Django。

现在你绑定了一个`index`视图到URL配置中，我们确定一下他是否工作，运行以下命令：
```
$ python manage.py runserver
```
在浏览器中访问`http://localhost:8000/polls/`，你会看到你在`index`视图中定义的“Hello, world. You’re at the polls index.”的文字。
`url()`方法传递四个参数，两个是必须的：`regex`和`view`，两个是可选的：`kwargs`和`name`。在这里我们有必要查阅一下这些参数。

### URL()argument:regex
"regex"术语通常表示“regular expression”（正则表达式）的缩写，这是一种用于匹配字符串中的模式的语法，或者在这种情况下是url模式。 Django从第一个正则表达式开始遍历整个列表，将请求的URL与每个正则表达式进行比较，直到找到匹配的一个。
注意这些正则表达式不会搜索GET和POST的参数部分，或域名部分。举个例子，在访问**https://www.example.com/myapp/**的请求中，URL配置会寻找`myapp/`，在**https://www.example.com/myapp/?page=3**的请求中，URL配置还是会寻找`myapp/`。
如果你需要关于正则表达式方面的帮助，参看[Wikipedia’s entry](https://en.wikipedia.org/wiki/Regular_expression)和Python的[`re`](https://docs.python.org/3/library/re.html#module-re)模块的文档。此外，Jeffrey Friedl的O'Reilly书《掌握正则表达式》也是非常棒的。 然而，实际上，你不需要是正则表达式的专家，因为您只需要知道如何捕获简单的模式。 实际上，复杂的正则表达式的查找性能会很差，所以你可能不应该依靠正则表达式的全部功能。
最后，一个性能说明：这些正则表达式在第一次加载URLconf模块时被编译。 它们超级快（只要查找不是太复杂，如上所述）。
### URL()argument:view
当Django发现正则表达式匹配时，Django会调用指定的视图函数，使用[`HttpRequest`](https://docs.djangoproject.com/en/1.11/ref/request-response/#django.http.HttpRequest)对象作为第一个参数，并将正则表达式中的任何“捕获”值作为其他参数。 如果正则表达式使用简单的捕获，则值作为位置参数传递; 如果它使用命名捕获，则值作为关键字参数传递。 我们稍后会给出一个例子。
### URL()argument:kwargs
任意关键词参数可以在字典中传递到目标视图。我们不会在教程中使用Django的这个功能。
### URL()argument:name
命名您的URL可让您从Django其他地方明确地引用它，特别是在模板中。 这个强大的功能可让您全面更改项目的URL模式，同时只修改单个文件。

当你对基本请求和响应流程感到满意时，请阅读本教程的第2部分，开始使用数据库。
