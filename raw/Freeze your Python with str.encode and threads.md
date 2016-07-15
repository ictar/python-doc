原文：[Freeze your Python with str.encode and threads](https://blog.sqreen.io/freeze-python-str-encode-threads/)

---

[![](https://blog.sqreen.io/wp-content/uploads/2016/07/frozen-python-2040.png)](https://blog.sqreen.io/wp-content/uploads/2016/07/frozen-python-2040.png "frozen-python-2040")
                                    
While working on the [sqreen.io Python agent](https://www.sqreen.io/?utm_medium=social&amp;utm_source=blog&amp;utm_campaign=Freeze%20your%20Python%20with%20str.encode%20and%20threads), I discovered a rather nasty, but fun to analyse bug that lead me deep into Python internals.

### The nasty bug

I was working on a piece of code that spawns a thread, retrieves some JSON, and stores it for later use. Basically, the code looked something like this:

```python
from threading import Thread, Event

class MyThread(Thread):

    def __init__(self):
        self.event = Event()

    def run(self):
        data = self.retrieve_json()
        self.data = data
        self.event.set()
        print("Data retrieval done!")
        while True:
            pass

thread = MyThread()
thread.start()
timeout = thread.event.wait(5)

if timeout is not None:
    print("JSON retrieval was too slow")
```

Everything seemed to work, and the error message displayed correctly when network latency was too high:

```python
class MyThread(Thread):

    def run(self):
        data = self.retrieve_json()
        # Convert unicode strings into latin1 strings because of WSGI
        self.data = {key.encode('latin1'): value.encode('latin1') for (key, value) in data.keys()}
        self.event.set()
        print("Data retrieval done!")
        while True:
            pass
```

I’m pretty sure you’ve all made this type of transformation once when receiving JSON, haven’t you?

After adding this code, the error message started showing up no matter what value I gave to timeout, anywhere from 5 seconds or 1 hour. What made this even weirder was that the `"Data retrieval done!"` message always printed right after the error message:

```sh
$> python2 app.py
JSON retrieval was too slow
Data retrieval done!
```

However, when I ran this in Python 3 everything worked as expected:

```sh
$> python3 app.py
Data retrieval done!
```

Something very strange was going on, and I decided to investigate.

### The isolation

I tried to pinpoint the problem and came up with this minimal reproducing script, let’s call it `"str_encode_thread.py"`:

```python
import time
import threading


def to_latin_1(event, value):
    print(time.time(), "Before encode")
    value.encode('latin-1')
    print(time.time(), "After encode")
    event.set()
    print(time.time(), "Event set")

event = threading.Event()
thread = threading.Thread(target=to_latin_1, args=(event, u'42'))
thread.start()

event.wait(10)
```

If you launch this script with Python 3, you’ll see:

```sh
$> python3 str_encode_thread.py
1467046041.643346 Before encode
1467046041.643446 After encode
1467046041.643483 Event set
```

And with Python 2, you’ll see:

```sh
$> python2 str_encode_thread.py
(1467046099.495261, 'Before encode')
(1467046099.495937, 'After encode')
(1467046099.495961, 'Event set')
```

So far, so good. The fun comes when we try to import this script. If you import the script with Python 3, everything will still be normal:

```sh
$> time python3 -c "import str_encode_thread"
1467046239.921475 Before encode
1467046239.921578 After encode
1467046239.921602 Event set
python3 -c "import str_encode_thread"  0,05s user 0,01s system 84% cpu 0,081 total
```

When imported in python 2 however our nasty friend shows up again:

```sh
$> time python2 -c "import str_encode_thread"
(1467046276.826755, 'Before encode')
(1467046286.830485, 'After encode') # <- 10 seconds (╯°□°）╯︵ ┻━┻
(1467046286.830519, 'Event set')
python2 -c "import str_encode_thread"  0,03s user 0,03s system 0% cpu 10,109 total
```

If you look closely at the `"After encode"`&nbsp;message, you can see that it’s printed 10 seconds after the `"Before encode"`&nbsp;message, indicating that the thread is somehow freezing.

### Down the rabbit hole

Why would a simple `"value.encode('latin1', 'encode')"`&nbsp;block a thread?

Fortunately, there is a [very nice package named faulthandler](https://pypi.python.org/pypi/faulthandler/2.4)&nbsp;which is very helpful in such a situation. It can display a traceback on unix signals, user signals or python faults. It’s part of the standard library since Python 3.3, and can be installed via pip in older Python versions.

In our case, we will use [dump_traceback_later](http://faulthandler.readthedocs.io/#dumping-the-tracebacks-after-a-timeout)&nbsp;to see where the tread is blocking.

The new script with faulthandler is not that different (don’t forget to `"pip install faulthandler"`):

```python
import time
import threading
import faulthandler


faulthandler.dump_traceback_later(5)


def to_latin_1(event, value):
    print(time.time(), "Before encode")
    value.encode('latin-1')
    print(time.time(), "After encode")
    event.set()
    print(time.time(), "Event set")

event = threading.Event()
thread = threading.Thread(target=to_latin_1, args=(event, u'42'))
thread.start()

event.wait(10)
```

Let’s run our new script:

```sh
time python2 -c "import str_encode_thread"
(1468083195.13011, 'Before encode')
Timeout (0:00:05)!
Thread 0x0000700000401000 (most recent call first):
  File "/usr/lib/python2.7/encodings/__init__.py", line 100 in search_function
  File "str_encode_thread.py", line 11 in to_latin_1
  File "/usr/lib/python2.7/threading.py", line 754 in run
  File "/usr/lib/python2.7/threading.py", line 801 in __bootstrap_inner
  File "/usr/lib/python2.7/threading.py", line 774 in __bootstrap

Current thread 0x00007fff71258000 (most recent call first):
  File "/usr/lib/python2.7/threading.py", line 359 in wait
  File "/usr/lib/python2.7/threading.py", line 614 in wait
  File "str_encode_thread.py", line 20 in <module>
  File "<string>", line 1 in <module>
(1468083205.133041, 'After encode')
(1468083205.133106, 'Event set')
python2 -c "import str_encode_thread"  0,03s user 0,02s system 0% cpu 10,043 total
```

What can we see with the traceback?

The main thread `"0x00007fff71258000"`, is blocked on line 20 of our module which is `"event.wait(10)"`, that’s perfectly normal.

The other thread `"0x0000700000401000"`, is running on line 11 of our module `"value.encode('latin-1')"`, and calling the function `"search_function"`&nbsp;of the builtin module `"encoding"`&nbsp;which freezes our thread. What does this function do and why is it blocking?

The [source code](https://hg.python.org/cpython/file/v2.7.12rc1/Lib/encodings/__init__.py#l99)&nbsp;tells us that it tries to import a module which begins with `"encodings."`.

```python
In [1]: import sys

In [2]: before_modules = sys.modules.keys()

In [3]: '42'.encode('latin-1')
Out[3]: '42'

In [4]: after_modules = sys.modules.keys()

In [5]: print("Imported modules", set(after_modules) - set(before_modules))
('Imported modules', set(['encodings.latin_1']))
```

Gotcha! `"str.encode(X)"`&nbsp;tries to import the module `"encodings.X"`. So the fix should be easy: import the module in the main thread before using the function, and it should work, right?

```python
import time
import threading
import encodings.latin_1


def to_latin_1(event, value):
    print(time.time(), "Before encode")
    value.encode('latin-1')
    print(time.time(), "After encode")
    event.set()
    print(time.time(), "Event set")

event = threading.Event()
thread = threading.Thread(target=to_latin_1, args=(event, u'42'))
thread.start()

event.wait(10)
```

Let’s try this again with python2:

```sh
$> time python2 -c "import str_encode_thread"
(1467049098.995541, 'Before encode')
(1467049109.000293, 'After encode')
(1467049109.000324, 'Event set')
python2 -c "import str_encode_thread"  0,03s user 0,02s system 0% cpu 10,054 total
```

We still have the same problem! What’s wrong?

### We have to go deeper

We know that `"str.encode"`&nbsp;tries to import a module which is likely freezing our thread.

At this point, I went to my favorite IRC channel (#python-fr) and asked for help, and we finally found out!

What does `"import module"`&nbsp;do? If we trust [the Python documentation](https://docs.python.org/2/library/imp.html#examples), it basically does this:

```python
import imp
import sys

def __import__(name, globals=None, locals=None, fromlist=None):
    # Fast path: see if the module has already been imported.
    try:
        return sys.modules[name]
    except KeyError:
        pass

    # If any of the following calls raises an exception,
    # there's a problem we can't handle -- let the caller handle it.

    fp, pathname, description = imp.find_module(name)

    try:
        return imp.load_module(name, fp, pathname, description)
    finally:
        # Since we may exit via an exception, close fp explicitly.
        if fp:
            fp.close()
```

Nothing very fancy there: return the module from `"sys.modules"`&nbsp;if it has already been imported or try to locate the source file for the module, load it and return it.

Nothing should block here either, but if we continue to read the `"imp"`&nbsp;module documentation, we can find a [very interesting function named `"lock_held"`](https://docs.python.org/2/library/imp.html#imp.lock_held):

> On multithreaded platforms, a thread executing an import holds an internal lock until the import is complete. This lock blocks other threads from doing an import until the original import completes, which in turn prevents other threads from seeing incomplete module objects constructed by the original thread while in the process of completing its import (and the imports, if any, triggered by that).

We are getting closer…

### Locks and threads, a love-hate relationship

When googling `"python import lock"`, we can quickly find this section about [Importing in threaded code](https://docs.python.org/2/library/threading.html#importing-in-threaded-code)&nbsp;which simply tells you that it’s a terrible idea, and is likely to create deadlocks.

Let’s sum up what we’ve found:

```python
import imp
import time
import threading


def to_latin_1(event, value):
    print(time.time(), "Before encode", threading.current_thread().name, imp.lock_held())
    value.encode('latin-1')
    print(time.time(), "After encode")
    event.set()
    print(time.time(), "Event set")

event = threading.Event()
thread = threading.Thread(target=to_latin_1, args=(event, u'42'))
thread.start()

event.wait(10)
```

```sh
$> python2 -c "import str_encode_thread"
(1467050582.565935, 'Before encode', 'Thread-1', True)
(1467050592.567373, 'After encode')
(1467050592.567418, 'Event set')
```

When we imported our module `"str_encode_thread"`, the import lock was acquired by the MainThread, so when our custom thread tried to execute the `"value.encode('latin-1')"`&nbsp;the stdlib tried to import the `"encoding.latin_1"`&nbsp;module which is blocked since the import lock is already held by the MainThread. We then block the MainThread by doing `"event.wait(10)"`&nbsp;and then both threads are blocked for 10 seconds, too bad… While the wait times out, the `"import str_encode_thread"`&nbsp;ends, releasing the import lock which wakes up our custom thread.

If we hadn’t set a maximum timeout for the event, we could have deadlocked our Python interpreter forever!

When dealing with threads, there is no magic solution. You might wonder what would have happened if you manually imported a module without using a lock, but It would only “solve” the deadlock by raising additional fun bugs.

For example, when you call `"time.strptime"`, it tries to import the `"_strptime"`&nbsp;module in a non-safe manner, which may trigger some hard-to-debug issues, like [this boto issue](https://github.com/boto/boto/issues/1898)&nbsp;and [the corresponding Python bug page](http://bugs.python.org/issue7980). This problem can be solved by doing `"import _strptime"`&nbsp;earlier in the main thread so the thread won’t block due to the lock or see an incomplete module.

### Python 3 is the future<span style="font-size: 16px; line-height: 1.5;">&nbsp;</span>

But why did it works in Python 3? In addition to breaking all your import names and forcing you to put `".encode"`&nbsp;and `".decode"`&nbsp;everywhere in your code (which is a good thing!), the import lock was refactored in Python 3, and [global import locking is now handled per-module](https://mail.python.org/pipermail/python-dev/2013-August/127902.html). You’re now less likely to trigger the problem.

### We should all use Python 3<span style="font-size: 16px; line-height: 1.5;">&nbsp;</span>

It may have been a very nasty bug, but it was pretty fun to dig into one of the lesser known mechanisms of Python.

**TL;DR**: In Python 2, there is a single reentrant lock that will block your threads if they try to import something while the lock is held by another thread. In Python 3, the import lock is now per module.
