#
Django一览

由于Django是在一个快节奏的新闻编辑室环境下开发出来的，因此它被设计成让普通的网站开发工作简单而快 捷。以下简单关于如何用 Django 编写一个数据库驱动的Web应用程序的非正式的概述。

本文档的目标是给你描述足够的技术细节能让你理解Django是如何工作的，但是它并不表示是一个新手指南或参考目录 – 其实这些我们都有! 当你准备新建一个项目，你可以 [从新手指南开始](https://docs.djangoproject.com/en/1.11/intro/tutorial01/) 或者 [深入阅读详细的文档](https://docs.djangoproject.com/en/1.11/topics/)。

## 设计你的模型(model)
尽管你在 Django 中可以不使用数据库，但是它提供了一个完善的可以用 Python 代码描述你的数据库结构的对象关联映射([ORM，object-relational mapper](https://en.wikipedia.org/wiki/Object-relational_mapping))。

[数据模型语法](https://docs.djangoproject.com/en/1.11/topics/db/models/) 提供了许多丰富的方法来展现你的模型 – 到目前为止，它一直在解决多年的数据库模式问题。
下面是个简单的例子：
```python
# mysite/news/models.py

from django.db import models

class Reporter(models.Model):
full_name = models.CharField(max_length=70)

def __str__(self): # __unicode__ on Python 2
return self.full_name

class Article(models.Model):
pub_date = models.DateField()
headline = models.CharField(max_length=200)
content = models.TextField()
reporter = models.ForeignKey(Reporter, on_delete=models.CASCADE)

def __str__(self): # __unicode__ on Python 2
return self.headline
```

## 安装
下一步，运行 Django 命令行工具来自动创建数据库表：
```bash
$ python manage.py migrate
```
`migrate` 命令会查找你所有可用的模型(models)然后在你的数据库中创建还不存在的数据库表，同时提供可选的[更丰富的模式控制](https://docs.djangoproject.com/en/1.11/topics/migrations/)。

## 享用便捷的 API

接着，你就可以使用一个便捷且功能丰富的 Python API 来访问你的数据。API 是动态生成的，不需要代码生成：
```python
# Import the models we created from our "news" app
>>> from news.models import Reporter, Article

# No reporters are in the system yet.
>>> Reporter.objects.all()
<QuerySet []>

# Create a new Reporter.
>>> r = Reporter(full_name='John Smith')

# Save the object into the database. You have to call save() explicitly.
>>> r.save()

# Now it has an ID.
>>> r.id
1

# Now the new reporter is in the database.
>>> Reporter.objects.all()
<QuerySet [<Reporter: John Smith>]>

# Fields are represented as attributes on the Python object.
>>> r.full_name
'John Smith'

# Django provides a rich database lookup API.
>>> Reporter.objects.get(id=1)
<Reporter: John Smith>
>>> Reporter.objects.get(full_name__startswith='John')
<Reporter: John Smith>
>>> Reporter.objects.get(full_name__contains='mith')
<Reporter: John Smith>
>>> Reporter.objects.get(id=2)
Traceback (most recent call last):
...
DoesNotExist: Reporter matching query does not exist.

# Create an article.
>>> from datetime import date
>>> a = Article(pub_date=date.today(), headline='Django is cool',
... content='Yeah.', reporter=r)
>>> a.save()

# Now the article is in the database.
>>> Article.objects.all()
<QuerySet [<Article: Django is cool>]>

# Article objects get API access to related Reporter objects.
>>> r = a.reporter
>>> r.full_name
'John Smith'

# And vice versa: Reporter objects get API access to Article objects.
>>> r.article_set.all()
<QuerySet [<Article: Django is cool>]>

# The API follows relationships as far as you need, performing efficient
# JOINs for you behind the scenes.
# This finds all articles by a reporter whose name starts with "John".
>>> Article.objects.filter(reporter__full_name__startswith='John')
<QuerySet [<Article: Django is cool>]>

# Change an object by altering its attributes and calling save().
>>> r.full_name = 'Billy Goat'
>>> r.save()

# Delete an object with delete().
>>> r.delete()
```

## 一个动态的管理接口：它不仅仅是个脚手架 – 还是个完整的房子

一旦你的 models 被定义好，Django 能自动创建一个专业的，可以用于生产环境的 [管理界面](https://docs.djangoproject.com/en/1.11/ref/contrib/admin/) – 一个可让授权用户添加，修改和删除对象的网站。它使用起来非常简单，只需在你的 admin site 中注册你的模型即可。
```python
# mysite/news/models.py
from django.db import models

class Article(models.Model):
pub_date = models.DateField()
headline = models.CharField(max_length=200)
content = models.TextField()
reporter = models.ForeignKey(Reporter, on_delete=models.CASCADE)
# mysite/news/admin.py
from django.contrib import admin

from . import models

admin.site.register(models.Article)
```
这种设计理念是你的网站一般是由一个员工,或者客户，或者仅仅是你自己去编辑 – 而你应该不会想要仅仅为了管理内容而去创建后台界面。

在一个创建 Django 应用的典型工作流中，首先需要创建模型并尽可能快地启动和运行管理站点， 让您的员工(或者客户)能够开始录入数据，然后才开发展现数据给公众的方式。

## 设计你的 URLs

一个干净的，优雅的 URL 方案是一个高质量 Web 应用程序的重要细节。 Django 鼓励使用漂亮的 URL 设计，并且不鼓励把没必要的东西放到 URLs 里面，像 `.php` 或 `.asp`

为了给一个 app 设计 URLs，你需要创建一个 Python 模块叫做[URLconf](https://docs.djangoproject.com/en/1.11/topics/http/urls/)。这是一个你的 app 内容目录， 它包含一个简单的 URL 匹配模式与 Python 回调函数间的映射关系。这有助于解耦 Python 代码和 URLs 。

这是针对上面 Reporter/Article 例子所配置的 URLconf 大概样子：
```python
# mysite/news/urls.py
from django.conf.urls import url

from . import views

urlpatterns = [
url(r'^articles/([0-9]{4})/$', views.year_archive),
url(r'^articles/([0-9]{4})/([0-9]{2})/$', views.month_archive),
url(r'^articles/([0-9]{4})/([0-9]{2})/([0-9]+)/$', views.article_detail),
]
```
上面的代码映射了 URLs ，从一个简单的正则表达式，到 Python 回调函数(“views”)所在的位置。 正则表达式通过圆括号来“捕获” URLs 中的值。当一个用户请求一个页面时， Django 将按照顺序去匹配每一个模式，并停在第一个能匹配请求URL的模式上。(如果没有匹配到， Django 将会展示一个404的错误页面。) 整个过程是极快的，因为在加载时正则表达式就进行了编译。

一旦有一个正则表达式匹配上了，Django 将导入和调用对应的视图，它其实就是一个简单的 Python 函数。每个视图将得到一个 request 对象 – 它包含了 request 的元数据 – 和正则表达式所捕获到的值。

例如：如果一个用户请求了个 URL “/articles/2005/05/39323/”, Django 将会这样调用函数 `news.views.article_detail(request, '2005', '05', '39323')`.

## 编写你的视图(views)

每个视图只负责两件事中的一件：返回一个包含请求页面内容的 [`HttpResponse`](https://docs.djangoproject.com/en/1.11/ref/request-response/#django.http.HttpResponse) 对象; 或抛出一个异常如 Http404 。至于其他就靠你了。

通常，一个视图会根据参数来检索数据，加载一个模板并且根据检索出来的数据来渲染该模板。下面是个接上例的 `year_archive` 例子
```python
# mysite/news/views.py
from django.shortcuts import render

from .models import Article

def year_archive(request, year):
a_list = Article.objects.filter(pub_date__year=year)
context = {'year': year, 'article_list': a_list}
return render(request, 'news/year_archive.html', context)
```
这个例子使用了 Django 的 [模板系统](https://docs.djangoproject.com/en/1.11/topics/templates/)，该模板系统有多种强大的特性，但努力保持着简单易用，甚至非编程人员也会使用。

## 设计你的模板(templates)

上面的例子中载入了`news/year_archive.html`模板。

Django 有一个模板搜索路径板，它让你尽可能的减少冗余而重复利用模板。在你的 Django设置中，你可以指定一个查找模板的[目录列表](https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-TEMPLATES-DIRS)。如果一个模板没有在这个 列表中，那么它会去查找第二个，然后以此类推。

假设找到了模板 news/year_archive.html 。下面是它大概的样子:
```html
<!--mysite/news/templates/news/year_archive.html-->
{% extends "base.html" %}

{% block title %}Articles for {{ year }}{% endblock %}

{% block content %}
<h1>Articles for {{ year }}</h1>

{% for article in article_list %}
<p>{{ article.headline }}</p>
<p>By {{ article.reporter.full_name }}</p>
<p>Published {{ article.pub_date|date:"F j, Y" }}</p>
{% endfor %}
{% endblock %}
```
变量使用双花括号包围。`{{ article.headline }}`表示 “输出 article 的 headline 属性”。而点符号不仅用于表示属性查找，还可用于字典的键值查找、索引查找和函数调用。

注意`{{ article.pub_date|date:"F j, Y" }}`使用了 Unix 风格的“管道”(“|”符号)。 模板过滤器，一种通过变量来过滤变量值的方式。本例中，日期过滤器使Python datetime 对象被过滤成指定的格式(在 PHP 的日期函数中可以见到这种变换)。

你可以无限制地串联使用多个过滤器。你可以编写[自定义的过滤器](https://docs.djangoproject.com/en/1.11/howto/custom-template-tags/#howto-writing-custom-template-filters)。你可以定制自己的[模板标记](https://docs.djangoproject.com/en/1.11/howto/custom-template-tags/)，在幕后运行自定义的 Python 代码。

最后，Django 使用了“模板继承”的概念：这就是`{% extends "base.html" %}`所做的事。它意味着 “首先载入名为 ‘base’的模板，它定义了一些用于填充的空白块，然后用接下来的内容填充这些空白块”总之，模板继承让你在模板间大大减少冗余内容：每一个模板只需要定义它独特的部分即可。

下面是使用了 静态文件 的 “base.html” 模板的大概样子:
```html
<!--mysite/templates/base.html-->
{% load static %}
<html>
<head>
<title>{% block title %}{% endblock %}</title>
</head>
<body>
<img src="{% static "images/sitelogo.png" %}" alt="Logo" />
{% block content %}{% endblock %}
</body>
</html>
```
简单地说，它定义了网站的外观（包括网站的 logo ），并留下了个“洞”让子模板来填充。这使站点的重新设计变得非常容易，只需改变一个文件 – “base.html” 模板。

它也可以让你创建一个网站的多个版本，使用不同的基础模板，而重用子模板。Django 的创建者已经利用这一技术来创造了显著不同的手机版本的网站 – 只需创建一个新的基础模板。

请注意，如果你喜欢其他模板系统，那么你可以不使用 Django 的模板系统。 虽然 Django 的模板系统特别集成了 Django 的模型层，但并没有强制你使用它。同理，你也可以不使用 Django 的数据库 API。您可以使用其他数据库抽象层，您可以读取 XML 文件，你可以从磁盘中读取文件，或任何你想要的方法去操作数据。 Django 的每个组成部分：模型、视图和模板都可以解耦，以后会谈到。

## 这仅仅是一点皮毛

这里只是简要概述了 Django 的功能。以下是一些更有用的功能：

- 一个 [缓存框架](https://docs.djangoproject.com/en/1.11/topics/cache/) 可以与 memcached 或其他后端缓存集成。
- 一个 [聚合框架](https://docs.djangoproject.com/en/1.11/ref/contrib/syndication/) 可以让创建 RSS 和 Atom 的 feeds 同写一个小小的 Python 类一样容易。
- 更性感的自动创建管理站点功能 – 本文仅仅触及了点皮毛。

显然，下一步你应该[下载Django](https://www.djangoproject.com/download/)，阅读[教程](https://docs.djangoproject.com/en/1.11/intro/tutorial01/)和加入[社区](https://www.djangoproject.com/community/)，感谢您的关注！