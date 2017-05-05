原文：[Python Weekly - Issue 293](http://eepurl.com/cNw1mf)

---

欢迎来到Python Weekly第293期。本周干货满满。尽情享用！  
  
# 来自赞助商  

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/be2db49d-26ff-4973-8f45-7f5e5f877422.png)](https://software.intel.com/en-us/intel-distribution-for-python?utm_source=04May2017%20image%20ad%20Python%20weekly&utm_medium=email&utm_campaign=Python%20Weekly%20newsletter)

现在可以从YUM和APT Linux储存库上免费下载Python的Intel发行版及其所有优化包了。点击[这里](https://software.intel.com/en-us/articles/installing-intel-free-libs-and-python?utm_source=04May2017%20ad%20Python%20weekly&utm_medium=email&utm_campaign=Python%20Weekly%20newsletter)以获取详细指令。在Intel Python环境中运行你基于NumPy/SciPy/scikit-learn的Python代码，轻松获得更快的性能吧。
  
  
# 新闻  
  
[PyCon JP 2017征求提议](https://pycon.jp/2017/en/talks/cfp/)  
  
  
# 文章，教程和讲座  
  
[使用计算机视觉和深度学习创建现代OCR管道](https://blogs.dropbox.com/tech/2017/04/creating-a-modern-ocr-pipeline-using-computer-vision-and-deep-learning/) 

在这篇文章中，我们将会带你走入为我们的移动文件扫描仪构建的最先进的光学字符识别（OCR）管道的幕后。我们使用了计算机视觉和深度学习高级技术，例如双向长短期记忆 (Long Short Term Memory, LSTMs)，联结主义时间分类 (Connectionist Temporal Classification, CTC)，卷积神经网络 (CNNs)等等。此外，我们还将深入了解实际上让我们的OCR管道在Dropbox规模上可投入生产的因素。
  
[使用Python和SciKit Learn 0.18的神经网络初学者指南](https://www.springboard.com/blog/beginners-guide-neural-network-in-python-scikit-learn-0-18/)  

Python最流行的机器学习库是SciKit Learn。最新版本 (0.18) 现在已经支持神经网络模型了！在这篇文章中，我们将学习神经网络是如何工作的，以及如何使用Python编程语言和SciKit-Learn的最新版本来实现它们！
  
[构建自定义块模板标签](https://www.caktusgroup.com/blog/2017/05/01/building-custom-block-template-tag/)  

这些年来，在所提供的装饰器（它们在构建常见的简单的标签时做了大部分的工作）的帮助下，为django模板构建自定义标签变得越发容易了。但是，一个没有覆盖的区域就是块标签，即那些拥有一个开始和结束标签，其中的内容可能还需要模板引擎进行处理的标签。块标签几乎可以做任何事，这或许就是为什么不存在一个简单的装饰器来帮助编写它们的原因。在这篇文章中，我将要介绍如何构建一个示例块标签，它接受可以控制其逻辑的参数。
  
[Podcast.__init__ 第107集 - Scapy](https://www.podcastinit.com/episode-107-scapy-with-guillaume-valadon/)  

网络协议通常是不可预测的，但是如果你有一种有效的方式来尝试，那么它们会暴露出更大的威力。本周，Guillaume Valadon解释了可以如何使用Scapy来检查你的网络流浪，测试系统的安全性，以及开发全新的协议，所有这些都是用Python完成的！
  
[一个Django RESTful API的测试驱动开发](https://realpython.com/blog/python/test-driven-development-of-a-django-restful-api) 

这篇文章介绍了用Django和Django REST框架开发一个基于CRUD的RESTful API的过程，用于快速构建基于django模型的RESTful API。
  
[Episode #110: 用Redash进行数据民主化](https://talkpython.fm/episodes/show/110/data-democratization-with-redash) 

你曾经被要求过根据你公司的数据生成报告吗？有没有人建议暗示你购买/部署昂贵、闭源并且通常是平庸的大型BI软件？嗯，此时，Redash和Python可救你于水火之中。今天，你将会见到Arik Fraimovich，Redash的创造者，他的目标是通过连接到任意数据来源，轻松实现数据可视化，从而驱动你的公司数据。
  
[企业局域网中的Jupyter和Python](https://medium.com/@olivier.borderies/jupyter-python-in-the-corporate-lan-109e2ffde897)  

我们 (欧元区的一家投资银行) 正在在企业环境中部署Jupyter和Python科学栈，从而向员工和承包商提供交互式计算环境，以帮助他们利用他们的数据，自动化流程，并且更一般地，促进合作和想法、服务以及代码分享。我们提供了一些配置样例和示例Notebook。
  
[用于Mac的Google Home](https://newmediaeurope.com/google-home-mac/)  

Google近期宣布，他们向开发者开放Google Assistant SDK。现在，你可以在你的Mac上使用Google Home助手了。继续阅读，了解如何做到！
  
[pygit：能够创建一个存储库、提交、并推送自己到GitHub的刚刚够用的Git客户端](http://benhoyt.com/writings/pygit/) 

最近，我写了大约500行Python代码，它们实现了刚刚够用的一个Git客户端，它可以创建一个存储库、添加文件到索引中、提交、以及推送到GitHub。本文提供了一些背景，并介绍了代码。
  
[Celery任务检查表](http://celerytaskschecklist.com/)  

构建棒棒哒的Celery异步任务的检查表。
  
[使用Faker，为Python单元测试创建假数据](https://semaphoreci.com/community/tutorials/generating-fake-data-for-python-unit-tests-with-faker)  

学习如何使用Faker库，在你的Python单元测试中生成假数据。
  
[关于Django中的预取(prefetching)，你所需要知道的一切](https://hackernoon.com/all-you-need-to-know-about-prefetching-in-django-f9068ebe1e60)  
  
[设计一个异步API，从sans-I/O开始](https://snarky.ca/designing-an-async-api-from-sans-i-o-on-up/)  
  
[如何创建你的第一个Python 3.6 AWS Lambda函数](https://www.fullstackpython.com/blog/aws-lambda-python-3-6.html)  
  
[如何在Python中执行常见的Excel和SQL任务](http://code-love.com/2017/04/30/excel-sql-python/)  
  
[使用word2vec和keras的推特情绪分析](http://ahmedbesbes.com/sentiment-analysis-on-twitter-using-word2vec-and-keras.html)  
  
[如何使用Keras和Tensorflow中的传递学习和微调，来构建构建一个图像识别系统，并分类（几乎）任何对象](https://deeplearningsandbox.com/how-to-use-transfer-learning-and-fine-tuning-in-keras-and-tensorflow-to-build-an-image-recognition-94b0b02444f2)  
  
[使用单一职责原理（Single Responsibility Principle）重构Python代码库](https://medium.com/unbabel-dev/refactoring-a-python-codebase-using-the-single-responsibility-principle-ed1367baefd6)  
  
[利用流行建模来改善Facebook上的广告点击率](https://medium.com/towards-data-science/utilising-epidemic-modelling-to-improve-advertising-click-rates-on-facebook-6f294205a43f)  
  
  
# 本周的Python工作  
  
[Vote.org招聘软件工程师](http://jobs.pythonweekly.com/jobs/software-engineer-18/)   

Vote.org正在寻找一名软件工程师，帮助永久增加美国的投票率。如果你既有才华又务实；如果你喜爱干净并且文档良好的代码；如果你想要编写解决实际的现实世界问题的软件；并且如果你想要对美国选举产生直接持久的影响，那么，这或许是一份适合你的工作。
  
  
# 好玩的项目，工具和库  
  
[Automatic-Speech-Recognition](https://github.com/zzw922cn/Automatic_Speech_Recognition)  

在TensorFlow中实现的端到端自动化语音识别系统。
  
[Gixy](https://github.com/yandex/gixy)   

Gixy是一个分析nginx配置的工具。Gixy的主要目标是防止错误配置和自动化缺陷检测。
  
[Mocktails Mixer](https://github.com/Deeplocal/mocktailsmixer)  

做一个由Google Assistant SDK提供动力的DIY机器人Mocktails混音器。
  
[octodns](https://github.com/github/octodns)  

在架构即代码（Infrastructure as Code）的脉络中，OctoDNS提供了一组工具和模式，使得管理跨多个提供商的DNS记录变得简单。结果配置可以存储在存储库中，并且就像你的其他代码一样被部署，保持干净的历史记录并且使用你已有的评审工作流程。
  
[SeqBox](https://github.com/MarcoPon/SeqBox)  

一个即使在完全丢失了文件系统结构后，也能重新构建的单个文件容器/归档。
  
[py-backwards](https://github.com/nvbn/py-backwards)  

Python到python编译器，允许你在较老的版本中使用Python 3.6特性。
  
[Perceptron](https://github.com/casparwylie/Perceptron)  

一个用来分析性能并优化最佳模型的灵活的人工神经网络构建器。
  
[Flaskex](https://github.com/anfederico/Flaskex)  

用于快速原型和小型应用的简单flask示例。
  
[scrape](https://github.com/huntrar/scrape)   

一个命令行网络爬虫工具。
  
[Science Flask](https://github.com/danielhomola/science_flask)  

Science Flask是一个用于在线科学研究工具的web应用模板，使用Python Flask，JavaScript和Bootstrap CSS构建。
  
[ActualVim](https://github.com/lunixbochs/ActualVim)  

使用Neovim的Sublime Text 3输入模式。
  
[subconscious](https://github.com/paxos-bankchain/subconscious)  

用于python3的支持Redis (内存中) 的db，兼容asyncio。
  
[django-notifyAll](https://github.com/inforian/django-notifyAll)  

一个可以用于所有类型的通知（如短信、邮件、推送）的库。
  
[clif](https://github.com/google/clif)  

用于使用LLVM，为Python和其他语言封装C++的封装器生成器基础。
  
  
# 近期活动和网络研讨会  
  
[PyCon CZ 2017](https://cz.pycon.org/2017/)  

PyCon CZ旨在成为捷克共和国的Python社区最大的年度聚会。它专注于尊重和支持优秀人才在捷克以及周边欧盟成员国，用Python进行教学、学习和创新。
  
[Boulder Python 2017年5月聚会 - Boulder, CO](https://www.meetup.com/BoulderPython/events/239106610/)  
  
[Austin Python 2017年5月聚会 - Austin, TX](https://www.meetup.com/austinpython/events/234490249/)  
  
[Edmonton Python 2017年5月聚会 - Edmonton, AB](https://www.meetup.com/startupedmonton/events/238378393/)   
  

 

