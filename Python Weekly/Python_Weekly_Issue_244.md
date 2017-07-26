原文：[Python Weekly Issue 244](http://us2.campaign-archive1.com/?u=e2e180baf855ac797ef407fc7&id=26a54823c2&e=148158c7b4)

---

欢迎来到Python周刊第244期。本周，我们仍然有满满的干货。尽情享用吧！

# 来自赞助商

[![](https://gallery.mailchimp.com/72f68dcee17c92724bc7822fb/images/a7efe9e7-ad6c-40b1-88e4-aad1f91af194.png)](http://hrd.cm/1LcF7Hc)

花时间写代码，而不是寻找工作。让我们带给你机遇吧。在Hired Marketplace待一周，你将获得来自美国和英国的顶级技术公司的5份以上的介绍信。[今天就加入吧！](http://hrd.cm/1LcF7Hc)


# 新闻

[PEP 518 -- 为Python项目指定最低构建系统要求](https://www.python.org/dev/peps/pep-0518/)

此PEP指定了Python软件包应该如何指定它们的依赖，以执行它们所选择的构建系统。作为本规范的一部分，介绍了一个用于软件包的新的配置文件，用以指定它们的构建依赖（希望相同的配置文件将被用于未来配置的详细信息）。

[PEP 519 -- 新增一个文件系统路径协议](https://www.python.org/dev/peps/pep-0519/)

此PEP为类提出了一个协议，它表示一个文件系统路径能够提供一个str或者bytes表示。还提出了Python标准库的变化，用以利用这个协议，其中，适当地在那些历史上只接受str和/或者bytes文件系统路径的地方使用path对象。其目的在于加速用户向富path对象迁移，同时提供一个简单的方法来使用期望str或bytes的代码。
（Ele注：好吧，这个也乱七八糟的。等对PEP 519有一定的理解再修）

# 文章，教程和讲座

[如何在Python中构建一个SMS Slack Bot](https://www.twilio.com/blog/2016/05/build-sms-slack-bot-python.html)

Bot是Slack通道和外部应用直接一个超级有用的桥梁。让我们编写一个简单的Slack Bot，它是一个结合了Slack API和Twilio SMS API的Python应用，这样，用户就可以通过SMS发送和接收Slack信息了。

[用Python进行实用机器学习教程](https://pythonprogramming.net/machine-learning-tutorial-python-introduction/)

这门课程的目的是为你提供一个完整的机器学习的理解，涵盖理论，应用，以及监督，非监督和深度学习算法的内部工作。在这个系列中，我们将介绍线性回归，K最近邻，支持向量机（SVM），平聚类，层次聚类和神经网络。

[Matplotlib教程 - 绘制提到Trump, Clinton & Sanders的推特](https://www.dataquest.io/blog/matplotlib-tutorial/)

在这个matplotlib教程中，我们将介绍该库的基本知识，并看看如何进行一些中间可视化。我们将使用包含将近240,000条关于Hillary Clinton, Donald Trump, 和Bernie Sanders，目前所有美国总统候选人的推特的数据集。

[中文版](../Science%20and%20Data%20Analysis/Matplotlib教程%20-%20绘制提到Trump,%20Clinton%20&%20Sanders的推特.md)

[Episode #58: 使用并发，库和模式创建更好的Python程序](https://talkpython.fm/episodes/show/58/create-better-python-programs-with-concurrency-libraries-and-patterns)

本周，你将见到Mark Summerfield，他是许多Python书籍的多产作家。我们花时间钻研他的书 —— Python实践：使用并发，库和模式创建更好的Python程序(Python in Practice: Create Better Programs Using Concurrency, Libraries, and Patterns) —— 背后的思想。

[Django和GitLab - 用你的免费账户进行持续集成和测试](http://dezoito.github.io/2016/05/11/django-gitlab-continuous-integration-phantomjs.html)

本文试图解释如何设置你的Django项目，从而使你可以利用GitLab.com的免费持续集成功能 —— 对于它们的免费账户层，在它们的托管环境上可用，位于无限的私人仓库顶部！
（Ele注：这个翻译怪怪的。等到看完这篇文再进行调整）

[Podcast.__init__ 第57集 - 和Pierre Tardy聊聊Buildbot](http://pythonpodcast.com/pierre-tardy-buildbot.html)

作为技术专业人员，我们需要确保所写的软件可靠无bug的，而对此最好的方法是使用持续集成和持续部署管道。本周，我们和Pierre Tardy聊聊Buildbot，Buildbot是一个Python框架，用于构建和维护CI/CD工作流，从而使得我们的软件项目按计划推进。

[从Scratch (Software)构建一个深度学习机器](https://github.com/saiprashanths/dl-setup)

一个构建用于深度学习研究的机器的详细指南。包含安装驱动器、工具和多种深度学习框架的指令。

[Episode #59: SageMath - 开源准备好竞争了](https://talkpython.fm/episodes/show/59/sagemath-open-source-is-ready-to-compete-in-the-classroom)

这一集都是关于SageMath的，它是一个开源项目，为科学家和数学家提供了丰富的选项，由超过500个贡献者构建，并由超过50万行Python和Cython代码组成。

[Scrapy技巧：2016年五月版](https://blog.scrapinghub.com/2016/05/18/scrapy-tips-from-the-pros-may-2016-edition/)

该文显示了一些技巧和工具，用以帮助Scrapy用户调试他们的爬虫。

[Python 3: 对加密的介绍](http://www.blog.pythonlibrary.org/2016/05/18/python-3-an-intro-to-encryption/)

在Python 3的标准库中，并没有很多处理加密的库。相反，有散列库。我们将简单的介绍一下这些，但主要重点将放在以下第三方包：PyCrypto和cryptography。我们将学习如何使用这两个库来加密和解密字符串。

[车辆速度检测器](https://gregtinkers.wordpress.com/2016/03/25/car-speed-detector/)

[在Keras中构建自编码器](http://blog.keras.io/building-autoencoders-in-keras.html)

[NHL季后赛中的盖帽](http://blog.yhat.com/posts/hockey-shot-blocking.html)

[Swiss Python Summit 2016年视频](https://www.youtube.com/playlist?list=PL4_MBPz5hOsK1fflMqTEbOC9rPAsksG4A)

[你最喜欢的Python错误信息是神马？](https://www.reddit.com/r/Python/comments/4ivd2k/what_is_your_favorite_python_error_message/)

[在一个C应用程序中内嵌PyPy](http://codelle.com/blog/2016/5/embedding-pypy-in-a-c-application/)

# 本周的Python工作

[m.i.r. media诚聘Python Web开发者](http://jobs.pythonweekly.com/jobs/python-web-developer-django-framework-2/)

我们正在寻找Django开发者来支持我们在Cologne, Germany的开发团队。你将会为我们的本国和国际用户实现web应用程序和产品。

# 好玩的项目，工具和库

[Data Brewery](http://databrewery.org/)

Data Brewery是用于数据处理和分析的Python框架和工具的集合。

[Thorn](https://github.com/robinhood/thorn)

Thorn是一个Python的网络钩子框架，关注于灵活性和易用性，以及用于开始使用时和维护生产系统时。

[Jolla](https://github.com/salamer/jolla)

Jolla是一个纯API服务器框架，基于gevent。

[EmPyre](https://github.com/adaptivethreat/EmPyre)

EmPyre是一个纯Python的后开发代理，建立在密码学安全的通信和灵活的架构之上。它主要是基于Empire的控制器和通信结构。

[Ascii Py](https://github.com/ProfOak/ascii_py)

做一些ascii艺术品。

[CMDPlayer](https://github.com/Anil1331/CMDPlayer)

从带有搜索查询的命令行播放歌曲和视频。

[pygrid](https://github.com/mjs7231/pygrid)

PyGrid是一个小工具，它可以让你轻松地通过平铺，调整和定位，来组织打开的窗口，从而使你的桌面资源得到最佳利用。它很容易配置，并且支持多台显示器。

[PanAvatar](https://github.com/ondergetekende/python-panavatar)

为任何东西（不仅仅是用户）生成伪随机SVG替身。

[graphene-gae](https://github.com/ekampf/graphene-gae)

Google AppEngine的GraphQL支持。

[cppimport](https://github.com/tbenthompson/cppimport)

直接从Python导入C++文件！

[DataSciencePython](https://github.com/ujjwalkarn/DataSciencePython)

数据科学，自然语言处理和机器学习的Python教程清单。

[Buku](https://github.com/jarun/Buku)

强大的命令行书签管理器。你的迷你网站！

# 最新发布

[Python 3.6.0a1](https://www.python.org/downloads/release/python-360a1/)

[Twisted 16.2](http://labs.twistedmatrix.com/2016/05/twisted-162-released.html)

# 近期活动和网络研讨会

[SciPy 2016](http://scipy2016.scipy.org/ehome/146062/332936/)

年度SciPy会议汇集了来自业界、学术界和政府的超过650个参与者来一起展示他们最新的项目，从熟练的用户和开发者身上学习，以及代码开发商的协作。完整的计划将包括2天的教程（7月11日至12日），3天的座谈（7月13日至15日），以及2天的开发者短程赛（7月16日至17日）。早到优惠价将于5月22日结束。快来注册吧！

[大规模使用Python - Philadelphia­, PA](http://www.meetup.com/phillypug/events/230382483/)

在这次演讲中，AWeber首席技术Brian K. Jones讨论了平台和公司使用Python的规模的考虑，后果，以及一些沉浮。

[Python Web开发之夜 #26 - 明尼苏达州，明尼阿波利斯](http://www.meetup.com/PyMNtos-Twin-Cities-Python-User-Group/events/231141683/)

