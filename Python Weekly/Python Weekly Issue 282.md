原文：[Python Weekly Issue 282](http://eepurl.com/cCgOaT)
  
欢迎来到Python周刊第282期。让我们直奔主题。  
  
# 来自赞助商 

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/fb8e903d-788d-4986-a791-c5be3926cbca.png)](https://www.yhat.com/whitepapers/data-science-in-practice?utm_source=PyWeekly) 

[[白皮书] 数据科学的5个常见应用](https://www.yhat.com/whitepapers/data-science-in-practice?utm_source=PyWeekly)  

在这个白皮书中，我们介绍了五种常见的数据科学应用，以及使用它们来解决实际业务问题的公司示例。不不不，它跟这只猫半毛钱关系都没有。

\- Yhat团队
  
  
# 新闻
  
[DjangoCon US 2017更新：征求建议、指导和财政支持！](https://www.djangoproject.com/weblog/2017/feb/13/djangocon-us-2017-update-call-proposals-mentorship/)  
  
  
# 文章，教程和讲座
  
[Episode #98: 使用Django Channels为Django增加并发性](https://talkpython.fm/episodes/show/98/adding-concurrency-to-django-with-django-channels)  

Python 3中的一个主要的创新领域是异步和并发编程。然而，当使用任何主要的web框架时：django，flask，或者pyramid，基本都没有并发选项。这就是为什么Andrew Godwin决定使用django channels解决django站点上的这个问题。
  
[与François Chollet聊聊深度学习和Keras](https://softwareengineeringdaily.com/2016/01/29/deep-learning-and-keras-with-francois-chollet/ )

Keras是一个简约且高度模块化的神经网络库，用Python编写，并且能够在TensorFlow或者Theano之上运行。它的开发重点是实现快速实验。在这一集中，François讨论了深度学习的状态，并解释了为什么这个领域正在经历一次寒武纪爆炸，并最终可能会逐渐消失。他解释了为什么需要Keras，以及为什么它的简单性和易用性让它成为一个对开发者实验和构建有用的深度学习库。
  
[用OpenCV和Python识别数字](http://www.pyimagesearch.com/2017/02/13/recognizing-digits-with-opencv-and-python/) 

这篇文章演示了如何使用OpenCV和Python识别图像中的数字。在这篇教程的第一部分，我们会讨论七段显示器是什么，以及我们可以如何应用计算机视觉和图像处理操作来识别这些类型的数字 (无需机器学习！)。在那里，我会提供可以用来识别图像中的数字的实际的Python和OpenCV代码。
  
[Podcast.__init__ 第96集 - 和Dave Vandenbout聊聊SKIDL](https://www.podcastinit.com/episode-96-skidl-with-dave-vandenbout/)  

随着电路和电子组件变得更加复杂，可视化电路构建工具变得更难以有效使用。如果你希望你可以只是用Python编写你的电路，那么你很幸运！Dave Vandenbout写了一个名为SKIDL的库，它将Python的强大功能和灵活性带到了电气工程领域，并且，在本周的节目中，他还会告诉我们所有关于这个库的东东。 
  
[利用Uber的Pyflame和日志来解决扩展问题](https://benbernardblog.com/using-ubers-pyflame-and-logs-to-tackle-scaling-issues/)  

在这篇文章中，你将了解我用来诊断我基于Python的网络爬虫的扩展问题（以及一定程度上更一般的性能问题）的技术和工具。
  
[不好了！这个包只能用于Python 2](https://medium.com/@anthonypjshaw/oh-no-this-package-is-python-2-only-8e6316f9a02)  

你埋头进行一个新项目，但是其中一个依赖仍然未支持Python 3 —— 啊！这有一个关于如何解决那个问题的手把手指南。
  
[马尔科夫链图像生成](https://jonnoftw.github.io/2017/01/18/markov-chain-image-generation)  

在这篇文章中，我将会描述一种使用马尔科夫链，从训练图像生成图像的方法。我们训练一个马尔科夫链来存储像素颜色，作为节点值，而相邻像素颜色的数目则成为到相邻节点的连接权重。为了生成图像，我们随机遍历该链，并在输出图像上绘制像素。结果是拥有与原始图像相似的图像调色，但并无一致性的图像。
  
[树莓派上的TensorFlow图像识别](http://svds.com/tensorflow-image-recognition-raspberry-pi/)  
  
[为什么说分层模型是棒棒哒、棘手并且贝叶斯的](http://twiecki.github.io/blog/2017/02/08/bayesian-hierchical-non-centered/)  
  
[用namedtuple编写干净的Python代码](https://dbader.org/blog/writing-clean-python-with-namedtuples)  
  
[Python中有效的数据可视化的一个提示](https://www.dataquest.io/blog/how-to-communicate-with-data/)  
  
[TensorFlow howto：神经网络中的一个通用逼近器](https://blog.metaflow.fr/tensorflow-howto-a-universal-approximator-inside-a-neural-net-bb034430b71e)  
  
[用50行代码实现生成对抗式网络(Generative Adversarial Networks, GANs) (PyTorch)](https://medium.com/@devnag/generative-adversarial-networks-gans-in-50-lines-of-code-pytorch-e81b79659e3f)  
  
  
# 书籍
  
[The Hacker's Guide to Python](http://amzn.to/2kUz1rh)  

Python是一种精彩的编程语言，被越来越多地使用在很多不同的行业中。它快速，灵活，并且功能齐备。你读到的大部分关于Python的书籍教你的是语言基础知识。但一旦你学会了，你就可以自己设计应用，发掘最佳实践。在这本书中，我们将会看到如何利用Python来有效地解决你的问题，以及构建伟大的Python应用。
  
[Tiny Python 3.6 Notebook](https://github.com/mattharrison/Tiny-Python-3.6-Notebook/blob/master/python.rst)

这并非Python介绍。相反，这是一个笔记本，里面包含了Python 3的一些例子，以及Python 3.6中发现的新特性。
  
  
# 好玩的项目，工具和库
  
[ergonomica](https://github.com/ergonomica/ergonomica)  

一个用Python编写的跨平台现代shell。
  
[Bella](https://github.com/manwhoami/Bella)  

一个macOS的纯python、post-exploitation的数据挖掘工具和远程管理工具。
  
[TensorFlowOnSpark](https://github.com/yahoo/TensorFlowOnSpark)  

TensorFlowOnSpark为Apache Hadoop和Apache Spark集群带来了可扩展的深度学习。通过结合深度学习框架TensorFlow和大数据框架Apache Spark和Apache Hadoop的显著特征，TensorFlowOnSpark可以在GPU和CPU服务器集群上实现分布式深度学习。
  
[neat-python](https://github.com/CodeReclaimers/neat-python)  

NEAT神经归化算法的Python实现。
  
[Lark](https://github.com/erezsh/Lark)  

一个带Earley/LALR(1)支持的现代解析库，用Python编写。
  
[ipytracer](https://github.com/sn0wle0pard/ipytracer)  

Jupyter/IPython Notebook的算法可视化。  
  
[django-behaviors](https://github.com/audiolion/django-behaviors)  
轻松集成Django模型的常见行为，例如，时间戳、发布、创作、编辑等等。

[mongoaudit](https://github.com/stampery/mongoaudit)  

mongoaudit是一个审计MongoDB服务器、检测弱安全设置以及执行自动渗透测试的CLI工具。
  
[Flametree](https://github.com/Edinburgh-Genome-Foundry/Flametree)  

Flametree是一个Python库，它为处理文件和文件夹提供了一种简单的语法 (无os.path.join，os.listdir等等)，并且对于不同的文件系统，使用相同的工作方式。
  
[densenet.pytorch](https://github.com/bamos/densenet.pytorch)  

DenseNet的PyTorch实现。
  
  
# 近期活动和网络研讨会
  
[你需要知道的关于Ansible的一切 - 简介 - Philadelphia­, PA](https://www.meetup.com/phillypug/events/236696626/)

来了解一下配置管理和Python代码部署的令人兴奋的世界吧。我们的演讲者将会介绍Ansible，提供大量的详情和用例，以及给问答留下大量的时间。你有关于如何扩展大数据应用部署的挑战或问题吗？想知道如何让你的Django站点在多节点上运行，并使用runserver和SQLite吗？随时提出你的问题！
  
[数据科学家的统计数据 - Houston, TX](https://www.meetup.com/python-14/events/236367843/)  

你正在统计数据世界的“门外”，想要找到敲门砖吗？刷新你的统计数据知识，并希望增加此类知识，这怎么样？如果你想知道概率、随机变量、统计推断、探索性数据分析等等意味着什么，那么，这个讲座将会向你展示抵达这些概念的路线图。
  
[VFX产业中的Python微服务 - London, United Kingdom](https://www.meetup.com/LondonPython/events/237120009/)

这个讲座将会从关于微服务是什么以及为什么它们有用的一个简单介绍开始，然后深入探讨它们在要求严苛的VFX世界中的使用。接着，Python微服务框架nameko背后的人将会像我们展示我们自己可以怎么来做这类很酷的事情。