原文：[Defensive programming in Python](http://tutorials.pluralsight.com/python/defensive-programming-in-python)

---

这是星期五下午，而你的新版本已经发布几天了。你带着骄傲与轻松的心情开始了这周，但随着本周慢慢推进，你的骄傲则慢慢的消失。要发布这样一个没有bug的版本是需要很多努力的。事实上，在发布之日，你很有信心未来的几周将会很安静，因为用户没有别的需要或想要的东西。

当然，如果是真的那就太好了。但在发布不久后，你的第一个bug报告就出现了。第一个bug报告只是一些无关痛痒的东西，一个新的对话框上的一个小小的拼写错误。接着，又是一些小的bug单出现，你快速的将它们修复，然后推送到仓库中。

接着不幸的事情发生了，它是每个开发者最糟糕的噩梦：在系统中你最自豪的部分里报告了一个bug。你疯狂的看着代码，及时你清楚的记着它。代码的那个分支怎么可能在这种场景下执行？！代码**一定**是在骗你。

快进几天到bug搜索，然而你仍然不知道这是怎么发生的。甚至，你没法在你的测试环境上重新该场景。如果你有更多关于该错误的调试信息就好了……

### 真相将会让你自由

如果你花费了大量的时间来写代码，那么你将意识到这个场景。尽管你尽了最大的努力，你仍然会再次编写出会崩溃的软件，这令人不安。但是，不要担心，这种事总是在发生。

这是我透露一劳永逸解决这个问题的灵丹妙药的故事的一部分。不幸的是，我不能，并且我不认为会存在这样一劳永逸解决的方法。

隐藏的事实是，所有的软件都有bug。然而，这并不意味着我们应该放弃，而不是力求完美。这只是意味着我们我们最好稍微改变下我们对于这个事实的观念。我们应该就像计划了缺陷一样编写软件。我们应该防御性地编写软件，也就是为那些不可避免和不知情的bug冷静地设置陷阱。

## 防御性编程

描述这种风格最好的词语是[防御性编程](http://en.wikipedia.org/wiki/Defensive_programming)。维基百科的描述并没有很好的表达出我的真实想法，但它是一个很好的起点：

    防御性设计的形式旨在确保一款软件的持续性功能，而不管软件的不可预见的使用。该思想可以被看做减少或消除墨菲定理的前景产生的效果。防御性编程技术尤其被用于软件被恶意使用或无意中滥用所导致的灾难性影响。

我真正谈论到的是以下原则的组合：

原则

*   每一行代码都是一种责任
*   编码你的假设
*   可执行文档时最好的 [1]

这些原则是确保我们可以包含我们的代码和理智免遭不可避免的bug的关键。记住，我们是基于不打算编写没有bug的代码的观点进行操作的。

我们必须将这些原则铭记于心，以帮助我们快速地找到bug。很多时候，查找bug是最困难的部分。所以，让我们优化查找，而不是将它们完全阻止这个不可能完成的任务。

### Python工具

[![description](https://raw.githubusercontent.com/pluralsight/guides/master/images/a1516ea4-3261-43a6-a52d-348446b844df.png) ](https://raw.githubusercontent.com/pluralsight/guides/master/images/a1516ea4-3261-43a6-a52d-348446b844df.png)

让我们更深入地了解一些可用工具，以帮助我们执行下述原则。作为演示目的，我们将使用[Python](http://python.org)作为我们的语言，但大多数的语言都有非常类似的工具。

*   断言(Assert)
*   日志(Logging)
*   单元测试(Unit test)

假设我们有下述函数，它从用户那里获取值，然后将数据的指定范围规范到0到1之间的某个数字上，这可以用于以后用在道路上的一个新的小部件上。
```py
def normalize_ranges(colname):
    """ Normalize given data range to values in [0 - 1] Return dictionary new 'min' and 'max' keys in range [0 - 1] """

    # 1-D numpy array of data we loaded application with
    original_range = get_base_range(colname)
    colspan = original_range['datamax'] - original_range['datamin']

    # User filtered data from GUI
    live_data = get_column_data(colname)
    live_min = numpy.min(live_data)
    live_max = numpy.max(live_data)

    ratio = {}
    ratio['min'] = (live_min - original_range['datamin']) / colspan
    ratio['max'] = (live_max - original_range['datamin']) / colspan

    return ratio
```

现在，假设我们有由`get_column_data()`函数返回的“列”：
```py
age = numpy.array([10.0, 20.0, 30.0, 40.0, 50.0])
height = numpy.array([60.0, 66.0, 72.0, 63.0, 66.0])
```

我们来验证下它确实将我们给定的范围转换成[0 - 1]之间的某个数:
```py
>>> normalize_ranges('age')
    {'max': 1.0, 'min': 0.0}
```

好了，这是一个非常简短的工作，但它似乎可以工作。我们传递了一系列“真正的”数字，然后将它们规范化到[0 - 1]之间的某个数上。

请记住上面提到的原则，然后让我们来找找函数中存在的错误。我们在代码中做了什么隐含假设呢？

我可以看到一些假设：

1.  `original_range`只包含正数
2.  返回的比例位于0.0到1.0之间

经过仔细检查，在上面的代码中包含了相当多的假设。不幸的是，但初初一瞥代码时，它们不是显而易见的。如果我们可以让这些假设更清晰……

## 断言（assert）

[Asserts](http://docs.python.org/2/reference/simple_stmts.html#the-assert-statement)在[unit tests](http://docs.python.org/2/library/unittest.html)中非常常见。
事实上，[Python](http://python.org)拥有大量定制的[用于单元测试的断言](http://docs.python.org/2/library/unittest.html#assert-methods)。然而，没有道理这个有用的工具只在测试世界中使用。

正常代码中的[断言语句](http://docs.python.org/2/reference/simple_stmts.html#the-assert-statement)也是非常有用的。这些语言接收一个表达式，然后当该表达式为`False`时，引发一个带有可选消息的[AssertionError](http://docs.python.org/2/library/exceptions.html#exceptions.AssertionError)。

例如，我们上面的函数声称总是返回[0 -
1]之间的值。不幸的是，我们的假设的更多的压力测试的结果表明这不是真的：
```py
>>> age = numpy.array([-10.0, 20.0, 30.0, 40.0, 50.0])
>>> normalize_ranges('age')
    {'max': 1.0, 'min': -0.5}
```

正如你能想象的那样，这个场景会很长时间不被注意到，而返回值将会传遍整个代码库。这恰恰是不可能找到的bug类型，并会导致文章开始的那个悲伤的故事。

我们可以试着相信，我们的用户可能传递任何可能的值，并妥善处理它们。事实上，这是正确的事情，但并不确保我们不会错过什么。我们已经承认程序员容易犯错这个事实了。

幸运的是，既然我们已经接受了我们会犯错误，那么我们久可以使用断言语句写代码来**对付**未来的自己。
```py
def normalize_ranges(colname):
    """ Normalize given data range to values in [0 - 1]
    Return dictionary new 'min' and 'max' keys in range [0 - 1]
    """

    # 1-D numpy array of data we loaded application with
    original_range = get_base_range(colname)
    colspan = original_range['datamax'] - original_range['datamin']

    # User filtered data from GUI
    live_data = get_column_data(colname)
    live_min = numpy.min(live_data)
    live_max = numpy.max(live_data)

    ratio = {}
    ratio['min'] = (live_min - original_range['datamin']) / colspan
    ratio['max'] = (live_max - original_range['datamin']) / colspan

    assert 0.0 <= ratio['min'] <= 1.0, (
            '"%s" min (%f) not in [0-1] given (%f) colspan (%f)' % (
            colname, ratio['min'], original_range['datamin'], colspan))
    assert 0.0 <= ratio['max'] <= 1.0, (
            '"%s" max (%f) not in [0-1] given (%f) colspan (%f)' % (
            colname, ratio['max'], original_range['datamax'], colspan))

    return ratio
```

我们添加了一些断言语句，它们将提醒我们是否我们并未返回期待范围内的值。让我们看看这些断言如何改变我们小小的测试用例：
```py
>>> age = numpy.array([-10.0, 20.0, 30.0, 40.0, 50.0])
>>> normalize_ranges('age')
    AssertionError: "age" min (-0.500000) not in [0-1] given (10.000000) colspan(40.000000)
```

这个小修改有一些好处：

*   作为一种_可执行_文档的形式
*   在更靠近问题根源的地方放置告警
*   包含有关“无效”参数的有价值的调试信息

1.  **作为一种_可执行_文档的形式**

通常情况下，文档附带几种不同的类型，例如在线或块注释，文档字符串和sphinx。它们每一个都服务于特定的目的，并且对软件开发几乎是必不可少的。不幸的是，它们都受到同一个问题的影响。它们可能快速地与那些快速变化的代码和需求失去同步。这导致了开发者不相信文档。

断言作为带有不同目的的文档。它们清晰简要地描述了应用在运行时的期望状态。此外，如果我们修改了假设，而没有修改相应的断言以匹配新的行为，应用将会抱怨。

断言语句更有可能随着其他变动而更新。因此，较之那些不可执行的文档，断言更值得信任。此外，断言仍然提供了评论、文档字符串等的诸多好处。

值得提出的是，Python生态圈中有另一个非常常见的可执行文档的形式，它就是[doctests](http://docs.python.org/2/library/doctest.html)。这些测试/文档可能看起来有点丑陋，但是它们的关键特点是**接近代码**，就像断言一样。

2.  **在更靠近问题根源的地方放置告警**

我们都曾有过这样的经历，你花费了好几个小时调试一个问题，然后发现真正的bug甚至并不接近你开始的地方(见[5个为什么](http://en.wikipedia.org/wiki/5_Whys))。也许bug的根源逻辑上远离你第一次看到它的地方。

例如，你在你的系统的某处发现了一个字节字符串，但是你假设内部所有字符串都是Unicode字符串。这可能要花费很长一段时间来找到转化在哪里首先被打破。这是一个令人沮丧的情景。要是能更快的找到bug，或者至少有更多的调试信息就好了。

断言不会阻止这种情况，但是它们确实提供了一个机会来改善它。上面的断言将会在函数不遵循它的[合约](http://en.wikipedia.org/wiki/Design_by_contract)返回位于[0 - 1]之间的一个值的那一刻提醒我们。这在稍后我们发现其他代码拥有无效范围时，提供给我们一个有价值的线索。我们会知道这个函数没有符合[合约](http://en.wikipedia.org/wiki/Design_by_contract)中的描述。这一个线索可以节省好几个小时，因为它能够避免从症状一路追查到原因。

3.  **包含有关“无效”参数的有价值的调试信息**

请注意，我们的断言语句也包含关于输入参数的信息。当一个用户碰到使用没有权限使用的数据的bug时，这个信息将是无价的。此外，当用户在描述错误场景时遇到麻烦时，这个调试信息将特别有用。因此，这些小小的断言语句将阻止你成为那种可耻地标记一个bug为“不可重现”状态的人。

输入参数信息还有一些其他微妙的好处：

    *   显示关于用户正在运行哪一类的数据的无效假设
    *   在文档中说明我们期望什么类型的数据
    *   公开不能执行的潜在新用例

### 断言的缺点

我们已经认识到，断言能够提供大量的好处，但它并不总是好玩有趣。如往常一样，它也有缺点。

1.  **debug模式**

通常情况下，出于技术和现实原因，断言语句并不是用于生产代码的。只有在隐藏的[debug](http://docs.python.org/2/library/constants.html#__debug__)常量是`True`的时候，断言才被启用。然后，这个常量的默认值是`True`，这意味着你的代码现在最有可能处在 _debug_ 模式下。

值得考虑一下，你的应用是否正处在这样的环境下：少量额外的逻辑是明显的。关掉 _debug_ 模式的唯一的方式就是带着`-O`[选项](http://docs.python.org/2/using/cmdline.html#cmdoption-O)运行Python解析器。

2.  **增长的代码噪音**

非常容易过度使用断言，并迅速地使得你的代码难以阅读。这可能会让你的代码非常嘈杂，并把真正的功能埋在一系列的错误检查和条件中。下面的代码就是过度使用的例子，它显示了会多难看出代码是要干什么的。
```py
def normalize_ranges(colname):
    """
    Normalize given data range to values in [0 - 1]
    Return dictionary new 'min' and 'max' keys in range [0 - 1]
    """

    assert isinstance(colname, str)

    original_range = get_base_range(colname)
    assert original_range['datamin'] >= 0
    assert original_range['datamax'] >= 0
    assert original_range['datamin'] <= original_range['datamax']

    colspan = original_range['datamax'] - original_range['datamin']
    assert colspan >= 0, 'Colspan (%f) is negative' % (colspan)

    live_data = get_column_data(colname)
    assert len(live_data), 'Empty live data'

    live_min = numpy.min(live_data)
    live_max = numpy.max(live_data)

    ratio = {}
    ratio['min'] = (live_min - original_range['datamin']) / colspan
    ratio['max'] = (live_max - original_range['datamin']) / colspan

    assert 0.0 <= ratio['min'] <= 1.0
    assert 0.0 <= ratio['max'] <= 1.0

    return ratio
```

#### 恰当的断言使用

谨慎使用断言，以及为那些你假设**永远**不会发生的东西使用断言。不要过分使用断言来检查无效输入。

对此，没有硬性及快速的法则，每个开发者都有可能对断言使用有不同的容忍。尽量采用你自己的一些标准，将其包含在你的[开发者风格指南](http://google-styleguide.googlecode.com/svn/trunk/pyguide.html)中。

你确实有一份风格指南，对吧？

此外，记住Python拥抱[鸭子类型](http://en.wikipedia.org/wiki/Duck_typing)，所以，不要过分使用断言来验证所有的类型，从而毁了这点。

One technique I've found useful is to catch all 我发现一项有用的技术是在应用的顶层捕捉所有的`AssertionError`异常，然后将它们与另一项有用的技术结合在一起。

##  日志

日志可以类似于断言语句那样使用。它能够提供运行时调试信息，并潜在改善你的可执行文档。虽然，日志并不完全像断言。它有一些额外的好处。

1.  增长的控制粒度

Python的[logging](http://docs.python.org/2/library/logging.html)模块式非常全面并且可定制的。你可以发送消息到不同的级别，每个级别都可以随你的意愿打开或关闭。所以，你可能会比别人考虑某些更严重的场景，并且能够使用日志级别来编码它。

请记住，这不是断言的那种情况，因为它们依赖[debug模式](http://docs.python.org/2/library/constants.html#__debug__)。

2.  更多动态控制

日志允许你几乎可以从任何地方读取日志级别。一些常见的地方是一个配置文件，环境变量和数据库。这种灵活性允许你控制你可以在不重新运行或发布应用的情况下看到多少日志信息。

相比之下，Python不允许你为断言语句动态的设置[debug](http://docs.python.org/2/library/constants.html#__debug__)常量值。所以，你不能在不重新运行应用的情况下打开或关闭断言。

当你决定你的防御性编程策略时，这值得你考虑。动态日志控制在你使用一种“另类的”发布机制，例如[PyInstaller](http://www.pyinstaller.org/)时，特别重要。

3.  默默地保存回溯信息

当错误发生时，拥有回溯信息和调试信息特别有用，而智能日志记录的使用几乎可以为你动态做到这点。

这个概念在[Doug Hellman](http://doughellmann.com)的[伟大的异常处理博文](http://doughellmann.com/2009/06/python-exception-handling-techniques.html#index-1)中得到证实：
```py
def main():
    logging.basicConfig(level=logging.WARNING)
    log = logging.getLogger('example')
    try:
        throws()
        return 0
    except Exception, err:
        log.exception('Error from throws():')
        return 1
```

[log.exception](http://docs.python.org/2/library/logging.html#logging.Logger.exception)的调用为我们自动地添加异常信息。然后，我们可以配置logger将回溯信息和异常信息放到一个单独的日志文件中，以用于稍后的审视，而无需保留所有的正常信息及警告。

如果在生产代码中启用，这个异常文件可能包含优秀的调试日志。它开启了挖掘日志数据很大的令人兴奋的可能性：

    *   发现用户正尝试我们不曾测试的特性的不同排列，或者甚至考虑，什么会导致新特性的增加，从而使得普通的用例更容易。
    *   由于用户对于应用程序的工作原理的误解而发现的常见错误，这可能导致写出更好的用户文档。

4.  与断言更高级的组合

你还可以将断言和日志串联使用。例如，你可以在默认的debug模式下运行你的应用，然后捕抓记录`AssertionError`到一个不同的文件中。这可以导致更多的数据挖掘的可能，例如找到关于你正运行的平台的一个环境假设。

这只是在防御性编程的情况下日志的几个用途。事实上，你可以使用日志为各种各样的问题创造低保真解决方案，这就是另一个文章的事了。[2]

### 日志的缺点

日志有着许多与断言相同的缺点。然而，伴随着需要额外考虑的东西，日志有其额外的灵活性。

1.  Difficulty managing consistent levels

The most difficult thing with logging is using the available set of levels
consistently throughout a code base. This boils down to the subjective
problem of naming, which is one of only
[two problems in Computer Science](http://martinfowler.com/bliki/TwoHardThings.html).
The best solution is to commit guidelines alongside your code in a
style guide.  Then, there is some project-specific documentation for
newcomers to refer to when adding log messages.

2.  多个logger的设计

The [logging module](http://docs.python.org/2/library/logging.html) is
extremely flexible, but that flexibility comes at a cost.  Logging
configurations can get complicated. Consider a strategy like the following:

    *   Debug level messages go to a hidden file called .debug
    *   Info, warning, and error level messages go to stderr
    *   Critical and exception level messages go into GUI pop up boxes

    This is a good starting point. Also, Python's documentation includes
several
[good logging strategies](http://docs.python.org/2/howto/logging-cookbook.html)
that are definitely worth a read before deciding on your setup.

Remember, logging could be your saving grace when chasing a difficult bug.  So,
take the time to learn the logging system and be sure to design your
configuration carefully.  Even simple applications deserve a good, carefully
designed logging strategy.

## 单元测试

One of the last ways to protect ourselves from future bugs is to not forget
past bugs.  Old bugs have a tendency to creep back into long lived code bases
This is usually a result of someone not understanding _why_ something is
written in a particular way and removing it to 'clean it up.'

This presents another scenario where it's useful to have another form of
executable documentation.  So, when an unsuspecting developer changes some code
or removes something there's running code to alert them of their mistake.

Typically people encounter unit tests in the context of
[Test Driven Development](http://en.wikipedia.org/wiki/Test-driven_development).
This is a great concept, but often in practice it's a bit too optimistic
and difficult to follow (read: customer wants code now).  However, that
discussion is for another blog post.

What I want to discuss is how to use unit tests to protect you from future and
past bugs.  Think of this as somewhat reverses the
[TDD](http://en.wikipedia.org/wiki/Test-driven_development) testing concept
into something a bit more pragmatic with less up-front time costs.

I propose you write tests AFTER you fix a bug. [(see discussion)](http://codrspace.com/durden/a-software-developers-hidden-truth/#comment-1096395589)

This serves a few purposes that might not be immediately obvious:

1.  Improves documentation of bug fix

Surely you included a nice commit message indicating how you fixed the bug,
but don't stop there.  It's likely that your commit message and comments
are lacking something like:

    *   How did you test the fix?
    *   What exact scenario caused the bug?

    This is where a good unit test comes into play.  You already know how to
test the bug.  (You did test it before committing, right?)  So, codify the
scenario you tested with and let everyone benefit from your hard work.

Unit tests are an excellent place to
[spell it all out](http://en.wikipedia.org/wiki/Five_Ws) and provide
documentation for a bug fix.  You can not only explain how and why you
fixed it, but how you tested it.  This information can be very valuable if
the bug creeps up again.

Don't forget, a passing test can be used as a clue to where the bug is
**not** located.

2.  Future-proofing code against duplicate bugs

It's not uncommon for a particular bug to sneak back into a code base.
This can happen because of changing requirements, re-factoring errors, or
any number of situations.  You can catch regressions by writing a unit test
for a specific bug fix and remembering to run your tests.  Think of a unit
test as giving yourself a get out of jail free card on a bug you might
inject again later.

### 单元测试的缺点

单元测试的主要缺点是，很容易忘记运行它们。这只是测试没有直接与代码放在一起的一个副产品。这个缺点可以通过使用一些像[持续集成](http://en.wikipedia.org/wiki/Continuous_integration)这样的实践，利用一些像[Travis CI](https://travis-ci.org/)这样的工具，自动化测试来缓解。

另一个缺点是，单元测试通常只能在自己的测试环境上执行，这是一种没有实际用户的模拟环境。

### 总结

这种开发风格很难归类，并且不幸的是，没有任何坚实的规则可以说啥时候要使用那种。所以我鼓励你将这些原则铭记于心。

这些原则将会导致心态微妙的变化。心态的变化是很重要的，而不是工具或者机制自身。最终，你将因滥用断言或日志而犯一些错误，并且开始形成你自己的风格。此外，因为每个项目的需求不同，所以学习所有的工具并将它们结合起来使用很重要，并且这样对你自己的处境才有意义。

#### 脚注

*   这篇文章的所有引用都被分成一个[链接插件板集合](https://pinboard.in/u:durden/t:defensive_coding_talk/)
*   在[Pytexas 2013会议](http://pytexas.org)上[介绍](http://www.youtube.com/watch?v=HZUY-lo-Esg&amp;list=PL0MRiRrXAvRhewgIznFV_mSDaWst3kHKE&amp;feature=player_detailpage)了该文的一个版本。

        *   你可以在[这里](http://durden.github.io/defensive_coding/)看到那个谈话的幻灯片
        *   你还可以在[这里](http://bitly.com/defensive_coding)看到相关谈话的引用

[1]

    可执行文档是有时用来描述[doctests](http://docs.python.org/2/library/doctest.html)的一个术语。“识字测试”是用来描述这个概念的一个术语。

[2]

    你甚至可以使用日志来构建自己你的分析工具。每次使用一个特性，就将其记录在网络上或Dropbox上的一个文件中。然后，使用一个shell脚本来收集这些文件。现在，你有了大量以一种简单的基于文本的格式存在的有用信息，这开启了数据挖掘以及帮助你的用户的巨大可能性。

 <!-- markdown parsing fails if we don't include a true html break -->
    记住，大部分的用户都不会填写调查问卷。所以，这可能是一种收集他们试图使用的特性或常用工作流信息的方法。然后，你就可以在未来的版本中做得更好。
