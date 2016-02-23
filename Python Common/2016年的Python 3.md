原文：[Python 3 in 2016](https://hynek.me/articles/python3-2016/)

---
关于2016年的Python 3的完全坊间观点。基于我最近的经历，观察，以及与Python社区的其他成员的交流。

今天，我做了我[第一个tweet storm](https://twitter.com/hynek/status/699928792752594944)，然后人们让我正确的把它写出来，所以我们开始吧。

## 阴云密布

综观PyPI的下载统计[1](#fn:4b446252e182cd944dcdae12180464ab:skewed)，Python 3的情况似乎[阴云密布](https://www.reddit.com/r/Python/comments/45sm94/what_are_the_most_recent_python_3_vs_python_2/czzy5wa)：所有的Python 3版本加起来大约才像Python 2.6一样流行，因此任何人都不应再使用。

而且，如果我的公司有任何迹象显示，Python 2中的应用程序不可能被移植到Python 3上。为什么它们要呢？Python 2直到2020年都会得到官方支持。谁知道这些应用程序届时是否还会以那样的形式存在。

这些应用程序的数目是巨大的。而且在可预见的未来里，这一数字将不会显著下跌。没有人愿意去碰他们的工作系统[2](#fn:4b446252e182cd944dcdae12180464ab:easy)。

## 非常平缓的改善

**但是**.

PyPI上绝大多数重要的库都是混合的：它们同时支持Python 2和3。现在，发布一个只支持Python 2的库这种情况是非常罕见的。（我宁愿偶尔看到反面）。

新的项目和应用程序被频繁地基于Python 3启动[3](#fn:4b446252e182cd944dcdae12180464ab:nail)。例如，[从PHP移到Python 3的Patreon](https://talkpython.fm/episodes/show/14/moving-from-php-to-python-3-with-patreon)。为什么不呢？除非你打算使用[PyPy](http://pypy.org/py3donate.html)（一些罕见且低级别的unicode的边缘情况除外），客观来说，它是更好的语言。新项目多久开始一次？在微服务的现今，它可能比你所想的还要频繁。

**鉴于库的状态，Python的2和3之间选择是个人的偏好和公司政策的问题，而不是技术原因。**

尤其是那些后来加入我们的，并且对Python 3没有根深蒂固的反感的社区成员，对他们来说，使用Python 3是很自然的。我已经在Twisted和Pyramid社区都看到这种情况了。在Twisted真正适用于Python 3之前，人们就已经试图在Python 3上使用它了。Django - 使用Python的主要原因之一 - 自身通过在其文档中使用Python 3的语法来制造Python 3使用的印象。

现在，还记得在过去的几个月和几年中，Python社区有什么巨大的增长吗。在某些时候，当保守派大吼大叫时，那些人要负责。这种转变已经开始了。

![](./img/2016-02-20_221149.png)

`asyncio`是另一个有趣的话题。令我感到惊讶的是，Python社区的那个小角落是如此的活跃（虽然主要是讲俄语的国家 🙂）。它当之无愧，所以我可能会加入。所以，对于Python 3.4是第一个被广为接受的版本这件事，我并不感到惊讶。

## 结论

这些都意味着什么？

这意味着，对于可预见的未来，我们不得不为PyPI编写混合库[4](#fn:4b446252e182cd944dcdae12180464ab:py26)。

它意味着，由于为数不少的只支持Python 2的代码的编写，PyPI下载统计数据将不会在短期内变得好看，而Python 2.7仍然在不断增长。

但是，这也意味着Python 3正在增长。新一代的Python开发者喜欢它，他们不明白为什么有人想在每个类中编写`object`的子类。

最后，它意味着Python 3不是Python的死亡。但是，我们将在相当长一段时间内与两个版本的Python共存。如果我们能够在此基础上不杀死对方，那么每个人都会没事。


* * *

现在，我们都用Python 3开始我们的项目，而且并不想回去再用Python 2.所以，停止争论，加入我们吧。我想你会喜欢它的。

## 脚注

* * *

1.  这些数字必须有保留的对待。镜像，缓存，系统包等等以不可预知的方式使得结果出现偏差。
 [↩︎](#fnref:4b446252e182cd944dcdae12180464ab:skewed)
2.  虽然，移植到Python 3并且具有一个很好的测试套件并不是一个[大问题](http://python3porting.com)。但是很少有好的测试套件，甚至更少人有动力投入任何时间进去。
 [↩︎](#fnref:4b446252e182cd944dcdae12180464ab:easy)
3.  对不起，我不能对此进行量化。这只是在我身边听到的普遍想法。这个讨论其中一个最大的问题是，几乎不可能量化任何东西。
 [↩︎](#fnref:4b446252e182cd944dcdae12180464ab:nail)
4.  虽然，我想邀请你[加入我们](https://twitter.com/hynek/status/699886071451144192)， [弃用Python 2.6](https://twitter.com/hynek/status/699921119600574464)。
 [↩︎](#fnref:4b446252e182cd944dcdae12180464ab:py26)
