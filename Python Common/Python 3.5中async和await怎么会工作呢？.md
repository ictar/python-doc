原文：[How the heck does async/await work in Python 3.5?
Or, generators let you do neat stuff](http://www.snarky.ca/how-the-heck-does-async-await-work-in-python-3-5)

---
作为[Python](https://www.python.org/)的一名核心开发者使得我想要了解这个语言一般是怎样工作的。我意识到，总会有一些我不知道每一个复杂细节的不起眼的角落，但是为了能够帮助解决问题以及Python的一般设计，我觉得我应该尝试了解它的核心语义，以及在引擎下，一切是怎样工作的。

但是，直到现在，我都不清楚[`async`/`await`是如何在Python 3.5工作的](https://docs.python.org/3/whatsnew/3.5.html#whatsnew-pep-492)。我知道，[Python 3.3中的`yield from`](https://docs.python.org/3/whatsnew/3.3.html#pep-380)与[Python 3.4中的`asyncio`](https://docs.python.org/3/library/asyncio.html#module-asyncio)结合起来，从而导致了这种新的语法。但是话说不是做了一堆网络的东西 - `asyncio`并不局限于，但是重点在于 -- 已经导致我并不真的十分重视`async`/`await`的一切。我的意思是，我知道：
```py
yield from iterator
```
是（基本上）等同于：
```py
for x in iterator:
    yield x
```

而且我知道，`asyncio`是一个事件循环框架，它允许异步编程，并且我知道那些词（基本上）自己意味着自己。不过话说从未深入`async`/`await`语法来了解这一切是怎么走到了一起的，我觉得我不理解Python中的异步编程，这困扰着我。所以，我决定花时间，并设法弄清楚到底这一切是如何工作的。因为我已经从不同的人听到，他们也听不懂的异步编程的这个新的世界是如何工作的，所以我决定写这篇文章（是的，这个帖子花费了这么久的时间，并且有这么长的话，以至于我的妻子已标记它为一篇文章）。

现在，因为我想正确理解这个语法是如何工作的，所以这篇文章有一些CPython是如何做事的一些低层次的技术细节。如果它比你想了解的拥有更多的细节，或者你不完全了解它，因为我并不解释为了CPython内部的每一个细节，以防止这篇文章变成了一本书（例如，如果你不知道那个代码对象有标志，那就不要管一个代码对象是什么，这没关系，你不需要关心从这篇文章获得什么东西），这完全没有关系。我试图在每节的最后提供了一个更方便的摘要，如果它们超出了你要理解的东西，你可以略过细节。

# 关于Python中协程的历史课程

根据[Wikipedia](https://www.wikipedia.org/), “[协程](https://en.wikipedia.org/wiki/Coroutine)是非抢先多任务的一般子例程，通过允许多个入口点用于在某些位置挂起和恢复执行的计算机程序组件”。这是一种相当技术的说法，“协同程序是那些你可以暂停执行的函数”。如果你对自己说，“这听起来像生成器”，你可能是正确的。

早在[Python 2.2](https://docs.python.org/3/whatsnew/2.2.html)中，生成器首先被[PEP 255](https://www.python.org/dev/peps/pep-0255/)引入（自从生成器实现了[迭代器协议](https://docs.python.org/3/library/stdtypes.html#iterator-types)后，它们也被称作生成器迭代器）。主要受[Icon编程语言](http://www.cs.arizona.edu/icon/)启发，生成器允许创建迭代器的一种方式，也就是当在迭代过程中计算下一个值时，并不会浪费内存。举例来说，如果你想创建自己的`range()`版本，你可以通过创建一个整数列表来做到这点：
```py
def eager_range(up_to):
    """Create a list of integers, from 0 to up_to, exclusive."""
    sequence = []
    index = 0
    while index < up_to:
        sequence.append(index)
        index += 1
    return sequence
```

但是，这样做的问题是，如果你想有一个大的序列，例如从0到1,000,000的整数，那么你必须创建一个足够长的列表来保存1,000,000个整数。但是，当生成器被添加到语言中时，你突然可以创建一个不需要事先创建整个序列的迭代器。相反，所有你必须​​做的事是，每次都有足够的内存来保存一个整数。
```py
def lazy_range(up_to):
    """Generator to return the sequence of integers from 0 to up_to, exclusive."""
    index = 0
    while index < up_to:
        yield index
        index += 1
```

拥有一个无论何时碰到`yield`表达式时都会暂停它所在做的事 -- 虽然它是从Python 2.5开始才有的声明 -- 然后稍后能够恢复它的函数，对于使用更少的内存，允许无限序列的想法等等，都非常有用。

但是，正如你可能已经注意到的，生成器都是关于迭代器的。现在，有一个更好的方式来创建迭代器显然是非常棒的（并且当你在一个作为生成器的对象上定义一个`__iter__()`方法，这会显示出来），但人们知道，如果我们采用了生成器的“暂停”一部分，并且增加一个“发送东西回来”的概念给它们，Python将会突然有协同程序的概念（但直到我说不然，这一切都只是在Python中的一个概念，后面将讨论Python中具体的协同程序）。感谢[PEP 342](https://www.python.org/dev/peps/pep-0342/)，将东西发送给一个已暂停的生成器这一确切功能已经被添加到[Python 2.5](https://docs.python.org/3/whatsnew/2.5.html)中。此外，PEP 342引进了生成器上的`send()`方法。这使得它不仅暂停生成器，而且发送值回生成器暂停的地方。进一步考虑我们的`range()`例子，你可以使得序列向前或向后跳过一些量：
```py
def jumping_range(up_to):
    """Generator for the sequence of integers from 0 to up_to, exclusive.

    Sending a value into the generator will shift the sequence by that amount.
    """
    index = 0
    while index < up_to:
        jump = yield index
        if jump is None:
            jump = 1
        index += jump


if __name__ == '__main__':
    iterator = jumping_range(5)
    print(next(iterator))  # 0
    print(iterator.send(2))  # 2
    print(next(iterator))  # 3
    print(iterator.send(-1))  # 2
    for x in iterator:
        print(x)  # 3, 4
```
生成器并未与之划清界限，直到当[PEP 380](https://www.python.org/dev/peps/pep-0380/)将`yield from`加入到[Python 3.3](https://docs.python.org/3/whatsnew/3.3.html)时。严格来说，这个特性使你能够通过容易的从迭代器中yield每一个值（碰巧，生成器可以方便做到），从而以一种干净的方法重构生成器。
```py
def lazy_range(up_to):
    """Generator to return the sequence of integers from 0 to up_to, exclusive."""
    index = 0
    def gratuitous_refactor():
        while index < up_to:
            yield index
            index += 1
    yield from gratuitous_refactor()
```

通过使得重构更加容易，`yield from`也允许你将生成器串联在一起，从而代码不必做任何特别的工作就可以让值沿着调用栈上下冒泡。
```py
def bottom():
    # Returning the yield lets the value that goes up the call stack to come right back
    # down.
    return (yield 42)

def middle():
    return (yield from bottom())

def top():
    return (yield from middle())

# Get the generator.
gen = top()
value = next(gen)
print(value)  # Prints '42'.
try:
    value = gen.send(value * 2)
except StopIteration as exc:
    value = exc.value
print(value)  # Prints '84'.
```

## 概要

Python 2.2中的生成器会暂停代码的执行。一旦在Python 2.5中引入将值发送回已暂停的生成器，Python中协程的概念变成可能。而在Python 3.3中额外增加的`yield from`则使得重构生成器以及将它们链在一起变得更加容易。

# 什么是事件循环？

如果你将在意`async`/`await`，那么了解一个事件循环是什么，以及它们是如何使得异步编程成为可能的至关重要。如果你之前完成了GUI编程，包括web前端工作，那么你已经使用了事件循环了。但是，由于作为一个语言结构，具有异步编程的概念在Python中是新的，所以如果你不知道事件循环是什么，那么没关系。

让我们回到维基百科，一个[事件循环](https://en.wikipedia.org/wiki/Event_loop) “是一种编程结构，它等待并调度呈现中的事件或消息”。基本上，一个事件循环让你这样进行：“当A发生的时候，做B”。也许来解释这个的最简单的例子是在每一个浏览器中的JavaScript事件循环。每当你点击某个东西（“当A发生时”），点击会被提交给JavaScript事件循环，以检查是否有任何注册的`onclick`来处理该点击（“做B”）。如果有任何注册的回调，那么会随着点击的详细信息调用回调。事件循环被认为是一个循环，因为它在不断收集事件并遍历它们从而找到如何处理该事件。

在Python的情况下，`asyncio`被添加到标准库以提供一个事件循环。有一个关注于网络的`asyncio`，它在事件循环的背景下，使“当A发生时”成为当来自于一个socket的I/O准备好读和/或写(通过[`selectors`模块](https://docs.python.org/3/library/selectors.html#module-selectors))时。除了​​GUI和I/O，事件循环也经常用于在另一个线程或子进程中执行代码，并让事件循环充当调度器（即，[协同多任务](https://en.wikipedia.org/wiki/Cooperative_multitasking)）。如果你碰巧了解Python的GIL，那么事件循环在释放GIL是可能的和有用的情况下，是非常有用的。

## 概要

事件循环提供了一个循环，使得，“当A发生时，然后做B”。基本上，一个事件循环监控某些事情何时发生，以及该事件循环关心的事情何时发生，然后它调用关心发生的事情的代码。在Python 3.4中，以`asyncio`的形式，Python在标准库中获得了一个事件循环。

# `async`和`await`如何工作

## 它在Python 3.4中的方式

在Python 3.3中发现的生成器和以`asyncio`形式出现的事件循环之间，Python 3.4足以支持[并发编程](https://en.wikipedia.org/wiki/Concurrent_computing)形式的异步编程。异步编程是基本的编程，其中执行顺序是事先不知道的（因此异步代替了同步）。并发编程是编写代码来独立执行其他部分，即使它都是在单个线程中执行（[并发并不是平行](http://blog.golang.org/concurrency-is-not-parallelism)）。例如，下面的Python 3.4代码是在两个异步，并行函数调用中按秒倒计时。
```py
import asyncio

# Borrowed from http://curio.readthedocs.org/en/latest/tutorial.html.
@asyncio.coroutine
def countdown(number, n):
    while n > 0:
        print('T-minus', n, '({})'.format(number))
        yield from asyncio.sleep(1)
        n -= 1

loop = asyncio.get_event_loop()
tasks = [
    asyncio.ensure_future(countdown("A", 2)),
    asyncio.ensure_future(countdown("B", 3))]
loop.run_until_complete(asyncio.wait(tasks))
loop.close()
```

在Python 3.4中, [`asyncio.coroutine`装饰器](https://docs.python.org/3/library/asyncio-task.html#asyncio.coroutine)被用于标记一个函数为一个[协程](https://docs.python.org/3/reference/datamodel.html?#coroutine-objects)，它意味着使用`asyncio`及其事件循环。这提供了Python协同程序的第一个具体定义：一个对象，它实现了[PEP 342](https://www.python.org/dev/peps/pep-0342/)中添加到生成器的方法，并且由[`collections.abc.Coroutine`抽象基类](https://docs.python.org/3/library/collections.abc.html#collections.abc.Coroutine)所表示。这意味着，突然之间，所有的生成器都实现了协程接口，即使它们并不是以那种方式使用。要解决这个问题，`asyncio`要求所有打算作为协程使用的生成器都必须[用`asyncio.coroutine`装饰](https://docs.python.org/3/library/asyncio-task.html#asyncio.coroutine)。

随着协程的这个具体的定义（这与生成器提供的一个API相匹配），你可以在任何[`asyncio.Future`对象](https://docs.python.org/3/library/asyncio-task.html#future)上使用`yield from`，来将它传递到事件循环中，当你等待某些事发生的时候暂停协程的执行（作为一个未来对象，它是`asyncio`的实现细节，并且它并不重要）。一旦未来对象到达事件循环，它会被监控，直到未来的对象做完了它需要做的事。一旦未来对象做完了它需要做的事，事件循环会注意到，然后暂停以等待未来的结果的协程再次启动，而其结果发回给使用`send()`方法的协程。

（unfinished）就拿上面的例子来说。事件循环开始每个的倒数（）协程的调用，执行，直到它碰到从收率和asyncio.sleep（）在其中的一个功能。返回一个asyncio.Future它被传递到事件循环，并暂停协程的执行对象。有事件循环手表将来对象，直到一秒以上（以及检查在其他的东西它看，像其他协程）。一旦一秒时，该事件循环发生暂停倒计时（）给了事件循环以后对象协程，未来对象的结果发送回给了它未来的目标放 ​​在首位的协程，而协程再次开始运行。继续这样跌下去，直到所有的倒计时（）协同程序完成后运行，事件循环无关观看。我实际上将告诉你如何准确所有这一切协同程序/事件循环东西的作品后，一个完整的例子，但首先我想解释如何异步和等待的工作。
Take our example above. The event loop starts each of the `countdown()` coroutine calls, executing until it hits `yield from` and the `asyncio.sleep()` function in one of them. That returns an `asyncio.Future` object which gets passed down to the event loop and pauses execution of the coroutine. There the event loop watches the future object until the one second is over (as well as checking on other stuff it's watching, like the other coroutine). Once the one second is up, the event loop takes the paused `countdown()` coroutine that gave the event loop the future object, sends the result of the future object back into the coroutine that gave it the future object in the first place, and the coroutine starts running again. This keeps going until all of the `countdown()` coroutines are finished running and the event loop has nothing to watch. I'll actually show you a complete example of how exactly all of this coroutine/event loop stuff works later, but first I want to explain how `async` and `await` work.

## 在Python 3.5中，从`yield from`到`await`

In Python 3.4, a function that was flagged as a coroutine for the purposes of asynchronous programming looked like:
```py
# This also works in Python 3.5.
@asyncio.coroutine
def py34_coro():
    yield from stuff()
```

In Python 3.5, the [`types.coroutine` decorator](https://docs.python.org/3/library/types.html#types.coroutine) has been added to also flag a generator as a coroutine like `asyncio.coroutine` does. You can also use `async def` to syntactically define a function as being a coroutine, although it cannot contain any form of `yield` expression; only `return` and `await` are allowed for returning a value from the coroutine.
```py
async def py35_coro():
    await stuff()
```

A key thing `async` and `types.coroutine` do, though, is tighten the definition of what a coroutine is. It takes coroutines from simply being an interface to an actual type, making the distinction between any generator and a generator that is meant to be a coroutine much more stringent (and the [`inspect.iscoroutine()` function](https://docs.python.org/3/library/inspect.html#inspect.iscoroutine) is even stricter by saying `async` has to be used).

You will also notice that beyond just `async`, the Python 3.5 example introduces `await` expressions (which are only valid within an `async def`). While `await` operates much like `yield from`, the objects that are acceptable to an `await` expression are different. Coroutines are definitely allowed in an `await` expression since the concept of coroutines are fundamental in all of this. But when you call `await` on an object , it technically needs to be an [_awaitable_ object](https://docs.python.org/3/reference/datamodel.html?#awaitable-objects): an object that defines an `__await__()` method which returns an iterator which is **not** a coroutine itself . Coroutines themselves are also considered awaitable objects (hence why `collections.abc.Coroutine` inherits from `collections.abc.Awaitable`). This definition follows a Python tradition of making most syntax constructs translate into a method call underneath the hood, much like `a + b` is `a.__add__(b)` or `b.__radd__(a)` underneath it all.

How does the difference between `yield from` and `await` play out at a low level (i.e., a generator with `types.coroutine` vs. one with `async def`)? Let's look at the bytecode of the two examples above in Python 3.5 to get at the nitty-gritty details. The bytecode for `py34_coro()` is:
```py
>>> dis.dis(py34_coro)
  2           0 LOAD_GLOBAL              0 (stuff)
              3 CALL_FUNCTION            0 (0 positional, 0 keyword pair)
              6 GET_YIELD_FROM_ITER
              7 LOAD_CONST               0 (None)
             10 YIELD_FROM
             11 POP_TOP
             12 LOAD_CONST               0 (None)
             15 RETURN_VALUE
```

The bytecode for `py35_coro()` is :
```py
>>> dis.dis(py35_coro)
  1           0 LOAD_GLOBAL              0 (stuff)
              3 CALL_FUNCTION            0 (0 positional, 0 keyword pair)
              6 GET_AWAITABLE
              7 LOAD_CONST               0 (None)
             10 YIELD_FROM
             11 POP_TOP
             12 LOAD_CONST               0 (None)
             15 RETURN_VALUE
```

Ignoring the difference in line number due to `py34_coro()` having the `asyncio.coroutine` decorator, the only visible difference between them is the [`GET_YIELD_FROM_ITER` opcode](https://docs.python.org/3/library/dis.html#opcode-GET_YIELD_FROM_ITER) versus the [`GET_AWAITABLE` opcode](https://docs.python.org/3/library/dis.html#opcode-GET_AWAITABLE). Both functions are properly flagged as being coroutines, so there's no difference there. In the case of `GET_YIELD_FROM_ITER`, it simply checks if its argument is a generator or coroutine, otherwise it calls `iter()` on its argument (the acceptance of a coroutine object by the opcode for `yield from` is only allowed when the opcode is used from within a coroutine itself, which is true in this case thanks to the `types.coroutine` decorator flagging the generator as such at the C level with the `CO_ITERABLE_COROUTINE` flag on the code object).

But `GET_AWAITABLE` does something different. While the bytecode will accept a coroutine just like `GET_YIELD_FROM_ITER`, it will **not** accept a generator if has not been flagged as a coroutine. Beyond just coroutines, though, the bytecode will accepted an _awaitable_ object as discussed earlier. This makes `yield from` expressions and `await` expressions both accept coroutines while differing on whether they accept plain generators or awaitable objects, respectively.

You may be wondering why the difference between what an `async`-based coroutine and a generator-based coroutine will accept in their respective pausing expressions? The key reason for this is to make sure you don't mess up and accidentally mix and match objects that just happen to have the same API to the best of Python's abilities. Since generators inherently implement the API for coroutines then it would be easy to accidentally use a generator when you actually expected to be using a coroutine. And since not all generators are written to be used in a coroutine-based control flow, you need to avoid accidentally using a generator incorrectly. But since Python is not statically compiled, the best the language can offer is runtime checks when using a generator-defined coroutine. This means that when `types.coroutine` is used, Python's compiler can't tell if a generator is going to be used as a coroutine or just a plain generator (remember, just because the syntax says `types.coroutine` that doesn't mean someone hasn't earlier done `types = spam` earlier), and thus different opcodes that have different restrictions are emitted by the compiler based on the knowledge it has at the time.

One very key point I want to make about the difference between a generator-based coroutine and an `async` one is that only generator-based coroutines can actually pause execution and force something to be sent down to the event loop. You typically don't see this very important detail because you usually call event loop-specific functions like the [`asyncio.sleep()` function](https://docs.python.org/3/library/asyncio-task.html#asyncio.sleep) since event loops implement their own APIs and these are the kind of functions that have to worry about this little detail. For the vast majority of us, we will work with event loops rather than be writing them and thus only be writing `async` coroutines and never need to really care about this. But if you're like me and were wondering why you couldn't write something like `asyncio.sleep()` using only `async` coroutines, this can be quite the "aha!" moment.

### 概要

Let's summarize all of this into simpler terms. Defining a method with `async def` makes it a _coroutine_. The other way to make a coroutine is to flag a generator with `types.coroutine` -- technically the flag is the `CO_ITERABLE_COROUTINE` flag on a code object -- or a subclass of `collections.abc.Coroutine`. You can only make a coroutine call chain pause with a generator-based coroutine.

An _awaitable object_ is either a coroutine or an object that defines `__await__()` -- technically `collections.abc.Awaitable` -- which returns an iterator that is not a coroutine. An `await` expression is basically `yield from` but with restrictions of only working with awaitable objects (plain generators will not work with an `await` expression). An `async` function is a coroutine that either has `return` statements -- including the implicit `return None` at the end of every function in Python -- and/or `await` expressions (`yield` expressions are not allowed). The restrictions for `async` functions is to make sure you don't accidentally mix and match generator-based coroutines with other generators since the expected use of the two types of generators are rather different.

# 对于异步编程，想想作为API的`async`/`await`

A key thing that I want to point out is actually something I didn't really think deeply about until I watched [David Beazley's Python Brasil 2015 keynote](https://www.youtube.com/watch?v=lYe8W04ERnY). In that talk, David pointed out that `async`/`await` is really an API for asynchronous programming (which [he reiterated to me on Twitter](https://twitter.com/dabeaz/status/696028946220056576)). What David means by this is that people shouldn't think that `async`/`await` as synonymous with `asyncio`, but instead think that `asyncio` is a framework that can utilize the `async`/`await` API for asynchronous programming.

David actually believes this idea of `async`/`await` being an asynchronous programming API that he has created the [`curio` project](https://pypi.python.org/pypi/curio) to implement his own event loop. This has helped make it clear to me that `async`/`await` allows Python to provide the building blocks for asynchronous programming, but without tying you to a specific event loop or other low-level details (unlike other programming languages which integrate the event loop into the language directly). This allows for projects like `curio` to not only operate differently at a lower level (e.g., `asyncio` uses future objects as the API for talking to its event loop while `curio` uses tuples), but to also have different focuses and performance characteristics (e.g., `asyncio` has an entire framework for implementing transport and protocol layers which makes it extensible while `curio` is simpler and expects the user to worry about that kind of thing but also allows it to run faster).

Based on the (short) history of asynchronous programming in Python, it's understandable that people might think that `async`/`await` == `asyncio`. I mean `asyncio` was what helped make asynchronous programming possible in Python 3.4 and was a motivating factor for adding `async`/`await` in Python 3.5. But the design of `async`/`await` is purposefully flexible enough to **not** require `asyncio` or contort any critical design decision just for that framework. In other words, `async`/`await` continues Python's tradition of designing things to be as flexible as possible while still being pragmatic to use (and implement).

# 一个例子

At this point your head might be awash with new terms and concepts, making it a little hard to fully grasp how all of this is supposed to work to provide you asynchronous programming. To help make it all much more concrete, here is a complete (if contrived) asynchronous programming example, end-to-end from event loop and associated functions to user code. The example has coroutines which represents individual rocket launch countdowns but that appear to be counting down simultaneously . This is asynchronous programming through concurrency; three separate coroutines will be running independently, and yet it will all be done in a single thread.
```py
import datetime
import heapq
import types
import time


class Task:

    """Represent how long a coroutine should wait before starting again.

    Comparison operators are implemented for use by heapq. Two-item
    tuples unfortunately don't work because when the datetime.datetime
    instances are equal, comparison falls to the coroutine and they don't
    implement comparison methods, triggering an exception.

    Think of this as being like asyncio.Task/curio.Task.
    """

    def __init__(self, wait_until, coro):
        self.coro = coro
        self.waiting_until = wait_until

    def __eq__(self, other):
        return self.waiting_until == other.waiting_until

    def __lt__(self, other):
        return self.waiting_until < other.waiting_until


class SleepingLoop:

    """An event loop focused on delaying execution of coroutines.

    Think of this as being like asyncio.BaseEventLoop/curio.Kernel.
    """

    def __init__(self, *coros):
        self._new = coros
        self._waiting = []

    def run_until_complete(self):
        # Start all the coroutines.
        for coro in self._new:
            wait_for = coro.send(None)
            heapq.heappush(self._waiting, Task(wait_for, coro))
        # Keep running until there is no more work to do.
        while self._waiting:
            now = datetime.datetime.now()
            # Get the coroutine with the soonest resumption time.
            task = heapq.heappop(self._waiting)
            if now < task.waiting_until:
                # We're ahead of schedule; wait until it's time to resume.
                delta = task.waiting_until - now
                time.sleep(delta.total_seconds())
                now = datetime.datetime.now()
            try:
                # It's time to resume the coroutine.
                wait_until = task.coro.send(now)
                heapq.heappush(self._waiting, Task(wait_until, task.coro))
            except StopIteration:
                # The coroutine is done.
                pass


@types.coroutine
def sleep(seconds):
    """Pause a coroutine for the specified number of seconds.

    Think of this as being like asyncio.sleep()/curio.sleep().
    """
    now = datetime.datetime.now()
    wait_until = now + datetime.timedelta(seconds=seconds)
    # Make all coroutines on the call stack pause; the need to use `yield`
    # necessitates this be generator-based and not an async-based coroutine.
    actual = yield wait_until
    # Resume the execution stack, sending back how long we actually waited.
    return actual - now


async def countdown(label, length, *, delay=0):
    """Countdown a launch for `length` seconds, waiting `delay` seconds.

    This is what a user would typically write.
    """
    print(label, 'waiting', delay, 'seconds before starting countdown')
    delta = await sleep(delay)
    print(label, 'starting after waiting', delta)
    while length:
        print(label, 'T-minus', length)
        waited = await sleep(1)
        length -= 1
    print(label, 'lift-off!')


def main():
    """Start the event loop, counting down 3 separate launches.

    This is what a user would typically write.
    """
    loop = SleepingLoop(countdown('A', 5), countdown('B', 3, delay=2),
                        countdown('C', 4, delay=1))
    start = datetime.datetime.now()
    loop.run_until_complete()
    print('Total elapsed time is', datetime.datetime.now() - start)


if __name__ == '__main__':
    main()
```

As I said, it's contrived, but if you run this in Python 3.5 you will notice that all three coroutines run independently in a single thread and yet the total amount of time taken to run is about 5 seconds. You can consider `Task`, `SleepingLoop`, and `sleep()` as what an event loop provider like `asyncio` and `curio` would give you. For a normal user, only the code in `countdown()` and `main()` are of importance. As you can see, there is no magic to `async`, `await`, or this whole asynchronous programming deal; it's just an API that Python provides you to help make this sort of thing easier.

# 我对未来的希望和梦想

Now that I understand how this asynchronous programming works in Python, I want to use it all the time! It's such an awesome concept that's so much better than something you would have used threads for previously. The problem is that Python 3.5 is so new that `async`/`await` is also very new. That means there are not a lot of libraries out there supporting asynchronous programming like this. For instance, to do HTTP requests you either have to construct the HTTP request yourself by hand (yuck), use a project like the [`aiohttp` framework](https://pypi.python.org/pypi/aiohttp) which adds HTTP on top of another event loop (in this case, `asyncio`), or hope more projects like the [`hyper` library](https://pypi.python.org/pypi/hyper) continue to spring up to provide an abstraction for things like HTTP which allow you to use whatever I/O library you want (although unfortunately `hyper` only supports HTTP/2 at the moment).

Personally, I hope projects like `hyper` take off so that we have a clear separation between getting binary data from I/O and how we interpret that binary data. This kind of abstraction is important because most I/O libraries in Python are rather tightly coupled to how they do I/O and how they handle data coming from I/O. This is a problem with the [`http` package in Python's standard library](https://docs.python.org/3/library/http.html#module-http) as it doesn't have an HTTP parser but a connection object which does all the I/O for you. And if you were hoping `requests` would support asynchronous programming, [your hopes have already been dashed](https://github.com/kennethreitz/requests/issues/2801) because the synchronous I/O that `requests` uses is baked into its design. This shift in ability to do asynchronous programming gives the Python community a chance to fix a problem it has with not having abstractions at the various layers of the network stack. And we have the perk of it not being hard to make asynchronous code run as if its synchronous, so tools filling the void for asynchronous programming can work in both worlds.

I also hope that Python gains some form of support in `async` coroutines for `yield`. Maybe this will require yet another keyword (maybe something like `anticipate`?), but the fact that you actually can't implement an event loop system with just `async` coroutines bothers me. Luckily, it turns out [I'm not the only one who thinks this](https://twitter.com/dabeaz/status/696014754557464576), and since the author of [PEP 492](https://www.python.org/dev/peps/pep-0492/) agrees with me, I think there's a chance of getting this quirk removed.

# 总结

Basically `async` and `await` are fancy generators that we call coroutines and there is some extra support for things called awaitable objects and turning plain generators in to coroutines. All of this comes together to support concurrency so that we have better support for asynchronous programming in Python. It's awesome and much easier to use than comparable approaches like threads -- I wrote an end-to-end example of asynchronous programming in under 100 lines of commented Python code -- while still being quite flexible and fast (the [curio FAQ](http://curio.readthedocs.org/en/latest/#questions-and-answers) says that it runs faster than `twisted` by 30-40% but slower than `gevent` by 10-15%, and all while being implemented in pure Python; remember that [Python 2 + Twisted can use less memory and is easier to debug than Go](https://news.ycombinator.com/item?id=10402307), so just imagine what you could do with this!). I'm very happy that this landed in Python 3 and I look forward to the community embracing it and helping to flesh out its support in libraries and frameworks so we can all benefit from asynchronous programming in Python.
