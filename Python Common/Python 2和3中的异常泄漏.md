原文：[Exception leaks in Python 2 and 3](http://cosmicpercolator.com/2016/01/13/exception-leaks-in-python-2-and-3/)

---
最近，我觉得移植一个小包到Python 3，但遭遇了traceback循环引用问题。这篇博客是我不得不做的检测工作的结果，它既能让自己重新熟悉这个问题（已经没做这类事情好几年了），又能发现Python 3的具体行为。

## 背景

在Python 2中，异常作为三种独立的对象进行内部存储：类型，值和traceback对象。值一般是Python代码运行时的类型的一个实例，因此，大部分时间，我们都只是在处理值和traceback。当编写异常处理代码时，应该注意到两个陷阱。

### traceback循环问题

通常情况下，并不用担心traceback对象。你这样编写代码：
```python
def foo():
    try:
        return bar()
    except Exception as e:
        print "got this here", e
```
当你想要对traceback做些什么时，麻烦来了。可能是要记录它，或者是将异常转换成一些别的东西：
```python
def foo():
    try:
        return bar()
    except Exception as e:
        type, val, tb = sys.exc_info()
        print "got this here", e, repr(tb)
```
问题是，存储在`tb`中的traceback拥有`foo`执行栈桢的一个引用，这个执行栈桢同时也含有`tb`的定义。这就是循环引用，它意味着，traceback以及它所包含的所有的栈桢将不会立即消失。

这就是“traceback循环引用问题”，对于认真的Python开发者，它应该很熟悉。这是由于traceback包含了一个链接，这个链接指向从捕获异常到它发生的地方的所有的栈桢以及所有的临时变量。(如果启用的话)循环垃圾收集器最后将回收它，但是其发生不可预知，并且时在稍后的某个时间点。随后的内存损耗可能会出问题，当gc最终运行时会出现延迟，或者它可能会在使用依赖于引用计数来检测对象何时消亡的单元测试时导致问题。理想情况下，这些东西在不再需要的时候应该消失。

无论何时traceback出现在一个引发或捕捉异常的框架中，相同的问题也会出现。例如，在被调用函数`translate()`中，这种模式也将引发此问题，因为`tb`出现在它被抛出的的框架中。
```python
def translate(tp, val, tb):
    # translate this into a different exception and re-raise
    raise MyException(str(val)), None, tb
```
在Python 2中，标准的解决方法是在可能的情况下避免检索traceback对象，例如，通过使用

`tp, val = sys.exc_info()[:2]`
或者自己显式地清除它，从而去除循环：
```python
def translate(tp, val, tb):
    # translate this into a different exception and re-raise
    try:
        raise MyException(str(val)), None, tb
    finally:
        del tb
```
>通过积极使用`try-finally`，谨慎的程序员可以避免将引用留给堆栈上的traceback对象。

### 挥之不去的异常问题

一个相关的问题是挥之不去的异常问题。当异常被捕捉并在一个稍后不存在的函数中，例如一个驱动循环，对其进行处理时，它就发生了：
```python
def mainloop():
    while True:
        try:
            do_work()
        except Exception as e:
            report_error(e)
```
这个代码可能看起来很无辜，但是它却有一个问题：最近捕获的异常仍然活在该系统中。这包括了它的traceback，即使代码中已经不再使用它了。即使清除了变量也没有用：
```python
report_error(e)
e = None
```
这是因为Python文档中以下条款：

>如果不存在任何表达式，抛出（重抛出）在当前范围内活跃的最后一个异常。

在Python 2中，只要你不从函数中返回，即使已经退出了`try-except`结构，异常在内部仍然保持活动状态。

对此，Python 2的标准解决方法是使用`sys`模块中的`exc_clear()`函数：
```python
def mainloop():
    while True:
        try:
            do_work()
        except Exception as e:
            report_error(e)
        sys.exc_clear() # clear the internal traceback
```
>谨慎的程序员在他的主循环中遍洒`sys.exc_clear()`。

## Python 3

在Python 3中，有两件事使得事情变得有点不一样。

1. traceback已经与异常对象合为一体
2. sys.exc_clear()已被删除

让我们来看看反过来的影响。

### Exception.__traceback__

虽然将traceback作为一个属性跟异常实例捆绑在一起毫无疑问是有意义的，但是这意味着traceback引用循环会变得更为常见。不再是充分的避免检查`sys.exc_info()`。无论何时你将一个异常对象存储在作为它的traceback的一部分的一个堆栈的一个本地变量中，你都会得到一个循环。这包括了异常被抛出的地方以及其被捕获的地方。

像这样的代码时不可信的：
```python
def catch():    
    try:
        result = bar()
    except Exception as e:
        result = e
    return result
```
变量`result`是`result.__traceback__`指向的堆栈的一部分，并且已创建了一个循环。

(注意，变量`e`并无问题。在Python 3中，这个变量会在退出except语句时自动被清除。)

类似的:
```python
def reraise(tp, value, tb=None):
    if value is None:
        value = tp()
    if value.__traceback__ is not tb:
        raise value.with_traceback(tb)
    raise value
```
(上面的代码取自[six](https://pypi.python.org/pypi/six)模块)

这两种情况都可以通过使用一个位于适当位置的`try-finally`来分别清理变量`result`, `value`和`tb`进行处理：
```python
def catch():    
    try:
        result = bar()
    except Exception as e:
        result = e
    try:
        return result
    finally:
        del result
def reraise(tp, value, tb=None):
    if value is None:
        value = tp()
    try:
        if value.__traceback__ is not tb:
            raise value.with_traceback(tb)
        raise value
    finally:
        del value, tb
```
注意，`reraise()`的调用者也必须清除那些它用作参数的本地变量，因为相同的异常会被重新抛出，并且调用者的堆栈将添加到异常中：
```python
try:
    reraise(*exctuple):
finally:
    del exctuple
```
从中吸取的教训如下：

>不要超过必要时间在本地对象中储存异常。当离开函数时，总是使用`try-finally`清除这些变量。

### sys.exc_clear()

这个方法在Python 3中被移除。因为不再需要它了：
```python
def mainloop():
    while True:
        try:
            do_work()
        except Exception as e:
            report_error(e)
        assert sys.exc_info() == (None, None, None)
```
在调用此函数时，一旦`sys.exc_info()`为空，例如，它不作为异常处理部分被调用，那么在Except语句外，内部异常状态是清除的。

然而，如果你想要保留一个异常一段时间，并且担心循环引用或者内存使用情况，那么你有两个选择：

1. 清除异常的`__traceback__`属性：
  `e.__traceback__ = None`
2. 使用新的`traceback.clear_frames()`函数：
  `traceback.clear_frames(e.__traceback__)`

新增的`clear_frames()`被用来从traceback中移除本地变量，以便减少他们的内存占用。副作用是，它将清除循环引用。

## 总结

当开发健壮的Python时，异常的循环引用是个讨厌鬼。Python 3新增额了一些额外的陷阱。即使异常语句中的局部变量被自动清除，但是用户也必须自己清除那些可能包含异常对象的任何其他变量。
