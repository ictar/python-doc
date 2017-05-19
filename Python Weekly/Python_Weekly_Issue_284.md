原文：[Python Weekly - Issue 284](http://eepurl.com/cEro-H)
---

欢迎来到Python周刊第284期。让我们直奔主题。


# 文章，教程和讲座  
  
[Django Channels入门](https://realpython.com/blog/python/getting-started-with-django-channels/)  

在这篇教程中，我们会使用Django Channels来创建一个实时的应用，它会在用户登录和注销时更新用户。有了在客户端和服务器之间管理通信的WebSockets (通过Django Channels)，每当认证了一个用户时，就会广播一个事件给其他每一个连接的用户。每个用户的屏幕都会自动改变，无需他们自己加载浏览器。
  
[Podcast.__init__ 第98集 - 和Jeff Reback聊聊Pandas](https://www.podcastinit.com/episode-98-pandas-with-jeff-reback/)  

在Python界，Pandas是进行数据操作和分析的最通用和最广泛使用的工具之一。本周，Jeff Reback解释了为什么会那样，你可以如何用它来让你的生活更轻松，以及在未来的几个月中，你可以期待的东西。
  
[在AWS上，使用Python分析4百万Yelp评论](http://www.developintelligence.com/blog/2017/02/analyzing-4-million-yelp-reviews-python-aws-ec2-instance/)  

Yelp每年都有一次数据竞赛，邀请人们探索其真实世界的数据集，以获取独特见解。在这篇文章中，我们将介绍如何把数据集加载到在一个强大但便宜的AWS spot实例上运行的Jupyter Notebook中，并得出一些初步的探索和可视化。
  
[利用Python, pandas和statsmodels，通过线性回归预测房价](http://www.learndatasci.com/predicting-housing-prices-linear-regression-using-python-pandas-statsmodels/)  

在这篇文章中，我们将通过建立线性回归模型来预测由经济活动导致的房价。
  
[现代Django](https://github.com/djstein/modern-django)  

一篇关于怎样在2017年部署基于Django的Web应用的指南。
  
[使用基于坐标的神经网络的超分辨率](http://liviu.me/blog/super-resolution-using-coordinates-networks)  

这是一篇探索在除了缩小图像自身之外，无需使用训练数据的情况下，使用神经网络扩展图像的快速发布。
  
[Python 3.6中的后现代错误处理](http://journalpanic.com/post/postmodern-error-handling/)  

如何在一些类型错误发生之前捕获它们。
  
[防止Python中的SQL注入 (以及其他漏洞)](https://blog.sqreen.io/preventing-sql-injections-in-python/)  

随着应用复杂度的增加，可能很容易在无意中引入潜在问题和漏洞。在这篇文章中，我将会强调可能会引发最大问题的最容易忽略的问题和漏洞，如何避免，以及帮助你节省时间的工具和服务。
  
[在MacOS应用上嵌入Python](https://medium.com/python-pandemonium/embedding-a-python-application-in-macos-d866adfcaf94)  

通过pyinstaller，嵌入一个Python应用到MacOS cocoa应用中。
  
[客户细分的初学者指南](http://blog.yhat.com/posts/customer-segmentation-python-rodeo.html)  

在这篇文章中，我将详细介绍你可以如何使用K均值聚类来帮助进行客户细分的某些探索。
  
[Zendesk是如何在生产上为TensorFlow模型服务的](https://medium.com/zendesk-engineering/how-zendesk-serves-tensorflow-models-in-production-751ee22f0f4b)  
  
[利用LDAP连接池缩放Django+Gevent](https://medium.com/@joey_tallieu/scaling-django-gevent-with-ldap-connection-pooling-d2c5cbb60a40)  
  
[如果构建一个可扩展的爬虫来在短短2个小时内，仅用单个机器即可爬取百万页面](https://medium.com/@tonywangcn/how-to-build-a-scaleable-crawler-to-crawl-million-pages-with-a-single-machine-in-just-2-hours-ab3e238d1c22)  
  
[PyPy3上的异步HTTP基准](https://morepypy.blogspot.com/2017/03/async-http-benchmarks-on-pypy3.html)  
  
[达美乐比萨(Domino's Pizza)通知器](http://www.technologyversus.com/pizza/)  
  
  
# 好玩的项目，工具和库  
  
[Machine Learning From Scratch](https://github.com/eriklindernoren/ML-From-Scratch)  

从头开始用Python实现一些基础机器学习模型和算法。
  
[DeepVideoAnalytics](https://github.com/AKSHAYUBHAT/DeepVideoAnalytics)  

Deep Video Analytics提供了一个从视频和图像索引和提取信息的平台。使用深度学习检测和识别算法来用检测到的对象索引各个帧/图像。Deep Video analytics的目标是成为一个用于开发视觉和视频分析应用的快速可定制平台，同时受益于与视觉研究社区发布的状态(state)或者艺术模型无缝集成。
  
[Prophet](https://github.com/facebookincubator/prophet)  

用于生成用于时间序列数据（具有线性或非线性增长的多季节性）的高质量预测的工具。
  
[mazesolving](https://github.com/mikepound/mazesolving)  

解决来自输入图像的迷宫的各种算法。
  
[keep](https://github.com/OrkoHunter/keep)  

Meta CLI工具包：个人shell命令保存器
  
[BrainDamage](https://github.com/mehulj94/BrainDamage)  

一个功能齐全的后门，使用Telegram作为C&C服务器。
  
[pdftabextract](https://github.com/WZBSocialScienceCenter/pdftabextract)  

用于从PDF文件中提取表单的一组工具，以助于在（OCR处理过的）扫描文档上进行数据挖掘。
  
[Staffjoy](https://github.com/Staffjoy/suite)  

Staffjoy V1，又名"Suite" - 给数百名工作者的的时间安排应用。
  
[facebook-bot](https://github.com/srcecde/facebook-bot)  

一个基本的Python facebook机器人，它可以按照所需的时间间隔，自动点赞和更新状态。你所需要做的只是将状态数据提交给机器人。无需API Developer key。
  
[SWA-Scraper](https://github.com/wcrasta/SWA-Scraper)  

一个爬取西南航空公司网站并展示当前机票的最低价格的命令行工具。如何当前的最低价格在你指定的某些阈值之下，那么，将会发送一条短信给你。
  
[TensorFlow-NRE](https://github.com/thunlp/TensorFlow-NRE)  

神经关系提取旨在通过神经模型从纯文本中提取关系，这是关系提取的最先进的方法。在这个项目中，我们提供了我们词级和句子级组合的双向GRU网络 (BGRU+2ATT)的实现。 
  
[PythonBuddy](https://github.com/ethanchewy/OnlinePythonLinterSyntaxChecker)  

带有即时语法检查和执行的Python编辑器。
  
  
# 最新发布  
  
[Django问题修复版本发布：1.10.6](https://docs.djangoproject.com/en/1.10/releases/1.10.6/)  
  
  
# 近期活动和网络研讨会  
  
[San Francisco Python 2017年3月聚会 - San Francisco, CA](https://www.meetup.com/sfpython/events/237795676/)

Eli Uriegas将会介绍Sanic是如何使用异步的，它的优点，以及我们在Python的标准文档中看到的标准的httprequests / sleep和print应用之外的有用的async/await应用。他还会提供一些例子，包括asyncio协议实现、通过异步函数使用那个asyncio协议，以及如何在一个asyncio循环中运行所有这些东东 (优选uvloop)。然后，了解旧金山海湾地区最有前途的Python驱动的新兴公司。
  
[PyAtl 2017年3月聚会 - Atlanta, GA](https://www.meetup.com/python-atlanta/events/233890712/)  

将会有以下讲座

  * 给普通人的Python Packaging
  * 我是如何停止焦虑，并学着在Python中使用一点点线程的

  
[Austin Python 2017年3月聚会 - Austin, TX](https://www.meetup.com/austinpython/events/235872974/)  
  



