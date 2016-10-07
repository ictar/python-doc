原文：[Database concurrency in Django the right way](http://www.vinta.com.br/blog/2016/database-concurrency-in-django-the-right-way/)

---

When developing applications that have specific needs for running asynchronous tasks outside the web application, it is common to adopt a task queue such as [Celery](http://www.celeryproject.org/). This allows, for example, for the server to handle a request, start an asynchronous task responsible of doing some heavyweight processing, and return an answer while the task is still running. 

Building upon this example, the ideal is to separate the high time-demanding parts from the view processing flow, so we run those parts in a separate task. Now, let's suppose we have to do some database operations both in the view and in the separate task. If not done carefully, those operations can be a source for issues that can be hard to track.

### ATOMIC_REQUESTS database config parameter

It's a common practice to set the `ATOMIC_REQUESTS` parameter to `True` in the database configuration. This configuration enables for the Django views to run inside of a transaction, so that if an exception is produced during the request handling, Django can simply roll back the transaction. This also ensures the database will never be left in an inconsistent state, and since a transaction is an atomic operation in the database, no other application trying to access the database while a transaction is running will be able to see inconsistent data coming from an incomplete transaction.

### Database race condition with tasks and Django request handlers

Data races happen when two or more concurrent threads try to access the same memory address (or in this case, some specific data in a database) at the same time. This can lead to non-deterministic results if, say, one thread is trying to read data while the other is writing it, or if both threads are writing at the same time. Of course, if both threads are just reading data, no problem will occur.

Now, regarding our problem, let's start by writing a simple view, which will write some data to our database:


```python
from django.views.generic import View
from django.http import HttpResponse
from .models import Data


class SimpleHandler(View):

    def get(self, request, *args, **kwargs):
        myData = Data.objects.create(name='Dummy')
        return HttpResponse('pk: {pk}'.format(pk=myData.pk))
```

And here's our model:

```python
from django.db import models


class Data(models.Model):
    name = models.CharField(max_length=50)
```

This models a very simple request handling. If we make a request from our browser, we will get as an answer the primary key from the inserted data, such as `pk: 41`. Now, we modify our get method to launch a celery task that will fetch data from our database:

```python
    def get(self, request, *args, **kwargs):
        myData = Data.objects.create(name='Dummy')
        do_stuff.delay(myData.pk)
        return HttpResponse('pk: {pk}'.format(pk=myData.pk))
```

And in our tasks file:

```python
from celery_test.celery import app
from .models import Data


@app.task
def do_stuff(data_pk):
    myData = Data.objects.get(pk=data_pk)
    myData.name = 'new name'
```

(Don't forget to import `do_stuff` in your views file!)

Now we have a race condition and it is likely that Celery will eventually raise an exception when fetching the data, such as `Task request_handler.tasks.do_stuff[2a3aecd0-0720-4360-83b5-3558ae1472f2] raised unexpected: DoesNotExist('Data matching query does not exist.',)`. It might seem that this should not happen, since we are inserting a row, getting its primary key, and passing it for the task. So, the data matching the query should exist. But, as said earlier, if `ATOMIC_REQUESTS` is set to `True`, the view will run in a transaction. So, the data will only be externally accessible when the view finishes its execution, and the transaction is commited.

### Solution approaches

There are many solutions for this problem. The first and more obvious one is to set `ATOMIC_REQUESTS` to `False`, but we want to avoid this since this will affect every other view in our project, and using transactions in requests have many advantages as stated earlier. Another solution is to use the [non_atomic_requests](https://docs.djangoproject.com/en/1.8/topics/db/transactions/#django.db.transaction.non_atomic_requests) decorator, as this would only affect one view. Still, this can be unwanted, since we can be compromising this one view's functionality. There are also libraries that were used to run code when the current transaction is committed, such as [django_atomic_celery](https://github.com/adamchainz/django_atomic_celery) and [django-transaction-signals](https://github.com/aaugustin/django-transaction-signals), but those are now legacy and should not be used. An explanation can be read on the django-transaction-signals project.

The current and most used alternative is to use hooks. For Django &gt;= 1.9, you can use the [on_commit](https://docs.djangoproject.com/en/1.10/topics/db/transactions/#django.db.transaction.on_commit) hook, and for Django &lt; 1.9, use [django-transaction-hooks](https://django-transaction-hooks.readthedocs.io/en/latest/). We'll use the `on_commit` approach here, but if you have to use django-transaction-hooks, the solution is very similar.

All you have to do is import `transaction` as in `from django.db import transaction` on your views file, and pass any function you want to execute after the commit to `transaction.on_commit`. This function should not have any arguments, but we can wrap our task call in a lambda, so our final view looks like this:

```python
class SimpleHandler(View):

    def get(self, request, *args, **kwargs):
        myData = Data.objects.create(name='Dummy')
        transaction.on_commit(lambda: do_stuff.delay(myData.pk))
        return HttpResponse('pk: {pk}'.format(pk=myData.pk))
```

This solution is simple enough, and suitable for most applications. If you need something more specific, you can use the `non_atomic_requests` decorator. But remember that you will have to deal with roll backs manually.

Want read more about _Django_? Check out those posts from our blog:

*   [Controlling access: a Django permission apps comparison](http://www.vinta.com.br/blog/2016/controlling-access-a-django-permission-apps-comparison/)

*   [10 Django apps you're not using but should be](http://www.vinta.com.br/blog/2015/10-django-apps-youre-not-using-but-should-be/)</div>
