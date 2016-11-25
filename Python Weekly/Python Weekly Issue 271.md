原文：[Python Weekly - Issue 271](http://eepurl.com/cp_sQf)

---

欢迎来到Python Weekly第271期。享受这周的出版，并且如果你过的话，感恩节快乐！


# 文章，教程和讲座  
  
[Episode #85: 用Python解析可怕的东西](https://talkpython.fm/episodes/show/85/parsing-horrible-things-with-python)  

你是否曾经需要解析可怕而令人费解的东西呢？显然，你会从这一集中学到一堆技巧和窍门。但是，你将看到，高级解析是通往许多有趣的计算机科学技术之路。听听我与Erik Rose谈谈他在Mozilla解析奇奇怪怪的东西之旅吧。
  
[深入了解地理空间分析](http://nbviewer.jupyter.org/github/ResidentMario/boston-airbnb-geo/blob/master/notebooks/boston-airbnb-geo.ipynb)

数据科学家处理的许多数据集都有某些地理空间部分，而这种信息对于理解手头上的问题常常是至关重要的。这样，理解空间数据，以及如何使用它们就成为了任何数据科学家具有的宝贵技能。更妙的是，Python提供了用于该领域的丰富的工具集，并且最近的进展大大简化和巩固了它们。在这个教程中，我们将深入了解Python中的地理空间分析，使用诸如geopandas, shapely, 和pysal这样的工具来分析由Kaggle（来源于Inside AirBnB）提供的数据集，它带有Boston, Massachusetts中的AirBnB位置样本。
  
[用Pandas构建金融模型](http://pbpython.com/amortization-model.html)  

在我前面的文章中，我讨论了如何使用pandas作为数据操作工具来替代Excel。在许多情况下，python + pandas解决方案优于许多人在Excel中用于操作数据的高度手动过程。然而，Excel被用于商业环境下的许多常见 - 不仅是数据操作。这篇详细的文章将讨论如何用pandas取代Excel进行金融建模。在这个例子中，我将在pandas中构建一个简单的分期偿还债务表，并且展示如何建模各种成果。
  
[Django站点上的水印图像](http://www.machinalis.com/blog/watermarking-images-django/)  

使用Pillow和django-imagekit生成可见和不可见水印的技术。
  
[贝叶斯线性回归](https://github.com/liviu-/notebooks/blob/master/bayesian_linear_regression.ipynb)  

贝叶斯线性回归的快速演示 -- 特别是，我想要向你展示你可以如何找到一个从中你可以取样权重来适应于你的数据集的高斯分布的参数！然后，你可以把这个分布当成找到预测分布和利用置信水平的先验使用。我会尽量解释我所做的所有事，但你应该知道线性回归、一些线性代数和一些贝叶斯统计数据。
  
[给工作中的Python开发者的AsyncIO](https://hackernoon.com/asyncio-for-the-working-python-developer-5c468e6e2e8e)  
  
[iSee: 使用深度学习来从脸部移除眼镜](https://blog.insightdatascience.com/isee-removing-eyeglasses-from-faces-using-deep-learning-d4e7d935376f)  
  
  
# 书籍
  
[智能网算法](http://amzn.to/2fSfV2T)  

智能网算法教你如何创建紧缩和争论（crunch and wrangle）那些从用户、web应用和网站日志收集来的数据的机器学习应用。在这个完全修订版，你将看到从数据中提取实际价值的智能算法。通过使用Python的scikit-learn编写的代码样例，解释了关键的机器学习概念。这本书将引导你通过算法捕获、存储和结构化来自网络的数据流。通过统计算法、神经网络和深度学习，你将探索推荐引擎，并深入分类。
  
  
# 好玩的项目，工具和库 
  
[Speech-Hacker](https://github.com/ParhamP/Speech-Hacker)

通过连接名人的话，让他们说出任何你想他们说的话。
  
[pipfile](https://github.com/pypa/pipfile)  

一个Pipfile，及其相关的Pipfile.lock，是pip的requirements.txt文件的新的（及好得多的！）替换品。 
  
[Kalliope](https://github.com/kalliope-project/kalliope)  

Kalliope是一个模块化的不间断声控个人助手，专为家庭自动化而设。
  
[Pydoc](https://www.pydoc.io/)

Pydoc是一个为每一个上传到PyPI的包构建自动生成的API文档的项目。它类似于诸如
http://rubydoc.info/ 和 https://godoc.org/这样的项目 -- 但是是用于Python社区的。一旦获取了PyPI中所有的包，我们就希望也把它扩展到GitHub上的包。
  
[gzint](https://github.com/pirate/gzint)  

一个python3库，用于有效存储大整数 (用 gzip 压缩的整数表示)。
  
[NBA-prediction](https://github.com/christopherjenness/NBA-prediction)  

使用矩阵完备预测NBA比赛比分。
  
[Quiver](https://github.com/jakebian/quiver)  

为Keras提供的互动式卷积神经网络特性可视化
  
[Streamlink](https://github.com/streamlink/streamlink)  

Streamlink是一个CLI工具，它将在线流服务的Flash视频导到各种视频播放器，例如VLC，或者浏览器。streamlink的主要目的在于转换重CPU的Flash插件到一个较少CPU密集的格式。
  
[colorcet](https://github.com/bokeh/colorcet)  

一套用于绘制科学数据的有用的感知一致的色图。
  
  
# 最新发布
  
[Python 3.6.0 beta 4](http://blog.python.org/2016/11/python-360-beta-4-is-now-available.html)  
  
[PyCharm 2016.3](https://www.jetbrains.com/pycharm/whatsnew/)  
  
  
# 近期活动和网络研讨会 
  
[AnacondaCON 2017](https://anacondacon17.io/)

一场致力于聚集开放数据科学（Open Data Science）和Anaconda社区最强大脑的盛会。在AnacondaCON，你将接触到使用Anaconda的企业客户，以及那些开放数据科学运动中，利用社区力量和革新的基础贡献者和思想领袖。议程将充满教育性、知识性和发人深省的部分，确保你轻松获得提高开放数据科学所需的知识和连接。
  
[San Francisco Django 2016年12月聚会 - San Francisco, CA](https://www.meetup.com/The-San-Francisco-Django-Meetup-Group/events/235111262/)  

将会有以下演讲

  * Django + Ionic: 从POC到生产
  * 使用Django Channels的REST Websockets API

