原文：[Lists vs. Tuples](http://nedbatchelder.com/blog/201608/lists_vs_tuples.html "Link to this post" )

---

常见的Python初学者问题：列表和元组之间有何区别？

答案是，有两个不同的差异，以及两者之复杂的相互作用。这就是技术差异和文化差异。

首先：它们有相同之处：列表和元组都是容器，即对象序列：

```python
>>> my_list = [1, 2, 3]  
>>> type(my_list)  
<class 'list'>  
>>> my_tuple = (1, 2, 3)  
>>> type(my_tuple)  
<class 'tuple'>  
```

它们任意一个的元素可以是任意类型，甚至在一个单一序列中。它们都维护元素的顺序（不像集合和字典那样）。

现在是不同之处。列表和元组之间的技术差异是，列表示可变的（可以被改变），而元组是不可变的（不可以被改变）。这是Python语言对它们的唯一区分：

```python
>>> my_list[1] = "two"  
>>> my_list  
[1, 'two', 3]  
>>> my_tuple[1] = "two"  
Traceback (most recent call last):  
  File "<stdin>", line 1, in <module>  
TypeError: 'tuple' object does not support item assignment  
```

这就是列表和元组之间的唯一技术差异，虽然它体现在几个方面。例如，列表有一个.append()方法，用以添加更多元素到列表中，而元组并没有：

```python
>>> my_list.append("four")  
>>> my_list  
[1, 'two', 3, 'four']  
>>> my_tuple.append("four")  
Traceback (most recent call last):  
  File "<stdin>", line 1, in <module>  
AttributeError: 'tuple' object has no attribute 'append'  
```

元组并不需要.append()方法，因为你不可以修改元组。

文化差异是关于列表和元组的实际使用的：当你有一个未知长度的同质序列时使用列表；当你预先知道元素个数的时候使用元组，因为元素的位置语义上是显著的。

例如，假设你有一个函数，它查找目录下以*.py结尾的文件。它应该返回一个列表，因为你并不知道会找到多少个文件，而它们都具有相同的语义：只是你找到的另一个文件。

```python
>>> find_files("*.py")  
["control.py", "config.py", "cmdline.py", "backward.py"]  
```

另外，假设你需要存储五个值来表示气象观测站的位置：id, city, state, latitude和longitude。那么较之列表，元组更适合：

```python
>>> denver = (44, "Denver", "CO", 40, 105)  
>>> denver[1]  
'Denver'  
```

（目前，不要讨论使用类来替代）这里，第一个元素是id，第二个元素是city，以此类推。位置决定了意思。

将文化差异加之于C语言上，列表像数组，元组像结构。

Python有一个namedtuple工具，它可以让意思更加明确：

```python
>>> from collections import namedtuple  
>>> Station = namedtuple("Station", "id, city, state, lat, long")  
>>> denver = Station(44, "Denver", "CO", 40, 105)  
>>> denver  
Station(id=44, city='Denver', state='CO', lat=40, long=105)  
>>> denver.city  
'Denver'  
>>> denver[1]  
'Denver'  
```

元组和列表之间的文化差异一个聪明的总结是：元组是没有名字的namedtuple。

技术差异和文化差异是一个不稳定的联盟，因为有时它们相左。为什么同源序列应该可变，而异源序列不是？例如，因为namedtuple是一个元组，它是不可变的，所以我不可以修改我的气象站：

```python
 denver.lat = 39.7392  
Traceback (most recent call last):  
  File "<stdin>", line 1, in <module>  
AttributeError: can't set attribute  
```

有时，技术方面的考虑覆盖了文化因素。你不能把一个列表当做一个字典键，因为只有不可变值才能够被哈希，所以只有不可变值才能作为键。要把一个列表当成键，你可以将其转换成元组：

```python
 d = {}  
>>> nums = [1, 2, 3]  
>>> d[nums] = "hello"  
Traceback (most recent call last):  
  File "<stdin>", line 1, in <module>  
TypeError: unhashable type: 'list'  
>>> d[tuple(nums)] = "hello"  
>>> d  
{(1, 2, 3): 'hello'}  
```

另一个技术和文化的冲突是：Python自身在使用列表更有意义的情况下使用了元组。当你使用*args定义一个函数时，args作为元组传递，即使据Python所知，值的位置并不重要。你可能会说，它是元组，因为你不可以改变你所传递的值，但这只是较之文化，更重视了技术差异。

我知道，我知道，在*args中，位置可能是重要的，因为它们是位置参数。但在一个接受*args，然后将其传递给另一个函数的函数中，它只是一个参数序列，和另一个没什么不同，而它们的数量在调用之间可以不同。

这里，Python使用元组，是因为较之列表，它们的空间效率会多一点。列表是过度分配的，以便让附加更快些。这说明Python务实的一面：因地制宜使用数据结构，而不是纠结于*args的列表/元组语义。

在大多数情况下，你应该基于文化差异选择是使用列表还是元组。想想你的数据的含义。如果基于你的程序在现实世界中遇到的，它会有不同的长度，那么可能要使用列表。如果你知道在你写代码的时候，第三个元素意味着什么，那么可能要使用元组。

另一方面，函数式编程强调不可变数据结构，作为一种避免难以推理代码这一副作用的方式。如果你是一个函数式编程粉，那么你可能会因为不可变性喜欢元组。

所以：你应该使用元组还是列表呢？答案是：它并不总是一个简单的答案。