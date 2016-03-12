原文：[How to pitch Python 3 to management](http://www.snarky.ca/how-to-pitch-python-3-to-management)

---

[This blog post has been sitting as a draft for months, and I'm finally finishing while at home sick; sorry if that makes it a little less coherent compared to my other posts]

Over on our [Python team at Microsoft blog](https://blogs.msdn.microsoft.com/pythonengineering/), one of my teammates wrote a [blog post](https://blogs.msdn.microsoft.com/pythonengineering/2016/03/08/python-3-is-winning/) showing that project releases on PyPI within a single month will begin to support Python 3 more than Python 2 starting in May of this year. Up to this point the usual complaint I have heard about moving to Python 3 was lack of support, but with the trend very quickly approaching the point where Python 2 is the less supported version than Python 3 (at least for new releases), it means that argument is going to become less and less important.

And if you read the comments related to that blog post in various places you will discover that in fact people are not using missing dependency support as a reason not to switch to Python 3. Instead the reason has shifted to Python 2 being "good enough". These people typically don't argue against the fact that Python 3 is better, just that it isn't enough of an improvement over Python 2 to make the switch. And if developers are using this reasoning, that means people who do want to switch might be running up against this with their manager(s).

To help you make a case that Python 3 is in fact worth moving towards, I have decided to write this blog post as a laundry list of points you can take to your manager(s) to try and convince them to let you upgrade your code at work to Python 3. While not all of these arguments will work for all managers, my hope is that there will at least be a couple that you can use. Please realize, though, that while my list below is a bit long, it is not exhaustive. To see **everything** that has changed in Python since Python 2.7 (because you [stopped using Python 2.6](http://www.snarky.ca/stop-using-python-2-6), right?), please look at the ["What's New" documents](https://docs.python.org/3/whatsnew/index.html) for Python 3.0 - 3.5 which might contain something that is important to you that I simply overlooked (e.g., I don't cover `nonlocal` since it's nice, but not something I see a manager going "wow!" over). Also realize that focusing on a single feature may not be enough and instead you need to point to the whole package of Python 3 as a benefit.

I should mention that I am leaving out anything that you can already do in Python 2, i.e. any modules in the standard library that you can easily grab off of [PyPI](https://pypi.python.org). Anything I mention in this post should be either exclusive to Python 3 entirely or at least would require you to re-compile Python 2 -- and hence lose any QA guarantees from the Python development team -- to gain the benefit.

I'm also not orienting this post towards new projects. In that instance you should just be using Python 3, period. When [over 87% of the top 360 projects on PyPI by download count](http://py3readiness.org/) support Python 3 and the trend in Python 3's favour you should not have to justify why you want to go with the latest and greatest version of Python.

Finally, I'm using Python 3.5 as my basis of argument. If you have not made the switch yet then you might as well switch directly to the latest stable release and gain all the benefits you can from the switch. There is no benefit for you switching to, e.g. Python 3.3 which is widely considered the first version of Python 3 that was usable.

# Language

## Improvements

To start, the Python 3 is just a nicer language than Python 2. Once you have been coding in Python 3 for a while, Python 2 feels somewhat rough. Unfortunately fluffy wording like "generally better" doesn't usually cut it with management (if it did then people would have moved to Python 3 years ago).

### No more `int`/`long` dichotomy

While [Python 3.0 improved integers overall](https://docs.python.org/3/whatsnew/3.0.html#integers), probably the biggest change was the merging of the `int` and `long` type. If you don't typically have to deal with arbitrary integer math then this change probably doesn't matter to you. But if you have dealt with the quirky edge cases involved with having `int` and `long` being separate types in Python 2.7 then you know how this merger is rather beneficial for your sanity.

### Non-ASCII identifiers

If you happen to work somewhere that speaks a language that does not use ASCII natively, then Python 2 could be annoying by forcing you to either write in a contorted version of your native language that used characters of your alphabet that happened to overlap with ASCII or simply program in a non-native language for you. Having [PEP 3131](https://www.python.org/dev/peps/pep-3131/) for Python3 allows for non-ASCII identifiers to be used in Python 3. This goes beyond simply making UTF-8 the default source encoding to Python and allows you to use Unicode for, e.g. your variable names:
```py
# This example shows both how the default encoding for source files is UTF-8
# and that non-ASCII identifiers are allowed in Python 3.
üñîçóÐè = 'åèíôü'
```

Not every Unicode identifier is allowed, hence the term "non-ASCII" being used. There is actually a Unicode standard about what characters are allowed in identifiers that Python follows to prevent situations where you might not be able to visually tell the difference between two characters.

### No-argument `super()`

When a crowd first learns that Python 3 has a version of the `super()` built-in that takes no arguments, there is usually applause. In Python 2.7, if you wanted to call `super()` then you needed to list out the base class you were calling from within, e.g., `super(Foo, self).__init__()`. What [PEP 3135](https://www.python.org/dev/peps/pep-3135/) gave Python 3 really makes people happy because it did away with the required arguments to Python 3: `super().__init__()`. And since it makes people so happy I figured it deserved a quick mention and acts as an example of some of the simple tweaks made to Python 3 that make it nicer than Python 2.
```py
class Foo:

    def __init__(self):
        attr1 = None


class Bar(Foo):

    def __init__(self):
        # In Python 2, this would be `super(Bar, self).__init__()`.
        super().__init__()
        attr2 = self.attr1
```

### The `@` operator

If you have to work with matrix multiplication, [Python 3.5's addition of the `@` operator](https://docs.python.org/3/whatsnew/3.5.html#whatsnew-pep-465) should make you happy. In Python 2.7 the concept of matrix multiplication needed to be represented using functions or methods: `(H.dot(beta) - r).T.dot(inv(H.dot(V).dot(H.T))).dot(H.dot(beta) - r)`. But in Python 3, you get an operator for matrix multiplication: `(H @ beta - r).T @ inv(H @ V @ H.T) @ (H @ beta - r)`. While not used by any built-in types or anything in the standard library, the Python scientific community is [using it in libraries like NumPy](http://docs.scipy.org/doc/numpy-1.10.1/reference/generated/numpy.matmul.html#numpy.matmul) already.

### `async`/`await`

In Python 2.7, the two most common ways to write asynchronous programming is [Twisted](https://pypi.python.org/pypi/Twisted) or [gevent](https://pypi.python.org/pypi/gevent). While both projects have served the Python community well, working with them has not always been as smooth as it could be due to the lack of syntactic support from Python to make asynchronous programming easier. But thanks to [PEP 492](https://www.python.org/dev/peps/pep-0492/), that changed in Python 3.5 with the addition of `async` functions and awaitable objects.

I won't go into much detail here on the topic of `async`/`await` because I wrote an extensive [post on the topic](http://www.snarky.ca/how-the-heck-does-async-await-work-in-python-3-5) previously. What I will mention, though, is that David Beazley created a concurrency library called [curio](https://github.com/dabeaz/curio) which he benchmarked as being 30-40% faster than Twisted and 10-15% slower than gevent on a simple echo server example, all while using nicer syntax (and gevent is now supports Python 3 while some parts of Twisted do as well).

### Keyword-only arguments

Let's say you have a function that takes a single argument:
```py
def spam(X): ...
```

Some time in the future, that function has to gain a new argument, let's say a flag of some sort:
```py
def spam(X, flag=True): ...
```

Now that's fine, but what is stopping someone from incorrectly calling spam with two arguments instead of one, e.g., meaning to do `spam(0)` but accidentally doing `spam(0, 1)` because they mis-remembered the arguments to `spam()`? In Python 2.7, there's nothing really you can do easily without resorting to `*args`/`**kwargs` and managing parameter checking yourself, but that ruins the code as documentation since you can't tell what the function does (not) accept based on that function signature.

But in Python 3 thanks to [PEP 3102](https://www.python.org/dev/peps/pep-3102), you can now have keyword-only arguments which makes expanding APIs so much nicer:
```py
def spam(X, *, flag=1): ...
```
This makes `spam(0, 1)` an error, so accidental use of a new argument to a function is really hard. Instead, if the use of the new argument was intentional, the call would need to be `spam(0, flag=1)`. Keyword-only arguments are also helpful in forcing people to label arguments that are typically just integer or boolean constants. Looking at the `spam(0, 1)` example again, it's non-obvious what those constant values represent, while `spam(0, flag=1)` is a bit more obvious.

## Debugging

Beyond changes to the language to make it more pleasant to work with, various things were added to make debugging Python 3 easier than Python 2.

### Chained exceptions

Consider the following code:
```py
try:
    raise ValueError
except ValueError as exc:
    raise TypeError
```

In Python 2, you get the following traceback:
```py
Traceback (most recent call last):
  File "<stdin>", line 4, in <module>
TypeError
```

That's fine, but what if that `ValueError` had important information? The person who wrote the code may have thought the `ValueError` wasn't important, but maybe you could really use the information that the exception captures. This is why [PEP 3134](https://www.python.org/dev/peps/pep-3134/) introduced chained exceptions. This lets the code above to lead to a traceback of the following in Python 3:
```py
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
ValueError
```
During handling of the above exception, another exception occurred:
```py
Traceback (most recent call last):
  File "<stdin>", line 4, in <module>
TypeError
```

Notice how there is no lost traceback information? The code could have used `raise ValueError from exc` to make an explicit chained exception instead of an implicit one. And in both instances the chained exception is accessible on the raised exception (as is the traceback itself).

### Simplified ordering comparisons

Have you ever been bitten by the fact that all types can be compared to each other to come up with some ordering? For instance, has something like `(4,) &gt; 4` actually working in Python 2.7 caused you pain? If so then [Python 3's simplified ordering comparisons](https://docs.python.org/3/whatsnew/3.0.html#ordering-comparisons) would have helped in that situation. The benefit of this change can be especially beneficial when  you have code like this:
```py
x = [(4,), 4]
# ... stuff ...
x.sort()
```

What should the result of that `list.sort()` call be? In Python 2 the answer is `[4, (4,)]`, if that even makes sense (should a tuple of `4` be greater than `4` itself?). But in Python 3 it raises a `TypeError` about how `int` and `tuple` are unorderable between each other. This means that if you accidentally slip in something into a list and try to sort it that it will complain loudly that it can't lead to a sane answer.

### Unicode is less error-prone

In Python 2.7 have you ever gotten a `UnicodeError` and wondered what the heck happened? If you have, you definitely aren't the only one. Clearly separating textual and binary data to prevent errors when they intermingle accidentally was a key motivating factor in breaking backwards-compatibility in Python 3. I have consistently heard from people who have to do work that involves Unicode and/or networking that have discovered latent bugs in their code thanks to their Python 3 migration. By being so explicit, Python 3 forces you to deal with potential problems instead of hiding from them.

### `tracemalloc`

If you have ever written a C extension module, chances are you have accidentally introduced a memory leak. Or how about a pure Python app that just seemed to consume way more memory than necessary? Tracking down those kinds of bugs typically are really difficult, especially if you don't know what kind of object is consuming all of that memory. But in Python 3.4 a new [module called `tracemalloc`](https://docs.python.org/3/library/tracemalloc.html) was introduced which keeps statistics on memory allocated by Python. This allows you to more easily track down where all your memory is going and who is allocating it.

## Security

When you use Python 2.7 for system scripting, you have to worry about someone mucking with the environment running the script. If you're not careful, someone could be malicious and do things like tweak environment variables to make sure some evil code gets executed with elevated permissions. That's why in Python 3.4, [isolated mode](https://docs.python.org/3/whatsnew/3.4.html#whatsnew-isolated-mode) was introduced. The `-I` turns off various things like environment variables and such to prevent you from accidentally giving people the chance to influence the execution environment to get you to execute their code for them.

# Performance

## Unicode

Thanks to everything being Unicode for textual data in Python 3, a lot of work has gone into improving the performance of Unicode strings. Beyond the fact that the common encoders and decoders have all been sped up, there are two key tweaks relating to strings that deserve being called out.

One is key-sharing in dictionaries thanks to [PEP 412](https://www.python.org/dev/peps/pep-0412/). What this means is that if you construct a ton of objects all with the same attributes, those objects' `__dict__` objects will be able to share the strings representing the attributes, saving you memory.

The other performance improvement is flexible string representations thanks to [PEP 393](https://www.python.org/dev/peps/pep-0393/). What this improvement does is allow strings to be stored in their smallest constant-sized encodings possible (in this case, Latin-1, UTF-16, or UTF-32). What this allows for is string operations to continue to be constant (e.g., indexing), while using as little space as necessary (e.g., if your text can be represented by Latin-1, then it will only take 1 byte per character). This also doesn't place any artificial limit on what  Unicode characters you can use (e.g., you aren't limited to Latin-1). But the best side-effect of all is there is no concept of narrow and wide builds of Python anymore like in Python 2.7. That means you don't have to compile your extensions twice to support the two possible build types of Python.

## `memoryview`

[PEP 3118](https://docs.python.org/3/whatsnew/3.3.html#pep-3118-new-memoryview-implementation-and-buffer-protocol-documentation) introduced the concept of `memoryview` (which was significantly improved upon in Python 3.3). Now you might be wondering what `memoryview` offers over buffers  from Python 2.7, and the [answer is "speed"](https://twitter.com/jnuneziglesias/status/684361106869993473).

# Cost analysis

Another way to pitch to management why you should be supported in porting to Python 3 is cost. Let's say your company chooses not to switch to Python 3 and just rides Python 2 out. Well, come 2020 the Python development team is going to stop supporting Python 2.7. That means that if you want maintenance to allow for support for newer compilers, bugfixes, support for OS changes, etc., you're probably going to need to pay someone like Red Hat or Continuum Analytics for a support contract. Not only that, but you will have to pay that support contract for as long as you use Python.

Now compare that to the one-time cost of upgrading your code base to Python 3. That's a one-time cost as you will get to then continue to use the version of Python supported by the Python development team. You will also get to improve your code base as part of the transition. And updating to Python 3 is still going to be a cheaper one-time cost than rewriting your entire application in a new language like Go where you have to learn a new language, a new tooling ecosystem, etc.

# How to upgrade subversively/slowly

If all of this fails to convince management to let you actively port to Python 3, then you will simply need to fall back on the old Python tradition of sneaking it into your organization. Start by introducing new tools that help check for Python 3 compatibility like pylint under the guise of checking for general code  errors. Start running Python 2.7 with the `-3` flag to also look for potential errors. Start using `__future__` statements. Make it a personal policy to write all new code to be compatible with Python 2 &amp; 3. If you follow the tips in the [official porting HOWTO](https://docs.python.org/3/howto/pyporting.html) then you can port your code file by file and simply do it slowly so that the problem at least stops growing for you.

