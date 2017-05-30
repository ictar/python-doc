# 编写你的第一个Django应用程序，第4部分

本教程从[教程3](./tutorial03.md)结束的地方开始。我们正在继续网页投票应用程序，并将重点介绍简单的表单处理和减少代码量。

## 编写一个简单的表单

让我们从上一个教程中更新我们的投票详细信息模板（“`polls / detail.html`”），以使模板包含一个HTML`<form>`元素：
```html
<!--polls/templates/polls/detail.html-->

<h1>{{ question.question_text }}</h1>

{% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}

<form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
{% for choice in question.choice_set.all %}
    <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
    <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br />
{% endfor %}
<input type="submit" value="Vote" />
</form>
```
快速解释：
- 上述模板为问题的每个选择显示一个单选按钮。每个单选按钮的`value`是相关的问题选择的ID，每个单选按钮的`name`是"**choice**" 。这意味着，当有人选择其中一个单选按钮并提交表单时，它将发送POST数据`choice=#`其中＃是所选择的选择的ID。这是HTML表单的基本概念。
- 我们将表单的`action`设置为`{% url 'polls:vote' question.id %}` ，设置`method="post"` 。使用`method="post"` （而不是`method="get"` ）是非常重要的，因为提交此表单的行为会改变数据服务器端。无论何时创建一个更改数据服务器端的表单，请使用method="post" 。这个技巧不是Django的特有的;这只是很好的Web开发实践。
- `forloop.counter`指示标签通过其for循环的次数（即循环中的第几个）
- 由于我们正在创建POST表单（可能会影响修改数据），所以我们需要担心`Cross Site Request Forgeries`（跨站点请求伪造）。幸运的是，你不用担心太多，因为Django自带一个非常易于使用的系统来防范它。简而言之，针对内部网址的所有POST表单都应使用`{% csrf_token %}`模板标记。

