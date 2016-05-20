原文：[Python Weekly Issue 243](http://us2.campaign-archive1.com/?u=e2e180baf855ac797ef407fc7&id=d4ea510559&e=148158c7b4)

---

欢迎来到Python周刊第243期。本周，我想要感谢我们的赞助商，Intel的支持。一定要看看他们对高性能Python惊人的支持。

本周，我们有满满的干货。尽情享用吧！

# 来自赞助商

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/711a53fa-d9a3-4b1d-897c-853ccb078c96.png)](https://software.intel.com/en-us/intel-sdp-home)

想要在不需要自己构建的情况下活动高性能Python吗？加入[Intel® Distribution for Python* 2017 Beta](https://software.intel.com/en-us/python-distribution)，通过诸如Intel® Math Kernel库和增强线程体验NumPy/SciPy性能，方便的访问Numba, Cython, data analytics和conda。

# 文章，教程和讲座

[Episode #57: Intel中由内而外的Python性能](https://talkpython.fm/episodes/show/57/python-performance-from-the-inside-out-at-intel)

当你考虑你的软件性能时，还有什么比让代码在CPU自身执行更低水平了呢？我们中许多人学习，并试图了解如何在这个低层次最大化性能。但很少人有资格去定义在这个层次发生了什么。本周，你将见到David Stewart，Intel数据中心软件技术小组的经历。我们将谈谈Intel在开源和Python方面正在进行的各种工作。

[使用Tensorflow进行车牌识别](http://matthewearl.github.io/2016/05/06/cnn-anpr/)

本文表明，使用相当少的代码(~800行)，以及非常少的特定领域的知识，是有可能构建一个ANPR系统，而不需要导入任何特定域的库。

[你需要学习写Python装饰器的5个原因](https://www.oreilly.com/ideas/5-reasons-you-need-to-learn-to-write-python-decorators)

装饰器可以大大放大你所写的代码的积极影响。

[中文版](../Others/你需要学习编写Python装饰器的五大理由.md)

[Podcast.__init__ 第56集 - 跟Lazar和Zheng谈谈洋葱物联网(Onion IoT)](http://pythonpodcast.com/onion-iot.html)

技术上的最大趋势之一是物联网，而驱动力之一则是正不断推出的新的传感器和平台。在这一集中，我们与名为Onion的这样一个平台的创始人和首席工程师进行对话。Omega开发板是一个新的硬件平台，它运行OpenWRT，并且允许你使用不同的语言进行配置，其中最重要的就是Python。

[部署Django + Python 3 + PostgreSQL到AWS Elastic Beanstalk](https://realpython.com/blog/python/deploying-a-django-app-and-postgresql-to-aws-elastic-beanstalk/)

下面是一个完整的说明，展示了如何设置和部署一个基于Python 3和PostgreSQL搭建的Django应用，到亚马逊网络服务(AWS)上。

[在Python中使用pandas进行更简单的数据分析 (视频系列)](http://www.dataschool.io/easier-data-analysis-with-pandas/)

[通过实例学习List, Dict和Set推导](https://www.smallsurething.com/list-dict-and-set-comprehensions-by-example/)

[你认为Python中什么是比它本应该的更难？](https://www.reddit.com/r/Python/comments/4if7wj/what_do_you_think_is_more_difficult_in_python/)

[使用了Numba的相当快的word2vec](https://d10genes.github.io/blog/2016/05/03/word2vec/)

[分析Last.fm收听历史](http://geoffboeing.com/2016/05/analyzing-lastfm-history/)

[Django, ELB健康检查和持续交付](http://tech.octopus.energy/2016/05/05/django-elb-health-checks.html) 

[中文版](../Django/Django, ELB健康检查和持续交付.md)

[Python, Postgres, SQLAlchemy, 和PGA Tour Stats](https://bigishdata.com/2016/05/08/python-postgres-sqlalchemy-and-pga-tour-stats/)

# 本周的Python工作

[Timetric招聘Python开发者](http://jobs.pythonweekly.com/jobs/timetric-python-developer/)

我们生产跨大范围主题的聚焦数据内容，从建筑起重机到非接触式支付；从巨灾保险到铜矿开采，而你会了解一系列数量惊人，范围惊人的事情。我们正在寻找一个可以在我们的基于网络的数据发布平台和产品上工作的Python开发者。 

# 好玩的项目，工具和库

[Kel](http://www.kelproject.com/)

一个基于Python和Go的开源的，基于Kubernetes的PaaS

[Arcade](http://pythonhosted.org/arcade/) 

Arcade是一个容易学习的Python库，用于创建2D视频游戏。它非常适合那些正在学习编程的人，以及那些想要编写一个2D游戏但又不想学习复杂框架的程序员。

[DeepOSM](https://github.com/trailbehind/DeepOSM)

使用OpenStreetMap特性和卫星图像进行深入学习网络训练。

[DECO](https://github.com/alex-sherman/deco) 

Python的一个简化并行计算模型。DECO自动并行化Python程序，并且仅需要对现有的串行程序进行少量修改。

[Pyroute2](http://docs.pyroute2.org/general.html) 

Pyroute2是一个纯Python网络链路和Linux网络配置库。它只需要Python stdlib，不需要其他第三方库。

[uvloop](https://github.com/MagicStack/uvloop) 

uvloop是内建asyncio事件循环的一个快速的直接替代品。uvloop使用Cython实现的，使用libuv。

[pypub](https://github.com/wcember/pypub)

一个Python库，用来以编程的方式创建epub文件。

[Fierce](https://github.com/mschwager/fierce) 

Fierce是一个DNS侦查工具，用以定位非连续IP地址空间。

[deep-anpr](https://github.com/matthewearl/deep-anpr)

利用神经网络建立一个自动车牌识别系统。

[certbot](https://github.com/certbot/certbot)

Certbot，前身是Let's Encrypt Client，是用来获得Let's Encrypt证书的EFF工具，可以在服务器上（可选的）自动启用HTTPS。它还可以作为任何使用ACME协议的CA的客户端。

[sqlitebiter](https://github.com/thombashi/sqlitebiter)

sqlitebiter是一个CLI工具，用来从CSV/JSON/Excel/Google-Sheets创建一个SQLite数据库。

[FeatherDuster](https://github.com/nccgroup/featherduster) 

FeatherDuster是一个用于破解加密的工具，它试图让识别和利用弱密码系统尽可能的简单。Cryptanalib是FeatherDuster中的一个移动部件，并且可以独立于FeatherDuster使用。

[Toil](https://github.com/BD2KGenomics/toil) 

Toil是一个可扩展的，高效跨平台管道管理系统，完全用Python编写，并围绕函数式编程的原理设计。

[Growler](https://github.com/pyGrowler/Growler)

Growler是一个web框架，建立在原子asyncio，这个在PEP 3156中描述，并且添加到python 3.4中的标准库的异步库。它从nodejs生态中的Connect Express框架中获得灵感，使用一个单一的应用对象和一系列的中间价来处理HTTP请求。自定义的中间件链提供了一种简单的方式，以实现复制应用。

[pur](https://github.com/alanhamlett/pip-update-requirements)

在requirements.txt文件中更新包。

[deer](https://github.com/VinF/deer)

DeeR是一个用于Deep Reinforcement的Python库。它的构建考虑到了模块化，因此可以很容易地适应任何需要。它提供了许多可能性默认项（优先体验重播，双Q学习，等等）。还提供了许多不同的环境范例（它们中的一些使用OpenAI gym）。

[gitsome](https://github.com/donnemartin/gitsome)

一个增强的Git/Shell自动完成器，整合了GitHub。

# 最新发布

[MicroPython 1.8](https://github.com/micropython/micropython/releases/tag/v1.8)

此版本标志着MicroPython代码库中的官方ESP8266支持的第一个通用版本。ESP8266串口拥有许多改进和补充，其中包括：websocket和webrepl模块，深度睡眠模式，UART读取，增强I2C支持，增强网络配置，启动脚本的完整序列（内置_boot.py, boot.py和main.py），带有自动flash-size检测以及文档教程的改善的文件系统支持。

[Pandas 0.18.1](http://pandas.pydata.org/pandas-docs/version/0.18.1/whatsnew.html#v0-18-1-may-3-2016)

这是0.18.0的一个错误修复小版本，包含了大量的bug修复，以及一些新特性，增强功能，和性能改进。

# 近期活动和网络研讨会

[PyHou Meetup May 2016 - 休斯顿，德克萨斯](http://www.meetup.com/python-14/events/226999479/)
