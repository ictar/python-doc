原文：[Python Weekly - Issue 273](http://eepurl.com/csvGCX)

---

欢迎来到Python Weekly第273期。本周干货满满。尽情享用！
  
# 来自赞助商 

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/6a426b27-541e-4bd7-b621-23ccdc662301.jpg)](http://www.amazon.com/gp/product/B0185367JQ/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B0185367JQ&linkCode=as2&tag=mymerch-20&linkId=OLIXWD4WZ5X6FFHD)

嘿，Python粉，你想要表达你对**Python**的爱吗？那么，[点击这里](http://www.amazon.com/gp/product/B0185367JQ/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B0185367JQ&linkCode=as2&tag=mymerch-20&linkId=OLIXWD4WZ5X6FFHD)，获取你的T恤，骄傲地穿上它吧。

  
# 新闻
  
[Django论战用户跟踪](https://lwn.net/Articles/707443/)  
  
  
# 文章，教程和讲座 
  
[如何用数据抓住地铁环线的异常地铁](https://blog.data.gov.sg/how-we-caught-the-circle-line-rogue-train-with-data-79405c86ab6a)  

这篇文章详细描述了一组数据科学家是如何调查新加坡地铁环线的神秘中断的。
  
[利用深度学习，寻找一首歌的曲风](https://chatbotslife.com/finding-the-genre-of-a-song-with-deep-learning-da8f59a61194)  

让你的计算机成为一个音乐专家的手把手指南。
  
[榨干uWSGI的每一微秒](https://blog.codeship.com/getting-every-microsecond-out-of-uwsgi/)

这篇文章关于调整通过uWSGIThis post looks into tuning a Python application running via uWSGI.  
  
[为你的聊天机器人利用NLP和机器学习的终极指南](https://chatbotslife.com/ultimate-guide-to-leveraging-nlp-machine-learning-for-you-chatbot-531ff2dd870c)  

这篇文章向你展示如何实现一个在给定对话上下文的情况下，可以分配分数给潜在响应的，基于检索的神经网络模型。

[Episode #88: Lightweight Django](https://talkpython.fm/episodes/show/88/lightweight-django)  

Django是一个非常流行的Python web框架。原因之一就是，对于你的应用的大部分，你有许多构建块可用。需要一个完整的管理员表编辑器后端吗？仅需几行代码，然后，你就有了一个基本的表编辑器啦～这对许多人都适用。但是我们这些人，包括我在内，都欣赏轻量级框架，其中，我们从最好的组成部分选择各个部分，放在一起，组成我们的web应用，因此觉得（django）这样并不好。本周，你将会见到Julia Elman和Mark Lavin，他们是Lightweight Django的作者，他们来到这里，为你驱散Django应用必须基于大构建块进行构建的迷信。

[跟踪一个奇特的Python内存泄漏](https://benbernardblog.com/tracking-down-a-freaky-python-memory-leak/)  

在这篇文章中，你将会发现我是如何使用各种工具，在Windows下跟踪我的Python应用中的内存泄漏的！
  
[介绍：Fastparquet](https://www.continuum.io/blog/developer-blog/introducing-fastparquet)  

fastparquet是Python中对于Parquet格式文件的一个适应性、灵活且快速的接口，提供了内存中pandas DataFrame和磁盘中存储之间的无缝转换。在这篇文章中，我将会介绍两个函数，它们是fastparquet中最常使用的两个函数，接着关于当前的大数据版图的讨论，Python在其中的位置，以及fastparquet在构建Python中一个完整的端到端的大数据管道的过程中如何填补其中一个沟壑的细节。 
  
[Python机器学习教程，Wine Snob版本](https://elitedatascience.com/python-machine-learning-tutorial-scikit-learn)  

在这个端到端Python机器学习教程中，你将会学到如何使用Scikit-Learn来构建和调整一个监督学习模型！
  
[线程化异步魔法，以及如何运用它](https://medium.com/@tryexceptpass/threaded-asynchronous-magic-and-how-to-wield-it-bba9ed602c32)  

深入Python的异步io任务和事件循环。
  
[利用Telegram和Python构建一个聊天机器人 (第2部分)：添加一个SQLite数据库后端](https://www.codementor.io/garethdwyer/tutorials/building-a-chatbot-using-telegram-and-python-part-2-sqlite-databse-backend-m7o96jger)  

在本教程的第一部分，我们使用Python从头构建了一个基本的Telegram聊天机器人。我们的机器人不大聪明，只能简单的将发送给它的东西回显给用户。我们构建的这个机器人是广大可能机器人的良好基础，因为我们可以接收输入，处理它，然后返回结果 —— 经典计算的基础。我们之前的机器人的一个主要限制是它不能存储任何信息。它没有长期记忆。在这部分，我们会添加一个SQLite数据库后端到我们的机器人，允许它永远记住特定用户的信息。我们会构建一个简单的ToDo列表，允许用户添加新的项，或者删除旧有的。
  
[Python中的整洁数据](http://www.jeannicholashould.com/tidy-data-in-python.html)  

在这篇文章中，我会总结Wickham在他的[论文](http://vita.had.co.nz/papers/tidy-data.pdf)中使用的一些整理样例，并且我将会演示如何用Python pandas库来做到。

[Podcast.__init__ 第86期 - 与Alexis Metaireau和Mathieu Leplatr聊聊Kinto](https://www.podcastinit.com/episode-86-kinto-with-alexis-metaireau-and-mathieu-leplatre/)  

你是否在寻找一个这样的后端，它作为服务提供，并且你拥有对你的数据完整的控制权？眼下就有一个：Kinto! 本周，Alexis Metaireau和Mathieu Leplatre分享了Kinto是如何创造出来的，它如何在幕后工作的故事，以及将它用于Mozilla和网络上的方式。
  
[使用Python, Chromedriver和我们在Reddit上好奇的小伙伴来雾化你的谷歌搜索历史。](http://howlroundmusic.org/wp/?p=23)  
  
[分析2016年世界国际象棋锦标赛](https://medium.com/pachyderm-data/analyzing-the-2016-world-chess-championship-b823d0d2fd11)  
  
[用Python写的x86-64玩具反编译器](https://yurichev.com/writings/toy_decompiler.pdf)  
  
[在 < 3.6的版本中，实现python 3.6的print](https://oded.ninja/2016/12/05/723/)  
  
[Python和故事片](http://dgovil.com/blog/2016/11/30/python-for-feature-film/)  
  
[用Scikit-Learnfen lei分类Amazon评论 -- 证明越多数据越好](https://bigishdata.com/2016/12/05/classifying-amazon-reviews-with-scikit-learn-more-data-is-better-turns-out/)  
  
[Python中的时间序列分析 (TSA) - 从线性模型到 GARCH](http://www.blackarbs.com/blog/time-series-analysis-in-python-linear-models-to-garch/11/1/2016)  
  
[使用Python可视化Tweet](http://www.johnwittenauer.net/visualizing-tweet-vectors-using-python/)  
  
[40+个用于数据科学资源的Python统计数据](https://www.datacamp.com/community/tutorials/python-statistics-data-science)  
  
  
# 书籍
  
[微控制器上的Python：开始MicroPython之旅（Python for Microcontrollers: Getting Started with MicroPython）](http://amzn.to/2h7VQnT)  

本DIY指南对用MicroPython进行微控制器编程进行了实用的介绍。本书的作者是一位经验丰富的电子爱好者，微控制器上的Python：开始MicroPython之旅（Python for Microcontrollers: Getting Started with MicroPython）有八个完整的项目，它们清晰地演示了每项技术。你将学到如何使用传感器、存储数据、控制电机和其他设备，以及使用扩展ban扩展板。从那里，你会发现如何自己设计、构建和编程所有类型的娱乐性核实用性项目。
  
  
# 好玩的项目，工具和库 
  
[Universe](https://github.com/openai/universe)  

Universe是一个软件平台，用来跨世界上提供的游戏、网站和其他应用，衡量和训练一个AI的一般智能。
  
[pina-colada](https://github.com/ecthros/pina-colada)  

一个强大可扩展的无线投递箱，能够在网络上进行广泛的远程攻击。可以通过它的命令行界面来控制它，或者连接到它的命令核控制远程服务器来远程控制，通过使用web应用或者Android应用。 
  
[oil](https://github.com/oilshell/oil)  

Oil是一个新的Unix shell，仍处于初期阶段。
  
[DeepAudioClassification](https://github.com/despoisj/DeepAudioClassification)  

从你自己的音乐库中构建一个数据集，并且用它来填补丢失的音乐流派的管道。
  
[malboxes](https://github.com/GoSecure/malboxes)  
构建恶意软件分析的Windows虚拟机，这样你就无需自己构建。

[Gransk](https://github.com/pcbje/gransk)  

Gransk是一个开源工具，旨在cheng wei成为文档处理核分析的瑞士军刀。它的初始目标是在调查期间，快速地为用户提供对他们的文档的洞察。它包含了一个用Python编写的处理引擎和一个web界面。钩子之下，它使用Apache Tika进行内容提取，使用Elasticsearch进行数据索引，使用dfVFS来解压磁盘镜像。
  
[pook](https://github.com/h2non/pook)  

Python中的一个用于HTTP流量模拟和期望的多变、具有表现力且可hack的功能库。

[git-of-theseus](https://github.com/erikbern/git-of-theseus)  

分析一个Git repo如何随时间增长。
  
[fastparquet](https://github.com/martindurant/fastparquet/)  

fastparquet是parquet格式的python实现，旨在集成到基于python的大数据工作流。
  
[foss-heartbeat](https://github.com/sarahsharp/foss-heartbeat)  

FOSS Heartbeat分析贡献者社区的健康度。  
  
[LinNetLim](https://github.com/chozabu/LinNetLim)  

一个LinuxNetlimiting程序。
  
[Harser](https://github.com/sihaelov/harser)  

HTML解析和构建XPath的简单方式。
  
  
# 最新发布
  
[Python 3.6.0 RC1](http://blog.python.org/2016/12/python-360-release-candidate-is-now.html)  
  
[Django问题修复版本发布： 1.10.4, 1.9.12, 1.8.17](https://www.djangoproject.com/weblog/2016/dec/01/bugfix-releases/)  
  
  
# 近期活动和网络研讨会 
  
[Princeton Python用户2016年12月聚会 - Princeton, NJ](https://www.meetup.com/pug-ip/events/236092516/)

Alan Yorinks将会分享他在RedBot机器人平台上使用树莓派（从最初基于Arduino的处理器板子过渡）的经历。
  
[Boulder Python 2016年12月聚会 - Boulder, CO](https://www.meetup.com/BoulderPython/events/235416975/)  

将会有以下演讲

  * 使用Celery来处理Django中的API调用
  * Python和Kafka  
  * Flask和Django颂歌

  
[函数式编程和Python - London, United Kingdom](https://www.meetup.com/LondonPython/events/235125918/)  

在介绍以及（稍微）一点理论之后，我们将看看如何用Python进行函数式编程，以及欣赏这样做的优势，包括写更好的Python代码。
  
[Edmonton Python 2016年12月聚会 - Edmonton, AB](https://www.meetup.com/startupedmonton/events/235763425/)  
  
[Austin Python 2016年12月聚会 - Austin, TX](https://www.meetup.com/austinpython/events/233895154/)  
  