原文：[Python Weekly - Issue 300](http://eepurl.com/cTwwiH)

---

欢迎来到Python Weekly第300期。本周，让我们直入主题。 
  
# 来自赞助商  
[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/45a64cc4-8d9a-460d-85d2-38c82f745d31.png)](https://www.datadoghq.com/dg/apm/ts-python-performance/?utm_source=Advertisement&utm_medium=Advertisement&utm_campaign=PythonWeekly-Tshirt&utm_content=Python)

[如何监控Python应用](https://www.datadoghq.com/dg/apm/ts-python-performance/?utm_source=Advertisement&utm_medium=Advertisement&utm_campaign=PythonWeekly-Tshirt&utm_content=Python)  

关于实时Python指标的图表和告警，并与你的栈中的150多种其他技术生成的数据相关联。  
 

# 文章，教程和讲座  
  
[Episode #117: 用Coconut实现函数式Python](https://talkpython.fm/episodes/show/117/functional-python-with-coconut)  

Python语言的一个好处是，它至少有3种编程范例：过程式分割、面向对象式分割和函数式分割。本周，你将见到Evan Hubinger，他采用Python的函数式编程风格，并将其转换成11.我们聊到Coconut。这是一种完整的函数式编程语言，它是Python本身的一个合适的超集。
   
[使用Python，Plaid和Twilio，通过SMS检查你的每日支出](https://www.twilio.com/blog/2017/06/check-daily-spending-sms-python-plaid-twilio.html)  

你使用的银行也许会让你为各种触发行为设置短信告警。它甚至还会让你选择通过短信接收定期的支出总结 (我的不是！)。但是，一个跨所有账号的每日支出短信总结怎么样呢？这种总结更难以得到，但是幸运的是，通过将Plaid（一个易于使用的金融服务API）和Twilio短信结合起来，使用一点点Python3，就可以自己弄一个。让我们开始吧！
   
[使用OpenCV和Python的图像差异](http://www.pyimagesearch.com/2017/06/19/image-difference-with-opencv-and-python/)  

在这篇文章中，你将会学习如何使用OpenCV，Python和scikit-image的结构相似性指数 (SSIM)来计算图像差异。基于图像差异，你还将学习如何标记和可视化两张图像中的不同区域。  
   
[Podcast.__init__ 第114集 - 和Jonas Neubert聊聊工业自动化](https://www.podcastinit.com/episode-114-industrial-automation-with-jonas-neubert/)  

我们都是用工厂中生产的物品，但是你有没有过停下来思考驱动生产的代码呢？本周，Jonas Neubert把我们带到幕后，聊聊驱动现代设施的系统和软件，开发工作流，以及如何使用Python来将所有的东东绑在一起的。
   
[Edward和Keras中的随机效应神经网络](http://willwolf.io/2017/06/15/random-effects-neural-networks/)  

本文的目的是为Edward中的贝叶斯模型奠定实际基础，然后探索我们可以如何，以及如何轻松地通过Keras，在经典深度学习的方向上扩展这些模型。它将会给出下面模型的概念概述，以及它们的实现的实际考虑的注意事项 —— 哪些有用，哪些没用。最后，本文将会总结进一步扩展这些模型的具体方式。
   
[深度学习航空影像的语义分割](https://www.azavea.com/blog/2017/05/30/deep-learning-on-aerial-imagery/)  

光栅视野开始于我们对ISPRS提供的航空图像进行语义分割的工作。在这篇文章中，我们会讨论分析这个数据集的方法。我们好ui描述我们使用的主要模型架构，我们在Keras和Tensorflow中是如何实现它们的，并且聊聊使用ISPRS数据运行的各种实验。然后，我们讨论怎样使用Azavea构建的其他开源工具来可视化结果，展示交互式网络地图绘制工具中的深度学习的一些分析，并为未来的工作提供方向。  
   
[神经嵌入式Emojis](http://willwolf.io/2017/06/19/neurally-embedded-emojis/)  
   
[使用Dunder (魔法、特别的) 方法，丰富你的Python类](https://dbader.org/blog/python-dunder-methods)  
   
[最快的可用于生产的图像大小调整就在那里，第0部分](https://blog.uploadcare.com/the-fastest-production-ready-image-resize-out-there-part-0-7c974d520ad9)  
   
[使用Python，Flask和Twilio，为忙碌的父亲宣告新生儿的诞生](https://www.twilio.com/blog/2017/06/birth-announcements-busy-father-python-flask-twilio-sms.html )  
   
[重塑的乐趣](https://www.youtube.com/watch?v=js_0wjzuMfc)  
   
[经验丰富的python程序员：有哪些python的标准特性你仍未经常使用？](https://www.reddit.com/r/Python/comments/6i829l/experienced_python_programmers_are_there_any/)   
  
  
# 本周的Python工作  
  
[Omaze招聘高级后端工程师](http://jobs.pythonweekly.com/jobs/senior-back-end-engineer-2/)  

Omaze的API团队正在寻找厉害的工程师人才，以助益我们在2017,2018以及之后的时间里的成长。我们拥有有趣的难题以及巨大的野心。我们需要出色的人来帮助我们构建一些棒极的东西。Omaze的高级软件工程师是特别的。你经验丰富，富有智慧；你技术棒棒，但也与人协作良好；你为自己的技能感到骄傲，但也能带领团队人员走向成功。你有很高的标准。你喜爱发布。并且你对即将使用你对才能来有意义地帮助其他人而感到兴奋不已。

  
# 好玩的项目，工具和库  
  
[tensor2tensor](https://github.com/tensorflow/tensor2tensor)  

用于TensorFlow的监督学习的一个模块化可扩展库和二进制文件，带对序列任务的支持。
   
[Dash](https://github.com/plotly/dash)  

纯python的交互式响应web应用
   
[IFFSE](https://github.com/kendricktan/iffse)  

Instagram面部特性搜索引擎。
   
[goSecure](https://github.com/iadgov/goSecure)  

一个易于使用的便携式虚拟专用网 (VPN) 系统，使用Linux和一个树莓派进行构建。
  
[LemonGraph](https://github.com/NationalSecurityAgency/lemongraph)  

LemonGraph是一个基于日志的事务图 (节点/边/属性) 数据库引擎，由单个文件支撑。其主要使用场景是支持流式种子集扩展。这个图库的核心部分由C编写，而Python (2.x)层则增加了友好的绑定，查询语言和一个REST服务。
   
[html5-parser](https://github.com/kovidgoyal/html5-parser)  

一个快速、符合标准、基于C的python HTML 5解析器。比诸如html5lib的基于纯python的解释器快超过30倍。
   
[whitepy](https://github.com/yasn77/whitepy)  

用Python3写的空白解释器。
   
[django-wedding-website](https://github.com/czue/django-wedding-website)  

一个由django驱动的婚礼网站和客人管理系统。
   
[transformer](https://github.com/Kyubyong/transformer)  

转换器的一种TensorFlow实现：仅需关注。
   
[django-admin-env-notice](https://github.com/dizballanze/django-admin-env-notice)  

Django Admin中的可视化区分环境。
   
[Nash](https://github.com/drvinceknight/Nashpy)  

一个用于计算2人策略游戏的平衡的python库。
   
[salt-scanner](https://github.com/0x4D31/salt-scanner )  

Linux漏洞扫描程序，基于Salt Open和Vulners审计API，带Slack通知和JIRA集成。
   
  
# 最新发布  
  
[Python 3.6.2rc1](http://blog.python.org/2017/06/python-362rc1-is-now-available-for.html)  
   
[Jython 2.7.1 发布候选版本3](https://fwierzbicki.blogspot.nl/2017/06/jython-271-release-candidate-3-released.html)   
   
  
# 近期活动和网络研讨会  
  
[SoCal Python 2017年六月聚会 -  Santa Monica, CA](https://www.meetup.com/socalpython/events/240587429/)    

将会有以下演讲：

  * 更好地循环：更深入了解Python中的迭代
  * 当一切被自动化时，我们做什么

   
[San Francisco Django2017年六月聚会 - San Francisco, CA](https://www.meetup.com/The-San-Francisco-Django-Meetup-Group/events/240092699/)  

将会有以下演讲：

  * Django从小白到大牛
  * GIS，Django，数据科学和农业



   
[London Python 2017年六月聚会 - London, UK](https://www.meetup.com/LondonPython/events/240079951/)  

将会有一个演讲 —— 用Python构建一个医疗聊天机器人时学到的经验教训。
  


