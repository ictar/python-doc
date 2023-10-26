原文：[Python Gotcha: Comparisons](https://andrewwegner.com/python-gotcha-comparisons.html)

---

# Python 陷阱：比较


## 浮点数等价性比较[¶](#float-equality-comparisons "Permanent link")

与其他所有编程语言一样，Python 也无法准确表示浮点数。我确信许多计算机科学专业的学生已经花费了很多时间来学习[如何表示浮点](https://www.doc.ic.ac.uk/~eedwards/compsys/float/)。那堂课我记得很清楚。

无论如何，让我们讨论一下Python 中比较 `float` 值的问题以及如何处理它。

```py
>>> 0.1 + 0.2 == 0.3
False

```

你、我，以及任何读过几年小学的人都可以看出，这*应该是* `True`。

那这里发生了什么呢？我们通过分解这个比较的组成部分就可以看出问题所在。

```py
>>> 0.3
0.3
>>> 0.1 + 0.2
0.30000000000000004

```
现在我们已经看到了导致问题的浮点表示形式。

那么，我们该如何处理这个问题呢？


### Decimal[¶](#decimal "Permanent link")

有几种选择，它们都有各自的缺点。让我们从 [`Decimal`](https://docs.python.org/3/library/decimal.html) 开始。

> 
> The decimal module provides support for fast correctly rounded decimal floating point arithmetic. （decimal 模块提供对快速正确舍入的十进制浮点运算的支持。）
> 

这听起来不错，但这里的一个重要问题是，它如何处理数字与字符串。


```py
>>> from decimal import Decimal
>>> Decimal(0.1)
Decimal('0.1000000000000000055511151231257827021181583404541015625')
>>> Decimal('0.1')
Decimal('0.1')

```
这意味着，为了完成上面的比较，我们需要将每个数字包装在一个字符串中。

```py
>>> Decimal('0.1') + Decimal('0.2') == Decimal('0.3')
True

```

很烦，但确实能用。

### isclose[¶](#isclose "Permanent link")

Python 3.5 实现了 [PEP 485](https://peps.python.org/pep-0485/) 来测试近似相等。这是在 [`isclose`](https://docs.python.org/3/library/math.html#math.isclose) 函数中完成的。


```py
>>> import math
>>> math.isclose(0.1+0.2,0.3)
True

```

这比把所有东西都用字符串包裹起来更干净。但是，它也比简单的 `==` 语句更冗长。它让您的代码不太干净，但确实提供了准确的比较。

## is vs. ==[¶](#is-vs "Permanent link")

我经常看到的另一个被误用的比较是，开发人员用了 `is`但他们其实是想表达 `==`. 简而言之，`is` **仅**当您检查两个引用是否引用同一对象时才应使用。`==` 用于通过调用底层方法 `__eq__` 来比较值。

让我们看看实际效果：

```py
>>> a = [1, 2, 3]
>>> b = a
>>> c = [1, 2, 3]
>>> d = [3, 2, 1]
>>> a == b
True
>>> a == c
True
>>> a == d
False

```
到目前为止，没有什么异常。`a` 具有和 `b` 与 `c` 相同的值，并且与 `d` 具有不同的值。现在让我们使用 `is`

```py
>>> a is b
True
>>> b is a
True
>>> a is c
False
>>> a is d
False
>>> b is c
False

```

这里，唯一为 `True` 的语句是 `a` 和 `b` 之间的比较。这是因为 `b` 是用 `b = a` 语句初始化的。另外两个变量被初始化为它们自己的语句和值。**请记住，`is` 比较对象引用。如果它们相同，则返回 `True`。**


```py
>>> id(a), id(b), id(c), id(d)
(2267170738432, 2267170738432, 2267170545600, 2267170359040)

```

由于 `a` 和 `b` 是同一个对象，在对其进行比较的时候，我们才会得到 `True`。其他是不同对象，因此是 `False`。


## nan == nan[¶](#nan-nan "Permanent link")

`nan`，或者说“非数字（Not a Number）”是一个浮点值，它无法被转换为浮点数以外的任何值，并且被视为不等于所有其他值。这是表示数据集中缺失值的常见方法。

上面的描述中有一个关键短语，它是这个陷阱的基础：
> 
> is considered not equal to all other values（被视为不等于所有其他值）
> 

软件通常会在采取操作之前检查两个值是否彼此相等。对于 `nan`，这不起作用：

```py
>>> float('nan') == float('nan')
False

```

这可以防止这样的代码进入 `if/else` 语句的 `if` 块。


```py
>>> a = float('nan')
>>> b = float('nan')
>>> if a == b:
... .. ## Do something if equal
... else:
... .. ## Do something if not equal

```

在这个例子中，它们*永远*不相等。

这导致了一种有意思的（如果不是不直观的话）检查变量是否是 `nan` 值的方法。由于 `nan` 不等于所有其他值，因此它也不等于自身。

```py
>>> a != a
True

```

就像我说的，“有意思”。但是，当其他人查看您的代码时，它也会“令人困惑”。有一种更简单的方法可以表明您正在检查一个 `nan` 值。[`isnan`](https://docs.python.org/3/library/math.html#math.isnan)


```py
>>> import math
>>> a = float('nan')
>>> b = 5
>>> c = float('infinity')
>>> math.isnan(a)
True
>>> math.isnan(b)
False
>>> math.isnan(c)
False

```

对我来说，这是一个更清晰的检查，我们想看看该值是否为 `nan`。 您可能不只是传递 `nan` 给单个变量。你可能正在使用诸如 [NumPy](https://numpy.org/) 或者 [Pandas](https://pandas.pydata.org/) 这样的库。在这种情况下，每个库都自带了一些函数，你可以用它们来完美检查 `nan`。


* 在 NumPy 中，这个函数具有相同的名字，但是是在 NumPy 库中的：`numpy.isnan(value)`。
* 在 Pandas 中，这个函数具有一个稍微不同的名字：`pandas.isna(value)`


### 总结[¶](#conclusion "Permanent link")

比较并不总是像我们希望的那样直接。我在这里介绍了 Python 中的一些常见比较问题。

浮点比较在各种语言中都很常见。Python 有几种方法可以让开发人员更轻松地做到这一点。我建议使用 `isclose`，因为它可以使代码更加简洁，并且在使用该模块时无需像 `Decimal` 模块那样将数字包装在字符串中

`is` 应该*只*用于检查两个项目是否引用同一个对象。在任何其他情况下，它都不会执行您希望它执行的检查。

最后，`nan`等于*没有别的*。在开始将数据集中的值相互比较之前，了解这一点很重要。