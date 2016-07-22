原文：[Freeze your Python with str.encode and threads](https://blog.sqreen.io/freeze-python-str-encode-threads/)

---

[![](https://blog.sqreen.io/wp-content/uploads/2016/07/frozen-python-2040.png)](https://blog.sqreen.io/wp-content/uploads/2016/07/frozen-python-2040.png "frozen-python-2040")
                                    
当我在[sqreen.io Python agent](https://www.sqreen.io/?utm_medium=social&utm_source=blog&utm_campaign=Freeze%20your%20Python%20with%20str.encode%20and%20threads)工作的时候，我发现了一个相当讨厌，但是分析起来却挺有意思的bug，它让我深入到Python内部。

### 这个讨厌的bug

我正在写一段代码，它产生一个线程，检索一些JSON，然后存储以用于后续使用。基本上，代码看起来是这样的：

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

一切似乎都还好，并且当网络延迟过高的时候，错误信息会正确显示：

```sh
$> python2 app.py
Data retrieval done!
```

接下来，我想将字典键和值从unicode转换成latin-1字符串：

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

我敢肯定，在接收JSON时，你们都做过这种类型的转换，不是吗？

添加这个代码后，无论我提供了什么样子的值，错误信息开始显示超时，从5s到1小时。而更诡异的是，`"Data retrieval done!"`信息总是在错误信息之后打印出来：

```sh
$> python2 app.py
JSON retrieval was too slow
Data retrieval done!
```

然而，当我在Python 3上跑的时候，一切如我所预期：

```sh
$> python3 app.py
Data retrieval done!
```

一些非常奇怪的事情一直发生，而我决定调查一下。

### 隔离

我试图找出问题所在，于是有了这个最小化的再现脚本，让我们把它命名为`"str_encode_thread.py"`：

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

如果你用Python 3启动这个脚本，将会看到：

```sh
$> python3 str_encode_thread.py
1467046041.643346 Before encode
1467046041.643446 After encode
1467046041.643483 Event set
```

而用Python 2，你会看到：

```sh
$> python2 str_encode_thread.py
(1467046099.495261, 'Before encode')
(1467046099.495937, 'After encode')
(1467046099.495961, 'Event set')
```

目前一切OK。而当我们尝试导入这个脚本时，好玩的事情来了。如果你用Python 3导入这个脚本，那么一切仍然是正常的：

```sh
$> time python3 -c "import str_encode_thread"
1467046239.921475 Before encode
1467046239.921578 After encode
1467046239.921602 Event set
python3 -c "import str_encode_thread"  0,05s user 0,01s system 84% cpu 0,081 total
```

而用Python 2导入时，我们讨厌的朋友再次出现了：

```sh
$> time python2 -c "import str_encode_thread"
(1467046276.826755, 'Before encode')
(1467046286.830485, 'After encode') # <- 10 seconds (╯°□°）╯︵ ┻━┻
(1467046286.830519, 'Event set')
python2 -c "import str_encode_thread"  0,03s user 0,03s system 0% cpu 10,109 total
```

如果你仔细看看`"After encode"`消息，可以看到，它在`"Before encode"`消息打印10s之后才打印，这意味着线程莫名其妙被冻结了。

### 掉进兔子洞，不知如何是好

为嘛一个简单的`"value.encode('latin1', 'encode')"`会阻塞一个线程呢？

幸好，有一个[非常棒的名为faulthandler的包](https://pypi.python.org/pypi/faulthandler/2.4)，它在这种情况下非常有用。它可以在unix信号、用户信号或者python错误上展示回溯。从Python 3.3开始，它就是标准库的一部分了，而对于老一点的Python版本，可以用pip进行安装。

本例中，我们将使用[dump_traceback_later](http://faulthandler.readthedocs.io/#dumping-the-tracebacks-after-a-timeout)来看看该线程在哪里阻塞。

带faulthandler的新脚本并没有什么不同 (不要忘了`"pip install faulthandler"`):

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

让我们运行新的脚本：

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

回溯中我们能看到什么？

主线程`"0x00007fff71258000"`在我们模块的第20行阻塞了，在那里是`"event.wait(10)"`，这完全正常。

其他线程`"0x0000700000401000"`正运行我们模块的第11行`"value.encode('latin-1')"`，并且调用内置模块`"encoding"`的函数`"search_function"`，它冻结了我们的线程。这个函数做了什么，为什么它是阻塞的？

[源代码](https://hg.python.org/cpython/file/v2.7.12rc1/Lib/encodings/__init__.py#l99)告诉我们，它试图导入一个以`"encodings."`开头的模块。

```python
In [1]: import sys

In [2]: before_modules = sys.modules.keys()

In [3]: '42'.encode('latin-1')
Out[3]: '42'

In [4]: after_modules = sys.modules.keys()

In [5]: print("Imported modules", set(after_modules) - set(before_modules))
('Imported modules', set(['encodings.latin_1']))
```

明白了！`"str.encode(X)"`试图导入模块`"encodings.X"`。所以修复应该是容易的：在主线程中，使用该函数之前导入该模块，这样就可以了，是吧？

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

让我们用python2再试试：

```sh
$> time python2 -c "import str_encode_thread"
(1467049098.995541, 'Before encode')
(1467049109.000293, 'After encode')
(1467049109.000324, 'Event set')
python2 -c "import str_encode_thread"  0,03s user 0,02s system 0% cpu 10,054 total
```

我们仍然有相同的问题！怎么了？

### 必须更深入

我们知道，`"str.encode"`试图导入一个可能冻结我们线程的模块。

在这一点上，我到我最喜欢的IRC频道 (#python-fr)寻求帮助，最终我们找到了原因！

`"import module"`做了什么？如果我们相信[Python文档](https://docs.python.org/2/library/imp.html#examples)，那它基本上做了这些：

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

没有啥特别的：如果已经导入了该模块，那么从`"sys.modules"`返回该模块，否则，尝试找到该模块的源文件，加载并返回。

这里也不应该阻塞，但若我们继续看`"imp"`模块文档，可以找到一个[非常有趣的名为`"lock_held"`的函数](https://docs.python.org/2/library/imp.html#imp.lock_held)：

> 在多线程平台上，执行导入的线程持有一个内部锁，直到导入完成。这个锁阻塞了其他线程进行导入，直到初始导入完成，这反过来阻止其他线程看到由初始线程在完成导入（以及可能有的由此导入触发的其他导入）过程中构建的未完成的模块对象。

真相越来越近……

### 锁和线程，爱恨交加

当搜索`"python import lock"`，我们可以迅速找到关于[在线程代码中导入](https://docs.python.org/2/library/threading.html#importing-in-threaded-code)的章节，它只是告诉你这是一个可怕的想法，并有可能造成死锁。

让我们总结下我们的发现：

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

当我们导入我们的模块`"str_encode_thread"`时，导入锁由主线程获得，因此当我们的自定义线程试图执行`"value.encode('latin-1')"`时，stdlib试图导入`"encoding.latin_1"`模块，这会被阻塞，因为导入锁已经在主线程手里。然后，我们通过`"event.wait(10)"`阻塞了主线程，接着这两个线程都阻塞了10秒，太糟糕了……当等待超时，`"import str_encode_thread"`结束，释放导入锁，从而唤醒了我们的自定义线程。

如果我们没有设置一个最大化的事件超时，我们将永远让Python解释器死锁！

在处理线程时，并无神奇解决方案。如果你手动导入一个模块而无需使用锁，那么你可能不知道发生了什么，但是它只会通过抛出额外有趣的bug来“解决”死锁。

例如，当你调用`"time.strptime"`时，它试图以一种不安全的方式导入`"_strptime"`模块，这可能会引发一些难以调试的问题，像[这个boto问题](https://github.com/boto/boto/issues/1898)和[对应的Python bug页面](http://bugs.python.org/issue7980)。这个问题可以通过在主线程之前`"import _strptime"`，这样线程就不会因为锁或者看到一个未完成的模块而阻塞，从而解决。

### **Python 3**才是未来

那为什么在Python 3就一切正常？除了打破你所有的导入名，强制你在代码中到处用`".encode"`和`".decode" (这是件好事！)之外，Python 3对导入锁进行了重构，而[现在每个模块都处理全局导入锁](https://mail.python.org/pipermail/python-dev/2013-August/127902.html)。现在，你不大可能触发这个问题了。

### 我们都应该使用**Python 3**

这可能是一个非常讨厌的问题，但是深入Python的一个鲜为人知的机制却是非常有趣的。

**TL;DR**: Python 2中，还有一个可重入锁，当该锁由其他线程持有时，如果你的线程试图进行导入，它将阻塞它们。Python 3中，该导入锁现在是每个模块持有的。
