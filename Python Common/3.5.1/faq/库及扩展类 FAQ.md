# 库及扩展类 FAQ

原文： [Library and Extension FAQ](https://docs.python.org/3/faq/library.html)

---
<!-- MarkdownTOC -->

- [一般库问题](#一般库问题)
    - [如何查找一个执行任务X的模块或应用？](#如何查找一个执行任务x的模块或应用？)
    - [math.py (socket.py, regex.py, 等.)的源文件在哪里？](#mathpy-socketpy-regexpy-等的源文件在哪里？)
    - [如何在Unix上使一个Python脚本可执行？](#如何在unix上使一个python脚本可执行？)
    - [有一个用于Python的curses/termcap包吗？](#有一个用于python的cursestermcap包吗？)
    - [Python中有C的onexit()的等价物吗？](#python中有c的onexit的等价物吗？)
    - [为什么我的信号处理器不工作？](#为什么我的信号处理器不工作？)
- [常见任务](#常见任务)
    - [如何测试一个Python程序或组件？](#如何测试一个python程序或组件？)
    - [如何从文档字符串中创建文档？](#如何从文档字符串中创建文档？)
    - [如何一次活动一个按键？](#如何一次活动一个按键？)
- [线程](#线程)
    - [如何使用线程进行编程？](#如何使用线程进行编程？)
    - [我的线程似乎都不运行：为什么？](#我的线程似乎都不运行：为什么？)
    - [如何在一堆工作线程中分配工作？](#如何在一堆工作线程中分配工作？)
    - [什么样的全局变异量是线程安全的？](#什么样的全局变异量是线程安全的？)
    - [我们可以摆脱全局解释锁吗？](#我们可以摆脱全局解释锁吗？)
- [输入和输出](#输入和输出)
    - [如何删除一个文件？（及其他文件问题……）](#如何删除一个文件？（及其他文件问题……）)
    - [如何复制一个文件？](#如何复制一个文件？)
    - [如何读取（或写入）二进制数据？](#如何读取（或写入）二进制数据？)
    - [我似乎无法在一个由os.popen()创建的管道上使用os.read()；为什么？](#我似乎无法在一个由ospopen创建的管道上使用osread；为什么？)
    - [如何访问串口（RS232）端口？](#如何访问串口（rs232）端口？)
    - [为什么关闭sys.stdout (stdin, stderr)并不真正关闭它？](#为什么关闭sysstdout-stdin-stderr并不真正关闭它？)
- [网络/Internet编程](#网络internet编程)
    - [Python的WWW工具有啥？](#python的www工具有啥？)
    - [怎样模仿CGI表单提交(METHOD=POST)？](#怎样模仿cgi表单提交methodpost？)
    - [我应该使用什么模块以助于生成HTML？](#我应该使用什么模块以助于生成html？)
    - [如何在Python脚本中发送邮件？](#如何在python脚本中发送邮件？)
    - [如何避免阻塞套接字的connect()方法？](#如何避免阻塞套接字的connect方法？)
- [数据库](#数据库)
    - [Python中有任何数据库包的接口吗？](#python中有任何数据库包的接口吗？)
    - [如何在Python中实现持久化对象？](#如何在python中实现持久化对象？)
- [数学和数字](#数学和数字)
    - [如何在Python中生成随机数？](#如何在python中生成随机数？)

<!-- /MarkdownTOC -->


## 一般库问题
### 如何查找一个执行任务X的模块或应用？
检查[库参考](https://docs.python.org/3/library/index.html#library-index)，看看是否有相关的标准库模块。（最终你会学到标准库中有什么，并可以跳过此步骤。）

对于第三方软件包，搜索[Python包索引](https://pypi.python.org/pypi)或尝试[谷歌](https://www.google.com/)或其他搜索引擎。搜索“Python”加上一两个你感兴趣的主题的关键字，通常会发现一些有帮助的东西。

### math.py (socket.py, regex.py, 等.)的源文件在哪里？
如果你不能找到一个模块的源文件，那么它可能是用C，C ++或其他编译语言实现的内置或动态加载的模块。在这种情况下，可能没有源文件，也可能是类似 mathmodule.c的东西，在C源代码目录（而不是在Python的路径）下。

在Python中，有（至少）三种模块：

1. 用Python编写的模块 (.py);

2. 用C编写及动态加载的模块(.dll, .pyd, .so, .sl, 等);

3. 用C编写并与解释器链接在一起的模块；要获得这类模块的列表，输入：
```python
import sys
print(sys.builtin_module_names)
```

### 如何在Unix上使一个Python脚本可执行？
你需要做两件事情：该脚本文件必须是可执行文件，以及第一行必须以`#!`开头，并紧接着Python解释器的路径。

第一件事是通过执行`chmod +x scriptfile` 或者 `chmod 755 scriptfile`完成。

第二可以以多种方式来完成。最直接的方法就是写：

`#!/usr/local/bin/python`

作为文件的第一行，使用Python解释器在平台上的安装路径。

如果您想要脚本是独立于Python解释器的位置，那么你可以使用env程序。几乎所有的Unix变体支持下面写法，假设Python解释器位于用户PATH中的的一个目录：

`#!/usr/bin/env python`

不要对CGI脚本这样做。CGI脚本中的PATH变量往往是很小的，所以你需要使用解释器的实际绝对路径。

有时，用户的环境已满，以致于`/usr/bin/env`程序失败; 或者完全没有env程序。在这种情况下，你可以试试下面的技巧（由Alex Rezinsky提供）：
```python
#! /bin/sh
""":"
exec python $0 ${1+"$@"}
"""
```
一个小小的缺点是，它定义了该脚本的**__doc__**字符串。但是，你可以通过增加下面内容来修复：

`__doc__ = """...Whatever..."""`

### 有一个用于Python的curses/termcap包吗？
对于Unix变体：[Modules](https://hg.python.org/cpython/file/3.5/Modules)子目录中的一个curses模块伴随着标准Python源码发布版，虽然它不是默认编译的。（注意，这在Windows发布版本不可用-对于Windows，没有curses模块。）

该[curses](https://docs.python.org/3/library/curses.html#module-curses)模块支持基本的curses功能，以及从ncurses到SYSV curses的许多附加功能，如颜色，替代字符集支持，pads和鼠标的支持。这意味着模块与只具有BSD curses的操作系统不兼容，但似乎并没有被任何当前维护的操作系统是属于这一类的。

对于Windows：使用[consolelib](http://effbot.org/zone/console-index.htm)模块。

### Python中有C的onexit()的等价物吗？
[atexit](https://docs.python.org/3/library/atexit.html#module-atexit)模块提供了一个类似于C的onexit()函数的寄存器函数。

### 为什么我的信号处理器不工作？
最常见的问题是，使用错误的参数列表来声明该信号处理器。它被这样调用：

`handler(signum, frame)`
所以应用两个参数来声明它：
```python
def handler(signum, frame):
    ...
```

## 常见任务
### 如何测试一个Python程序或组件？
Python提供了两个测试框架。[doctest](https://docs.python.org/3/library/doctest.html#module-doctest)模块搜索一个模块的文档字符串中的例子并运行它们，然后将输出与文档字符串中提供的预期输出进行比较。

[unittest](https://docs.python.org/3/library/unittest.html#module-unittest)模块是一个更神奇的测试框架，它以Java和Smalltalk测试框架为范本。

为了使测试更加简单，你应该在你的程序中使用良好的模块化设计。你的程序中应该将几乎所有的功能封装在任何函数或类方法中 - 这有时具有使程序运行速度更快的令人惊奇和令人愉快的效果（因为局部变量的访问比全局变量的访问速度更快）。此外，该方案应避免依赖突变的全局变量，因为这使得测试更困难。

程序的“全局main逻辑”可能很简单，如：
```python
if __name__ == "__main__":
    main_logic()
```
在程序的主模块底部。

一旦你的程序被组织成函数和类行为的可跟踪集合，你就应该写测试函数来测试这些行为。一个自动运行一系列测试的测试套件，可与每个模块相关联。这听起来需要大量的工作，但由于Python是如此简洁和灵活，因此它是非常容易的。您可以通过编写与“生产代码”并行的测试函数来使得编码更加舒适有趣，因为这可以很容易地发现错误，甚至更早的设计缺陷。

不打算成为程序的主模块的“支持模块”可能包含模块的自检。
```python
if __name__ == "__main__":
    self_test()
```
当外部接口不可用时，通过使用Python实现的“假”接口，即使是那些与复杂的外部接口进行交互的程序也可以对其进行测试。

### 如何从文档字符串中创建文档？
[pydoc](https://docs.python.org/3/library/pydoc.html#module-pydoc)模块可以根据你的Python源代码中的文档字符串创建HTML。单纯从文档字符串创建API文档的另一种方法是[epydoc](http://epydoc.sourceforge.net/)。 [Sphinx](http://sphinx-doc.org/)也可以包含文档字符串的内容。

### 如何一次活动一个按键？
对于Unix变体，有几种解决方案。最直接的方法是使用curses，但是curses是一个相当大的模块。

## 线程
### 如何使用线程进行编程？
.一定要使用的[threading](https://docs.python.org/3/library/threading.html#module-threading)模块，而不是[_thread](https://docs.python.org/3/library/_thread.html#module-_thread)模块。[threading](https://docs.python.org/3/library/threading.html#module-threading)模块建立在由[_thread](https://docs.python.org/3/library/_thread.html#module-_thread)模块所提供的低级原语的方便抽象之上。

Aahz在他的线程教程中有一套有用的幻灯片；见http://www.pythoncraft.com/OSCON2001/.

### 我的线程似乎都不运行：为什么？
一旦主线程退出，所有的线程就会被杀掉。主线程运行太快，使得线程没有时间做任何工作。

一个简单的解决方法是，在程序末端增加一个足够长的sleep以便于结束线程：
```python
import threading, time

def thread_task(name, n):
    for i in range(n): print(name, i)

for i in range(10):
    T = threading.Thread(target=thread_task, args=(str(i), i))
    T.start()

time.sleep(10)  # <---------------------------!
```
但现在（在许多平台上），线程并不并行运行，而似乎是按顺序运行，一次一个！其原因是操作系统线程调度不启动一个新的线程，直到前一个线程被阻塞。

一个简单的解决方法是在run函数的前端增加一个小小的sleep：
```python
def thread_task(name, n):
    time.sleep(0.001)  # <--------------------!
    for i in range(n): print(name, i)

for i in range(10):
    T = threading.Thread(target=thread_task, args=(str(i), i))
    T.start()

time.sleep(10)
```
不要试图为[time.sleep()](https://docs.python.org/3/library/time.html#time.sleep)猜测一个好的延迟值，而是最好使用一些信号量机制。其中一个想法是使用[queue](https://docs.python.org/3/library/queue.html#module-queue)模块来创建一个队列(queue)对象，每个线程在结束的时候往该队列中附加一个token，只要有线程，就让主线程从队列中读取token。

### 如何在一堆工作线程中分配工作？
最简单的方法是使用新的[concurrent.futures](https://docs.python.org/3/library/concurrent.futures.html#module-concurrent.futures)模块，特别是[ThreadPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#concurrent.futures.ThreadPoolExecutor)类。

或者，如果你想很好地控制调度算法，可以手工编写自己的逻辑。使用[queue](https://docs.python.org/3/library/queue.html#module-queue)模块来创建一个包含作业列表的队列。[Queue](https://docs.python.org/3/library/queue.html#queue.Queue)类维护对象的列表，并有一个`.put(obj)`方法用于向队列中添加元素，以及一个`.get()`方法来返回它们。类会处理好必要的锁定，以确保一次返回一个作业。

这里有一个简单的例子：
```python
import threading, queue, time

# The worker thread gets jobs off the queue.  When the queue is empty, it
# assumes there will be no more work and exits.
# (Realistically workers will run until terminated.)
def worker():
    print('Running worker')
    time.sleep(0.1)
    while True:
        try:
            arg = q.get(block=False)
        except queue.Empty:
            print('Worker', threading.currentThread(), end=' ')
            print('queue empty')
            break
        else:
            print('Worker', threading.currentThread(), end=' ')
            print('running with argument', arg)
            time.sleep(0.5)

# Create queue
q = queue.Queue()

# Start a pool of 5 workers
for i in range(5):
    t = threading.Thread(target=worker, name='worker %i' % (i+1))
    t.start()

# Begin adding work to the queue
for i in range(50):
    q.put(i)

# Give threads time to run
print('Main thread sleeping')
time.sleep(5)
```
运行时将产生以下输出：
```python
Running worker
Running worker
Running worker
Running worker
Running worker
Main thread sleeping
Worker <Thread(worker 1, started 130283832797456)> running with argument 0
Worker <Thread(worker 2, started 130283824404752)> running with argument 1
Worker <Thread(worker 3, started 130283816012048)> running with argument 2
Worker <Thread(worker 4, started 130283807619344)> running with argument 3
Worker <Thread(worker 5, started 130283799226640)> running with argument 4
Worker <Thread(worker 1, started 130283832797456)> running with argument 5
...
```
参考该模块的文档以了解更多详细信息；[Queue](https://docs.python.org/3/library/queue.html#queue.Queue)类提供了一个多特征的接口。

### 什么样的全局变异量是线程安全的？
一个[全局解释器锁（GIL）](https://docs.python.org/3/glossary.html#term-global-interpreter-lock)用于内部，以确保在同一时间内Python虚拟机上只运行一个线程。一般来说，Python只提供了字节码指令之间的线程切换; 可以通过`sys.setswitchinterval`设置切换频率。在Python程序看来，每个字节码指令，以及每一个指令所指的所有C实现代码，都是原子的。

理论上，这意味着确切计数需要对PVM字节码实现的精确了解。实践上，它意味着，在内置数据类型（ints, lists, dicts, 等等）的共享变量上的操作，看起来是“原子的”，并且实际上确实是。

例如，下面的操作都是原子的（L, L1, L2是列表, D, D1, D2是字典, x, y是对象, i, j是整型）：
```python
L.append(x)
L1.extend(L2)
x = L[i]
x = L.pop()
L1[i:j] = L2
L.sort()
x = y
x.field = y
D[x] = y
D1.update(D2)
D.keys()
```
这些都不是：
```python
i = i+1
L.append(L[-1])
L[i] = L[j]
D[x] = D[x] + 1
```
当它们的引用计数达到0时，替代其他对象的操作可能调用这些其它对象的 [__del__()](https://docs.python.org/3/reference/datamodel.html#object.__del__)方法，并且可能影响的一些东西。对于字典和列表大量的更新来说尤其如此。如有疑问，请使用互斥！

### 我们可以摆脱全局解释锁吗？
在[全局解释器锁（GIL）](https://docs.python.org/3/glossary.html#term-global-interpreter-lock)往往被视为Python在高端多处理器服务器上部署的一个障碍，因为出于坚持认为（几乎）所有的Python代码只能在坚持GIL的情况下运行的原因，一个多线程的Python程序只能有效地使用一个CPU。

回到Python 1.5的那些日子，Greg Stein实际上实现了一个全面的补丁集（“自由线程”补丁），它去掉了GIL，并用一个细粒度锁来取代它。Adam Olsen最近在他的[python-safethread](http://code.google.com/p/python-safethread/)项目中进行了类似的实验。不幸的是，这两个实验都在单线程性能上显示出明显下滑（至少30%的下降），因为细粒度锁必须补偿对GIL的去除。

这并不意味着你不能在多CPU的机器上好好的使用Python！你只需要创造性地在多进程之间分配工作，而不是多线程。在新的[concurrent.futures](https://docs.python.org/3/library/concurrent.futures.html#module-concurrent.futures)模块中的[ProcessPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#concurrent.futures.ProcessPoolExecutor)类提供了一个简单的方法来做到这点; [multiprocessing](https://docs.python.org/3/library/multiprocessing.html#module-multiprocessing)模块提供一个较低级别的API以防你想更好地控制任务的调度。

明智地使用C扩展也将有所帮助; 如果您使用的是C扩展来执行耗时的任务，扩展在执行的线程是C代码的情况下可以释放GIL，并允许其他线程完成一些工作。一些标准库模块，如[zlib](https://docs.python.org/3/library/zlib.html#module-zlib)和[hashlib](https://docs.python.org/3/library/hashlib.html#module-hashlib)已经做到这一点。

有人建议，GIL应该是每个解释器状态锁，而不是真正的全局; 那么解释器之间将无法共享对象。不幸的是，这也是不可能发生的。这将是需要巨大的工作量，因为许多对象的实现目前拥有全局状态。例如，小整数和短字符串缓存; 这些缓存必须被移动到解释器状态。其他对象类型都有自己的空闲列表; 这些空闲列表将不得不被转移到解释器状态。等等。

而且我怀疑，它是否可以在有限的时间内完成，因为第三方扩展也存在同样的问题。也有可能，比之你可以将它们转换以在解释器状态中储存它们所有的全局状态，它们可以被编写以达到更快的速度。

最后，一旦你有多个不共享状态的解释器，那么比之在每个进程中运行一个解释器，你又获得了什么呢？

## 输入和输出
### 如何删除一个文件？（及其他文件问题……）
使用`os.remove(filename)` 或者 `os.unlink(filename)`; 请参阅[os](https://docs.python.org/3/library/os.html#module-os)模块。这两个函数功能是相同的; [unlink()](https://docs.python.org/3/library/os.html#os.unlink)仅仅是Unix系统调用这个函数的名称。

要删除一个目录，使用`os.rmdir()`; 使用[os.mkdir()](https://docs.python.org/3/library/os.html#os.mkdir)来创建一个目录。`os.makedirs(path)`将创建路径中任何不存在的目录。`os.removedirs(path)`，将去除只要是空的中间目录; 如果要删除整个目录树及其内容，请使用`shutil.rmtree()`。

要重命名文件，使用`os.rename(old_path, new_path)`。

要截断一个文件，使用`f = open(filename, "rb+")`打开它，然后使用`f.truncate(offset)`; offset默认为当前查找位置。还有用于使用`os.open()`打开的文件的`os.ftruncate(fd, offset)`，其中，fd是文件描述符（一个小整数）。

[shutil](https://docs.python.org/3/library/shutil.html#module-shutil)模块还包含许多关于文件的函数，包括的[CopyFile()](https://docs.python.org/3/library/shutil.html#shutil.copyfile)，copytree()，和 rmtree()。

### 如何复制一个文件？
[shutil](https://docs.python.org/3/library/shutil.html#module-shutil)模块包含一个[copyfile()](https://docs.python.org/3/library/shutil.html#shutil.copyfile)函数。注意，在MacOS 9上，他不会复制资源的派生以及Finder信息。

### 如何读取（或写入）二进制数据？
要读取或写入复杂的二进制数据格式，最好使用[struct](https://docs.python.org/3/library/struct.html#module-struct)模块。它允许你接收一个包含二进制数据（通常为数字）的字符串，并将其转换为Python对象; 反之亦然。

例如，下面的代码从文件中以大端格式读取两个2字节的整数和一个4字节整数：
```python
import struct

with open(filename, "rb") as f:
   s = f.read(8)
   x, y, z = struct.unpack(">hhl", s)
```
格式字符串中的'>'会强制大端数据; 字母“h”从字符串中读取一个“短整型”（2字节），“l”从字符串中读取一个“长整数”（4字节）。

对于比较常规的数据（例如整数或浮点数的同质列表），你也可以使用[array](https://docs.python.org/3/library/array.html#module-array)模块。

>注意： 要读写二进制数据，那么强制以二进制方式打开该文件（这里，传`"rb"`给[open()](https://docs.python.org/3/library/functions.html#open)）。如果使用`"r"`（默认），那么文件将以文本模式打开，而`f.read`将返回[str](https://docs.python.org/3/library/stdtypes.html#str)对象，而不是 [bytes](https://docs.python.org/3/library/functions.html#bytes)对象。

### 我似乎无法在一个由os.popen()创建的管道上使用os.read()；为什么？
[os.read()](https://docs.python.org/3/library/os.html#os.read)是一个低级别的函数，它接受一个文件描述符，一个表示打开文件的小整数。[os.popen()](https://docs.python.org/3/library/os.html#os.popen)创建了一个高层次的文件对象，与内置的[open()](https://docs.python.org/3/library/functions.html#open)函数具有相同的返回类型。因此，要从使用[os.popen()](https://docs.python.org/3/library/os.html#os.popen)创建的管道`p`中读取`n`个字节，你需要使用`p.read(n)`。

### 如何访问串口（RS232）端口？
对于Win32，POSIX（Linux，BSD等），Jython的：

http://pyserial.sourceforge.net

对于Unix，见Mitch Chapman的一个新闻组帖子：

http://groups.google.com/groups?selm=34A04430.CF9@ohioee.com

### 为什么关闭sys.stdout (stdin, stderr)并不真正关闭它？
Python的[file对象](https://docs.python.org/3/glossary.html#term-file-object)是在低级C文件描述符上的高级抽象。

对于大多数文件对象，在Python中通过内置[open](https://docs.python.org/3/library/functions.html#open)函数进行创建，从Python的角度来看，`f.close()`标记的Python文件对象已经被关闭，并且将关闭底层C文件描述符。这在`f`变成垃圾后，在`f`的析构函数中也会自动发生。

但是，因为C提供的特殊状态，标准输入，输出和错误都经过Python的特殊处理。运行`sys.stdout.close()`标记着Python级别的文件对象是被关闭的，但并不会关闭关联的C文件描述符。

要对这三个中的一个关闭底层C文件描述符，你首先应该肯定这确实是你真正想做的事情（比如，你可能会混淆试图做I/O的扩展模块）。如果是这样的话，则使用`os.close()`：
```python
os.close(stdin.fileno())
os.close(stdout.fileno())
os.close(stderr.fileno())
```
或者你也可以分别使用数字常量0,1和2。

## 网络/Internet编程
### Python的WWW工具有啥？
见库参考手册上的标题为[互联网协议及支持](https://docs.python.org/3/library/internet.html#internet)和[互联网数据处理](https://docs.python.org/3/library/netdata.html#netdata)的章节。Python有许多模块，用于帮助你建立服务器端和客户端的网络系统。

可用框架的整理由Paul Boddie维护，见 https://wiki.python.org/moin/WebProgramming。

Cameron Laird维护了关于Python Web技术的一组有用页面，见 http://phaseit.net/claird/comp.lang.python/web_python.

### 怎样模仿CGI表单提交(METHOD=POST)？
我想检索由提交表单的结果生成的网页。是否有现有的代码可以让我轻松地做到这一点？

是滴，有一个使用`urllib.request`的简单例子：
```python
#!/usr/local/bin/python

import urllib.request

### build the query string
qs = "First=Josephine&MI=Q&Last=Public"

### connect and send the server a path
req = urllib.request.urlopen('http://www.some-server.out-there'
                             '/cgi-bin/some-cgi-script', data=qs)
with req:
    msg, hdrs = req.read(), req.info()
```
注意，对于一般的百分比编码的POST操作，查询字符串必须使用`urllib.parse.urlencode()`来引用。例如，要发送`name=Guy Steele, Jr.`：
```python
>>> import urllib.parse
>>> urllib.parse.urlencode({'name': 'Guy Steele, Jr.'})
'name=Guy+Steele%2C+Jr.'
```
>另见 [如何使用urllib包获取互联网资源](https://docs.python.org/3/howto/urllib2.html#urllib-howto)，以获得更多的例子。

### 我应该使用什么模块以助于生成HTML？
你可以在[Web编程的维基页面](https://wiki.python.org/moin/WebProgramming)上找到有用链接的集合。

### 如何在Python脚本中发送邮件？
使用标准库模块[smtplib](https://docs.python.org/3/library/smtplib.html#module-smtplib)。

下面是使用它的一个非常简单的交互式邮件发送者。这种方法将工作在支持SMTP侦听器的任何主机上。
```python
import sys, smtplib

fromaddr = input("From: ")
toaddrs  = input("To: ").split(',')
print("Enter message, end with ^D:")
msg = ''
while True:
    line = sys.stdin.readline()
    if not line:
        break
    msg += line

# The actual mail send
server = smtplib.SMTP('localhost')
server.sendmail(fromaddr, toaddrs, msg)
server.quit()
```
一个只用于Unix的可选项是使用sendmail。sendmail程序的位置在系统之间是变化的; 有时它是`/usr/lib/sendmail`，有时是`/usr/sbin/sendmail`。sendmail的手册将帮助你。下面是一些示例代码：
```python
SENDMAIL = "/usr/sbin/sendmail"  # sendmail location
import os
p = os.popen("%s -t -i" % SENDMAIL, "w")
p.write("To: receiver@example.com\n")
p.write("Subject: test\n")
p.write("\n")  # blank line separating headers from body
p.write("Some text\n")
p.write("some more text\n")
sts = p.close()
if sts != 0:
    print("Sendmail exit status", sts)
```
### 如何避免阻塞套接字的connect()方法？
[select](https://docs.python.org/3/library/select.html#module-select)模块通常用于帮助套接字上的异步I/O。

为了防止TCP连接阻塞，可以设置套接字为非阻塞模式。然后，当你进行`connect()`操作时，将可以立即连接（不太可能），或者得到一个包含错误号`.errno`的异常。`errno.EINPROGRESS`表示连接正在进行中，但还没有完成。不同的操作系统将返回不同的值，所以你要检查你的系统返回了什么。

您可以使用`connect_ex()`方法，以避免产生异常。它将只返回错误值。要进行轮询，您可以稍后再调用`connect_ex()` - 0或errno.EISCONN表明您已经连接-或者你可以将这个套接字传递给select来检查它是否是可写的。

>注意：[asyncore](https://docs.python.org/3/library/asyncore.html#module-asyncore)库为编写非阻塞网络的代码提供了一个类似于框架的方法。第三方的[Twisted](https://twistedmatrix.com/trac/)库是一个流行且功能丰富的选择。

## 数据库
### Python中有任何数据库包的接口吗？
有。

标准Python也包含了基于磁盘的哈希（例如[DBM](https://docs.python.org/3/library/dbm.html#module-dbm.ndbm)和[GDBM](https://docs.python.org/3/library/dbm.html#module-dbm.gnu)）的接口。还有一个 [sqlite3](https://docs.python.org/3/library/sqlite3.html#module-sqlite3)的模块，它提供了一个轻量级基于磁盘关系的数据库。

对于大多数关系型数据库的支持是可用的。见[DatabaseProgramming维基页面](https://wiki.python.org/moin/DatabaseProgramming)了解详细信息。

### 如何在Python中实现持久化对象？
[pickle](https://docs.python.org/3/library/pickle.html#module-pickle)库模块以一种非常普遍的方法解决了这个问题（虽然你仍然不能储存像打开的文件，套接字或窗口这类的东西），而[shelve](https://docs.python.org/3/library/shelve.html#module-shelve)库模块使用pickle和(g)dbm创建包含任意Python对象的持久化映射。

## 数学和数字
### 如何在Python中生成随机数？
标准模块[random](https://docs.python.org/3/library/random.html#module-random)实现了一个随机数生成器。用法很简单：
```python
import random
random.random()
```
它将返回[0, 1)范围内的随机浮点数。

此模块中还有许多其他专业的生成器，例如：

* `randrange(a, b)` 在[a, b)范围中选取一个整数.
* `uniform(a, b)` 在[a, b)范围中选取一个浮点数.
* `normalvariate(mean, sdev)` 对正常（高斯）分布进行取样。

某些更高级别的功能直接操作序列，如：

* `choice(S)`从一个给定的序列中选取随机元素
* `shuffle(L)` 就地打乱序列，即随机排列它们

还有一个`Random`类，你可以对其进行实例化，以创建多个独立的随机数生成器。
