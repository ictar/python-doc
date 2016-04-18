原文：[Python 201 – What’s a deque?](http://www.blog.pythonlibrary.org/2016/04/14/python-201-whats-a-双端队列(deque)/)

---

根据Python文档， **双端队列(deque)** 是“栈和队列的泛化”。它们显然是“deck”，这是“双端队列(double-ended queue)”的简称。它们也是Python中的列表(list)的一个替代容器。双端队列(deque)是线程安全的，并支持向双端队列(deque)两端内存高效地追加(append)或弹出(pop)元素。列表为快速地固定长度的操作进行了优化。你可以从Python文档中获得所有的相关细节。一个双端队列(deque)接受一个**maxlen**参数，这个参数设置了该双端队列(deque)的范围。否则，该双端队列(deque)将增长到任意大小。当一个有界的双端队列(deque)满了，任何新元素添加操作将会导致相同数量的元素从另一端弹出。

作为一般规则，如果你需要快速追加或快速弹出，那么使用一个双端队列(deque)吧。如果你需要快速的随机访问，那么使用列表。让我们花一些时间来看看你会怎么创建并使用一个双端队列(deque)。
```py
>>> from collections import 双端队列(deque)
>>> import string
>>> d = 双端队列(deque)(string.ascii_lowercase)
>>> for letter in d:
...     print(letter)
```

这里，我们从collections模块导入了双端队列(deque)，还导入了**string**模块。要真正创建一个双端队列(deque)的实例，需要传递一个可迭代对象给它。在这种情况下，我们传递了**string.ascii_lowercase**，它将返回一个字母表中所有的小写字母组成的列表。最后，我们遍历这个双端队列(deque)，并打印出每个元素。现在，让我们看看双端队列(deque)拥有的一些方法。
```py
>>> d.append('bork')
>>> d
双端队列(deque)(['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 
       'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 
       'y', 'z', 'bork'])
>>> d.appendleft('test')
>>> d
双端队列(deque)(['test', 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 
       'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 
       'v', 'w', 'x', 'y', 'z', 'bork'])
>>> d.appendleft('test')
>>> d
双端队列(deque)(['test', 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 
       'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 
       'v', 'w', 'x', 'y', 'z', 'bork'])
>>> d.rotate(1)
>>> d
双端队列(deque)(['bork', 'test', 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 
       'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 
       't', 'u', 'v', 'w', 'x', 'y', 'z'])
```

让我们打破这一点。首先，我们添加一个字符串到双端队列(deque)的右端。然后，我们又追加字符串到双端队列(deque)的左侧。最后，我们调用**rotate**并传递给它一个参数`1`，从而使它向右旋转一次。换句话说，它会导致一个元素旋转离开右端并插入到前方。你可以传递一个负数，使双端队列(deque)向左旋转。

让我们来看看一个例子以结束这一节，这个例子基于Python文档中的一些东东：
```py
from collections import 双端队列(deque)
 
 
def get_last(filename, n=5):
    """
    Returns the last n lines from the file
    """
    try:
        with open(filename) as f:
            return 双端队列(deque)(f, n)
    except OSError:
        print("Error opening file: {}".format(filename))
        raise
```

此代码很大程度上与Linux的**tail**程序以相同的方式工作。下面，我们传递一个文件名以及一个表示我们想要返回的行数的`n`​​给我们的脚本。双端队列(deque)以任何我们传递的数字，也就是`n`为界。这意味着，一旦双端队列(deque)满了，当新的行被读入并加入到双端队列(deque)中，那么较旧的行会被从另一端弹出并丢弃。我还在一个简单的异常处理器中用**with**语句来打开该文件，因为它真的很容易传递一个畸形的路径。这将捕获哪些不存在的文件。

### 总结

现在，你知道了Python的双端队列(deque)的基本知识了。collections模块中还有这么一个方便的小工具。虽然我本人从未需要过这个特殊的集合，但是它仍然是一个有用的结构。我希望在你自己的代码中，你会发现它的一些很棒的用途。
