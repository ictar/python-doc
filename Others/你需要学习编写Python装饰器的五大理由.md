原文：[5 reasons you need to learn to write Python decorators](https://www.oreilly.com/ideas/5-reasons-you-need-to-learn-to-write-python-decorators)

---

装饰器可以大大放大你所写的代码的积极影响。

如果你对学习如何编写Python装饰器感兴趣，并希望写出更健壮，更可靠，更可维护的Python代码，那么你会想要看看Aaron Maxwell的在线课程，[Python–Beyond the Basics](https://www.oreilly.com/online-courses/python-beyond-basics.html?intcmp=il-prog-olreg-article-oltrain_5_reasons_you_need_to_learn_to_write_python_decorators_inline)。

Python装饰器是很容易使用的。任何知道如何编写Python函数的人都能学习使用装饰器：

```python

    @somedecorator
    def some_function():
        print("Check it out, I'm using decorators!")
    
```

但是，_编写_装饰器则是一个完全不同的技能。并且它并不简单；你必须明白：

  * 闭包
  * 如果将函数作为第一类参数进行工作
  * 可变参数
  * 甚至是，参数解包
  * Python如何加载其源代码的一些细节。

这都要花费大量的时间去理解和掌握。而你已经积压了大量的东西要学习。这值得吗？

对我而言，答案都是“一千次，YES！”。而且可以保证的是，对你来说，也会是这样的。编写装饰器的主要好处是什么…… 在你日常的开发中，它们怎样让你轻松有力呢？

## 分析，记录和工具

尤其是对大型应用，我们往往需要专门衡量发生了什么事，记录量化不同活动的指标。通过在它们自己的函数或方法中封装这些值得关注的事件，一个装饰器能够非常可读并且轻松地处理这种需求。

```python

    from myapp.log import logger
    
    def log_order_event(func):
        def wrapper(*args, **kwargs):
            logger.info("Ordering: %s", func.__name__)
            order = func(*args, **kwargs)
            logger.debug("Order result: %s", order.result)
            return order
        return wrapper
    
    @log_order_event
    def order_pizza(*toppings):
        # let's get some pizza!
    
```

相同的方法可以用于记录个数或其他指标。

## 验证和运行时检查

Python的类型系统是强类型，但极具动态。由于这种好处，也意味着一些bug会试图悄悄混进来，而那些更静态类型的语言（例如JAVA）会在编译时捕获这种bug。除此之外，你可能想要在数据的出口和入口执行更加复杂的自定义检测。装饰器可以让你轻松地处理这一切，并且可以立即将其应用到许多函数。

想象一下：你有一组函数，每个都返回一个字典，其（在其他字段）包含了一个名为“**summary**”的字段。该字段的值不能超过80个字符；如果违反了，则属于错误。下面是一个装饰器，它在该错误发生时抛出一个**ValueError**：

```python

    def validate_summary(func):
        def wrapper(*args, **kwargs):
            data = func(*args, **kwargs)
            if len(data["summary"]) > 80:
                raise ValueError("Summary too long")
            return data
        return wrapper
    
    @validate_summary
    def fetch_customer_data():
        # ...
    
    @validate_summary
    def query_orders(criteria):
        # ...
    
    @validate_summary
    def create_invoice(params):
        # ...
    
```

## 创建框架

一旦你掌握了如何编写装饰器，那么你将能够从使用它们的简单语法中获益，也就是说，这让你添加语义到这个易于使用的语言中。能够扩展Python语法本身，就是另一个最好的事情。

事实上，许多流行的开源框架都使用它。webapp框架Flask用它来路由URL到处理该HTTP请求的函数上：

```python

    # For a RESTful todo-list API.
    @app.route("/tasks/", methods=["GET"])
    def get_all_tasks():
        tasks = app.store.get_all_tasks()
        return make_response(json.dumps(tasks), 200)
    
    @app.route("/tasks/", methods=["POST"])
    def create_task():
        payload = request.get_json(force=True)
        task_id = app.store.create_task(
            summary = payload["summary"],
            description = payload["description"],
        )
        task_info = {"id": task_id}
        return make_response(json.dumps(task_info), 201)
    
    @app.route("/tasks/<int:task_id>/")
    def task_details(task_id):
        task_info = app.store.task_details(task_id)
        if task_info is None:
            return make_response("", 404)
        return json.dumps(task_info)
    
```

这里，你有一个全局对象，名为**app**，以及一个名为**route**的方法，该方法接收某些参数。**route**方法返回一个装饰器，用于处理器函数。引擎之下发生的事情将非常错综复杂，但是从使用Flask的人的角度来说，所有这些复杂性都被隐藏了。
 
以这种方式使用装饰器也出现在stock Python中。例如，完全使用对象系统依赖于**classmethod**和**property**装饰器：

```python

    class WeatherSimulation:
        def __init__(self, **params):
             self.params = params
    
        @classmethod
        def for_winter(cls, **other_params):
            params = {'month': 'Jan', 'temp': '0'}
            params.update(other_params)
            return cls(**params)
    
        @property
        def progress(self):
            return self.completed_iterations() / self.total_iterations()
    
```

这个类有三个不同的def声明。但是它们的语义完全不同：

  * 构造函数是一个普通的方法
  * for_winter是一个类方法（classmethod），提供一种工厂，而
  * progress是一个只读的动态属性

**@classmethod**和**@property**装饰器的简单性，使得其易于在日常使用中扩展Python对象语义。

## 重用不可能重用的代码

Python为你提供了一些非常强大的工具，使用表达的函数语法，函数式编程支持，以及一个全功能的对象系统，封装代码到易于重用的形式。然而，有一些代码复用模式不能被这些单独封装。

想想使用一个古怪的API。你通过HTTP，使用JSON向对端发起请求，在99.9%的情况下它正常工作。但是……这些请求的一些部分会使得服务器返回一个内部错误。在这种情况下，你会实现一些重试逻辑，像这样：

```python

    resp = None
    while True:
        resp = make_api_call()
        if resp.status_code == 500 and tries < MAX_TRIES:
            tries += 1
            continue
        break
    process_response(resp)
    
```

现在，想象一下，你有几十个类似于**make_api_call()**的函数，它们被整个代码库调用。你要到处实现那个while循环吗？你要在每次添加一个新的API调用函数时都来一遍？这种模式使得它难以拥有样板代码。除非你使用装饰器。然后，它很简单：

```python

    # The decorated function returns a Response object,
    # which has a status_code attribute. 200 means
    # success; 500 indicates a server-side error.
    
    def retry(func):
        def retried_func(*args, **kwargs):
            MAX_TRIES = 3
            tries = 0
            while True:
                resp = func(*args, **kwargs)
                if resp.status_code == 500 and tries < MAX_TRIES:
                    tries += 1
                    continue
                break
            return resp
        return retried_func
    
    # This gives you an easy-to-use @retry decorator:
    
    @retry
    def make_api_call():
        # ....
    
```

## 发展你的事业

编写装饰器在开始并不容易。它不是一飞冲天，但需要足够的努力来学习，以及充分了解细微差别，因此，许多开发者永远都不会费心思去掌握它。这就使你加分不少。当你成为了你的团队中那个写得一手好装饰器的人，并且编写了可以解决实际问题的装饰器，那么其他开发者将会使用它们。因为一旦编写它们这一项工作完成了，那么装饰器是很容易使用的。这可以**大规模**放大你所写的代码的积极影响。并且，它还有可能让你成为一个英雄哦。

随着我远行，培训了数百名正在工作的软件工程师更有效地使用Python，团队不断地在反馈，编写装饰器是他们在我的高级Python编程工场中学习到的最有价值以及最重要的工具。这就是为什么它是即将到来的2016年5月25日和26日的[Python: Beyond the Basics](http://www.oreilly.com/online-courses/python-beyond-basics.html?intcmp=il-prog-olreg-article-oltrain_5_reasons_you_need_to_learn_to_write_python_decorators_inline)在线课程的关键部分。

无论你如何学会写装饰器，你都可以兴奋于能够用它们所做到的事，以及它将如何，不是开玩笑的，永远改变你编写Python代码的方式！
