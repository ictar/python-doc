原文：[Python Weekly Issue 267](http://eepurl.com/clVF5r)
  
欢迎来到Python周刊第267期。让我们直奔主题。 
  
# 新闻  
  
[现在，每个Python包都预装在了Repl.it —— 一个在线REPL](https://repl.it/site/blog/python-import)  
  
[PEP 531 —— 存在性检查操作符](https://www.python.org/dev/peps/pep-0531/)  
  
  
# 文章，教程和讲座 
  
[Episode #81: 天文学中的Python和机器学习](https://talkpython.fm/episodes/show/81/python-and-machine-learning-in-astronomy)  

在过去的一个世纪里，天文学的进步是人类聪明才智的最高层次的证据和证明。通过研究光的频率，我们学习到宇宙正在膨胀。通过观察水星的轨道，我们证明了爱因斯坦的广义相对论是正确的。当你知道Python和数据科学在当代天文学发挥着核心作用的时候，你可能不会感到惊讶。本周，你将会遇见Jake VanderPlas，一个来自华盛顿大学的天文学家和数据科学家。加入到Jake和我对天文学中Python的状态的讨论中来吧。
  
[在Django ORM中解决性能问题](https://medium.com/@hansonkd/performance-problems-in-the-django-orm-1f62b3d04785)  

对于利用ORM，并没有一个一刀切的答案。大部分时候，小应用的性能增益并没什么大不了的。你应该首先寻求让你的代码变得清晰，然后才去优化它。随着你的应用的增长，在使用ORM的时候，进行好的清理是很重要的。现在，关于资源的消耗，养成良好的习惯将会在随后获得很大的好处。
  
[Podcast.__init__ 第80集 - 和Sean Gillies聊聊把Python用于GIS](https://podcastinit.com/sean-gillies-python-gis.html)  

随着我们有了越来越多的连接到互联网的带GPS功能的设备，位置信息日渐成为软件系统一个相关的方面。GIS (地理资讯系统)是用来处理和分析这些数据的，幸运的是，Python拥有一套库来助力这些努力。本周，Sean Gillies，许多这些工具的作者和贡献者，会跟我们分享他职业生涯的故事及贡献，还有他在MapBox的工作。
  
[防弹Django模型](https://medium.com/@hakibenita/bullet-proofing-django-models-c080739be4e)  

最近，我们添加了一个类银行账户的功能到我们其中一个产品中。在开发期间，我们遇到了一些教科书式问题，因此我认为这是重温我们在我们的Django模型中使用的一些模式的一个好机会。
  
[如何编写你自己的Python文档生成器](https://medium.com/@tryexceptpass/python-introspection-with-the-inspect-module-2c85d5aa5a48)  

使用inspect模块进行检查。
  
[使用Airflow调度Spark作业](https://blog.insightdatascience.com/scheduling-spark-jobs-with-airflow-4c66f3144660)  

本文介绍了如何使用Airflow来调度由从S3下载Reddit数据触发的Spark作业。
  
[集成Pandas, Django REST框架和Bokeh](http://www.machinalis.com/blog/pandas-django-rest-framework-bokeh/)

使用Pandas和Django REST框架使用基于Ajax的Bokeh图表。
  
[使用Numpy进行图像处理](http://www.degeneratestate.org/posts/2016/Oct/23/image-processing-with-numpy/)  
  
[话说乡村歌曲中的卡车，啤酒和爱 —— 分析天才歌词](https://bigishdata.com/2016/10/25/talkin-bout-trucks-beer-and-love-in-country-songs-analyzing-genius-lyrics/)  
  
[隐式矩阵分解介绍](http://blog.ethanrosenthal.com/2016/10/19/implicit-mf-part-1/)  
  
[PyData DC 2016视频集](https://www.youtube.com/playlist?list=PLGVZCDnMOq0qLoYpkeySVtfdbQg1A_GiB)  
  
[在Python中开发CLI应用的轻松（漂亮）的方式](https://medium.com/@trstringer/the-easy-and-nice-way-to-do-cli-apps-in-python-5d9964dc950d)  
  
[不要把重要数据放到你的Celery队列中](https://www.caktusgroup.com/blog/2016/10/18/dont-keep-important-data-your-celery-queue/)  
  
[一个在Django使用Braintree的指南](https://blog.bitlabstudio.com/a-guide-for-using-braintree-in-django-53e99d677f71)  
  
[如何在Python中快速编写一个MapReduce框架](https://medium.com/@nidhog/how-to-quickly-write-a-mapreduce-framework-in-python-821a79fda554)  
  
[为Discord创建聊天机器人](https://medium.com/@sto5e/creating-chatbots-for-discord-90407e1bf382)  
  
  
# 书籍
  
[Programming Raspberry Pi 3: Getting Started With Python](http://amzn.to/2fhhNTD)

24小时内，学会使用树莓派套件，并且学会编写Python！这本指导书将会确保你具备对树莓派3进行编程的完整知识。
  
  
# 本周Python工作
  
[Patch招聘CTO/首席程序员](http://jobs.pythonweekly.com/jobs/cto-lead-developer/)  

Patch正在寻找CTO/首席程序员。作为扩张公司的一部分，我们正在扩展我们的技术团队。加入我们，有机会大大影响我们的电子商务平台，帮助规整我们正在创造的新服务。
  
  
# 好玩的项目，工具和库  
  
[RapidSMS](https://www.rapidsms.org/)

RapidSMS是一个用来按规模快速构建移动服务的免费开源框架。RapidSMS使用Python和Django构建的，设计来使用基于web的面板构建健壮且高度定制的移动服务。RapidSMS提供了一个灵活的平台和模块化组件，用来进行大规模数据收集、管理复杂工作流和自动化数据分析。
  
[is-service-up](https://github.com/marcopaz/is-service-up)  

IsServiceUp帮助你在一个web页面中监控弄所有你依赖的云服务。你可以使用你想要监控的服务来对其自定义，并且在你自己的服务器上托管它。
  
[DeepTeach](https://github.com/mldbai/deepteach)  

DeepTeach是一个MLDB.ai插件，允许用户通过一个迭代的过程，教会一个机器学习模型他正在寻找什么类型的图像。它是人类增强的一个好的例子，其中，机器学习被用来让人类更有效。
  
[Deform](https://github.com/Pylons/deform)  

Deform是一个Python表单库，用来在服务端生成HTML表单。默认支持日期和时间选择小部件、富文本编辑器、带动态添加和删除功能的表单，以及起一些其他复杂用例。

[paco](https://github.com/h2non/paco)  

用于在Python +3.4中进行协程驱动的基于异步的泛型编程的小工具库。
  
[CNN-Sentence-Classifier](https://github.com/shagunsodhani/CNN-Sentence-Classifier)

“用于句子分类的卷积神经网络（Convolutional Neural Networks for Sentence Classification）”论文的简化实现。

[keras-molecules](https://github.com/maxhodak/keras-molecules)  

自动编码网络，用来学习分子结构的连续表示。
  
[illuminOS](https://github.com/idimitrakopoulos/illuminOS)  

一个用于启用了WiFi的微控制器的基于SDK的开源MicroPython。
  
[yip](https://github.com/balzss/yip)

用来搜索PyPI的前端，一个功能丰富的pip搜索替代品。
  
  
# 最新发布 
  
[MicroPython 1.8.5](https://github.com/micropython/micropython/releases/tag/v1.8.5)

这个版本增加了一个新的MicroPython端口，来运行在Zephyr实时操作系统之上。作为这个的一部分，现在有了将mbedTLS作为ussl模块使用的基本支持。该版本还通过一个Python模块接口，带来了在具有低堆内存的裸机系统（例如esp8266）上运行包管理器upip的初始支持。
  
[Django REST框架3.5](http://www.django-rest-framework.org/topics/3.5-announcement/)

3.5版本时计划中的系列的第二个版本，它提供schema生成，超媒体支持，API客户端库，以及最后实时支持。
  
  
# 近期活动和网络研讨会 
  
[线上事件 - 开放PyCraft编码服务器](https://www.facebook.com/events/552075598331982/) 

在周日，也就是10月30号，我们将有一个开放的PyCraft服务器。这是我们的PyCraft编码服务器，在上面，学生在“玩”Minecraft的时候，还可以学习编程。我们会运行一些有趣的脚本，让我们的客人四处看看，咨询我们导师问题，以及跟我们度过一段时间……或许甚至有机会运行一些脚本。
  
