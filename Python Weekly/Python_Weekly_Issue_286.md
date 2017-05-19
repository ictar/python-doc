原文：[Python Weekly - Issue 286](http://eepurl.com/cGFJaz)
---

欢迎来到Python Weekly第286期。本周，让我们直入主题。 
  
# 来自赞助商  

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/6a426b27-541e-4bd7-b621-23ccdc662301.jpg)](http://www.amazon.com/gp/product/B0185367JQ/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B0185367JQ&linkCode=as2&tag=mymerch-20&linkId=OLIXWD4WZ5X6FFHD) 

嘿，Python粉，你想要表达你对**Python**的爱吗？那么，[点击这里](http://www.amazon.com/gp/product/B0185367JQ/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B0185367JQ&linkCode=as2&tag=mymerch-20&linkId=OLIXWD4WZ5X6FFHD)，获取你的T恤，骄傲地穿上它吧。

  
# 新闻  
  
[XLA - TensorFlow, 已编译](https://developers.googleblog.com/2017/03/xla-tensorflow-compiled.html)  

Google已经宣布了XLA (加速线性代数)，这是一个用于TensorFlow的编译器。XLA使用JIT编译技术来分析用户在运行时创建的TensorFlow图，专门用于实际的运行时维度和类型，与多种操作相联合，并为它们产生高效的本地机器码 —— 对于像CPU, GPU和自定义加速器（例如，谷歌的TPU）之类的设备。
  
  
# 文章，教程和讲座  
  
[Podcast.__init__ 第100集 - MetPy: 以Python为辅，驯服天气](https://www.podcastinit.com/episode-100-metpy-with-ryan-may-sean-arms-and-john-leeman/)  

明天的天气怎么样？这是一个气象学家一直试图更好解答的问题。本周，MetPy的开发者讨论了他们的项目是如何用在这个任务上的，以及在大气和天气研究中固有的挑战。处理不确定性，使用凌乱、多维数据来建模一个大规模复杂系统，是相当名人的。
  
[在Python中构建分析数据管道](https://www.dataquest.io/blog/data-pipelines-tutorial/)  

数据管道的一个常见用途是找出你的站点的访问者的相关信息。如果你熟悉Google Analytics，那你就会知道查看访问者的实时和历史信息的价值。在这篇文章中，我们将使用来自web服务器日志的数据来回答有关访问者的问题。
  
[如果丢失的Python源代码仍然驻留在内存中的话，如何恢复它们](https://gist.github.com/simonw/8aa492e59265c1a021f5c5618f9e6b12) 

我弄错了git的使用 (对于错误的文件使用了"git checkout --")，设法删除我刚写好的代码……但是这些代码仍然在一个docker容器中的一个进程里运行。这里，我将展示如何使用pyrasite和uncompyle6，将它们弄回来。
  
[如何编写DSL (在Python中，利用Lark)](http://blog.erezsh.com/how-to-write-a-dsl-in-python-with-lark/)  

在这个教程中，我将向你展示如何用仅仅70行代码，解析和解释一个类Logo语言，并使用这个例子来设计和实现你自己的语言。为此，我们将使用我的解析库Lark，以及Python的turtle模块。让我们开始吧！
  
[检测Apache和Nginx日志中的机器人](http://tech.marksblogg.com/detect-bots-apache-nginx-logs.html)  

由于阻止基于JavaScript的跟踪锚节点的浏览器插件现在有9位数的用户数，因此，网络流量日志是一个可以更好的了解多少人访问你的网站的好地方。但是，任何一个已经监控网络流量日志几分钟的人都会发现，存在一个爬取网站的机器人军队。然而，能够在网络服务器日志中将机器人和人为产生的流量分离出来却是一种挑战。在这篇文章中，我将介绍构建一个IPv4所有权和浏览器基于字符串的机器人检测脚本的步骤。
  
[使用Google Maps Distance Matrix API的等流时线](http://blog.yhat.com/posts/isochrones-isocronut.html)  
  
[SciPy新的LowLevelCallable是一种创新](https://ilovesymposia.com/2017/03/12/scipys-new-lowlevelcallable-is-a-game-changer/)  
  
[Python 3还存在那些WTF的东东？](https://www.reddit.com/r/Python/comments/5zk97l/what_are_some_wtfs_still_in_python_3/)  
  
[适用于Python/Django应用的可用于生产的Dockerfile](https://www.caktusgroup.com/blog/2017/03/14/production-ready-dockerfile-your-python-django-app/)  
  
[神秘Python崩溃的情况](https://benbernardblog.com/the-case-of-the-mysterious-python-crash/)  
  
[Python 3中新的有趣的数据结构](https://github.com/topper-123/Articles/blob/master/New-interesting-data-types-in-Python3.rst)  
  
[标题党重顾：标题+内容特性上的深度学习，以捕获标题党](https://www.linkedin.com/pulse/clickbaits-revisited-deep-learning-title-content-features-thakur)  
  
[Python中的拟合高斯过程模型](https://blog.dominodatalab.com/fitting-gaussian-process-models-python/ )  
  
[Vector Venture - 在Python中使用Tkinter的官方预告片！](https://www.youtube.com/watch?v=XSWWsX7gUp0 )  
  
  
# 本周的Python工作  
  
[Academic Merit招聘首席Python软件工程师](http://jobs.pythonweekly.com/jobs/lead-python-software-engineer/)  

由于我们从此开始为快速的业务增长做准备，因此，AcademicMerit正在寻找一些软件工程师，我们需要你敏捷，对于伟大的软件组件和服务持有自己的观点，并且在交付根据用户期望进行了良好测试的可靠软件方面非常注意细节。软件工程师将有机会参与全栈开发、可视化、用户分析、分布式系统、基于docker容器的服务和机器学习算法。你将工作于给美国和全球各国际学校的教师和高中生使用的关于写作教育和AP课程的多种产品。
  
[MindMeld招聘经验丰富的后台工程师](http://jobs.pythonweekly.com/jobs/seasoned-backend-engineer/)  

作为高级软件工程师，你将负责MindMeld Conversational AI平台的几个组件和功能，并且在财富500强公司的其中一个概念验证或者生产部署中扮演重要角色。你将加入一个团队，这个团队试图为大型词汇知识领域实现最先进的端到端准确性（> 99%），并且满足长尾用户请求。你将主要用python进行编码，并且利用大量的库和框架。你将使用像Amazon Mechanical这样的众包工具来进行数据收集。作为工程团队的早期成员，你将拥有独一无二的机会来构建关键产品功能和基础设施，同时塑造团队和公司的方向。
  
  
# 好玩的项目，工具和库  
  
[pdir2](https://github.com/laike9m/pdir2)  

愉悦的漂亮dir()打印。
  
[face_recognition](https://github.com/ageitgey/face_recognition)  

世界上最简单的Python和命令行面部识别API。
  
[seq2seq](https://github.com/google/seq2seq)  

Tensorflow的通用编码-解码框架。
  
[trio](https://github.com/python-trio/trio) 

Trio是为Python生成一个生产级质量、带有许可的原生async/await I/O库的一种实验性尝试，重点在于可用性和正确性，我们希望使得正确操作变得容易。  
  
[MellPlayer](https://github.com/Mellcap/MellPlayer)  

基于Python3的微型终端播放器。
  
[LXDock](https://github.com/lxdock/lxdock)  

LXDock是LXD的一个封装器，允许开发者使用类似于Vagrant的工作流来编排他们的开发环境。
  
[django-react-blog](https://github.com/raymestalez/django-react-blog)

使用Django和React/Redux构建，通过Docker部署，利用nginx/uwsgi提供服务的简单播客。
  
[Jarvis](https://github.com/sukeesh/Jarvis)  

Linux上的个人助理  
  
[sharingan](https://github.com/vipul-sharma20/sharingan) 

从报纸中提取新闻文章，并提供有关新闻的上下文的工具。
  
[CHIPSEC](https://github.com/chipsec/chipsec)

CHIPSEC是一个用于分析包括硬件、系统固件(BIOS/UEFI)和平台组件的PC平台的安全性的框架。它包括了一个安全测试套件，访问各种低级接口的工具以及取证功能。它可以在Windows, Linux, Mac OS X 和UEFI shell上运行。
  
  
# 最新发布  
  
[Django REST框架 3.6](http://www.django-rest-framework.org/topics/3.6-announcement/)

3.6版本添加了两个主要的新特性到REST框架。

  * 内置的交互式API文档支持。
  * 一个新的JavaScript客户端库。

  
[Keras 2](https://blog.keras.io/introducing-keras-2.html)  
  
  
# 近期活动和网络研讨会  
  
[San Francisco Django 2017年3月聚会 - San Francisco, CA](https://www.meetup.com/The-San-Francisco-Django-Meetup-Group/events/237666575/) 

将会有以下讲座

  * 将Twisted作为你的WSGI容器
  * Django和Webpack + React

  
[PyHou 2017年3月聚会 - Houston, TX](https://www.meetup.com/python-14/events/236367841/)  

Glen Zangirolami将会介绍Asyncio模块 —— 从生成器到Asyncio，我们将会学到一点关于Asyncio的东西，以及它是如何能够在你的个人和专业项目中帮助你的。我们将从生成器开始，并创建我们自己的基础I/O循环来演示Asyncio是如何工作的。从那开始，我们将覆盖基本的Asyncio代码示例。
  
[San Diego Python 2017年3月聚会 - San Diego, CA](https://www.meetup.com/pythonsd/events/237628732/)  

将会有以下讲座

  * Disk-based spooling with Django query
  * Style II元素 - 词选择

  




