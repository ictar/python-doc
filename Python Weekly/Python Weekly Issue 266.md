原文：[Python Weekly Issue 266](http://eepurl.com/ckV18z)

---
  
欢迎来到Python周刊第266期。让我们直奔主题。 
  
# 来自赞助商  

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/6a426b27-541e-4bd7-b621-23ccdc662301.jpg)](http://www.amazon.com/gp/product/B0185367JQ/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B0185367JQ&linkCode=as2&tag=mymerch-20&linkId=OLIXWD4WZ5X6FFHD)

嘿，Python粉，你想要表达你对**Python**的爱吗？那么，[点击这里](http://www.amazon.com/gp/product/B0185367JQ/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B0185367JQ&linkCode=as2&tag=mymerch-20&linkId=OLIXWD4WZ5X6FFHD)，获取你的T恤，骄傲地穿上它吧。
  
  
# 文章，教程和讲座   
  
[Episode #80: TinyDB: 一个用Python编写的小小的document数据库](https://talkpython.fm/episodes/show/80/tinydb-a-tiny-document-db-written-in-python)

NoSQL和document数据库，例如MongoDB，使得对广泛类型的应用，构建易于发展和维护的快速可扩展软件更容易。嵌入式、基于文件的数据库，例如SQLite，使得不用费什么脑子就可以“传送”一个需要数据库的应用。该数据库只是运行在经常中，因此无需安装和维护。然而，当你尝试相交这两个出色的功能的时候，你会发现选择非常有限。只是没有太多嵌入式document数据库可供选择。如果你是一个Python开发者，并且你想要原生Python解决方案，那么可选择的会更小。这就是为什么我很高兴地向你介绍Markus Siemens和TinyDb。它是一个用于Python的，100%纯python编写的，可嵌入，并且可用pip进行安装的document数据库。
  
[NumPy教程：使用Python进行数据分析](https://www.dataquest.io/blog/numpy-tutorial-python/)

在本教程中，我们将会看到如何使用NumPy来分析酒质量的数据。数据包含了酒的各种属性，例如pH和固定酸度信息，以及每种酒在0到10之间的质量得分。质量得分是至少3个人类品尝测试给出得分的平均值。当我们学习如何使用NumPy时，我们会尽力找出更多关于酒的感知质量。
  
[Podcast.__init__ 第79集 - K Lars Lohn](https://podcastinit.com/k-lars-lohn.html)  

K Lars Lohn有着长而丰富的工作经历，他最近几年是在Mozilla度过的。本周，他与我们分享了他参与到Python中的一些故事，他在Mozilla的工作，以及他对于PyCon US 2016闭幕式的一些想法。他还详细阐述了他所绘制的错综复杂的迷宫，以及在Oregon作为一个有机农民的生活。
  
[使用MongoDB和Python进行模糊搜索](https://medium.com/xeneta/fuzzy-search-with-mongodb-and-python-57103928ee5d)  

在Xeneta，我们运营着世界上最大的集装箱运价数据库，并且在其之上提供强大的风险。一个最基本的用例是找到将某一类型的集装箱从一个港口运送到另一个港口的平均价格。为了做到这点，我们必须提供给用户搜索港口的能力。如同所有的地理相关对象，港口名不一定容易记或者正确拼写，因此，我们的搜索工具必须对拼写错误具有鲁棒性。本文介绍了解决这一问题的一个相当简单而灵活的方法，如果你正运行MongoDB，那么它会特别有用，因为它并不需要外部搜索工具。
  
[使用Rust提高Python性能](https://blog.sentry.io/2016/10/19/fixing-python-performance-with-rust.html)  
  
[使用Flask和Orator ORM构建一个REST API](http://blog.eustace.io/buiding-rest-api-with-flask-orator.html)  
  
[百年孤独：我是如何分析最爱的书的](https://medium.com/@finalfire/one-hundred-years-of-solitude-how-i-analyzed-my-favorite-book-6c20456480c8)  
  
[Python中的静态类型，哎哟，my(py)!](http://blog.zulip.org/2016/10/13/static-types-in-python-oh-mypy/)  
  
[迭代器模式的堆栈](http://garethrees.org/2016/09/28/pattern/)  
  
  
# 好玩的项目，工具和库 
  
[HawkPost](https://github.com/whitesmith/hawkpost)  

生成链接，用户可以用来提交使用你的公钥加密的消息。
  
[python-reference](https://github.com/justmarkham/python-reference)  

一个脚本和Notebook中的Python快速参考。
  
[Sanic](https://github.com/channelcat/sanic)  
类Flask的Python 3.5+ web服务器，编写来更快的运行。
  
[pyvisgraph](https://github.com/TaipanRex/pyvisgraph)

给定一个简单的障碍多边形列表，构建知名度图，然后找到两个点之间的最短路径。
  
[PyMoe](https://github.com/ccubed/PyMoe)  

PyMoe是一个python接口，用来为Python 3提供一些流行的动画与漫画服务。
  
[urlwatch](https://github.com/thp/urlwatch)  
urlwatch旨在帮助你监控web页面的改动，并且一旦有任何改动，就会（通过电子邮件，在你的终端中，或者使用自定义编写的报告(reporter)类）通知你。改动提醒会包含发生了改动的URL，以及所发生变化的统一差异。
  
[Dragonchain](https://github.com/dragonchain/dragonchain)

Dragonchain平台试图简化真实商业应用到区块链的集成。提供诸如轻松集成、业务数据保护、固定5秒区块（5 second blocks）、货币不可知论和互操作功能，Dragonchain让区块链技术显得新颖有趣。
  
[fast-neural-style.tf](https://github.com/junrushao1994/fast-neural-style.tf)

用于实时艺术风格传输的前馈式神经网络。
  
[lptrace](https://github.com/khamidou/lptrace)

lptrace是用于Python程序的堆栈跟踪工具。它让你实时看到一个Python程序正在运行什么函数。在调试生产上的疑难杂症特别有用。
  
[mocker](https://github.com/tonybaloney/mocker) 

Docker的一个概念证明模仿版本，100%用Python编写。对Linux使用内核命名空间，cgroup和网络命名空间/iproute2。
  
  
# 近期活动和网络研讨会 
  
[线上事件：有关pandas的答疑！](https://www.crowdcast.io/e/pandas/register)  

这是关于pandas的一个1小时问答环节，pandas是用来进行数据分析、探索和操作的Python库。欢迎各种层次的pandas用户参加以及提问！这里，任何问题都不会太简单或太复杂。 
  
[旧金山Django 2016年10月聚会 - San Francisco, CA](https://www.meetup.com/The-San-Francisco-Django-Meetup-Group/events/234806317/)  

将会有以下演讲

  * Django Rest框架：技巧、诀窍和hack
  * Django和Jinja2模板的两步舞

