原文：[Python Weekly Issue 243](http://us2.campaign-archive1.com/?u=e2e180baf855ac797ef407fc7&id=d4ea510559&e=148158c7b4)

---

欢迎来到Python周刊第243期。本周，我想要感谢我们的赞助商，Intel的支持。一定要看看他们对高性能Python惊人的支持。

本周，我们有满满的干货。尽情享用吧！

# 来自赞助商

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/711a53fa-d9a3-4b1d-897c-853ccb078c96.png)](https://software.intel.com/en-us/intel-sdp-home)

想要在不需要自己构建的情况下活动高性能Python吗？Join the [ Intel® Distribution for Python* 2017](https://software.intel.com/en-us/python-distribution) Beta and experience NumPy/SciPy performance through native libraries like Intel® Math Kernel Library and enhanced threading, easy access to Numba, Cython, data analytics, and conda.

# 文章，教程和讲座

[Episode #57: Python performance from the inside-out at Intel](https://talkpython.fm/episodes/show/57/python-performance-from-the-inside-out-at-intel)

When you think about the performance of your software, there is nothing more low level and fundamental than how your code executes on the CPU itself. Many of us study and try to understand how to maximize performance at this low level. But few are in a position to define what happens at this level. This week you'll meet David Stewart, manager in the Intel Data Center Software Technology group at Intel. We'll discuss the wide variety of work Intel is doing in open source and Python.

[Number plate recognition with Tensorflow](http://matthewearl.github.io/2016/05/06/cnn-anpr/)

本文表明，使用相当少的代码(~800行)，是有可能构建一个ANPR系统，而不需要导入任何特定域的库，and with very little domain-specific knowledge.

[你需要学习写Python装饰器的5个原因](https://www.oreilly.com/ideas/5-reasons-you-need-to-learn-to-write-python-decorators)

装饰器可以大大放大你所写的代码的积极影响。

[Podcast.__init__ 第56集 - 跟Lazar和Zheng谈谈洋葱物联网(Onion IoT)](http://pythonpodcast.com/onion-iot.html)

One of the biggest new trends in technology is the Internet of Things and one of the driving forces is the wealth of new sensors and platforms that are being continually introduced. In this episode we spoke with the founder and head engineer of one such platform named Onion. The Omega board is a new hardware platform that runs OpenWRT and lets you configure it using a number of languages, not least of which is Python.

[部署Django + Python 3 + PostgreSQL到AWS Elastic Beanstalk](https://realpython.com/blog/python/deploying-a-django-app-and-postgresql-to-aws-elastic-beanstalk/)

The following is a soup to nuts walkthrough of how to set up and deploy a Django application, powered by Python 3, and PostgreSQL to Amazon Web Services (AWS) all while remaining sane

[在Python中使用pandas进行更简单的数据分析 (视频系列)](http://www.dataschool.io/easier-data-analysis-with-pandas/)

[通过实例学习List, Dict和Set推导](https://www.smallsurething.com/list-dict-and-set-comprehensions-by-example/)

[你认为Python中什么是比它本应该的更难？](https://www.reddit.com/r/Python/comments/4if7wj/what_do_you_think_is_more_difficult_in_python/)

[Pretty fast word2vec with Numba](https://d10genes.github.io/blog/2016/05/03/word2vec/)

[分析Last.fm收听历史](http://geoffboeing.com/2016/05/analyzing-lastfm-history/)

[Django, ELB健康检查和持续交付](http://tech.octopus.energy/2016/05/05/django-elb-health-checks.html)

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

FeatherDuster is a tool  for breaking crypto which tries to make the process of identifying and exploiting weak cryptosystems as easy as possible. Cryptanalib is the moving parts behind FeatherDuster, and can be used independently of FeatherDuster

[Toil](https://github.com/BD2KGenomics/toil) 

Toil是一个可扩展的，高效跨平台管道管理系统，完全用Python编写，并围绕函数式编程的原理设计。

[Growler](https://github.com/pyGrowler/Growler)

Growler is a web framework built atom asyncio, the asynchronous library described in PEP 3156 and added to the standard library in python 3.4. It takes a cue from the Connect &amp; Express frameworks in the nodejs ecosystem, using a single application object and series of middleware to process HTTP requests. The custom chain of middleware provides an easy way to implement complex applications.

[pur](https://github.com/alanhamlett/pip-update-requirements)

在requirements.txt文件中更新包。

[deer](https://github.com/VinF/deer)

DeeR is a python library for Deep Reinforcement. It is build with modularity in mind so that it can easily be adapted to any need. It provides many possibilities out of the box (prioritized experience replay, double Q-learning, etc). Many different environment examples are also provided (some of them using OpenAI gym).

[gitsome](https://github.com/donnemartin/gitsome)

一个增强的Git/Shell自动完成器，整合了GitHub。

# 最新发布

[MicroPython 1.8](https://github.com/micropython/micropython/releases/tag/v1.8)

This release marks the first general release of official ESP8266 support within the MicroPython code base. The ESP8266 port has many improvements and additions, including: websocket and webrepl modules, deep-sleep mode, reading on UART, enhanced I2C support, enhanced network configuration, full sequence of start-up scripts (built-in _boot.py, boot.py and main.py), improved filesystem support with automatic flash-size detection as well as documentation and a tutorial.

[Pandas 0.18.1](http://pandas.pydata.org/pandas-docs/version/0.18.1/whatsnew.html#v0-18-1-may-3-2016)

This is a minor bug-fix release from 这是0.18.0的一个错误修复小版本，包含了大量的bug修复，以及一些新特性，增强功能，和性能改进。

# 近期活动和网络研讨会

[PyHou Meetup May 2016 - Houston, TX](http://www.meetup.com/python-14/events/226999479/)
