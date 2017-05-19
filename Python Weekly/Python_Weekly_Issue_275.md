原文：[Python Weekly - Issue 275](http://eepurl.com/cuGU8L)

---

 
欢迎来到Python Weekly第275期。我祝福你们都节日愉快，并且期待2017，送给你们最好的Python相关连接。（Ele注：节日快乐呀，小伙伴们~~）
  
# 文章，教程和讲座 
  
[利用TensorFlow进行交通标志识别](https://medium.com/@waleedka/traffic-sign-recognition-with-tensorflow-629dffc391a6)  

这是关于构建一个深度学习模型来识别交通标志系列的第一部分。在这部分，我将谈谈图像分类，并且会尽可能的让模型简单。在后面的部分中，我将介绍卷积神经网络、数据增强和对象检测。  
  
[Episode #90: 使用Python进行数据再加工](https://talkpython.fm/episodes/show/90/data-wrangling-with-python)  

你有没有脏乱数据的问题？无论你是一名软件开发者，或者是一个数据科学家，你都一定会遇到那些畸形、不完整、甚至可能是错的数据。不要让凌乱的数据破坏了你的应用，或者生成错误的结果。你应该怎么办呢？听听这一期，Katharine Jarmul会谈到有关她合作编写的名为使用Python进行数据再加工（Data Wrangling with Python）书，以及她的PyCon UK演讲，名为，如何用Python自动化你的数据清理(How to Automate your Data Cleanup with Python)。
  
[如何在Debian 8上，使用uWSGI和Nginx，提供Django应用服务](https://www.digitalocean.com/community/tutorials/how-to-serve-django-applications-with-uwsgi-and-nginx-on-debian-8)  

本指南中，我们将演示如何在Debian 8上安装和配置一些组件，以支持和提供Django应用服务。我们会配置uWSGI应用容器服务器来与我们的应用交互。然后，我们会设置Nginx来反向代理到uWSGI，利用其安全性和性能特性来服务我们的应用。
  
[Podcast.__init__ 第88集 - 和Michal Čihař聊聊Weblate](https://www.podcastinit.com/episode-88-weblate-with-michal-cihar/)  

添加翻译到我们的项目中让它们可以用于更多的地方，更多的人，这最终会让它们更有价值。如果你没有正确的工具，那么管理本地化过程是很困难的，因此，本周，Michal čihař跟我们聊聊Weblate项目，以及它是如何简化集成翻译到你的源代码中的过程的。
  
[Python化的代码审查](https://access.redhat.com/blogs/766093/posts/2802001)  
  
[24小时生成850k张图像：自动化深度学习数据集创建](https://gab41.lab41.org/850k-images-in-24-hours-automating-deep-learning-dataset-creation-60bdced04275)  
  
[为本地开发和小规模部署使用Docker和Docker Compose](https://www.codementor.io/jquacinella/tutorials/docker-and-docker-compose-for-local-development-and-small-deployments-ph4p434gb)  
  
[使用Pandas构建一个金融模型 - 第2版](http://pbpython.com/amortization-model-revised.html)  
  
[给SAS用户的Python指南](http://nbviewer.jupyter.org/github/RandyBetancourt/PythonForSASUsers/tree/master/)  
  
[如何远程监控你的Pi进程](https://github.com/initialstate/pi-process-dashboard/wiki)  
  
  
# 好玩的项目，工具和库 
  
[Maya](https://github.com/kennethreitz/maya) 

在Python中，使用Datetime是非常令人沮丧的，特别是处理不同系统上的不同地区时。这个库的存在是为了让简单的事情更简单，同时承认时间就是一个幻觉 (时区更是如此)。
  
[awesome-functional-python](https://github.com/sfermigier/awesome-functional-python)  

关于Python中进行函数式编程的棒棒哒的东西的列表。
  
[Whitewidow](https://github.com/WhitewidowScanner/whitewidow)  

Whitewidow是一个开源的自动SQL漏洞扫描器，它能够过一遍文件列表，或者能够抓取Google，查找潜在易受攻击的网站。它允许自动文件格式化、随机用户代理、IP地址、服务器信息、多种SQL注入语法、从程序中加载sqlmap的能力，以及有趣的环境。
  
[Elizabeth](https://github.com/lk-geimfari/elizabeth)  

Elizabeth是一个用于生成伪数据的快速且更易于使用的Python库，这在软件测试阶段需要引导数据库时是非常有用的。
  
[PixieDust](https://github.com/ibm-cds-labs/pixiedust)  

PixieDust是一个开源Python辅助库，它作为Jupyter notebooks的一个附加品工作，用以改善处理数据的用户体验。它还提供额外的能力，填补当notebook被托管到云上，并且用户无法访问配置文件时的沟壑。
  
[Bowtie](https://github.com/jwkvam/bowtie)  

Bowtie是一个用于在python中编写面板的库。无需懂得web框架或者JavaScript，关注于构建python中的功能性。以全新的方式交互式探索你的数据！部署并与他人分享吧！

[libtmux](https://libtmux.git-pull.com/en/latest/) 

libtmux是tmuxp背后的工具，一个python中的tmux工作空间管理器。它建立在tmux的目标和格式之上，创建一个对象映射到遍历、检查和与存活的tmux会话交互。
  
[zxcvbn-python](https://github.com/dwolfhub/zxcvbn-python) 

Dropbox的现实密码强度评估器的Python实现。
  
[kernelscope](https://github.com/josefbacik/kernelscope) 

一个用于收集内核跟踪数据和可视化它的守护进程和web应用。
  
[kansha](https://github.com/Net-ng/kansha) 

Kansha是一个开源的web应用，用来管理和共享协作的scrum板等等。
  
[Wifi-Dumper](https://github.com/Viralmaniar/Wifi-Dumper)  

这是一个开源工具，用来Windows机器上的转储wiki配置和已连接接入点的明文密码。这个工具将在Wifi渗透测试中帮助你。此外，当执行red team或者内部基础设置约定时也有用。
  
[EBS-SnapShooter](https://github.com/smileisak/ebs-snapshooter)  

EBS-SnapShooter是一个基于boto2的python脚本，它为你所有的aws ebs卷创建每日、每周、每月快照。  
  
  
# 近期活动和网络研讨会  
  
[网络研讨会：使用PyCharm，扩展一个带REST Capabilities的Django应用](https://blog.jetbrains.com/pycharm/2016/12/webinar-extending-a-django-app-with-rest-capabilities-using-pycharm-january-10th/)  

这个实作型网络研讨会将会教你如何利用Python和Django来扩展一个已经存在的web应用，并且添加REST capabilities。此次网络研讨会以一个构建来跟踪笔记的已有的Django应用的概述开始，然后使用Django REST框架深入添加REST。参与者可以跟着我们构建Notes web应用。我们将展示使用PyCharm来检查数据库和测试我们的API。我们还将看看使用强大的PyCharm调试器来调试这个应用。
  
