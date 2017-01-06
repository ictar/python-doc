原文：[Python Weekly - Issue 276](http://eepurl.com/cwf-qT)

---
  
欢迎来到Python Weekly第276期。本周，我想要感谢我们的赞助商Springboard的支持。一定要看看他们已经启动的第一个数据科学集训。
  
# 文章，教程和讲座

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/ca5dd213-17e8-4c92-b392-4774dfe4b15e.gif)](https://www.springboard.com/workshops/data-science-career-track?utm_source=pythonweekly&utm_campaign=pythonweeklydscblast&utm_medium=email)

Springboard已经启动了[第一个数据科学集训](https://www.springboard.com/workshops/data-science-career-track?utm_source=pythonweekly&utm_campaign=pythonweeklydscblast&utm_medium=email)，保证你得到一个数据科学家职位 —— 否则，归还你支付的金钱。为你提供专家课程，来自行业专家的1对1辅导，职业指导和雇主合作关系，扩展你的**Python**技能，让你学习成为一名数据科学家所需的一切知识，从而领你走进数据科学的大门。


# 文章，教程和讲座 
  
[给研究者的免费超级计算：在Open Science Grid上使用Python的一个教程](https://srcole.github.io/2017/01/03/osg_python/)  

超级计算机资源一般要花钱，但是，Open Science Grid (OSG)为美国的任何研究人员免费提供了高吞吐量的计算。这个教程的目的是提供一个在OSG上运行Python任务的完整示例。这个例子很重要，其中，包含了多个数据集、公共库 (例如，scipy)、私有库和输出分析。
  
[愉快玩耍机器学习：新手指南](https://github.com/humphd/have-fun-with-machine-learning)  

这个给没有AI背景的程序员的机器学习动手指南。在这个指南中，我们的目标将是编写一个程序，它使用机器学习进行高度确定性预测：在没看到图像之前，仅使用图像本身，预测数据/未训练样本中的图像是海豚还是海马。
  
[迭代器协议：在Python中，for循环是如何工作的](http://treyhunner.com/2016/12/python-iterator-protocol-how-for-loops-work/)  

我们将要学习，在Python中，for循环是如何工作的。一路上，我们会需要学习可迭代对象、迭代器和迭代器协议。让我们循环起来。
  
[Python中的本地Hadoop文件系统 (HDFS) 连通性](http://wesmckinney.com/blog/python-hdfs-interfaces/)  

通过Hadoop文件系统（HDFS）的WebHDFS网关，以及它的本地基于Protocol Buffers的RPC接口，已经开发了许多与其交互的Python库。我将对现有的工具进行概述，并且展示一些我一直在做的工程，以提供在发展中的Arrow生态系统中的一个高性能的HDFS接口。
  
[在Python中，从头开始实现一个分类器，我学到了什么](http://www.jeannicholashould.com/what-i-learned-implementing-a-classifier-from-scratch.html)  

为了揭秘机器学习算法背后的一些魔法，我决定从头开始实现一个简单的机器学习算法。我将不会使用诸如scikit-learn这样的已经实现了许多算法的库。相反，我会编写所有的代码，以便拥有一个可用的二进制分类器算法。这个练习的目标在于了解它的内部工作。
  
[使用Fabric和Ansible自动化Django部署](https://realpython.com/blog/python/automating-django-deployments-with-fabric-and-ansible/)  

在上一篇文章中，我们覆盖了成功在单个服务器上开发和部署一个Django应用所需的的所有步骤。在这个教程中，我们会使用Fabric (v1.12.0)和Ansible (v2.1.3)自动化部署过程，以解决这些问题：缩放和冗余。
  
[使用Python，破解BBC的GCHQ Puzzlebook挑战](https://stiobhart.net/2016-12-03-gchqpuzzlebook/)  
  
[利用深度学习，根据文本进行图像合成](https://www.youtube.com/watch?v=rAbhypxs1qQ)  
  
[TensorKart：使用的TensorFlow的自驾MarioKart](http://kevinhughes.ca/blog/tensor-kart)  
  
[Flask-Ask —— 构建复杂Alexa技能的简单易用方式的一个教程](https://blog.craftworkz.co/flask-ask-a-tutorial-on-a-simple-and-easy-way-to-build-complex-alexa-skills-426a6b3ff8bc)  
  
[超容易的Micropython ESP8266 Windows指南。无需Guesswork！](http://www.instructables.com/id/The-Super-Easy-Micropython-ESP8266-Guide-No-Guessw/?ALLSTEPS)  
  
[当心Python的新式字符串格式](http://lucumr.pocoo.org/2016/12/29/careful-with-str-format/)  
  
[如何用Python创建间距图表](https://gavinr.com/2016/12/22/create-pitch-charts-python/)  
  
  
# 好玩的项目，工具和库 
  
[Grumpy](https://github.com/google/grumpy)  

Grumpy是一个从Python到Go源代码反编译器和运行时，其目的在于成为CPython 2.7的一个近似替代品。关键的区别在于，它将Python源代码编译成Go源代码，后者接着会被编译成本地代码，而不是字节码。这意味着，Grumpy没有虚拟机。已编译的Go源代码是对Grumpy运行时（一个Go库，提供与Python C API类似功能的服务）的一系列调用。
  
[audio-reactive-led-strip](https://github.com/scottlawsonbc/audio-reactive-led-strip)  

使用ESP8266和Python的实时LED条音乐可视化。
  
[PEP8 Speaks](https://github.com/OrkoHunter/pep8speaks)  

一个GitHub集成，它检查pep8问题，然后通过Pull请求评论。
  
[chatbot-rnn](https://github.com/pender/chatbot-rnn)  

一个由深度学习驱动，基于Reddit上的数据进行训练的玩具聊天机器人。
  
[Thonny](http://thonny.org/)  

给初学者的Python IDE。
  
[yarl](https://github.com/aio-libs/yarl)  

这个模块提供了用于url解析和更改的便捷的URL类。
  
[GeeMusic](https://github.com/stevenleeg/geemusic)  

GeeMusic是一项Alexa技能，它桥接了Google Music和Amazon的Alexa。它希望拯救所有那些想要一个Echo/Dot，但是不想关闭Google Music，或者为Amazon Music Unlimited订阅付费的人。 
  
[EH Forwarder Bot](https://github.com/blueset/ehForwarderBot)  

一个可扩展的聊天机器人通道框架。在平台间传递消息，远程控制你其他的账户。
  
[Truffle Hog](https://github.com/dxa4481/truffleHog)  

通过git仓库搜索高熵字符串，深挖提交历史和分支。这对于查找那些意外提交的包含高熵的密钥很有效。

[ThreadTone](https://github.com/theveloped/ThreadTone)  

一个由线条组成的图像的半色调展示。
  
[Amazon-Alert](https://github.com/anfederico/Amazon-Alert)  

跟踪亚马逊上的价格，并且在降价的时候接收到电子邮件告警。
  
  
# 最新发布
  
[Python 3.6.0](http://blog.python.org/2016/12/python-360-is-now-available.html)  
  
[Django错误修复版本发布：1.10.5](https://www.djangoproject.com/weblog/2017/jan/04/bugfix-release/)  
  
  
# 近期活动和网络研讨会 
  
[San Francisco Python 2017年1月聚会 - San Francisco, CA](https://www.meetup.com/sfpython/events/236600824/)  

将会有以下讲座

  * Magic-Wormhole：简单的安全文件传输
  * pycoin库

  
[IndyPy 2017年1月每月聚会 - Indianapolis, IN](https://www.meetup.com/indypy/events/228228310/)  

这个月，Matt Rasband将会展示，Docker, Python和You（Ele注：你？）。
  
[Austin Python 2017年1月聚会 - Austin, TX](https://www.meetup.com/austinpython/events/234338374/)  
  
[Boulder Python 2017年1月聚会 - Boulder, CO](https://www.meetup.com/BoulderPython/events/235791889/)  
  
[PyAtl 2017年1月聚会 - Atlanta, GA](https://www.meetup.com/python-atlanta/events/234242113/)  
  
