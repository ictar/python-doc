原文：[Python Weekly - Issue 299](http://eepurl.com/cSIjsr)

---

欢迎来到Python Weekly第299期。本周，我想要感谢我们的赞助商Datadog的支持。一定要看看他们免费的棒棒哒的解决放啊。
  
# 来自赞助商  

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/4d68e311-8c60-4c65-b078-603ce0d8ffb8.png)](https://www.datadoghq.com/dg/apm/ts-python-performance/?utm_source=Advertisement&utm_medium=Advertisement&utm_campaign=PythonWeekly-Tshirt&utm_content=Python)

[怎样监控Python应用](https://www.datadoghq.com/dg/apm/ts-python-performance/?utm_source=Advertisement&utm_medium=Advertisement&utm_campaign=PythonWeekly-Tshirt&utm_content=Python)  

关于实时Python指标的图表和告警，并与你的栈中的150多种其他技术生成的数据相关联。

  
# 新闻  
  
[NumPy首次获得资金资助](https://www.numfocus.org/blog/numpy-receives-first-ever-funding-thanks-to-moore-foundation/)  

有史以来，NumPy获得了资助。提案 —— "为了更好的数据科学，改进NumPy" —— 将会在两年中收到来自摩尔基金会的$645,020，资金将投入到加州大学伯克利分校，用于数据科学。
  
  
# 文章，教程和讲座  
  
[Instagram平缓移植到Python 3](https://thenewstack.io/instagram-makes-smooth-move-python-3/)  

Instagram不仅仅是已经成为世界上最大的Python用户，这个公司最近还移植到了Python 3，并且不对用户体验造成任何影响。Instagram工程师Hui Ding和Lisa Guo聊到了新的技术栈，以分享对Python的爱，并描述了Python 3的迁移体验。
   
[Podcast.__init__ 第113集 - 和David Halter聊聊Jedi代码完成](https://www.podcastinit.com/episode-113-jedi-code-completion-with-david-halter/) 

当你在写python代码，而你的编辑器提供一些建议的时候，那些建议都是从哪来的呢？最有可能的答案是Jedi！本周，David Halter解释了Jedi自动完成库是如何创建的，它的工作原理是什么，以及他计划在哪里采用的历史。
   
[了解Python中的异步编程](https://dbader.org/blog/understanding-asynchronous-programming-in-python)  

如何使用Python编写异步程序，以及为什么要做这样的事。
   
[从Flask-Script迁移到新的Flask CLI](https://blog.miguelgrinberg.com/post/migrating-from-flask-script-to-the-new-flask-cli)  

这篇文章向你展示新的Flask CLI提供的功能，如果你想要迁移你的基于Flask-Script的应用，那么这将会有用。
   
[Pelican使用更快的R-CNN来计数对象](https://softwaremill.com/counting-objects-with-faster-rcnn/)

精确计算一个给定图像或视频帧中的对象实例数量是机器学习中一个难以解决的问题。已经开发了许多解决方法来计数人、汽车和其他对象，但它们没有一个是完美的。当然，这里，我们讲的是图像处理，因此，神经网络似乎是解决这个问题的一个好工具。在这篇文章中，你可以找到关于不同的解决方法、常见问题、挑战和神经网络对象计数领域中的最新方法的描述。作为对概念的证明，将会使用现有的更快的R-CNN网络模型来计算文章末尾给出的视频中街道上的对象数。
   
[如何使用Pelican和Jinja2创建你的第一个静态站点](https://www.fullstackpython.com/blog/generating-static-websites-pelican-jinja2-markdown.html)  

Pelican是一个用来创建静态站点的非常完美的Python工具。在这个教程中，你将学到如何使用Pelican，从头开始创建你自己的静态站点。
   
[破解一个由Jupyter notebook驱动的播客的方法](https://nipunbatra.github.io/blog/2017/Jupyter-powered-blog.html)  

一篇关于通过Jupyter notebook驱动博客和相关的挣扎的文章！
   
[给Python程序员的Apache Kafka介绍](https://www.confluent.io/blog/introduction-to-apache-kafka-for-python-programmers/) 

在这篇文章中，我们将会回归基础知识，并了解如何开始为你的Python应用使用Apache Kafka。
   
[用Django REST框架和基于类的视图构建一个API](https://medium.com/@ktruong008/building-an-api-with-django-rest-framework-and-class-based-views-75b369b30396)  
    
[5种让Django Admin更安全的方法](https://hackernoon.com/5-ways-to-make-django-admin-safer-eb7753698ac8)  
  
[GOAI：开放GPU加速数据分析](https://devblogs.nvidia.com/parallelforall/goai-open-gpu-accelerated-data-analytics/)  
   
[用Python检测虚假视频](http://sunnybala.com/2017/05/28/python-video-loop-detection.html)  
   
  
# 好玩的项目，工具和库  
  
[ShutIt](https://github.com/ianmiell/shutit)  

ShutIt是一个对用户终端操作进行建模的自动化工具。它可以毫不费力地自动化人类在命令行中可以运行的任何过程。
   
[Music Genre Classifier](https://github.com/indrajithi/mgc-django)  

基于流派分类音乐的机器学习方法。 
   
[Spans](https://github.com/runfalk/spans)   

Spans是https://github.com/damianavila/RISE的range类型的纯Python实现。
   
[Sudoku](https://github.com/Kyubyong/sudoku)   

神经网络可以破解数独吗？已经有各种解决数独的方法了，包括计算方法。这个项目展示了简单的卷积神经网络在无需任何基于规则的后处理情况下破解数独的潜力。
   
[RISE](https://github.com/damianavila/RISE)  

RISE: "Live" Reveal.js —— Jupyter/IPython Slideshow扩展
   
[DLTK](https://github.com/DLTK/DLTK)  

用于医学图像分析的深度学习工具包。
   
[Snaek](https://github.com/mitsuhiko/snaek)  

Snaek是一个Python库，帮你构建Rust库，并通过cffi将它们与Python结合在一起。
   
[scikit-video](https://github.com/scikit-video/scikit-video)  

Scikit-video专为使用Python轻松进行视频处理而设计。它的建模受其他成功的scikits（例如scikit-learn和scikit-image）精髓影响。

[snaptile](https://github.com/jakebian/snaptile)  

用于X11的带功能强大的键盘控件的多功能窗口平铺。
   
[molotov](https://github.com/loads/molotov)  

编写负载测试的简单Python 3.5+工具。
   
[Sounder](https://github.com/SlapBot/sounder)   

一个预测给定文本意图的意图识别算法。
   
[uvicorn](https://github.com/tomchristie/uvicorn)  

一个用于Python 3的快如闪电的asyncio服务器。
   
[VADER-Sentiment-Analysis](https://github.com/cjhutto/vaderSentiment) 

VADER (Valence Aware Dictionary and sEntiment Reasoner) 是一个基于词汇和规则的情绪分析工具，专门针对社交媒体中表达的情绪。
   
  
# 最新发布  
  
[NumPy 1.13.0](https://github.com/numpy/numpy/releases/tag/v1.13.0)  
   
[PyPy v5.8](https://morepypy.blogspot.com/2017/06/pypy-v58-released.html)  
   
  
# 近期活动和网络研讨会  
  
[PyOhio 2017](https://www.eventbrite.com/e/pyohio-2017-tickets-33066656259)  

为Ohio，整个中西部地区，甚至整个世界提供的Python程序员免费年会。
   
[San Diego Python 2017年六月聚会 - San Diego, CA](https://www.meetup.com/pythonsd/events/238867409/)

将会有以下演讲

  * 为Python配置Vim
  * Django路上的磕磕碰碰，以及如何避免

   
[PyHou 2017年六月聚会 - Houston, TX](https://www.meetup.com/python-14/events/239458416/)  
  

 

