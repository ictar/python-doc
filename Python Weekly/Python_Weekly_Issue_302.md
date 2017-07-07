原文：[Python Weekly - Issue 302](http://eepurl.com/cU-W3r)

---

欢迎来到Python Weekly第302期。本周，让我们直入主题。
 

# 文章，教程和讲座  
  
[使用Linux，Python和树莓派，酿酒](https://opensource.com/article/17/7/brewing-beer-python-and-raspberry-pi)  

使用Python和树莓派，构建自制酿酒软件的便捷方法。
  
[空间，时间和杂货](https://tech.instacart.com/space-time-and-groceries-a315925acf3a)

在Instacart，我们提供了大量的杂货。到明年年底，80%的美国家庭将能够使用Instacart。我们的挑战是：准时完成每一次交货，并尽可能快地提供正确的货品。在一周的过程中，我们多次遍及美国的各个城市，并提供货品。我们是怎样在杂乱中井井有条的？在这篇文章的剩余部分，我们将首先介绍Instacart正在解决的物流问题，概述我们的系统架构，并描述我们收集的GPS数据。然后，我们将会通过浏览一系列的datashader可视化来得出结论。
  
[了解pandas中的SettingwithCopyWarning](https://www.dataquest.io/blog/settingwithcopywarning/) 

SettingWithCopyWarning是人们在学习pandas时碰到的最常见障碍之一。快速进行网页搜索，你将会发现大量的来自程序员的Stack Overflow问题，GitHub问题和论坛帖子，他们绞尽脑汁试图搞明白在特定的情景下该警告的含义。对此有诸多挣扎并不令人感到奇怪；有许许多多的方式对pandas的数据结构进行索引，每个都有其特定的细微差别，甚至于pandas自身都不能保证对于两行代码的同种输出会看起来相同。本指南解释了为什么会生成这种警告，并且向你介绍如何解决它。它还介绍了内部细节，以便让你更好了解发生了什么，并且提供关于这个主题的一些历史信息，让你了解为什么它都以这种方式工作。
  
[如何处理机器学习中的不平衡类](https://elitedatascience.com/imbalanced-classes) 

不平衡类使得“准确性”失去意义。这是机器学习（特别是分类）中令人惊讶的常见问题，发生于每个类中存在不恰当比例的观察的数据集中。标准的准确性不再可靠地评估性能，这使得建模训练更加棘手。在这个指南中，我们将探索5种处理不平衡类的有效方法。
  
[PonyGE2：Python中的文法演进](https://arxiv.org/pdf/1703.08535.pdf)  

文法演进 (Grammatical Evolution, GE)是一个基于人口的演进算法，其中，一个形式化文法被用于基因型以表型映射过程。PonyGE2是Python中GE的一种开源实现，由UCD的自然计算研究和应用组开发。它旨在作为给GE新人的广告和起点，为学生和研究人员提供参考，为我们自己的实验提供快速原型介质，以及一个Python练习。除了为GE的表型映射提供特征基因型外，还提供一个搜索算法引擎。已经开发了一些关于如何使用和调整PonyGE2的示例问题和教程。
  
[将你的Python函数作为REST API进行部署](https://datascience.ibm.com/blog/deploy-your-python-functions-as-a-rest-api/)

本教程演示了如何使用Bluemix和Flask，将任意的Python函数作为API进行部署，并配有干净直观的Swagger API文档。
  
[Django项目优化指南（第二部分）](http://dizballanze.com/django-project-optimization-part-2/)

这是django项目优化系列的第二部分。这个部分将会关于使用数据库优化 (Django模型)。
  
[Podcast.__init__ 第116集 - 和Glyph Lefkowitz聊聊Automat状态机](https://www.podcastinit.com/automat-state-machines-with-glyph-lefkowitz-episode-116/)

珍贵的‘if’语句是程序流程和业务逻辑的基石，但有时，它会变得笨重，导致不可维护的软件。一种可以生成更干净更容易理解的代码的替代方案是状态机。本周，Glyph解释了Automat是怎样创建的，以及它是怎样被用于升级Twisted项目的部分的。

[快速提示：使用进程池加速你的Python数据处理脚本](https://medium.com/@ageitgey/quick-tip-speed-up-your-python-data-processing-scripts-with-process-pools-cf275350163a)  
  
[使用daiquiri轻松进行Python日志记录](https://julien.danjou.info/blog/python-logging-easy-with-daiquiri)  
  
[Python中的市场篮子分析介绍](http://pbpython.com/market-basket-analysis.html)  
  
  
# 书籍  
  
[The Python 3 Standard Library by Example (Developer's Library)](http://amzn.to/2sO8egU) 

Python 3标准库包含了数百个用于与操作系统、解释器和网络交互的模块，所有这些模块都进行了广泛的测试，并准备好快速启动应用开发。现在，Python专家Doug Hellmann通过简洁的源代码和输出样例，介绍了Python 3.x库的每个主要领域。Hellmann的样例充分展示了每个特性，旨在轻松学习和重用。你将会找到处理文本、数据结构、算法、日期/时间、数学、文件系统、持久化、数据交换、压缩、归档、加密、进程/线程、网络、互联网功能、电子邮件、开发人员和语言工具、运行时、包等等的实用代码。每个部分完全涵盖一个模块，其中包含附加资源的链接，这使得本书成为理想的教程和参考资料。
  
  
# 本周的Python工作  
  
[Beauhurst招聘全栈开发人员](http://jobs.pythonweekly.com/jobs/full-stack-developer-5/)

Beauhurst的使命是寻找并追踪英国的每一个野心勃勃的高增长业务。这进展顺利，事实上，我们已经是这类信息位列第一的数据源。我们已经建立了一个聪明的在线平台，以便于与用户分享空前数量的信息。他们告诉我们，这是非常有价值的，但是，我们自己作为一个野心勃勃的公司，还没有完成此类的工作。如果你是一名无畏的全才，喜欢使用Django和Python，并且不介意踩坑，那么，这可能就是一份适合你的工作。
  
  
# 好玩的项目，工具和库  
  
[Datashader](https://github.com/bokeh/datashader)  

Datashader是一个图形管道系统，用于快速灵活地创建大型数据集的有意义展示。Datashader将图像的创建分解为一系列允许在中间展示上进行的明确的步骤。该方法允许自动生成精确有效的可视化，并且也让数据科学家能够简单地以原则性的方法，关注特定的数据和感兴趣的关系。

[CraftBeerPI](https://github.com/manuel83/craftbeerpi)   

基于树莓派的家庭酿造软件。
  
[Dipy](https://github.com/nipy/dipy)  

DIPY是一个用于分析MR扩散成像的python工具箱。它实现了用于去燥、注册、重建、跟踪、聚类、可视化和MRI数据统计分析的广泛算法。
  
[Iris](https://github.com/linkedin/iris)   

Iris是一个高度可配置的灵活服务，用于分页和信息传递。
  
[SOTA-Py](https://github.com/mehrdadn/SOTA-Py)  

用于随机准时到达路由问题的离散时间Python解算器。
  
[logzero](https://github.com/metachris/logzero)   

强大而有效的日志记录，适用于Python 2和3。
  
[ssl_logger](https://github.com/google/ssl_logger)  

解密并记录进程的SSL流量。
  
[kube-shell](https://github.com/cloudnativelabs/kube-shell)  

与Kubernetes CLI一起使用的集成shell。
  
[daiquiri](https://github.com/jd/daiquiri)  

轻松设置基本的日志功能的Python库。
  
[Susanoo](https://github.com/ant4g0nist/Susanoo)  

一个REST API安全测试框架。
  
  
# 最新发布  
  
[Django问题修复版本：1.11.3](https://www.djangoproject.com/weblog/2017/jul/01/bugfix-release/)  
  
  
# 近期活动和网络研讨会  
  
[使用python的数据科学介绍 - New York, NY](https://www.meetup.com/NYDataScientists/events/240372745/)  

虽然了解数据科学绝不会更简单，但是有个指南来帮助你开始征程却是很有用的。在这个免费的实践工作室中，和galvanize一起，你会通过python编程语言，学到数据科学的基础知识。
  
[Boulder Python 2017年七月聚会 - Boulder, CO](https://www.meetup.com/BoulderPython/events/239106652/)  

将会有以下演讲：

  * Python字典的替代品
  * 使用Stream的API来构建一个社交活动Feed
  * 使用Flask和Docker开发微服务
  
[Python展示之夜（Python Presentation Night） #53 - Minneapolis, MN](https://www.meetup.com/PyMNtos-Twin-Cities-Python-User-Group/events/239200309/)  

将会有以下演讲：

  * 在无辜的Python项目中创建恶意后门
  * 使用Python和OpenGL进行最新数学
  * Jupyter notebook集群特性 (ipyparallel)

[IndyPy 2017年七月每月聚会 - Indianapolis, IN](https://www.meetup.com/indypy/events/239881564/)  

本月，Jeff Licquia将会带来“在云端部署Python”。
  
[Austin Python 2017年七月聚会 - Austin, TX](https://www.meetup.com/austinpython/events/239803285/)  
  
[PyAtl 2017年七月聚会 - Atlanta, GA](https://www.meetup.com/python-atlanta/events/237560610/)  
  


