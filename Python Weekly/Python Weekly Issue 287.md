原文：[Python Weekly - Issue 287](http://eepurl.com/cHK54v)

---

欢迎来到Python Weekly第287期。本周干货满满。尽情享用！

  
# 文章，教程和讲座  
  
[构建安全A.I.](https://iamtrask.github.io/2017/03/17/safe-ai/)

在这篇文章中，我们会训练一个在训练期间完全加密的神经网络（在未加密数据上进行训练）。结果将会是一个带两个有益属性的神经网络。首先，该神经网络的智能被保护，以免遭受窃取，允许在不安全的环境中对有价值的AI进行训练，而无需冒着智能被窃取的风险。其次，该网络可以只进行加密预测（可假定对外部世界没有影响，因为外部世界无法在没有秘钥的情况下理解这个预测）。这在用户和超级智能之间创造了一种有价值的权利不均衡。如果AI被同态加密，那么从它的角度来看，整个外部世界也被同态加密了。人类控制秘钥，并且可以选择解锁AI自身 (将它释放到世界中)，还是仅仅解锁AI进行的个别预测 (看起来更安全)。  
  
[如何用Python挖掘新闻源数据，并且提取交互式洞察](http://ahmedbesbes.com/how-to-mine-newsfeed-data-and-extract-interactive-insights-in-python.html)  

在这个教程中，我们还将了解一个具体用例的数据。我们会从60个不同的来源 (Google News, The BBC,
Business Insider, BuzzFeed,等等)收集新闻源。我们会将它们转换成一种可用格式作为输入。然后，应用一些机器学习技术来根据它们的相似性进行文章聚类，最后，对结果进行可视化，以获取高水平的洞察。这将提供给我们一种对分组聚类和基础主体的可视化感受。这些技术是我们所说的主题挖掘的一部分。

  
[Podcast.__init__ 第101集 - 与Tobias Oberstein和Alexander Godde聊聊Crossbar.io](https://www.podcastinit.com/episode-101-crossbar-io-with-tobias-oberstein-and-alexander-goedde/)  

随着我们的系统架构和物联网不断地将我们推向分布式逻辑，我们需要一种方式来在这些各种各样的组件之间路由流量。Crossbar.io是web应用程序消息传递协议 (WAMP)的原始实现，这个协议将远程过程调用 (RPC) 和发布/订阅 (PubSub) 通信模式组合到单个通信层里。在这一集中，Tobias Oberstein描述了当你在一个高吞吐量和低延迟系统中拥有一个基于事件的RPC时，可能会出现的用例和设计模式。

  
[Python和JSON：使用Pandas处理大型数据集](http://www.dataquest.io/blog/python-json-tutorial/)  

在这篇文章中，我们将会看看如何利用诸如Pandas这样的工具来探索并在地图上标出Montgomery County, Maryland的警察活动。我们先会看看JSON数据，然后再用Python对JSON进行探索和分析。

  
[Episode #103：通过PyLLVM和MongoDB，为数据科学家编译Python](https://talkpython.fm/episodes/show/103/compiling-python-through-pyllvm-and-mongodb-for-data-scientists)  

我们先看看使用LLVM编译器和一个称为PyLLVM的项目（使用普通的Python代码，将其编译为已优化的机器指令，并在一个集群中分发它），为机器学习优化Python代码的一部分。在下半部分，我们看看给写Python的数据科学家的一个和MongoDB一起使用的神话般的新方法。这个项目称为bson-numpy，它提供MongoDB和NumPy之间的直接连接，并且比标准的pymongo快10倍。

  
[使用data.world Python库加速你的数据采集](https://www.dataquest.io/blog/datadotworld-python-tutorial/)  

在处理数据的时候，工作过程的一个重要部分是查找和导入数据集。能够快速的定位数据，理解它，并将其与其他来源进行合并可能是困难的。一个可以帮助你的工具是data.world，其中，你可以搜索、拷贝、分析和下载数据集。另外，你也可以上传你的数据到data.world，然后用它与他人协作。在这个教程中，我们将会向你展示如何使用data.world的Python库来轻松用你的Python脚本或者Jupyter notebook玩耍数据。
  
[无反向传播的深度学习](https://iamtrask.github.io/2017/03/21/synthetic-gradients/)  

在这篇文章中，我们将会（从头开始）原型化和学习DeepMind最近提出的“使用合成梯度的解耦神经网络接口（Decoupled Neural Interfaces Using Synthetic Gradients）”论文背后的逻辑。
  
[在Keras和TensorFlow中实现的五种视频分类方法](https://medium.com/@harvitronix/five-video-classification-methods-implemented-in-keras-and-tensorflow-99cad29cc0b5)  

在这篇文章中，我们将会介绍使用Keras和TensorFlow后端分类视频的不同策略。我们将尝试学习如何将五种深度学习模型应用于具有挑战且研究较多的UCF101数据集。


[Python的函数是一类对象](https://dbader.org/blog/python-first-class-functions)  

Python的函数是一种一类对象。你可以将它们赋值给变量，将其存储到数据结构中，将它们作为参数传递给其他函数，甚至是在其他函数中将它们作为值返回。在这篇文章中，我将会引导了解一些例子，以助你拥有这些直观的理解。这些例子将会构建在其他例子之上，因此你也许想要按顺序阅读它们，甚至是边读边在Python解释器中试试它们。

[使用scikit-learn和Python，从评论中预测Yelp星级](http://www.developintelligence.com/blog/2017/03/predicting-yelp-star-ratings-review-text-python/)  

在这篇文章中，我们将看看来自Yelp Dataset Challenge的评论。我们会训练一个深度学习系统，仅仅基于一个评论的文本来预测它的星级。例如，如果文本是"Everything was great! Best
stay ever!!"，那么我们会预计是5星评级。如果文本是"Worst stay of my life. Avoid at all costs"，那么我们预计回事1星评级。我们可以训练一个机器学习分类器，通过给它标记好的例子，来“学习”积极评论和消极评论之间的差异，而不是编写一系列的规则来确定一些文本是正面的还是负面的。
  
[在AWS GPU上运行Jupyter notebooks：入门者指南](https://blog.keras.io/running-jupyter-notebooks-on-gpu-on-aws-a-starter-guide.html)  

这是一个开始在AWS GPU实例上运行深度学习Jupyter notebooks，同时在你的浏览器中随时随地编辑notebooks的手把手指南。如果在你的本机上没有GPU，那么这就是深入学习研究的完美设置。
  
[数据科学的基本统计：使用Python的案例研究，第一部分](http://www.learndatasci.com/data-science-statistics-using-python/)  

我们最后一篇文章直接进入到线性回归。在这篇文章中，我们将回顾一下每个数据科学家都应该知道的基本统计信息。为了论证这些要点，我们会看看一个假设的案例研究，它涉及一个负责提高Tennessee学校表现的管理者。
  
[用mypy给你的Jupyter Notebook加点料](http://journalpanic.com/post/spice-up-thy-jupyter-notebooks-with-mypy/)  

添加类型检查到Jupyter Notebook单元。

[自组织地图：全面介绍](http://blog.yhat.com/posts/self-organizing-maps-2.html)  

在第一部分，我介绍了自组织地图 (SOMs)的概念。现在在第2部分，我想要介绍一下训练的过程，以及使用一个SOM，包括直觉和Python代码。最后，我还会介绍几个现实生活中的用例，而不仅仅是我们将用于实现的小例子。

[使用机器学习过滤初创公司的新闻 —— 第一部分](https://blog.monkeylearn.com/filtering-startup-news-machine-learning/)  

在这个新的文章系列，我们会分析成千上万篇来自TechCrunch, VentureBeat和Recode的文章，以发现初创公司的酷炫趋势和见解。在这第一篇文章中，我们会介绍如何使用Scrapy来获取曾经在这些技术新闻网站上发布的所有文章，以及可以如何使用MonkeyLearn来根据它们是否是初创公司来过滤这些爬下来的文章。我们想要创建一个初创公司新闻文章的数据集，稍后，它们可以被用来学习趋势。

[黑进虚拟内存：Python字节](https://blog.holbertonschool.com/hack-the-virtual-memory-python-bytes/)  
  
[高级网络爬虫：绕过"403 Forbidden," 验证码，等等](http://sangaline.com/post/advanced-web-scraping-tutorial/)  
  
[Python中使用感知哈希的重复图像检测](http://tech.jetsetter.com/2017/03/21/duplicate-image-detection/)  
  
[在Dask中开发Convex优化算法](https://matthewrocklin.com/blog//work/2017/03/22/dask-glm-1)  
  
[随机步进贝叶斯深层网络（Random-Walk Bayesian Deep Networks）：处理非固定数据](http://twiecki.github.io/blog/2017/03/14/random-walk-deep-net/)  
  
[分析和优化你的Python代码](https://toucantoco.com/back/2017/01/16/python-performance-optimization.html)  
  
[Pandas和Seaborn - 优雅处理和可视化数据的指南](https://tryolabs.com/blog/2017/03/16/pandas-seaborn-a-guide-to-handle-visualize-data-elegantly/)  
  
[添加AJAX交互到Django中，不用写Javascript啦！](https://petercuret.com/add-ajax-to-django-without-writing-javascript/)  
  
  
# 好玩的项目，工具和库  
  
[aeneas](https://github.com/readbeyond/aeneas)  

aeneas是一个Python/C库和一组工具，用来自动化同步音频和文本 (又名强制排列)。
  
[visdom](https://github.com/facebookresearch/visdom)  

用于创建、组织和共享实时丰富数据的可视化的灵活工具。支持Torch和Numpy。
  
[better-exceptions](https://github.com/Qix-/better-exceptions)  

Python中自动的漂亮而有用的异常。  
  
[Instant-Lyrics](https://github.com/bhrigu123/Instant-Lyrics)  

显示当前播放的Spotify歌曲，或者任何歌曲的歌词。
  
[flango](https://github.com/kennethreitz/flango)  

使用flask作为前端，Django作为后端的一个Django模板。

[Bit](https://github.com/ofek/bit)  

Bit是Python最快的比特币库，并且从头设计以顺应直接，以毫不费力地使用，并且具有可读源代码。它受到Requests和Keras的深刻启发。
  
[bulbea](https://github.com/achillesrasquinha/bulbea)  

基于深度学习的Python库，用以股票市场预测和建模。

[PytchControl](https://github.com/WyattWismer/PytchControl)  

通过基于python的音调检测控制你的电脑
  
[pythonnet](https://github.com/pythonnet/pythonnet)  

Python for .NET是让Python开发者近乎无缝集成.NET公共语言运行库 (CLR)，并为.NET开发者提供强大应用脚本工具的一个软件包。它允许Python代码与CLR进行交互，并且也可能用来嵌入Python代码到.NET应用中。

  
[selenium-respectful](https://github.com/SerpentAI/selenium-respectful)  

极简Selenium WebDriver封装器，用于同时使用在任意数量的网站速度限制之内。并行处理友好。
  
[python-cx_Oracle](https://github.com/oracle/python-cx_Oracle)  

遵循Python DB API 2.0规范的Oracle数据库的Python接口。
  
[flask-rest-jsonapi](https://github.com/miLibris/flask-rest-jsonapi)  

根据JSONAPI 1.0规范构建REST API的Flask扩展。
  
[plugin.video.netflix](https://github.com/asciidisco/plugin.video.netflix)  

Kodi的基于Inputstream的Netflix插件。
  
[DiscoGAN-pytorch](https://github.com/carpedm20/DiscoGAN-pytorch) 

"Learning to Discover Cross-Domain Relations with Generative Adversarial Networks"的PyTorch实现。
  
  
# 最新发布  
  
[Python 3.6.1](https://docs.python.org/3.6/whatsnew/changelog.html#python-3-6-1)  
  
[Django 1.11 候选版本1](https://www.djangoproject.com/weblog/2017/mar/21/django-111-rc-1-released/)  
  
[PyPy2.7和PyPy3.5 v5.7 - 二合一版本](https://morepypy.blogspot.nl/2017/03/pypy27-and-pypy35-v57-two-in-one-release.html)  
  
  
# 近期活动和网络研讨会  
  
[SoCal Python 2017年3月聚会 - Los Angeles, CA](https://www.meetup.com/socalpython/events/238244773/) 
 
将会有以下讲座

  * Magic Method, on the wall, who, now, is the `__fairest__` one of all? 
  * Cooking with Bio-Python

  
[模块生命周期：如何测试和部署你的Python Web应用 - Philadelphia, PA](https://www.meetup.com/phillypug/events/237877302/)  

本次技术研讨会将会遵循开发者在使用一个Python模块时所采用的步骤：单元测试代码的修改；构建该模块的部署版本；使用已部署的版本，对应不同的OS运行集成测试。单元测试也将会作为集成测试，针对多个Python版本运行。与会者将会更加了解如何测试和部署Python Web应用。
