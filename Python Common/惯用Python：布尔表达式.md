原文：[Idiomatic Python: boolean expressions](https://blogs.msdn.microsoft.com/pythonengineering/2016/04/18/idiomatic-python-boolean-expressions/)

---

你可能会认为布尔表达式 —— 最常用于条件判读，是一点测试`if`或者`while`语句是否应该执行的代码 —— 是一个相当直接的概念，而且它们确实完全没有什么微妙之处。虽然一般概念是很简单的，但是在写布尔表达式时，有一些要遵循的惯用做法。

首先，要确保每个人都明白，是什么让某个东西被视为true或者false （有时也被称为是“真实”与否）。Python 3中，官方对[什么是true或者false的定义](https://docs.python.org/3/reference/expressions.html#booleans)是：

> `False`, `None`, 所有类型的数字0, 以及空字符串和空容器 (包括字符串(string), 元组(tuple), 列表(list), 字典(dictionary), 集合(set)和frozenset)。所有其他值则被解析为true。通过提供一个`__bool__()`方法，用户定义的对象可以自定义它们的真值。

Python的一点历史：在讨论[在Python 2.3中添加布尔类型](https://docs.python.org/3/whatsnew/2.3.html#pep-285-a-boolean-type)期间，对于什么是false的定义从“所有表示空的东西”变为“所有表示空的东西**和**`False`”，有些人并不喜欢，他们觉得这样有失简单性。而另一些人则认为，`False`会让代码更加清晰。结果，支持`False`概念的人更多，因此，后者赢得了这场争论。你也许也注意到了，在Python中，布尔不不是_那么_老，这就是为什么布尔可以（大部分）被当成整型，因为它能与那些简单的使用`1`和`0`来分别表示`True`和`False`的代码向后兼容。

建议的第一条就是不要过分的使用`is`比较。`is`用于[标识比较(identity comparison)](https://docs.python.org/3/reference/expressions.html#is-not)，只有当表达式中的两个对象都表示同个对象的时候，它的计算结果才是`True`。不幸的是，人们总是会把标识比较和值比较混为一谈。例如，有些人意外地发现，出于对性能的考量，Python的某些实现中缓存了特定的值，导致像这样的表达式：

```python
40 + 2 is 42  # True in CPython, not necessarily in other VMs.
```

为真。但是，对数字的缓存并不是Python语言定义的一部分，它只是实现细节中一个古怪的副作用。这是一个问题，如果你想要改变Python的实现，或者碰巧认为对任何数字使用`is`都有效，这显然不对，看看当你试图这样做：

```python
2**32 is 2**32 # False.
```

结果会是`False`。换句话说，只有在你真的**真的**想要测试标识而不是值时，才使用`is`。

另一个我们看到`is`以一种非惯用方式使用的地方是直接测试`True`或者`False`，例如：

```python
something() is False  # Too restrictive.
```

这在技术上并不像前面的例子那样是错误的，因为`False`是一个单子 —— 就像`None`和`True`一样 —— 这意味着，实际上`False`只有一个实例可以用来比较。但是这样做的问题在于，它带来了不必要的限制。幸好Python是[鸭子类型(duck typing)](https://en.wikipedia.org/wiki/Duck_typing)的庞大的支持者，不赞成将任何API的类型指定为`True`或者`False`，因为它将一个API锁定为指定的类型。假若出于某些原因，该API变成返回了另一种类型的值，但具有相同的布尔解释，那么，这个代码会突然崩溃。取代直接检查`False`的是，该代码应该简单检查假值：

```python
not something()  # Just right.
```

然后，这也可以扩展到其他类型。如果你关心某样东西是否是空的，那么不要`spam == []`，而是`not spam`，以防万一那个返回`spam`值的API突然开始返回元祖，而不是列表。

而你在日复一日的代码中唯一可能合理使用`is`的时候是，和`None`一起使用。有时，你可能会碰到一个API，其中，`None`具有特殊含义，在这种情况下，你应该使用`is None`来检查那个特殊值。例如，Python中的模块具有一个[`__package__`属性](https://docs.python.org/3/reference/import.html#__package__)，它存储了一个表示该模块属于哪个包的字符串。诀窍是，顶层模块，例如，不被包含在一个包中的那些模块，它们的`__package__`值为空字符串，这是假的，但却是一个有效值，但需要有一个值表示不知道`__package__`应该被设为什么值。在这种情况下，`None`被用来表示“我不知道”。这允许[计算一个模块属于哪个包的代码](https://github.com/python/cpython/blob/d0ffca8d6aa055300f7361e4bea2d4def0fca571/Lib/importlib/_bootstrap.py#L1031)使用：

```python
package is None  # OK when you need an "I don't know" value.
```

来检查是否包名未知 (`not package`会错误的认为`''`也表示这个意思)。但请务必确保不滥用`None`的这种用法，因为false值趋向于满足表示“我不知道”的需要。

另一点建议是，在我们自己的类中定义`__bool__()`之前三思而后行。虽然你应该明确的定义表示容器的类的方法（以助于“若为假，则为空”的概念），但是在任何情况下，你都应该停下来想想定义该方法是否真的有意义。虽然使用`__bool__()`来表示一个对象的某种状态可能是很诱人的，但是后果会出奇的影响深远，因为它意味着，突然让人不得不开始明确检查一些特殊值，例如`None`，这表示一个API是否返回一个实际值，而不是简单的依赖着所有的对象默认都为值这种条件。作为定义`__bool__()`为嘛可能是令人惊讶的一个例子，见[Python issue](https://bugs.python.org/issue13936)，这里有一个多年的讨论，该讨论关于怎样定义`datetime.time()`在午夜的时候为false，其他时候都为true是错误的，以及最好如何修复这个错误 (最后， [在Python 3.5中移除](https://docs.python.org/3/whatsnew/3.5.html#changes-in-the-python-api)了`__bool__()`的实现)。

在面对一个可能的false值时，如果你发现自己需要提供一个特定的默认值，那么使用`or`将是有帮助的。`and`和`or`都不返回一个特定的布尔值，而是返回第一个确定为true值的值。对于`or`，如果它的第一个值是true，则返回第一个值，否则返回第二个值，无论第二个值是什么。这意味着，如果像这样：

```python
# Use the value from something() if it is true, else default to None.
spam = something() or None
```

那么，如果`something()`是true，那么`spam`将获得`something()`的返回值，否则将会被设为`None`。并且，因为`and`和`or`都是短路的，所以你可以将其与一些对象实例组合在一起，并知道除非必要，否则不会发生；

```python
# Only execute AnotherThing() if something() returns a false value.
spam = something() or AnotherThing()
```

不会执行`AnotherThing()`，除非`something()`返回一个false值。

最后，在可能的时候，确保使用[`any()`](https://docs.python.org/3/library/functions.html#any)和[`all()`](https://docs.python.org/3/library/functions.html#all)。这些内建函数在需要的时候是非常方便的，而将它们与生成器表达式组合使用，则是相当强大的。