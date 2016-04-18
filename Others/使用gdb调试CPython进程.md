原文：[Debugging of CPython processes with gdb](http://podoliaka.org/2016/04/10/debugging-cpython-gdb/)

---

当Python程序员需要找到他们应用中的问题根源时，[pdb](https://docs.python.org/3.5/library/pdb.html)一直是，而且很可能永远是他们的面包和黄油，因为它是一个内置的，并且易于使用的调试器。但也有些情况时`pdb`无法帮你的，例如，如果你的应用在某些地方卡住了，而你需要在不重启它的情况下，连接到正在运行的进程来找出原因。而这就是[gdb](https://www.gnu.org/software/gdb/)让人眼前一亮的原因。

## 为嘛是gdb?

`gdb`是一个通用调试器，它主要是用于C和C++应用程序的调试（虽然它实际上支持阿Ada, Objective-C, Pascal等等）。

Python程序员对使用`gdb`进行调试感兴趣有多种原因：

*   `gdb`允许你在不以debug模式启动一个应用，或者以某些方式先修改该应用代码 (例如，把一些像`import rpdb; rpdb.set_trace()`之类的东西放到代码里)的情况下，连接到一个正在运行的进程。

*   `gdb`允许你获得一个进程的[核心转储(core dump)](https://en.wikipedia.org/wiki/Core_dump)，以便稍后分析。当你不希望停止该进程的持续时间，或者当你正在审视它的状态，以及当你对一个已经失败(e.g. [crashed](https://www.freedesktop.org/software/systemd/man/systemd-coredump.html) with a segmentation fault)的程序进行[事后剖析](https://en.wikipedia.org/wiki/Debugging#Techniques)时，这是有用的。

*   Python大多数可用的调试器 (明显的例外是[winpdb](http://winpdb.org/)和[pydevd](https://github.com/fabioz/PyDev.Debugger))并不支持在被调试的应用线程之间进行切换。`gdb`允许这样，它还允许调试由非Python代码(例如，在一些使用的原生库中)创建的线程 

## 解释型语言的调试

所以，当使用`gdb`时，是什么让Python与众不同呢？

不像诸如C或C++这样的编程语言，Python代码并不会被编译成目标平台的本地二进制文件。取而代之的是，有一个解释器 (例如，[CPython](https://en.wikipedia.org/wiki/CPython)，Python的参考实现)，它执行编译的[字节码](http://security.coverity.com/blog/2014/Nov/understanding-python-bytecode.html)。

这实际上意味着，当你用`gdb`连接到一个Python进程时，你会在解释器级别调试解释器实例和内省进程状态，而不是应用程序级别：即你会看到解释器的函数和变量，而不是你的应用程序。

给你举个例子，让我们来看看一个CPython（最流行 ​的Python解释器）进程的`gdb`回溯：
```
#0  0x00007fcce9b2faf3 in __epoll_wait_nocancel () at ../sysdeps/unix/syscall-template.S:81
#1  0x0000000000435ef8 in pyepoll_poll (self=0x7fccdf54f240, args=<optimized out>, kwds=<optimized out>) at ../Modules/selectmodule.c:1034
#2  0x000000000049968d in call_function (oparg=<optimized out>, pp_stack=0x7ffc20d7bfb0) at ../Python/ceval.c:4020
#3  PyEval_EvalFrameEx () at ../Python/ceval.c:2666
#4  0x0000000000499ef2 in fast_function () at ../Python/ceval.c:4106
#5  call_function () at ../Python/ceval.c:4041
#6  PyEval_EvalFrameEx () at ../Python/ceval.c:2666
```

以及一个通过`traceback.extract_stack()`工具获得的:
```
/usr/local/lib/python2.7/dist-packages/eventlet/greenpool.py:82 in _spawn_n_impl
    `func(*args, **kwargs)`

/opt/stack/neutron/neutron/agent/l3/agent.py:461 in _process_router_update
    `for rp, update in self._queue.each_update_to_next_router():`

/opt/stack/neutron/neutron/agent/l3/router_processing_queue.py:154 in each_update_to_next_router
    `next_update = self._queue.get()`

/usr/local/lib/python2.7/dist-packages/eventlet/queue.py:313 in get
    `return waiter.wait()`

/usr/local/lib/python2.7/dist-packages/eventlet/queue.py:141 in wait
   `return get_hub().switch()`

/usr/local/lib/python2.7/dist-packages/eventlet/hubs/hub.py:294 in switch
    `return self.greenlet.switch()`
```

照这样看，当你尝试找到你的Python代码中的错误时，前者没啥帮助，而你所看到的是解释器本身的当前状态。

然而，[PyEval_EvalFrameEx](https://docs.python.org/2/c-api/veryhigh.html#c.PyEval_EvalFrameEx)看起来很有趣：这是CPython的一个函数，它执行Python应用级别的函数的字节码，因此，可以访问它们的状态 —— 那个我们通常感兴趣的非常态。

## gdb和Python

`"gdb debug python"`的搜索结果可能会造成混淆。问题是，从`gdb`的版本7开始，使用Python代码[扩展](https://sourceware.org/gdb/current/onlinedocs/gdb/Python.html#Python)编译器成为了可能，例如，为了提供C++ [STL](https://sourceware.org/gdb/wiki/STLSupport)类型的可视化，这用Python实现比在内置的[macro](http://www.ibm.com/developerworks/aix/library/au-gdb.html)语言实现容易得多。

为了能够调试CPython进程以及内省应用级别的状态，解释器开发者决定扩展`gdb`，为此编写一个[script](https://github.com/python/cpython/blob/master/Tools/gdb/libpython.py)，当然，是用Python！

所有有两种不同，但是相关的事：

*   `gdb`版本7+ 是可以用Python模块扩展的
*   `gdb`有一个用于CPython进程调试的Python扩展

## 使用gdb 101调试Python

首先，你需要安装`gdb`：
```sh
# apt-get install gdb
```

或者
```sh
# yum install gdb
```

根据你正在使用的Linux不同发行版本。

下一步是为你的CPython安装[调试符号](http://www.tutorialspoint.com/gnu_debugger/gdb_debugging_symbols.htm) ：

`# apt-get install python-dbg`

或者

`# yum install python-debuginfo`

一些Linux发行版本，例如CentOS或者RHEL[分别](http://debuginfo.centos.org/)从所有其他包自带了调试符号[separately](http://debuginfo.centos.org/)，推荐像这样安装那些：

`# debuginfo-install python`

为了分析`PyEval_EvalFrameEx`帧 (一个帧实际上是一个函数调用，以及以局部变量和CPU寄存器等形式的关联状态)，并把它们映射到你的代码中的应用级别的函数，安装的调试符号将被CPython[脚本](https://github.com/python/cpython/blob/master/Tools/gdb/libpython.py)用于`gdb`。

没有调试符号，就会比较难了 - `gdb`允许你以任何你想要的方式操纵进程内存，但是你不能很容易地理解什么数据结构驻留在哪个内存区域。

在所有准备步骤已经完成之后，你可以试一试`gdb`。例如，为了连接到一个运行着的CPython进程上，这样在：
```sh
gdb /usr/bin/python -p $PID
```

此时，你可以获得当前线程的应用级别的回溯 (注意，一些帧“缺失”了 - 这是意料之中的，因为`gdb`计算所有解释器级别的帧，而它们之中只有一些实在应用级别代码调用 - `PyEval_EvalFrameEx`):
```
(gdb) py-bt

#4 Frame 0x1b7da60, for file /usr/lib/python2.7/sched.py, line 111, in run (self=<scheduler(timefunc=<built-in function time>, delayfunc=<built-in function sleep>, _queue=[<Event at remote 0x7fe1f8c74a10>]) at remote 0x7fe1fa086758>, q=[...], delayfunc=<built-in function sleep>, timefunc=<built-in function time>, pop=<built-in function heappop>, time=<float at remote 0x1a0a400>, priority=1, action=<function at remote 0x7fe1fa083aa0>, argument=(171657,), checked_event=<...>, now=<float at remote 0x1b8ec58>)
    delayfunc(time - now)
#7 Frame 0x1b87e90, for file /usr/bin/dstat, line 2416, in main (interval=1, user='ubuntu', hostname='rpodolyaka-devstack', key='unit_hi', linewidth=150, plugin='page', mods=('page', 'page24'), mod='page', pluginfile='dstat_page', scheduler=<scheduler(timefunc=<built-in function time>, delayfunc=<built-in function sleep>, _queue=[<Event at remote 0x7fe1f8c74a10>]) at remote 0x7fe1fa086758>)
    scheduler.run()
#11 Frame 0x7fe1fa0bc5c0, for file /usr/bin/dstat, line 2554, in <module> ()
    main()
```

或者找出当前正在执行的应用代码的确切行：
```
(gdb) py-list

 106            pop = heapq.heappop
 107            while q:
 108                time, priority, action, argument = checked_event = q[0]
 109                now = timefunc()
 110                if now < time:
>111                    delayfunc(time - now)
 112                else:
 113                    event = pop(q)
 114                    # Verify that the event was not removed or altered
 115                    # by another thread after we last looked at q[0].
 116                    if event is checked_event:
```

或者看看局部变量和它们的值：
```
(gdb) py-locals

self = <scheduler(timefunc=<built-in function time>, delayfunc=<built-in function sleep>, _queue=[<Event at remote 0x7fe1f8c74a10>]) at remote 0x7fe1fa086758>
q = [<Event at remote 0x7fe1f8c74a10>]
delayfunc = <built-in function sleep>
timefunc = <built-in function time>
pop = <built-in function heappop>
time = <float at remote 0x1a0a400>
priority = 1
action = <function at remote 0x7fe1fa083aa0>
argument = (171657,)
checked_event = <Event at remote 0x7fe1f8c74a10>
now = <float at remote 0x1b8ec58>
```

CPython[脚本](https://github.com/python/cpython/blob/master/Tools/gdb/libpython.py)还为`gdb`提供了更多的`py-`命令。看看调试[指南](https://docs.python.org/devguide/gdb.html)以获得详细信息。

## 陷阱

虽然所描述的技术应该开箱即用，但还有一些已知的陷阱。

## python-dbg

Debian和Ubuntu上的`python-dbg`包将不只为`python`安装调试符号 (这在变异的时候被去掉以节省磁盘空间)，还提供一个额外的CPython库`python-dbg`。

后者本质上市带有许多运行时检查的CPython的单独构建(传递`--with-debug`给`./configure`)。通常情况下，你不希望在生产环境上使用`python-dbg`，因为它可比`python`慢（得多），例如：
```sh
$ time python -c "print(sum(range(1, 1000000)))"
499999500000

real    0m0.096s
user    0m0.057s
sys 0m0.030s

$ time python-dbg -c "print(sum(range(1, 1000000)))"
499999500000
[18318 refs]

real    0m0.237s
user    0m0.197s
sys 0m0.016s
```

好处是，你不需要：它仍然可以使用`gdb`工具调试`python`可执行文件，只要安装相应的调试符号。所以`python-dbg`只是给CPython/gdb增加了更多的混乱 —— 你可以放心地忽略它的存在。

## 构建标志

一些Linux发行版本将`-g0`或者`-g1`[选项](https://gcc.gnu.org/onlinedocs/gcc/Debugging-Options.html)传递给`gcc`来构建CPython：前者在完全不需要调试信息的情况下生成了一个二进制文件，而后者不允许`gdb`在运行时获得局部变量的信息。

这些选项都打破了使用`gdb`工具调试CPython进程的所述工作流。解决方法是使用`-g`或者`-g2`(当传递`-g`时，2是默认值) 选项重新构建CPython。

幸运的是，主要的Linux发行版本(Ubuntu Trusty,
Debian Jessie, CentOS/RHEL 7)的所有当前版本都自带了“正确的”已构建CPython。

## 优化帧

要让内省正常工作，为每一次调用保存关于`PyEval_EvalFrameEx`参数的信息至关重要。取决于当构建CPython或者所使用的具体编译器版本时，在`gcc`中使用的[优化级别](https://gcc.gnu.org/onlinedocs/gcc/Optimize-Options.html)，在运行时这个信息有可能丢失 (特别是使用`-O3`启用的积极优化)。在这种情况下，`gdb`会给你显示这些东东：
```
(gdb) bt

#0  0x00007fdf3ca31be3 in __select_nocancel () at ../sysdeps/unix/syscall-template.S:84
#1  0x00000000005d1da4 in pysleep (secs=<optimized out>) at ../Modules/timemodule.c:1408
#2  time_sleep () at ../Modules/timemodule.c:231
#3  0x00000000004f5465 in call_function (oparg=<optimized out>, pp_stack=0x7fff62b184c0) at ../Python/ceval.c:4637
#4  PyEval_EvalFrameEx () at ../Python/ceval.c:3185
#5  0x00000000004f5194 in fast_function (nk=<optimized out>, na=<optimized out>, n=<optimized out>, pp_stack=0x7fff62b185c0, 
    func=<optimized out>) at ../Python/ceval.c:4750
#6  call_function (oparg=<optimized out>, pp_stack=0x7fff62b185c0) at ../Python/ceval.c:4677
#7  PyEval_EvalFrameEx () at ../Python/ceval.c:3185
#8  0x00000000004f5194 in fast_function (nk=<optimized out>, na=<optimized out>, n=<optimized out>, pp_stack=0x7fff62b186c0, 
    func=<optimized out>) at ../Python/ceval.c:4750
#9  call_function (oparg=<optimized out>, pp_stack=0x7fff62b186c0) at ../Python/ceval.c:4677
#10 PyEval_EvalFrameEx () at ../Python/ceval.c:3185
#11 0x00000000005c5da8 in _PyEval_EvalCodeWithName.lto_priv.1326 () at ../Python/ceval.c:3965
#12 0x00000000005e9d7f in PyEval_EvalCodeEx () at ../Python/ceval.c:3986
#13 PyEval_EvalCode (co=<optimized out>, globals=<optimized out>, locals=<optimized out>) at ../Python/ceval.c:777
#14 0x00000000005fe3d2 in run_mod () at ../Python/pythonrun.c:970
#15 0x000000000060057a in PyRun_FileExFlags () at ../Python/pythonrun.c:923
#16 0x000000000060075c in PyRun_SimpleFileExFlags () at ../Python/pythonrun.c:396
#17 0x000000000062b870 in run_file (p_cf=0x7fff62b18920, filename=0x1733260 L"test2.py", fp=0x1790190) at ../Modules/main.c:318
#18 Py_Main () at ../Modules/main.c:768
#19 0x00000000004cb8ef in main () at ../Programs/python.c:69
#20 0x00007fdf3c970610 in __libc_start_main (main=0x4cb810 <main>, argc=2, argv=0x7fff62b18b38, init=<optimized out>, fini=<optimized out>, 
    rtld_fini=<optimized out>, stack_end=0x7fff62b18b28) at libc-start.c:291
#21 0x00000000005c9df9 in _start ()

(gdb) py-bt
Traceback (most recent call first):
  File "test2.py", line 9, in g
    time.sleep(1000)
  File "test2.py", line 5, in f
    g()
  (frame information optimized out)
```

即，某些应用级别的帧将是可用的，而某些不可用。在这一点上，你基本没啥可做的，除了以一种较低的优化级别来重新构建CPython，但这样通常不适用于生产（别说你回使用自定义的CPython构建，而不是你的Linux发行版提供的CPython构建）。

## 虚拟环境和自定义的CPython构建

当使用一个虚拟环境时，可能会出现`gdb`扩展不能工作的情况：
```
(gdb) bt

#0  0x00007ff2df3d0be3 in __select_nocancel () at ../sysdeps/unix/syscall-template.S:84
#1  0x0000000000588c4a in ?? ()
#2  0x00000000004bad9a in PyEval_EvalFrameEx ()
#3  0x00000000004bfd1f in PyEval_EvalFrameEx ()
#4  0x00000000004bfd1f in PyEval_EvalFrameEx ()
#5  0x00000000004b8556 in PyEval_EvalCodeEx ()
#6  0x00000000004e91ef in ?? ()
#7  0x00000000004e3d92 in PyRun_FileExFlags ()
#8  0x00000000004e2646 in PyRun_SimpleFileExFlags ()
#9  0x0000000000491c23 in Py_Main ()
#10 0x00007ff2df30f610 in __libc_start_main (main=0x491670 <main>, argc=2, argv=0x7ffc36f11cf8, init=<optimized out>, fini=<optimized out>, 
    rtld_fini=<optimized out>, stack_end=0x7ffc36f11ce8) at libc-start.c:291
#11 0x000000000049159b in _start ()

(gdb) py-bt

Undefined command: "py-bt".  Try "help".
```

`gdb`仍然可以遵循CPython帧框架，但是在`PyEval_EvalCodeEx`调用上的信息就不可用了。

如果你向上滚动一下`gdb`的输出，那么你会看到`gdb`没有为`python`可执行文件找到调试符号：
```sh
$ gdb -p 2975

GNU gdb (Debian 7.10-1+b1) 7.10
Copyright (C) 2015 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word".
Attaching to process 2975
Reading symbols from /home/rpodolyaka/workspace/venvs/default/bin/python2...(no debugging symbols found)...done.
```

一个虚拟环境有啥不同呢？为嘛`gdb`找不到调试符号？

首先，`python`可执行文件的路径不同。注意，当连接到该进程时，我没有指定可执行文件。在这种情况下，`gdb`将采用该进程的可执行文件 (例如，在Linux上的 `/proc/$PID/exe`值)。

[分开](https://sourceware.org/gdb/onlinedocs/gdb/Separate-Debug-Files.html)调试符号的一种方法是将它们放到一个知名的目录 (默认是，`/usr/lib/debug/`，虽然它是通过`gdb`中的`debug-file-directory`选项来配置的)。在我们的例子中，`gdb`试图从`/usr/lib/debug/home/rpodolyaka/workspace/venvs/default/bin/python2`加载调试符号，而且显然在那里没找到任何东东。

解决方法是简单的 —— 当运行`gdb`时，在调试下明确指定可执行文件：
```sh
$ gdb /usr/bin/python2.7 -p $PID
```

因此，`gdb`会在“正确的”地方查找调试符号 —— `/usr/lib/debug/usr/bin/python2.7`。

另外，值得一提的是，有可能一个特定的可执行文件的调试符号由存储在[ELF](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format)可执行头中的唯一的`build-id`所标识。例如，在我的Debian机器上的CPython：
```sh
$ objdump -s -j .note.gnu.build-id /usr/bin/python2.7

/usr/bin/python2.7:     file format elf64-x86-64

Contents of section .note.gnu.build-id:
 400274 04000000 14000000 03000000 474e5500  ............GNU.
 400284 8d04a3ae 38521cb7 c7928e4a 7c8b1ed3  ....8R.....J|...
 400294 85e763e4
```

在这个例子中，`gdb`会使用该`build-id`值来查找调试符号：
```sh
$ gdb /usr/bin/python2.7

GNU gdb (Debian 7.10-1+b1) 7.10
Copyright (C) 2015 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from /usr/bin/python2.7...Reading symbols from /usr/lib/debug/.build-id/8d/04a3ae38521cb7c7928e4a7c8b1ed385e763e4.debug...done.
done.
```

这有一个很好的暗示 —— 怎样调用可执行文件不再是问题：`virtualenv`刚创建了指定的解释器可执行文件的一个副本，因此，这两个可执行文件 —— 一个在`/usr/bin/`，和一个在你的虚拟环境中 —— 将使用同样的调试符号：
```sh
$ gdb -p 11150

GNU gdb (ebian 7.10-1+b1) 7.10
Copyright () 2015 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "how copying"
and "how warranty" for details.
This GDB was configured as "86_64-linux-gnu".
Type "how configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "elp".
Type "propos word" to search for commands related to "ord".
Attaching to process 11150
Reading symbols from /home/rpodolyaka/sandbox/testvenv/bin/python2.7...Reading symbols from
/usr/lib/debug/.build-id/8d/04a3ae38521cb7c7928e4a7c8b1ed385e763e4.debug...done.

$ ls -la /proc/11150/exe
lrwxrwxrwx 1 rpodolyaka rpodolyaka 0 Apr 10 15:18 /proc/11150/exe -> /home/rpodolyaka/sandbox/testvenv/bin/python2.7
```

第一个问题解决了，`bt`输出现在看起来好多了，但是`py-bt`命令仍然未定义：
```sh
(gdb) bt

#0  0x00007f3e95083be3 in __select_nocancel () at ../sysdeps/unix/syscall-template.S:84
#1  0x0000000000594a59 in floatsleep (secs=<optimized out>) at ../Modules/timemodule.c:948
#2  time_sleep.lto_priv () at ../Modules/timemodule.c:206
#3  0x00000000004c524a in call_function (oparg=<optimized out>, pp_stack=0x7ffefb5045b0) at ../Python/ceval.c:4350
#4  PyEval_EvalFrameEx () at ../Python/ceval.c:2987
#5  0x00000000004ca95f in fast_function (nk=<optimized out>, na=<optimized out>, n=<optimized out>, pp_stack=0x7ffefb504700, 
    func=0x7f3e95f78c80) at ../Python/ceval.c:4435
#6  call_function (oparg=<optimized out>, pp_stack=0x7ffefb504700) at ../Python/ceval.c:4370
#7  PyEval_EvalFrameEx () at ../Python/ceval.c:2987
#8  0x00000000004ca95f in fast_function (nk=<optimized out>, na=<optimized out>, n=<optimized out>, pp_stack=0x7ffefb504850, 
    func=0x7f3e95f78c08) at ../Python/ceval.c:4435
#9  call_function (oparg=<optimized out>, pp_stack=0x7ffefb504850) at ../Python/ceval.c:4370
#10 PyEval_EvalFrameEx () at ../Python/ceval.c:2987
#11 0x00000000004c32e5 in PyEval_EvalCodeEx () at ../Python/ceval.c:3582
#12 0x00000000004c3089 in PyEval_EvalCode (co=<optimized out>, globals=<optimized out>, locals=<optimized out>) at ../Python/ceval.c:669
#13 0x00000000004f263f in run_mod.lto_priv () at ../Python/pythonrun.c:1376
#14 0x00000000004ecf52 in PyRun_FileExFlags () at ../Python/pythonrun.c:1362
#15 0x00000000004eb6d1 in PyRun_SimpleFileExFlags () at ../Python/pythonrun.c:948
#16 0x000000000049e2d8 in Py_Main () at ../Modules/main.c:640
#17 0x00007f3e94fc2610 in __libc_start_main (main=0x49dc00 <main>, argc=2, argv=0x7ffefb504c98, init=<optimized out>, fini=<optimized out>, 
    rtld_fini=<optimized out>, stack_end=0x7ffefb504c88) at libc-start.c:291
#18 0x000000000049db29 in _start ()

(gdb) py-bt

Undefined command: "py-bt".  Try "help".
```

再次，这是由在一个虚拟环境中的`python`二进制有不同的路径这个事实所引起的。默认，`gdb`会尝试在调试的情况下，为特定的对象文件[自动加载](https://sourceware.org/gdb/onlinedocs/gdb/Python-Auto_002dloading.html#set%20auto%2dload%20python%2dscripts)Python扩展，如果它们存在的话。具体来说，`gdb`会查找`objfile-gdb.py`，并尝试在开始的时候`source`它：
```
(gdb) info auto-load

gdb-scripts:  No auto-load scripts.
libthread-db:  No auto-loaded libthread-db.
local-gdbinit:  Local .gdbinit file was not found.
python-scripts:
Loaded  Script
Yes     /usr/share/gdb/auto-load/usr/bin/python2.7-gdb.py
```

如果，处于某些语言，这还没有完成，那么你可以总是手工进行：
`(gdb) source /usr/share/gdb/auto-load/usr/bin/python2.7-gdb.py`

例如，如果你想要测试CPython自带的`gdb`扩展的一个新版本。

## PyPy, Jython, 等等

所描述的调试技术唯一对CPython解释器可行，因为`gdb`扩展是专门写于内省CPython内部状态（例如，`PyEval_EvalFrameEx`调用）的。

对于[PyPy](http://pypy.org/)，在Bitbucket上有一个未决[问题](https://bitbucket.org/pypy/pypy/issues/1204/gdb-hooks-for-debugging-pypy)，其中，有人提议为用户提供对`gdb`的整合，不过貌似附带的补丁尚未合并，而且写这些的那个人对此失去了兴趣。

对于[Jython](http://www.jython.org/)，你可以使用用于调试`JVM`应用的标准工具，例如，[VisualVM](http://visualvm.java.net/).

## 总结

`gdb`是一个强大的工具，它允许你调试崩溃或夯住的CPython进程，以及那些确实调用了原生库的Python代码的复杂问题。在现代的Linux发行版本中，使用`gdb`调试CPython进程必然是与安装用于具体的解释器版本的调试符号一样简单，虽然有一些已知的陷阱，尤其是当使用虚拟机时。
