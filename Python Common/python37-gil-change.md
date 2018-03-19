原文：[How I fixed a very old GIL race condition in Python 3.7](https://vstinner.github.io/python37-gil-change.html "Permalink to How I fixed a very old GIL race condition in Python 3.7" )

---

**我花了 4 年的时间修复了著名的 Python GIL（Global Interpreter Lock，全局解释器锁，Python最关键的部分之一）中的一个令人讨厌的 bug**。为了解决它，我不得不挖出 Git 历史，寻找 **Guido van Rossum** 在 **26 年前做的一个改动**：在那个时候，_线程还是某些深奥难懂的东西_。请听我娓娓道来。

## 由 C 线程和 GIL 引发的致命 Python 错误

2014 年 3 月，**Steve Dower** 报告了这个“C 线程”使用 Python C API 时发生的 bug [bpo-20891](https://bugs.python.org/issue20891)：

> 在 Python 3.4rc3 中，从非 Python 创建，并且没有对 `PyEval_InitThreads()` 进行任何调用的线程中调用 `PyGILState_Ensure()`，将导致一个致命的退出：

>

> `Fatal Python error: take_gil: NULL tstate`

我的第一个评论是：

> IMO it's a bug in `PyEval_InitThreads()`（在我看来，这是一个 `PyEval_InitThreads()` 中的问题）

[![Release the GIL!](https://vstinner.github.io/images/release_the_gil.png)](https://twitter.com/kwinkunks/status/619496450834087938)

## PyGILState_Ensure() 修复

在随后的 2 年中，我忘掉了这个 bug。2016 年 3月，我修改了 Steve 的测试程序，以使其与 Linux 兼容（测试是为 Windows 写的）。我成功地在我的电脑上重现了这个 bug，并且为 `PyGILState_Ensure()` 编写了一个修复代码。

一年后，也就是 2017 年 11 月，**Marcin Kasperski** 问：

> 该修复发布了吗？在更新日志中，我没法找到相关信息……

哎呀，再一次，我彻底地忘记了这个问题！这一次，我不仅 **应用了我的 PyGILState_Ensure() 修复代码**，还编写了**单元测试** `test_embed.test_bpo20891()`：

> 好啦，这个 bug 现在已经在 Python 2.7、3.6 和 master（未来的 3.7）上修复了。在 3.6 和 master 上，该修复自带单元测试。

我的主分支修复，提交 [b4d1e1f7](https://github.com/python/cpython/commit/b4d1e1f7c1af6ae33f0e371576c8bcafedb099db)：

```

    bpo-20891: Fix PyGILState_Ensure() (#4650)
    
    When PyGILState_Ensure() is called in a non-Python thread before
    PyEval_InitThreads(), only call PyEval_InitThreads() after calling
    PyThreadState_New() to fix a crash.
    
    Add an unit test in test_embed.
    
```

然后，我关闭了这个问题 [bpo-20891](https://bugs.python.org/issue20891)……

## 在 macOS 上测试的随机崩溃

一切进展顺利……但在一周后，我注意到我新增加的单元测试在 macOS buildbot 上发生了**随机**崩溃。我成功地手动重现了这个 bug，下面是第三次运行时崩溃的示例：

```

    macbook:master haypo$ while true; do ./Programs/_testembed bpo20891 ||break; date; done
    Lun  4 déc 2017 12:46:34 CET
    Lun  4 déc 2017 12:46:34 CET
    Lun  4 déc 2017 12:46:34 CET
    Fatal Python error: PyEval_SaveThread: NULL tstate
    
    Current thread 0x00007fffa5dff3c0 (most recent call first):
    Abort trap: 6
    
```

macOS 上的 `test_embed.test_bpo20891()` 展现出了 `PyGILState_Ensure()` 中的竞争条件：GIL 锁自身的创建……不受锁保护！增加一个新锁来检查 Python 当前是否有 GIL 锁并没有任何意义……

我为 `PyThread_start_new_thread()` 提出了一个不完整的修复：

> 我发现了一个能用的解决方法：在 `PyThread_start_new_thread()` 中调用 `PyEval_InitThreads()`。这样的话，一旦产生第二个线程，就会立即创建 GIL。当同时运行两个线程时，就不能在创建 GIL 了。至少，使用 `python` 二进制文件。它没法修复线程不是由 Python 生成时产生的问题，但是，这个线程调用了 `PyGILState_Ensure()`。（I found a working fix: call `PyEval_InitThreads()` in `PyThread_start_new_thread()`. So the GIL is created as soon as a second thread is spawned. The GIL cannot be created anymore while two threads are running. At least, with the `python` binary. It doesn't fix the issue if a
thread is not spawned by Python, but this thread calls `PyGILState_Ensure()`.）

## 为什么不总是创建 GIL 呢？

**Antoine Pitrou** 问了一个简单的问题：

> 为什么不_总是_在解释器初始化时调用 `PyEval_InitThreads()` 呢？这样有什么缺点吗？（Why not _always_ call `PyEval_InitThreads()` at interpreter initialization? Are there any downsides?）

感谢 `git blame` 和 `git log`，我发现了“按需”创建 GIL 的代码的起源，**这是一个 26 年前做出的改动**！

```

    commit 1984f1e1c6306d4e8073c28d2395638f80ea509b
    Author: Guido van Rossum <guido@python.org>
    Date:   Tue Aug 4 12:41:02 1992 +0000
    
        * Makefile adapted to changes below.
        * split pythonmain.c in two: most stuff goes to pythonrun.c, in the library.
        * new optional built-in threadmodule.c, build upon Sjoerd's thread.{c,h}.
        * new module from Sjoerd: mmmodule.c (dynamically loaded).
        * new module from Sjoerd: sv (svgen.py, svmodule.c.proto).
        * new files thread.{c,h} (from Sjoerd).
        * new xxmodule.c (example only).
        * myselect.h: bzero -> memset
        * select.c: bzero -> memset; removed global variable
    
    (...)
    
    +void
    +init_save_thread()
    +{
    +#ifdef USE_THREAD
    +       if (interpreter_lock)
    +               fatal("2nd call to init_save_thread");
    +       interpreter_lock = allocate_lock();
    +       acquire_lock(interpreter_lock, 1);
    +#endif
    +}
    +#endif
    
```

我猜，动态创建 GIL 的目的是为了减少只使用单个 Python 线程（永远不会产生新的 Python 线程）的应用的 GIL “开销”。

幸运的是，**Guido van Rossum** 就在旁边，他能够阐明理由：

> 是哒，最初的理由是**线程是某些深奥的东西，大部分代码都没有用它**，并且当时，我们肯定觉得，**总是使用 GIL 会导致（微小的）性能下滑**，并**增加**由于 GIL 代码中的错误**而导致的崩溃的风险**。我很高兴地知道，我们不再需要担心这一点了，并且**可以始终对其进行初始化**。（Yeah, the original reasoning was that **threads were something esoteric and not used by most code**, and at the time we definitely felt that **always using the GIL would cause a (tiny) slowdown** and **increase the risk of crashes** due to bugs in the GIL code. I'd be happy to learn that we no longer need to worry about this and **can just always initialize it**.）

## 提出 Py_Initialize() 的第二个修复版本

我提出了 `Py_Initialize()` 的**第二个修复版本**：在 Python 启动时始终创建 GIL，而不是“按需”创建，以防止任何竞争条件产生的风险：

```

    +    /* Create the GIL */
    +    PyEval_InitThreads();
    
```

**Nick Coghlan** 问，我的补丁是否可以通过性能基准测试。于是，我在我的 [PR 4700](https://github.com/python/cpython/pull/4700/) 上运行了 [pyperformance](http://pyperformance.readthedocs.io/)。有至少 5% 的差异：

```

    haypo@speed-python$ python3 -m perf compare_to \
        2017-12-18_12-29-master-bd6ec4d79e85.json.gz \
        2017-12-18_12-29-master-bd6ec4d79e85-patch-4700.json.gz \
        --table --min-speed=5
    
    +----------------------+--------------------------------------+-------------------------------------------------+
    | Benchmark            | 2017-12-18_12-29-master-bd6ec4d79e85 | 2017-12-18_12-29-master-bd6ec4d79e85-patch-4700 |
    +======================+======================================+=================================================+
    | pathlib              | 41.8 ms                              | 44.3 ms: 1.06x slower (+6%)                     |
    +----------------------+--------------------------------------+-------------------------------------------------+
    | scimark_monte_carlo  | 197 ms                               | 210 ms: 1.07x slower (+7%)                      |
    +----------------------+--------------------------------------+-------------------------------------------------+
    | spectral_norm        | 243 ms                               | 269 ms: 1.11x slower (+11%)                     |
    +----------------------+--------------------------------------+-------------------------------------------------+
    | sqlite_synth         | 7.30 us                              | 8.13 us: 1.11x slower (+11%)                    |
    +----------------------+--------------------------------------+-------------------------------------------------+
    | unpickle_pure_python | 707 us                               | 796 us: 1.13x slower (+13%)                     |
    +----------------------+--------------------------------------+-------------------------------------------------+
    
    Not significant (55): 2to3; chameleon; chaos; (...)
    
```

哎呀，这 5 个基准测试都更慢了。Python 是不欢迎性能衰退的：我们很努力地[再让 Python 更快](https://lwn.net/Articles/725114/)！

## 在圣诞节前跳过失败的测试

我没想到那 5 个基准测试会变得更慢。这需要进一步的研究，但是我没有时间，并且我太羞愧去承担让性能衰退的责任。

在圣诞节前，我没有做出任何决定，而 `test_embed.test_bpo20891()` 在 macOS buildbot 上仍然会随机失败。在两周的假期之前，我**不大情愿去接触 Python 的重要部分**，它的 GIL。所以，我决定跳过 `test_bpo20891()`，直到我回来。

不给你带礼物，Python 3.7。

[![Sad Christmas tree](https://vstinner.github.io/images/sad_christmas_tree.png)](https://drawception.com/panel/drawing/0teL3336/charlie-brown-sad-about-small-christmas-tree/)

## 运行新的基准测试，以及将第二次修复用于主分支

在 2018 年 1 月底，我再次跑了那 5 个在我的 PR 上更慢的基准测试。我在我的笔记本电脑上，使用 CPU 隔离，手动运行了这些基准测试：

```

    vstinner@apu$ python3 -m perf compare_to ref.json patch.json --table
    Not significant (5): unpickle_pure_python; sqlite_synth; spectral_norm; pathlib; scimark_monte_carlo
    
```

好了，根据[Python “性能”基准套件](http://pyperformance.readthedocs.io/)，它证实了我的第二次修复**对性能没有显著的影响**。

于是，我决定**把我的修复推*到主分支上，提交 [2914bb32](https://github.com/python/cpython/commit/2914bb32e2adf8dff77c0ca58b33201bc94e398c)：

```

    bpo-20891: Py_Initialize() now creates the GIL (#4700)
    
    The GIL is no longer created "on demand" to fix a race condition when
    PyGILState_Ensure() is called in a non-Python thread.
    
```

然后，我在主分支上重新启用了 `test_embed.test_bpo20891()`。

## 没有用于 Python 2.7 和 3.6 的第二次修复，抱歉！

**Antoine Pitrou** 认为，[不应该合并](https://github.com/python/cpython/pull/5421#issuecomment-361214537)用于 Python 3.6 的补丁：

> 我不这么认为。人么可能已经调用了 `PyEval_InitThreads()`。（I don't think so. People can already call `PyEval_InitThreads()`.）

**Guido van Rossum** 也不想向后移植这项改动。所以，我只是将 `test_embed.test_bpo20891()` 从 3.6 分支移除。

出于同样的理由，我没有将我的第二次修复应用到 Python 2.7。而且，由于很难向后移植，Python 2.7 没有单元测试。

但是至少，Python 2.7 和 3.6 包含了我的第一次 `PyGILState_Ensure()` 修复。

## 结论

在极端场景下，Python 仍然存在一些竞争条件。当使用 Python API 启动 C 线程时，创建 GIL 会发现这样的错误。我推了第一次修复版本，但是在 macOS 上发现了另一个新的不一样的竞争条件。

我不得不深入 Python GIL 的历史（1992年）。幸运的是，**Guido van Rossum**也能够阐述理由。

在基准测试中出现的一点小问题后，我们同意修改 Python 3.7 为总是创建 GIL，而不是“按需”创建 GIL。这个改动对性能没有显著的影响。

还有让 Python 2.7 和 3.6 保持不变的决定，以防止任何衰退的风险：继续“按需”创建 GIL。

**我花了 4 年的时间解决著名的 Python GIL 中的一个令人讨厌的 bug。** 当触及这类 Python **关键部分**时，我从没有觉得舒服过。现在，很高兴的是，这个错误已经过去了：它完全在未来的 Python 3.7 中修复了！

完整的故事见 [bpo-20891](https://bugs.python.org/issue20891)。感谢所有帮助我解决这个 bug 的开发者！
