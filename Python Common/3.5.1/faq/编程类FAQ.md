# 编程类FAQ

原文： [Programming FAQ](https://docs.python.org/3/faq/programming.html)

---
<!-- MarkdownTOC -->

- [一般问题](#一般问题)
    - [是否有断点，单步等源代码级调试器?](#是否有断点，单步等源代码级调试器)
    - [是否有一种工具来帮助发现错误或执行静态分析?](#是否有一种工具来帮助发现错误或执行静态分析)
    - [我怎样才能从Python脚本中创建一个独立二进制文件?](#我怎样才能从python脚本中创建一个独立二进制文件)
    - [Python程序是否有编码标准或风格指南?](#python程序是否有编码标准或风格指南)
- [核心语言](#核心语言)
    - [当变量有值时，为什么我会得到一个UnboundLocalError错误?](#当变量有值时，为什么我会得到一个unboundlocalerror错误)
    - [Python中的局部和全局变量规则是什么?](#python中的局部和全局变量规则是什么)
    - [为什么在一个循环中用不同的值定义的lambda都返回相同的结果?](#为什么在一个循环中用不同的值定义的lambda都返回相同的结果)
    - [如何跨模块共享全局变量?](#如何跨模块共享全局变量)
    - [什么是在模块中使用import的“最佳实践?](#什么是在模块中使用import的“最佳实践)
    - [为什么对象之间共享默认值?](#为什么对象之间共享默认值)
    - [如何将可选或关键字参数从一个函数传递到另一个?](#如何将可选或关键字参数从一个函数传递到另一个)
    - [参数arugment和参数parameter之间有何区别?](#参数arugment和参数parameter之间有何区别)
    - [为什么修改列表‘y’的同时也修改了列表‘x’?](#为什么修改列表‘y’的同时也修改了列表‘x’)
    - [如何编写一个拥有输出参数的函数（通过引用调用）?](#如何编写一个拥有输出参数的函数（通过引用调用）)
    - [如何在Python中编写高阶函数?](#如何在python中编写高阶函数)
    - [如何拷贝Python中的对象?](#如何拷贝python中的对象)
    - [如何查找一个对象的方法或属性?](#如何查找一个对象的方法或属性)
    - [如何让我的代码发现一个对象的名称?](#如何让我的代码发现一个对象的名称)
    - [逗号运算符的优先级是怎么回事?](#逗号运算符的优先级是怎么回事)
    - [是否有与C的“?:”等价的三元运算符?](#是否有与c的“”等价的三元运算符)
    - [Python中，有没有可能编写混淆的一行话?](#python中，有没有可能编写混淆的一行话)
- [数字和字符串](#数字和字符串)
    - [如何制定十六进制和八进制整型?](#如何制定十六进制和八进制整型)
    - [为什么-22 // 10返回-3?](#为什么-22--10返回-3)
    - [如何将字符串转换为数字?](#如何将字符串转换为数字)
    - [如何将数字转换成字符串?](#如何将数字转换成字符串)
    - [如何就地修改一个字符串?](#如何就地修改一个字符串)
    - [如何使用字符串来调用函数/方法?](#如何使用字符串来调用函数方法)
    - [是否有与Perl的chomp()等价的函数/方法来删除字符串的尾随换行符?](#是否有与perl的chomp等价的函数方法来删除字符串的尾随换行符)
    - [是否有与scanf() 或 sscanf()等价的函数/方法?](#是否有与scanf-或-sscanf等价的函数方法)
    - [‘UnicodeDecodeError’或者‘UnicodeEncodeError’错误意味着什么?](#‘unicodedecodeerror’或者‘unicodeencodeerror’错误意味着什么)
- [性能](#性能)
    - [我的程序太慢了。如何加速?](#我的程序太慢了。如何加速)
    - [将许多字符串连起来的最有效的方法是什么?](#将许多字符串连起来的最有效的方法是什么)
- [序列 (元组/列表)](#序列-元组列表)
    - [如何在元组和列表之间进行转换?](#如何在元组和列表之间进行转换)
    - [什么是负索引?](#什么是负索引)
    - [如何以相反的顺序遍历序列?](#如何以相反的顺序遍历序列)
    - [如何从列表中删除重复元素?](#如何从列表中删除重复元素)
    - [如何在Python中创建一个数组?](#如何在python中创建一个数组)
    - [如何创建一个多维列表?](#如何创建一个多维列表)
    - [如何对一个对象序列应用一个方法?](#如何对一个对象序列应用一个方法)
    - [为什么当使用加号时，a_tuplei +=](#‘item’)
- [字典](#字典)
    - [如何获得一个存储的字典，并且将其键按照一致顺序展示?](#如何获得一个存储的字典，并且将其键按照一致顺序展示)
    - [我想进行一个复杂排序：可以在Python进行Schwartzian变换吗?](#我想进行一个复杂排序：可以在python进行schwartzian变换吗)
    - [如何根据另一个列表的值排序一个列表?](#如何根据另一个列表的值排序一个列表)
- [对象](#对象)
    - [什么是类?](#什么是类)
    - [什么是方法?](#什么是方法)
    - [什么是self?](#什么是self)
    - [如何检查一个对象是否为给定类或其子类的实例?](#如何检查一个对象是否为给定类或其子类的实例)
    - [什么是委托(delegation)?](#什么是委托delegation)
    - [如何在派生类中调用其已经覆盖的基类中的方法?](#如何在派生类中调用其已经覆盖的基类中的方法)
    - [如何组织代码，以便更容易修改基类?](#如何组织代码，以便更容易修改基类)
    - [如何创建静态类数据和静态类方法?](#如何创建静态类数据和静态类方法)
    - [Python中如何重载构造函数（或方法）?](#python中如何重载构造函数（或方法）)
    - [在尝试使用__spam时，得到了一个关于_SomeClassName__spam的错误.](#在尝试使用__spam时，得到了一个关于_someclassname__spam的错误)
    - [我的类中定义了 __del__，但在删除对象的时候并不调用此方法.](#我的类中定义了-__del__，但在删除对象的时候并不调用此方法)
    - [如何获得一个给定类中所有实例的列表?](#如何获得一个给定类中所有实例的列表)
    - [为什么id()返回的结果看起来不唯一?](#为什么id返回的结果看起来不唯一)
- [模块](#模块)
    - [如何创建一个.pyc文件?](#如何创建一个pyc文件)
    - [如何查找当前模块的名称?](#如何查找当前模块的名称)
    - [如何让模块彼此互相导入?](#如何让模块彼此互相导入)
    - [__import__(‘x.y.z’) 返回 ; 如何获得z?](#__import__‘xyz’-返回--如何获得z)
    - [当我编辑一个导入的模块并重新导入它，所做的更改并不会显示处理。为什么会出现这种情况?](#当我编辑一个导入的模块并重新导入它，所做的更改并不会显示处理。为什么会出现这种情况)

<!-- /MarkdownTOC -->

## 一般问题
### 是否有断点，单步等源代码级调试器?
有。

pdb模块是一个简单但已足够可用的Python控制台模式的调试器。它是Python标准库的一部分，并被记录在[库参考手册](https://docs.python.org/3/library/pdb.html#module-pdb)中。你也可以通过使用pdb代码的例子来编写自己的调试器。

作为Python标准发行版本（通常可作为工具/脚本/idle）的一部分，IDLE交互式开发环境包含了一个图形调试器。

PythonWin是一个包含了基于pdb的GUI调试器的Python IDE。PythonWin调试器拥有色彩丰富的断点及不少很酷的功能，例如调试非Pythonwin程序。Pythonwin可以作为[Python的Windows扩展项目](http://sourceforge.net/projects/pywin32/)的一部分，也可以作为ActivePython发行版本的一部分（见http://www.activestate.com/activepython）。

[Boa构造器](http://boa-constructor.sourceforge.net/)是一个使用wxWidgets的IDE和GUI构建器。它提供了可视化框架的创建和操作，对象检查，源代码视图，例如对象浏览器，继承层次，文档字符串生成的HTML文档，高级调试器，集成帮助，和Zope支持。

[Eric](http://eric-ide.python-projects.org/)是建立在PyQt和Scintilla编辑组件上的IDE。

Pydb是Python标准调试器pdb的一个版本，使用DDD(Data Display Debugger，数据显示调试器)，一个流行的图形调试器前端。Pydb见http://bashdb.sourceforge.net/pydb/，DDD见http://www.gnu.org/software/ddd。

还有一些包含图形调试器的商用Python IDE。它们包括：

* Wing IDE (http://wingware.com/)
* Komodo IDE (http://komodoide.com/)
* PyCharm (https://www.jetbrains.com/pycharm/)

### 是否有一种工具来帮助发现错误或执行静态分析?
有。

PyChecker是一个静态分析工具，用于发现Python源代码中的错误，并对代码复杂度和风格做出警告。可以从http://pychecker.sourceforge.net/获取PyChecker。

[Pylint](http://www.logilab.org/projects/pylint)是另一个工具，它检查一个模块是否满足编码规范，同时还能编写插件以添加自定义功能。除了PyChecker执行的错误检查，Pylint还提供一些额外的功能，例如检查行长度，变量名是否按照你的编码规范组织良好，声明的接口是否得到充分实现，等等。

### 我怎样才能从Python脚本中创建一个独立二进制文件?
如果你只是想要一个独立的程序，用户可以下载并在不安装Python发布版本的情况下运行，那么你并不需要将Python编译成C代码。有许许多多的工具可以用来确定一个程序所需要的模块，并将这些模块与Python二进制文件绑定在一起，以生成一个可执行文件。

一种是使用freeze工具，它作为Python源码树中的`Tools/freeze`被引进。它将Python二进制代码转换成C数组；C编译器可以将所有的模块嵌入到一个新的程序中，然后将其与标准的Python模块链接。

它通过递归地扫描你的代码以查找import语句（两种形式），并寻找标准Python路径及源代码目录中（内置模块）的模块来工作。然后将用Python写的模块字节码转换成C代码（数组初始化，其能够使用marshal模块转变成代码对象），并创建一个只包含那些实际在程序中使用的内置模块的定制配置文件。接着，它编译生成的C代码，将其与Python解释器的其他部分链接在一起，形成一个作用完全像你的脚本的自包含的二进制文件，

显然，freeze需要一个C编译器。有一些不需要C编译器的工具。一个是Thomas Heller的py2exe（仅适用于Windows）：
http://www.py2exe.org/

另一个工具是Anthony Tuininga的cx_Freeze。

### Python程序是否有编码标准或风格指南?
是的。标准库模块需要的代码风格记录在[PEP 8](https://www.python.org/dev/peps/pep-0008)。

## 核心语言
### 当变量有值时，为什么我会得到一个UnboundLocalError错误?
当通过在函数体中的某个位置增加一个赋值语句来修改以前工作的代码时，获得UnboundLocalError错误可能会让你感到惊讶。

代码如下:
```
>>> x = 10
>>> def bar():
...     print(x)
>>> bar()
10
```
正常工作，但下面的代码:
```
>>> x = 10
>>> def foo():
...     print(x)
...     x += 1
```
返回一个UnboundLocalError错误:
```
>>> foo()
Traceback (most recent call last):
  ...
UnboundLocalError: local variable 'x' referenced before assignment
```
这是因为，当你在一个范围内对变量进行赋值时，该变量会变成此范围中的局部变量，并且覆盖外部范围的任何类似命名的变量。由于foo函数中最后一条语句将一个新值赋给`x`，编译器认为它是一个局部变量。因此，当签名的`print(x)`试图打印该未初始化的局部变量时，会产生一个错误的结果。

在上面的例子中，你可以通过声明其为global来访问外部作用域的变量：
```
>>> x = 10
>>> def foobar():
...     global x
...     print(x)
...     x += 1
>>> foobar()
10
```
这种显式声明时必须的，以便提醒你（不像类和实例变量表面上类似的情况），你实际上是在修改外部范围的变量的值：
```
>>> print(x)
11
```
你可以在一个嵌套范围内使用[nonlocal](https://docs.python.org/3/reference/simple_stmts.html#nonlocal)关键字进行类似的工作：
```
>>> def foo():
...    x = 10
...    def bar():
...        nonlocal x
...        print(x)
...        x += 1
...    bar()
...    print(x)
>>> foo()
10
11
```

### Python中的局部和全局变量规则是什么?
在Python中，只在函数里引用的变量暗示其为全局变量。如果在该函数的任何一个地方对变量进行赋值，那么则假定它是局部变量，除非显式声明其为全局变量。

虽然这有点出人意料，但是想一想，你就明白了。一方面，赋值变量需要[global](https://docs.python.org/3/reference/simple_stmts.html#global)提供了避免无意的副作用。另一方面，如果所有的全局引用都需要`global`，那么，你要一直使用`global`。你必须为每一个内置函数或者导入模块的每一部分的每一个引用声明其为全局的。这种混乱会破坏`global`声明识别副作用的实用性。

### 为什么在一个循环中用不同的值定义的lambda都返回相同的结果?
假设你使用一个for循环来定义一些不同的lambda表达式（即使是普通函数），例如：
```
>>> squares = []
>>> for x in range(5):
...    squares.append(lambda: x**2)
```
它提供一个包含5个计算`x**2`的lambda列表。你可能会期望，当调用它们的时候，它们将分别返回0, 1, 4, 9, 和 16。然而，当你实际尝试的时候，将看到它们都返回16：
```
>>> squares[2]()
16
>>> squares[4]()
16
```
这是因为`x`并不是lambda的局部变量，它定义于外部范围，并在lambda调用的时候访问 - 而不是lambda定义的时候。在循环结束时，`x`的值是`4`，所以所有的函数都返回了`4**2`，即，16。你也可以通过修改`x`的值，然后查看lambda表达式结果的变化来验证它。
```
>>> x = 8
>>> squares[2]()
64
```
为了避免这种情况，你需要将该值保存在lambda表达式的局部变量中，这样，它们才不会依赖于全局变量x的值：
```
>>> squares = []
>>> for x in range(5):
...    squares.append(lambda n=x: n**2)
```
这里，`n=x`为lambda表达式创建了一个新的局部变量`n`，然后在lambda表达式定义的时候计算该值，这样，它就具备了与`x`在循环中具备的相同的值。这意味着`n`在第一个lambda的值是`0`，在第二个lambda的值是`1`,在第三个lambda的值是`2`,等等。因此，每一个lambda表达式现在会返回正确的结果：
```
>>> squares[2]()
4
>>> squares[4]()
16
```
注意，这种行为不是lambda表达式特有的，常规函数也同样适用。

### 如何跨模块共享全局变量?
在一个程序中跨模块共享信息的规范方式是创建一个特殊的模块（通常称为config或者cfg）。只要在应用程序中的所有模块中导入config模块，该模块即作为全局名称可用。因为每一个模块只有一个实例，任何对此模块的修改会反馈到每一个角落。例如：

config.py:
```
x = 0   # Default value of the 'x' configuration setting
```
mod.py:
```
import config
config.x = 1
```
main.py:
```
import config
import mod
print(config.x)
```
注意，出于同样的原因，使用一个模块也是基于单一设计模式的实现。

### 什么是在模块中使用import的“最佳实践?
一般情况下，不要使用`from modulename import *`。这样做，会混淆导入器的命名空间，并使得检测器(linter)更难以检测未定义名称。

在文件的顶部导入模块。这样做，代码需要什么其他模块更加清晰，并且避免模块名称是否在范围内的问题。采用每行导入一个的方法，会使得增加及删除模块导入变得容易，但是每行导入多个会使用较少的屏幕空间。

若你按照以下顺序导入模块，则是最佳实践：

1. 标准库模块 – 例如 sys, os, getopt, re
2. 第三方库模块（任何安装在Python的site-packages目录的模块） – 例如 mx.DateTime, ZODB, PIL.Image, 等等.
3. 本地开发的模块

有时候需要将导入移到一个方法或类以避免循环导入问题。Gordon McMillan 说:

>在两个模块使用导入形式“import <module>”时，循环导入不会有问题。但当第二个模块想要从第一个模块中获得name（“from module import name”）并且导入位于顶层时，则会失败。这是因为由于第一个模块正忙于导入第二个模块，第一个的名称还尚未可用。

在这种情况下，如果第二个模块只在一个函数中使用，那么可以简单的将导入移入到该函数中。在调用导入的时候，第一个模块将完成初始化，然后第二个模块可以进行它的导入。

若有些模块是平台特定的，也可能必须将导入移出代码的顶层。在这种情况下，甚至可能没有可能在文件的顶部导入所有的模块。这种情况下，导入对应平台相关的正确的模块代码是一个好的选择。

只有在必须解决诸如避免循环导入的问题，或者试图减少模块的初始化时间的时候，才将导入移入到一个局部作用域（例如函数定义内）。这种技术在许多导入不需要依赖程序如何执行时特别有用。如果模块只在某函数中使用，你也可能想要将导入移动到该函数中。注意，因为模块的一次初始化，在第一次时间加载模块可能是昂贵的，但是多次加载模块几乎是免费的，之浪费一点字典查找的时间。即使模块名称已经超出了范围，该模块也可能在[sys.modules](https://docs.python.org/3/library/sys.html#sys.modules)中。

### 为什么对象之间共享默认值?
新手程序员通常会遇到这种类型错误。考虑一下这个函数：
```
def foo(mydict={}):  # Danger: shared reference to one dict for all calls
    ... compute something ...
    mydict[key] = value
    return mydict
```
第一次调用这个函数，`mydict`包含一个项，第二次，`mydict`包含两个项，这是因为当`foo()`开始执行的时候，`mydict`已经包含了一个项。

通常期望函数调用为默认值创建新的对象。这是不会发生的。在函数定义的时候，默认值只创建一次。如果修改该对象，例如此例子中的自动，后续该函数的调用将会指向这个被修改的对象。

根据定义，诸如数字，字符串，元组和None这种不可变对象，可以从修改中全身而退。对诸如字典，列表和类实例这种可变对象的修改会导致混乱。

由于这个特点，不使用可变对象作为默认值是一个好的编程习惯。相反，使用`None`作为默认值，并在函数中检查参数是否为`None`，若是，则创建一个新的列表/字典/其他什么的。例如，不要这样：
```
def foo(mydict={}):
    ...
```
而要这样:
```
def foo(mydict=None):
    if mydict is None:
        mydict = {}  # create a new dict for local namespace
```
这个功能非常有用。当你有一个计算费时的函数，一个常用方法是缓存参数及每一次函数调用返回的结果，当再次请求相同的值时，返回缓存的值。这就是所谓的“memoizing”，并且可以这样实现：
```
# Callers will never provide a third parameter for this function.
def expensive(arg1, arg2, _cache={}):
    if (arg1, arg2) in _cache:
        return _cache[(arg1, arg2)]

    # Calculate the value
    result = ... expensive computation ...
    _cache[(arg1, arg2)] = result           # Store result in the cache
    return result
```
你可以使用一个包含字典的全局变量来替代默认值；这只是个人喜好。

### 如何将可选或关键字参数从一个函数传递到另一个?
使用函数参数列表中的`*`和`**`说明符来收集参数；这将为你提供一个位置参数元组和关键字参数字典。你可以在调用其他函数的时候通过使用`*`和`**`传递这些参数：
```
def f(x, *args, **kwargs):
    ...
    kwargs['width'] = '14.3c'
    ...
    g(x, *args, **kwargs)
```

### 参数arugment和参数parameter之间有何区别?
[参数parameter](https://docs.python.org/3/glossary.html#term-parameter)是通过一个函数定义中出现的名称定义的，而[参数argument](https://docs.python.org/3/glossary.html#term-argument)是在调用一个函数时实际传递给它的值。参数parameter定义一个函数可以接受什么类型的参数argument类型。例如，给定函数定义：
```
def func(foo, bar=None, **kwargs):
    pass
```
foo, *bar* 和 *kwargs*是func的参数parameter。然而，当调用`func`时，例如：
```
func(42, bar=314, extra=somevar)
```
值 42, 314, 和 `somevar`是参数argument。

### 为什么修改列表‘y’的同时也修改了列表‘x’?
如果你像这样编写代码：
```
>>> x = []
>>> y = x
>>> y.append(10)
>>> y
[10]
>>> x
[10]
```
那么你可能会奇怪，为什么在`y`后面追加一个元素，也会改变`x`。

有两个产生这样的结果的两个因素：

1. 变量只是指向对象的名称。`y = x`并不会创建此列表的一个拷贝 - 它创建了一个新的变量`y`，其指向`x`指向的同一个对象。这意味着只有一个对象（这个列表），并且`x`和`y`都指向它。
2. 列表是[可变的](https://docs.python.org/3/glossary.html#term-mutable)，这意味着你可以修改它们的内容。

调用`append()`后，可变对象的内容从`[]`变为`[10]`。由于这两个变量都指向同一个对象，使用任一名称都会读取已修改的值`[10]`。

如果我们是将一个不可变对象赋给`x`：
```
>>> x = 5  # ints are immutable
>>> y = x
>>> x = x + 1  # 5 can't be mutated, we are creating a new object here
>>> x
6
>>> y
5
```
可以看到，在这种情况下，`x`和`y`不再相等了。这是因为，整型是[不可变的](https://docs.python.org/3/glossary.html#term-immutable)，当我们进行`x = x + 1`时，并不是通过增加它的值来改变整型`5`，而是创建一个新的对象（整型`6`），并把它赋给`x`（即，改变对象x的指向）。在这个赋值后，我们拥有两个对象（整型`6`和`5`），以及两个指向它们的变量（`x`现在指向`6`，但是`y`仍然指向`5`）。

一些操作（例如，`y.append(10)`和`y.sort()`）改变了对象，而表面上类似的操作（例如，`y = y + [10]`和`sorted(y)`）创建一个新的对象。一般在Python中（及在标准库中所有的情况下），一个改变对象的方法将返回`None`，以避免混淆这两种操作。所以，如果你错误的使用`y.sort()`，认为它将返回给你一个`y`已排序的拷贝，那么你将得到一个`None`，这可能会导致你的程序产生一个容易诊断的错误。

然而，有一类操作，其中对于相同的操作，不同的类型会具备不同的行为：增量赋值运算符。例如，`+=`改变列表，但不改变元组或者整型(`a_list += [1, 2, 3]`等价于`a_list.extend([1, 2, 3])`，它会改变`a_list`，而`some_tuple += (1, 2, 3)`和`some_int += 1`创建新的对象)。

也就是说：

* 如果我们有一个可变对象（[list](https://docs.python.org/3/library/stdtypes.html#list), [dict](https://docs.python.org/3/library/stdtypes.html#dict), [set](https://docs.python.org/3/library/stdtypes.html#set)等），那么可以使用一些特定的操作符来改变它，并且所有指向它的变量都会跟着改变。
* 如果我们有一个不可变对象（str, int, [tuple](https://docs.python.org/3/library/stdtypes.html#tuple)等）,那么所有指向它的变量将总是具有相同的值，但是将此值转变为另一个新值的操作将总是返回一个新的对象。

如果你想知道两个变量是否指向同一个对象，可以使用[is](https://docs.python.org/3/reference/expressions.html#is)操作符，或者是内置函数[id()](https://docs.python.org/3/library/functions.html#id)。

### 如何编写一个拥有输出参数的函数（通过引用调用）?
请记住，Python中是通过赋值传递参数的。由于赋值只是创建对象的引用，调用者和被调用者的参数argument名之间并不存在别名。因此，并不存在按引用调用。可以通过多种方式实现预期效果。

1. 通过返回结果的元组：
    ```
    def func2(a, b):
        a = 'new-value'        # a and b are local names
        b = b + 1              # assigned to new objects
        return a, b            # return new values
    
    x, y = 'old-value', 99
    x, y = func2(x, y)
    print(x, y)                # output: new-value 100
    ```
    这几乎总是最清晰的方案。

2. 通过使用全局变量。这不是线程安全的，并不推荐。

3. 通过传递一个可变对象（就地可变）：
    ```
    def func1(a):
        a[0] = 'new-value'     # 'a' references a mutable list
        a[1] = a[1] + 1        # changes a shared object
    
    args = ['old-value', 99]
    func1(args)
    print(args[0], args[1])    # output: new-value 100
    ```

4. 通过传递一个字典来获取改变：
    ```
    def func3(args):
        args['a'] = 'new-value'     # args is a mutable dictionary
        args['b'] = args['b'] + 1   # change it in-place
    
    args = {'a':' old-value', 'b': 99}
    func3(args)
    print(args['a'], args['b'])
    ```
5. 或者用一个类实例来捆绑值：
    ```
    class callByRef:
        def __init__(self, **args):
            for (key, value) in args.items():
                setattr(self, key, value)
    
    def func4(args):
        args.a = 'new-value'        # args is a mutable callByRef
        args.b = args.b + 1         # change object in-place
    
    args = callByRef(a='old-value', b=99)
    func4(args)
    print(args.a, args.b)
    ```
    几乎从来没有一个好的理由来让它如此复杂。

最好的选择是返回一个包含多个结果的元组。

### 如何在Python中编写高阶函数?
有两种选择：可以使用嵌套作用域，或者可以使用可调用对象。例如，假设你想要定义函数`linear(a,b)`，它返回一个函数`f(x)`，这个函数计算`a*x+b`的值。使用嵌套作用域：
```
def linear(a, b):
    def result(x):
        return a * x + b
    return result
```
或者使用一个可调用对象：
```
class linear:

    def __init__(self, a, b):
        self.a, self.b = a, b

    def __call__(self, x):
        return self.a * x + self.b
```
在这两种情况下，
```
taxes = linear(0.3, 2)
```
提供一个可调用对象，其中，`taxes(10e6) == 0.3 * 10e6 + 2`。

可调用对象有一个缺点，它有点慢，并需要有点长的代码。然而，注意，可调用对象的集合可以通过继承共享它们的签名：
```
class exponential(linear):
    # __init__ inherited
    def __call__(self, x):
        return self.a * (x ** self.b)
```
对象可以用几种方法来封装状态：
```
class counter:

    value = 0

    def set(self, x):
        self.value = x

    def up(self):
        self.value = self.value + 1

    def down(self):
        self.value = self.value - 1

count = counter()
inc, dec, reset = count.up, count.down, count.set
```
这里，`inc()`, `dec()` 和 `reset()`像函数一样，并共享相同的计数变量。

### 如何拷贝Python中的对象?
一般情况下，使用[copy.copy()](https://docs.python.org/3/library/copy.html#copy.copy)或者[copy.deepcopy()](https://docs.python.org/3/library/copy.html#copy.deepcopy)。虽然不是所有的对象都能拷贝，但是大多数情况下可以。

一些对象可以更容易被拷贝。字典有一个[copy()](https://docs.python.org/3/library/stdtypes.html#dict.copy)方法：
```
newdict = olddict.copy()
```
序列可以通过切片拷贝：
```
new_l = l[:]
```
### 如何查找一个对象的方法或属性?
对于用户定义的类的一个实例`x`来说，`dir(x)`返回一个由实例属性名称，方法和类定义的属性构成的按字母序排列的列表。

### 如何让我的代码发现一个对象的名称?
一般来说，不能，因为对象实际上并没有名字。从本质上讲，赋值通常为一个值绑定一个名称；`def`和`class`语句也是一样的，但在这种情况下，值是可调用的。考虑一下下面的代码：
```
class A:
    pass

B = A

a = B()
b = a
print(b)
<__main__.A object at 0x16D07CC>
print(a)
<__main__.A object at 0x16D07CC>
```
可以说，类有一个名字：即使它绑定到两个名字，并通过名字B引用，创建的实例仍然是类A的实例。然而，不能说实例的名字是a还是b，因为这两个名字都被绑定到相同的值上。

一般而言，你的代码不应该需要“知道特定值的名字”。除非，你在故意编写内省程序，这通常暗示，一个方法的改变可能是有用的。

在comp.lang.python上，Fredrik Lundh曾经提供了一个很好的比喻来回答这个问题：

> 正如你得到在你的走廊上发现的猫咪的名字的方法一样：那只猫咪（对象）自己不能告诉你它的名字，它并不真正关心 - 因此，找出它叫什么的唯一方法是询问你所有的邻居（命名空间），如果它是他们的猫（对象）的话……
> 
> 如果你发现它有很多名字，或者没有名字，请不要感到惊讶！

### 逗号运算符的优先级是怎么回事?
逗号并不是Python中的一个操作符。考虑一下：
```
>>> "a" in "b", "a"
(False, 'a')
```
由于逗号并不是一个操作符，但如果你输入如下内容，则上面表达式之间的分隔符会被认为：
```
("a" in "b"), "a"
```
而不是：
```
"a" in ("b", "a")
```
各种赋值表达式（`=`, `+=`等等）也是一样的。它们并不是真正的运算符，而是赋值语句中的句法界定符。

### 是否有与C的“?:”等价的三元运算符?
是的，有。语法如下：
```
[on_true] if [expression] else [on_false]

x, y = 50, 25
small = x if x < y else y
```
在这个语法自Python 2.5引入之前，一个常见的习惯是使用逻辑运算符：
```
[expression] and [on_true] or [on_false]
```
但是，这个并不安全，因为当*on_true*有一个false布尔值时，它可能会提供错误的结果。因此，最好总是使用`... if ... else ...`形式。

### Python中，有没有可能编写混淆的一行话?
有。它一般通过在[lambda](https://docs.python.org/3/reference/expressions.html#lambda)中嵌套[lambda](https://docs.python.org/3/reference/expressions.html#lambda)。查看以下Ulf Bartelt的三个例子：
```
from functools import reduce

# Primes < 1000
print(list(filter(None,map(lambda y:y*reduce(lambda x,y:x*y!=0,
map(lambda x,y=y:y%x,range(2,int(pow(y,0.5)+1))),1),range(2,1000)))))

# First 10 Fibonacci numbers
print(list(map(lambda x,f=lambda x,f:(f(x-1,f)+f(x-2,f)) if x>1 else 1:
f(x,f), range(10))))

# Mandelbrot set
print((lambda Ru,Ro,Iu,Io,IM,Sx,Sy:reduce(lambda x,y:x+y,map(lambda y,
Iu=Iu,Io=Io,Ru=Ru,Ro=Ro,Sy=Sy,L=lambda yc,Iu=Iu,Io=Io,Ru=Ru,Ro=Ro,i=IM,
Sx=Sx,Sy=Sy:reduce(lambda x,y:x+y,map(lambda x,xc=Ru,yc=yc,Ru=Ru,Ro=Ro,
i=i,Sx=Sx,F=lambda xc,yc,x,y,k,f=lambda xc,yc,x,y,k,f:(k<=0)or (x*x+y*y
>=4.0) or 1+f(xc,yc,x*x-y*y+xc,2.0*x*y+yc,k-1,f):f(xc,yc,x,y,k,f):chr(
64+F(Ru+x*(Ro-Ru)/Sx,yc,0,0,i)),range(Sx))):L(Iu+y*(Io-Iu)/Sy),range(Sy
))))(-2.1, 0.7, -1.2, 1.2, 30, 80, 24))
#    \___ ___/  \___ ___/  |   |   |__ lines on screen
#        V          V      |   |______ columns on screen
#        |          |      |__________ maximum of "iterations"
#        |          |_________________ range on y axis
#        |____________________________ range on x axis
```
亲，不要在家里尝试！

## 数字和字符串
### 如何制定十六进制和八进制整型?
要指定一个八进制数字，那么在八进制值前面放一个0和一个小写或者大写字母"o"。例如，设置变量"a"的值为八进制值"10"（十进制值为8），输入：
```
>>> a = 0o10
>>> a
8
```
十六进制也很简单。只需在十六进制数前面加一个0及一个小写或大写字母"x"。十六进制数字可以通过小写或大写来指定。例如，在Python解释器中：
```
>>> a = 0xa5
>>> a
165
>>> b = 0XB2
>>> b
178
```
### 为什么-22 // 10返回-3?
它主要由这样的需求所驱使：`i % j`的结果与`j`具备相同的正负号。如果你想这样，同时也想让下面成立：
```
i == (i // j) * j + (i % j)
```
那么，整数除法必须返回向下取整的结果。C语言也要求保持这个特性。编译器将`i // j`截断，以使得`i % j`与`i`具备相同的正负号。

只有少数几种有用的情况下`i % j`中的`j`是负数的。当`j`是正数，有许多，并且在几乎它们所有中，令`i % j`是`>= 0`更有用。如果当前时间是10，那么200个小时前是多少？`-190 % 12 == 2`是有效的，而`-190 % 12 == -10`则是一个等待测试的错误。

### 如何将字符串转换为数字?
对于整型，使用内置的[int()](https://docs.python.org/3/library/functions.html#int)类型构造器，例如，`int('144') == 144`。同样地，[float()](https://docs.python.org/3/library/functions.html#float)将字符串转换成浮点数，如`float('144') == 144.0`。

默认情况下，它们将数字解释为十进制，因此`int('0144') == 144` 和 `int('0x144')`会引发[ValueError错误](https://docs.python.org/3/library/exceptions.html#ValueError)。`int(string, base)`将转换所需的基数作为其第二个可选参数，因此`int('0x144', 16) == 324`。若指定base为0，那么该数字会使用Python的规则来解析：前置‘0o’表示八进制，而前置‘0x’表示十六进制数。

若你需要的只是将字符串转换成数字，那么不要使用内置函数[eval()](https://docs.python.org/3/library/functions.html#eval)。[eval()](https://docs.python.org/3/library/functions.html#eval)会特别慢，并且它有一个安全风险：有人可能传递一个Python表达式，从而引发不必要的副作用。例如，可能传递值为`__import__('os').system("rm -rf $HOME")`，这将删除你的home目录。

[eval()](https://docs.python.org/3/library/functions.html#eval)函数还具有将数字解析为Python表达式的作用，使得如`eval('09')`将会抛出一个语法错误，因为Python不允许一个十进制数字以'0'开头（除了“0”）。

### 如何将数字转换成字符串?
要转换例如数字144为字符串"144"，使用内置的类型构造器[type()](https://docs.python.org/3/library/stdtypes.html#str)。如果你想要显示其十六进制或八进制，使用内置函数[hex()](https://docs.python.org/3/library/functions.html#hex)或者[oct()](https://docs.python.org/3/library/functions.html#oct)。要转换成其他格式，请参见[字符串格式化](https://docs.python.org/3/library/string.html#string-formatting)部分，例如：`"{:04d}".format(144)` 得到 `'0144'`，而`"{:.3f}".format(1.0/3.0)` 得到 `'0.333'`。

### 如何就地修改一个字符串?
不可以，因为字符串是不可变的。在大多数的情况下，你应该使用该字符串中所需要的部分来简单地构建一个新的字符串。然而，如果你需要一个具备就地修改unicode数据能力的对象，尝试使用[io.StringIO](https://docs.python.org/3/library/io.html#io.StringIO)对象或[array](https://docs.python.org/3/library/array.html#module-array)模块：
```
>>> import io
>>> s = "Hello, world"
>>> sio = io.StringIO(s)
>>> sio.getvalue()
'Hello, world'
>>> sio.seek(7)
7
>>> sio.write("there!")
6
>>> sio.getvalue()
'Hello, there!'

>>> import array
>>> a = array.array('u', s)
>>> print(a)
array('u', 'Hello, world')
>>> a[0] = 'y'
>>> print(a)
array('u', 'yello, world')
>>> a.tounicode()
'yello, world'
```
### 如何使用字符串来调用函数/方法?
有各种技术。

* 最好的方法是使用一个将字符串映射到函数的字典。该方法主要优点在于字符串不需要与函数名匹配。这也是用于仿真一个样例构建器的主要方法：
```
def a():
    pass

def b():
    pass

dispatch = {'go': a, 'stop': b}  # Note lack of parens for funcs

dispatch[get_input()]()  # Note trailing parens to call function
```
* 使用内置函数`getattr()`:
    ```
    import foo
    getattr(foo, 'bar')()
    ```
    注意，[getattr()](https://docs.python.org/3/library/functions.html#getattr)适用于任何对象，包括类，类实例，模块等等。
    
    它用于标准库中的一些地方，例如：
    ```
    class Foo:
        def do_foo(self):
            ...
    
        def do_bar(self):
            ...
    
    f = getattr(foo_instance, 'do_' + opname)
    f()
    ```
* 使用[locals()](https://docs.python.org/3/library/functions.html#locals) 或者 [eval()](https://docs.python.org/3/library/functions.html#eval) 来解析函数名:
    ```
    def myFunc():
        print("hello")
    
    fname = "myFunc"
    
    f = locals()[fname]
    f()
    
    f = eval(fname)
    f()
    ```
    注意：使用[eval()](https://docs.python.org/3/library/functions.html#eval)是缓慢而微笑的。若你无法拥有字符串内容的绝对控制权，那么有人可能传递一个导致执行任意函数的字符串。

### 是否有与Perl的chomp()等价的函数/方法来删除字符串的尾随换行符?
你可以使用`S.rstrip("\r\n")`来从字符串`S`末端去除所有行终止符，而不删除其他尾随空格。如果字符串`S`具有多行，并在结尾有多个空白行，那么，将会删除所有空白行的行终止符：
```
>>> lines = ("line 1 \r\n"
...          "\r\n"
...          "\r\n")
>>> lines.rstrip("\n\r")
'line 1 '
```
由于一般只有希望一次阅读文本一行时才这样做，因此，使用`S.rstrip()`这个方法效果也不错。

### 是否有与scanf() 或 sscanf()等价的函数/方法?
不是这样的。

对于简单的输入解析，最简单的方法通常是使用string对象的[split()](https://docs.python.org/3/library/stdtypes.html#str.split)方法，将行分为以空格分隔的单词，然后使用[int()](https://docs.python.org/3/library/functions.html#int)或者[float()](https://docs.python.org/3/library/functions.html#float)将十进制字符串转换成数值。`split()`支持一个可选的"sep"参数，该参数在该行使用空格之外的东西作为分隔符时使用。

对于更复杂的输入解析，正则表达式比C的sscanf()更为强大，并且更适合。

### ‘UnicodeDecodeError’或者‘UnicodeEncodeError’错误意味着什么?
见[Unicode指南](https://docs.python.org/3/howto/unicode.html#unicode-howto).

## 性能
### 我的程序太慢了。如何加速?
这一般是一个艰难的问题。首先，在深入之前，请记住以下列表：

* 不同的Python实现具有不同的性能特点。本FAQ关注[CPython](https://docs.python.org/3/glossary.html#term-cpython)。
* 操作系统间行为各不同，特别是在讨论I/O或多线程时。
* 在尝试优化任何代码前，你应该总是寻找程序中的热点（见[profile](https://docs.python.org/3/library/profile.html#module-profile)模块）。
* 编写基准脚本将让你在搜索改进的时候快速迭代（见[timeit](https://docs.python.org/3/library/timeit.html#module-timeit)模块）。
* 强烈建议在可能引入潜藏在复杂代码优化的回归之前，具有良好的代码覆盖率（通过单元测试或其他技术）。

话虽如此，也有很多技巧可以用来加快Python代码。下面是一些一般原则，它们大大有助于实现可接受的性能水平：

* 相比于试图对你的代码使用微小优化的技巧，让你的算法更快（或者使用更快的算法）可以产生更大的效益。
* 使用正确的数据结构。好好学习[内置类型](https://docs.python.org/3/library/stdtypes.html#bltin-types)和[集合模块](https://docs.python.org/3/library/collections.html#module-collections)的相关文档。
*  当标准库提供了做某事的基本方法，很有可能（也不一定）比你想出的任何方法都快。这对于用C写的基本方法，例如内置及一些扩展类型更加如此。例如，务必使用内置方法[list.sort()](https://docs.python.org/3/library/stdtypes.html#list.sort)或者相关的函数[sorted()](https://docs.python.org/3/library/functions.html#sorted)来进行排序（见[如何排序](https://docs.python.org/3/howto/sorting.html#sortinghowto)中较为高级的使用例子）。
* 抽象往往会造成间接，并且强迫解释器做更多的工作。若间接的层次超过了有效工作量，程序将会变得更慢。你应该避免过多的抽象，特别是在微小的函数或方法下（它们也往往有损于可读性）。

如果你已经到达了纯Python允许的极限，有些工具可以帮到你。例如，[Cython](http://cython.org/)可以将一个略加修改的Python代码编译成C扩展，并且可以在许多不同的平台上使用。Cython利用汇编（及可选类型注释）使你的代码比解释时的速度获得显著提高。如果你对你的C编程技巧倍感自信，也可以自己编写[C扩展模块](https://docs.python.org/3/extending/index.html#extending-index)。

>另见维基专门用于[性能提示](https://wiki.python.org/moin/PythonSpeed/PerformanceTips)的页面。

### 将许多字符串连起来的最有效的方法是什么?
[str](https://docs.python.org/3/library/stdtypes.html#str)和[byte](https://docs.python.org/3/library/functions.html#bytes)对象是不可变的，因此将多个字符串连接在一起是低效的，因为每次连接都会创建一个新的对象。在一般情况下，总的运行时间成本是字符串总长度的平方。

要聚集多个[str](https://docs.python.org/3/library/stdtypes.html#str)对象，推荐的惯用方式是将它们放到一个列表中，然后在其后调用[str.join()](https://docs.python.org/3/library/stdtypes.html#str.join)：
```
chunks = []
for s in my_strings:
    chunks.append(s)
result = ''.join(chunks)
```
(另一个合理有效的惯用方法是使用io.StringIO)

要聚集多个[byte](https://docs.python.org/3/library/functions.html#bytes)对象，推荐的惯用方法是使用就地连接(`+=``运算符)扩展一个[bytearray](https://docs.python.org/3/library/functions.html#bytearray)对象：
```
result = bytearray()
for b in my_bytes_objects:
    result += b
```
## 序列 (元组/列表)
### 如何在元组和列表之间进行转换?
类型构造器`tuple(seq)`将任何序列（实际上，是任何可迭代对象）转换成元组，元组中项的顺序与原序列相同。

例如，`tuple([1, 2, 3])`得到`(1, 2, 3)`而`tuple('abc')`得到`('a', 'b', 'c')`。如果参数是一个元组，那么并不会将其拷贝，而是返回一个相同的对象，因此，它的运行开销很低，当你不能确定一个对象是否已经是一个元组时，放心调用[tuple()](https://docs.python.org/3/library/stdtypes.html#tuple)吧。

类型构造器`list(seq)`将任何序列或者可迭代对象转换成一个列表，列表中项的顺序与原序列相同。例如，`list((1, 2, 3))`得到`[1, 2, 3]`，而`list('abc')`得到`['a', 'b', 'c']`。如果参数是一个列表，它会如`seq[:]`一般构造一个拷贝。

### 什么是负索引?
Python序列可用正数和负数进行索引。对于正数，0是第一个索引，1是第二个索引，以此类推。对于负数，-1是最后一个索引，-2是倒数第二个索引，以此类推。可以把`seq[-n]`当成如`seq[len(seq)-n]`。

使用负索引是非常有效的。例如，`S[:-1]`是该串去除其最后一个字符之后的字符串，这对于从一个字符串中去除其后的换行符来说是很有用的。

### 如何以相反的顺序遍历序列?
使用内置的[reversed()](https://docs.python.org/3/library/functions.html#reversed)函数，在Python 2.4中引入：
```
for x in reversed(sequence):
    ... # do something with x...
```
它不会改动你原始的序列，但会创建一个逆序的拷贝用来遍历。

在Python 2.3及之前的版本，可以使用扩展的切片语法：
```
for x in sequence[::-1]:
    ... # do something with x...
```
### 如何从列表中删除重复元素?
见the Python Cookbook中一个长时间关于多种方法来达成这个目标的讨论：
http://code.activestate.com/recipes/52560/

如果你不介意重新对列表进行排序，那么对其进行排序，然后从列表的末尾进行扫描，扫描过程中删除重复元素：
```
if mylist:
    mylist.sort()
    last = mylist[-1]
    for i in range(len(mylist)-2, -1, -1):
        if last == mylist[i]:
            del mylist[i]
        else:
            last = mylist[i]
```
若列表中的所有元素都可以作为集合中的键（即，它们都是[可哈希的](https://docs.python.org/3/glossary.html#term-hashable)），那会快得多：
```
mylist = list(set(mylist))
```
它将列表转换成一个集合，从而去除重复，然后再返回列表。

### 如何在Python中创建一个数组?
使用列表:
```
["this", 1, "is", "an", "array"]
```
在时间复杂度上，列表相当于C或者Pascal数组；主要的差异在于，Python列表可以包含许多不同类型的对象。

`array`模块还提供了具有简洁表示的固定类型的数组创建方法，但它们在索引方面比列表慢。还要注意的是，数字扩展及其他限定类数组结构也具备各种特性。

要获得Lisp风格的链接列表，可以使用元组模拟地址对：
```
lisp_list = ("like",  ("this",  ("example", None) ) )
```
若需要可变性，可以使用列表来取代元组。`lisp_list[0]`类似于lisp car，而`lisp_list[1]`类似于cdr。只有当你确定真的需要时才这样做，因为它通常比使用Python列表慢得多。

### 如何创建一个多维列表?
你可能试图创建一个这样的多维数组：
```
>>> A = [[None] * 2] * 3
```
如果打印它，看起是正确的：
```
>>> A
[[None, None], [None, None], [None, None]]
```
但当你赋值时，所赋的值会在多处出现：
```
>>> A[0][0] = 5
>>> A
[[5, None], [5, None], [5, None]]
```
原因在于，使用`*`来复制一个列表并不会创建拷贝，只是创建已有对象的引用。`*3`创建了一个包含3个对长度为2的同一个列表的引用的列表。对一行的修改将在所有行显示，这基本上不是你想要的。

建议的做法是，首先创建一个具有所需长度的列表，然后为其中每一个元素创建一个新列表：
```
A = [None] * 3
for i in range(3):
    A[i] = [None] * 2
```
这会生成一个包含3个长度为2的不同列表的列表。也可以使用列表解析式：
```
w, h = 2, 3
A = [[None] * w for i in range(h)]
```
或者，可以使用提供矩阵数据类型的扩展；[数值Python](http://www.numpy.org/)是最有名的。

### 如何对一个对象序列应用一个方法?
使用列表推导式：
```
result = [obj.method() for obj in mylist]
```
### 为什么当使用加号时，a_tuple[i] += [‘item’]抛出一个异常?
这是由于以下事实的结合：增量赋值操作符是赋值操作符，以及Python中可变对象和不可变对象的差异。

此讨论也适用于将增量赋值操作符应用到一个指向可变对象的元组的元素上的一般情况，但我们将适用`list`和`+=`作为例子。

如果你这样写：
```
>>> a_tuple = (1, 2)
>>> a_tuple[0] += 1
Traceback (most recent call last):
   ...
TypeError: 'tuple' object does not support item assignment
```
引发此异常的原因一目了然：增加`1`到指向`(1)`的对象`a_tuple[0]`，生成结果对象`2`，但当我们试图将计算结果`2`赋给此元组的下标为`0`的元素时，会得到一个错误，因为我们不能修改一个元祖所指向的元素。

在背后，增量赋值语句所做的近似于：
```
>>> result = a_tuple[0] + 1
>>> a_tuple[0] = result
Traceback (most recent call last):
  ...
TypeError: 'tuple' object does not support item assignment
```
操作的赋值部分产生了此错误，因为元组时不可变的。

当你像这样写：
```
>>> a_tuple = (['foo'], 'bar')
>>> a_tuple[0] += ['item']
Traceback (most recent call last):
  ...
TypeError: 'tuple' object does not support item assignment
```
此异常有点令人吃惊，而让人更吃惊的是，即使有错误，追加操作实际上是有效的：
```
>>> a_tuple[0]
['foo', 'item']
```
要知道为什么会发生这种情况，你需要知道的是，(a) 若一个对象实现了`__iadd__`魔法方法，它会在执行`+=`增量赋值时被调用，其返回值是在赋值语句中使用的值；以及(b) 对于列表而言，`__iadd__`相当于调用列表上的`extend`方法，然后返回此列表。这就是为什么我们说，对于列表，`+=`是`list.extend`的“缩写”：
```
>>> a_list = []
>>> a_list += [1]
>>> a_list
[1]
```
这相当于：
```
>>> result = a_list.__iadd__([1])
>>> a_list = result
```
a_list指向的对象已经被修改，而指向可变对象的指针被重新赋值给a_list。此赋值的最终结果是一个空操作，因为它指向a_list原先指向的指针，但赋值仍然发生了。

因此，发生在我们的元组例子中的事情等价于：
```
>>> result = a_tuple[0].__iadd__(['item'])
>>> a_tuple[0] = result
Traceback (most recent call last):
  ...
TypeError: 'tuple' object does not support item assignment
```
由于`__iadd__`成功了，因此该列表得到扩展，但即使`result`指向`a_tuple[0]`已经指向的对象，最后的赋值仍然会导致一个错误，因为元组时不可变的。

## 字典
### 如何获得一个存储的字典，并且将其键按照一致顺序展示?
使用collections.OrderedDict。

### 我想进行一个复杂排序：可以在Python进行Schwartzian变换吗?
由Perl社区的Randal Schwartz提供的技术是，通过一个将其每一个元素映射到它的“排序值”的矩阵，来排序一个列表中的元素。在Python中，只需要使用`sort()`方法的`key`参数：
```
Isorted = L[:]
Isorted.sort(key=lambda s: int(s[10:15]))
```
`key`参数在Python 2.4引进，对于更旧的版本，这种分类可以使用列表解析相当容易的完成。要根据它们的大写值排序字符串列表：
```
tmp1 = [(x.upper(), x) for x in L]  # Schwartzian transform
tmp1.sort()
Usorted = [x[1] for x in tmp1]
```
要按照每一个字符串中的位置10-15组成的整数值进行排序：
```
tmp2 = [(int(s[10:15]), s) for s in L]  # Schwartzian transform
tmp2.sort()
Isorted = [x[1] for x in tmp2]
```
对于3.0之前的版本，可以通过以下计算Isorted：
```
def intfield(s):
    return int(s[10:15])

def Icmp(s1, s2):
    return cmp(intfield(s1), intfield(s2))

Isorted = L[:]
Isorted.sort(Icmp)
```
但由于这种方法对于`L`中对每个元素多次调用`intfield()`，它慢于Schwartzian变换。

### 如何根据另一个列表的值排序一个列表?
将它们合并成元组的一个迭代器，对生成的列表进行排序，然后挑选出你想要的元素。
```
>>> list1 = ["what", "I'm", "sorting", "by"]
>>> list2 = ["something", "else", "to", "sort"] #要排序的列表
>>> pairs = zip(list1, list2)
>>> pairs = sorted(pairs)
>>> pairs
[("I'm", 'else'), ('by', 'sort'), ('sorting', 'to'), ('what', 'something')]
>>> result = [x[1] for x in pairs]
>>> result
['else', 'sort', 'to', 'something']
```
最后一步的一个替代方案是：
```
>>> result = []
>>> for p in pairs: result.append(p[1])
```
如果你觉得这样更加清晰，你可能更愿意这个来替代最后的列表解析。然而，它的运行时间几乎是长列表的两倍。为什么呢？首先，`append()`操作符需要重新分配内存，虽然它使用了一些技巧以避免每次都要这样做，但是它仍然需要偶尔进行，这会花费不少时间。第二，表达式“result.append”需要额外的属性查找。第三，必须调用所有那些函数会导致减速。

## 对象
### 什么是类?
类是通过执行class语句创建的特定对象类型。类对象作为创建实例对象的模板使用，同时体现了特定数据类型的数据（属性）和代码（方法）。

一个类可以基于一个或多个类，它(们)称为此类的基类。然后，它会继承它的基类的属性和方法。这允许一个对象模型通过继承不断精细。你可能有一个通用的`Mailbox`类，它提供了基本的邮箱存取方法，而其子类，例如`MboxMailbox`, `MaildirMailbox`, `OutlookMailbox`可以处理各种特定的邮箱格式。

### 什么是方法?
一个方法就是某个对象`x`上的一个函数，通常你可以以`x.name(arguments...)`的方式进行调用。方法是在类定义中定义的函数：
```
class C:
    def meth (self, arg):
        return arg * 2 + self.attribute
```
### 什么是self?
self仅仅是方法的第一个参数的常规名称。像`meth(self, a, b, c)`一样定义的方法，对于此定义所发生的类的某些实例`x`，应该通过像`x.meth(a, b, c)`一样被调用；别调用的方法会认为，它是像`meth(self, a, b, c)`一样被调用。

另见[为什么必须在方法定义和调用中显式使用‘self’?](https://docs.python.org/3/faq/design.html#why-self)。

### 如何检查一个对象是否为给定类或其子类的实例?
使用内置函数`isinstance(obj, cls)`.通过提供一个元组而不是一个简单的类，你可以检测一个类是否是任意数量的类的实例，也可以检测一个对象是否是Python的一个内置类型，例如，`isinstance(obj, str)`或者 `isinstance(obj, (int, float, complex))`。

注意，大多数程序不会频繁的在用户定义的类上使用[isinstance()](https://docs.python.org/3/library/functions.html#isinstance)。如果你正在开发自己的类，一个更恰当的面向对象的风格是在类中定义方法来封装特定的行为，而不是检查对象的类，然后基于它是什么类来进行不同的操作。例如，如果你有一个做某些事的函数：
```
def search(obj):
    if isinstance(obj, Mailbox):
        # ... code to search a mailbox
    elif isinstance(obj, Document):
        # ... code to search a document
    elif ...
```
更好的方法是在所有的类中定义`search()`方法，然后仅仅调用它：
```
class Mailbox:
    def search(self):
        # ... code to search a mailbox

class Document:
    def search(self):
        # ... code to search a document

obj.search()
```
### 什么是委托(delegation)?
委托(delegation)是一项面向对象技术（也称为设计模式）。比方说，你有一个对象`x`并想改变它其中一个方法的行为。您可以创建一个新的类，它提供了你想要修改的方法的一种新的实现，并将`x`的所有其他方法委托给`x`相应的方法。

Python程序员可以轻松的实现委托。例如，下面的类实现了这样的一个类，它的行为像一个文件，但它将所有写入的数据转换为大写：
```
class UpperOut:

    def __init__(self, outfile):
        self._outfile = outfile

    def write(self, s):
        self._outfile.write(s.upper())

    def __getattr__(self, name):
        return getattr(self._outfile, name)
```
在这里，`UpperOut`类重新定义了`write（）`方法，在调用底层的`self.__outfile.write()`方法前，将参数字符串转换成大写。其他所有方法都将委托给底层的`self.__outfile`对象。委托是通过`__getattr__`方法实现的; 见[语言参考](https://docs.python.org/3/reference/datamodel.html#attribute-access)，以获得有关控制属性访问的更多详细信息。

请注意，对于更一般的情况，委托可能变得很棘手。当必须设置以及检索属性时，该类必须也定义一个[__setattr __（）](https://docs.python.org/3/reference/datamodel.html#object.__setattr__) 方法，并且它必须小心操作。[__setattr __（）](https://docs.python.org/3/reference/datamodel.html#object.__setattr__)的基本实现大致等同于以下内容：
```
class X:
    ...
    def __setattr__(self, name, value):
        self.__dict__[name] = value
    ...
```
大多数[__setattr __（）](https://docs.python.org/3/reference/datamodel.html#object.__setattr__)的实现必须在不会导致无限递归的情况下，修改self.`__ dict__`以存储self的本地状态。

### 如何在派生类中调用其已经覆盖的基类中的方法?
使用内置的[super()](https://docs.python.org/3/library/functions.html#super)：函数：
```
class Derived(Base):
    def meth (self):
        super(Derived, self).meth()
```
对于3.0之前的版本，可以使用经典类：对于诸如`class Derived(Base): ...`的类定义，可以通过`Base.meth(self, arguments...)`调用`Base`（或者`Base`的基类中的一个类）中定义的`meth()`方法。这里，`Base.meth`是未绑定的方法，所以需要提供`self`参数。

### 如何组织代码，以便更容易修改基类?
你可以为基类定义一个别名，在类定义前将真正的基类赋给它，然后在你整个类中使用别名。然后，你所需修改的是赋给别名的值。顺便说一下，这一招在你想动态的决定使用哪一个基类（例如，基于资源可用性）也挺方便的。例如：
```
BaseAlias = <real base class>

class Derived(BaseAlias):
    def meth(self):
        BaseAlias.meth(self)
        ...
```
### 如何创建静态类数据和静态类方法?
Python同时支持（C++或者Java意义上的）静态数据和静态方法。

对于静态数据，只需简单定义一个类属性。要对该属性赋新值，则需在赋值中显式的使用类名：
```
class C:
    count = 0   # number of times C.__init__ called

    def __init__(self):
        C.count = C.count + 1

    def getcount(self):
        return C.count  # or return self.count
```
对于任意`c`，若有`isinstance(c, C)`为True，那么`c.count`也指向`C.count`，除非被`c`自身覆盖，或者被从`c.__class__`到C的基类搜索路径上的某些类覆盖。

注意：在C方法中，诸如`self.count = 42`的赋值，在`self`自己的字典中创建一个新的且无关的名为"count"的实例。无论是否在方法中，类静态数据名的重新绑定必须始终指定类：
```
C.count = 314
```
静态方法是可能的：
```
class C:
    @staticmethod
    def static(arg1, arg2, arg3):
        # No 'self' parameter!
        ...
```
然而，一个获得静态方法效果更直接的方法是，通过一个简单的模块级函数：
```
def getcount():
    return C.count
```
假如你的代码是根据一个模块定义一个类（或者紧密相关的类层次）来构建的，那么这将提供期望的封装。

### Python中如何重载构造函数（或方法）?
这个答案其实适用于所有方法，但此问题通常先出现在构造函数中。

在C++中，会这样写：
```
class C {
    C() { cout << "No arguments\n"; }
    C(int i) { cout << "Argument is " << i << "\n"; }
}
```
在Python中，你必须编写唯一一个构造函数，它使用默认参数并可以捕捉所有情况。例如：
```
class C:
    def __init__(self, i=None):
        if i is None:
            print("No arguments")
        else:
            print("Argument is", i)
```
这并不完全等下，但在实践中足够接近。

你也可以尝试一个可变长度的参数列表，例如：
```
def __init__(self, *args):
    ...
```
同样的方法适用于所有方法定义。

### 在尝试使用__spam时，得到了一个关于_SomeClassName__spam的错误.
带两个前导下划线的变量名称是“错位的”，以提供一种简单而有效的方法来定义类的私有变量。任何具有形式`__spam`（至少两个前导下划线，至多有一个结尾下划线）的标志，文本上，会被使用`_classname__spam`替换，其中`classname`是当前移除了任何前导下划线的类名。

这并不能确保隐私：外部用户仍然可以故意访问“_classname__spam”属性，并且在对象的`__dict__`中，私有值是可见的。很多Python程序员从来不屑于使用私有变量名。

### 我的类中定义了 __del__，但在删除对象的时候并不调用此方法.
有几种可能的原因。

del语句并不一定要求调用[__del __（）](https://docs.python.org/3/reference/datamodel.html#object.__del__) - 它只是递减对象的引用计数，如果这达到了零，则调用[__del __（）](https://docs.python.org/3/reference/datamodel.html#object.__del__)。

如果你的数据结构包含循环链接（比如一棵树，其中，每个孩子都有一个父引用，每个父母都有一个孩子的列表），那么引用计数将永远不会变为零。有时，Python会运行一个算法来检测这种循环，但是垃圾收集器可能会在您的数据结构的最后一个引用消失后运行一段时间，所以你的[__del __（）](https://docs.python.org/3/reference/datamodel.html#object.__del__)方法可能在一个不方便且随机的时间内被调用。如果你想重现一个问题，那么这是不方便的。更糟的是，该对象[__del __（）](https://docs.python.org/3/reference/datamodel.html#object.__del__)方法的顺序的执行是任意的。你可以运行[gc.collect()](https://docs.python.org/3/library/gc.html#gc.collect)来强制收集，但也在某些病态的情况下，对象将永远不会被收集。

尽管有循环收集器，
无论何时结束使用它，为对象定义一个明确的`close()`方法仍然是一个好主意。在 `close（）`方法会删除引用subobjecs的属性。不要直接调用[__del __（）](https://docs.python.org/3/reference/datamodel.html#object.__del__)方法 - [__del __（）](https://docs.python.org/3/reference/datamodel.html#object.__del__)方法应该调用`close（）`方法，而`close（）`方法应该确保它可以被同一个对象多次调用。

另一种避免循环引用的方法是使用[weakref](https://docs.python.org/3/library/weakref.html#module-weakref)模块，它允许你指向对象而不增加其引用计数。例如，树的数据结构应该对它们的父引用和同胞的引用使用弱引用（如果它们需要它们！）。

最后，如果你的[__del __（）](https://docs.python.org/3/reference/datamodel.html#object.__del__)方法抛出一个异常，警告信息会被打印到[sys.stderr](https://docs.python.org/3/library/sys.html#sys.stderr)。

### 如何获得一个给定类中所有实例的列表?
Python不跟踪一个类（或者一个内置类型）的所有实例。你可以通过在类的构造函数中保存一个列表，列表中保存每一个实例的弱引用，来跟踪所有的实例。

### 为什么id()返回的结果看起来不唯一?
内置的[id()](https://docs.python.org/3/library/functions.html#id)函数返回一个整数，它保证在该对象的生命周期中是唯一的。这是因为在CPython中，它是该对象的内存地址，在从内存中删除一个对象后，会在内存中的相同位置上分配下一个新创建的对象，这种情形会不断的发生。如下例所示：
```
>>> id(1000)
13901272
>>> id(2000)
13901272
```
这两个id属于两个不同的整型对象，它们首先创建，然后在执行完`id()`调用后立即删除。为了确保你想检测的对象id还可用，创建该对象的另一个引用：
```
>>> a = 1000; b = 2000
>>> id(a)
13901272
>>> id(b)
13891296
```
## 模块
### 如何创建一个.pyc文件?
当一个模块第一次被导入时（或当自创建当前编译的文件后，源文件已经改变），一个包含经编译的代码的`.pyc`文件应在包含`.py`文件的目录的一个`__pycache__`子目录下被创建。该`.pyc`文件将具有这样的名称，它的名字以`.py`文件名开头，以`.pyc`结尾，并且它拥有一个中间件，其依赖于创建它的特定python二进制。（详细信息，见[PEP 3147](https://www.python.org/dev/peps/pep-3147)。）

`.pyc`文件可能不会被创建的原因之一是，包含源文件的目录的权限问题文件，这意味着无法创建`__pycache__` 子目录。这可能发生，例如，如果你作为一个用户进行开发，但使用另一个用户来运行，正如与一个Web服务器进行。

除非设置[PYTHONDONTWRITEBYTECODE](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONDONTWRITEBYTECODE)环境变量，那么如果你正导入一个模块，并且Python有能力（权限，自由空间，等...）创建`__pycache__`子目录，及编写编译的模块到该子目录，`.pyc`文件会自动被创建。

在顶层脚本中运行的Python不认为是一种导入，因此将不会创建任何` .pyc`文件。例如，如果你有一个顶层模块`foo.py`，它导入另一个模块`xyz.py`，当你运行 `foo`（通过输入作为一个shell命令的`python foo.py`），`xyz`的一个`.pyc`文件将会被创建，因为 `xyz`被导入，但不会为`foo`创建任何`.pyc`文件，因为并没有导入`foo.py`。

如果你需要为`foo`创建的`.pyc`文件 -也就是说，为一个未导入的模块创建一个 `.pyc`文件 -你可以使用 [py_compile](https://docs.python.org/3/library/py_compile.html#module-py_compile)和[compileall](https://docs.python.org/3/library/compileall.html#module-compileall)模块。

该[py_compile](https://docs.python.org/3/library/py_compile.html#module-py_compile)模块可以手动编译任何模块。一种方法是交互地该模块中的`compile()`函数：
```
>>> import py_compile
>>> py_compile.compile('foo.py')   
```
这将写入把`.pyc`文件写入到与`foo.py`文件同一位置的`__pycache__`子目录中（或者使用可选参数`cfile`选择其他位置）。

您还可以使用[compileall](https://docs.python.org/3/library/compileall.html#module-compileall)模块自动编译一个或多个目录中的所有文件。您可以通过在shell提示下运行`compileall.py`以及提供一个包含Python文件编译目录的路径来做到：
```
python -m compileall .
```
### 如何查找当前模块的名称?
一个模块可以通过查找预定义的全局变量`__name__`来找到它自己的模块名。如果此值为`'__main__'`，那么程序作为脚本运行。许多通常通过导入它们来使用的模块，也提供了一个一个命令行界面或者自测试，并且只有在检查`__name__`之后才执行它们的代码：
```
def main():
    print('Running test...')
    ...

if __name__ == '__main__':
    main()
```
### 如何让模块彼此互相导入?
假设你有以下几个模块：

foo.py:
```
from bar import bar_var
foo_var = 1
```
bar.py:
```
from foo import foo_var
bar_var = 2
```
问题是，解释器将执行下列步骤：

* foo的主要导入
* 创建foo的空全局变量
* 编译foo并且开始执行foo
* foo 导入 bar
* 创建bar的空全局变量
* 编译bar并且开始执行bar
* bar 导入 foo (这是一个空操作，因为已经有一个名为foo的模块)
* bar.foo_var = foo.foo_var

最后一步会失败，因为Python尚未完成解析`foo`，而`foo`的全局符号表仍然是空的。

当你使用`import foo`，然后在全局代码中尝试访问`foo.foo_var`时，同样的事情也会发生。

针对这个问题，（至少）有三种可能的解决方法。

Guido van Rossum建议避免所有`from <module> import ...`的使用，将所有的代码放置在函数中。全局变量和类变量的初始化应该只使用常量或内建函数。这意味着一个导入模块中的一切会被引用为`<module>.<name>`。

Jim Roskind建议每个模块中按一下顺序执行步骤：

* 导出 (全局变量，函数和不需要导入基类的类)
* `import`语句
* 活动代码 (包括根据导入值初始化的全局变量).

van Rossum并不喜欢这种方式，因为导入出现在一个奇怪的地方，但它确实工作。

Matthias Urlichs建议重构代码，以使得首先不需要递归导入。

这些解决方案并不互相排斥。

### __import__(‘x.y.z’) 返回 <module ‘x’>; 如何获得z?
考虑使用[importlib](https://docs.python.org/3/library/importlib.html#module-importlib)中的便利函数[import_module()](https://docs.python.org/3/library/importlib.html#importlib.import_module):
```
z = importlib.import_module('x.y.z')
```
### 当我编辑一个导入的模块并重新导入它，所做的更改并不会显示处理。为什么会出现这种情况?
为了提高效率及一致性，Python只在模块第一次导入时读取模块文件。如果不这样做，那么在由许多模块组成，并且每个模块导入相同的基本模块的程序中，基本模块将多次解析再解析。要强制重新读取一个已改变的模块，这样做：
```
import importlib
import modname
importlib.reload(modname)
```
警告：这个技术并不是100%有效的。特别是，含有像这样的语句的模块：
```
from modname import some_objects
```
将继续使用导入对象的旧版本。若此模块包含类定义，现有的类实例将不会被更新为使用新的类定义。这可以导致以下反常行为：
```
>>> import importlib
>>> import cls
>>> c = cls.C()                # Create an instance of C
>>> importlib.reload(cls)
<module 'cls' from 'cls.py'>
>>> isinstance(c, cls.C)       # isinstance is false?!?
False
```
如果你打印出类对象的“id”，此问题的性质是明确的：
```
>>> hex(id(c.__class__))
'0x7352a0'
>>> hex(id(cls.C))
'0x4198d0'
```
