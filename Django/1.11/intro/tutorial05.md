# 编写你的第一个Django应用程序，第5部分

本教程从[教程4](./tutorial04.md)结束的地方开始
。我们已经构建了一个Web-poll应用程序，现在我们将为它创建一些自动测试。

## 介绍自动测试

### 什么是自动测试？

测试是检查代码运行的简单例行步骤。

测试运行在不同的层次。一些测试可能适用于一个细微的细节（_特定的模型方法是否按期望返回值？_）而另外一些检查软件的整体操作（_站点上的一系列用户输入是否产生所需的结果？_）。这与以前在[教程2](./tutorial02.md)中所做的那种测试没有什么不同，使用[`shell`](https://docs.djangoproject.com/en/1.11/ref
/django-admin/#django-admin-shell)来检查方法的行为，或者运行应用程序并输入数据来检查它的行为。

_自动化_测试有什么不同之处在于测试工作是由系统完成的。创建一组测试一次，然后在对应用进行更改时，您可以检查代码是否仍然按照原始的方式工作，而无需执行耗时的手动测试。

### 为什么你需要创建测试

那么为什么要创建测试，为什么现在呢？

你可能会觉得只是学习Python/Django已经足够了，还有另外一件事要学习和尝试可能看起来会使你不堪重负，而且也许是不必要的。毕竟，我们的投票应用程序现在工作得很顺利，经历创建自动化测试的麻烦过程并不会使其工作更好。如果创建投票应用程序是你学习Django编程的最后一步（只是浅尝辄止），那么你不需要知道如何创建自动化测试。但是，如果不是这样，现在是学习它的好时机。

#### 测试会节省你的时间

直到某一点，“检查它好像在工作工作”将是一个令人满意的测试。在更复杂的应用程序中，组件之间可能会有数十种复杂的交互。

任何这些组件的更改可能会对应用程序的行为产生意想不到的后果。仍然只是检查它“似乎工作”可能意味着使用二十个不同的变化的测试数据运行您的代码的功能，只是为了确保没有坏掉的部分- 差不多在浪费你的时间。

当自动化测试可以在几秒钟内为您做到这一点时尤其如此。如果出现问题，测试也将有助于识别导致意外行为的代码。

有时让自己脱离生产性的创意编程工作，面对乏味的编写测试的工作看起来是个苦差事，特别是当您知道代码正常工作时。

然而，编写测试的任务比花费几个小时小时手动测试应用程序要更加充分，或者在试图找出新产生的问题的原因的方面。

#### 测试不仅仅是识别问题，而且能阻止他们

将测试仅仅视为开发工作的消极方面是错误的。

没有测试，应用程序的目的或预期行为可能是相当不透明的。即使这是你自己的代码，你也会发现自己正在试图找出它在做什么。

测试改变了这种情况，它们从内部显示明白你的代码，当出现问题时，他们将重点放在已经出错的部分 - _即使你甚至没有意识到错误_ 。

#### 测试使您的代码更有吸引力

您可能已经创建了一个非常棒的软件，但您会发现，许多其他开发人员将拒绝注意它，因为它缺乏测试;没有测试，他们不会相信。Django的原始开发人员之一Jacob Kaplan-Moss说：“没有测试的代码被设计破坏了”。

其他开发人员想要在认真对待之前从您的软件中看到测试，这是您开始编写测试的另一个原因。

#### 测试帮助团队合作
以前的观点是从维护应用程序的单个开发者的角度撰写的。复杂的应用程序将由团队维护。测试保证同事不会无意中破坏您的代码（你也不会在不知情的情况下破坏他们的）。如果你想作为Django程序员谋生，你必须善于编写测试！

## 基本测试策略

编写测试有很多方法。

一些程序员遵循一个名为“ [测试驱动开发](https://en.wikipedia.org/wiki/Test-driven_development)”的[原则](https://en.wikipedia.org/wiki/Test-driven_development);他们实际上在编写代码之前就编写测试。这可能看起来是反直觉的，但实际上它类似于大多数人经常会做的：他们描述一个问题，然后创建一些代码来解决它。测试驱动开发简单地在Python测试用例中形成问题。

更多的时候，一个测试新手会创建一些代码，然后决定它应该有一些测试。也许早些时候写一些测试会更好一些，但是从来没有太迟。

有时很难弄清楚在哪里开始编写测试。如果你已经写了几千行Python代码，选择要测试的东西可能并不容易。在这种情况下，无论是添加新功能还是修复错误，在下次进行更改时编写第一个测试都是非常有益的。

所以我们这样做吧。

## 编写我们的第一个测试

### 我们找到一个bug

幸运的是，poll应用程序中现在就有一个可以让我们来修正的小bug：当Question在上一天（正确的）之前发布和Question的pub_date是未来的时间（当然是错误的）时，`Question.was_published_recently()`方法返回
`True`。

要检查该bug是否真的存在，使用Admin创建一个日期在将来的问题，并使用[`shell`](https://docs.djangoproject.com/en/1.11/ref/django-admin/#django-admin-shell)检查该方法：
```python
>>> import datetime
>>> from django.utils import timezone
>>> from polls.models import Question
>>> # create a Question instance with pub_date 30 days in the future
>>> future_question = Question(pub_date=timezone.now() + datetime.timedelta(days=30))
>>> # was it published recently?
>>> future_question.was_published_recently()
True
```

由于未来的事情不是“最近”，这显然是错误的。

### 创建一个测试来暴露错误

我们刚刚在[`shell`](https://docs.djangoproject.com/en/1.11/ref/django-admin/#django-admin-shell)做的测试问题正是我们可以在自动化测试中做的，所以让我们把它变成一个自动测试。

应用程序测试的常规位置在应用程序的`tests.py`文件中;测试系统将自动在任何名称以`test`开始的文件中寻找`test` 。

将以下内容放在`polls`程序的`tests.py`文件中：
```python
# polls/tests.py


import datetime

from django.utils import timezone
from django.test import TestCase

from .models import Question


class QuestionMethodTests(TestCase):

    def test_was_published_recently_with_future_question(self):
    """
    was_published_recently() should return False for questions whose
    pub_date is in the future.
    """
    time = timezone.now() + datetime.timedelta(days=30)
    future_question = Question(pub_date=time)
    self.assertIs(future_question.was_published_recently(), False)
```

我们在这里做的是创建一个[`django.test.TestCase`](https://docs.djangoproject.com/en/1.11/topics/testing/tools/#django.test.TestCase"django.test.TestCase")的子类，该子类将在之后创建一个带有一个`pub_date`属性的`Question`实例。然后我们检查`was_published_recently()`的输出，它_应该_是False。

### 运行测试

在终端，我们可以运行我们的测试：
```shell
$ python manage.py test polls
```

你会看到下面这些：
```shell
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
F
======================================================================
FAIL: test_was_published_recently_with_future_question (polls.tests.QuestionMethodTests)
----------------------------------------------------------------------
Traceback (most recent call last):
File "/path/to/mysite/polls/tests.py", line 16, in test_was_published_recently_with_future_question
self.assertIs(future_question.was_published_recently(), False)
AssertionError: True is not False

----------------------------------------------------------------------
Ran 1 test in 0.001s

FAILED (failures=1)
Destroying test database for alias 'default'...
```

发生了以下几件事情：
-  `python manage.py test polls`在`polls`应用程序中查找测试；
-  它发现了一个[`django.test.TestCase`](https://docs.djangoproject.com/en/1.11/topics/testing/tools/#django.test.TestCase "django.test.TestCase" )类的子类；
-  它为了测试而创建了一个特殊的数据库；
-  它查找测试方法 - 名称以`test`开始的；
-  在`test_was_published_recently_with_future_question`方法中它创建了一个`Question`实例，其`pub_date`字段是未来30天；
-  使用`assertIs()`方法，它发现它的`was_published_recently()`返回`True` ，然而我们希望它返回`False`；

测试通知我们哪个测试失败，甚至报告发生故障的行。

### 修正错误

我们已经知道问题是什么：如果它的`pub_date`在将来， `Question.was_published_recently()`将返回`False`。修改`models.py`中的方法，以便它将只在日期保证在过去的情况下返回`True` ：
```python
# polls/models.py

def was_published_recently(self):
    now = timezone.now()
    return now - datetime.timedelta(days=1) <= self.pub_date <= now
```

并再次运行测试：
```shell

Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.
----------------------------------------------------------------------
Ran 1 test in 0.001s

OK
Destroying test database for alias 'default'...
```

在识别bug后，我们写了一个测试使它暴露出来，并更正了代码中的错误，以便我们的测试通过。

许多其他事情在将来也可能会出现错误，但我们可以肯定，我们不会无意中重新引入这个错误，因为只需运行测试就会立即警告我们。我们可以考虑把这个应用程序的这一小部分在一个安全的地方永远固定。

### 更全面的测试

在这里，我们可以进一步把`was_published_recently()`方法固定下来；事实上，在修复一个bug时引入另一个的bug的话会是非常尴尬的。

向同一个类添加两个测试方法，以更全面地测试该方法的行为：
```python
# polls/tests.py

    def test_was_published_recently_with_old_question(self):
    """
    was_published_recently() should return False for questions whose
    pub_date is older than 1 day.
    """
    time = timezone.now() - datetime.timedelta(days=30)
    old_question = Question(pub_date=time)
    self.assertIs(old_question.was_published_recently(), False)

def test_was_published_recently_with_recent_question(self):
    """
    was_published_recently() should return True for questions whose
    pub_date is within the last day.
    """
    time = timezone.now() - datetime.timedelta(hours=1)
    recent_question = Question(pub_date=time)
    self.assertIs(recent_question.was_published_recently(), True)
```

现在我们有三个测试来证实`Question.was_published_recently()`为过去，最近和将来的问题返回合理的值。

再说一遍，`polls`是一个简单的应用程序，但无论如何，它将在未来增长复杂，并且与其进行交互的其他代码一样，我们现在有一些保证，我们为其编写测试的方法将以预期的方式运行。

##测试视图

poll应用程序是相当不会分辨的：它会发布任何问题，包括`pub_date`字段是未来时间的问题。我们应该改善这个问题。今后设置一个`pub_date`应该意味着问题在某个时刻发布，但直到当时（看到的时候）才被看到。

### 视图的测试

当我们修复上面的错误时，我们首先编写测试，然后修改代码来修复它。实际上这是测试驱动开发的一个简单例子，但是我们做的这个工作的确切顺序并不重要。

在我们的第一个测试中，我们密切关注代码的内部行为。对于这个测试，我们想检查一下用户通过Web浏览器所体验的行为。

在我们尝试修复任何东西之前，让我们来看看我们所掌握的工具。

### Django测试客户端

Django提供一个测试[`客户端`](https://docs.djangoproject.com/en/1.11/topics/testing/tools/#django.test.Clien)来模拟一个用户在视图级与代码进行交互。我们可以在`tests.py`或甚至[`shell`](https://docs.djangoproject.com/en/1.11/ref/django-admin/#django-admin-shell)中使用它。

我们将再次从[`shell`](https://docs.djangoproject.com/en/1.11/ref/django-admin/#django-admin-shell)开始，做一些在`tests.py`中不必要的工作。首先是在[`shell`](https://docs.djangoproject.com/en/1.11/ref/django-admin/#django-admin-shell)中设置测试环境：
```shell
>>> from django.test.utils import setup_test_environment
>>> setup_test_environment()
```

[`setup_test_environment()`](https://docs.djangoproject.com/en/1.11/topics/testing/advanced/#django.test.utils.setup_test_environment"django.test.utils.setup_test_environment")安装一个模板渲染器，这将允许我们检查响应的其他一些属性，如`response.context`，否则将不可用。请注意，此方法_不会_设置测试数据库，因此将根据现有数据库运行以下操作，并且输出可能会略有不同，具体取决于您已创建的问题。如果`settings.py`中的`TIME_ZONE`不正确，您可能会收到意想不到的结果。如果您不记得之前设置过，请在继续之前检查这一部分。

接下来，我们需要导入测试客户端类（稍后在`tests.py`我们将使用[`django.test.TestCase`](https://docs.djangoproject.com/en/1.11/topics/testing/tools/#django.test.TestCase)类附带的客户端，因此不需要）：
```shell
>>> from django.test import Client
>>> # create an instance of the client for our use
>>> client = Client()
```

这些准备好了之后，我们可以让客户端为我们做一些工作：
```shell
>>> # get a response from '/'
>>> response = client.get('/')
Not Found: /
>>> # we should expect a 404 from that address; if you instead see an
>>> # "Invalid HTTP_HOST header" error and a 400 response, you probably
>>> # omitted the setup_test_environment() call described earlier.
>>> response.status_code
404
>>> # on the other hand we should expect to find something at '/polls/'
>>> # we'll use 'reverse()' rather than a hardcoded URL
>>> from django.urls import reverse
>>> response = client.get(reverse('polls:index'))
>>> response.status_code
200
>>> response.content
b'\n    <ul>\n    \n        <li><a href="/polls/1/">What&#39;s up?</a></li>\n    \n    </ul>\n\n'
>>> response.context['latest_question_list']
<QuerySet [<Question: What's up?>]>
```

### 改进我们的视图

投票列表显示尚未发布的投票（即`pub_date`的值为未来时间的投票）。我们来解决这个问题。

在[教程4中，](https://docs.djangoproject.com/en/1.11/intro/tutorial04/)我们介绍了基于[`ListView`](https://docs.djangoproject.com/en/1.11/ref/class-based-views/generic-display/#django.views.generic.list.ListView" )的类视图：
```python
# polls/views.py


class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
        """Return the last five published questions."""
        return Question.objects.order_by('-pub_date')[:5]
```

我们需要修改`get_queryset()`方法，使其通过与`timezone.now()`进行比较来检查日期。首先我们需要添加一个导入：
```python
# polls/views.py

from django.utils import timezone
```

然后我们必须修改`get_queryset`方法，如下所示：
```python
# polls/views.py

def get_queryset(self):
    """
    Return the last five published questions (not including those set to be
    published in the future).
    """
    return Question.objects.filter(
    pub_date__lte=timezone.now()
    ).order_by('-pub_date')[:5]
```

`Question.objects.filter(pub_date__lte=timezone.now())`返回一个包含`Question`的查询集，其`pub_date`小于或等于 - 即早于或等于- `timezone.now` 。

### 测试我们的新视图

现在，您可以通过启动运行服务器，在浏览器中加载站点，创建过去和将来的日期的`Questions`，并检查仅列出已发布的站点，来满足您的需求。你不会希望_每一次当做了一些可能会影响到这个的改变时都重复这些内容_- 所以我们还要根据上面的[`shell`](https://docs.djangoproject.com/en/1.11/ref/django-admin/#django-admin-shell)会话创建一个测试。

将以下内容添加到`polls/tests.py` ：
```python
# polls/tests.py

from django.urls import reverse
```

我们将创建一个快捷方式函数来创建问题以及一个新的测试类：
```python
# polls/tests.py


def create_question(question_text, days):
    """
    Creates a question with the given `question_text` and published the
    given number of `days` offset to now (negative for questions published
    in the past, positive for questions that have yet to be published).
    """
    time = timezone.now() + datetime.timedelta(days=days)
    return Question.objects.create(question_text=question_text, pub_date=time)


class QuestionViewTests(TestCase):
    def test_index_view_with_no_questions(self):
        """
        If no questions exist, an appropriate message should be displayed.
        """
        response = self.client.get(reverse('polls:index'))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(response.context['latest_question_list'], [])
    
    def test_index_view_with_a_past_question(self):
        """
        Questions with a pub_date in the past should be displayed on the
        index page.
        """
        create_question(question_text="Past question.", days=-30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
        response.context['latest_question_list'],
        ['<Question: Past question.>']
        )
    
    def test_index_view_with_a_future_question(self):
        """
        Questions with a pub_date in the future should not be displayed on
        the index page.
        """
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(response.context['latest_question_list'], [])
    
    def test_index_view_with_future_question_and_past_question(self):
        """
        Even if both past and future questions exist, only past questions
        should be displayed.
        """
        create_question(question_text="Past question.", days=-30)
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
        response.context['latest_question_list'],
        ['<Question: Past question.>']
        )
    
    def test_index_view_with_two_past_questions(self):
        """
        The questions index page may display multiple questions.
        """
        create_question(question_text="Past question 1.", days=-30)
        create_question(question_text="Past question 2.", days=-5)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
        response.context['latest_question_list'],
        ['<Question: Past question 2.>', '<Question: Past question 1.>']
        )
```

我们再来看一下这些。

首先是一个问题的快捷方法， `create_question` ，在创建问题的过程中帮忙做一些重复工作。

`test_index_view_with_no_questions`不会创建任何问题，但会检查该消息：“No polls are
available.”，并验证`latest_question_list`是否为空。注意到这里[`django.test.TestCase`](https://docs.djangoproject.com/en/1.11/topics/testing/tools/#django.test.TestCase)类提供了一些额外的断言方法。在这些例子中，我们使用了[`assertContains()`](https://docs.djangoproject.com/en/1.11/topics/testing/tools/#django.test.SimpleTestCase.assertContains)和[`assertQuerysetEqual()`](https://docs.djangoproject.com/en/1.11/topics/testing/tools/#django.test.TransactionTestCase.assertQuerysetEqual) 。

在`test_index_view_with_a_past_question`方法中 ，我们创建一个问题并验证它是否显示在列表中。

在`test_index_view_with_a_future_question`，我们创建一个pub_date在将来的问题。数据库为每个测试方法重置，所以第一个问题不再存在，索引也不应该有任何问题。

实际上，我们正在使用测试来讲述网站上的管理员输入和用户体验的故事，并检查在每个状态和系统状态的每一个新的变化，预期的结果被公布。

### 测试`DetailView`

我们的代码工作得不错；然而，即使未来的问题没有出现在_首页中_，如果用户知道或猜测到正确的URL，仍然可以访问它们。所以我们需要添加一个类似的约束到`DetailView` ：
```python
# polls/views.py

class DetailView(generic.DetailView):
    ...
    def get_queryset(self):
        """
        Excludes any questions that aren't published yet.
        """
        return Question.objects.filter(pub_date__lte=timezone.now())
```

当然，我们会添加一些测试来检查`pub_date`在过去的`Question`是否可以被显示，而在将来有的问题则不会：
```python
# polls/tests.py


class QuestionIndexDetailTests(TestCase):
    def test_detail_view_with_a_future_question(self):
        """
        The detail view of a question with a pub_date in the future should
        return a 404 not found.
        """
        future_question = create_question(question_text='Future question.', days=5)
        url = reverse('polls:detail', args=(future_question.id,))
        response = self.client.get(url)
        self.assertEqual(response.status_code, 404)

    def test_detail_view_with_a_past_question(self):
        """
        The detail view of a question with a pub_date in the past should
        display the question's text.
        """
        past_question = create_question(question_text='Past Question.', days=-5)
        url = reverse('polls:detail', args=(past_question.id,))
        response = self.client.get(url)
        self.assertContains(response, past_question.question_text)
```

### 更多测试的想法

我们应该为`ResultsView`添加一个类似的`get_queryset`方法，并为该视图创建一个新的测试类。这与我们刚刚创造的非常相似;事实上确实会有很多重复。

我们也可以以其他方式改进我们的应用程序，一路上添加测试。例如，没有Choice的`Questions`居然可以在的网站上发布，这是很愚蠢的。所以，我们的视图可以检查，并排除这些`Questions`。我们的测试将创建一个没有`Choices`的`Question` ，然后测试它_是否已_发布，以及创建一个类似带有`Choices`的`Question` ，并测试它_是否已_发布。

也许登录的管理员用户可以看到未发布的`Questions`，但普通的访问者不能。再次：无论什么需要被添加到软件中以完成某项功能，都应该伴随着一个测试，无论你是先写测试，然后使代码通过测试，或者先编写代码中的逻辑，然后写一个测试证明给它。

在某一点上，你一定要看看你的测试，想知道你的代码是否遭受了过多的测试，将我们关注接下来的部分：

## 编写测试时，越多越好

似乎我们的测试正在失控。在这个速度下，我们的测试中将会有比在应用程序中更多的代码，并且与其他代码的优雅的简洁性相比，重复是不美观的。

但这无关紧要！就让它们增多吧。在大多数情况下，您可以编写一次测试，然后忘记它。当您继续开发程序时，它将继续执行其有用的功能。

有时测试需要更新。假设我们修改我们的视图，以便只让有`Choices`的`Questions`被发布。在这种情况下，我们许多现有的测试将会失败 -_告诉我们哪些测试需要修改以使它们更新_ ，所以在这个程度上测试可以帮助照顾自己。

最糟糕的是，随着您继续开发，您可能会发现您有一些现在已经是冗余的测试。即便这样也不是问题;在测试方面，冗余是件好事。

只要您的测试合理安排，就不会变得难以控制。良好的经验规则包括：
-  每个模型或视图有一个单独的`TestClass`
-  一个单独的测试方法，用于您要测试的每组条件
-  描述其功能的测试方法名称

## 进一步测试

本教程仅介绍一些测试的基础知识。您可以做更多的工作，还有一些非常有用的工具可用于实现一些非常聪明的事情。

例如，虽然我们在这里的测试已经涵盖了模型的一些内部逻辑以及我们的视图发布信息的方式，但您可以使用“浏览器内”框架（如[Selenium](http://seleniumhq.org/)）来测试HTML在浏览器中的实际呈现方式。这些工具不仅可以检查Django代码的行为，还可以检查您的JavaScript。看到测试启动浏览器，并开始与您的网站进行交互，就好像一个人正在开车一样！Django包含了[`LiveServerTestCase`](https://docs.djangoproject.com/en/1.11/topics/testing/tools/#django.test.LiveServerTestCase) ，以便于与Selenium等工具集成。

如果您有一个复杂的应用程序，您可能希望自动运行测试，每次提交都是为了[持续集成](https://en.wikipedia.org/wiki/Continuous_integration)的目的，因此质量控制本身至少是部分自动化的。

发现应用程序的未测试部分的一个好方法是检查代码覆盖。这也有助于识别脆弱的甚至死代码。如果您无法测试一段代码，通常意味着代码应该重构或删除。覆盖将有助于识别死代码。有关详细信息，请参阅[与coverage.py集成](https://docs.djangoproject.com/en/1.11/topics/testing/advanced/#topics-testing-code-coverage) 。

[Django中的测试](https://docs.djangoproject.com/en/1.11/topics/testing/)有关于测试的全面信息。

##下一步做什么呢？

有关测试的详细信息，请参阅[Django中的测试](https://docs.djangoproject.com/en/1.11/topics/testing/)。

当您对测试Django视图感到满意时，请阅读[本教程的第6部分](./tutorial06.md)，了解静态文件管理。
