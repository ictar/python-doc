原文：[Python Weekly - Issue 301 ](http://eepurl.com/cUlDLb)

---

欢迎来到Python Weekly第301期。本周，让我们直入主题。
  
# 来自赞助商  

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/6a426b27-541e-4bd7-b621-23ccdc662301.jpg)](http://www.amazon.com/gp/product/B0185367JQ/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B0185367JQ&linkCode=as2&tag=mymerch-20&linkId=OLIXWD4WZ5X6FFHD)

嘿，Python粉，你想要表达你对**Python**的爱吗？那么，[点击这里](http://www.amazon.com/gp/product/B0185367JQ/ref=as_li_tl?ie=UTF8&camp=1789&creative=390957&creativeASIN=B0185367JQ&linkCode=as2&tag=mymerch-20&linkId=OLIXWD4WZ5X6FFHD)，获取你的T恤，骄傲地穿上它吧。
 

# 文章，教程和讲座  
  
[GreenPiThumb：一个树莓派园艺机器人](https://mtlynch.io/greenpithumb/)  

这是关于GreenPiThumb的故事：它是一个自动给室内植物浇水的园艺机器人，但有时也会杀死它们（室内植物）。
  
[为了好玩，（零）利益将Python语法编译到x86-64程序集](http://benhoyt.com/writings/pyast64/)  

我使用Python内置的AST模型来解析Python语法的一部分，并将其转换到一个x86-64程序集里。它基本上就是一个工具，但是，它展示了出于自己的目的，使用AST模型来选择Python可爱的语法是多么的容易。
  
[Django vs Flask：一个从业者的角度](https://www.git-pull.com/code_explorer/django-vs-flask.html)

这个分析比较了2个Python框架，Flask和Django。它讨论了二者的特性，以及它们的技术哲学是如何影响软件开发人员的。这基于我自己使用它们的经历，以及花费在二者代码上的个人时间。
  
[使用Python管理你的AWS容器基础架构](https://www.caktusgroup.com/blog/2017/06/28/managing-your-aws-container-infrastructure-with-python/) 

在这篇文章中，我将会讨论在AWS上托管Python/Django应用和管理服务器基础架构的另一种方法。特别是，我们将会看看一个名为troposphere的Python库，它允许你使用Python来描述AWS资源，并且生成CloudFormation模板来上传上传到AWS。我们还会看看我编译的一个troposphere样本集，作为这篇文章的的准备工作的一部分，我将其命名为（至少是现在）AWS容器基础知识。
  
[将Snips和Home Assistant集成](https://medium.com/snips-ai/integrating-snips-with-home-assistant-314723645c77)  

在这篇文章中，我们将会展示如何使用Snips和Home Assistant来声控家庭电灯，无需网络连接。
  
[Python中的链表](https://dbader.org/blog/python-linked-list)  

学习如何在Python中实现一个链表数据结构，只使用内置的数据类型和标准库。
  
[我是如何使用Python和Twilio“黑入”我大学的注册系统的](https://www.twilio.com/blog/2017/06/hacked-my-universitys-registration-system-python-twilio.html)

大学生都知道注册一门课程，但是只发现注册人员已满的痛苦。在我的大学，对于大多数的课程，我们甚至没有一个候补名单系统。我们不得不依靠每天多次登录然后检查网站。这看起来像是一个计算机可以做的事，因此，我开始使用一点点Python和Twilio API来自动化它。
  
[如何使用Python 3.6中的静态类型检查](https://medium.com/@ageitgey/learn-how-to-use-static-type-checking-in-python-3-6-in-10-minutes-12c86d72677b)  
在编码时自动捕获许多常见错误。
  
[使用隐写术的指纹文件](http://blog.fastforwardlabs.com/2017/06/23/fingerprinting-documents-with-steganography.html)  

隐写术是将消息隐藏到不被认为会出现的地方的做法。在很好地执行了隐写术的片段中，任何不是预期接收者的人都能看到消息，但是却完全无法意识到消息就在那儿。本文谈论关于使用基于文本的隐写术的指纹文件。
  
[将Docker用于Flask应用部署（不仅仅是生产环境！）](http://www.patricksoftwareblog.com/using-docker-for-flask-application-development-not-just-production/)  

本文展示了如何配置Docker和Docker，来创建一个你可以在日常基础上轻松用来开发Flask应用的开发环境。
  
[Python世界里的GraphQL](http://nafiulis.me/graphql-in-the-python-world.html)  
  
[使用深度学习来重构高分辨率音频](https://blog.insightdatascience.com/using-deep-learning-to-reconstruct-high-resolution-audio-29deee8b7ccd)  
  
[为什么Pinterest从Django移到Flask。](https://www.reddit.com/r/Python/comments/6jjqxx/why_did_pinterest_move_from_django_to_flask/)  
  
[如何在Python中执行性能微基准测试](https://www.peterbe.com/plog/how-to-do-performance-micro-benchmarks-in-python)   
  
[开始翻译Django应用](https://blog.braham.biz/getting-started-with-translating-a-django-application-d85ec34e505)  
  
  
# 好玩的项目，工具和库  
  
[Solid](https://github.com/100/Solid)   

一个用Python写的全面的无梯度优化框架。
  
[python-nmap](http://xael.org/pages/python-nmap-en.html)  

python-nmap是一个有助于使用nmap端口扫描器的python库。它允许轻松操作nmap扫描结果，并且将会是想要自动化扫描任务和报告的系统管理员的完美工具。它还支持nmap脚本输出。
  
[implicit](https://github.com/benfred/implicit)  

用于隐式数据集的快速Python协同过滤。
  
[Object-Detector-App](https://github.com/datitran/Object-Detector-App)  

一个使用谷歌的TensorFlow对象检测API和OpenCV的实时对象识别应用。
  
[pyethereum](https://github.com/ethereum/pyethereum)  

这是Ethereum项目的Python核心库。
  
[netutils-linux](https://github.com/strizhechenko/netutils-linux)  

简化linux网络故障排除和性能调优的实用工具。  
  
[coremltools](https://pythonhosted.org/coremltools/)  

Core ML是一个苹果框架，它允许开发者简单轻松地机器学习（ML）模型到在苹果设备（包括iOS, watchOS, macOS和tvOS）上运行的应用。Core ML为包括深度学习网络（卷积和复现）、带有增强功能的树形组合以及广义线性模型的广泛的ML方法引入了一种公共文件格式 (.mlmodel)。使用这种格式的模型可以通过Xcode直接集成到应用中去。python包中的coremltools用于创建、检查和测试使用.mlmodel格式的模型。
  
[lousy_bitcoins](https://github.com/joarleymoraes/lousy_bitcoins)  

尝试破解从弱密码生成的比特币私钥的实用工具。
  
[GPkit](https://github.com/hoburg/gpkit)   

GPkit是一个Python包，用于定义和操作几何编程模型，抽象出后端求解器。
  
[keras-vis](https://github.com/raghakot/keras-vis)  

keras-vis是用于可视化和调试你训练有素的keras神经网络模型的高级工具包。
  
  
# 近期活动和网络研讨会  
  
[DjangoCon US 2017](https://ti.to/defna/djangocon-us-2017)  

DjangoCon US是社区为社区举办的关于Django网络框架的一个六天国际会议，每年在北美举行。你将会发现来自世界各地的人使用Django构建的各式各样的应用细节，更深入了解你已经熟知的概念，并且发掘使用它们的新方法，玩得开心！
  
[DFW Pythoneers 2017年七月聚会 - Plano, TX](https://www.meetup.com/dfwpython/events/238602894/)

本月安排的演讲是来自Kevin的“将Python代码发布到PyPI”。这个演讲将会涵盖如何使用最近的工具将一个Python版本放到PyPI上，以及最佳实践。
