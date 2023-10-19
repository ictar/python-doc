原文：[Prototyping using Django](https://code.abhi.co/prototyping-django/)

我正在写一个关于如何原型化以及只使用一点工具就可以快速的发布产品的系列！这是一个具有三部分的教程。第一部分将用Django编写一个API。第二部分将使用原生React编写一个IOS应用，而最后一部分将使用Angular.JS编写一个Web应用。这三个工具将允许你进行原型化并快速的推出一些东西！

* * *

让我们首先开始使用Django编写我们的API！你现在所需要知道的是，“Django是一个高级的Python Web框架”。如果你不清楚一个Web框架是什么，或者过去还没创建过一个API，那么跟着我们吧，希望你可以体会到其精髓！

在本教程中，我们将使用Python 2.7.10。我们将要制作一个APP来显示正在发生的事件列表。首先，让我们来安装一个[虚拟环境](http://docs.python-guide.org/en/latest/dev/virtualenvs)! 具体来说，我们将使用[virtualenvwrapper](http://virtualenvwrapper.readthedocs.org/en/latest/index.html). 这使你可以轻松地在虚拟环境之间进行切换。如果你已经安装好了虚拟环境，那么你可以自由使用自己的环境并跳过这一部分。

在你的终端运行下列命令：
```sh
pip install virtualenv
pip install virtualenvwrapper
```

将下列内容增加到你的`~/.bash_profile`中:
```sh
export WORKON_HOME=$HOME/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh
```

现在，你已经安装了`virtualenvwrapper`，你可以通过运行`mkvirtualenv <project_name>`来创建一个虚拟环境。我们将调用我们的项目`today`。它只是你的项目名。你可以用任何你想要的名字来替换它！`todayapp`只是该虚拟环境的名字，因此，你可以自由称其为你的项目名。所以，让我们继续，并运行：

`mkvirtualenv todayapp`

在你创建这个虚拟环境后，你应该位于此虚拟环境中！在你的终端窗口中，应该会看到这样的东西：`(todayapp)abhiagarwal at Abhis-MacBook-Pro in ~`。第一部分只是让你知道你正位于哪个虚拟环境中。要离开此虚拟环境，只需运行`deactivate`，而要进入一个虚拟环境，则运行`workon todayapp`。

太棒了！现在，你有了一个虚拟环境。我们可以开始构建我们的Django API了！让我们开始Django开发！首先，你需要在你的虚拟环境中安装django。所以，留在你所在的虚拟环境中，然后运行：

`pip install Django==1.9
`

这将安装1.9版本的Django。我们将坚持使用1.9版本，因为它是最新的版本（在此文撰写时）。如果你想要看看在你的虚拟环境中所有可用的Python包，那么可以运行`pip freeze`。现在，当你运行`pip freeze`时，应该可以看到：

`Django==1.9
wheel==0.24.0
`

这应该已经在你的环境中安装了一个名为`django-admin`的命令。我们将把我们的API放置在一个名为`today-api`的文件夹中。所以，继续执行：

`mkdir today-api
cd today-api
`

此时，它位于你的计算机上的何处并不太重要。要开始一个新的Django项目，你可以运行：

`django-admin startproject today
`

这将在该文件夹中创建一个名为today的目录。现在，你应该可以见到以下结构：

`.
├── today-api
    └── today
        ├── manage.py
        └── today
            ├── __init__.py
            ├── settings.py
            ├── urls.py
            └── wsgi.py
`

这些文件将负责拟定Django应用如何运行。来自于[Django 文档](https://docs.djangoproject.com/en/1.9/intro/tutorial01/) (它们非常不可思议，并且非常有用):

*   “外部的`today/`根目录只是拟定项目的一个容器。它的名字对Django并不重要；你可以将其重命名为任何你喜欢的名字。”
*   “`manage.py`: 一个命令行工具，它允许你以多种方式与此Django项目进行交互。你可以在[django-admin and manage.py](https://docs.djangoproject.com/en/1.9/ref/django-admin/)中阅读所有关于manage.py的信息。”
*   “内部的 `today/` 目录是你的项目实际的Python包。它的名字是该Python包的名字，你将需要使用它来导入其中的任何东西（例如，today.urls）。”
*   “`today/__init__.py`: 一个空文件，它告诉Python，这个目录应该被认为是一个Python包。(如果你是一个Python初学者，那么你可以阅读Python官方文档中的[packages](https://docs.python.org/2/tutorial/modules.html#packages)以获得更多信息)”
*   “`today/settings.py`: 该Django项目的设置/配置。Django的设置将告诉你关于设置如何工作的一些信息。”
*   “`today/urls.py`: 该Django项目的URL声明；你的Django驱动的网站的一个目录。你可以从[URL dispatcher](https://docs.djangoproject.com/en/1.9/topics/http/urls/)中阅读关于URL的更多信息。”
*   “`today/wsgi.py`: 服务于你的项目的WSGI兼容的Web服务器的一个入口点。见[如何使用WSGI进行部署](https://docs.djangoproject.com/en/1.9/howto/deployment/wsgi/)以获得更多信息。”

特别是当你进行原型化时，你并不想重造轮子。所以我们复用一些Python包，这些包将帮助我们加快开发进程。首先，我们将建立应用的结构。此时，我们将使用一些Django最佳实践作为开始。

最初运行你的Django应用，可以`cd today`，然后运行`./manage.py runserver`。它将提醒你：“你有未应用的迁移；你的应用将可能无法正常工作，除非应用它们（you have unapplied migrations; your app may not work properly until they are applied）”。 这是Django在告诉你，你基本上必须运行数据库迁移。你可以简单的遵循Django告诉你需要做的事，运行`python manage.py migrate`。它可用`./manage.py migrate`替代。基本上，这意味着，Django在一开始便将必须的模块导入到你的数据库中。最初，Django使用SQLite。所以当你运行迁移时，应该可以在你的目录下见到`db.sqlite3`。稍后，我们将要使用其他的数据库，但现在，我们将继续使用SQLite。

现在，你应该可以运行你的Django应用了。运行它，然后在你的电脑上访问`http://127.0.0.1:8000/`。当你访问该URL时，一开始，你应该会看到`It worked!`！恭喜！你已经成功的运行了你的Django Web服务器。无论接下来会发生什么，你仍然可以成功的运行你的Django应用！

## 增加应用(app)

你的Django项目（名为`today`）中的主目录被称为一个项目。现在，我们必须增加`apps`（应用）。这些应用是你的Django项目中较小的模块，它们代表了你所构建的东西。让我们创建应用文件夹：

`$ cd today
$ mkdir apps
$ cd apps
$ touch __init__.py
$ mkdir events
$ cd events
$ touch __init__.py
`

现在，你的目录结构应该看起来像这样：

`.
└── today-api
    └── today
        ├── db.sqlite3
        ├── manage.py
        └── today
            ├── __init__.py
            ├── apps
            │   ├── __init__.py
            │   └── events
            │       └── __init__.py
            ├── settings.py
            ├── urls.py
            └── wsgi.py
`

棒棒哒！现在，我们已经在我们的项目中安装了一个名为`events`的应用。这将是我们为我们的事件API编写逻辑的应用程序。首先，在文件夹`events`中创建一个名为`models.py`的文件。你可以通过运行`touch models.py`来完成。在那个文件夹中，我们将为Event模型编写逻辑。一个模型可以被看作不同方法和变量的组合。如果你之前还没有遇见过面对对象编程，那么别担心！我们的Event模型只是如何保存和从数据库中加载数据的蓝图。在`models.py`文件中黏贴下述代码：
```py
# -*- coding: utf-8 -*-
from django.db import models


class Event(models.Model):
  name = models.TextField(blank=False, max_length=100)
  description = models.TextField(blank=False, max_length=1000)
  date = models.DateTimeField(blank=False, null=False)

  def __unicode__(self):
      return self.name
```

最初，我们的模型中有三个字段。第一个名为`name`，第二个名为`description`，而第三个名为`date`。name和description字段都是`TextField`类型。Django自带了“安装电池”，这意味着它为我们提供了很多东西，我们可以开始使用。date字段是`DateTimeField`类型。我们可以传递某些参数到这些字段中。在name字段，我们传递：

*   `blank=False` – 意味着它不能为空 - 它不是一个可选字段
*   `max_length=100` – 事件名最多为100个字符

在data字段中，我们传递：

*   `null=False` – 不会在你的数据库中将此值设置为NULL。“Django字段类型的空值，例如DateTimeField或者ForeignKey在数据中将存储为NULL”([参考](http://stackoverflow.com/questions/8609192/differentiate-null-true-blank-true-in-django)).

这里，我们已经为一个单一事件设置了一个基础模型。每一个事件必须有一个名字，一个描述，以及一个日期。我们应该继续并增添一个`image`字段。一个时间可以有一个图像！在date下面，你应该增加：

`image = models.URLField(blank=True, max_length=500)`

它使用了Django内置的`URLField`。这与`TextField`不同，因为它在内部进行校验，并确保当你添加一个URL时，该URL是正确的格式。这里，我们使用了`blank=True`，它意味着image是一个可选字段。当我们添加一个新的事件时，我们可能并没有一个关于它的图像 - 可选部分使得我们也不需要有。最终产品应该是这样的：
```py
# -*- coding: utf-8 -*-
from django.db import models


class Event(models.Model):
    name = models.TextField(blank=False, max_length=100)
    description = models.TextField(blank=False, max_length=1000)
    date = models.DateTimeField(blank=False, null=False)
    image = models.URLField(blank=True, max_length=500)

    def __unicode__(self):
        return self.name
```

还是棒棒哒！所以，我们已经增加了我们的基础模型。现在，通过运行`touch admin.py`来增加一个名为`admin.py`的文件。Django自带了一个管理接口，通过此接口，你可以见到你的模型中的所有数据。这个文件告诉Django将你的模型增加到该管理接口中。在这个文件中，你应该继续并只增加：
```py
# -*- coding: utf-8 -*-
from django.contrib import admin

from .models import Event

admin.site.register(Event)
```

现在，我们已经增加了模型和管理文件，我们将开始构建我们的API视图。首先，我们必须增加一个Django包，并进行配置。我们将使用Django Rest框架，它用于轻松构建REST API。继续并运行（在你的虚拟环境中）：

`pip install djangorestframework==3.3.2`

此时，我们应该创建一个`requirements.txt`文件。它将允许你跟踪已安装的Python包。回到基础的`today-api`文件夹。然后运行`touch requirements.txt`。现在，你可以使用`pip freeze`，然后将其输出添加至requirements.txt。要完成这点，只需执行命令`pip freeze > requirements.txt`。这应该会将你当前的Python包添加到该文件中。想查看该文件，你可以在终端中运行`cat requirements.txt`。此时应该可以见到：

`Django==1.9
djangorestframework==3.3.2
wheel==0.24.0
`

将你的要求保存在一个文件中是很重要的。在未来，安装你的要求，你可以简单的通过`pip install -r requirements.txt`完成。现在，你的目录应该看起来像这样：

`.
└── today-api
    ├── requirements.txt
    └── today
        ├── db.sqlite3
        ├── manage.py
        └── today
            ├── __init__.py
            ├── apps
            │   ├── __init__.py
            │   └── events
            │       ├── __init__.py
            │       ├── admin.py
            │       └── models.py
            ├── settings.py
            ├── urls.py
            └── wsgi.py
`

## 构建settings.py

现在，打开你的`settings.py`文件。它应该与`apps`文件夹位于同一个目录。在`settings.py`文件中，你应该会见到一个名为`INSTALLED_APPS`的变量。它管理当你运行`./manage.py runserver`时，Django会为你运行哪些应用。我们必须将我们的新应用添加到里面。对此，我们将添加`'rest_framework'`, 和 `'today.apps.events'`。Django路径被设置为`manage.py`所在文件夹的相对路径。所以你所有的文件将相对于`today`文件夹。`urls.py`文件将为`today.urls`，等等。现在，让我们修改`INSTALLED_APPS`为：

`INSTALLED_APPS = [
     'django.contrib.admin',
     'django.contrib.auth',
     'django.contrib.contenttypes',
     'django.contrib.sessions',
     'django.contrib.messages',
     'django.contrib.staticfiles',
     'rest_framework',
     'today.apps.events',
]
`

## 进行迁移

现在，我们必须做些振奋人心的事。我们必须进行迁移！所以，现在，你已经创建了一个Event模型，我们必须使用你编写的模式，然后让Django在数据库中为其创建一个表。你已经无形中使用字段创建了这个模型，但是现在，我们将如何在数据中对此结构进行镜像呢？好吧，Django自带了这些工具。运行`./manage.py makemigrations events`。`events`是指我们正在谈论的特定的应用。当你运行它时，它应该会返回：

`Migrations  for  'events':
  0001_initial.py:
    - Create model Event
`

如果打印出一些不同的东西，那么说明你在某个地方犯了一个错误。这个命令只是创建迁移。它在`apps/events`文件夹中创建了一个名为`migrations`的文件夹（你不应该删除它！），然后在其中创建了一个包含你的模式的具体细节的python文件。现在，你应该运行`./manage.py migrate events`。这将返回：

`Operations to perform:
  Apply all migrations: events
Running migrations:
  Rendering model states... DONE
  Applying events.0001_initial... OK
`

这意味着，我们已经成功的获得了我们的模式，然后将这些字段增添到数据库中。每当你为你的模型增加一个新的字段 - 你应该总是创建迁移（使用`makemigrations`），然后对它进行迁移（使用`migrate`）。Django使得它非常非常容易做到这些。传统地，你必须要遭受很多痛苦才能将新的字段增添到你的数据库中 - 特别是如果你正在处理SQL数据库。Django的ORM包含了非常多的电池。

## 编写我们的第一个视图集(ViewSet)

Django的Rest框架中的视图集(ViewSet)被定义为“Django的REST框架允许你在一个类中将一组相关的视图的逻辑组合在一起，称为视图集。在其他框架中，你也可以找到类似概念的实现，其名称类似于‘资源(Resources)’或者‘控制器(Controllers)’” ([参考](http://www.django-rest-framework.org/api-guide/viewsets/))。

让我们回到我们的事件应用。只用`cd`进去。这里，创建一个名为`views.py`的文件。这是负责处理当某些东西试图到达你的事件终端时会发生什么的文件。这里，你应该添加：
```py
# -*- coding: utf-8 -*-
from .models import Event
from .serializers import EventSerializer
from rest_framework import viewsets


class EventsViewSet(viewsets.ModelViewSet):
  serializer_class = EventSerializer

  def get_queryset(self,):
      queryset = Event.objects.all()
      uid = self.kwargs.get('pk')
      if uid:
          return queryset.filter(pk=uid)
      else:
          return queryset
```

这是一个视图集。基本上，当一个请求进入它时，它调用`get_queryset`来获得一个`queryset`，这是它必须返回的。一个queryset可以简单被看成`Event`模型的实例数组。当你访问`/events`时，你想其返回所有的事件，但当你访问`/events/1`时，你想其返回`id`为1的事件。在Django中，这个`id`被称为`pk`或者主键。所以我们坚持是否`uid`不为None，若它不为None，我们想要过滤此queryset，然后返回带该ID的事件。

现在，我们必须编写一个串化器(Serializer)。Django的Rest框架中的一个串化器的定义是“串行器允许诸如查询集合模型实例的复杂数据转换为可以很容易被渲染成JSON, XML或者其他内容类型的原生Python数据类型。串化器也提供反序列化，允许已解析的数据在第一次检验传入的数据后，被转换回复杂类型。” ([参考](http://www.django-rest-framework.org/api-guide/serializers/))。
```py
# -*- coding: utf-8 -*-
from .models import Event
from rest_framework import serializers


class EventSerializer(serializers.HyperlinkedModelSerializer):

    def to_representation(self, obj):
        return {
            'id': obj.pk,
            'name': obj.name,
            'description': obj.description,
            'date': obj.date,
            'image': obj.image,
        }

    class Meta:
        model = Event
        fields = ('name', 'description', 'date', 'image',)
```

## 建立我们的URL

现在，我们已经完成了我们的视图集和串化器！现在，我们终于可以继续前进，并预览我们的API。但首先，我们必须建立我们的`urls.py`文件！更改目录到`apps`文件夹。在`apps`文件夹中编写一个名为`router.py`的文件。在这个文件中，你可以添加：

` # -*- coding: utf-8 -*-
 from rest_framework  import routers
 from today.apps.events.views  import EventsViewSet

router = routers.DefaultRouter()
router.register( r'events', EventsViewSet, base_name= 'event')
`

在`urls.py`文件中添加：

` # -*- coding: utf-8 -*-
 from django.conf.urls  import include, url
 from django.contrib  import admin

 from today.apps.router  import router

urlpatterns = [
    url( r'^admin/', include(admin.site.urls)),
    url( r'^api/', include(router.urls)),
]
`

现在，你可以启动你的应用程序。 `./manage.py runserver`。 在浏览器中访问`http://localhost:8000/api/events/`。这就是你的API！当你隐身打开它，你应该可以使用你的API，并且创建一个GET请求来获取所有的事件数据。如果你想要作为JSON进行格式化，尝试访问这个url – `http://localhost:8000/api/events/?format=json`。当你将东西推送到生产环境时，你可以使其成为默认格式。Django Rest框架提供这个整洁的接口使你轻松的玩转你的数据。尝试添加一些数据！当你访问`http://localhost:8000/api/events/`时，在底部应该有一个名为`HTML form`的区域。这里，你可以增加数据。下面是一些样例数据：

` Name:  Party on Broadway
 Description:  There's a party on Broadway!
 Date:  01/20/2016, 12:00 PM
`

或者如果你想要通过`原始数据(Raw data)`进行创建：

`{
    " name":  "Party on Broadway",
    " description":  "There's a party on Broadway!",
    " date":  "2016-01-20T12:00:00Z",
    " image":  ""
}
`

它应该会返回：

`HTTP 201 Created
Allow: GET, POST, HEAD, OPTIONS
Content-Type: application/json
Vary: Accept

{
     "date":  "2016-01-20T12:00:00Z",
     "description":  "There's a party on Broadway!",
     "id": 1,
     "image":  "",
     "name":  "Party on Broadway"
}
`

现在，当你访问`/api/events` API终端 - 你应该会见到：

`HTTP 200 OK
Allow: GET, POST, HEAD, OPTIONS
Content-Type: application/json
Vary: Accept

[
    {
         "date":  "2016-01-20T12:00:00Z",
         "description":  "There's a party on Broadway!",
         "id": 1,
         "image":  "",
         "name":  "Party on Broadway"
    }
]
`

现在，我们必须进行一些权限工作。现在(译者：原文是right now，但感觉应该不是马上的意思，而是当前)，任何人都可以增加数据到此API终端。Django Rest框架自带了一个权限框架，它允许我们很容易的解决这个问题！回到`settings.py`文件，然后增加一个新的变量到里面：

`REST_FRAMEWORK = {
     'DEFAULT_PERMISSION_CLASSES': [
         'rest_framework.permissions.IsAuthenticatedOrReadOnly'
    ]
}
`

Django Rest框架有一堆你可以在这里使用的设置项。它也为每一个模型提供一个权限框架。所以你可以基于每一个模型声明并定制你自己的权限。例如，如果你拥有用户配置文件，你可以为用户自定义权限，使其能够编辑他们自身的权限，而不能编辑其他人的。在你增添此行后，你不再能够添加事件。现在，你将必须创建一个Django超级用户，然后使用`admin`功能！运行以下命令：

`./manage.py createsuperuser
`

然后遵照提示。它应该会询问你：

`Username (leave blank to <span class="hljs-operator"> use  'abhi'): abhi
Email address: hi@abhi.co
 Password: xxxxxxx1
 Password (again): xxxxxxx1
`

现在，访问`http://localhost:8000/admin/`，然后填写你的详细信息。现在，你应该会见到`admin`面板。你应该会看到`Groups`, `Users`, 和 `Events`。在这里，你可以创建，读取，更新或删除行的事件，组或用户。当你新增新的模型时，你应该也会见到它们出现在这里！如果你访问`Events`部分，你应该会在那里见到“Party on Broadway”事件。你应该能够创建，读取，更新或删除它！

现在，当你访问`http://localhost:8000/api/events/`时，你应该又可以新增一个事件了！当你隐身打开，你将不能够新增一个事件。这太棒了！这是非常基本的安全，而你当然可以做得更好。

此外，你应该也可以访问`http://localhost:8000/api/events/1`，且只能看到`id` = 1的事件。我们已经成功使用一个终端创建了一个非常简单的REST API。

最终，你的目录结构将是：

`.
└── today-api
    ├── requirements.txt
    └── today
        ├── db.sqlite3
        ├── manage.py
        └── today
            ├── __init__.py
            ├── apps
            │   ├── __init__.py
            │   ├── events
            │   │   ├── __init__.py
            │   │   ├── admin.py
            │   │   ├── migrations
            │   │   │   ├── 0001_initial.py
            │   │   │   └── __init__.py
            │   │   ├── models.py
            │   │   ├── serializers.py
            │   │   └── views.py
            │   └── router.py
            ├── settings.py
            ├── urls.py
            └── wsgi.py

最终的项目位于Github [这里](https://github.com/AbhiAgarwal/prototyping-django)。

在下一个原型化教程，我们将经历如何使用带着一个原生React iOS应用的Django来连接此REST API！

## 资源

*   [Django文档](https://docs.djangoproject.com/en/1.9/)
