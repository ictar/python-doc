原文：[Python: Declaring Dynamic Attributes](http://amir.rachum.com/blog/2016/10/05/python-dynamic-attributes/)

---

下面的例子用的是Python 3.5，但除此之外，这篇文章同时也适用于Python 2.x和3.x

在Python中覆盖类中的`__getattr__`魔术方法，以实现动态属性，这是一种常见的解决方法。想想`AttrDict`，它是一个字典，允许如属性般访问它存储的键值对：

```python
class AttrDict(dict):
    def __getattr__(self, item):
        return self[item]
```

简化的`AttrDict`允许像属性一样从它读取字典值，但是也允许_设置_键值对是非常简单的。无论如何，下面是对它的操作：

```python
>>> attrd = AttrDict()
... attrd["key"] = "value"
... print(attrd.key)
value
```

覆盖`__getattr__` (和`__setattr__`)是非常有用的，从简单的诸如`AttrDict`这样让你的代码更具可读性的“噱头”，到你的应用中诸如远程过程调用（RPC）这样的基本组成部分。然而，动态属性有些让人郁闷的地方 —— 在你用之前，无法看到它们！

当工作在一个交互式shell中的时候，东太熟悉有两个可用性问题。第一个是，当用户通过调用`dir`方法来试图检查对象的API时，它们并不出现：

```python
>>> dir(attrd)  # I wonder how I can use attrd
['__class__', '__contains__', ... 'keys', 'values']
>>> # No dynamic attribute :(
```

第二个问题是自动完成 —— 如果我们用老方法将`normal_attribute`设置成一个熟悉，那么在大部分的现代shell环境下，可以自动完成（下面是来自IDLE的shell截图）：

![](/images/posts/normal_attribute.png)

但是通过将`dynamic_attribute`当成一个键值对插入来设置它的时候，并不会自动完成：

![](/images/posts/dynamic_attribute_before.png)

然而，在实现动态属性的时候，你可以采取一个额外的方法，该方法将会让你的用户感到愉悦，并且[一石二鸟](https://www.youtube.com/watch?v=71gilEP4aJY) - **实现`__dir__`方法**。来自[文档](https://docs.python.org/2/library/functions.html#dir)：

> 如果对象有一个名为`__dir__()`的方法，那么这个方法将会被调用，并且必须返回属性列表。这允许实现了自定义的`__getattr__()`或者`__getattribute__()`函数的对象自定义`dir()`报告属性的方式。

实现`__dir__`是很简单的：返回该对象的属性名列表：

```python
class AttrDict(dict):
    def __getattr__(self, item):
        return self[item]

    def __dir__(self):
        return super().__dir__() + [str(k) for k in self.keys()]
```

这会让`dir(attrd)`返回动态属性，以及常规属性。关于这个的一个有趣的事情是，**shell环境通常使用`__dir__`来提供自动完成选项建议！**因此，无需任何额外的努力，我们就得到了自动完成：

![](/images/posts/dynamic_attribute_after.png)

在[Hacker News](https://news.ycombinator.com/item?id=12644164), [/r/Programming](https://www.reddit.com/r/programming/comments/55zuip/python_declaring_dynamic_attributes/), 或者下面的评论部分**讨论**这篇文章。

**感谢**[Ram Rachum](http://ram.rachum.com)阅读了本文草稿。