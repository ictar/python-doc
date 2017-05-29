# 编写你的第一个Django应用程序，第3部分

本教程从[教程2](./tutorial02.md)结束的地方开始。我们将继续Web-poll应用程序，并将重点创建公共站点的界面 - “视图”。

## 概述

视图是Django应用程序中的“一种”网页，通常用于特定功能并具有特定的模板。例如，在一个博客应用程序中，您可能有以下视图：
- 博客首页 - 显示最新的几个条目。
- 单个条目的“详细”页面 - 单个条目的固定链接页面。
- 基于年份的存档页面 - 显示给定年份的所有月份的条目。
- 基于月份的存档页面 - 显示给定月份的所有日期的条目。
- 基于日的存档页面 - 显示给定日期中的所有条目。
- 评论 - 处理向给定条目发布评论的行为。

在我们的投票应用中，我们将有以下四个视图：
- Question“首页”页面 - 显示最新的几个问题。
- Question“详细”页面 - 显示一个问题的文本，以及一个投票表单而不是结果。
- Question“结果”页面 - 显示特定问题的结果。
- 投票 - 处理特定问题中特定选择的投票行为。

在Django中，网页和其他内容由视图提供。每个视图都由一个简单的Python函数（或方法，在者基于类的视图的情况下）来表示。Django将通过检查所请求的URL（确切地说，域名后的URL部分）来选择一个视图。

现在你在网络上的时候，可能会遇到像`ME2/Sites/dirmod.asp？sid=&type=gen&mod=Core+Pages&gid=A6CD4967199A42D9B65B1B`这样的链接，你会很乐意知道，Django允许我们提供比这更优雅的网址格式 。

URL格式只是URL的一般形式，例如： `/newsarchive/<year>/<month>/` 。

要从URL到视图，Django使用所谓的“URLconfs”。URLconf将URL模式（用正则表达式描述）映射到视图。

