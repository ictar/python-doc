原文：[Python Weekly - Issue 306](http://eepurl.com/cYjQj1)

---

欢迎来到Python周刊第306期。让我们直奔主题。
  
# 来自赞助商  
[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/45a64cc4-8d9a-460d-85d2-38c82f745d31.png)](https://www.datadoghq.com/dg/apm/ts-python-performance/?utm_source=Advertisement&utm_medium=Advertisement&utm_campaign=PythonWeekly-Tshirt&utm_content=Python)

[Python应用性能监控](https://www.datadoghq.com/dg/apm/ts-python-performance/?utm_source=Advertisement&utm_medium=Advertisement&utm_campaign=PythonWeekly-Tshirt&utm_content=Python)  

实时Python度量的图表和告警，并与来自你的栈中150多个其他技术的数据相关联。


# 文章，教程和讲座  
  
[一个可用于数百万图像文件的Keras多线程DataFrame生成器](https://techblog.appnexus.com/a-keras-multithreaded-dataframe-generator-for-millions-of-image-files-84d3027f6f43)

AppNexus的数据科学团队使用深度学习来处理各种图像分类任务。有许多资源可以让你快速运行深度学习模型，但是它们很少有提供大规模运行的样例。为了帮助填补这个空白，本文以及随附的代码演示：如何使用Keras中用于深度学习（分类或回归）的高效内存生成器，利用数百GB或者更多的磁盘空间来处理数百万图像文件，以及如何使用相同的生成器来有效实现一个合并模型。
  
[Python入口点解释](http://amir.rachum.com/blog/2017/07/28/python-entry-points/)  

在这篇文章中，我将要解释Python的入口点。大多数人都把入口点当成放在setup.py文件中以使得包可以在命令行被当成脚本使用的小小代码片段，但是它们的用途更广泛得多。我将要向你展示如何将入口点作为一个模块化插件架构使用。如果你想要让其他人编写在运行时与你现有的Python包交互或添加新功能的Python包，那么这是超级有用的。
  
[16行Python代码，网络抓取总统谎言](http://www.dataschool.io/python-web-scraping-of-president-trumps-lies/)  

这是关于Python中的网络爬取的介绍性较差。只需要基本了解Python语言即可。在本教程结束之后，你将能够使用requests和Beautiful Soup库，从一个静态网络页面抓取数据，并使用pandas库，来将数据导出到结构化文本文件中。
  
[利用OpenCV和Python，介绍计算机视觉](https://dzone.com/articles/introduction-to-computer-vision-with-opencv-and-py)  

只有利用AI的最新发展，才能使真正伟大的计算机视觉成为可能。以下是我们如何使用算法和计算机视觉来实现人物探测器的。
  
[揭秘动态编程](https://medium.freecodecamp.org/demystifying-dynamic-programming-3efafb8d4296)  

如何构建和编写动态编程算法。
  
[使用谷歌的卷积神经网络进行对象识别](https://medium.com/@williamkoehrsen/object-recognition-with-googles-convolutional-neural-networks-2fe65657ff90)

使用谷歌的预训练初始CNN模型来对图像进行分类。
  
[如何在一个Django项目中安装Amazon S3](https://simpleisbetterthancomplex.com/tutorial/2017/08/01/how-to-setup-amazon-s3-in-a-django-project.html)  

在本教程中，你将会学习如何使用Amazon S3服务来处理静态资源以及用户上传的文件，即媒体资源。

[Pandas的Grouper和Agg函数解释](http://pbpython.com/pandas-grouper-agg.html)

本文将介绍如何以及为什么你可能希望在你自己的数据上使用Grouper和agg函数。其中，我将介绍一些关于如何最有效地使用它们的技巧和提示。
  
[使用TensorFlow检测伪钞](https://medium.com/tensorist/detecting-fake-banknotes-using-tensorflow-be21ffd2c478)   

这篇文章展示了如何使用TensorFlow来构建一个检测伪钞的人工神经网络。
  
[利用深度学习，构建音乐推荐器](http://mattmurray.net/building-a-music-recommender-with-deep-learning/)  
  
[优化Pandas代码执行速度的初学者指南](https://engineering.upside.com/a-beginners-guide-to-optimizing-pandas-code-for-speed-c09ef2c6a4d6)  
  
[用Python，让山姆清理学城（GoT）10个小时](https://kazuar.github.io/got-remix/)  
  
[在Python中，使用Scrapy进行网络爬取（例子多多）](https://www.analyticsvidhya.com/blog/2017/07/web-scraping-in-python-using-scrapy/)  
  
  
# 书籍  
  
[Mastering Bitcoin: Programming the Open Blockchain（精通比特币：编程开放区块链）](http://amzn.to/2uShplT)

精通比特币是是带你走过看似复杂的比特币世界的指南，为你提供参与互联网金融所需要的知识。无论你是在建立下一个杀手级应用，投资初创公司，还是单纯地对技术感到好奇，此修订和扩展的第二版提供了让你开始的基本细节。
  
  
# 好玩的项目，工具和库  
  
[sandsifter](https://github.com/xoreaxeaxeax/sandsifter)  

sandsifter通过系统地生成机器码来搜索处理器的指令集，并监视异常执行情况，来审核x86处理器的隐藏指令和硬件错误。sandsifter已经发行了每个主要供应商的秘密处理器指令；反编译器、汇编器和仿真器中普遍存在的软件错误；企业管理程序的缺陷；以及x86芯片中的两性和安全关键硬件错误。
  
[pywonderland](https://github.com/neozhaoliang/pywonderland)  

使用Python，渲染数学中漂亮的图像或者生动有趣的算法。

[PlasmaPy](https://github.com/PlasmaPy/PlasmaPy)  

一个用于等离子体物理学的社区开发的Python包。
  
[CryptoTracker](https://github.com/EthVentures/CryptoTracker)  

一个用于跟踪和可视化主要交换机上的加密价格变动的完整开源系统。
  
[QuTiP](http://qutip.org/)   

QuTiP是用于模拟开放量子系统动力学的开源软件。QuTiP旨在为各种哈密尔顿算子提供用户友好的高效数值模拟，包括那些具有任意时间依赖性的数值模拟，通常在物理应用中广泛应用，如量子光学，俘获离子，超导电路和量子纳米机械谐振器。
  
[LabelImg](https://github.com/tzutalin/labelImg)  

LabelImg是一个图形图像注释工具，在图像上标记对象边界边框。
  
[P4wnP1](https://github.com/mame82/P4wnP1)   

P4wnP1是一个高度可定制的USB攻击平台，基于低成本的Raspberry Pi Zero或者Raspberry Pi Zero W。
  
[jupyter-notify](https://github.com/shoprunner/jupyter-notify)  

关于单元格完成的浏览器通知的Jupyter Notebook魔法。
  
[Photographic Image Synthesis](https://github.com/CQFIO/PhotographicImageSynthesis)  

这是用于从语义布局合成摄影图像的级联细化网络的Tensorflow实现。
  
[EAST](https://github.com/argman/EAST)  

EAST文本检测器的tensorflow实现。
  
[text_classification](https://github.com/brightmart/text_classification)  

各种文本分类模型，以及更多的深度学习。
   
[SimGAN-Captcha](https://github.com/rickyhan/SimGAN-Captcha)  

无需手动标识训练集的情况下解决验证码问题。
  
  
# 最新发布  
  
[Django问题修复版本：1.11.4](https://www.djangoproject.com/weblog/2017/aug/01/bugfix-release/)  
  
  
# 近期活动和网络研讨会  
  
[IndyPy 2017年8月每月聚会 - Indianapolis, IN](https://www.meetup.com/indypy/events/239881571/)  

将会有一个讲座，Coffeebot 3000 - 一个有关树莓派、Redis、LED和Mario的物联网故事。
  
[PyAtl 2017年8月聚会 - Atlanta, GA](https://www.meetup.com/python-atlanta/events/237560613/)  

将会有以下讲座：

  * Python工作市场总览
  * Dunder方法：系列，第一集： __init__
  
[Boulder Python 2017年8月聚会 - Boulder, CO](https://www.meetup.com/BoulderPython/events/239106655/)  
  
[Austin Python 2017年8月聚会 - Austin, TX](https://www.meetup.com/austinpython/events/239803300/)  
  


