原文：[Python Weekly Issue 246](http://us2.campaign-archive1.com/?u=e2e180baf855ac797ef407fc7&id=073427826c&e=148158c7b4)

---

欢迎来到Python周刊第243期。让我们直奔主题。

# 来自赞助商

[![](https://gallery.mailchimp.com/72f68dcee17c92724bc7822fb/images/a7efe9e7-ad6c-40b1-88e4-aad1f91af194.png)](http://hrd.cm/1OcLAGQ)

**Python**开发者有需求市场，所以不应该是公司像你申请吗？在Hired，这就是它如何运作的。仅需一次申请，就可获得来自像Uber, Square, 和Facebook这样的公司的5+个工作offer。[今天就加入Hired吧](http://hrd.cm/1OcLAGQ)，一旦你获得一份工作，就可以得到**$1,000红利**！


# 文章，教程和讲座

[Podcast.__init__ 第59集 - 和Alex Clark谈谈Pillow](http://pythonpodcast.com/alex-clark-pillow.html)

如果你需要处理图像，那么Pillow就是你要使用的库。Python Image Libary (PIL)很长时间都是在Python中调整，分析和处理图像的金本位制。Pillow是一个现代的fork，它将PIL带向未来，从而我们都能够继续用它向前推进。本周，我们和Alex Clark聊聊是什么促使他第一次fork该项目，以及他维护它的经验，包括迁移到Python 3.

[SortedContainers大规模下的性能](http://www.grantjenks.com/docs/sortedcontainers/performance-scale.html)

SortedContainers在大规模上运行得相当好。本文在理论和实践层面讨论了原因。SortedContainers的设计允许测试数百亿项和基准，并在这个规模上展示结果。本文的结果将会难以使用其他实现进行复制。

[PyCon 2016视频](https://www.youtube.com/channel/UCwTD5zJbsQGJN75MwbykYNw/)

[深层强化学习：利用Pixels进行乒乓游戏](http://karpathy.github.io/2016/05/31/rl/)

[弦事件的时间地图](https://github.com/bzamecnik/ml-playground/blob/master/beatles/time_map_of_chord_events.ipynb)

[使用Pandas, Docker和OS(R)M，猜测神秘的旅游地点](http://nbviewer.jupyter.org/gist/mhermans/8c32eea0d5ec29e6b4329acbe7f0d3de)

[中文版](../Science and Data Analysis/使用Pandas, Docker和OS(R)M来猜测神秘的旅行地.md)

[带django教程的Facebook聊天机器人，又名笑话机器人](https://codeexperiments.quora.com/Facebook-chat-bot-aka-joke-bot-with-django-tutorial) 

[中文版](../Django/带django教程的Facebook聊天机器人，又名笑话机器人.md)

[使用人工智能来评估手写数学公式](http://www.willforfang.com/computer-vision/2016/4/9/artificial-intelligence-for-handwritten-mathematical-expression-evaluation)

[使用Python的动力系统：Wilson和Cowan模型](http://martinosorb.github.io/blog/2016/05/26/wilsoncowan.html)

[在Django Admin中的漂亮JSON格式化](http://www.pydanny.com/pretty-formatting-json-django-admin.html)


# 本周的Python工作

[RTI招聘高级Python开发者，国际](http://jobs.pythonweekly.com/jobs/sr-python-developer-4/)

我们正在寻找那些具有高级Python开发技能的人，但更重要的是，对于那些将成为RTI软件开发领导的人，将会兴奋于投入复杂的项目，帮助开发有用的库以及资源（例如ETL和分析管道），以及执行其他活动，例如教导这里的其他人如何用Python进行工作。

[Criteo招聘软件工程师（Python）](http://jobs.pythonweekly.com/jobs/software-engineer-python-2/)

作为Criteo的开发者，你将努力提高我们服务的可靠性和性能。我们的目标是尽可能的自动化数据中心 —— 思考定义软件的数据中心！我们正在寻找有才华的Python开发者，但此外，我们也在寻找那些可以快速适应，并具有开发思想的人。我们的工程师选择并使用适用于工作的最佳工具。 


# 好玩的项目，工具和库

[bashplotlib](https://github.com/glamp/bashplotlib)

bashplotlib是一个python包和命令行工具，用于在终端绘制基础图。这是一个快速的方式，用以在没有GUI的情况下可视化数据。它是用纯Python编写的，并且可以使用pip快速地安装。

[sumy](https://github.com/miso-belica/sumy)

一个简单的库和命令行实用程序，用以从HTML页面或者明文中抽取摘要。该包也包含用于文本摘要的简单评价框架。

[PyThalesians](https://github.com/thalesians/pythalesians)

Python开源财务库，用以回测交易策略，绘制图表，无缝下载市场数据，分析市场格局等等！

[PyMessager](https://github.com/enginebai/PyMessager)

一个Python API和教程，使用Python Flask来开发Facebook消息平台。

[fibratus](https://github.com/rabbitstack/fibratus)

Fibratus是一个工具，它可以捕捉大部分的Windows内核活动 —— 进程/线程创建和终止，文件系统I/O，注册表，网络活动，DLL加载/卸载等等。Fibratus具有一个非常简单的CLI，它封装机器，以启动内核事件流收集器，设置内核事件过滤器或者运行轻量的Python模块（名为filaments）。你可以使用filaments，用你自己的工具库来扩展Fibratus。

[docker-ida](https://github.com/intezer/docker-ida)

在Docker容器中运行IDA Pro反汇编，以自动化、缩放和分发IDAPython脚本的使用。

[WarBerryPi](https://github.com/secgroundzero/warberry)

WarBerry的构建只有一个目的；在红队组交战中使用（Ele注：原文是to be used in red teaming engagement，请随意纠正），其中，我们想要在一个很短的时间内活动尽可能隐蔽地获得尽可能的信息。只需找到一个网络端口并且将其插入即可。该脚本是以这种方式进行设计的：该方法针对避免网络中的噪声，可能导致检测并尽可能的有效。WarBerry脚本是将扫描工具放在一起的集合，从而提供此功能。

[RSPET](https://github.com/panagiks/RSPET)

RSPET (Reverse Shell and Post Exploitation Tool)是一个基于Python的反向外壳，它配备了可以在一个实施漏洞利用场景中辅助使用的功能。

[digit-classifier](https://github.com/karandesai-96/digit-classifier)

使用MNIST数据库的一个简单的手写数字分类器。在Python中，通过人工神经网络来实现。

[sklearn-evaluation](https://github.com/edublancas/sklearn-evaluation)

评估scikit学习模型的炫酷方式：图，表和markdown报告。

[TensorFlow-Examples](https://github.com/aymericdamien/TensorFlow-Examples)

带流行的机器学习算法实现的TensorFlow教程。本教程专为通过例子以易于深入TensorFlow而设计。

[Magenta](https://github.com/tensorflow/magenta)

使用机器智能生成音乐和艺术

[Leather](https://github.com/onyxfish/leather) 

Leather是为那些现在需要图表并且不在乎它们是否完美的人提供的Python图表库。

[swim](https://github.com/kylef/swim)

用于Swift语言的简单构建系统。


# 最新发布

[Flask 0.11](https://www.palletsprojects.com/blog/flask-011-released/) 

经过一段长长的等待，最后，Flask终于发布新版本了。这个版本的亮点是改善的开发体验，也就是说现在，浏览器挂了会重新加载，而不是返回一个“连接重置”的页面，并且支持新的命令行。

[PyPy3.3 v5.2 alpha 1](http://morepypy.blogspot.in/2016/05/pypy33-v52-alpha-1-released.html)

[Pyston 0.5](https://blog.pyston.org/2016/05/25/pyston-0-5-released/)


# 近期活动和网络研讨会

[Austin Python Meetup June 2016 - Austin, TX](http://www.meetup.com/austinpython/events/229461674/)

[PyAtl Meetup June 2016 - Atlanta, GA](http://www.meetup.com/python-atlanta/events/228588708/)