现在，我们来创建一个处理提交的数据的Django视图，并让它来工作。请记住，在教程3中 ，我们为包含此行的投票应用程序创建了一个URLconf：
```python
# polls/urls.py

url(r'^(?P<question_id>[0-9]+)/vote/$', views.vote, name='vote'),
```
我们还创建了一个`vote()`函数的虚拟实现。现在我们来创建一个真正的版本。将以下内容添加到`polls/views.py` ：
```python
# polls/views.py

from django.shortcuts import get_object_or_404, render
from django.http import HttpResponseRedirect, HttpResponse
from django.urls import reverse

from .models import Choice, Question
# ...
def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST['choice'])
    except (KeyError, Choice.DoesNotExist):
        # Redisplay the question voting form.
        return render(request, 'polls/detail.html', {
            'question': question,
            'error_message': "You didn't select a choice.",
        })
    else:
        selected_choice.votes += 1
        selected_choice.save()
        # Always return an HttpResponseRedirect after successfully dealing
        # with POST data. This prevents data from being posted twice if a
        # user hits the Back button.
        return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
```
这段代码包含了我们在本教程中还没有介绍的几件事情：
- [request.POST](https://docs.djangoproject.com/en/1.11/ref/request-response/#django.http.HttpRequest.POST)是一个类似字典的对象，可以通过键名访问提交的数据。在这种情况下， `request.POST['choice']`以字符串格式返回所选择的选项的ID。 `request.POST`的值始终是字符串。
- 请注意，Django还提供了以相同方式访问GET数据的[request.GET](https://docs.djangoproject.com/en/1.11/ref/request-response/#django.http.HttpRequest.GET) ，但是我们在代码中明确地使用了`request.POST`，以确保数据只能通过POST调用进行更改。
- 如果POST数据中没有提供choice ，则`request.POST['choice']`将引发[KeyError](https://docs.python.org/3/library/exceptions.html#KeyError)。上述代码检查`KeyError`，如果没有提供choice则重新显示具有错误消息的问题表单。
- 选择计数递增后，代码返回[HttpResponseRedirect](https://docs.djangoproject.com/en/1.11/ref/request-response/#django.http.HttpResponseRedirect)而不是正常的[HttpResponse](https://docs.djangoproject.com/en/1.11/ref/request-response/#django.http.HttpResponse)。 `HttpResponseRedirect`接受一个参数：用户将被重定向到的URL（在这种情况下，我们如何构建URL，请参阅以下内容）。
- 正如上面的Python注释所指出的那样，在成功处理POST数据之后，应始终返回一个`HttpResponseRedirect`。这个技巧不是Django的特有的;这只是很好的Web开发实践。
- 在这个例子中，我们在`HttpResponseRedirect`构造函数中使用[reverse()](https://docs.djangoproject.com/en/1.11/ref/urlresolvers/#django.urls.reverse)函数。此功能有助于避免在视图功能中对URL进行硬编码。这个函数需要给出我们要将控件传递给的视图的名称以及指向该视图的URL模式的变量部分。在这种情况下，使用我们在教程3中设置的URLconf，这种`reverse()`调用将返回一个字符串。
  ```
  '/polls/3/results/'
  ```
  其中3是`question.id`的值。然后，这个重定向网址会调用'results'视图来显示最终的页面。

如教程3所述， `request`是一个`HttpRequest`对象。有关`HttpRequest`对象的更多信息，请参阅[请求和响应文档](https://docs.djangoproject.com/en/1.11/ref/request-response/)。

在有人给一个问题投票后， `vote()`视图重定向到问题的结果页面。我们来编写这个视图：
```python
# polls/views.py

from django.shortcuts import get_object_or_404, render


def results(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/results.html', {'question': question})
```
这与教程3中的`detail()`视图几乎完全相同。唯一的区别是模板名称。我们稍后会修复这个冗余的地方。

现在，创建一个`polls/results.html`模板：
```html
<!--polls/templates/polls/results.html-->

<h1>{{ question.question_text }}</h1>

<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
{% endfor %}
</ul>

<a href="{% url 'polls:detail' question.id %}">Vote again?</a>
```
现在，在浏览器中转到`/polls/1/`并在此问题上投票。您应该会看到一个结果页面，每次投票时都会更新。如果你提交表单时没有选择，你应该看到错误消息。

>备注
>
>我们的`vote()`视图的代码确实有一个小问题。它首先从数据库中获取`selected_choice`对象，然后计算新的`votes`数值，然后将其保存回数据库。如果网站的两位用户尝试在完全相同的时间投票，这可能会出错：将会同时在数据库中检索vote对应的值，比如说42 。然后，对于两个用户，计算并保存新值43，但是44才是预期值。
>
>这被称为竞争条件 。如果您有兴趣，可以阅读[使用F()避免竞争条件](https://docs.djangoproject.com/en/1.11/ref/models/expressions/#avoiding-race-conditions-using-f)，以了解如何解决此问题。

---
## 使用通用视图：代码越少越好

`detail()`（来自[教程3](./turotial03.md)）和`results()`视图非常简单 - 同时如上所述，冗余。显示投票列表的`index()`视图也是类似的。

这些视图代表基本Web开发的常见情况：根据URL中传递的参数从数据库中获取数据，加载模板并返回渲染的模板。因为这很常见，Django提供了一个称为“通用视图”系统的快捷方式。

通用视图将常见的模式抽象出来，甚至不需要编写Python代码来编写应用程序。

我们将我们的投票应用转换为使用通用视图系统，因此我们可以删除一堆我们自己的代码。我们只需要采取几个步骤进行转换。我们将：
- 转换URLconf。
- 删除一些旧的，不需要的视图。
- 介绍基于Django通用视图的新视图。

请继续阅读。

>为什么代码要改组？
>
>通常，在编写Django应用程序时，您将评估通用视图是否适合您的问题，您将从头开始使用它们，而不是重写代码。但是，本教程有意“用困难的方法”编写视图直到现在，来把重点放在核心概念上。
>
>在开始使用计算器之前，应该先了解基础数学。

### 修改URLconf

首先，打开`polls/urls.py`文件并修改URLconf：
```python
# polls/urls.py
from django.conf.urls import url

from . import views

app_name = 'polls'
urlpatterns = [
    url(r'^$', views.IndexView.as_view(), name='index'),
    url(r'^(?P<pk>[0-9]+)/$', views.DetailView.as_view(), name='detail'),
    url(r'^(?P<pk>[0-9]+)/results/$', views.ResultsView.as_view(), name='results'),
    url(r'^(?P<question_id>[0-9]+)/vote/$', views.vote, name='vote'),
]
```
请注意，第二和第三个模式的正则表达式中匹配模式的名称已从`<question_id>`更改为`<pk>`。

---
### 修改视图

接下来，我们将删除旧的`index`， `detail`和`results`视图，并改用Django的通用视图。要这样做，打开`polls/views.py`文件并更改它：
```python
# polls/views.py
from django.shortcuts import get_object_or_404, render
from django.http import HttpResponseRedirect
from django.urls import reverse
from django.views import generic

from .models import Choice, Question


class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
        """Return the last five published questions."""
        return Question.objects.order_by('-pub_date')[:5]


class DetailView(generic.DetailView):
    model = Question
    template_name = 'polls/detail.html'


class ResultsView(generic.DetailView):
    model = Question
    template_name = 'polls/results.html'


def vote(request, question_id):
    ... # same as above, no changes needed.
```
我们在这里使用两个通用视图： `ListView`和`DetailView`。这两个视图分别提取了“显示对象列表”和“显示特定类型对象的详细信息页面”的概念。

每个通用视图都需要知道它将用于什么样的模型。这是由model属性提供的。
`DetailView`通用视图期望从URL捕获的主键值被称为"pk" ，因此为了通用视图我们已将`question_id`更改为pk。
默认情况下， `DetailView`通用视图使用名为`<app name>/<model name>_detail.html`。在我们的例子中，它将使用模板"polls/question_detail.html" 。`template_name`属性用于告诉Django使用特定的模板名称，而不是自动生成的默认模板名称。我们还为results列表视图指定了`template_name` - 这样可以确保结果视图和细节视图在呈现时具有不同的外观，即使它们幕后都是`DetailView` 。

类似地， `ListView`通用视图使用名为`<app name>/<model name>_list.html`的默认模板;我们使用`template_name`来告诉`ListView`使用我们现有的"polls/index.html"模板。

在本教程的前面部分，模板已经提供了一个包含question和latest_question_list上下文变量的上下文。对于DetailView ， question变量是自动提供的 - 由于我们使用Django模型（ Question ），Django能够为上下文变量确定适当的名称。然而，对于ListView，自动生成的上下文变量是question_list 。要重写这个，我们提供context_object_name属性，指定我们要使用latest_question_list 。作为一种替代方法，您可以更改模板以匹配新的默认上下文变量 - 但是让Django使用所需的变量要容易得多。

运行服务器，使用基于通用视图的新的投票应用程序。

有关通用视图的完整详细信息，请参阅[通用视图文档](https://docs.djangoproject.com/en/1.11/topics/class-based-views/)。

当您对表单和通用视图感到满意时，请阅读本教程的[第5部分](./tutorial05.md)，了解如何测试我们的投票应用程序。
