原文：[hasattr() Considered Harmful](https://hynek.me/articles/hasattr/)

---
不要使用Python的`hasattr()`，除非你想要它奇奇怪怪，或者你正编写纯Python 3代码。

这是Python最常见的讨论之一，因此，不是不断的为每一个提供依据，而是一劳永逸：

不要：
```python
if hasattr(x, "y"):
    print(x.y)
else:
    print("no y!")
```
而是要这样做：
```python
try:
    print(x.y)
except AttributeError:
    print("no y!")
```
或者：
```python
y = getattr(x, "y", None)
if y is not None:
    print(y)
else:
    print("no y!")
```
特别是在你处理不属于自己的类时。

`hasattr()`并不比`getattr()`快，因为它经历完全相同的查找过程，然后丢掉结果。

## 为什么？

它看起来似乎差不多，而额外的行令人感到厌烦，但是在Python 2上使用接近于这样写：
```python
try:
    print(x.y)
except:
    print("no y!")
```
这几乎不会是你想要的，因为它隐藏了属性(property)中的错误！

例如：
```python
>>> class C(object):
...     @property
...     def y(self):
...         0/0
...
>>> hasattr(C(), "y")
False
```
由于在第三方类中，你不能知道一个属性是不是一个属性(property)（或者在数月或数年后更新为一个属性），因此这是危险的。

你觉得这没啥大不了，是吗？看看这个闪闪发光的[例子](https://news.ycombinator.com/item?id=10893431)！另一个明显的影响是在属性上使用`hasattr()`并不执行它们的getter函数，这使得名字具有误导性。

对于它的价值，Python 3 使其恢复正常：
```python
>>> class C:
...     @property
...     def y(self):
...         0/0
...
>>> hasattr(C(), "y")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 4, in y
ZeroDivisionError: division by zero
```
这使得当同时为Python 2 和 Python 3 编写混合代码时，它成为了一种边缘情况。但是，你会预期`hasattr()`抛出该错误吗？

---
细心的读者可能会问，`AttributeErrors`呢？事实上，没有办法区分一个`AttributeError`是由于属性缺失引起的，还是由一个错误的属性引起的。The outlined approaches reduce your possible errors to only that one and avoid confusing differences in behavior between Python 2 and 3.

当然，对于自己的代码，依然可以使用`hasattr()`。但你必须跟踪它，并在你的类发生改变时记得解决它。这一切都增加了不必要的精神负担，而目的只在于节省一些打字。

附注：是滴，标题很烂。因为我已经写了这篇文章，所以可以在讨论的时候向人们展示它。我没想到它会在Hacker News上排行第一，并且我同意“被认为是有害的”这种说法正在过时。只是，我并没有花费太多时间来担心，而是在起床和去上班之间进行处理；它只是突然出现在我的脑海里的第一件事。感谢您的理解。未来可能会修改它，反正这个主题是中立的。

再附注：有些人对于我敢匹配Python 3中一个很好用的函数感到很愤怒。因为没人应该写Python 2了。虽然我并不是一个讨厌Python 3的Armin，但必须承认，现今大多数的Python代码不是Python 2就是混合的。而对于一些人提及的许多混合库，我发现Python 2和3之间的行为差异甚至比Python 2自身的行为还要糟糕，而这会使人措手不及。所以，冷静下来，喝杯可可，然后移植一些一些库。

## 补充说明

1. `getattr()`例子假设属性的确实等价于其值为`None`（这是常见的）。如果你想要区别那两种情况，使用一个警戒值。
2. 至少在[CPython](https://hg.python.org/cpython/file/77d24f51effc/Python/bltinmodule.c#l1051)中。
