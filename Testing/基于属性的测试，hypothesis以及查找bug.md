原文：[Property based testing, hypothesis and finding a bug](http://vknight.org/unpeudemath/code/2016/03/07/Property-based-testing-and-finding-a-bug.html)

---

我真的爱上了[测试驱动开发](https://en.wikipedia.org/wiki/Test-driven_development)，坚持使用它是如此的理所应当。它主要围绕着编写Python单元测试，以使得（对我来说）编程更加简单。在Namibia，我很幸运地可以和[David MacRiver](http://www.drmaciver.com/)，[hypothesis](https://hypothesis.readthedocs.org/en/latest/)包的主要开发者，见面聊天。在此之前，[Axelrod项目](http://axelrod.readthedocs.org/en/latest/)已经加入了hypothesis创建的目的的一部分：基于属性的测试。这篇文章将简要描述这是什么，它怎样花费我几个小时的睡眠时间（以一种好的方式），以及为什么它对研究软件意义特别重大。

## 基于属性的测试

我慢慢滴慢慢滴学习到hypothesis是多么的聪明，但在一个非常基础的水平上，它允许你做的是，使用一个测试，以及测试参数的整个范围来取代测试一个单一的例子。

这里是一个简单的例子，基于我在[2016年在Edinburgh的合作研讨会](http://www.software.ac.uk/cw16)所给出的一个简单的演示。
你可以在这个链接[vknight.org/Talks/2016-03-22-Division-by-11-and-property-based-testing-with-hypothesis/](http://vknight.org/Talks/2016-03-22-Division-by-11-and-property-based-testing-with-hypothesis/)中找到我为其提供的幻灯片。

让我们编写一个测试是否能够被11整除的函数（假设我们并不知道有`% 11`这个东西。要做到这点，我们将使用下面的属性）：

> 一个数可以被11整除，当前仅当该数字的数位（变换符号）之和为0.

例如：121可以被11整除，因为（1-2+1=0）（边注：如果你要在评论中对我嚷嚷，请阅读这篇文章的剩余部分）。

让我们将下面的代码保存在一个名为`main.py`的文件中:
```py
def divisible_by_11(number):
    """Uses above criterion to check if number is divisible by 11"""
    string_number = str(number)
    alternating_sum = sum([(-1) ** i * int(d) for i, d
                           in enumerate(string_number)])
    return alternating_sum == 0
```

现在，如果我们写了一些例子，那么我们可以得到期望输出：
```py
>>> import main
>>> for k in range(10):
...     print(main.divisible_by_11(11 * k))
True
True
True
True
True
True
True
True
True
True
```

**但是**，让我们编写一个实际的测试套件。这是一个非常基本的单元测试，我们把它保存在`test_main.py`中：
```py
import unittest
import main

class TestDivisible(unittest.TestCase):
    def test_divisible_by_11(self):

        for k in range(10):
            self.assertTrue(main.divisible_by_11(11 * k))
            self.assertFalse(main.divisible_by_11(11 * k + 1))

        # Some more examples
        self.assertTrue(main.divisible_by_11(121))
        self.assertTrue(main.divisible_by_11(12122))

        self.assertFalse(main.divisible_by_11(123))
        self.assertFalse(main.divisible_by_11(12123))
```

上面的代码测试了可以被11整除的前10个数字（而这些数字+1不是），以及一些特定的测试（121和12123）。

然后，我们通过使用下面的命令运行它：
```sh
$ python -m unittest test_main
.
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
```

现在，我们可以非常开心，并对自己感到非常自豪：我们已经测试了编写良好的软件，它可以传播以及被世界各地的研究者用来测试一个数字能否被11整除了！！

**This is how mathematics breaks.**

![Everything is wrong.](http://vknight.org/Talks/2016-03-22-Division-by-11-and-property-based-testing-with-hypothesis/img/disaster.gif)

让我们编写一个[hypothesis](https://hypothesis.readthedocs.org/en/latest/)测试。我们将在`test_property_main.py`中编写下述代码：
```py
import unittest
import main

from hypothesis import given  # This is how we will define inputs
from hypothesis.strategies import integers  # This is the type of input we will use

class TestDivisible(unittest.TestCase):

    @given(k=integers(min_value=1))  # This is the main decorator
    def test_divisible_by_11(self, k):
        self.assertTrue(main.divisible_by_11(11 * k))
```

上面的代码其实是一个比之前更简单的测试：我们只是测试一个我们知道可以被11正常的数字，事实上从我们的函数中获得了期望的输出。

让我们运行它：
```py
$ python -m unittest test_propert_main
Falsifying example: test_divisible_by_11(self=<test_property_main.TestDivisible testMethod=test_divisible_by_11>, k=19)
F
======================================================================
FAIL: test_divisible_by_11 (test_property_main.TestDivisible)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "test_property_main.py", line 10, in test_divisible_by_11
    def test_divisible_by_11(self, k):
  File "/usr/local/lib/python2.7/site-packages/hypothesis/core.py", line 502, in wrapped_test
    print_example=True, is_final=True
  File "/usr/local/lib/python2.7/site-packages/hypothesis/executors.py", line 57, in default_new_style_executor
    return function(data)
  File "/usr/local/lib/python2.7/site-packages/hypothesis/core.py", line 103, in run
    return test(*args, **kwargs)
  File "test_property_main.py", line 11, in test_divisible_by_11
    self.assertTrue(main.divisible_by_11(11 * k))
AssertionError: False is not true

----------------------------------------------------------------------
Ran 1 test in 0.058s

FAILED (failures=1)
```

我们得到了一个错误！在顶部右侧，我们得到了`Falsifying example`提示，由此可见，对于`k=19`，我们的函数失败了。对于`k=19`，被测试的数字是(19*11=209)。这个数字显然可以被11整除（根据构造），但是它的交替和实际上是11，而不是0。

在这一点上，如[前面关于被11整除所描述的](http://vknight.org/unpeudemath/code/2014/11/22/on-divisibility-by-11.html)，对于被11整除这个特性，我忽略了**正确**的特性：

> 一个数可以被11整除，当前仅当该数字的数位（变换符号）之和可以被11整除。

(在[较老的博文](http://vknight.org/unpeudemath/code/2014/11/22/on-divisibility-by-11/)中，有一个关于它的简单代数证明。)

现在，让我们修改我们的`main.py` (请注意，比我在研讨会上给出的那个随便的演示，这里我使用了一个稍微好点的修改):
```py
def divisible_by_11(number):
    """Uses above criterion to check if number is divisible by 11"""
    string_number = str(number)
    # Using abs as the order of the alternating sum doesn't matter.
    alternating_sum = abs(sum([(-1) ** i * int(d) for i, d
                               in enumerate(string_number)]))
    # Recursively calling the function
    return (alternating_sum in [0, 11]) or divisible_by_11(alternating_sum)
```

运行测试：
```py
$ python -m unittest test_propert_main
.
----------------------------------------------------------------------
Ran 1 test in 0.043s

OK
```

正如我们希望的！

**我认为上面就是关于为什么基于属性的测试和工具，例如hypothesis，对于研究软件是有用的一个很好的例子。**

它帮助我识别我的数学思维过程中的错误，一个我实际上认为已经很好的测试的错误（使用`test_main.py`测试用例）。如果不是因为hypothesis，我可能已经传播了这个重新定义什么是被11整除的代码。

**如果你来只是想要获取hypothesis的一个基本工作样例，那么现在你可以停下来了 :)**

显然，上面是一个简单的例子，但是在[Axelrod](http://axelrod.readthedocs.org/en/latest/) (一个python包，用于再现迭代囚徒困境比赛)中，**hypothesis**最近被发现了一个bug。

Axelrod从123个策略(在撰写本文时)中挑取一个来创造球员锦标赛。下面是其中一个测试，它随机选取策略来创建锦标赛，然后检查其运行：
```py
    @given(s=lists(sampled_from(axelrod.strategies),
                   min_size=2,  # Errors are returned if less than 2 strategies
                   max_size=5, unique=True),
           turns=integers(min_value=2, max_value=50),
           repetitions=integers(min_value=2, max_value=4),
           rm=random_module())
    @settings(max_examples=50, timeout=0)
    @example(s=test_strategies, turns=test_turns, repetitions=test_repetitions,
             rm=random.seed(0))
    def test_property_serial_play(self, s, turns, repetitions, rm):
        """Test serial play using hypothesis"""
        # Test that we get an instance of ResultSet

        players = [strat() for strat in s]

        tournament = axelrod.Tournament(
            name=self.test_name,
            players=players,
            game=self.game,
            turns=turns,
            repetitions=repetitions)
        results = tournament.play()
        self.assertIsInstance(results, axelrod.ResultSet)
        self.assertEqual(len(results.cooperation), len(players))
        self.assertEqual(results.nplayers, len(players))
        self.assertEqual(results.players, players)
```

没有去到具体细节，在某一点上，hypothesis发现，两个特定的策略不能很好的一起工作。[这里是github上的记录这个的issue](https://github.com/Axelrod-Python/Axelrod/issues/465)。他们是：

*   `Backstabber`
*   `MindReader`

这两个策略**事实上**已经在一个锦标赛中一起使用了。包括所有的作弊策略的主要的锦标赛，而其副作用是另一个作弊策略先使用`BackStabber`，然后覆盖它的策略（Ele注：我也晕了，将就着看吧。原文是“The main tournament that includes all cheating strategies, but the side effect of that is that another cheating strategy played BackStabber first and overwrite it's strategy”）(意味着它可以很好地与`MindReader`发挥作用)。**然而，hypothesis创建了一个比赛，其中，它们是完全对立的，然后bug就发生了。**

反正，现在这个问题修复了，并且测试包含了一个`@example`语句来确保这个特定的错误确实修复了：[你可以在源代码中看到这点](https://github.com/Axelrod-Python/Axelrod/blob/master/axelrod/tests/unit/test_tournament.py#L120)。

在实践中，建议你使用基于属性的测试和传统的基于实例的测试的组合（因为有一些特定的东西需要检查），**但是我认为，所有的测试套件通过使用像hypothesis这样的工具都可以得到提高，特别是研究软件**。使用研究软件，一个强大的测试框架不仅有助于测试代码，还有助于测试想法。

有用的连接：

*   [@DRMacIver](https://twitter.com/DRMacIver): David是Hypothesis的作者。
*   [Github仓库](https://github.com/DRMacIver/hypothesis).
*   [文档](https://hypothesis.readthedocs.org/en/latest/).

最后，如果你使用IRC，那么我真心推荐在freenode上顺便看看`#hypothesis`。这里有一个直接链接到irccloud的通道：[www.irccloud.com/invite?channel=%23hypothesis&amp;hostname=irc.freenode.net&amp;port=6697&amp;ssl=1](https://www.irccloud.com/invite?channel=%23hypothesis&amp;hostname=irc.freenode.net&amp;port=6697&amp;ssl=1)。那里的每个人都是乐于助人的。
