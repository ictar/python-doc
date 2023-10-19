原文：[Database concurrency in Django the right way](http://www.vinta.com.br/blog/2016/database-concurrency-in-django-the-right-way/)

---

当开发对于在web应用外运行异步任务有特殊需求的应用的时候，通常是采用一个任务对了，例如[Celery](http://www.celeryproject.org/)。这允许，例如，让服务器处理请求，启动一个异步任务来负责进行一些重量级处理，并在任务仍然运行的时候返回一个响应。

在这个例子的基础中，最理想的是将高时间要求的部分从视图处理流中分离开来，因此，我们在分离的任务重运行那些部分。现在，假设我们必须在view和分离的任务中进行一些数据库操作。如果不小心完成，那么那些操作会成为难以追踪的问题之源。

### ATOMIC_REQUESTS数据库配置参数

将数据库配置中的`ATOMIC_REQUESTS`参数设置为`True`是一种常见的做法。该配置允许Django view在一个事务中运行，这样，如果在处理请求过程中生成了异常，那么Django可以简单的回滚事务。这也保证了数据库永远不会处于不一致阶段，而由于事务是数据库中的一个原子操作，因此，在一个事务运行过程中，其他试图访问数据的应用不会看到来自未完成事务的不一致数据。

### 任务和Django请求处理器的数据库竞争条件

当两个或多个并发线程试图同时访问相同的内存地址（或者，在这个例子中，数据中的一些特定的数据）时，数据竞争发生了。如果，比方说，一个线程正试图读取数据，而另一个线程正试图写，或者两个线程同时在写，那么这会导致非确定结果。当然，如果两个线程只是读取数据，那么将不会发生任何问题。

现在，回到我们的问题，让我们从编写一个简单的view开始，它将把一些数据写入到我们的数据库中：


```python
from django.views.generic import View
from django.http import HttpResponse
from .models import Data


class SimpleHandler(View):

    def get(self, request, *args, **kwargs):
        myData = Data.objects.create(name='Dummy')
        return HttpResponse('pk: {pk}'.format(pk=myData.pk))
```

这里是我们的模型：

```python
from django.db import models


class Data(models.Model):
    name = models.CharField(max_length=50)
```

它塑造了一个非常简单的请求处理。如果从浏览器发起请求，那么会得到已插入数据的主键作为响应，例如`pk: 41`。现在，修改我们的get方法，来加载一个celery任务，它会从数据库中获取数据：

```python
    def get(self, request, *args, **kwargs):
        myData = Data.objects.create(name='Dummy')
        do_stuff.delay(myData.pk)
        return HttpResponse('pk: {pk}'.format(pk=myData.pk))
```

而在我们的任务文件中：

```python
from celery_test.celery import app
from .models import Data


@app.task
def do_stuff(data_pk):
    myData = Data.objects.get(pk=data_pk)
    myData.name = 'new name'
```

(不要忘了在你的view文件中导入`do_stuff`！)

现在，我们有了一个竞争条件，有可能Celery在获取数据的时候最终引发一场，例如`Task request_handler.tasks.do_stuff[2a3aecd0-0720-4360-83b5-3558ae1472f2] raised unexpected: DoesNotExist('Data matching query does not exist.',)`。也许看起来这不应该发生，因为我们正插入一行，获取它的主键，然后将它传递给该任务。因此，匹配该查询的数据应该存在。但是，正如前面所说的，如果将`ATOMIC_REQUESTS`设置为`True`，那么该view将在一个事务中运行。因此，只有当该view完成了它的执行，并提交了事务，数据才能被外部访问。

### 解决方法

对于这个问题，有多种解决方法。第一个，也是最明显的方法是设置`ATOMIC_REQUESTS`为`False`，但我们想要避免这样做，因为这将影响到我们项目中的其他view，并且正如前面所说的，在请求中使用事务是由许多优势的。另一个方法是使用[non_atomic_requests](https://docs.djangoproject.com/en/1.8/topics/db/transactions/#django.db.transaction.non_atomic_requests)装饰器，因为这只影响到一个view。不过，这可能不是你想要的，因为我们可以牺牲掉这一个view的功能。还有一些库，用于在提交当前事务的时候运行代码，例如[django_atomic_celery](https://github.com/adamchainz/django_atomic_celery)和[django-transaction-signals](https://github.com/aaugustin/django-transaction-signals)，但那些现在已经死掉了，不应该再用它们。可以在django-transaction-signals项目中读到解释。

当前最常用的方法是使用钩子。对于Django >= 1.9，你可以使用[on_commit](https://docs.djangoproject.com/en/1.10/topics/db/transactions/#django.db.transaction.on_commit)钩子，而对于Django < 1.9，使用[django-transaction-hooks](https://django-transaction-hooks.readthedocs.io/en/latest/)。这里，我们将使用`on_commit`方法，但如果你必须使用django-transaction-hooks，那么方法是非常类似的。

你所需做的是在你的view文件中，像`from django.db import transaction`这样导入`transaction`，然后传递任何你想要在提交之后执行的函数给`transaction.on_commit`。这个方法不应该有任何参数，但是我们可以将我们的任务调用封装在一个lambda中，因此，最后的view看起来像这样：

```python
class SimpleHandler(View):

    def get(self, request, *args, **kwargs):
        myData = Data.objects.create(name='Dummy')
        transaction.on_commit(lambda: do_stuff.delay(myData.pk))
        return HttpResponse('pk: {pk}'.format(pk=myData.pk))
```

这个方法够简单也适合于大多数应用。如果你需要一些更具体的，那么可以使用`non_atomic_requests`装饰器。但记住，你必须手工处理回退。
