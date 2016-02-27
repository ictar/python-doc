原文： [The Idiomatic Way to Merge Dictionaries in Python](https://treyhunner.com/2016/02/how-to-merge-dictionaries-in-python/)

---

你是否曾经想过在Python将两个或多个字典进行合并？

有多种方式来解决这个问题：有一些是不方便的，一些是不准确的，并且大多数需要多行代码。

让我们漫游解决此问题的不同方式，并讨论哪一个最[Pythonic](https://docs.python.org/3/glossary.html#term-pythonic)。

## 我们的问题

在我们讨论解决方案之前，我们需要清楚地界定我们的问题。

我们的代码有两个字典：`user`和`defaults`。我们希望将这两个字典合并成一个名为`context`的新字典。

我们有一些需求：

1.  在有重复的键的情况下，`user`值应该覆盖`defaults`值
2.  `defaults`和`user`中的键可以是任何有效的键
3.  `defaults`和`user`中的值可以是任何东西
4.  在创建`context`的过程中，`defaults`和`user`不应该更改
5.  对`context`做的更新绝不应该改变`defaults`或`user`

**注**: 在5中，我们专注于对字典的更新，而不是包含对象。有关嵌套对象的可变性的考虑，我们应该考虑[copy.deepcopy](https://docs.python.org/3/library/copy.html#copy.deepcopy)。

因此，我们希望是这样的：
```py
>>> user = {'name': "Trey", 'website': "http://treyhunner.com"}
>>> defaults = {'name': "Anonymous User", 'page_name': "Profile Page"}
>>> context = merge_dicts(defaults, user)  # magical merge function
>>> context
{'website': 'http://treyhunner.com', 'name': 'Trey', 'page_name': 'Profile Page'}
```

我们也将考虑一个解决方法是否Pythonic。这是一个非常主观，并且经常是虚幻的度量。下面一些我们将使用的特殊的标准：

*   解决方案应该是简洁的，但不是简短生硬的
*   解决方案应该是可读的，但不是过于冗长的
*   如果有可能，解决方案应该是一行代码，这样的话，如果有需要，它可以内联使用
*   解决方案应该是不必要的低效的

## 可能的解决方案

现在我们已经定义了我们的问题，让我们一起讨论一些可能的解决方案吧。

我们将看到许多合并字典的方法，并讨论这些方法中，哪一个是最准确的，以及哪一个是最地道的。


### 多次更新

下面是合并我们的字典最简单的方法之一：
```py
context = {}
context.update(defaults)
context.update(user)
```

在这里，我们创建了一个空字典，然后使用[update](https://docs.python.org/3.5/library/stdtypes.html#dict.update)方法从每个字典中添加`defaults`，这样做使得`user`中的任何公共键将覆盖`defaults`中的公共键。

我们所有的五个需求都得到满足，因此这是**准确的**。这个解决方案有三行代码，因此不能内联执行，但它是相当清晰的。

得分：

*   准确性：是的
*   符合语言习惯：相当符合，但是如果可以内联的话，那就更好了

### 复制然后更新

另外，我们可以复制`defaults`，然后用`user`来更新这个副本。
```py
context = defaults.copy()
context.update(user)
```

这种解决方案只与前一个略有不同。

对于这个特定的问题，我更喜欢复制`defaults`字典这个解决方案，因为这清晰的表示了`defaults`代表的默认值。

得分：

*   准确性：是的
*   符合语言习惯：是的（译者：但是还是没法内联呀！！）

### 字典构造器

我们也可以将我们的字典传递给`dict`构造器，这样，它也将为我们复制这个字典：
```py
context = dict(defaults)
context.update(user)
```

该解决方案与前一个是非常相似的，但它有点不明确。

得分：

*   准确性：是的
*   符合语言习惯：有点，但相比于这个，我更喜欢前两个

### 巧用关键参数

你可以之前已经看到过这个聪明的回答，[可能是在StackOverflow上](http://stackoverflow.com/a/39858/98187):
```py
context = dict(defaults, **user)
```

这个代码只有一行。这很酷。但是，这个方案有点难理解。

除了可读性之外，还有一个更大的问题：**这个解决方案是错误的。**

键必须是字符串。在Python 2（使用CPython解释器）中，我们可以侥幸使用非字符串作为键，但不要被愚弄：这是一种hack行为，它只能偶然在使用标准CPython运行时间的Python 2中有效。

得分：

*   准确性：不是。不满足需求2（键应该是任何有效键）
*   符合语言习惯：不是。这是一种hack行为。

### 字典推导

只是因为我们可以，所以让我们借助字典推导来进行以下尝试：
```py
context = {k: v for d in [defaults, user] for k, v in d.items()}
```

这有效，但是这有点难以阅读。

如果我们有数量不明的字典，那么这可能是一个好主意，但我们可能需要将我们的推导变成多行代码，以使之更具可读性。在我们两个字典的情况下，这种双重嵌套的推导有点多。

得分：

*   准确性：是的
*   符合语言习惯：可以说不是

### 连接项

如果我们从每个字典得到一个项`list`，连接它们，然后从中创建一个新的字典呢？
```py
context = dict(list(defaults.items()) + list(user.items()))
```

这确实有效。我们知道，`user`将胜过`defaults`，因为这些键在我们连接列表的结尾出现。

在Python 2中，我们其实并不需要`list`转换，但在这里，我们在Python 3中工作（你是在Python 3中，对吧？）。

得分：

*   准确性：是的
*   符合语言习惯：不特别复合，有一点重复

### 合并项

在Python 3中，`items`是一个`dict_items`对象，它是一个支持并操作的奇怪的对象。
```py
context = dict(defaults.items() | user.items())
```

这有点有趣。但是，**这是不准确的。**

要求1（`user`应该“胜过”`defaults`）不满足，因为两个`dict_items`对象的并集并不是一个键值对的[集合(set)](https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset)，并且集合是无序的，因此重复键可能以一种不可预知的方式来解决。

要求3（值可以是任何东西）不满足，因为集合要求它们的项是[可哈希的](https://docs.python.org/3/glossary.html#term-hashable)，所以在我们的键值元组中，无论是键还是值都必须是可哈希的。

附注：我不知道为什么甚至在dict_items对象上运行并操作。这有什么好处？

得分：
*   准确性：不是，需求1和3不满足
*   符合语言习惯：不是


### 链接项

目前，我们看到在一行代码中执行这个合并操作的最符合语言习惯的方式包括创建两个项列表，连接它们，然后组成一个字典。

我们可以使用[itertools.chain](https://docs.python.org/3/library/itertools.html#itertools.chain)更简洁的将我们的项放在一起：
```py
from itertools import chain
context = dict(chain(defaults.items(), user.items()))
```

这种方式运行良好，并可能比创建两个不必要的列表更有效率。

得分：

*   准确性：是的
*   符合语言习惯：相当符合，但是这些`items`调用似乎略显多余

### ChainMap

[ChainMap](https://docs.python.org/3/library/collections.html#collections.ChainMap)允许我们创建一个新的字典，在此过程中甚至没有遍历我们初始的字典（好吧，其实是有点的，我们将讨论这一点）：
```py
from collections import ChainMap
context = ChainMap({}, user, defaults)
```

`ChainMap`将字典集合成一个代理对象（一个“view”）；查找查询每个提供的字典，知道找到一个匹配。

此代码引发了一些问题。

#### 为什么我们将`user`放在`defaults`之前？

我们用这种方式排序是为了确保满足需求1.按顺序查找字典，所以`user`在`defaults`之前返回匹配。

#### 为什么在`user`之前有一个空字典？

这是为了满足需求5.更改为`ChainMap`对象会影响提供的第一个字典，而我们不想`user`被改变，所以我们首先提供了一个空字典。

#### 这个真的提供了一个字典吗？

`ChainMap`对象**不是一个字典**，但它是一个**类字典**映射。如果我们的代码实现[鸭子类型(duck typing)](https://docs.python.org/3/glossary.html#term-duck-typing)，那么我们也许会同意这点，但是以防万一，我们需要检查`ChainMap`的特性。在其他特性中，`ChainMap`对象被耦合到它们的[基础字典](https://gist.github.com/treyhunner/2abe2617ea029504ef8e)，并且它们以一种有趣的方式处理[项删除](https://gist.github.com/treyhunner/5260810b4cced03359d9)。

得分：

*   准确性：可能，我们需要考虑自己的用例
*   符合语言习惯：如果我们确定了这个满足用例，那么是的

### 来自ChainMap的字典

如果我们真的想要一个字典，那么我们可以转换我们的`ChainMap`到一个字典：
```py
context = dict(ChainMap(user, defaults))
```

在这个代码中，`user`必须位于`defaults`之前，这有点奇怪，而这个顺序出现在我们大多数解决方案中。除了这种怪异性外，这个代码是非常简单的，并且对于我们的目的是足够清晰的。


得分：

*   准确性：是的
*   符合语言习惯：是的

### 字典级联

如果我们只是简单级联我们的字典呢？
```py
context = defaults + user
```

这很酷，但是**无效**。去年，在[python-ideas线程](https://mail.python.org/pipermail/python-ideas/2015-February/031748.html)对其进行了讨论。

一些在此线程中被提出的问题包括：

*   或许`|`比`+`更有意义，因为字典类似集合
*   对于重复的键，应该是左侧还是右侧胜出呢？
*   是否应该有一个内置的`updated`来取而代之(有点像[sorted](https://docs.python.org/3/library/functions.html#sorted))?

得分：

*   准确性：不是。无效
*   符合语言习惯：不是。无效

### 字典解包(unpacking)

如果你使用的是Python 3.5，那么得益于[PEP 448](https://www.python.org/dev/peps/pep-0448/)，有一个新的方法来合并字典：
```py
context = {**defaults, **user}
```

这个方法简单并且Pythonic。有相当多的符号，但是至少它的输出是一个字典，这是相当明显的。

这是功能上等同于我们的第一个解决方案，在这个方案中我们创建了一个空字典，并反过来用所有来自于`defaults`和`user`的项填充它。我们所有的要求都得到满足，并且这可能是我们可以得到的最简单的解决方案。

得分：

*   准确性：是的
*   符合语言习惯：是的

## 总结

有许多方法可以合并多个字典，但也有几个优雅的方法，它们只用一行代码来做到。

如果你正在使用Python 3.5，那么这是解决这一问题的有效方法之一：
```py
context = {**defaults, **user}
```

如果你还没有使用Python 3.5，你需要看看上面的解决方案，以确定哪些方案最适合你。

**注**：对于那些特别关注性能的人来说，我还测量了[这些不同的字典合并方法的性能](https://gist.github.com/treyhunner/f35292e676efa0be1728)。

我以教Python为生。如果你喜欢我的教学方式，并且你的团队对**[Python培训](http://truthful.technology/)**有兴趣，请[与我联系](/cdn-cgi/l/email-protection#f29a979e9e9db2868087869a94879edc8697919a9c9d9e9d958b)!
