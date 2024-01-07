原文：[The Fanout Pattern Explained](https://www.better-simple.com/django/2023/12/06/fanout-pattern-explained/)

---

# 解释扇出模式（Fanout Pattern）

_2023 年 12 月 06 日_

在我在 [AspirEDU](https://www.aspiredu.com) 的工作中，我们每天都使用 Celery 来捕获教育信息。我们已经采用了几种不同的策略来设计 Celery 的任务签名[1](#fn:1)。其中最强大的策略是**扇出模式（Fanout Pattern）**。


### 扇出模式指的是用可变数量的其他任务替换自身的单个任务。

来一起看看一些代码。如果我们有如下的任务签名：


```py
task1.si() | task2.si() | task3.si()
```

每项任务必须在下一项任务开始之前完成。但是，假设在 `task2` 中，我们正在获取一些集合，并且需要为集合中的每个项目运行另一个任务。扇出模式将替换 `task2`为 `item1, item2, ..., itemN`，其中 N 是集合中的项目数。

下面解释了同个意思，但是用代码的方式进行了解释：

```py
# We schedule this:
(
    task1.si()
    | task2.si()
    | task3.si()
).apply_async()
# But this is what runs:
(
    task1.si()
    | task2.si()
    | group([item1.si(), item2.si(), itemN.si()])
    | task3.si()
).apply_async()
```

记住，您不需要用一组其他任务替换这个任务。在 Celery 中，您可以将当前正在运行的任务替换为任何其他任务签名，例如单个任务或复杂的任务签名。

下面深入探讨了一个示例及这个模式，但您可能可以摆脱上面的示例并[阅读关于 `Task.replace` 的文档](https://docs.celeryq.dev/en/stable/reference/celery.app.task.html#celery.app.task.Task.replace)。


## 现实的 Taco Bell 示例问题

一个更现实的例子是，您的任务是捕获每个 Taco Bell [2](#fn:2) Franchise 的所有菜单选项，然后发送一封包含最不常列出的项目[3](#fn:3)的电子邮件。您需要使用其中的数据的 API 端点是：

* `fetch_franchises -> list[Franchise]`
* `fetch_menu(franchise_id: int) -> Menu`


假设我们创建一个 Celery 任务来负责每个 API 端点。

```py
from celery import shared_task

@shared_task
def capture_franchises():
    for franchise in fetch_franchises():
        # Do something


@shared_task
def capture_menu_for_franchise(franchise_id: int):
    for menu_item in fetch_menu(franchise_id).menu_items:
        # Do something
```

现在，我们发送报告的任务是：

```py
from celery import shared_task
from django.core.mail import send_mail
from .models import MenuItem # Our imaginary django model

@shared_task
def send_report():
    least_listed_menu_items = MenuItem.objects.least_listed().values_list('name', flat=True)
    send_mail(
        subject="Least listed items report",
        message=f"{', '.join(least_listed_menu_items)}",
        recipient_list=["menu_design@taco-bell.better-simple.com"],
        from_email="fanout_pattern@better-simple",
    )
```

挑战在于，我们如何创建一个 Celery 任务签名来运行 `capture_franchises`，然后为找到的每个 franchise 运行 `capture_menu_for_franchise`，接着在所有这些任务完成后，调用运行 `send_report` 任务。

一种解决方案就是扇出模式。

## 实践中的扇出模式

扇出模式的关键是能够[用一个任务替换另一个任务](https://docs.celeryq.dev/en/stable/reference/celery.app.task.html#celery.app.task.Task.replace)签名。

在 Celery 中，要替换任务，您必须通过 `@shared_task(bind=True)` 或 `@app.task(bind=True)`[4](#fn:4) 创建[绑定任务](https://docs.celeryq.dev/en/stable/userguide/tasks.html#bound-tasks)。这使您可以通过任务参数 `self` 访问[任务请求](https://docs.celeryq.dev/en/stable/userguide/tasks.html#task-request-info)。然后就是调用 `self` 的 [`replace`](https://docs.celeryq.dev/en/stable/reference/celery.app.task.html#celery.app.task.Task.replace) 方法了。

有很多链接和文档。所以这里的代码应该能够强调我的观点：

```py
from celery import group
from .models import Franchise # Our imaginary django model

@shared_task(bind=True)
def capture_franchises(self):
    for franchise in fetch_franchises():
        # Do something

    # We now have fetched all franchises, let's replace this
    # task with another task signature

    self.replace(
        group([
            capture_menu_for_franchise.si(franchise_id=franchise.id)
            for franchise in Franchise.objects.all()
        ])
    )
```

您可以看到，我们正在通过 `self.replace()` 这行代码，使用一组 `capture_menu_for_franchise` 任务来替换任务 `capture_franchises`。

现在我们需要将报告任务链接到上面的代码中。

```py
def run_least_listed_report():
    signature = capture_franchises.si() | send_report.si()
    signature.apply_async()
```

## 关于任务设计，再多说一句

如果您像我一样，以上内容并不适合您。这个 `signature` 定义（`capture_franchises.si() | send_report.si()`）对读者来说并无意义。franchises 被捕获了，然后报告就发出来了？如果菜单也被捕获，这是否意味着还有其他数据也在 `capture_franchises` 中被捕获了？

相反，我认为更合适的解决方案是：

```py
@shared_task
def capture_franchises():
    for franchise in fetch_franchises():
        # Do something


@shared_task(bind=True)
def capture_franchises_menu_fanout(self):
    franchise_ids = list(Franchise.objects.all().values_list("id", flat=True))
    if franchise_ids:
        # Celery doesn't handle group([]) with an empty collection well
        # so don't replace if there are no franchises stored
        self.replace(
            group([
                capture_menu_for_franchise.si(franchise_id=franchise_id)
                for franchise_id in franchise_ids
            ])
        )
```

新的签名将是：

```py
capture_franchises.si() | capture_franchises_menu_fanout.si() | send_report.si()

```

对于其他类似工作流程来说，这更具描述性并且更容易扩展或复制。

## 回顾

扇出模式是在 Celery 任务签名中实现动态工作流程的好方法。您可以使用它来减小签名的大小，并根据外部数据以编程方式选择下一个任务。

我希望您觉得这篇文章有用。如果您有疑问，请随时与我联系。您可以在 [Fediverse](https://fosstodon.org/@CodenameTim)、[Django Discord server](https://discord.gg/xcRH6mN4fa) 或通过[电子邮件](mailto:schillingt@better-simple.com)找到我。


1. 一个 Celery 任务签名意味着一个或多个链接在一起的 Celery 任务。[↩](#fnref:1)
2. 芝士戈迪塔脆饼和鸡肉玉米饼。这是您不知道的问题的答案。 [↩](#fnref:2)
3. 我们使用最不常列出的项目，因为它要求您无论如何都会搜索每个 franchise。[↩](#fnref:3)
4. 我使用 [`shared_task` 是因为它被推荐用于 Django](https://docs.celeryq.dev/en/stable/userguide/tasks.html#how-do-i-import-the-task-decorator)，但如果你喜欢，你可以使用 `app.task` 。 [↩](#fnref:4)