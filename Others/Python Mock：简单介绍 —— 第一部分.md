原文：[Python Mocks: a gentle introduction - Part 1](http://blog.thedigitalcatonline.com/blog/2016/03/06/python-mocks-a-gentle-introduction-part-1)

---

正如在TDD的两个介绍性帖子（你可以在[这里](http://blog.thedigitalcatonline.com/categories/tdd/)找到他们）中已经强调过的，测试需要编写一些使用你将开发的函数和对象的代码。这意味着你需要隔离一个给定的（外部）函数，这个函数就是你的公共API的一部分，并证明对于标准输入，以及在边缘情况下，它都能正常工作。

例如，如果你要开发一个存储百分比（例如投票结果）的对象，你应该测试以下条件：类可以存储标准的百分比，如42％；当你尝试存储一个负的百分比时，类应当给予你一个错误；如果存储的百分比大于100％，类应给予一个错误。

测试应该是幂等和隔离的。数学和计算机科学中的幂等表示，一个过程可以在不改变系统状态的情况下多次运行。隔离是指，测试不应该基于它自身以前的执行来改变其行为，也不依赖于其他测试以前的执行（或缺少执行）来改变其行为。

这样的限制保证了你的测试并不因系统的一个临时配置或它们的运行顺序而通过，这在处理外部库和系统，或具有固有可变概念，例如时间时，会引发大的问题。在测试纪律中，使用mock大多数会面对这样的问题，也就是对象假装是其他对象。

在这一系列文章中，我要审视Python的`mock`库，并对其使用进行示例。我不会涵盖mock的方方面面，显而易见，但我希望我可以给你开始使用这个功能强大的库所需的信息。

## 安装

首先，mock是一个Python库，它的开发起步于2008年左右。它被选定为列入Python 3.3的标准库中，但是如果你喜欢，这并不妨碍你使用其他库。

因此，Python 3的用户不需要采取任何步骤，而对于Python 2的项目，你仍然需要使用`pip install mock`以将其安装到系统或当前的virtualenv中。

你可以在[这里](https://docs.python.org/dev/library/unittest.mock.html)找到官方文档。它非常详细，和往常一样，我强烈建议你花时间通读它。

## 基本概念

mock，用测试行话来说，就是模拟另一个（更复杂）对象的行为的对象。当你（单位）测试库的对象时，你有时需要访问你的对象要连接到的其他系统，但出于几个原因，你并不是真的想被迫运行它们

第一个是，与外部系统连接意味着具有一个复杂的测试环境，即你正在抛弃测试的隔离要求。如果你的对象要与网站连接，例如，你不得不拥有一个正在运行的Internet连接，如果远程站点关闭了，那么你无法测试你的库。

第二个原因是，与单元测试的速度相比，一个外部系统的安装通常比较慢。我们预计在几秒钟内运行成百个测试，如果要为它们每一个从远程服务器提取信息，那么时间容易以几个数量级增加。请记住：缓慢的测试意味着，当你开发的时候你不能运行它们，这反过来又意味着你不会真的将它们用于TDD。

第三个原因更微妙，并且与外部系统的可变性有关，所以我会暂时推迟这一问题的讨论。

让我们试着在Python中使用mock，看看它可以做什么。首先，打开一个Python 终端或一个[Jupyter Notebook](jupyter.org)，并导入库
```py
from unittest import mock
```

如果你使用的是Python 2，那么你必须先安装它，然后使用
```py
import mock
```

该库提供的主对象是`Mock`，你可以无参对其实例化
```py
m = mock.Mock()
```

这个对象具有一个特殊特性，当你需要的时候，可以立即创建方法和属性。让我们首先看看对象内部，看看它为我们提供了什么东西：
```py
>>> dir(m)
['assert_any_call', 'assert_called_once_with', 'assert_called_with', 'assert_has_calls', 'attach_mock', 'call_args', 'call_args_list', 'call_count', 'called', 'configure_mock', 'method_calls', 'mock_add_spec', 'mock_calls', 'reset_mock', 'return_value', 'side_effect']
```

正如你可以看见的，有一些方法，它们已经在`Mock`中定义了。让我们读取一个不存在的属性：
```py
>>> m.some_attribute
<Mock name='mock.some_attribute' id='140222043808432'>
>>> dir(m)
['assert_any_call', 'assert_called_once_with', 'assert_called_with', 'assert_has_calls', 'attach_mock', 'call_args', 'call_args_list', 'call_count', 'called', 'configure_mock', 'method_calls', 'mock_add_spec', 'mock_calls', 'reset_mock', 'return_value', 'side_effect', 'some_attribute']
```

好了，正如你可以看见的，这个类与那些你习惯使用的类有点不同。首先，当请求一个不存在的属性时，它的实例并不会引发`AttributeError`错误，而是欢快地返回`Mock`自身的另一个实例。其次，你试图访问的属性现在已经在对象内部创建完毕，对它进行访问会返回一个和之前相同的mock对象。
```py
>>> m.some_attribute
<Mock name='mock.some_attribute' id='140222043808432'>
```

mock对象是可调用对象，这意味着它们既可以被当做属性，又可以被当做方法。如果你尝试调用该mock，那么它只是返回另一个mock，这个mock的名字包含了括号以表示它可调用。
```py
>>> m.some_attribute()
<Mock name='mock.some_attribute()' id='140247621475856'>
```

正如你所了解的，这样的对象是模仿其他对象或系统的完美工具，因为它们可以暴露任何API，而不会引发异常。然而，要在测试中使用它们，我们需要它们就像原来的那样，这意味着返回合理的值或者执行一些操作。

## 返回值

mock可以为你做的最简单的事就是每次你调用它的时候给你返回一个给定值。这可以通过设置mock对象的`return_value`属性来设置。
```py
>>> m.some attribute.return_value = 42
>>> m.some attribute()
42
```

现在，这个对象不再返回mock对象了，而只是返回存储在`return_value`属性中的静态值。显然，你也可以保存一个可调用对象，例如一个函数或一个对象，如果是函数则会返回该函数，而不是运行该函数。让我给你举个例子：
```py
>>> def print_answer():
...  print("42")
... 
>>> 
>>> m.some_attribute.return_value = print_answer
>>> m.some_attribute()
<function print_answer at 0x7f8df1e3f400>
```

正如你所看见的，调用`some_attribute()`只是返回存储在`return_value`中的值，也就是函数自身。要返回来自一个函数的值，我们必须使用mock的一个稍微复杂点的属性, `side_effect`。

## 副作用

mock对象的`side_effect`参数是一个非常强大的工具。它接受三种不同风格的对象，可调用对象，可迭代对象和异常，并且相应地改变自身的行为。

如果你传递一个异常，那么mock将引发它
```py
>>> m.some_attribute.side_effect = ValueError('A custom value error')
>>> m.some_attribute()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/lib/python3.4/unittest/mock.py", line 902, in __call__
    return _mock_self._mock_call(*args, **kwargs)
  File "/usr/lib/python3.4/unittest/mock.py", line 958, in _mock_call
    raise effect
ValueError: A custom value error
```

如果你传递一个可迭代对象，例如一个生成器，或者一个普通的列表，元组，或者类似的对象，mock将生成该可迭代对象的值，例如，在mock的后续调用中返回该可迭代对象包含的每一个值。让我给你举个例子
```py
>>> m.some_attribute.side_effect = range(3)
>>> m.some_attribute()
0
>>> m.some_attribute()
1
>>> m.some_attribute()
2
>>> m.some_attribute()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/lib/python3.4/unittest/mock.py", line 902, in __call__
    return _mock_self._mock_call(*args, **kwargs)
  File "/usr/lib/python3.4/unittest/mock.py", line 961, in _mock_call
    result = next(effect)
StopIteration
```

正如所承诺的，mock只是一次返回在该可迭代对象中找到的一个对象（在这个例子中，是一个`range`对象），直到耗尽该生成器。根据迭代器协议(见[此文](blog/2013/03/25/python-generators-from-iterators-to-cooperative-multitasking/))，一旦所有的项都被返回了，该对象会引发`StopIteration`异常，这意味着你可以在一个循环中正确的使用它。

最后，也许是最常用的情况是，传递一个可调用对象给`side_effect`，这会无耻地使用它自身相同的参数来执行它。这是非常强大的，特别是当你停止思考“函数”，开始思考“可调用对象”时。事实上，`side_effect`也接受一个类，并调用它，也就是说，它可以实例化对象。让我们考虑一个无参函数的简单例子
```py
>>> def print_answer():
...     print("42")       
>>> m.some_attribute.side_effect = print_answer
>>> m.some_attribute.side_effect()
42
```

一个稍微复杂一点的例子：一个带参函数
```py
>>> def print_number(num):
...     print("Number:", num)
... 
>>> m.some_attribute.side_effect = print_number
>>> m.some_attribute.side_effect(5)
Number: 5
```

最后，一个使用函数的例子
```py
>>> class Number(object):
...     def __init__(self, value):
...         self._value = value
...     def print_value(self):
...         print("Value:", self._value)
... 
>>> m.some_attribute.side_effect = Number
>>> n = m.some_attribute.side_effect(26)
>>> n
<__main__.Number object at 0x7f8df1aa4470>
>>> n.print_value()
Value: 26
```

## 使用mock进行测试

现在，我们知道了如何建立一个mock，以及如何传递给它一个静态返回值或者让它调用一个可调用对象。是时候看看如何在测试中使用mock以及mock提供了什么样的功能。我将使用[pytest](http://pytest.org)作为测试框架。你可以在[这里](/categories/tdd/))找到pytest和TDD的简单介绍。

### 安装

如果你想要快速的安装一个pytest环境，可以在终端执行这个代码（要求系统中安装了Python 3和virtualenv）
```sh
mkdir mockplayground
cd mockplayground
virtualenv venv3 -p python3
source venv3/bin/activate
pip install --upgrade pip
pip install pytest
echo "[pytest]" >> pytest.ini
echo "norecursedirs=venv*" >> pytest.ini
mkdir tests
touch myobj.py
touch tests/test_mock.py
PYTHONPATH="." py.test
```

`PYTHONPATH`环境变量是一个避免为了测试一些简单的代码而设置整个Python项目的简单的方法。

### 三种测试类型

根据Sandy Metz，我们只需要测试对象间三种类型的消息（调用）：

*   来电查询（对结果进行断言）
*   来电命令 (对直接公开副作用的断言)
*   去电命令 (调用和参数上的异常)

你可以在[这里](https://www.youtube.com/watch?v=URSWYvyc42M)看到原始通话，或在[这里](https://speakerdeck.com/skmetz/magic-tricks-of-testing-railsconf)阅读到幻灯片。最后的表在第176页幻灯片中。

正如你可以看见的，当处理外部对象时，我们只对一个方法是否被调用以及调用者传递了哪些参数给该对象感兴趣。我们不测试远程对象是否返回正确的结果，这是由mock伪造的，它确实返回了我们所需要的结果。

所以，mock对象提供的方法的目的在于，允许我们检查我们在mock自身调用的方法以及我们在调用过程中使用的参数。

### 断言调用

为了展示如何在测试中使用Python的mock，我将遵循TDD方法，先编写测试，然后编写让这些测试通过的代码。在本文中，我想为你提供关于mock对象的一个简单概述，所以我不会实现一个真实世界用例，并且代码将非常简单。在这个系列的第二部分，我将测试和实现一个真正的类，以便展示一些更加有趣的用例。

在处理一个外部对象时，我们通常感兴趣的第一件事是，知道一个给定方法被调用了。Python的mock提供了`assert_called_with()`方法来检查该条件。

我们将测试的用例如下。 _实例化`myobj.MyObj`对象，它需要一个外部对象。该类会不带任何参数调用 外部对象的`connect()`方法。_
```py
from unittest import mock
import myobj

def test_instantiation():
    external_obj = mock.Mock()
    myobj.MyObj(external_obj)
    external_obj.connect.assert_called_with()
```

在这个简单的例子中，`myobj.MyObj`类需要连接到一个外部对象，例如，一个远程仓库或一个数据库。对于此测试目的，我们需要知道的唯一一件事是，该类是否不带任何参数调用了外部对象的`connect()`方法。

所以在这个测试中，我们要做的第一个件事是实例化mock对象。这是外部对象的一个伪造版本，它唯一的目的是在测试中接受`MyObj`对象的调用，然后返回合理的值。然后，我们实例化`MyObj`类，传递外部类。我们期望该类调用`connect()`方法，所以调用了`external_obj.connect.assert_called_with()`方法。

这个场景发生了什么事呢？`MyObj`类接收一个外部对象，这里，它的初始化过程会调用mock对象的`connect()`方法，而这会将方法本身创建为一个mock对象。这个新的mock记录调用它的参数，而接下来的`assert_called_with()`调用检查出该方法被调用了，并且并未传递任何参数。

运行pytest，测试明显失败。
```sh
$ PYTHONPATH="." py.test
========================================== test session starts ==========================================
platform linux -- Python 3.4.3+, pytest-2.9.0, py-1.4.31, pluggy-0.3.1
rootdir: /home/leo/devel/mockplayground, inifile: pytest.ini
collected 1 items 

tests/test_mock.py F

=============================================== FAILURES ================================================
___________________________________________ test_instantiation __________________________________________

    def test_instantiation():
        external_obj = mock.Mock()
>       myobj.MyObj(external_obj)
E       AttributeError: 'module' object has no attribute 'MyObj'

tests/test_mock.py:6: AttributeError
======================================= 1 failed in 0.03 seconds ========================================
$
```

将这个代码放到`myobj.py`中就可以让测试通过了
```py
class MyObj():
    def __init__(self, repo):
        repo.connect()
```

正如你所看见的，`__init__()`方法实际上会调用`repo.connect()`，这里，`repo`是一个全功能的外部对象，它提供一个给定的API。在这种情况下(目前)，该API只是它的`connect()`方法。当`repo`是一个mock对象时，调用`repo.connect()`会默默地作为mock对象创建该方法，如前所示。

`assert_called_with()`方法还运行我们检查调用时传递的参数。为了说明这点，让我们假装预计`MyObj.setup()`方法在该外部对象上调用`setup(cache=True, max_connections=256)`。如你所见，我们传递了一对参数(即`cache`和`max_connections`)给被调用方法，然后我们想确保该调用确实是这种形式。新的测试是这样的
```py
def test_setup():
    external_obj = mock.Mock()
    obj = myobj.MyObj(external_obj)
    obj.setup()
    external_obj.setup.assert_called_with(cache=True, max_connections=256)
```

像往常一样，第一次运行失败。确保检查这一点，因为这是TDD方法的一部分。你必须有一个DOES NOT PASS的测试，然后写一些代码使之通过。
```sh
$ PYTHONPATH="." py.test
========================================== test session starts ==========================================
platform linux -- Python 3.4.3+, pytest-2.9.0, py-1.4.31, pluggy-0.3.1
rootdir: /home/leo/devel/mockplayground, inifile: pytest.ini
collected 2 items 

tests/test_mock.py .F

=============================================== FAILURES ================================================
______________________________________________ test_setup _______________________________________________

    def test_setup():
        external_obj = mock.Mock()
        obj = myobj.MyObj(external_obj)
>       obj.setup()
E       AttributeError: 'MyObj' object has no attribute 'setup'

tests/test_mock.py:14: AttributeError
================================== 1 failed, 1 passed in 0.03 seconds ===================================
$
```

为了向你展示mock对象提供了什么类型的检查，我实现一个部分正确的解决方案
```py
class MyObj():
    def __init__(self, repo):
        self._repo = repo
        repo.connect()

    def setup(self):
        self._repo.setup(cache=True)
```

如你所见，该外部对象已经被存储在`self._repo`中了，但是对`self._repo.setup()`的调用并不完全是测试所期望的，因为它缺少`max_connections`参数。运行pytest，我们得到了下述结果（我移除了大部分的pytest输出）
```
E           AssertionError: Expected call: setup(cache=True, max_connections=256)
E           Actual call: setup(cache=True)
```

你可以看到，关于我们期望什么，以及我们的代码中发生了神马，错误消息是很清楚。

正如你可以在官方文档中读到的，`Mock`对象还提供下列方法和属性：`assert_called_once_with`, `assert_any_call`, `assert_has_calls`, `assert_not_called`, `called`, `call_count`。 它们每一个都涉及了关于调用mock行为的不同方面，一定要检查它们的描述和一起提供的例子。


## 最后几句话

在本系列的第一部分，我描述了mock对象的行为，以及它们提供的模拟返回值和测试调用的方法。它们是非常强大的工具，可以让你避免创建依赖于外部设施运行的复杂而缓慢的测试，从而错过测试的主要目的，也就是不断帮助你检查你的代码。

在本系列的下一个问题中，我将探讨根据给定对象，mock方法的自动创建，以及通过`patch`装饰器和上下文管理器提供的非常重要的修补机制。

## 反馈

随意使用[博客Google+页面](https://plus.google.com/u/0/111444750762335924049)来对本文发表评论。[GitHub issues](http://github.com/TheDigitalCatOnline/thedigitalcatonline.github.com/issues)页面是提交修改最好的地方。
