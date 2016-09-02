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

正如你所看到的，"zip"的结果是一个元祖序列。(在Python 2中，你会得到一个列表。在Python 3中，你会得到一个"zip对象"。) 下标为0的元祖包含s[0]和t[0]。下标为1的元祖包含s[1]和t[1]。以此类推。你还可以对多于一个可迭代对象使用zip：

```python
>>> s = 'abc'
>>> t = (10, 20, 30)
>>> u = (-5, -10, -15)

>>> list(zip(s,t,u))
[('a', 10, -5), ('b', 20, -10), ('c', 30, -15)]
```

(你还可以用一个可迭代对象来调用zip，从而获得一堆一元素元祖，但这对我来说似乎有点奇怪。)

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

(如果你想要zip为较长的可迭代对象中的每个元素返回一个元祖，那么使用"[itertools](https://docs.python.org/3/library/itertools.html#module-itertools)"包中的"[izip_longest](https://docs.python.org/3/library/itertools.html#itertools.zip_longest)"。)

现在，如果还有什么比"zip"更让我喜欢的，那它就是列表推导式了。所以，上周，当我的一个学生问我，是否我们可以使用列表推导式来实现"zip"的时候，我无法抗拒。

所以，我们可以怎么做呢？

首先，让我们假设，从上面，我们有两个相同长度的序列，s(一个字符串)和t(一个元祖)。我们想要获得一个包含三个元祖的列表. 其中一个方法是：

```python
[(s[i], t[i])              # produce a two-element tuple
 for i in range(len(s))]   # from index 0 to len(s) - 1
```

老实说，这工作良好！但我们还有可以改善它的一些方法。

First of all, it would be nice to make our comprehension-based "zip"
alternative handle inputs of different sizes.  What that means is not just
running range(len(s)), but running range(len(x)), where x is the shorter
sequence. We can do this via the "sorted" builtin function, telling it to sort
the sequences by length, from shortest to longest. For example:

```python
>>> s = 'abcd'
>>> t = (10, 20, 30)

>>> sorted((s,t), key=len)
[(10, 20, 30), 'abcd']
```

In the above code, I create a new tuple, (s,t), and pass that as the first
parameter to "sorted". Given these inputs, we will get a list back from
"sorted". Because we pass the builtin "len" function to the "key" parameter,
"sorted" will return [s,t] if s is shorter, and [t,s] if t is shorter.  This
means that the element at index 0 is guaranteed not to be longer than any
other sequence. (If all sequences are the same size, then we don't care which
one we get back.)

Putting this all together in our comprehension, we get:

```python
>>> [(s[i], t[i])    
    for i in range(len(sorted((s,t), key=len)[0]))]
```

This is getting a wee bit complex for a single list comprehension, so I'm
going to break off part of the second line into a function, just to clean
things up a tiny bit:

```python
>>> def shortest_sequence_range(*args):
        return range(len(sorted(args, key=len)[0]))

>>> [(s[i], t[i])     
    for i in shortest_sequence_range(s,t) ]
```

Now, our function takes *args, meaning that it can take any number of
sequences. The sequences are sorted by length, and then the first (shortest)
sequence is passed to "len", which calculates the length and then returns the
result of running "range".

So if the shortest sequence is 'abc', we'll end up returning range(3), giving
us indexes 0, 1, and 2 — perfect for our needs.

Now, there's one thing left to do here to make it a bit closer to the real
"zip": As I mentioned above, Python 2's "zip" returns a list, but Python 3's
"zip" returns an iterator object. This means that even if the resulting list
would be extremely long, we won't use up tons of memory by returning it all at
once. Can we do that with our comprehension?

Yes, but not if we use a list comprehension, which always returns a list. If
we use a generator expression, by contrast, we'll get an iterator back, rather
than the entire list. Fortunately, creating such a generator expression is a
matter of just replacing the [ ] of our list comprehension with the ( ) of a
generator expression:

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

And there you have it!  Further improvements on these ideas are welcome — but
as someone who loves both "zip" and comprehensions, it was fun to link these
two ideas together.
现在你明白了！欢迎对这些想法做进一步改善 —— 但作为同时喜欢"zip"和推导式的人，将这两个概念联系在一起是很有趣的。
