原文：[What is Stackless?](http://cosmicpercolator.com/2016/02/02/what-is-stackless/)

---
有时候我会有这个疑问。 而除了开始痛骂 _微线程_, _协程_, _微进程_ 和 _channel_，我从实现中拿出了下面这段重要的代码段：

### 代码:
```c
/*
    帧调度器将执行帧和管理帧堆栈直到“前一个”帧重新出现。
    The "Mario" code if you know that game :-)
 */

PyObject *
slp_frame_dispatch(PyFrameObject *f, PyFrameObject *stopframe, int exc, PyObject *retval)
{
    PyThreadState *ts = PyThreadState_GET();

    ++ts->st.nesting_level;

/*
    帧协议：
    若一个帧返回Py_UnwindToken对象，则意味着将运行一个不同的帧。
    一个Py_UnwindToken出现代表的含义：
    The true return value is in its tempval field.
    We always use the topmost tstate frame and bail
    out when we see the frame that issued the
    originating dispatcher call (which may be a NULL frame).
 */

    while (1) {
        retval = f->f_execute(f, exc, retval);
        if (STACKLESS_UNWINDING(retval))
            STACKLESS_UNPACK(retval);
        /* A soft switch is only complete here */
        Py_CLEAR(ts->st.del_post_switch);
        f = ts->frame;
        if (f == stopframe)
            break;
        exc = 0;
    }
    --ts->st.nesting_level;
    /* see whether we need to trigger a pending interrupt */
    /* note that an interrupt handler guarantees current to exist */
    if (ts->st.interrupt != NULL &&
        ts->st.current->flags.pending_irq)
        slp_check_pending_irq();
    return retval;
}
```

(这个特殊的代码段来自于一个名为[stackless-tealet](https://bitbucket.org/krisvale/stackless-tealet/src/993c788a4b5d3c6bed63e9cbc5bc2df440e37bc2/Stackless/core/stacklesseval.c?at=2.7-slpt)的实验分支, 根据清晰度进行选取)

### 它是什么呢?

它是帧执行代码，是执行Python函数帧的一个顶级循环。一个“帧”是位于一个Python函数内的代码。

### 为什么它很重要呢？

在它较之**C Python**的时候，它是重要的。

普通的**C Python**使用C执行堆栈，它对它解析的Python程序的执行堆栈进行镜像。当一个Python函数**foo()**调用一个Python函数**bar()**的时候，由C函数**PyEval_EvalFrame()**的一个递归调用来触发。这意味着，为了到达C Python程序的特定执行状态，解释器需要位于递归的特定状态。

在**Stackless Python**中，C堆栈会从Python堆栈中尽可能的解耦。下一个要执行的帧位于**ts->frame**中，而在循环中执行帧链。

这会允许两个重要的事情：

1.  一个Stackless的python程序的执行状态可以容易的进行保存和恢复。所需要的仅仅是 _pickle_ 执行帧和其他运行时结构的能力(Stackless补充了pickle的功能)。Python程序的递归状态可以在无需解释器进入相同水平的C递归的情况下进行恢复。
2.  可以以任何顺序执行帧。这允许创建许多微进程以及在它们之间进行切换的代码。如果你愿意的话，还允许微线程。而你如果更喜欢协程的话，也可以使用。但它不强制使用C Python拥有的 _生成器_ 机制（事实上，可以使用这个系统更容易更优雅的实现生成器）。

### 就是这样！

因为C的堆栈已经从Python堆栈上解耦了，所以Stackless Python是 _无堆栈的_。上下文切换成为可能，执行状态的动态管理也成为了可能。

### 好吧，还有更多：

*   堆栈切片：即使C堆栈阻碍了它，它也是一种更聪明的上下文切换的方法
*   微进程和channel探索(原文这里是exploint，觉得应该是exploit)上下文切换执行的一种框架
*   保持一切运行的一个调度器

不幸的是，Stackless Python的开发和支持在过去几年有所放缓。然而，使我感到惊讶的是，无堆栈的核心思想甚至还未被C Python所接纳。
