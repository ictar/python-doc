原文：[5 reasons you need to learn to write Python decorators](https://www.oreilly.com/ideas/5-reasons-you-need-to-learn-to-write-python-decorators)

---

Decorators can massively magnify the positive impact of the code you write.

By [Aaron
Maxwell](https://www.oreilly.com/people/27097813-9a87-49f9-9fe3-84144509ace3)

May 5, 2016

![Dragons](https://d3tdunqjn7n0wj.cloudfront.net/360x240/dragons-
1124668_1400-d7649773673ddae0700617e719275b81.jpg) Dragons  (source:
[Pixabay](https://pixabay.com/en/dragons-china-thailand-ornament-1124668/)).

If you're interested in learning how to use Python decorators and want to
write more robust, reliable, and maintainable Python code, you'll want to
check out Aaron Maxwell's online course, [Python–Beyond the
Basics](https://www.oreilly.com/online-courses/python-beyond-
basics.html?intcmp=il-prog-olreg-article-
oltrain_5_reasons_you_need_to_learn_to_write_python_decorators_inline).

Python decorators are so easy to use. Anyone who knows how to write a Python
function can learn to use a decorator:

[code]

    @somedecorator
    def some_function():
        print("Check it out, I'm using decorators!")
    
[/code]

But _writing_ decorators is a whole different skill set. And it’s not trivial;
you have to understand:

  * closures
  * how to work with functions as first-class arguments
  * variable arguments
  * argument unpacking, even
  * some details of how Python loads its source code.

This all takes significant time to understand and master. And you already have
a backlog of things to learn. Is this worth your time?

For me, the answer has been "a thousand times, YES!" And odds are it will be
for you, too. What are the key benefits of writing decorators...what they let
you do easily and powerfully, in your day-to-day development?

## Analytics, logging, and instrumentation

Especially with large applications, we often need to specifically measure
what’s going on, and record metrics that quantify different activities. By
encapsulating such noteworthy events in their own function or method, a
decorator can handle this requirement very readably and easily.

[code]

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
    
[/code]

The same approach can be used to record counts or other metrics.

## Validation and runtime checks

Python’s type system is strongly typed, but very dynamic. For all its
benefits, this means some bugs can try to creep in, which more statically
typed languages (like Java) would catch at compile time. Looking beyond even
that, you may want to enforce more sophisticated, custom checks on data going
in or out. Decorators can let you easily handle all of this, and apply it to
many functions at once.

Imagine this: you have a set of functions, each returning a dictionary, which
(among other fields) includes a field called "**summary**." The value of this
summary must not be more than 80 characters long; if violated, that’s an
error. Here is a decorator that raises a **ValueError** if that happens:

[code]

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
    
[/code]

## Creating frameworks

Once you master writing decorators, you'll be able to benefit from the simple
syntax of using them, which lets you add semantics to the language that are
easy to use. It's the next best thing to being able to extend the syntax of
Python itself.

In fact, many popular open source frameworks use this. The webapp framework
Flask uses it to route URLs to functions that handle the HTTP request:

[code]

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
    
[/code]

Here you have a global object called **app**, with a method called **route**,
taking certain arguments. That **route** method returns a decorator that is
applied to the handler function. What’s going on beneath the hood is pretty
intricate and complicated, but from the perspective of the person using Flask,
all that complexity is completely hidden.

Using decorators in this way also shows up in stock Python. For example, fully
using the object system relies on the **classmethod** and **property**
decorators:

[code]

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
    
[/code]

This class has three different def statements. But their semantics are all
different:

  * the constructor is a normal method
  * for_winter is a classmethod providing a kind of factory, and
  * progress is read-only, dynamic attribute

The simplicity of the **@classmethod** and **@property** decorators makes it
easy to extend Python’s object semantics in everyday use.

## Reusing impossible-to-reuse code

Python gives you some very powerful tools for encapsulating code into an
easily reusable form, with an expressive function syntax, functional
programming support, and a full-featured object system. However, there are
some patterns of code reuse which can’t be captured by these alone.

Consider working with a flakey API. You make requests to something that speaks
JSON over HTTP, and it works correctly 99.9% of the time. But… a small
fraction of all requests will cause the server to return an internal error,
and you need to retry the request. In that case, you’d implement some retry
logic, like so:

[code]

    resp = None
    while True:
        resp = make_api_call()
        if resp.status_code == 500 and tries < MAX_TRIES:
            tries += 1
            continue
        break
    process_response(resp)
    
[/code]

Now imagine you have dozens of functions like **make_api_call()**, and they
are called all over the codebase. Are you going to implement that while loop
everywhere? Are you going to do it again every time you add a new API-calling
function? This kind of pattern makes it hard to not have boilerplate code.
Unless you use decorators. Then it’s quite simple:

[code]

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
    
    This gives you an easy-to-use @retry decorator:
    
    @retry
    def make_api_call():
        # ....
    
[/code]

## Boosting your career

Writing decorators isn’t easy at first. It’s not rocket science, but takes
enough effort to learn, and to grok the nuances involved, that many developers
will never go to the trouble to master it. And that works to your advantage.
When you become the person on your team who learns to write decorators well,
and write decorators that solve real problems, other developers will use them.
Because once the hard work of writing them is done, decorators are so easy to
use. This can **massively** magnify the positive impact of the code you write.
And it just might make you a hero, too.

As I’ve traveled far and wide, training hundreds of working software engineers
to use Python more effectively, teams have consistently reported writing
decorators to be one of the most valuable and important tools they’ve learned
in my advanced Python programming workshops. And that’s why it’s a key part of
the upcoming [Python: Beyond the Basics](http://www.oreilly.com/online-courses
/python-beyond-basics.html?intcmp=il-prog-olreg-article-
oltrain_5_reasons_you_need_to_learn_to_write_python_decorators_inline) online
course on May 25th and 26th, 2016.

No matter how you learn to write decorators, you can be excited about what
you’ll be able to do with them, and how it will—no joke—change the way you
write Python code forever!

Article image: Dragons  (source: [Pixabay](https://pixabay.com/en/dragons-
china-thailand-ornament-1124668/)).

[![Aaron Maxwell](https://d3tdunqjn7n0wj.cloudfront.net/360x360/aaron-maxwell-
400x400-ac6d3f8dbbe679f53678b05d8ba70368.jpg)](https://www.oreilly.com/people/
27097813-9a87-49f9-9fe3-84144509ace3)

##  [Aaron
Maxwell](https://www.oreilly.com/people/27097813-9a87-49f9-9fe3-84144509ace3)

Aaron Maxwell is the author of Advanced Python: A Not-For-Beginners Guide, and
editor of the Advanced Python Newsletter. After a decade building the
infrastructure and code for different Silicon Valley startups using a variety
of languages—but mainly Python—he now travels widely to bring the most
powerful secrets and best practices to Python developers around the world. He
has started offering select online trainings, enabling students to benefit and
learn at a fraction of the normal cost.

* * *

Video play

[![Ada Lovelace Day cake](https://d3tdunqjn7n0wj.cloudfront.net/360x240/ada-
lovelace-cake-crop-
e327b863e8879e00e3622f98706f2da3.jpg)](https://www.oreilly.com/ideas/looking-
back-looking-ahead-ada-lovelace-day-founder-suw-charman-anderson)

[Software Engineering](https://www.oreilly.com/topics/software-engineering)

##  [Looking back and looking ahead with Ada Lovelace Day's
founder](https://www.oreilly.com/ideas/looking-back-looking-ahead-ada-
lovelace-day-founder-suw-charman-anderson)

By [Mac Slocum](https://www.oreilly.com/people/0d2c1-mac-slocum)

Suw Charman-Anderson, founder of Ada Lovelace Day, explains why she started
the day and why it's caught on.

[![Ada Lovelace portrait](https://d3tdunqjn7n0wj.cloudfront.net/360x240
/ada_lovelace_portrait-crop-
bbebd19ecc2a9d13093af94d1337ea90.jpg)](https://www.oreilly.com/ideas/ada-
lovelace-an-indirect-and-reciprocal-influence)

[Software Engineering](https://www.oreilly.com/topics/software-engineering)

##  [Ada Lovelace, an indirect and reciprocal
influence](https://www.oreilly.com/ideas/ada-lovelace-an-indirect-and-
reciprocal-influence)

By [Amy Jollymore](https://www.oreilly.com/people/amy-jollymore)

Celebrating women in technology and the curious mind of Ada Lovelace

[![Diagram for the computation of Bernoulli
numbers](https://d3tdunqjn7n0wj.cloudfront.net/360x240
/diagram_for_the_computation_of_bernoulli_numbers-crop-
c5bd32cf467cdc1ad1f6985ef050f1f7.jpg)](https://www.oreilly.com/ideas
/celebrating-ada-lovelace-day)

[Software Engineering](https://www.oreilly.com/topics/software-engineering)

##  [Celebrating Ada Lovelace Day](https://www.oreilly.com/ideas/celebrating-
ada-lovelace-day)

By [Suzanne Axtell](https://www.oreilly.com/people/6e5fe-suzanne-axtell)

The O'Reilly community shares stories of inspiring women in tech. Who inspired
you?

[![Chains and gears](https://d3tdunqjn7n0wj.cloudfront.net/360x240
/five_shouts-
45a016ae78432673ef9980ed931d408f.jpg)](https://www.oreilly.com/ideas/the-five-
shouts-of-good-programmers)

[Software Engineering](https://www.oreilly.com/topics/software-engineering)

##  [The five shouts of good programmers](https://www.oreilly.com/ideas/the-
five-shouts-of-good-programmers)

By [Abraham Marin-Perez](https://www.oreilly.com/people/99e91f20-69c9-4c42
-bacf-198ffd26915c)

A set of reactions to the most common programming scenarios that tend to turn
software projects sour.

### About Us

  * [Our Company](http://oreilly.com/about/)
  * [Work with Us](http://oreilly.com/work-with-us.html)
  * [Customer Service](http://shop.oreilly.com/category/customer-service.do)
  * [Contact Us](http://shop.oreilly.com/category/customer-service.do)

### Site Map

  * [Ideas](https://www.oreilly.com/ideas)
  * [Learning](https://www.oreilly.com/learning)
  * [Topics](https://www.oreilly.com/topics)
  * [All](https://www.oreilly.com/all)

  * [ facebook ](http://fb.co/OReilly)
  * [ twitter ](http://twitter.com/oreillymedia)
  * [ youtube-large ](https://www.youtube.com/user/OreillyMedia)
  * [ google ](https://plus.google.com/+oreillymedia)
  * [ linkedin ](https://www.linkedin.com/company/o%27reilly-media)

[ ](https://www.oreilly.com/)

(C) 2016 O'Reilly Media, Inc. All trademarks and registered trademarks
appearing on oreilly.com are the property of their respective owners.

  
[Terms of Service](http://oreilly.com/terms/) • [Privacy
Policy](http://oreilly.com/privacy.html) • [Editorial
Independence](http://www.oreilly.com/about/editorial_independence.html)

![Animal](https://d3ebicv0uqgr7t.cloudfront.net/images/tarsier.png)

