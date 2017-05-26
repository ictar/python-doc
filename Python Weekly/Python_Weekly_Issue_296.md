原文：[Python Weekly - Issue 296 ](http://eepurl.com/cPZM5n)

---

欢迎来到Python Weekly第296期。本周，让我们直入主题。
  
# 来自赞助商  
[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/d0352677-9ed4-4c6c-84bb-824d63faadb4.png)](http://www3.activestate.com/activepython-data-science.html?utm_source=python-weekly&utm_medium=email&utm_term=&utm_content=17-05-25-newsletter&utm_campaign=activepython-fc)

专注于算法，而不是准备时间。ActivePython自己预编译了TensorFlow, theano, Keras和其他难以构建的包。通过英特尔数学核心库，优化你的NumPy和SciPy算法。开始为数据科学和机器学习，使用ActivePython[免费开发](http://www3.activestate.com/activepython-data-science.html?utm_source=python-weekly&utm_medium=email&utm_term=&utm_content=17-05-25-newsletter&utm_campaign=activepython-fc)吧。
  
  
# 文章，教程和讲座  
  
[仅用3行Python代码，实现四子棋](https://danverbraganza.com/writings/connect-four-in-3-lines-of-python)  

带解释的终端四子棋的小小实现。
   
[抓取数据 —— 实用指南](https://medium.com/k-folds/scraping-for-data-a-practical-guide-67cc397450b2)  

一篇关于使用Python进行网络抓取、用Urllib和BeautifulSoup从Indeed.com提取工作描述文本以进行数据分析的介绍。
   
[如何使用Python和Asyncio，在10分钟内抓取并解析600个ETF选项](http://www.blackarbs.com/blog/how-to-scrape-and-parse-600-etf-options-in-10-mins-with-python-and-asyncio/5/18/2017)  

这是我正在进行的用Python半实时构建一个功能选项数据仪表盘的新系列的第一部分。在这篇文章中，我展示了关于获取数据的位置、抓取方法、解析方法和以快速读写访问为目的的存储方法的一个当前工作方案。我们将使用aiohttp和asyncio爬取Barchart.com的基本选项应用，这两个都包含在Python 3.6标准库中。我们将使用Pandas和Numpy解析它，并且以HDF5文件格式存储数据。
   
[Podcast.__init__ 第110集 - Yelp!的技术债和重构](https://www.podcastinit.com/tech-debt-and-refactoring-at-yelp-with-andrew-mason-episode-110/)

健康代码造就快乐码农，而由许许多多种方法来衡量一个项目的健康。本周，Andrew Mason聊到了来自Yelp!的Undebt项目，以及一些其他开发来确保他们的技术债卡余额保持低水平的工具和实践。听一听，学习如何以及为什么要测量和解决软件的痛点。
   
[ChatOps和PowerShell](https://www.youtube.com/watch?v=XIMOFnfdOx0)  

写了大量的棒极的PowerShell脚本，但发现难以让你的终端用户或者支持者使用它们，是吗？让我们使用ChatOps解决这个问题吧。我们将快速介绍ChatOps，然后投入Windows上一个Errbot（一个聊天机器人）的安装。在学习Errbot的基础知识后，我们会运行一些代码样例，以便你可以轻松通过聊天将你的脚本传递给用户。最后，我们会介绍涉及到ChatOps和PowerShell时，有哪些安全性。
   
[图灵模式](http://www.degeneratestate.org/posts/2017/May/05/turing-patterns/)  

探索由反应扩散方程产生的模式。 
   
[Python在诉讼下划线的含义](https://dbader.org/blog/meaning-of-underscores-in-python)  

本文讨论了五种下划线模式和命名约定，以及它们是如何影响你的Python程序的行为的。
   
[Python库API清单](http://python.apichecklist.com/)  

构建不错的Python库API的有用清单。
   
[最小化英语中的负对数似然（Negative Log-Likelihood）](http://willwolf.io/2017/05/18/minimizing_the_negative_log_likelihood_in_english/)  
   
[PyCon 2017视频集](https://www.youtube.com/channel/UCrJhliKNQ8g0qoE_zvL8eVg/)  
   
[在PyPi上构建僵尸网络](https://hackernoon.com/building-a-botnet-on-pypi-be1ad280b8d6)  
   
[Jupyter Notebook：鲜为人知的技巧！](https://blog.3blades.io/jupyter-notebook-little-known-tricks-b0866a558017)  
   
[在TensorFlow中了解和实现CycleGAN](https://hardikbansal.github.io/CycleGANBlog/)  
   
[利用Python和Mxnet，基于深度学习的现代人脸检测](https://medium.com/wassa/modern-face-detection-based-on-deep-learning-using-python-and-mxnet-5e6377f22674)   
   
[通过Python和Google BigQuery，在纽约出租车数据上构建机器学习合成分类器，来预测无小费 vs 很多小费](https://medium.com/towards-data-science/building-a-ml-classifier-on-ny-city-taxi-data-to-predict-no-tips-vs-generous-tips-with-python-92e21d5d9fd0)   
   
  
# 书籍  
  
[利用Python学习数据挖掘 - 第二版 (Learning Data Mining with Python - Second Edition）](http://amzn.to/2qhiTQ4)   

本书教你如何使用各种数据集来设计和开发数据挖掘应用，从基本分类和关联分析开始。本书涵盖了大量Python中可用的库，包括Jupyter Notebook， pandas，scikit-learn和NLTK。
  
  
# 好玩的项目，工具和库  
  
[ParlAI](http://parl.ai/)  

一个在各种公开可用对话数据集上训练和评估AI模型的框架。
  
[darkflow](https://github.com/thtrieu/darkflow)  

将darknet转换成tensorflow。加载训练权重，使用tensorflow重新训练/微调，导出常数图到移动设备。  
  
[BoopSuite](https://github.com/M1ND-B3ND3R/BoopSuite)  

一个用于无线审计和安全测试的Python工具套件。
   
[Cyphon](https://github.com/dunbarcyber/cyphon)  

Cyphon通过单一平台简化所有相关流程，终结了数据管理传统上带来的令人头疼的东西。Cyphon从电子邮件、日志信息、社交媒体和其他在线资源接收、处理和分类数据。
   
[SemiLive](https://github.com/toji/semilive)  

用于“实时”编码的Sublime Text插件。
   
[scikit-garden](https://github.com/scikit-garden/scikit-garden)  

Scikit-Garden或者skgarden (发音为skarden) 是Scikit-Learn兼容的决策树和森林的花园。
   
[Reconnoitre](https://github.com/codingo/Reconnoitre)  

一个安全工具，用于多线程信息收集和服务枚举，同时构建目录结构以存储结果，以及写出进一步测试的建议。
   
[haishoku](https://github.com/LanceGin/haishoku)  

Haishoku是一个从图像中获取主色或者代表色的开发工具，依赖于Python3和Pillow。
   
[binsnitch](https://github.com/NVISO-BE/binsnitch)   

binsnitch可以用于检测你的系统上的文件的静默无意义的修改。它会递归扫描一个给定目录下的文件，并且基于文件的SHA256值，跟踪其检测到的东西的任意修改。你可以选择跟踪可执行文件，或者所有文件。
   
[tinynumpy](https://github.com/wadetb/tinynumpy)  

一个轻量级、纯Python的符合numpy的ndarray类。
  
  
# 最新发布  
  
[PyCharm 2017.1.3](https://blog.jetbrains.com/pycharm/2017/05/pycharm-2017-1-3-out-now)  
   
  
# 近期活动和网络研讨会  
  
[SoCal Python 2017年5月聚会 - Los Angeles, CA](https://www.meetup.com/socalpython/events/240033209/)    

将会有以下演讲

  * 用Python玩转戴尔生命周期控制器
  * 通过生成测试“调戏”测试

   
[DFW Pythoneers 2017年6月聚会 - Plano, TX](https://www.meetup.com/dfwpython/events/238130973/)  
   
  

 

