原文：[Python Weekly - Issue 292](http://eepurl.com/cMFwd5)

---

欢迎来到Python Weekly第292期。本周，让我们直入主题。 
  
# 来自赞助商  

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/d0352677-9ed4-4c6c-84bb-824d63faadb4.png)](http://www3.activestate.com/activepython.html?utm_source=python-weekly&utm_medium=email&utm_term=&utm_content=17-04-27-newsletter&utm_campaign=activepython-fc)

ActivePython最新的发布版本中预编译了200+个包，现在，管理包更简单了。NumPy, SciPy, scikit-learn, Django, Flask, Tornado, AWS SDK等等用于数据科学和web应用的包。使用一个支持的Python版本[开始免费开发](http://www3.activestate.com/activepython.html?utm_source=python-weekly&utm_medium=email&utm_term=&utm_content=17-04-27-newsletter&utm_campaign=activepython-fc)，花费更少的时间配置吧。 
  
  
# 新闻  
  
[Python之力降临SQL Server 2017](http://www.infoworld.com/article/3191187/database/python-power-comes-to-sql-server-2017.html)  

微软的旗舰数据库的下一个版本将运行Python脚本，支持作为本地T-SQL存储过程的Python第三方数据库的完全访问
  
  
# 文章，教程和讲座  
  
[Episode #108: MicroPython和Adafruit的开源硬件](https://talkpython.fm/episodes/show/108/micropython-and-open-source-hardware-at-adafruit)  

想要学习如何构建一个像钢铁侠一样的电弧反应器配件，或者太阳能充电背包？假使你可以用Python编程这些设备，那么会怎么样呢？我们将谈到一个让这成为可能的项目和公司。本周，你会见到Tony DiCola，他在Adafruit工作。Adafruit是一家可以使硬件编程的公司。我们还将讨论micropython，它让你用Python编程这些很酷的设备！
  
[深刻了解GIL：如何编写快速并且线程安全的Python](https://opensource.com/article/17/4/grok-gil)  

我们探索Python的全局解释锁，并且学习它是如何影响多线程程序的。
  
[用Python构建和探索Reddit地图](https://lmcinnes.github.io/subreddit_mapping/)  

这个notebook的目标是构建和分析Reddit上10,000个最受欢迎的子reddit的地图。为了做到这一点，我们需要一种手段来衡量两个子reddit的相似性。在FiveThirtyEight上的一篇很不错的文章中，Trevor Martin通过考虑两个不同的子reddit上用户评论的重叠来进行分析。他们的兴趣在于，使用代表性向量的向量代数来查看，比方说，如果你从r/The_Donald删除r/politics，会发生什么事。我们的兴趣更广泛些 —— 我们想要绘制并可视化子reddit的空间，并尝试将子reddit聚集到它们的自然群众。之后，我们可以探索其中一些集群，然后找到有趣的东西。
  
[高效率Matplotlib](http://pbpython.com/effective-matplotlib.html)  

这篇文章将展示如何使用matplotlib，并为那些入门用户，或者没有时间学习matplotlib的用户提供一些建议。我坚信matplotlib是python数据科学栈的重要组成部分，并且希望这篇文章会帮助人们了解如何使用它来进行可视化。
  
[给Node开发者的Flask教程](http://mherman.org/blog/2017/04/26/flask-for-node-developers/) 

在这篇文章中，我们将会介绍如何使用Python和Flask构建一个基本的CRUD服务器端应用，面向精通Node和Express的JavaScript开发者。
  
[使用Python、Flask、NASA API和Twilio MMS的火星上的短信机器人](https://www.twilio.com/blog/2017/04/texting-robots-on-mars-using-python-flask-nasa-apis-and-twilio-mms.html)   

NASA有一大堆棒棒哒的API，它们让你可以编程以访问太空奇迹。我认为，火星漫游者照片API(Mars Rover Photos API)尤其惊人，因为你可以用它来看看好奇号火星漫游者照了哪些照片。让我们使用火星漫游者API（ Mars Rover API）、Twilio MMS、Python和Flask来构建一个应用，使得我们可以提供一个电话号码，然后接收来自火星的图片。
  
[我是怎样使用Python来比较文档相似性的？](https://www.oreilly.com/learning/how-do-i-compare-document-similarity-using-python)  

学习如何使用gensim Python库来确定两个或多个文档之间的相似性。
  
[7行代码的深度学习](https://chatbotslife.com/deep-learning-in-7-lines-of-code-7879a8ef8cfb)  

机器学习的本质在于识别数据之中的模式。这归结为3件事：数据、软件和数学。那么你要问了，用7行代码我们可以做些啥？答案是，很多很多。
  
[Podcast.__init__ 第106集 - yt项目](https://www.podcastinit.com/episode-106-yt-project-with-nathan-goldbaum-and-john-zuhone/)  

天体物理学和宇宙学是需要使用复杂的多维数据来模拟宇宙运行的领域。yt项目被创建来让使用这些数据以及有用可视化的提供简单而有趣的。本周，Nathan Goldbaum和John Zuhone分享了yt是如何开始、如何运作以及如何使用。
  
[如何使用Keras和Tensorflow，为一个1000件日常对象类别构建图像识别系统](https://deeplearningsandbox.com/how-to-build-an-image-recognition-system-using-keras-and-tensorflow-for-a-1000-everyday-object-559856e04699)  

这是系列文的第一篇，它介绍了如何使用Keras，TensorFlow和python requests库，在几行代码中构建你自己的识别或者检测/边框预测web服务。
  
[可视化调试](https://www.youtube.com/watch?v=nksiGORLDZw)  

PyCharm在调试时有一个可视化界面，我们希望帮助更多的开发者冒险尝试并调试。在这个演讲中，Paul Everitt介绍了这个调试器，并且介绍而来编写一个2d游戏的过程中许许多多必须的内容。
  
[Python和MongoDB入门](https://www.mongodb.com/blog/post/getting-started-with-python-and-mongodb)  

这篇文章是针对那些对MongoDB不熟悉的Python开发者的，其中，你将学到，如何：使用 MongoDB Atlas创建一个免费托管的MongoDB数据库，安装PyMongo，Python驱动程序，连接到MongoDB，浏览MongoDB集合（Collection）和文档（Document），使用PyMongo执行基本创建、检索、更新和删除（CRUD）操作。
  
[Python代码可视化](http://codimension.org/documentation/visualization-technology/python-code-visualization.html)  
  
[用OpenCV、Python和dlib进行眨眼检测](http://www.pyimagesearch.com/2017/04/24/eye-blink-detection-opencv-python-dlib/)  
  
[Python和Trio中的Control-C处理](https://vorpus.org/blog/control-c-handling-in-python-and-trio/)  
  
[Keras小抄：Python中的神经网络](https://www.datacamp.com/community/blog/keras-cheat-sheet)  
  
  
# 书籍  
  
[来点django：django 1.11最佳实践（Two Scoops of Django: Best Practices for Django 1.11）](https://www.twoscoopspress.com/products/two-scoops-of-django-1-11)

这本书干货满满，它将有助于你的django项目。它向你介绍了我们多年来收集的各种提示、技巧、模式、代码片段和技术。
  
  
# 本周的Python工作  
  
[Backend Engineer at Bitly](http://jobs.pythonweekly.com/jobs/backend-engineer-2/)   

Bitly正在寻找一名关注后端开发以帮助改进和扩展助力我们所有产品的服务的中级应用工程师。API设计会让你感到兴奋不已吗？你是否想应对构建可扩展、强大的分布式系统的挑战？你看到了构建工具来帮助人们了解周围世界的机遇了吗？那么，Bitly或许就是你的命定之所。
  
  
# 好玩的项目，工具和库  
  
[Timestrap](https://github.com/overshard/timestrap)  

你可以随时随地托管的发票实时跟踪。支持多种格式的完全导出，并且易于扩展。  
  
[dnc](https://github.com/deepmind/dnc)  

可微分神经计算机的TensorFlow实现。
  
[Manticore](https://github.com/trailofbits/manticore)  

Manticore是一个用于动态二进制分析的原型设计工具，支持符号执行、污染分析和二进制检测。
  
[UserLine](https://github.com/THIBER-ORG/userline)  

这个工具通过展示用户域、源和目标登录以及会话持续时间之间的图表关系，自动化从MS Windows安全事件创建登录关系的过程。
  
[Focuson](https://github.com/uber/focuson)   

Focuson是一个查找基于flask的python网络应用的安全漏洞的实验性工具。因为使用数据流分析，它将为安全工程师发出名单，用以以合理的信噪比进行调查。

[evilginx](https://github.com/kgretzky/evilginx)  

用于任何网络服务的钓鱼 凭证和会话cookies的中间人攻击框架。它应该只用于带有来自待验证方书面许可的合法渗透测试任务中。
  
[memory_profiler](https://pypi.python.org/pypi/memory_profiler)  

一个用于监控python程序的内存使用的模块。
  
[Memgraph](https://github.com/technige/memgraph)  

Memgraph是一个提供兼容Neo4j的内存中图像存储的Python库。
  
  
# 近期活动和网络研讨会  
  
[PyCon预演2：惰性、Mongo和不可变性 - Cambridge, MA](https://www.meetup.com/bostonpython/events/238648235/)  

将会有以下讲座：

  * 惰性求值（Lazy Evaluation）介绍
  * 用PyMongo分析数学素养数据
  * 不可变编程 - 编写函数式Python

  
[DFW Pythoneers 2017年5月聚会 - Plano, TX](https://www.meetup.com/dfwpython/events/238130975/)  
  

 