本教程提供了使用URLconfs的基本指导，你可以参考[django.urls](https://docs.djangoproject.com/en/1.10/ref/urlresolvers/#module-django.urls)了解更多信息。

## 编写更多视图

现在我们再添加一些视图到`polls/views.py`文件 。这些视图略有不同，因为它们需要一个参数：
```python
# polls/views.py

def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)

def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```
通过添加以下`url()`调用，将这些新视图连接到`polls.urls`模块中：
```python
# polls/urls.py

from django.conf.urls import url

from . import views

urlpatterns = [
    # ex: /polls/
    url(r'^$', views.index, name='index'),
    # ex: /polls/5/
    url(r'^(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
    # ex: /polls/5/results/
    url(r'^(?P<question_id>[0-9]+)/results/$', views.results, name='results'),
    # ex: /polls/5/vote/
    url(r'^(?P<question_id>[0-9]+)/vote/$', views.vote, name='vote'),
]
```
在浏览器中访问“/polls/34/”。它将运行`detail()`方法并显示您在URL中提供的任何ID。尝试“/polls/34/results/”和“/polls/34/vote/”，将显示结果和投票页面并显示占位符数字。

当有人从您的网站请求一个页面 - 例如“/poll/34/”时，Django将加载`mysite.urls`Python模块，因为它是由[ROOT_URLCONF](https://docs.djangoproject.com/en/1.10/ref/settings/#std:setting-ROOT_URLCONF)设置指向的。它找到名为`urlpatterns`的变量，并按顺序遍历正则表达式。在`'^polls/'`找到匹配项后，它将剥离匹配的文本（ **"polls/"** ），并将剩余的文本**"34/"**发送到“`polls.urls`”的URL配置进行进一步处理。在这里它匹配到`r'^(?P<question_id>[0-9]+)/$'` ，调用`detail()`视图，如下所示：
```python
detail(request=<HttpRequest object>, question_id='34')
```
`question_id='34'`部分来自`(?P<question_id>[0-9]+)`. 使用模式周围的括号“捕获”该模式匹配的文本，并将其作为参数发送给视图函数;`?P<question_id>`定义将用于识别匹配模式的名称；`[0-9]+`则是一个用来匹配一个数字序列（即一个数字）的正则表达式。

由于网址格式是正则表达式，因此你可以对它们做任何操作。而且没有必要添加如`.html`的繁琐的部分——除非你想要这样做，在这种情况下你可以这样做：
```python
url(r'^polls/latest\.html$', views.index),
```
但是，不要这样做。太傻了。

## 编写真的做些什么的视图

每个视图至少做两件事情：返回包含页面请求内容的[HttpResponse](https://docs.djangoproject.com/en/1.10/ref/request-response/#django.http.HttpResponse)对象或抛出异常如[Http404](https://docs.djangoproject.com/en/1.10/topics/http/views/#django.http.Http404)异常等。其间的逻辑可由你根据需求自己决定。

你的视图可以从数据库读取记录，或者不这么做它可以使用诸如Django的模板系统，也可以使用第三方Python模板系统，或者不使用。它可以生成PDF文件，输出XML，立即创建一个ZIP文件，或任何你想要的，使用任何你想要使用的Python库。

所有Django想要的是`HttpResponse`。或者一个异常。

我们使用在[教程2](tutorial02.md)中介绍过的Django自己的数据库API，因为它很方便。这是一个新的`index()`视图，它显示系统中最新的5个投票问题，用逗号分隔，根据发布日期排序：
```python
# polls/views.py
from django.http import HttpResponse

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ', '.join([q.question_text for q in latest_question_list])
    return HttpResponse(output)

# Leave the rest of the views (detail, results, vote) unchanged
```
这里有一个问题：页面的设计在视图中是硬编码的。如果要更改页面的样式，就必须编辑这段Python代码。所以让我们使用Django的模板系统，通过创建视图可以使用的模板来将视图设计与Python分开。

首先，在你的`polls`目录中创建一个名为`templates`的目录。Django会在那里寻找模板。

你的项目的[TEMPLATES](https://docs.djangoproject.com/en/1.10/ref/settings/#std:setting-TEMPLATES)设置描述了Django如何加载和渲染模板。默认设置文件配置其`DjangoTemplates`后端的[APP_DIRS](https://docs.djangoproject.com/en/1.10/ref/settings/#std:setting-TEMPLATES-APP_DIRS)选项设置为`True`。按照惯例， `DjangoTemplates`在每个[INSTALLED_APPS](https://docs.djangoproject.com/en/1.10/ref/settings/#std:setting-INSTALLED_APPS)中查找“templates” 子目录。

在刚刚创建的`templates`目录中，创建另一个名为`polls`的目录，并在其中创建一个名为`index.html`的文件。换句话说，你的模板应该在`polls/templates/polls/index.html`这个路径。由于`app_directories`模板加载器的工作原理如上所述，您可以将Django中的此模板简单地称为`polls/index.html`。

>**模板命名空间**
>
>现在我们*可以*将我们的模板直接放在`polls/templates`（而不是创建另一个polls子目录），但实际上是一个坏主意。Django将选择找到的第一个名称与之匹配的模板，如果在不同的应用程序中具有相同名称的模板，Django将无法区分它们。我们需要能够将Django指向正确模板的方法，最简单的方法是通过*命名空间*来确定这一点。也就是说，将这些模板放在为应用程序本身命名的另一个目录中。

将以下代码放在该模板中：
```html
<!--polls/templates/polls/index.html-->
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```
现在让我们更新我们在`polls/views.py`中的`index`视图来使用模板：
```python
# polls/views.py
from django.http import HttpResponse
from django.template import loader

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    template = loader.get_template('polls/index.html')
    context = {
        'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))
```
该代码加载名为`polls/index.html`的模板，并传递一个上下文。上下文是将模板变量名称映射到Python对象的字典。

通过将浏览器指向“/polls/”来加载页面，您应该看到包含[教程2](toturial02.md)中 “What's up”问题的符号列表。每一项的链接指向问题的详细页面。

### 一个快捷方式： render()

加载模板是一个非常常见的惯用语法，填充上下文并返回带有渲染结果模板的`HttpResponse`对象。Django提供了一个快捷方式。这是完整的重写过的`index()`视图：
```python
polls/views.py
from django.shortcuts import render

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```
请注意，一旦我们在所有这些视图中完成了这些操作，我们就不再需要导入`loader`和`HttpResponse`（如果你在detail ， results和vote中仍然使用原来的方法 ，则需要保留`HttpResponse`）。

`render()`函数将request对象作为其第一个参数，模板名称作为其第二个参数，并将一个字典作为其可选的第三个参数。它返回使用给定上下文呈现的给定模板的`HttpResponse`对象。

## 引发404错误

现在，我们来解决问题详细视图 - 显示给定投票的问题文本的页面。以下是视图代码：
```python
# polls/views.py
from django.http import Http404
from django.shortcuts import render

from .models import Question
# ...
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})
```
这里有一个新概念：如果请求的ID对应的问题不存在，则该视图引发`Http404`异常。

稍后我们将讨论你可以在`polls/detail.html`模板中添加什么，但是如果你想马上使上面的例子工作，只需要创建以下文件和内容：
```html
<!--polls/templates/polls/detail.html-->

{{ question }}
```
现在你就能开始运行它。

### 一个快捷方式： get_object_or_404()

如果对象不存在，那么使用`get()`之后引发`Http404`是一个很常见的惯用语法。Django提供了一个快捷方式。以下是重写的`detail()`视图：
```python
# polls/views.py

from django.shortcuts import get_object_or_404, render

from .models import Question
# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```
`get_object_or_404()`函数将Django模型作为其第一个参数，并接受任意数量的关键字参数，传递给模型管理器的`get()`函数。如果对象不存在，它会引发`Http404`。

>**哲学**
>
>为什么我们使用一个帮助函数`get_object_or_404()`而不是在更高级别自动捕获`ObjectDoesNotExist`异常，模型API引发`Http40`而不是`ObjectDoesNotExist`？
>
因为这会将模型层耦合到视图层。Django最重要的设计目标之一是保持松耦合。一些受控耦合在`django.shortcuts`模块中采用。

还有一个`get_list_or_404()`函数，它可以像`get_object_or_404()`一样工作 ，除了使用`filter()`而不是`get()`方法。如果列表为空，它会引发`Http404`。

## 使用模板系统

返回到我们的poll应用程序的`detail()`视图。给定上下文变量`question`，以下是`polls/detail.html`模板可能的样子：
```html
<!--polls/templates/polls/detail.html-->

<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
{% endfor %}
</ul>
```
模板系统使用点查找语法访问变量属性。在`{{ question.question_text }}`的例子中，Django首先对对象`question`进行字典查找。如果没有找到，它会尝试一个属性查找 - 在当前情况下使用的。如果属性查找失败，它将尝试列表索引查找。

方法调用发生在`{% for %}`循环中： `question.choice_set.all`会被解释为Python代码`question.choice_set.all()` ，它返回一个可迭代的`Choice`对象，适用于`{% for %}`标签。

有关模板的更多信息，请参阅[模板指南](https://docs.djangoproject.com/en/1.10/topics/templates/)。

## 在模板中删除硬编码的URL

当我们在`polls/index.html`模板中添加一个问题的链接时，链接部分硬编码如下：
```html
<li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
```
这种硬编码，紧密耦合的方法的问题是，在具有大量模板的项目上更改URL变得具有挑战性。但是，由于您在`polls.urls`模块中的`url()`函数中定义了`name`参数，因此可以使用`{% url %}`模板标记来删除对url配置中定义的特定URL路径的依赖：
```html
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```
这样做的方式是通过寻找`polls.urls`模块中指定的URL定义来工作的。你可以看到下面的代码定义了“detail”的URL名称：
```python
...
# the 'name' value as called by the {% url %} template tag
url(r'^(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
...
```
如果你想将poll的详细信息视图的链接更改为其他内容，或许像polls/specifics/12/这样，你可以在polls/urls.py中更改而不是在模板（或模板）中：
```python
...
# added the word 'specifics'
url(r'^specifics/(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
...
```

## 命名空间URL名称
教程项目只有一个应用程序， polls 。在真正的Django项目中，可能有五，十，二十个应用程序或更多。Django如何区分它们之间的URL名称？例如， polls应用程序具有detail视图，一个类似博客的应用程序也可能有。如何使Django知道使用`{% url %}`模板标签时为url创建的应用视图时哪一个？

答案是为您的URLconf添加命名空间。在`polls/urls.py`文件中，继续添加一个`app_name`来设置应用程序命名空间：
```python
# polls/urls.py

from django.conf.urls import url

from . import views

app_name = 'polls'
urlpatterns = [
    url(r'^$', views.index, name='index'),
    url(r'^(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
    url(r'^(?P<question_id>[0-9]+)/results/$', views.results, name='results'),
    url(r'^(?P<question_id>[0-9]+)/vote/$', views.vote, name='vote'),
]
```
现在修改你的`polls/index.html`模板：
```html
<!--polls/templates/polls/index.html-->

<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```
来指向命名空间对应的细节视图：
```html
<!--polls/templates/polls/index.html-->
<li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
```
当你对编写视图感到满意时，阅读本教程的[第4部分](tutorial04.md)，了解简单的表单处理和通用视图。
