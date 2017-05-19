原文：[Python Weekly - Issue 277](http://eepurl.com/cxbt0z)

---

欢迎来到Python Weekly第277期。本周，让我们直入主题。

  
# 文章，教程和讲座
  
[介绍Python的类型注释](https://www.youtube.com/watch?v=ZP_QV4ccFHQ)  

Dropbox有几百万行的生产代码都是用Python 2.7写的。作为迁移到Python 3的第一步，以及让我们的代码更易驾驭，我们利用PEP 484，使用类型注释来注释我们的代码，使用mypy来对注释代码进行类型检查。在这个讲座中，我们将讨论经验教训，并向你展示你也可以如何开始对旧的Python 2.7代码进行类型检查，每次一个文件。我们也将会描述在此过程中对mypy所做的许多改进，以及一些其他工具。
  
[深度学习指南](http://yerevann.com/a-guide-to-deep-learning/)  

深度学习是计算机科学和数学交叉的一个快速变化的领域。它是一个名为机器学习的更广泛的领域的一个相当新的分支。机器学习的目标是教会计算机基于给定的数据执行各种任务。这个指南是为那些知道一些数据并且知道一些编程语言，而现在想要深入深度学习的人准备的。
  
[Python机器学习：Scikit-Learn教程](https://www.datacamp.com/community/tutorials/machine-learning-python)  

这个教程将会介绍Python机器学习的基础知识：一步一步，它将会向你展示如何使用Python和它们的库，在matplotlib的帮助下，来探索你的数据，利用知名的算法KMeans和支持向量机 (SVM) 来构造模型，适配数据到这些模型，来预测值，以及验证你构建的模型。
  
[Podcast.__init__ 第91集 - 和Martijn Faassen聊聊Morepath](https://www.podcastinit.com/episode-91-morepath-with-martijn-faassen/)  

Python有广泛并且不断增长的各种web框架以供选择，但是如果你想要一个拥有超能力的框架，那么，你需要Morepath。本周，Martijn Faassen会跟我们分享怎样创造出Morepath，它是如何与其他可用选项区分开来的，以及你可以如何用它来助力你的下一个项目。
  
[神经网络的超参数优化](http://neupy.com/2016/12/17/hyperparameter_optimization_for_neural_networks.html)  

有时，为神经网络选择一个正确的架构会很难。通常，这个过程需要大量的经验，因为网络包含许多参数。让我们检查一些可以用来优化这个神经网络的最重要的参数。
  
[添加一个REST API到Django应用](https://blog.jetbrains.com/pycharm/2017/01/webinar-adding-a-rest-api-to-a-django-application/)  

这个动手讲座将会教你如何利用Python和Django来扩展一个已有的web应用，并添加REST能力。首先，它会对一个构建来跟踪注释的已有的Django应用进行概述，然后深入使用Django REST框架来添加REST。观看者可以跟着我们构建这个Notes web应用。我们会展示使用PyCharm专业版来检查数据库和测试我们的API。我们还会看看如何用强大的PyCharm调试器来调试这个应用。
  
[使用python和pandas分析和可视化公开的OKCupid个人资料数据集](http://nbviewer.jupyter.org/github/lalelale/profiles_analysis/blob/master/profiles.ipynb#Analysis-and-visualization-of-a-public-OKCupid-profile-dataset-using-python-and-pandas)  
  
[Episode #94: 通过Conda和Conda-Forge来保证包](https://talkpython.fm/episodes/show/94/guarenteed-packages-via-conda-and-conda-forge)  
  
[从Python到Numpy](http://www.labri.fr/perso/nrougier/from-python-to-numpy/)  
  
[利用Flask和Mapbox可视化你的旅行](http://kazuar.github.io/visualize-trip-with-flask-and-mapbox/)  
  
[每个Python项目必备之物](https://vladcalin.github.io/what-every-python-project-should-have.html)  
  
[使用机器学习增加来自你可预见客户的销量](https://jackstouffer.com/blog/target-predictable-customers.html)  
  
[编写一个Django应用时我犯的错误 (以及我是如何修复它们的)](https://hackernoon.com/mistakes-i-made-writing-a-django-app-and-how-i-fixed-them-16de4e632042)  
  
[Gumbel机制](https://cmaddis.github.io/gumbel-machinery)  
  
  
# 好玩的项目，工具和库
  
[MuGo](https://github.com/brilee/MuGo)  

这是AlphaGo主要部分的纯Python实现。
  
[Deep Text Correcter](https://github.com/atpaino/deep-text-correcter)  

深度学习模型，训练来修正短的、类消息文本中的输入错误。
  
[PySignal](https://github.com/dgovil/PySignal)  

一个不带QObject依赖的Qt信号系统的纯Python实现。
  
[KickThemOut](https://github.com/k4m4/kickthemout)  

通过执行ARP欺骗攻击来把设备从你的网络中踢掉。
  
[psync](https://github.com/lazywei/psync)  

基于rsync的同步项目；支持监控改动，并自动同步。
  
[predictron](https://github.com/zhongwen/predictron)  

"The Predictron: End-To-End Learning and Planning"的Tensorflow实现
  
[Argos](https://github.com/titusjan/argos)  

Argos是一个用于查看和探索科学数据的GUI，用Python和Qt编写。它有一个插件架构，从而能够扩展以读取新的数据格式。
  
[DVR-Scan](http://dvr-scan.readthedocs.io/en/latest/)  

DVR-Scan是一个跨平台的命令行 (CLI) 应用，它自动检测视频文件（例如，安全监控录像）中的运动事件。除了定位每个运动事件的时间和持续时间外，DVR-Scan将会保持每个动作事件的录像到一个新的独立的视频剪辑中。
  
[systemdlogger](https://github.com/techjacker/systemdlogger)  

导出systemd日志到一个外部服务，例如cloudwatch, elasticsearch。
  
[hsr](https://github.com/pyk/hsr)  

使用在TensorFlow中实现的卷积神经网络进行手势识别。
  
  
# 近期活动和网络研讨会
  
[网络研讨会：为机器学习构建理想堆栈](http://www.oreilly.com/pub/e/3855)  

机器学习从未有过像如今一样更容易靠近 —— 如果你的数据管道支持实时分析的话。参与者将会学习到用于集成不同行业和组织的机器学习模型的工具和技术。Steven Camiña是MemSQL产品经理，他将会介绍你的技术生态系统中所需的关键技术，包括Python, Apache Kafka, Apache Spark, 和一个实时数据库。
  
[New York Python 2017年1月聚会 - New York, NY](https://www.meetup.com/nycpython/events/235725722/)  

将会有以下讲座

  * Paxos简介
  * 测试工具/开源requestbin库
  * 201*年（到底）发生了什么？
  * 异步编程

  
[PyHou 2017年1月聚会 - Houston, TX](https://www.meetup.com/python-14/events/230111277/)  


