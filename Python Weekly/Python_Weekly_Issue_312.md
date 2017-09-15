原文：[Python Weekly - Issue 312](http://eepurl.com/c3nJ1P)

---

欢迎来到Python周刊第312期。本周干货满满。尽情享用吧！
  
# 来自赞助商  
[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/b88ddabb-e48c-4fbd-af9a-fe48f8a98690.png)](https://goo.gl/wlxnDm)

Intel®数学核心库现在支持ActivePython啦！[参加我们的联合网络研讨会](https://activestateevents.webex.com/mw3200/mywebex/default.do?nomenu=true&siteurl=activestateevents&service=6&rnd=0.6219684855931373&main_url=https%3A%2F%2Factivestateevents.webex.com%2Fec3200%2Feventcenter%2Fevent%2FeventAction.do%3FtheAction%3Ddetail%26%26%26EMK%3D4832534b00000004c82b6efb2c0888d34b84a9ab89af4d840047070eb1faf5af88ec94b52582c0dc%26siteurl%3Dactivestateevents%26confViewID%3D70927602767921042%26SourceId%3Dintel%26encryptTicket%3DSDJTSwAAAAT6b0GOS5rtpAcqtNKE4mrwF-sZRgKdFYjL0f3y8NSzIw2%26&utm_source=14Sep2017%20ad%20Python%20weekly&utm_medium=email&utm_campaign=Python%20Weekly%20newsletter)，了解Intel性能库是如何加速ActivePython中的线性代数、FFT、算术和超越运算，以推动Python中更快的数值计算和机器学习。体验高性能Python吧！
  
  
# 新闻  
  
[PEP 562 —— 模块__getattr__](https://www.python.org/dev/peps/pep-0562/)

建议支持模块上定义的__getattr__函数，以提供模块属性访问的基本自定义。
  
[PEP 563 —— 推迟注释评估](https://www.python.org/dev/peps/pep-0563/)

PEP 3107引入了函数注释的语法，但是，故意让语义保持未定义。PEP 484为注释引入了标准的含义：类型提示（type hint）。PEP 526定义了变量注释，明确将它们与类型提示用例绑定。这个PEP提出了更改函数注释和变量注释，使其在函数定义时间不在进行评估。相反，它们以字符串的形式保存在__annotations__中。
  
[PyTexas 2017征求提议](https://www.papercall.io/pytexas-2017)  
  
[PyCon Pune 2018征求提议](https://pyconpune.talkfunnel.com/2018/)  
  
  
# 文章，教程和讲座  
  
[如何在Python中生成FiveThirtyEight图表](https://www.dataquest.io/blog/making-538-plots/)

如果你阅读数据科学文章，那么你或许已经刚好碰到FiveThirtyEight的内容了。当然，它们惊人的可视化给你留下了深深的印象。在这篇文章中，使用Python的matplotlib和pandas，我们会看到，复刻任意FiveThirtyEight (FTE)的可视化的核心部分是相当容易的。

[使用Clarifai图像识别和Python，通过MMS实现Not Hot Dog](https://www.youtube.com/watch?v=MTVS2T8sjFA)  

让我们看看是否可以使用Clarifai来重新创建HBO的硅谷（Silicon Valley）创造的著名的Not Hot Dog应用。我们将会使用Twilio MMS来接收图像，并且通过Clarifai的食物模型来进行热狗检测。
  
[使用Python中的NetworkX进行图优化简介](https://www.datacamp.com/community/tutorials/networkx-python-graph-tutorial)

通过本教程，你将解决图论中一个称为中国邮差问题的已建立问题。我们首先将浏览图的基本构建块（点、边、路径等等），并且使用Python中的NetworkX库，解决真实图形（国家公园的路径网络）上的这个问题。我们将会关注核心概念和实现。
  
[使用深度学习进行心脏病诊断](https://blog.insightdatascience.com/heart-disease-diagnosis-with-deep-learning-c2d92c27e730)

最先进的结果，参数减少了60倍。
  
[可扩展机器学习（第一部分）](https://tomaugspurger.github.io/scalable-ml-01.html)

Anaconda对扩展科学Python生态系统颇感兴趣。我当前的关注点在于核外、并行和分布式机器学习。本系列将会介绍这些概念，探索我们今天所提供的东西，并且跟踪社区的努力以突破边界。本文将会关注在可以装入内存的数据集上进行训练，但是必须在比内存还要大的数据集上进行预测的场景。
  
[使用Python、BlockExplorer和Webhose.io跟踪比特币](http://www.automatingosint.com/blog/2017/09/follow-the-bitcoin-with-python-blockexplorer-and-webhose-io/)

在这篇文章中，我们将会开发一个工具，通过它，我们可以针对特定的比特币地址，可视化它的流出流入交易流，然后使用Webhose.io执行中级的暗网搜索，来看看我们是否可以找到该比特币钱包提到过的隐藏服务。
  
[利用机器学习助力产品分类](https://techblog.commercetools.com/boosting-product-categorization-with-machine-learning-ad4dbd30b0e8)

为了改善产品分类的过程，我们从机器学习寻找帮助。我们的目标是开发一个机器学习系统，对于一个给定的产品，它可以预测该产品最适宜哪个目录，从而使得整个过程更容易、更快并且更少出错。在这篇文章中，我将会介绍一路上我们面对的问题，以及我们是怎样决定解决方法的。
  
[使用Python和Kali linux进行伦理黑客攻击](https://www.youtube.com/watch?v=Q6W-mZY4Wuo)  
  
[使用Python，对Trump的推特进行情感分析](https://dev.to/rodolfoferro/sentiment-analysis-on-trumpss-tweets-using-python-)  
  
[创建分布式Web爬虫的故事](https://benbernardblog.com/the-tale-of-creating-a-distributed-web-crawler/)  
  
[使用Django REST框架的简单嵌套API](http://blog.apptension.com/2017/09/13/simple-nested-api-using-django-rest-framework/)  
   
[多元化：玩转Django中的客户数据](https://www.vinta.com.br/blog/2017/multitenancy-juggling-customer-data-django/)  
  
[Django的完全初学者指南 —— 第二部分](https://simpleisbetterthancomplex.com/series/2017/09/11/a-complete-beginners-guide-to-django-part-2.html)  
  
[通过Python，自动化web分析](https://rrighart.github.io/GA/)  
  
[使用自然语言处理，识别诗歌的编写日期](https://medium.com/towards-data-science/identifying-when-poems-were-written-with-natural-language-processing-a40ff286bcd)   
  
  
# 书籍  
  
[Test-Driven Development with Python: Obey the Testing Goat: Using Django, Selenium, and JavaScript（测试驱动开发）](http://amzn.to/2jq5Opu)

通过带你从头到尾开发一个真实的web应用，本动手指南的第二版演示了使用Python进行测试驱动开发（TDD）的实际优势。你将会学习如何在构建应用的每个部分之前，编写和运行测试，然后开发开发通过这些测试所需的最少量代码。结果呢？就是可以用的干净代码啦。
  
  
# 本周的Python工作  
  
[Pole Star招聘高级软件工程师（Python）](http://jobs.pythonweekly.com/jobs/senior-software-engineer-python-2/)

在Pole Star，我们需要一个具有丰富Python商业经验的软件工程师，来构建和维护我们基于Python的核心平台和服务。你将会在一个人才济济的跨学科团队中工作（该团队具有强大的创新文化），并且帮助我们实现高影响力的作品。

[Bromium招聘高级Python开发者](http://jobs.pythonweekly.com/jobs/senior-python-developer-8/)

你将会加入到一个团队中，来设计、工程化、测试和大规模部署需要处理数百万客户端的系统，这些都是用于Bromium客户和我们的OEM合作伙伴。你将会让用户能够汇总配置和管理信息，以及查看和分析Bromium软件隔离的任何攻击。
  
  
# 好玩的项目，工具和库  
  
[Cryptowatch](https://github.com/alexanderepstein/cryptowatch)  

加密货币价格和账户余额监控。
  
[AllenNLP](http://allennlp.org/)  

一个开源NLP研究库，基于PyTorch。
  
[DeepMoji](https://github.com/bfelbo/DeepMoji)  

使用在emojis上预先训练的深度学习模型的最先进的情感分析。
  
[jupyter4kids](https://github.com/mikkokotila/jupyter4kids)  

帮助教授孩子编程原理、Python和数学的notebooks系列。
  
[TensorLy](https://github.com/tensorly/tensorly)   

TensorLy是一个用于tensor学习的快速简单的Python库。它构建于NumPy、SciPy和MXNet之上，允许快速直接的tensor分解、tensor学习和tensor代数。
  
[Monique Dashboards](https://github.com/monique-dashboards/monique) 

Monique Dashboards是一个用来创造面板和监控应用的创新Python库。它具有用来创建自定义面板的完整的功能性示例Web和HTTP API应用：Monique Web和Monique API。
  
[ONNX](https://github.com/onnx/onnx)  

开放神经网络交换 (ONNX)是迈向开放生态系统的第一步，它使得AI开发者可以随着其项目的演进，选择合适的工具。ONNX为AI模块提供了一个开源格式。它定义了一个可扩展的计算图模型，以及内置运算符和标准数据类型的定义。
  
[anaGo](https://github.com/hironsan/anago)   

anaGo是一个使用Keras的用于序列标记的最先进的库。anaGo可以为多种语言执行命名实体识别 (NER)，词性标记 (POS标记)，语义角色标记 (SRL)等等。
  
[TensorFlow Agents](https://github.com/tensorflow/agents)  

这个项目提供了用于强化学习的优化基础设施。它扩展了OpenAI gym接口道多个并行环境，并且允许在TensorFlow中实现代理，以及执行批量计算。
  
[SRU](https://github.com/taolei87/sru)   

SRU是一个周期性单元，可以比cuDNN LSTM快十倍，而且在许多任务中测试都没有精确度损失。
  
[django-gamification](https://github.com/mattjegan/django-gamification)  

Django Gamification旨在填补Django包生态的游戏空缺。在当前的状态下，Django Gamification提供了一组可用于实现应用中游戏化功能的模块。这些包含了用来跟踪包括徽章、点和可解锁的所有游戏化相关对象的集中接口。
  
[GulpIO](https://github.com/TwentyBN/GulpIO)  

给大容量深度学习数据的二进制存储格式。
  
  
# 最新发布  
  
[Sublime Text 3.0](https://www.sublimetext.com/blog/articles/sublime-text-3-point-0)
  
  
# 近期活动和网络研讨会  
  
[我暑假都做了啥 - Cambridge, MA](https://www.meetup.com/bostonpython/events/241561681/)

这个夏天，我开始了一个新的项目。我碰到了一些在其他项目中也会碰到的问题。这个演讲是对其中一些问题进行的漫漫探索。随着演讲的推进，我会解释Python设施，并且提到棘手的工程问题：什么是大O表示法，以及它会如何影响代码速度？Python的特殊方法是什么？如何选择数据表示？如何处理其他人的代码？如何管理不确定性？以及最重要的是：如何克服自己的恐惧和疑虑。
  
[LA Django 2017年九月聚会 - Los Angeles, CA](https://www.meetup.com/ladjango/events/242340191/)

将会有以下演讲：

  * Python和Django中更好的调试
  * Django中的干净架构

  
[PyHou 2017年九月聚会 - Houston, TX](https://www.meetup.com/python-14/events/242580962/)

本周的主题将会是pipenv。它是啥，如何使用它，以及一些额外的细节。
  

