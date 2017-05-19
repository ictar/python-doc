原文：[Python Weekly Issue 245](http://us2.campaign-archive2.com/?u=e2e180baf855ac797ef407fc7&id=bb4672538f&e=148158c7b4)

---

欢迎来到Python周刊第245期。让我们直接看看本周有啥吧。

# 来自赞助商

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/711a53fa-d9a3-4b1d-897c-853ccb078c96.png)](https://software.intel.com/en-us/intel-sdp-home)

Intel是PyCon 2016的荣誉赞助商！参观 #533 展位来学习（并赢得酷炫奖品）Intel如何通过代码贡献，带[Python加上原生代码分析](https://software.intel.com/en-us/python-profiling)和[Python发行](https://software.intel.com/en-us/python-distribution)的性能解决方案, PyPy加速和数据分析平台，来助力Python社区。


# 新闻

[PyCon Ireland征集建议](https://python.ie/pycon-2016/call-proposals/)

PyConIE 2016的建议征集现已开放。格式将由两个座谈轨迹和两个研讨会轨迹组成。请在2016年7月18日之前提交建议。


# 文章，教程和讲座

[使用BigQuery和TensorFlow进行需求预测](http://nbviewer.jupyter.org/github/GoogleCloudPlatform/training-data-analyst/blob/blog_20160513/CPB100/lab4a/demandforecast.ipynb)

在这个notebook上，我们将开发一个机器学习模型来预测纽约的出租车需求。

[中文版](../Science and Data Analysis/使用BigQuery和TensorFlow进行需求预测.md)

[Episode #60：使用Ufora将Python缩放到1000个内核上](https://talkpython.fm/episodes/show/60/scaling-python-to-1000-s-of-cores-with-ufora)

之前，在这个节目上，你听过我谈论到关于缩放Python和Python性能。但在这一集，我将带给你一个非常有趣的项目，对于某一类的应用，它将提高Python性能的上限。你将见到来自Ufora的Braxton McKee。他们开发了一个全新的Python运行时环境，该运行时环境关注于跨1000个CPU内核和甚至GPU，来水平扩展Python应用。他们将其称之为“为数据科学编译，自动并行的python”。

[Python 101：用基准问题测试代码的一个简介](http://www.blog.pythonlibrary.org/2016/05/24/python-101-an-intro-to-benchmarking-your-code/)

用基准问题测试代码意味着什么？基准或分析背后的主要思想是弄清你的代码执行得有多快，以及瓶颈在哪里。做这种事的最主要的原因是为了优化。你将遇到一些情况，在这些情况下需要你的代码运行得更快，因为你的业务需求已经改变了。当这种情况发生时，你将需要弄清楚代码中的哪些部分让该过程变慢。本章将只讨论如果使用各种工具来分析代码。它不会涉及到具体代码优化。让我们开始吧！

[使用Python和Flask入门Slack API](https://realpython.com/blog/python/getting-started-with-the-slack-api-using-python-and-flask)

在这篇文章中，我们将看到如何通过API和官方的SlackClient Python辅助库来使用Slack。我们将抓取一个API访问token，并写一些Python代码到列表中，通过该API检索和发送数据。让我们开始挖掘吧！

[Podcast.__init__ 第58集 - 和Tom Dyson谈谈Wagtail](http://pythonpodcast.com/tom-dyson-wagtail.html)

如果你正在操作一个网站，该网站需要发布和管理定期的内容，那么一个CMS（内容管理系统）将成为减少你的工作量显而易见的选择。有大量可用的选项，但是如果你正在寻找一个利用Python的力量并具有灵活性的解决方案，那么你应该认真考虑下Wagtail。在这一集中，Tom Dyson解释了Wagtail是如何被创建的，它与其他选择有何不同之处，以及何时你应该为你的项目实现它。

[抛弃Python 2](https://asmeurer.github.io/blog/posts/moving-away-from-python-2/) 

[逆向工程我的酒店中的一个神秘的UDP流](http://wiki.gkbrk.com/Hotel_Music.html)

[Raspberry Pi LCD设置以及在Python中编程](https://www.youtube.com/watch?v=zC3i3CbKZfw)

[条件Python依赖](https://hynek.me/articles/conditional-python-dependencies/) 

[使用Python进行网页抓取 —— 抓取Comixology的数字漫画信息](http://felipegalvao.com.br/blog/2016/05/24/web-scraping-with-python-scraping-digital-comics-information-from-comixology/)


# 好玩的项目，工具和库

[Mycroft Core](https://github.com/MycroftAI/mycroft-core)

Mycroft Core是组成Mycroft人工智能平台的主要模块。Mycroft利用Adapt Intent Parser, Speech-to-Text软件和Text-to-Speech。该平台背后的思想在于，能够在任何设备上启用语音，并且将其变成一个智能个人助手，能够执行一些任务。

[OpenWPM](https://github.com/citp/OpenWPM)

OpenWPM是一个网络隐私测量框架，它易于收集规模从数千到以百万计的网站上的数据，以进行隐私研究。OpenWPM 建立在Firefox顶层，使用Selenium提供的自动化。它包括一些用于数据收集钩子，包括一个代理，一个Firefox扩展，以及对Flash cookie的访问。

[http-prompt](https://github.com/eliangcs/http-prompt)

HTTP Prompt是一个交互式命令行HTTP客户端，具有自动完成和语法高亮的功能，建立在HTTPie和prompt_toolkit之上。

[aima-python](https://github.com/aimacode/aima-python)

Russell和Norvig的 "Artificial Intelligence - A Modern Approach"中的算法的Python实现。

[stack](https://github.com/RyanKung/stack) 

stack是stack的一个Python版本，它是一个用于开发Python项目的跨平台的程序。

[textX](https://github.com/igordejanovic/textX) 

textX是一个元语言，用于在Python中构建领域特定语言(Domain-Specific Languages (DSLs))。简而言之，textX将帮助你以一种简单的方式构建你自己的文本语言。你可以发明你自己的语言，或者构建对已存在的文本语言或者文件格式的支持。

[tesserocr](https://github.com/sirfz/tesserocr)

一个用于光学字符识别(Optical Character Recognition (OCR))的tesseract-ocr API的简单的，Pillow友好型装饰器。

[flask-ask](https://github.com/johnwheeler/flask-ask)

容易用Python, Flask, 以及Alexa技能套件来写Amazon Echo应用。

[DQN-tensorflow](https://github.com/devsisters/DQN-tensorflow)

通过深度强化学习的人类水平控制的Tensorflow实现。

[micropython-redis](https://github.com/dwighthubbard/micropython-redis)

一个redis客户端实现，设计于使用micropython。

[say_what](https://github.com/joshnewlan/say_what)

在诈骗电话中，使用语音到文本，来全面检测。

[mlbgame](https://github.com/zachpanz88/mlbgame)

一个检索和读取MLB GameDay XML数据的Python API。

[Meson](https://github.com/mesonbuild/meson/)

Meson是一个跨平台的构建系统，设计为尽可能的快速和用户友好。它支持许多语言和编译器，包括GCC, Clang和Visual Studio。其生成定义是以一种简单的非图灵完备的DSL编写的。

[RhodeCode](https://rhodecode.com/download/community) 

分布式存储库的集中控制。Mercurial, Git, 和Subversion的统一使用工具。

[GooPyCharts](https://github.com/Dfenestrator/GooPyCharts)

Python的谷歌图表API。这意味着用于替代matplotlib。

[callbot](https://github.com/makaimc/callbot)

使用Twilio打电话的Slack机器人。


# 最新发布

[Scrapy 1.1](https://blog.scrapinghub.com/2016/05/25/data-extraction-with-scrapy-and-python-3/)
 
带有对Python 3支持的Scrapy 1.1正式发布了！Python 3支持并不是该版本的唯一一个好消息。它还有其他一些功能和改进。

[Django 1.10 alpha 1](https://www.djangoproject.com/weblog/2016/may/20/django-110-alpha-1-released/)


# 近期活动和网络研讨会

[PyData Paris 2016](http://pydata.org/paris2016/)

PyData会议是Python中数据分析工具的用户和开发者的聚会。其目标是提供Python爱好者一个平台来分享想法和彼此学习如何最好地应用该语言和工具，以应对数据管理、处理、分析和可视化的广泛领域中不断发展的挑战。

[网络研讨会：介绍SciPy生态系统](http://www.oreilly.com/pub/e/3714)

Python拥有一个庞大而活跃的科学编程社区，并且一直都在开发额外的工具。面对这个新世界，可能会让你感到困惑。加入Ben Root吧，他提供了SciPy生态系统的一个高层次的概述，并着重介绍了一些他最喜欢的工具，让你入门SciPy。