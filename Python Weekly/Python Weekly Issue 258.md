原文：[Python Weekly Issue 258](http://us2.campaign-archive1.com/?u=e2e180baf855ac797ef407fc7&id=dadedf0a62&e=148158c7b4)

---

欢迎来到Python周刊第258期。让我们直奔主题。

# 来自赞助商

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/7394541b-6b55-4fde-8756-6b7547029f1b.png)](https://hired.com/?utm_source=newsletters&amp;utm_medium=pythonweekly&amp;utm_campaign=q3-16)

你时髦，精明，高效。但是为什么还用老方法找工作？[试试Hired吧](https://hired.com/?utm_source=newsletters&amp;utm_medium=pythonweekly&amp;utm_campaign=q3-16)，在4,000+家高科技公司面前闪亮登场，并提供个性化支持，助你找到理想的工作。


# 文章，教程和讲座

[使用Python探索Git](https://www.youtube.com/watch?v=CB9p8n3gugM)

在这个演讲中，我们以磁盘上的Git数据结构的一个简单的解释开始。然后，开始直播编码，读取这些数据结构，接着重构一个用于任意git仓库的`git log`命令，而无需使用`git`命令。完成后，我们应该拥有自己能用的命令，对于任意仓库，任意分支，它和`git log`功能一致。我们将简单地从`HEAD`开始，一路直达数据结构。

[在Django中使用IBM Watson API](https://www.epilis.gr/en/blog/2016/08/18/ibm-watson-apis-django/)

花几分钟，构建一个使用IBM Watson API来分析评论的Django应用。

[Tensorflow上的RNNs，实用指南和未公开特性](http://www.wildml.com/2016/08/rnns-in-tensorflow-a-practical-guide-and-undocumented-features/)

这篇文章重温Tensorflow上的RNNs的使用最佳实践，特别是在官网上没有得到很好记录的特性。

[Lists和Tuples大对决](http://nedbatchelder.com/blog/201608/lists_vs_tuples.html)

常见的Python初学者问题：列表和元组之间有何区别？答案是，有两个不同的差异，以及两者之复杂的相互作用。还有就是技术差异和文化差异。

[Podcast.__init__ 第71集 - 和Radim Řehůřek聊聊Gensim](https://podcastinit.com/radim-rehurek-gensim.html)

能够了解一段文本的上下文通常被认为是人工智能领域。然而，主题建模和语义分析可以用来让计算机确定不同的消息和文章是否是关于同样的事情。本周，我们和Radim Řehůřek聊聊他关于GenSim的工作，GenSim是一个Python库，用来进行非结构化文本的无监督分析，并应用机器学习模型到自然语言理解的问题上。

[页面扫描](https://mzucker.github.io/2016/08/15/page-dewarping.html)

一篇显示了如何扁平化弯曲页面上的图像的文章。

[Python中的线性分类介绍](http://www.pyimagesearch.com/2016/08/22/an-intro-to-linear-classification-with-python/)

这篇文章讨论了参数化学习和线性分类的基础知识。虽然简单，但是线性分类可以被看成更高级的机器学习算法基本构架模块，自然扩展到神经网络和卷积神经网络。

[使用Python和LLVM的，用于TensorFlow计算图形的JIT本地代码生成](http://blog.christianperone.com/2016/08/jit-native-code-generation-for-tensorflow-computation-graphs-using-python-and-llvm/)

[Python JIT来了](https://lwn.net/Articles/691070/)

[用于格式化和数据清理的便捷Python库](https://blog.modeanalytics.com/python-data-cleaning-libraries/)

[生成梦幻地图](http://mewo2.com/notes/terrain/)

[如何用Python和Flask构建和部署一个Facebook Messenger机器人，一个教程](http://tsaprailis.com/2016/06/02/How-to-build-and-deploy-a-Facebook-Messenger-bot-with-Python-and-Flask-a-tutorial/)


# 好玩的项目，工具和库

[Kyoukai](https://github.com/SunDwarf/Kyoukai)

Kyōkai是一个快速的异步Python服务器端Web框架。它建立在asyncio和用于非常快速的web服务器的Asphalt框架之上。

[undebt](https://github.com/Yelp/undebt)

Undebt是一个快速、简单、可靠的工件，用于执行大规模的自动化代码重构（Yelp的使用工具）. Undebt允许你使用标准而直接的Python来定义复杂的查找替换规则，使用一个简单的命令就可以快速应用到整个代码库。

[PyGradle](https://github.com/linkedin/pygradle)&nbsp;

PyGradle构建系统是一个Gradle插件集，它可以用来构建Python工件。由PyGradle生成的工件与由Python的setuptools库生成的工件向前及向后兼容。

[PyCNN](https://github.com/ankitaggarwal011/PyCNN)

在Python中使用细胞神经网络进行图像处理。

[Enforce](https://github.com/RussBaz/enforce)

Enforce是一个简单的Python 3.5 (或者更高版本)应用，它基于类型提示(PEP 484)强制运行时类型检查。

[pybble](https://github.com/hiway/pybble)

Pebble的Python（几乎）支持

[ray](https://github.com/felipevolpone/ray)

一个帮助你提供精心设计的Python API的框架。

[picotui](https://github.com/pfalcon/picotui)

仅需最小化依赖的，轻量、纯Python文本用户界面控件工具箱。

[chartpy](https://github.com/cuemacro/chartpy)

简单的使用Python API封装器来使用matplotlib, plotly, bokeh等绘制图表。

[ulmo](https://github.com/ulmo-dev/ulmo)

干净、简单、快速地访问公共水文和气候数据。

[rex](https://github.com/shellphish/rex)

Shellphish的自动利用引擎，最初是为了Cyber Grand挑战赛创建的。

[posio](https://github.com/abrenaut/posio)

使用Websockets的多人地理游戏。

[ansible-django-stack](https://github.com/jcalazan/ansible-django-stack)

使用Nginx, Gunicorn, PostgreSQL, Celery, RabbitMQ, Supervisor, Virtualenv, 和Memcached设置一个Django应用的Ansible Playbook。还包含了一个用于配置VirtualBox虚拟机的Vagrantfile。

[3D-R2N2](https://github.com/chrischoy/3D-R2N2)

使用递归神经网络的单/多视图图像进行体元重建。


# 最新发布

[py.test 3.0](http://docs.pytest.org/en/latest/changelog.html)&nbsp;

[PyDev 5.2.0](http://pydev.blogspot.com.br/2016/08/pydev-520-released-static-type.html)


# 近期活动和网络研讨会*

[在线活动：Python生成器：将你的循环翻过来](https://www.crowdcast.io/e/generators/register)

我们会讨论Python中的生成器是什么，以及如何使用生成器来替代列表。我们还会讨论使用生成器来简化循环逻辑，以及如何使用生成器把while循环转换成for循环。

[SoCal Python 2016年八月聚会 - Los Angeles, CA](https://www.meetup.com/socalpython/events/233187442/)

将会有以下演讲：

*   利用Python魔术方法和延迟对象的力量
*   The Two Trains and Other Refactoring Analogies
