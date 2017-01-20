原文：[Python Weekly Issue 278](http://eepurl.com/cyas5j)

---
  
欢迎来到Python Weekly第278期。本周，让我们直入主题。 
  
# 来自赞助商 

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/6a426b27-541e-4bd7-b621-23ccdc662301.jpg)](http://www.amazon.com/gp/product/B0185367JQ/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B0185367JQ&linkCode=as2&tag=mymerch-20&linkId=OLIXWD4WZ5X6FFHD)

嘿，Python粉，你想要表达你对**Python**的爱吗？那么，[点击这里](http://www.amazon.com/gp/product/B0185367JQ/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B0185367JQ&linkCode=as2&tag=mymerch-20&linkId=OLIXWD4WZ5X6FFHD)，获取你的T恤，骄傲地穿上它吧。

  
# 新闻 
  
[PEP 541 -- 包索引名保留](https://www.python.org/dev/peps/pep-0541/)  
  
[Google Tensorflow选择了Keras](http://www.fast.ai/2017/01/03/keras/)  
  
  
# 文章，教程和讲座
  
[Python异步I/O漫游](http://pgbovine.net/python-async-io-walkthrough.htm)  

在这个90分钟视频系列中，Philip Guo介绍了一本名为使用asyncio协程的网络爬虫(A Web Crawler With asyncio Coroutines)的书中的关于Python中的异步I/O的这一章节，这本书的共同执笔者是Python的创建者。
  
[在Instagram禁用Python垃圾回收](https://engineering.instagram.com/dismissing-python-garbage-collection-at-instagram-4dca40b29172)  

通过禁用Python垃圾收集 (GC) 机制（通过收集和释放未使用数据来回收内存），Instagram可以获得10%的性能增益。是哒，你没听错！通过禁用GC，我们可以减少内存占用，提高CPU LLC缓存命中率。如果你对原因感兴趣，那么开始行动吧！
  
[Episode #95: Grumpy：在Go上运行Python](https://talkpython.fm/episodes/show/95/grumpy-running-python-on-go)  

Google运行数百万行Python代码。驱动youtube.com和YouTube API的前端服务器主要是用Python写的，并且它每秒服务数百万的请求！在这一集中，你将会见到Dylan Trotter，他的工作是为驱动YouTube的这些服务器提高性能和并发性。他刚刚发布了Grumpy：一个基于Go（来自Google的高并发语言）的Python实现。
  
[使用深度学习识别红绿灯](https://medium.freecodecamp.com/recognizing-traffic-lights-with-deep-learning-23dae23287cc)  

我是如何在10周内学习深度学习，并赢取$5,000的。
  
[抓取Craft Beers](http://www.jeannicholashould.com/python-web-scraping-tutorial-for-craft-beers.html)  

这篇文章分为两个部分：爬取和整理数据。在第一部分，我们将计划编写代码来从一个站点收集一个数据集。在第二部分，我们将会应用“整洁数据”原则到这个新鲜抓取的数据集上。在这篇文章的最后，我们将会得到一个干净的craft beers数据集。
  
[Podcast.__init__ 第92集 -  与Dwayne Bailey和Ryan Northey聊聊Translate House](https://www.podcastinit.com/episode-92-translate-house-with-dwayne-bailey-and-ryan-northey/)  

什么是国际化，何时你应该将其添加到你的程序中，以及你如何开始？本周，Dwayne Bailey和Ryan Northey告诉我们有关他们Translate House的工作，以及他们构建来更容易转换你的软件的不同项目。
  
[使用fastText进行自然语言处理简介](https://github.com/miguelgfierro/sciblog_support/blob/master/Intro_to_NLP_with_fastText/Intro_to_NLP.ipynb)  

在这个notebook中，我们将会讨论如何使用fastText的一个python封装器，fastText.py，来轻松实现多个项目。
  
[Python中的断言语句](https://dbader.org/blog/python-assert-tutorial)  

如何使用断言来帮助自动检测你的Python程序中的错误，以便让它们更可靠、更易于调试。  
  
[使用AWS Lambda和Python索引Amazon Elasticsearch Service中的元数据](https://aws.amazon.com/blogs/database/indexing-metadata-in-amazon-elasticsearch-service-using-aws-lambda-and-python/)  

这篇文章提供了关于如何使用AWS Lambda和Python，把元数据存储在Amazon Elasticsearch Service (Amazon ES)中的渐进式指导。
  
[Python 3的str的唯一一个问题是你并没有充分理解它](http://sircmpwn.github.io/2017/01/13/The-problem-with-Python-3.html)  
  
[使用Dask array的一个集群上的分布式NumPy](http://matthewrocklin.com/blog/work/2017/01/17/dask-images)  
  
[教程：PyTorch中的深度学习](https://iamtrask.github.io/2017/01/15/pytorch-tutorial/)  
  
[寻找HTTP请求API之间的共同性](https://snarky.ca/looking-for-commonality-among-http-request-apis/)  
  
[入门Spark Streaming, Python, 和Kafka](https://www.rittmanmead.com/blog/2017/01/getting-started-with-spark-streaming-with-python-and-kafka/)  
  
[了解Python的下划线( _ )](https://hackernoon.com/understanding-the-underscore-of-python-309d1a029edc)  
  
  
# 好玩的项目，工具和库
  
[PyTorch](http://pytorch.org/)  

Python中的Tensors和动态神经网络，具有很强的GPU加速。
  
[itermplot](https://github.com/daleroberts/itermplot)  

一个用于Matplotlib的超棒的iTerm2后端，这样，你就可以直接在终端上绘图。
  
[waveconverter](https://github.com/paulgclark/waveconverter)  

用于RF逆向工程的开源工具。
  
[image-learner](https://github.com/schenker/image-learner/)  

训练一个神经网络来将一个图像的x,y像素映射到r,g,b。
  
[Frozen-Flask](http://pythonhosted.org/Frozen-Flask/)  

Frozen-Flask将一个Flask应用冻结到一组静态文件中。该结果可以在除了传统的web服务器外，无需任何服务端软件的情况下被托管。
  
[tifu](https://github.com/c0riolis/tifu)  

TIFU是一个工具，用来帮助恢复被Github, Gitlab和Bitbucket上的强制push擦除的提交。
  
[rePy2exe](https://github.com/4w4k3/rePy2exe)  

用于py2exe应用的逆向工程工具。
  
[DonaldTrumpStockMonitor](https://github.com/Mhyles/DonaldTrumpStockMonitor)  

监控Donald Trumps的推特，如果有哪个公司的名字出现在他的推特上，那么监控这个公司下周的股票水平。
  
[WaterNet](https://github.com/treigerm/WaterNet)  

识别卫星图像中的水的卷积神经网络。
  
[snake_8x8_dotmatrix](https://github.com/lucasbrambrink/snake_8x8_dotmatrix)

用Python写的8x8蛇。也提供HC595和Dot matrix接口。  
  
  
# 最新发布
  
[Matplotlib 2.0](https://github.com/matplotlib/matplotlib/releases)  
  
[Python 3.5.3](https://www.python.org/downloads/release/python-353/)  
  
[Django 1.11 alpha 1](https://www.djangoproject.com/weblog/2017/jan/17/django-111-alpha-1/)  
  
  
# 近期活动和网络研讨会
  
[使用RPLY和RPython构建你自己的语言 - Philadelphia­, PA](https://www.meetup.com/phillypug/events/236726527/)  

我们将使用RPLY构建DIVSPL，它是David Beazley的PLY的一种实现 (但是有一个“更酷的”API)，并且将其与RPython，Python编程语言的有限子集，兼容。一路下来，你将学习到词法分析器，解析器和语法，最后，你将会知道如何构建你自己的编程语言。
  
[San Diego Python 2017年1月聚会 - San Diego, CA](https://www.meetup.com/pythonsd/events/236030102/)  

将会有以下讲座

  * python派生分析
  * 制作交互式科学应用上的bokeh和jupyter notebooks对比
  * WYSIWYG基于Web的HTML编辑器

  
[PYKC 2017年1月每月聚会 - Kansas City, MO](https://www.meetup.com/pythonkc/events/232904085/)  
  