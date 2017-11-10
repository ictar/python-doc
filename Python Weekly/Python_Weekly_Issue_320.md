原文：[Python Weekly - Issue 320 ](http://eepurl.com/c-Jfrr)

---

欢迎来到Python周刊第320期。本周干货满满。尽情享用吧！
  
# 来自赞助商  

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/b88ddabb-e48c-4fbd-af9a-fe48f8a98690.png)](https://goo.gl/wlxnDm)

[Intel® Distribution for Python*](https://software.intel.com/en-us/distribution-for-python?utm_source=9Nov2017%20ad%20Python%20weekly&utm_medium=email&utm_campaign=Nov%202017%20Python%20Weekly%20newsletter)免费提供，并且许可证允许商业和非商业用途。这就对了！试试Intel优化过的NumPy、SciPy和scikit-learn，全部都在内部进行了优化。你的代码保持不变 —— 只需要在Intel Python环境中运行它，就可以获得速度提升。
  
  
# 新闻  
  
[PyCon 2018注册现已开放！](https://pycon.blogspot.com/2017/11/pycon-2018-registration-is-now-open.html)

前六个PyCon已经售罄，所以准备另外一个PyCon，并且提早取票。前800张票享受早鸟优惠，企业门票节省超过20%，个人门票则超过12%。学生如果买得早的话，可以节省$25！
  
[2018年DSF董事会选举申请](https://www.djangoproject.com/weblog/2017/nov/07/2018-dsf-board-election-application/)

如果你对帮助支持Django的发展感兴趣，那么，这是一个机会。
  
  
# 文章，教程和讲座  
  
[构建你自己的Instagram发现引擎：手把手教程](https://getstream.io/blog/building-instagram-discovery-engine-step-step-tutorial/)

如果Instagram的“Explore”部分显示的是符合你的兴趣的内容，岂不是很棒？当你打开应用，展示的内容和推荐几乎总是与你特定的爱好、兴趣、联系等等相关。虽然认为我们是Instagram世界的中心可能很有趣，但是事实是，每天个性化的相关内容也是为其他4亿人专门准备的。每天有4亿活跃用户并且发布80万张照片，Instagram是如何决定把什么放在你的“explore”部分的呢？让我们探讨一下Instagram用来决定你的Instagram时间表中的帖子和“explore”部分的分数的关键因素。
  
[敌对攻击是如何工作的](http://blog.ycombinator.com/how-adversarial-attacks-work/)

机器学习算法将输入作为数值向量。以特定的方法设计输入，从而从模型获得错误的结果被称为敌对攻击（adversarial attack）。在这篇文章中，我们将会展示攻击的主要类型的实际样例，解释为什么执行这些攻击非常容易，并且讨论源自此技术的安全隐患。
  
[Python列表推导教程](https://www.datacamp.com/community/tutorials/python-list-comprehension)

学习如何有效地使用Python中的列表推导来创建列表、替换（嵌套）for循环以及map()、filter()和reduce()函数，……！
  
[结合Python和C：高级“ctypes”功能](https://dbader.org/blog/python-ctypes-tutorial-part-2#)  

学习把Python和原生库相结合的高级模式，例如在Pytnon中处理C结构，以及值传递与引用传递语义。
  
[使用PyTorch，为任意图像抽取特征向量](https://becominghuman.ai/extract-a-feature-vector-for-any-image-with-pytorch-9717561d1d4c)  
在这篇教程中，我们将会把图像转换成向量，并且使用余弦相似度来测试向量的质量。
  
[如何使用你的监控指标来编写一个自定义的Kubernetes调度程序](https://sysdig.com/blog/kubernetes-scheduler/)

本文涵盖了创建一个自定义的Kubernetes调度程序的用例，并且实现了一个使用来自Sysdig的监控指标（系统、网络、服务、statsd、JMX或者Prometheus指标）的例子。
  
[Eager Execution：一个TensorFlow命令式、通过运行定义的接口](https://research.googleblog.com/2017/10/eager-execution-imperative-define-by.html)  

Eager execution是一个命令式、通过运行定义的接口，其中，当从Python调用操作时，会立即执行它们。这使得TensorFlow更容易上手，并且会让研究和开发更直观。
  
[使用Flask构建一个GIF消息机器人](https://medium.com/codebagng/building-a-gif-messenger-bot-with-flask-fcdca58e581c)  

在这篇文章中，我们将会构建一个GIF Facebook Messenger机器人。这个机器人将会回复跟它从用户那边接收到的任意文本相关的GIF。
  
[TensorFlow神经网络教程](http://stackabuse.com/tensorflow-neural-network-tutorial/)  
  
[为Gevent开放任务队列](http://charlesleifer.com/blog/ditching-the-task-queue-for-gevent/)   
  
[如何构建你自己的区块链第3部分 —— 编写挖缺和谈论的节点](https://bigishdata.com/2017/11/02/build-your-own-blockchain-part-3-writing-nodes-that-mine/)  
  
[使用Django Channels，构建一个聊天室](https://www.ploggingdev.com/2017/11/building-a-chat-room-using-django-channels/)  
  
[快速使用SQLite和Python](http://charlesleifer.com/blog/going-fast-with-sqlite-and-python/)  
  
[爬取Reddit，查找最受欢迎的域名](https://www.find-me.co/blog/reddit_creators)  
  
[如何使用TensorFlow的对象检测API，来训练你自己的对象检测器](https://towardsdatascience.com/how-to-train-your-own-object-detector-with-tensorflows-object-detector-api-bec72ecfe1d9)  
  
  
# 本周的Python工作  
  
[Zapier招聘产品工程团队经理](http://jobs.pythonweekly.com/jobs/product-engineering-team-manager/)

我们正在寻找一名产品工程经理来领导Zapier不断增长的产品工程团队。有兴趣帮助构建为数百万专业人员提供自动化的工具吗？那么，请继续阅读……

  
# 好玩的项目，工具和库  
  
[pyro](https://github.com/uber/pyro)  

Pyro是一个建立在PyTorch之上的灵活的可扩展深度概率编程库。
  
[Colaboratory](https://research.google.com/colaboratory/unregistered.html)   

Colaboratory是一个用于机器学习教育和研究的研究工具。它是Jupyter notebook环境，无需安装即可使用。
  
[tt](https://github.com/welchbj/tt)  

一个用来使用布尔表达式的Pythonic工具包。
  
[Tangent](https://github.com/google/tangent)  

纯Python中的源码到源码可调式衍生物。
  
[conversation-tensorflow](https://github.com/DongjunLee/conversation-tensorflow)  

一个神经聊天机器人，使用Tensorflow 1.4版本的序列到序列模型与注意力解码器（attentional decoder）实现（估计、实验和数据集）

[Striker](https://github.com/UltimateHackers/Striker)  

Striker是一个烦人信息和漏洞扫描器。
  
[jbc](https://github.com/jackschultz/jbc)  

简单的区块链，用以学习和讨论区块链是如何工作的。
  
[sqs-s3-logger](https://github.com/ellimilial/sqs-s3-logger)  

通过SQS，自动化无服务器日志记录到S3。
  
[PySchemes](https://github.com/shivylp/pyschemes)   

PySchemes是一个用来验证数据结构的Python库。
  
[phishing_catcher](https://github.com/x0rz/phishing_catcher)  

使用certstream SSL证书直播捕获恶意钓鱼域名。
  
[Uplink](https://github.com/prkumar/uplink)  

一个Python声明式HTT客户端。  
  
[Mentalist](https://github.com/sc0tfree/mentalist)  

Mentalist是自定义词汇表生成的图形工具。它利用常见的人类范例来构建密码，并且能够输出完整的词汇表以及与Hashcat和John the Ripper兼容的规则。
  
[MazeGenerator](https://github.com/jostbr/MazeGenerator)   

生成和解决随机可解迷宫的Python脚本，使用深度优先搜索和递归回溯算法。
  
  
# 最新发布  
  
[TensorFlow r1.4](https://developers.googleblog.com/2017/11/announcing-tensorflow-r14.html)  
  
[Jinja 2.10](https://github.com/pallets/jinja/releases/tag/2.10)  
  
[RISE 5.1.0](http://www.damian.oquanta.info/posts/rise-510-is-out.html )  
  
  
# 近期活动和网络研讨会  
  
[Boulder 2017年11月Python聚会 - Boulder, CO](https://www.meetup.com/BoulderPython/events/239106676/)  

将会有以下演讲：

  * JavaScript vs. Python （我在6个小时中，从网络上学到的东西）
  * 用模拟绘制政治边界
  * 基于特性（有生产力）的测试，带使用Python和JS编写的示例

  
[Princeton Python用户2017年11月聚会 - Princeton, NJ](https://www.meetup.com/pug-ip/events/244002972/)

本周演讲将是：大致了解Wallaroo，大致了解我们使用我们规模无关的API所解决的问题，对Python API的简介，演示我们的自动缩放功能，运行中的规模无关的API的能力的想法。
  
[IndyPy 2017年11月每月聚会 - Indianapolis, IN](https://www.meetup.com/indypy/events/243754572/)

将会有以下演讲：

  * Python内置的`logging`模块
  * 作为一名开发者，如何在家工作，以及企业可以如何帮助它们的远程工作人员。

