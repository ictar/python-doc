# 词汇表
原文地址：[Glossary](https://docs.python.org/3/glossary.html)

---

## `>>>`
默认的Python交互shell提示符。常见于交互式解释器中执行的代码示例。

## `...`
当在一个缩进代码块或者一对匹配的左右分隔符（括号，方括号或大括号）内输入代码时的默认Python交互shell提示符。

## 2to3
一个尝试将Python 2.x代码转换成Python 3.x代码的工具。此工具通过解析代码及遍历解析树来检测大多数的不可兼容内容来处理转换。

2to3可通过标准库lib2to3使用；另一个独立入口点是作为/Tools/scripts/2to3提供。 见 [2to3 - Python2到3的自动翻译](https://docs.python.org/3/library/2to3.html#to3-reference)。

## 抽象基类
抽象基类通过通过当其他技术，例如[hasattr()](https://docs.python.org/3/library/functions.html#hasattr)，可能笨拙或者微妙的错误（例如使用[magic method](https://docs.python.org/3/reference/datamodel.html#special-lookup)）时，提供一种定义接口的方式来补充[鸭子类型duck-typing](https://docs.python.org/3/glossary.html#term-duck-typing). ABCs(abstract base classes)引入虚拟子类，它并不继承某个类，但仍然被[isinstance()](https://docs.python.org/3/library/functions.html#isinstance)和issubclass()识别；  见[abc模块文档](https://docs.python.org/3/library/abc.html#module-abc)。 Python提供了许多内置的ABCs，例如数据结构（[collections.abc模块](https://docs.python.org/3/library/collections.abc.html#module-collections.abc)），数字（[数字模块](https://docs.python.org/3/library/numbers.html#module-numbers)），流（[io模块](https://docs.python.org/3/library/io.html#module-io)），import发现器和加载器（[importlib.abc模块](https://docs.python.org/3/library/importlib.html#module-importlib.abc)）。你可以使用[ABC模块](https://docs.python.org/3/library/abc.html#module-abc)创建自己的ABCs。

## 参数(argument)
调用函数时传递给一个[函数](https://docs.python.org/3/glossary.html#term-function)（或[方法](https://docs.python.org/3/glossary.html#term-method)）的一个值。有两种参数：

* 关键字参数：函数调用前面有一个标识符的参数（例如，`name=`）或者值通过在字典前加`**`传递的参数。例如，在下面的的complex()调用中，`3`和`5`都是关键字参数：
```
complex(real=3, imag=5)
complex(**{'real': 3, 'imag': 5})
```
* 位置参数：不是关键字参数的参数。位置参数可以出现在参数列表的起始位置，及（或者）通过在一个可迭代对象的元素前加`*`进行传递。例如，在下面的调用中，`3`和`5`都是位置参数：
```
complex(3, 5)
complex(*(3, 5))
```
参数被赋值给函数体中的命名的局部变量。查看[Calls](https://docs.python.org/3/reference/expressions.html#calls)部分以获取管理此赋值的规则。语法上，任何表达式均可被用于表示一个参数；赋予局部变量评估值(evaluated value)。

另请参阅[参数parameter](https://docs.python.org/3/glossary.html#term-parameter)词条，关于[arguments和parameters之间不同](https://docs.python.org/3/faq/programming.html#faq-argument-vs-parameter)的FAQ问题，及[PEP 362](https://www.python.org/dev/peps/pep-0362)。

## 异步上下文管理器
一个通过定义[__aenter__()](https://docs.python.org/3/reference/datamodel.html#object.__aenter__)和[__aexit__()](https://docs.python.org/3/reference/datamodel.html#object.__aexit__)方法，控制一个[异步with](https://docs.python.org/3/reference/compound_stmts.html#async-with)语句中可见环境的对象。由[PEP 492](https://www.python.org/dev/peps/pep-0492)引入。

## 异步迭代
一个可以在[异步for(async for)](https://docs.python.org/3/reference/compound_stmts.html#async-for)语句中使用的对象。必须从其[__aiter__()](https://docs.python.org/3/reference/datamodel.html#object.__aiter__)方法中返回一个[awaitable](https://docs.python.org/3/glossary.html#term-awaitable), 它(awaitable)应该反过来由一个[异步迭代器对象](https://docs.python.org/3/glossary.html#term-asynchronous-iterator)解析。由[PEP 492](https://www.python.org/dev/peps/pep-0492)引入。

## 异步迭代器
实现 [__aiter__()](https://docs.python.org/3/reference/datamodel.html#object.__aiter__)和[__anext__()](https://docs.python.org/3/reference/datamodel.html#object.__anext__)方法的对象。它必须返回[awaitable](https://docs.python.org/3/glossary.html#term-awaitable)对象。 [异步for(async for)](https://docs.python.org/3/reference/compound_stmts.html#async-for)解析由异步迭代器的[__anext__()](https://docs.python.org/3/reference/datamodel.html#object.__anext__)方法返回的awaitable，直到它抛出一个[StopAsyncIteration](https://docs.python.org/3/library/exceptions.html#StopAsyncIteration)异常。由[PEP 492](https://www.python.org/dev/peps/pep-0492)引入。

## 属性
一个使用点表达式(dotted expressions)与通过名字引用的对象相关联的值。例如，如果一个对象o有一个属性a，它将通过o.a引用。

## awaitable
一个可以在[await](https://docs.python.org/3/reference/expressions.html#await)表达式中使用的对象。可以是一个[协程](https://docs.python.org/3/glossary.html#term-coroutine)或者一个拥有[__await__()](https://docs.python.org/3/reference/datamodel.html#object.__await__)方法的对象。另请参考[PEP 492](https://www.python.org/dev/peps/pep-0492)。

## BDFL
Benevolent Dictator For Life, 仁慈的生活独裁者，又名[Guido van Rossum](https://www.python.org/~guido/), Python的创造者。

## 二进制文件
一个能够读写[类二进制对象](https://docs.python.org/3/glossary.html#term-bytes-like-object)的[文件对象](https://docs.python.org/3/glossary.html#term-file-object)。

> 另请参考读写[str对象](https://docs.python.org/3/library/stdtypes.html#str)的[文本文件](https://docs.python.org/3/glossary.html#term-text-file)。

## 类字节对象(bytes-like object)
一个支持[缓冲协议Buffer Protocol](https://docs.python.org/3/c-api/buffer.html#bufferobjects)和能够导出[C-contiguous](https://docs.python.org/3/glossary.html#term-contiguous)缓冲区的对象。它包含了所有的[字节](https://docs.python.org/3/library/functions.html#bytes)，[字节数组](https://docs.python.org/3/library/functions.html#bytearray)和[array.array](https://docs.python.org/3/library/array.html#array.array)对象，以及许多常见的[memoryview](https://docs.python.org/3/library/stdtypes.html#memoryview)对象。类二进制对象能用于使用二进制数据的多种操作，包括压缩，保存到二进制文件，和通过socket发送。

某些操作需要二进制数据是可变的。文档常将其称之为“可读写类二进制对象(read-write bytes-like objects)”。例如可变缓冲区对象，包括[字节数组](https://docs.python.org/3/library/functions.html#bytearray)和一个[字节数组](https://docs.python.org/3/library/functions.html#bytearray)对象的[memoryview](https://docs.python.org/3/library/stdtypes.html#memoryview)。其他操作需要二进制数据存储在不可变对象中（“只读类二进制对象(read-only bytes-like objects)”）；例如[字节](https://docs.python.org/3/library/functions.html#bytes)和一个[字节](https://docs.python.org/3/library/functions.html#bytes)对象的[memoryview](https://docs.python.org/3/library/stdtypes.html#memoryview)。

## 字节码(bytecode)
源代码会被编译成字节码，这是一个Python程序在CPython解释器中的一种内部表示。字节码也被缓存在`.pyc`和`.pyo`文件中，以便于在第二次更快的执行相同的文件（避免了重新将源代码编译成字节码）。此“中间语言”运行在一个执行对应于每个字节码的机器码的虚拟机上。请注意，不要期望字节码在不同的Python虚拟机上都有效，也不要期望，它在不同的Python版本之间是稳定的。

你可以在[dis](https://docs.python.org/3/library/dis.html#bytecodes)模块中找到字节码指令列表。

## 类
创建用户自定义对象的模板。类定义通常含有类的实例操作方法的定义。

## coercion
在涉及两个相同类型的参数的操作时，实例从一个类型到另一个类型的隐式转换。例如，`int(3.15)`将浮点数转换成整型`3`，但在`3+4.5`中，每一个参数都是一个不同的类型（一个是`int`，另一个是`float`），在把它们加起来之前，都需要转换成相同的类型，否则将会引发一个`TypeError`错误。若没有强制类型转换，程序员需要将所有的参数，甚至是可兼容类型，归一化成相同的值，例如，`float(3)+4.5`，而不是仅仅`3+4.5`。

## 复数
熟知的实数系统的扩展，所有的数字都表示为一个实部和一个虚部的和。虚数是虚数单位（`-1`的平方根）的倍数，数学上常写为`i`，工程学上常写为`j`。Python已经内置对复数的支持，使用后一种表达方式；虚部使用一个后缀`j`，例如，`3+1j`。要通过等效于[math模块](https://docs.python.org/3/library/math.html#module-math)的模块来访问复数，使用cmath。复数的使用时一个相当高级的数学特性。如果你没有意识到需要它们，那几乎可以肯定的说，你可以放心忽略它们。

## 上下文管理器
通过定义[__enter__()](https://docs.python.org/3/reference/datamodel.html#object.__enter__)和[__exit__()](https://docs.python.org/3/reference/datamodel.html#object.__exit__)方法，来控制[with](https://docs.python.org/3/reference/compound_stmts.html#with)语句中的环境的一个对象。见[PEP 343](https://www.python.org/dev/peps/pep-0343).

## contiguous
一个缓冲区如果它是C连续，或者Fortran连续的，那么就认为它是连续的。零维缓冲区是C和Fortran连续的。在一维数组中，内存中的元素必须彼此相邻，以便从零开始增加索引。在多维C连续数组中，当通过内存地址的顺序访问其中的元素时，最后一个索引变化最快。然而，在Fortran连续数组中，第一个索引变化最快。

## 协程(coroutine)
协程时子程序的一个更广义的形式。子程序从一点进入，从另一点退出。协程可以在许多不同点进入，退出和回复。它们可以通过[async def](https://docs.python.org/3/reference/compound_stmts.html#async-def)语句实现。见[PEP 492](https://www.python.org/dev/peps/pep-0492)。

## 协程函数
返回一个[coroutine](https://docs.python.org/3/glossary.html#term-coroutine)对象的函数。一个协程函数可能使用[async def](https://docs.python.org/3/reference/compound_stmts.html#async-def)语句定义，也可能包含[await](https://docs.python.org/3/reference/expressions.html#await), [async for](https://docs.python.org/3/reference/compound_stmts.html#async-for)和[async with](https://docs.python.org/3/reference/compound_stmts.html#async-with)关键字。它们在[PEP 492](https://www.python.org/dev/peps/pep-0492)引入。

## CPython
Python编程语言的规范实现，在python.org上发布。术语“CPython”用于将此实现从其他诸如Jython或IronPython的实现中区分出来。

## 装饰器(decorator)
一个返回其他函数的函数，通常使用`@wrapper`语法作为一个函数变换应用。装饰器的一般例子是[classmethod()](https://docs.python.org/3/library/functions.html#classmethod)和[staticmethod()](https://docs.python.org/3/library/functions.html#staticmethod)。

装饰器语法知识语法糖，下面两个函数定义在语义上是等价的：
```python
def f(...):
    ...
f = staticmethod(f)

@staticmethod
def f(...):
    ...
```
类存在相同的概念，但它不太常使用。请参阅[函数定义](https://docs.python.org/3/reference/compound_stmts.html#function)和[类定义](https://docs.python.org/3/reference/compound_stmts.html#class)以获取更多相关内容。

## 描述器(descriptor)
定义[__get__()](https://docs.python.org/3/reference/datamodel.html#object.__get__), [__set__()](https://docs.python.org/3/reference/datamodel.html#object.__set__)或者[__delete__()]方法的任何对象。当一个类属性是一个描述器时，属性查找会触发它特殊的绑定行为。通常情况下，使用`a.b`来获取，设置或删除一个属性，会在a的类字典中查找名为b的对象，但如果b是一个描述器，则对应的描述器方法被调用。了解描述器是深入了解Python的关键，因为它们是许多特性，包括函数，方法，属性，类方法，静态方法的基础，也是超类的参考。

有关描述器方法的更多信息，请参阅[描述符的实现](https://docs.python.org/3/reference/datamodel.html#descriptors)。

## 字典
一个关联数组，其中任意键映射到值。键可以是任何拥有[__hash__()](https://docs.python.org/3/reference/datamodel.html#object.__hash__)和[__eq__()](https://docs.python.org/3/reference/datamodel.html#object.__eq__)方法的对象。在Perl中称为哈希（hash）。

## 字典视图(dictionary view)
从`dict.keys()`, `dict.values()`和`dict.items()`返回的对象称为字典视图。它们提供了字典条目的一个动态视图，这意味着当字典变化时，视图会反映这些变化。要强制字典视图为一个完整的列表，使用`list(dictview)`。见[字典视图对象](https://docs.python.org/3/library/stdtypes.html#dict-views)。

## 文档字符串(docstring)
作为类，函数或者模块中的第一个表达式显示的字符串文字。执行时忽略，并被编辑器识别，然后放入封装的类，函数或模块的`__doc__`属性。因为它可以通过自省(introspection)访问，它是该对象的文档规范的地方。

## 鸭子类型(duck-typing)
一种编程风格，不看对象的类型来决定它是否有正确的接口，而是简单调用或使用它的方法或属性（“如果它看起来像鸭子，叫起来像鸭子，那么它一定是鸭子。”）。通过强调接口而不是特定的类型，通过多态替换，精心设计的代码可以提高它的灵活性。鸭子类型避免了使用[type()](https://docs.python.org/3/library/functions.html#type)或者`isinstance()`来测试。（注意，然而，可以通过[抽象基类](https://docs.python.org/3/glossary.html#term-abstract-base-class)来补充鸭子类型）。相反的，它通常采用[hasattr()](https://docs.python.org/3/library/functions.html#hasattr)测试，或者[EAFP](https://docs.python.org/3/glossary.html#term-eafp)编程。

## EAFP
更容易获得原谅而不是许可（Easier to ask for forgiveness than permission）。这种常见Python代码风格假定有效键或者属性的存在，并在假设错误的情况下捕获异常。这种干净快速的风格是通过许多[try](https://docs.python.org/3/reference/compound_stmts.html#try)和[except](https://docs.python.org/3/reference/compound_stmts.html#except)语句标识的。这种技术与许多其他常见的语言，例如C，所使用的[LBYL](https://docs.python.org/3/glossary.html#term-lbyl)风格相对立。

## 表达式
可以得出一些值的一段语法。换句话说，一个表达式是表达式元素（例如文字，名字，属性访问，操作符或者函数调用，它们都返回一个值）的聚合。与其他许多语言相比，不是所有的语言结构都是表达式。还有一些不能被用作表达式的[语句](https://docs.python.org/3/glossary.html#term-statement)，例如[if](https://docs.python.org/3/reference/compound_stmts.html#if)。赋值也是语句，而不是表达式。

## 扩展模块
使用C或C++编写的模块，它使用Python的C API与核心core和用户代码进行交互。

## 文件对象(file object)
一个提供可以访问底层资源的面向文件的API（诸如read()或者write()方法）的对象。依据它创建的方式，一个文件对象可访问真实磁盘上的文件，也可以访问其他类型的存储或通信设备（例如，标准输入/输出，内存中缓冲区，socket，管道，等等）。文件对象也称为*类文件对象或流*。

实际上有三类文件对象：原始[二进制文件](https://docs.python.org/3/glossary.html#term-binary-file)，缓存[二进制文件](https://docs.python.org/3/glossary.html#term-binary-file)和[文本文件](https://docs.python.org/3/glossary.html#term-text-file)。它们的接口定义于[io](https://docs.python.org/3/library/io.html#module-io)模块。创建一个文件对象的规范方法是使用[open()](https://docs.python.org/3/library/functions.html#open)函数。

## 类文件对象(file-like object)
[文件对象](https://docs.python.org/3/glossary.html#term-file-object)的一个同义词。

## 查找器(finder)
一个尝试为正在导入的模块找到[加载器](https://docs.python.org/3/glossary.html#term-loader)的对象。

从Python 3.3起，有两种类型的查找器：使用[sys.meta_path](https://docs.python.org/3/library/sys.html#sys.meta_path)的[元路径查找器](https://docs.python.org/3/glossary.html#term-meta-path-finder)，和使用`sys.path_hooks`的[路径项查找器](https://docs.python.org/3/glossary.html#term-path-entry-finder)。

见[PEP 302](https://www.python.org/dev/peps/pep-0302), [PEP 420](https://www.python.org/dev/peps/pep-0420) 和 [PEP 451](https://www.python.org/dev/peps/pep-0451)以查看更多细节。

## 除后向下取整(floor division)
向下四舍五入到最近的整数的数学除法。除后向下取整操作符是//。例如，表达式`11 // 4`结果为`2`，而不是实际浮点除法返回的`2.75`。注意，`(-11) // 4`结果为`-3`，因为这是由`-2.75`向下四舍五入得到的。见[PEP 238](https://www.python.org/dev/peps/pep-0238)。

## 函数
返回一些值给调用者的一系列语句。可以传给它零到多个可用于执行体的[参数](https://docs.python.org/3/glossary.html#term-argument)。另见[参数](https://docs.python.org/3/glossary.html#term-parameter), [方法](https://docs.python.org/3/glossary.html#term-method)和[函数定义](https://docs.python.org/3/reference/compound_stmts.html#function)部分。

## 函数注释
与函数参数或返回值相关联的一个任意元数据值。它的语法在[函数定义](https://docs.python.org/3/reference/compound_stmts.html#function)中阐释。注释可以通过函数对象的特殊属性`__annotations__`访问。

Python自身并没有指定任何特殊含义的功能注释。它们意图在于让第三方库或工具来解释。见[PEP 3107](https://www.python.org/dev/peps/pep-3107)，其中介绍了一些潜在用途。

## __future__
一个伪模块，程序员可以用它来使用与当前解释器不兼容的新的语言特性。

通过导入[__future__](https://docs.python.org/3/library/__future__.html#module-__future__)模块，查看它的变量，你可以看到一个新特性何时第一次添加到语言中，何时它是默认可用的：
```
>>> import __future__
>>> __future__.division
_Feature((2, 2, 0, 'alpha', 2), (3, 0, 0, 'alpha', 0), 8192)
```

## 垃圾收集(garbage collection)
当不再使用时释放内存的过程。Python通过引用计数和环形垃圾收集器（它可以检测和打破循环引用）来进行垃圾收集。

## 生成器(generator)
返回一个[生成器迭代器](https://docs.python.org/3/glossary.html#term-generator-iterator)的函数。它看起来像一个正常的函数，除了包含[yield](https://docs.python.org/3/reference/simple_stmts.html#yield)表达式，以生成可用于for循环或者可以在一个时间内通过[next()](https://docs.python.org/3/library/functions.html#next)函数检索的一系列的值。

通常是指一个生成器函数，但在某些情况下指*生成器迭代器*。在一些指代不明的情况下，使用完整的术语以避免歧义。

## 生成器迭代器(generator iterator)
一个由[生成器](https://docs.python.org/3/glossary.html#term-generator)函数创建的对象。

每一个[yield](https://docs.python.org/3/reference/simple_stmts.html#yield)临时挂起处理，记录执行位置的状态（包括局部变量和挂起的try语句）。当生成器迭代器恢复时，它从它离开的地方继续执行（对比于每次调用时重新开始的函数）。

## 生成器表达式
返回一个迭代器的表达式。它看起来像一个正常的表达式，后面跟着一个[for](https://docs.python.org/3/reference/compound_stmts.html#for)表达式限定一个循环变量，范围和一个可选的[if](https://docs.python.org/3/reference/compound_stmts.html#if)表达式。合并后的表达式为为一个封闭函数生成值：
```
>>> sum(i*i for i in range(10))         # sum of squares 0, 1, 4, ... 81
285
```

## 泛型函数(generic function)
多个为不同的类型实现相同操作的函数组成的一个函数。调用时使用哪一个实现应由调度算法来决定。

见[单一调度](https://docs.python.org/3/glossary.html#term-single-dispatch)词条，[functools.singledispatch()](https://docs.python.org/3/library/functools.html#functools.singledispatch)装饰器和[PEP 443](https://www.python.org/dev/peps/pep-0443)。

## GIL
参见[global interpreter lock](https://docs.python.org/3/glossary.html#term-global-interpreter-lock).

## 全局解释锁(global interpreter lock)
[CPython](https://docs.python.org/3/glossary.html#term-cpython)解释器用来确保同一个时间内只有一个线程执行Python字节码的机制。它通过对象模型（包括关键的内置类型，例如[dict](https://docs.python.org/3/library/stdtypes.html#dict)）对并发访问的隐式安全来简化Cpython的实现。以多处理器计算机提供的平行度为代价，锁定整个解释器使得解释器更容易实现多线程。

然而，一些扩展模块，无论是标准或者第三方，被设计成在进行计算密集型任务，例如压缩或散列时，释放GIL。此外，在进行I/O时，也总是释放GIL。

过去创建一个“自由线程”（在更精细的粒度上锁定共享数据）的努力都没有成功，因为在常见的单处理器情况下性能会受到影响。人们认为,克服此性能问题会令实现更加复杂，并因此更难以维护。

## 可哈希(hashable)
当一个对象有一个在其生命周期内永不改变的散列值（需要一个[__hash__()](https://docs.python.org/3/reference/datamodel.html#object.__hash__)方法），并且可以与其他对象进行比较（需要一个[__eq__()](https://docs.python.org/3/reference/datamodel.html#object.__eq__)方法），那么这个对象就是可哈希的。相等的可哈希对象必须具有相同的散列值。

哈希性使对象可用作字典键和集合对象，因为这些数据结构内部使用哈希值。

所有Python不可变内置对象都是可哈希的，而可变容器（例如列表或字典）都不是。用户自定义类的实例对象默认是可哈希的；除了自身外，它们相互之间不等，且它们的哈希值是来自于它们的`id()`。

## IDLE
Integrated Development Environment for Python，Python集成开发环境。IDLE是一个基本的编辑器和解释器环境，与Python的标准发布一起发布。

## 不可变(immutable)
具有固定值的对象。不可变对象包括数字，字符串和元组。这些对象都是不能被改变的。如果要储存一个不同的值，那么必须创建一个新的对象。它们在需要一个恒定的哈希值的时候扮演着重要的角色，例如，作为字典中的一个键。

## 导入路径
用于导入模块的[基于路径的查找器](https://docs.python.org/3/glossary.html#term-path-based-finder)搜索的位置（或路径条目）列表。在导入过程中，这个位置列表通常来自于[sys.path](https://docs.python.org/3/library/sys.html#sys.path)，但对于子包，也可能来自于其父包的`__path__`属性。

## 导入(importing)
将一个模块中的Python代码提供给另一个模块中的Python代码使用的过程。

## 导入器(importer)
既查找又加载一个模块的一个对象；既是一个查找器，也是一个加载器对象。

## 交互式(interactive)
Python有一个交互式解释器，这意味着你可以在解释器的提示符下输入语句和表达式，然后立即执行它们，查看它们的结果。只要不带任何参数启动`python`（也可能从你的计算机的主菜单上选择它）。这是一种非常强大的测试新的想法或者检查模块和包（记住`help(x)`）的方法。

## 解释性(interpreted)
对比于编译性语言，Python是一种解释性语言，虽然由于字节码编译器存在，它们之间的区别是模糊的。这意味着源文件可以直接运行，而不用显式创建一个稍后运行的可执行文件。解释性语言通常有一个比编译性语言短的开发/调试周期，虽然它们的程序也相对跑得慢一点。另见[交互式](https://docs.python.org/3/glossary.html#term-interactive)。

## 解释器关闭
当提出关闭时，Python解释器进入一个特殊的阶段，它会逐渐释放所有分配的资源，例如模块和各种重要的内部结构。它还调用[垃圾收集器](https://docs.python.org/3/glossary.html#term-garbage-collection)。这可以触发用户定义的析构器或者weakref回调的代码的执行。由于它所依赖的资源可能无法正常工作（常见的例子是库模块或者告警机制），在关闭阶段执行的代码可能会遇到各种异常。

解释器关闭的主要原因是`__main__`模块或者正在运行的脚本已经执行完毕。

## 可迭代（iterable）
能够一次返回一个成员的对象。可迭代对象的例子包括所有的序列类型（例如，[list](https://docs.python.org/3/library/stdtypes.html#list), [str](https://docs.python.org/3/library/stdtypes.html#str)和[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)）和一些非序列类型，例如[dict](https://docs.python.org/3/library/stdtypes.html#dict), [文件对象](https://docs.python.org/3/glossary.html#term-file-object),及定义了[__iter__()](https://docs.python.org/3/reference/datamodel.html#object.__iter__)或[__getitem__()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__)方法的任意类对象。可迭代对象可以用于[for](https://docs.python.org/3/reference/compound_stmts.html#for)循环和其他需要序列的地方（[zip()](https://docs.python.org/3/library/functions.html#zip), [map()](https://docs.python.org/3/library/functions.html#map), ...）。当将一个可迭代对象作为参数传递给内置的[iter()](https://docs.python.org/3/library/functions.html#iter)函数时，它返回对象的一个迭代器。这个迭代器对于传递一组值来说是极好的。当使用可迭代对象时，通常不需要调用[iter()](https://docs.python.org/3/library/functions.html#iter)，或者自己处理迭代器对象。`for`语句会自动为你完成这些，它在循环期间创建一个临时的未命名变量保存迭代器。见[iterator](https://docs.python.org/3/glossary.html#term-iterator), [sequence](https://docs.python.org/3/glossary.html#term-sequence)和[generator](https://docs.python.org/3/glossary.html#term-generator)。

## 迭代器(iterator)
一个表示数据流的对象。重复调用迭代器的[__next__()](https://docs.python.org/3/library/stdtypes.html#iterator.__next__)方法（或将其传递给内置的[next()](https://docs.python.org/3/library/functions.html#next)函数），会返回流中的连续项。当没有更多可用的数据时，则引发[StopIteration](https://docs.python.org/3/library/exceptions.html#StopIteration)异常。此时，该迭代对象已被用尽，后续对它的`__next__()`方法的调用会重新引发[StopIteration](https://docs.python.org/3/library/exceptions.html#StopIteration)异常。迭代器都需要一个[__iter__()](https://docs.python.org/3/reference/datamodel.html#object.__iter__)方法，此方法返回迭代器对象自身以使得每一个迭代器都是可迭代的，并可以在大多数接受可迭代对象的地方使用。一个值得注意的异常是试图通过多次迭代的代码。一个容器对象（例如[列表](https://docs.python.org/3/library/stdtypes.html#list)）在每次将其传递给[iter()](https://docs.python.org/3/library/functions.html#iter)函数，或用于[for](https://docs.python.org/3/reference/compound_stmts.html#for)循环时都会生成一个新的迭代器。使用迭代器进行上述尝试则将只返回前面迭代使用过的相同的已耗尽迭代对象，使得它看起来像一个空的容器。

更多信息，可参见[迭代器类型](https://docs.python.org/3/library/stdtypes.html#typeiter)。

## 键函数（key function）
一个键函数或排序函数是一个返回用于排序的值的可调用对象。例如，[locale.strxfrm()](https://docs.python.org/3/library/locale.html#locale.strxfrm)用于生成考虑区域特定排序约定的排序键。

Python中的许多工具接受键函数来控制元素如何排序或分组，包括[min()](https://docs.python.org/3/library/functions.html#min), [max()](https://docs.python.org/3/library/functions.html#max), [sorted()](https://docs.python.org/3/library/functions.html#sorted), list.sort(), heapq.merge(), heapq.nsmallest(), heapq.nlargest(), 和 itertools.groupby()。

有几种方法来创建一个键函数。例如，[str.lower()](https://docs.python.org/3/library/stdtypes.html#str.lower)方法可以作为不区分大小写排序的一个键函数。可替代地，可以用[lambda](https://docs.python.org/3/reference/expressions.html#lambda)表达式构建键函数，例如,`lambda r: (r[0], r[2])`。此外，[operator模块](https://docs.python.org/3/library/operator.html#module-operator)提供了三个键函数构造器：attrgetter(), itemgetter(), 和 methodcaller()。请参考[Sorting HOW TO](https://docs.python.org/3/howto/sorting.html#sortinghowto)以查看关于如何创建和使用键函数的例子。

## 关键字参数（keyword argument）
见[参数argument](https://docs.python.org/3/glossary.html#term-argument).

## lambda
由一个单一表达式组成的匿名内联函数，在函数被调用时才进行计算。创建一个lambda函数的语法是：`lambda [arguments]: expression`

## LBYL
Look before you leap，三思而后行。这种代码风格在调用或查询之前显示的测试先决条件。这种风格与[EAFP](https://docs.python.org/3/glossary.html#term-eafp)方法相比，其特征是包含了许多[if](https://docs.python.org/3/reference/compound_stmts.html#if)语句。

在多线程环境中，LBYL方法会有引进“查找”和“跨越”之间的竞争的风险。例如，如果在测试后查找前，有其他线程从映射中移除了键，那么代码，`if key in mapping: return mapping[key]`则可能失败。这个问题是可以通过锁或者使用EAFP方法解决的。

## 列表(list)
内置的Python[序列](https://docs.python.org/3/glossary.html#term-sequence)。虽然它叫这个名字，但是因为访问元素的时间复杂度为O(1)，所以它更像其他语言中的数组而不是链表。

## 列表解析(list comprehension)
一种处理序列中所有或部分元素并返回一个结果列表的简洁方式。`result = ['{:#04x}'.format(x) for x in range(256) if x % 2 == 0]`生成一个字符串列表，这个列表包含从0到255的偶数的十六进制数字(0x..)。[if](https://docs.python.org/3/reference/compound_stmts.html#if)子句是可选的。如果省略它，则将处理所有在`range(256)`中的元素。

## 加载器(loader)
加载模块的一个对象。它必须定义一个名为`load_module()`的方法。加载器通常由[查找器(finder)](https://docs.python.org/3/glossary.html#term-finder)返回。查看[PEP 302](https://www.python.org/dev/peps/pep-0302)以查看相关细节，查看[importlib.abc.Loader](https://docs.python.org/3/library/importlib.html#importlib.abc.Loader)获取一个[抽象基类](https://docs.python.org/3/glossary.html#term-abstract-base-class)的信息。

## 映射
支持任意键查找和实现[Mapping](https://docs.python.org/3/library/collections.abc.html#collections.abc.Mapping)或[MutableMapping](https://docs.python.org/3/library/collections.abc.html#collections.abc.MutableMapping)[抽象基类](https://docs.python.org/3/library/collections.abc.html#collections-abstract-base-classes)中指定方法的一个容器对象。例子有，[dict](https://docs.python.org/3/library/stdtypes.html#dict), [collections.defaultdict](https://docs.python.org/3/library/collections.html#collections.OrderedDict), [collections.OrderedDict](https://docs.python.org/3/library/collections.html#collections.OrderedDict) 和 collections.Counter.

## 元路径查找器(meta path finder)
通过搜索`sys.meta_path`返回的一个[查找器](https://docs.python.org/3/glossary.html#term-finder)。元路径查找器与[路径项查找器](https://docs.python.org/3/glossary.html#term-path-entry-finder)相关，但异于[路径项查找器](https://docs.python.org/3/glossary.html#term-path-entry-finder)。

见[importlib.abc.MetaPathFinder](https://docs.python.org/3/library/importlib.html#importlib.abc.MetaPathFinder)中元路径查找器实现的方法。

## 元类(metaclass)
类的类。类定义创建一个类名，一个类字典，和一个基类列表。元类负责接收这三个参数，然后创建类。大多数的面向对象的语言都提供了一个默认的实现。Python的特别之处在于，它可以创建自定义的元类。大多数的用户都不需要这个工具，但在有需要的时候，元类能够提供强大，优雅的解决方法。它们已用于访问登录属性，增加线程安全，跟踪对象创建，实现单件，及其他任务。

更多信息，查看[自定义类创建](https://docs.python.org/3/reference/datamodel.html#metaclasses)。

## 方法(method)
类中定义的函数。如果被该类的一个实例作为一个属性调用，该方法会将此实例对象作为其第一个[参数](https://docs.python.org/3/glossary.html#term-argument)（通常是`self`）。请参阅[函数](https://docs.python.org/3/glossary.html#term-function)和[嵌套作用域](https://docs.python.org/3/glossary.html#term-nested-scope)。

## 方法解析顺序
方法解析顺序指的是搜索过程中查找基类成员的顺序。见[Python 2.3方法解析顺序](https://www.python.org/download/releases/2.3/mro/)。

## 模块(module)
用作Python代码组织单位的对象。模块拥有一个包含任意Python对象的命名空间。模块通过[导入](https://docs.python.org/3/glossary.html#term-importing)加载到Python中。

另请参见[包](https://docs.python.org/3/glossary.html#term-package)。

## 模块规格（module spec）
一个包含用于加载一个模块的导入相关的信息的命名空间。`importlib.machinery.ModuleSpec`的一个是咧。

## MRO
见[方法解析顺序](https://docs.python.org/3/glossary.html#term-method-resolution-order)

## 可变(mutable)
可变对象可以在保持自己的`id()`的情况下修改自身的值。另见[不可变immutable](https://docs.python.org/3/glossary.html#term-immutable)。

## 命名元组(named tuple)
类元组类，其可索引属性可通过命名属性访问（例如，[time.localtime()](https://docs.python.org/3/library/time.html#time.localtime)返回一个类元组对象，其中，*年份*可以通过索引，例如`t[0]`，访问，也可以通过命名属性，例如`t.tm_year`访问）。

一个命名元组可以是内置类型，例如[time.struct_time](https://docs.python.org/3/library/time.html#time.struct_time)，或者可以通过常规的类定义来创建。也可以使用工厂函数`collections.namedtuple()`创建一个功能齐全的命名元组。后者会自动提供额外的功能，例如，自文档表示，`Employee(name='jones', title='programmer')`。

## 命名空间
变量存储的地方。命名空间被实现为字典。有关于对象（方法）的局部，全局和内置命名空间，还有嵌套命名空间。命名空间通过防止命名冲突来支持模块化。例如，函数[builtins.open](https://docs.python.org/3/library/functions.html#open)和[os.open()](https://docs.python.org/3/library/os.html#os.open)可以通过它们的命名空间加以区分。命名空间通过明确模块实现的函数来协助其可读性和可维护性。例如，[random.seed()](https://docs.python.org/3/library/random.html#random.seed)或者[itertools.islice()](https://docs.python.org/3/library/itertools.html#itertools.islice)明确指出，那些函数是分别由[random](https://docs.python.org/3/library/random.html#module-random)和[itertools](https://docs.python.org/3/library/itertools.html#module-itertools)模块实现的

## 命名空间包(namespace package)
一个仅为子包充当容器的[PEP 420](https://www.python.org/dev/peps/pep-0420)[包](https://docs.python.org/3/glossary.html#term-package)。命名空间包可能没有物理表示，特别是不像一个[常规包](https://docs.python.org/3/glossary.html#term-regular-package)，因为它们并无`__init__.py`文件。

另见[模块](https://docs.python.org/3/glossary.html#term-module)。

## 嵌套作用域
在一个封闭定义中引用变量的能力。例如，函数中定义的一个函数可以引用它外部函数的变量。注意，默认的嵌套作用域仅对引用有效，对赋值无效。局部变量在内部作用域中可读可写。同样的，全局作用域在全局命名空间中可读可写。[非局部变量](https://docs.python.org/3/reference/simple_stmts.html#nonlocal)允许写入外部作用域。

## 新型类(new-style class)
类风味的旧名现在用于所有的类对象。在早期的Python版本中，只有新型类可以使用Python较新且灵活的功能，例如，[__slots__](https://docs.python.org/3/reference/datamodel.html#object.__slots__)，描述器，属性，[__getattribute__()](https://docs.python.org/3/reference/datamodel.html#object.__getattribute__)，类方法和静态方法。

## 对象(object)
任何具备状态（属性或值）和定义的行为（方法）的数据。另外，也是任何[新式类](https://docs.python.org/3/glossary.html#term-new-style-class)的最终基类。

## 包(package)
一个Python模块，它可以包含子模块或递归子包。从技术上说，一个包是一个具有`__path__`属性的Python模块。

另请参见[regular package](https://docs.python.org/3/glossary.html#term-regular-package) 和 [命名空间包namespace package](https://docs.python.org/3/glossary.html#term-namespace-package).

## 参数(parameter)
[函数](https://docs.python.org/3/glossary.html#term-function)或方法定义中的一个命名实体，它指定了该函数接受的一个[参数(argument)](https://docs.python.org/3/glossary.html#term-argument)（或在某些情况下，多个参数）。有五种参数：

* 位置或关键字参数(Positional-or-keyword parameter)：指定一个既可以[通过位置](https://docs.python.org/3/glossary.html#term-argument)，又可以作为[关键字参数](https://docs.python.org/3/glossary.html#term-argument)传递的参数。这是参数的默认类型，例如下面的`foot`和`bar`：

    `def func(foo, bar=None): ...`

* 仅位置参数(Positional-only parameter)：指定一个只能通过位置参数传递的方式传递的参数。Python没有定义仅位置参数的语法。然而，一些内置函数拥有仅位置参数（例如，`abs()`）。

* 仅关键字参数(keyword-only)：指定一个只能通过关键字提供的参数。仅关键字参数可以通过包含一个单一的任意数量的位置参数或者在函数定义的参数列表前放一个`*`来定义，例如，下面的*kw_only1 和 kw_only2*：

    `def func(arg, *, kw_only1, kw_only2): ...`

* 任意数量的位置参数(var-positional): 指定一个可以提供任意序列的位置参数（除了任何已被其他参数接收的位置参数）。这样的参数可以通过在参数名前加`*`来定义，例如下面的`args`：

    `def func(*args, **kwargs): ...`

* 任意数量的关键字参数(var-keyword): 指定可以提供任意多的关键字参数（除了任何已被其他参数接收的关键字参数）。这样的参数可以通过在参数名前加`**`来定义，例如上面例子中的`kwargs`。

可以同时指定可选参数和必传参数，也可以为一些可选参数指定默认值。

同时参考[参数(argument)](https://docs.python.org/3/glossary.html#term-argument)词条，关于[参数(argument)和参数(parameter)之间的差异](https://docs.python.org/3/faq/programming.html#faq-argument-vs-parameter)的常见问题，[inspect.Parameter](https://docs.python.org/3/library/inspect.html#inspect.Parameter)类，[函数定义](https://docs.python.org/3/reference/compound_stmts.html#function)部分，及[PEP 362](https://www.python.org/dev/peps/pep-0362)。

## 路径项
[导入路径](https://docs.python.org/3/glossary.html#term-import-path)上的一个单一位置，[基于路径的查找器](https://docs.python.org/3/glossary.html#term-path-based-finder)协商查找模块用以导入。

## 路径项查找器
[sys.path_hooks](https://docs.python.org/3/library/sys.html#sys.path_hooks)上的一个可调用对象（例如，一个[路径项钩子(path entry hook)](https://docs.python.org/3/glossary.html#term-path-entry-hook)）返回的一个[查找器](https://docs.python.org/3/glossary.html#term-finder)，它知道如何根据提供的[路径项](https://docs.python.org/3/glossary.html#term-path-entry)定位模块。

见[importlib.abc.PathEntryFinder](https://docs.python.org/3/library/importlib.html#importlib.abc.PathEntryFinder)以查看路径项查找器实现的方法。

## 路径项钩子(path entry hook)
`sys.path_hook`列表上的一个可调用对象，若它知道如何在一个指定的[路径项](https://docs.python.org/3/glossary.html#term-path-entry)上查找模块，那么它返回一个[路径项查找器](https://docs.python.org/3/glossary.html#term-path-entry-finder)。

## 基于路径的查找器(path based finder)
为模块搜索[导入路径](https://docs.python.org/3/glossary.html#term-import-path)的默认[元路径查找器](https://docs.python.org/3/glossary.html#term-meta-path-finder)之一。

## portion
有助于命名空间包的单个目录中的文件集合（可能存储在一个zip文件中）。在[PEP 420](https://www.python.org/dev/peps/pep-0420)中定义。

## 位置参数(positional argument)
见[参数argument](https://docs.python.org/3/glossary.html#term-argument).

## 临时APIprovisional API
临时API是一种被故意排除在标准库的向后兼容性保证的API。当不期望对这样的接口做重大修改时，只要把它们标记成临时的(provisional)，如果核心开发者认为有必要，向后不兼容修改（向上接口及包括接口的移除）则可能发生。这样的修改不会无缘无故的进行 - 只有在列入API前错过发现严重的根本缺陷，它们才会发生。

即使是临时API，向后不兼容修改也被看做是一个“最后的解决方案” - 对于任何发现的问题，都会尽全力尝试找到一个向后兼容的方法。

此过程允许标准库随着时间的推移持续的推进，而不被设计错误锁定 。更多细节，见[PEP 411](https://www.python.org/dev/peps/pep-0411)。

## 临时包(provisional package)
见[临时API](https://docs.python.org/3/glossary.html#term-provisional-api).

## Python 3000
Python 3.x版本线的昵称 (在版本3的发布还灰常遥远的时候创造的)。也简写为“Py3k”。

## Pythonic
紧随Python语言中最常用习语的一个想法或一段代码，而不是使用其他语言共有的概念来实现代码。例如，一个常见的Python习惯是使用[for](https://docs.python.org/3/reference/compound_stmts.html#for)语句来遍历可迭代对象中的所有元素。许多其他的语言并不具备这类型的结构，所以不熟悉Python的人们有时会使用一个数字计数器来代替：
```
for i in range(len(food)):
    print(food[i])
```
相对于更简洁的Pythonic方法:
```
for piece in food:
    print(piece)
```

## 限定名(qualified name)
一个带点名称，显示从一个模块的全局范围到模块中定义的一个类，函数或方法的“路径”。在[PEP 3155](https://www.python.org/dev/peps/pep-3155)中定义。对于顶级函数和类，限定名与对象的名字相同：
```
>>> class C:
...     class D:
...         def meth(self):
...             pass
...
>>> C.__qualname__
'C'
>>> C.D.__qualname__
'C.D'
>>> C.D.meth.__qualname__
'C.D.meth'
```
当用来指代模块时，*完整的限定名*意味到模块的整个带点路径，包括任何父包，例如`email.mime.text`：
```
>>> import email.mime.text
>>> email.mime.text.__name__
'email.mime.text'
```

## 引用计数
一个对象的引用数目。当一个对象的引用数目将为0时，它被释放。引用计数一般对Python代码不可见，但是它是[CPython](https://docs.python.org/3/glossary.html#term-cpython)实现的关键元素。[sys](https://docs.python.org/3/library/sys.html#module-sys)模块定义了一个[getrefcount()](https://docs.python.org/3/library/sys.html#sys.getrefcount)函数，程序员可以调用其来返回特定对象的引用计数。

## 常规包
一个传统[包](https://docs.python.org/3/glossary.html#term-package)，例如一个包含`__init__.py`文件的目录。

另请参加[命名空间包](https://docs.python.org/3/glossary.html#term-namespace-package)。

## __slots__
类中的一个声明，它通过为实例属性预先声明空间和消除实例字典来节省内存。虽然流行，该技术却有点难正确使用，所以最好只在那些存储关键应用中有大量实例的极少数情况下使用。

## 序列
一个支持通过特殊方法[__getitem__()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__)使用整型切片访问有效元素，同时支持定义返回序列长度的[__len__()](https://docs.python.org/3/reference/datamodel.html#object.__len__)方法的[可迭代对象](https://docs.python.org/3/glossary.html#term-iterable)。内建的序列类型有[list](https://docs.python.org/3/library/stdtypes.html#list), [str](https://docs.python.org/3/library/stdtypes.html#str), [tuple](https://docs.python.org/3/library/stdtypes.html#tuple), 及 [bytes](https://docs.python.org/3/library/functions.html#bytes)。注意，[dict](https://docs.python.org/3/library/stdtypes.html#dict)也支持[__getitem__()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) 和 [__len__()](https://docs.python.org/3/reference/datamodel.html#object.__len__)，但它被映射，而不是序列，因为它通过[可变](https://docs.python.org/3/glossary.html#term-immutable)键查找，而不是 

[collections.abc.Sequence](https://docs.python.org/3/library/collections.abc.html#collections.abc.Sequence)抽象基类定义了一个更丰富的接口，它不只有[__getitem__()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) 和 [__len__()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__)，还增加了count(), index(), [__contains__()](https://docs.python.org/3/reference/datamodel.html#object.__contains__), 和 [__reversed__()](https://docs.python.org/3/reference/datamodel.html#object.__reversed__)。可以显示的使用`register()`登记实现这个扩展接口的类型。

## 单一调度(single dispatch)
[泛型函数](https://docs.python.org/3/glossary.html#term-generic-function)调度的一种形式，它选择基于单一参数类型的实现。

## 切片(slice)
一个对象通常含有[序列](https://docs.python.org/3/glossary.html#term-sequence)的一部分。使用下标符号，[]及给出的数字之间的冒号，可以创建一个切片，例如`variable_name[1:3:5]`。括号（下标）符号内部使用[切片](https://docs.python.org/3/library/functions.html#slice)对象。

## special method
Python隐式调用来执行一个类型上某些特定操作（例如加法）的方法。这样的方法的名称以双下划线开始和结尾。特殊方法通过[特殊方法名](https://docs.python.org/3/reference/datamodel.html#specialnames)记录。

## 语句
一个语句是一个块（代码块）的一部分。一个语句要么是一个表达式，要么是一个由关键字[if](https://docs.python.org/3/reference/compound_stmts.html#if), [while](https://docs.python.org/3/reference/compound_stmts.html#while)或者[for](https://docs.python.org/3/reference/compound_stmts.html#for)构造

## 结构序列(struct sequence)
一个命名元素的元组。结构序列有一个类似于命名元组的接口：元组中的元素可以通过下标或者作为一个属性访问。然而，它们不具有任何命名元组的方法（例如[_make()](https://docs.python.org/3/library/collections.html#collections.somenamedtuple._make)或 _asdict()）。例如，[sys.float_info](https://docs.python.org/3/library/sys.html#sys.float_info)和`os.stat()`的返回值。

## 文本编码
一个将Unicode字符串编码成字节的编解码器(codec)。

## 文本文件(Text File)
一个能够读取和写入str对象的文件对象。通常情况下，一个文本文件实际访问一个面向字节的数据流，并自动处理[文本编码](https://docs.python.org/3/glossary.html#term-text-encoding)。

> 另见 一个[二进制文件](https://docs.python.org/3/glossary.html#term-binary-file)读写[字节对象](https://docs.python.org/3/library/functions.html#bytes)。

## 三引号字符串
一个由引号(")或者单引号(')的三个实例包围的字符串。虽然它们并未提供任何单引号字符串不具备的功能，但是它们确实很有用的。它们允许你在一个字符串中包含转义单引号和双引号，允许你跨越多行而不使用连字符，这使得在编写文档字符串(docstring)时特别好用。

## 类型
一个Python对象的类型决定了它是一个怎样的对象；每一个对象都具有一个类型。一个对象的类型可以通过其`__class__`属性访问，或者可以使用`type(obj)`进行检索。

## 通用换行符
在解析文本流的方式中，以下均被认为行尾：Unix的行尾'\n', Windows约定 '\r\n', 以及老的Macintosh约定 '\r'. 查看 [PEP 278](https://www.python.org/dev/peps/pep-0278) 和 [PEP 3116](https://www.python.org/dev/peps/pep-3116), 以及[bytes.splitlines()](https://docs.python.org/3/library/stdtypes.html#bytes.splitlines)获取额外用途。

## 虚拟环境
一个协同孤立的运行时环境，它允许Python用户和应用，在不受同一个系统上运行的其他Python应用干扰的情况下，安装及升级Python发行包。

另见[pyvenv - 创建虚拟环境](https://docs.python.org/3/using/scripts.html#scripts-pyvenv).

## 虚拟机
完全由软件定义的计算机。Python虚拟机执行由字节码编译器发出的字节码。

## Zen of Python (Python之禅)
Python设计原则和理念列表，有助于理解和使用这门语言。此列表可以用过在交互提示符中输入`import this`来查看。