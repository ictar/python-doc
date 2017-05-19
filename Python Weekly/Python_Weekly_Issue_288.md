原文：[Python Weekly - Issue 288](http://eepurl.com/cIQbBv)

---

欢迎来到Python Weekly第288期。本周，让我们直入主题。 

  
# 文章，教程和讲座  
  
[pdb教程](https://github.com/spiside/pdb-tutorial)  

有效使用pdb的一个简单的教程。
  
[使用深度学习分类白细胞](https://blog.athelas.com/classifying-white-blood-cells-with-convolutional-neural-networks-2ca6da239331)  

如果你可以像体温一样快速便宜地发现你的血细胞数目的话，那么你的医疗保健看起来会是怎样的呢？在Athelas，我们着迷于这种可能性，并相信，在现代深度学习技术的助力下，这样的未来将在可见之中。在这篇文章中，我们将演示一个关于如何利用深度学习技术来分类白细胞图像的简单玩具示例。我们的示例模型将会把白细胞分类为多核或者单核，在参考数据集上精度达到98%。
  
[Episode #105: 一次Pythonic数据库漫游](https://talkpython.fm/episodes/show/105/a-pythonic-database-tour)  

之所以说现在是成为一个开发者的好时机，是有许多原因的。其中之一就是，关于数据访问和数据库，有很多选择。所以，本周，我们和我们的嘉宾Jim Fulton会漫游一些你可能没有听过或者尝试过的数据库。你会听到纯Python数据库ZODB。有Zero DB，一个端到端加密数据库，其中，数据库服务器对它所存储的数据一无所知。以及NewtDb，跨越了ZODB和JSON友好型的Postgres世界。
  
[Python的实例、类和静态方法揭秘](https://realpython.com/blog/python/instance-class-and-static-methods-demystified/)  

在这个教程中，我会帮你揭秘类方法、静态方法和常规实例方法背后的东西。如果对于它们的不同，你有了直观的了解，那么你将能够编写面向对象的Python，从而更清楚地传达其意图，并从长远来看，将更容易维护。
  
[仅需$100，动手构建一个可对话的面部识别门铃](https://www.oreilly.com/ideas/build-a-talking-face-recognizing-doorbell-for-about-100)  

用Amazon Echo+树莓派进行DIY：以极低价格，每月识别你门口的成千上万个人。
  
[Podcast.__init__ 第102集 - 和Brian Warner聊聊数字身份、隐私和安全](https://www.podcastinit.com/episode-102-brian-warner/)  

随着互联网和数字技术不断渗透我们的生活方式，我们被迫考虑在这些空间中，如何反映我们的身份和安全概念。本周，Brian Warner加入我们，探讨他从事的关于聚焦隐私的项目的工作，包括Tahoe LAFS，Firefox Sync和Magic Wormhole。他还有一些关于我们可以如何替换密码，以及具有在线身份意味着什么的有趣想法。
  
[如何用Python处理丢失数据](http://machinelearningmastery.com/handle-missing-data-python/)  

真实世界的数据常常会丢了一些值。数据有某些值缺失有很多原因，例如未记录的观察结果和数据损坏。处理数据丢失的值很重要，因为许多机器学习算法不支持缺少值的数据。在这个教程中，你会发现如何使用Python为机器学习处理丢失数据。
  
[开始使用Apache Airflow开发工作流](http://michal.karzynski.pl/blog/2017/03/19/developing-workflows-with-apache-airflow/)  

Apache Airflow是一个用于编排复杂计算工作流和数据处理管道的开源工具。如果你发现自己在运行执行长长脚本的cron任务，或者安排了大数据处理批量任务，那么Airflow也许可以帮到你。这篇文章为那些想要开始使用Airflow编写管道的人提供了一个介绍性教程。
  
[Dask和Pandas，以及XGBoost](http://matthewrocklin.com/blog/work/2017/03/28/dask-xgboost)  

这篇文章谈到了使用Dask的分布式Pandas Dataframes，然后将其分发到分配式XGBoost以进行训练。更一般地，它讨论了在相同的共享内存进程中启动多个分布式系统，以及在它们之间平稳地处理数据的价值。
  
[神奇GAN，以及它们在哪里](http://guimperarnau.com/blog/2017/03/Fantastic-GANs-and-where-to-find-them)  

你有没有想过了解生成对抗网络 (GANs)？也许，你只是想要赶上潮流？又或许，你只想要看看在过去的几年中，这些网络的改进？那么，在这些情况下，你也许会对这篇文章感兴趣。
  
[利用一个内存损坏错误逃离Python沙箱](https://hackernoon.com/python-sandbox-escape-via-a-memory-corruption-bug-19dde4d5fea5)  
  
[分类算法的一个神奇介绍](http://blog.yhat.com/posts/harry-potter-classification.html)  
  
[科普君 —— 带递归点符号访问的Python字典](http://www.codecalamity.com/?p=307)  
  
[DeepDream：利用硬件加速深度学习](https://medium.com/@mrubash1/deepdream-accelerating-deep-learning-with-hardware-5085ea415d8a)  
  
  
# 书籍  
  
[Computational and Inferential Thinking](https://www.inferentialthinking.com/) 

这是UC Berkeley的数据科学基础课程的教科书。该课程结合了三个观点：推理思维，计算思维和现实世界相关性。给定一些现实世界现象产生的数据，要如何分析数据以了解相应现象？这门课程教授计算机程序设计和统计推理中的关键概念和技能，结合对现实世界数据集（包括经济数据、文档集合、地理数据和社会网络）的动手分析。它围绕数据分析探索诸如隐私和设计的社会问题。
  
  
# 好玩的项目，工具和库  
  
[LocalStack](https://github.com/atlassian/localstack)  

全功能本地AWS云栈。离线开发和测试你的云应用！
  
[Golem](https://github.com/golemfactory/golem)  

Golem项目的目标是为计算能力创造一个全球专业消费市场，其中，生产者可以销售他们个人计算机的备用CPU时间，而消费者可以为计算密集型任务获得资源。在技术方面，Golem被设计为一个由运行着Golem客户端软件的节点建立的分布式对等网络。根据本文的目标，我们假设在Golem网络中有两种类型的节点：宣布计算任务的请求者节点，和执行计算的计算节点（在实际实现中，节点可以在这两种角色之间切换）。
  
[Computer Vision Drone](http://cvdrone.de/)  

构建一个交互式计算机视觉无人机。
  
[poline](https://github.com/riolet/poline)  

Python一行程序：python中类awk的一行程序。  
  
[pyreportcard](https://github.com/mingrammer/pyreportcard)  

给你的Python应用的报告卡。它审查托管在Github上的python项目，分析源代码质量 (pep8, pyflakes和bandit等等。)，license文件的存在，以及整个代码库的一些有用的统计数据。然后在web上显示它的分析结果。
  
[Delbot](https://github.com/shaildeliwala/delbot)  

它理解你的声音指令，搜索新闻和知识源，然后为你总结和读取内容。
  
[RNN-Tutorial](https://github.com/silicon-valley-data-science/RNN-Tutorial)  

循环神经网络 - 一个简短的TensorFlow教程。
  
[flasgger](https://github.com/rochacbruno/flasgger)  

为你的Flask API提供毫不费力的Swagger UI。
  
[django-cruds](https://github.com/oscarmlage/django-cruds)  

django-cruds是一个简单顺手的django应用，为更快速的原型创造CRUD。
  
[sact](https://github.com/mfigurnov/sact)  

代码实现了一个基于剩余网络的深度学习体系结构，该体系结构自动为图像区域调整执行层数。这个体系结构是端到端可训练的，确定以及问题无关的。包含的代码应用这个到CIFAR-10一个ImageNet图像分类问题上。使用TensorFlow和TF-Slim实现。
  
[portSpider](https://github.com/xdavidhu/portSpider )  

一个带模块的轻量级快速多线程网络扫描器框架。（Ele注：貌似404了）
  
[PyMedium](https://github.com/enginebai/PyMedium)  

PyMedium是用python flask写的非官方Medium API。它让开发者能够访问Medium站点的用户、博文列表和详细信息。这是一个访问Medium公开信息的只读API，你可以自定义这个API来适配你的需求，以及部署在你自己的服务器上。
  
  
# 近期活动和网络研讨会  
  
[在线课程：音乐应用的音频信号处理](https://www.coursera.org/learn/audio-signal-processing)  

在这个课程中，你将会学习到音频信号处理方法，这些方法专用于音乐和实际应用。我们关注于与声音描述和转换相关的频谱处理技术，通过在音乐应用的背景下，分析、合成、转换和描述音频信号，来提高基本理论和实践知识。
  
[New York Python 2017年4月聚会 - New York, NY](https://www.meetup.com/nycpython/events/237182654/)  

将会有以下讲座

  * Pandas新特性！
  * Theano和Python！

  



