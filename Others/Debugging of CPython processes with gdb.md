原文：[Debugging of CPython processes with gdb](http://podoliaka.org/2016/04/10/debugging-cpython-gdb/)

---

[pdb](https://docs.python.org/3.5/library/pdb.html) has been, is and probably always will be the bread and butter of Python
programmers, when they need to find the root cause of a problem in their
applications, as it's a built-in and easy to use debugger. But there are cases,
when `pdb` can't help you, e.g. if your app has got stuck somewhere, and you
need to attach to a running process to find out why, without restarting it.
This is where [gdb](https://www.gnu.org/software/gdb/) shines.

## Why gdb?

`gdb` is a general purpose debugger, that is mostly used for debugging of C and
C++ applications (although it actually supports Ada, Objective-C, Pascal and more).

There are multiple reasons why a Python programmer would be interested in `gdb`
for debugging:

*   `gdb` allows you to attach to a running process without starting an app
in debug mode or modifying the app code in some way first (e.g. putting
something like `import rpdb; rpdb.set_trace()` into the code)

*   `gdb` allows you to take a [core dump](https://en.wikipedia.org/wiki/Core_dump) of a process and analyze it later.
This is useful, when you don't want to stop the process for the duration of time,
while you are introspecting its state, as well as when you do [post-mortem](https://en.wikipedia.org/wiki/Debugging#Techniques)
debugging of a process, that has already failed (e.g. [crashed](https://www.freedesktop.org/software/systemd/man/systemd-coredump.html) with a
segmentation fault)

*   most debuggers available for Python (notable exceptions are [winpdb](http://winpdb.org/) and [pydevd](https://github.com/fabioz/PyDev.Debugger))
do not support switching between threads of the application being debugged. `gdb`
allows that, as well as debugging of threads created by non-Python code (e.g. in some
native library used)

## Debugging of interpreted languages

So what makes Python special when using `gdb`?

Unlike programming languages like C or C++, Python code is not compiled into a
native binary for a target platform. Instead there is an interpreter (e.g.
[CPython](https://en.wikipedia.org/wiki/CPython), the reference implementation of Python), which executes compiled
[byte-code](http://security.coverity.com/blog/2014/Nov/understanding-python-bytecode.html).

This effectively means, that when you attach to a Python process with `gdb`,
you'll debug the interpreter instance and introspect the process state at the
interpreter level, not the application level: i.e. you will see functions and
variables of the interpreter, not of your app.

To give you an example, let's take a look at a `gdb` backtrace of a CPython
(the most popular Python interpreter) process:
```
#0  0x00007fcce9b2faf3 in __epoll_wait_nocancel () at ../sysdeps/unix/syscall-template.S:81
#1  0x0000000000435ef8 in pyepoll_poll (self=0x7fccdf54f240, args=<optimized out>, kwds=<optimized out>) at ../Modules/selectmodule.c:1034
#2  0x000000000049968d in call_function (oparg=<optimized out>, pp_stack=0x7ffc20d7bfb0) at ../Python/ceval.c:4020
#3  PyEval_EvalFrameEx () at ../Python/ceval.c:2666
#4  0x0000000000499ef2 in fast_function () at ../Python/ceval.c:4106
#5  call_function () at ../Python/ceval.c:4041
#6  PyEval_EvalFrameEx () at ../Python/ceval.c:2666
```

and one obtained by the means of `traceback.extract_stack()`:
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

As is, the former is of little help, when you are trying to find a problem
in your Python code, and all you see is the current state of the interpreter
itself.

However, [PyEval_EvalFrameEx](https://docs.python.org/2/c-api/veryhigh.html#c.PyEval_EvalFrameEx) looks interesting: it's a function of CPython,
which executes byte-code of Python application level functions and, thus,
has access to their state - the very state we are usually interested in.

## gdb and Python

Search results for `"gdb debug python"` can be confusing. The thing is, that starting
from `gdb` version 7 it's been possible to [extend](https://sourceware.org/gdb/current/onlinedocs/gdb/Python.html#Python) the debugger with Python code, e.g.
in order to provide visualisations for C++ [STL](https://sourceware.org/gdb/wiki/STLSupport) types, which is much easier to implement
in Python rather than in the built-in [macro](http://www.ibm.com/developerworks/aix/library/au-gdb.html) language.

In order to be able to debug CPython processes and introspect the application level state,
the interpreter developers decided to extend `gdb` and wrote a [script](https://github.com/python/cpython/blob/master/Tools/gdb/libpython.py) for that in... Python,
of course!

So it's two different, but related things:

*   `gdb` versions 7+ are extendable with Python modules
*   there's a Python extension for `gdb` for debugging of CPython processes

## Debugging Python with gdb 101

First of all, you need to install `gdb`:
```sh
# apt-get install gdb
```

or
```sh
# yum install gdb
```

depending on the Linux distro you are using.

The next step is to install [debugging symbols](http://www.tutorialspoint.com/gnu_debugger/gdb_debugging_symbols.htm) for the CPython build you have:

`# apt-get install python-dbg`

or

`# yum install python-debuginfo`

Some Linux distros like CentOS or RHEL ship debugging symbols [separately](http://debuginfo.centos.org/) from
all other packages and recommend to install those like:

`# debuginfo-install python`

The installed debugging symbols will be used by the CPython [script](https://github.com/python/cpython/blob/master/Tools/gdb/libpython.py) for `gdb`
in order to analyze the `PyEval_EvalFrameEx` frames (a frame essentially is a
function call and the associated state in a form of local variables and CPU
registers, etc) and map those to application level functions in your code.

Without debugging symbols it's much harder to do - `gdb` allows you to
manipulate the process memory in any way you want, but you can't easily
understand what data structures reside in which memory area.

After all preparatory steps have been completed, you can give `gdb` a try. E.g.
in order to attach to a running CPython process, do:
```sh
gdb /usr/bin/python -p $PID
```

At this point you can get an application level backtrace for the current
thread (note that some frames are "missing" - this is expected, as `gdb`
counts all the interpreter level frames and only some of those are calls
in application level code - `PyEval_EvalFrameEx` ones):
```
(gdb) py-bt

#4 Frame 0x1b7da60, for file /usr/lib/python2.7/sched.py, line 111, in run (self=<scheduler(timefunc=<built-in function time>, delayfunc=<built-in function sleep>, _queue=[<Event at remote 0x7fe1f8c74a10>]) at remote 0x7fe1fa086758>, q=[...], delayfunc=<built-in function sleep>, timefunc=<built-in function time>, pop=<built-in function heappop>, time=<float at remote 0x1a0a400>, priority=1, action=<function at remote 0x7fe1fa083aa0>, argument=(171657,), checked_event=<...>, now=<float at remote 0x1b8ec58>)
    delayfunc(time - now)
#7 Frame 0x1b87e90, for file /usr/bin/dstat, line 2416, in main (interval=1, user='ubuntu', hostname='rpodolyaka-devstack', key='unit_hi', linewidth=150, plugin='page', mods=('page', 'page24'), mod='page', pluginfile='dstat_page', scheduler=<scheduler(timefunc=<built-in function time>, delayfunc=<built-in function sleep>, _queue=[<Event at remote 0x7fe1f8c74a10>]) at remote 0x7fe1fa086758>)
    scheduler.run()
#11 Frame 0x7fe1fa0bc5c0, for file /usr/bin/dstat, line 2554, in <module> ()
    main()
```

or find out what exact line of the application code is currently being executed:
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

or look at local variables and their values:
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

There are more `py-` commands provided by the CPython [script](https://github.com/python/cpython/blob/master/Tools/gdb/libpython.py) for `gdb`.
Check out the debugging [guide](https://docs.python.org/devguide/gdb.html) for details.

## Gotchas

Although the described technique should work out-of-box, there are a few known
gotchas.

## python-dbg

The `python-dbg` package in Debian and Ubuntu will not only install the
debugging symbols for `python` (which are stripped at the package build time
to save disk space), but also provide an additional CPython binary
`python-dbg`.

The latter essentially is a separate build of CPython (with `--with-debug`
passed to `./configure`) with many run-time checks.  Generally, you don't want
to use `python-dbg` in production, as it can be (much) slower than `python`,
e.g.:
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

The good thing is, that you don't need to: it's still possible to debug
`python` executable by the means of `gdb`, as long as the corresponding debugging
symbols are installed.  So `python-dbg` just adds a bit more confusion to the
CPython/gdb story - you can safely ignore its existence.

## Build flags

Some Linux distros build CPython passing the `-g0` or `-g1` [option](https://gcc.gnu.org/onlinedocs/gcc/Debugging-Options.html) to `gcc`:
the former produces a binary without debugging information at all, and the latter
does not allow `gdb` to get information about local variables at runtime.

Both these options break the described workflow of debugging CPython processes
by the means of `gdb`. The solution is to rebuild CPython with `-g` or `-g2`
(2 is the default value when `-g` is passed).

Fortunately, all current versions of the major Linux distros (Ubuntu Trusty,
Debian Jessie, CentOS/RHEL 7) ship the "correctly" built CPython.

## Optimized out frames

For introspection to work properly, it's crucial, that information about
`PyEval_EvalFrameEx` arguments is preserved for each call. Depending on the
[optimization level](https://gcc.gnu.org/onlinedocs/gcc/Optimize-Options.html) used in `gcc` when building CPython or the concrete
compiler version used, it's possible that this information will be lost at
runtime (especially with aggressive optimizations enabled by `-O3`). In this
case `gdb` will show you something like:
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

i.e. some application level frames will be available, some will not.
There is little you can do at this point, except for rebuilding CPython
with a lower optimization level, but that often is not an option for production
(not to mention the fact you'll be using a custom CPython build, not the
one provided by your Linux distro).

## Virtual environments and custom CPython builds

When a virtual environment is used, it may appear that the `gdb` extension does
not work:
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

`gdb` can still follow the CPython frames, but information on `PyEval_EvalCodeEx`
calls is not available.

If you scroll up the `gdb` output a bit, you'll see that `gdb` failed to find
the debugging symbols for `python` executable:
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

How's a virtual environment different? Why did not `gdb` find the debugging symbols?

First and foremost, the path to `python` executable is different. Note, that I
did not specify the executable file, when attaching to the process. In this
case `gdb` will take the executable of the process (i.e. `/proc/$PID/exe`
value on Linux).

One of the ways to [separate](https://sourceware.org/gdb/onlinedocs/gdb/Separate-Debug-Files.html) debugging symbols is to put those into a well-known
directory (default is `/usr/lib/debug/`, although it's configurable via
`debug-file-directory` option in `gdb`). In our case `gdb` tried to load
debugging symbols from `/usr/lib/debug/home/rpodolyaka/workspace/venvs/default/bin/python2` and,
obviously, did not find anything there.

The solution is simple - specify the executable under debug explicitly when
running `gdb`:
```sh
$ gdb /usr/bin/python2.7 -p $PID
```

Thus, `gdb` will look for debugging symbols in the "right" place -
`/usr/lib/debug/usr/bin/python2.7`.

It's also worth mentioning, that it's possible that debugging symbols for a
particular executable are identified by a unique `build-id` value stored
in [ELF](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format) executable headers. E.g. CPython on my Debian machine:
```sh
$ objdump -s -j .note.gnu.build-id /usr/bin/python2.7

/usr/bin/python2.7:     file format elf64-x86-64

Contents of section .note.gnu.build-id:
 400274 04000000 14000000 03000000 474e5500  ............GNU.
 400284 8d04a3ae 38521cb7 c7928e4a 7c8b1ed3  ....8R.....J|...
 400294 85e763e4
```

In this case `gdb` will look for debugging symbols using the `build-id` value:
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

This has a nice implication - it no longer matters how the executable is called:
`virtualenv` just creates a copy of the specified interpreter executable, thus,
both executables - the one in `/usr/bin/` and the one in your virtual environment
will use the very same debugging symbols:
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

The first problem is solved, `bt` output now looks much nicer, but `py-bt` command is still
undefined:
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

Once again, this is caused by the fact that `python` binary in a virtual
environment has a different path. By default, `gdb` will try to [auto-load](https://sourceware.org/gdb/onlinedocs/gdb/Python-Auto_002dloading.html#set%20auto%2dload%20python%2dscripts)
Python extensions for a particular object file under debug, if they exist.
Specifically, `gdb` will look for `objfile-gdb.py` and try to `source` it on
start:
```
(gdb) info auto-load

gdb-scripts:  No auto-load scripts.
libthread-db:  No auto-loaded libthread-db.
local-gdbinit:  Local .gdbinit file was not found.
python-scripts:
Loaded  Script
Yes     /usr/share/gdb/auto-load/usr/bin/python2.7-gdb.py
```

e.g. if you want to test a new version of the `gdb` extension shipped with CPython.

## PyPy, Jython, etc

The described debugging technique is only feasible for the CPython interpreter
as is, as the `gdb` extension is specifically written to introspect the state
of CPython internals (e.g. `PyEval_EvalFrameEx` calls).

For [PyPy](http://pypy.org/) there is an open [issue](https://bitbucket.org/pypy/pypy/issues/1204/gdb-hooks-for-debugging-pypy) on Bitbucket, where it was proposed to
provide integration with `gdb`, but looks like the attached patches have not
been merged yet and the person, who wrote those, lost interest in this.

For [Jython](http://www.jython.org/) you could probably use standard tools for debugging of `JVM`
applications, e.g. [VisualVM](http://visualvm.java.net/).

## Conclusion

`gdb` is a powerful tool, that allows one to debug complex problems with
crashing or hanging CPython processes, as well as Python code, that does
calls to native libraries. On modern Linux distros debugging CPython processes
with `gdb` must be as simple as installing of debugging symbols for the
concrete interpreter build, although there are a few known gotchas, especially
when virtual environments are used.
