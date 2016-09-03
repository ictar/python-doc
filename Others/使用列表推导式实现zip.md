原文：[Implementing "zip" with list comprehensions](http://blog.lerner.co.il/implementing-zip-list-comprehensions/)

---

[![zipper](http://i1.wp.com/blog.lerner.co.il/wp-content/uploads/2016/08/zipper3.png?resize=150%2C300)](http://i1.wp.com/blog.lerner.co.il/wp-content/uploads/2016/08/zipper3.png)

我喜欢Python的"[zip](https://docs.python.org/3/library/functions.html#zip)"函数。我不大确定我喜欢zip什么，但是我常常发现它非常有用。在我描述"zip"做了什么之前，先让我告诉你一个例子：

```python
>>> s = 'abc'
>>> t = (10, 20, 30)

>>> zip(s,t)
[('a', 10), ('b', 20), ('c', 30)]
```

正如你所看到的，"zip"的结果是一个元组序列。(在Python 2中，你会得到一个列表。在Python 3中，你会得到一个"zip对象"。) 下标为0的元组包含s[0]和t[0]。下标为1的元组包含s[1]和t[1]。以此类推。你还可以对多于一个可迭代对象使用zip：

```python
>>> s = 'abc'
>>> t = (10, 20, 30)
>>> u = (-5, -10, -15)

>>> list(zip(s,t,u))
[('a', 10, -5), ('b', 20, -10), ('c', 30, -15)]
```

(你还可以用一个可迭代对象来调用zip，从而获得一堆一元素元组，但这对我来说似乎有点奇怪。)

我常常使用"zip"来将并行序列转换成字典。例如：

```python
>>> names = ['Tom', 'Dick', 'Harry']
>>> ages = [50, 35, 60]

>>> dict(zip(names, ages))
{'Harry': 60, 'Dick': 35, 'Tom': 50}
```

通过这种方式，我们可以快速方便的从两个并行序列中生成一个字典。

每当我在我的编程班中提到"zip"，难免有人会问，如果一个参数比另一个参数短，会发生什么事。简单地说，最短的那个获胜：

```python
>>> s = 'abc'
>>> t = (10, 20, 30, 40)
>>> list(zip(s,t))
[('a', 10), ('b', 20), ('c', 30)]
```

(如果你想要zip为较长的可迭代对象中的每个元素返回一个元组，那么使用"[itertools](https://docs.python.org/3/library/itertools.html#module-itertools)"包中的"[izip_longest](https://docs.python.org/3/library/itertools.html#itertools.zip_longest)"。)

现在，如果还有什么比"zip"更让我喜欢的，那它就是列表推导式了。所以，上周，当我的一个学生问我，是否我们可以使用列表推导式来实现"zip"的时候，我无法抗拒。

所以，我们可以怎么做呢？

首先，让我们假设，从上面，我们有两个相同长度的序列，s(一个字符串)和t(一个元组)。我们想要获得一个包含三个元组的列表. 其中一个方法是：

```python
[(s[i], t[i])              # produce a two-element tuple
 for i in range(len(s))]   # from index 0 to len(s) - 1
```

老实说，这工作良好！但我们还有可以改善它的一些方法。

首先，让我们基于推导式的"zip"选择性地处理不同大小的输入将是不错的。这意味着，不仅仅是运行range(len(s))，还运行range(len(x))，其中，x是较短的序列。我们可以通过内置函数"sorted"来做到这点，告诉它根据长度来对序列进行排序，从最短的到最长的。例如：

```python
>>> s = 'abcd'
>>> t = (10, 20, 30)

>>> sorted((s,t), key=len)
[(10, 20, 30), 'abcd']
```

在上面的代码中，我创建了一个新的元组：(s,t)，然后将其作为第一个参数传递给"sorted"。给定这些输入，我们将从"sorted"获得一个列表。由于我们传递了内置的"len"函数给"key"参数，因此，如果s更短，那么"sorted"会返回[s,t]，如果t更短，则返回[t,s]。这意味着，下标为0的元素保证不比其他任何序列长。(如果所有的序列都是相同的大小，那么，我们不在乎获得哪个。)

将这些一起放到我们的推导式中，获得：

```python
>>> [(s[i], t[i])    
    for i in range(len(sorted((s,t), key=len)[0]))]
```

这对于一个单一的列表推导式有点复杂，因此，我将把第二行的一部分折断成一个函数，仅仅是为了把它弄得干净些：

```python
>>> def shortest_sequence_range(*args):
        return range(len(sorted(args, key=len)[0]))

>>> [(s[i], t[i])     
    for i in shortest_sequence_range(s,t) ]
```

现在，我们的函数接收\*args，这意味着它可以接收任何数量的序列。序列们根据长度进行排序，然后传递第一个（最短的）序列给"len"，该函数计算长度，然后返回运行"range"的结果。

因此，如果最短的序列是'abc'，那么结果我们会返回range(3)，它为我们提供索引0, 1, 和2 —— 满足我们所需。

现在，它离真正的"zip"还有点距离：正如我上面提到的，Python 2的"zip"函数返回一个列表，但是Python 3的"zip"返回一个可迭代对象。这意味着，即使返回的列表非常的长，我们也不会因为一次性将其全部返回而占用大量的内存。我们可以用推导式来做到这点吗？

是哒，但如果我们使用列表推导式是做不到的，它总是返回一个列表。相反，如果我们使用一个生成器表达式，那么我们将会活动一个迭代器，而不是整个列表。幸运的是，创建这样一个生成器表达式只是将我们的列表推导式的[ ]替换成生成器表达式的( )这么简单而已：

```python
>>> def shortest_sequence_range(*args):
      return range(len(sorted(args, key=len)[0]))

>>> g = ((s[i], t[i])
         for i in shortest_sequence_range(s,t) )

>>> for item in g:
        print(item)
('a', 10)
('b', 20)
('c', 30)
```

现在你明白了！欢迎对这些想法做进一步改善 —— 但作为同时喜欢"zip"和推导式的人，将这两个概念联系在一起是很有趣的。
