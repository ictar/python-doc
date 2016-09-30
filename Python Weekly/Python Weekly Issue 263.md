原文：[Python Weekly - Issue 263](http://eepurl.com/chOyQj)

---

欢迎来到Python Weekly第263期。本周，我想要感谢我们的赞助商DataCamp。快来看看他们提供的精彩内容吧。
  
  
# 来自赞助商   

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/601133ee-e881-45b5-a5db-700acabbcc2f.png)](https://www.datacamp.com/) 

因**数据科学**之故，对学习**Python**感兴趣，是吗？通过进行100次互动编码挑战，以及观看由顶级导师提供的视频，从而成为数据专家吧。[加入](https://www.datacamp.com/) 500,000+个小伙伴，今天，就开始在[DataCamp](https://www.datacamp.com/)线上学习吧！


# 文章，教程和讲座 
  
[用Python进行股票市场数据分析概述 (第二部分)](https://ntguardian.wordpress.com/2016/09/26/introduction-stock-market-data-python-2/)  

这篇文章是使用Python进行股票数据分析系列的两部分中的第二部分。它讨论的主题包括划分一个移动平均交叉策略，回溯测试和基准，以及一些供读者思考的实际问题。
  
[Pyflame: Uber工程的Python追踪分析器](http://eng.uber.com/pyflame/)  

在Uber，我们努力写出高效的后端服务，以保证我们的计算成本低。随着我们业务的增长，这变得越来越重要；看起来小的低效在Uber的规模下被大大放大。我们发现了flame graph(火焰图)是一个了解我们服务的CPU和内存性能的有效工具，并且我们用它们来大大提高了我们Go和JavaScript服务的效率。为了获取Python服务的高质量火焰图，我们编写了一个名为Pyflame的高性能分析工具，用C++实现。本文，我们讨论设计考虑和一些让Pyflame成为分析Python代码的更好替代方案的独特实现特性。
  
[与C共舞](https://www.paypal-engineering.com/2016/09/22/python-by-the-c-side/)  

全世界都是遗留代码，而且总会有另一个，更低的层次剥离。这些现实使得世界各地的开发者定期朝圣，从Python大陆到C海岸。从zlib到SQLite再到OpenSSL，是否追求速度、效率或者功能，海是强大的，它时常波涛汹涌。好消息是，当你在写Python的时候，你可以在沙滩上玩一天C交互。
  
[Episode #77: 你没有（但应该）使用的20个Python库](https://talkpython.fm/episodes/show/77/20-python-libraries-you-aren-t-using-but-should)  

本周，你将见到Caleb Hattingh，他写了一本很棒的书，叫做你没有（但应该）使用的20个Python库（20 Python Libraries You Aren't Using (But Should)）。他和我花了一个小时来挖掘所有非常强大和有趣的包，你可能从未听说过它们，但是在你了解它们之后，你会超级兴奋地去使用它们。
  
[如何为Flask Web应用配置NGINX](http://www.patricksoftwareblog.com/how-to-configure-nginx-for-a-flask-web-application/)  

这篇文章解释了NGINX是什么，以及如何对它进行配置，以服务一个Flask web应用。我发现了许多关于NGINX以及如何配置它的文档，但是我想深入NGINX如何在Flask web应用中使用以及如何配置的细节。我发现NGINX的配置有点让人糊涂，因为许多文档只是简单展示配置文件，而不解释每一步的细节。希望这篇博文提供了为你的应用配置NGINX的清晰细节。
  
[Podcast.__init__ 第76集 - 和Jonathan Peirce聊聊PsychoPy](https://podcastinit.com/jonathan-peirce-psychopy.html)  

本周，在Podcast.__init__，我们和Jonathan Peirce钻研你的心灵的复杂运作。他告诉我们他是如何开始PsychoPy项目的，以及多年来，这个项目是如何变得实用且流行。我们讨论了它在大量心理学实验中使用的方法，如何设计和执行这些实验，以及未来有什么在等着它。
  
[异步Python](https://hackernoon.com/asynchronous-python-45df84b82434)

Python中的异步编程最近越来越受欢迎。Python中有很多不同的进行异步编程的库。其中之一就是asyncio，它是在Python 3.4添加的一个python标准库。asyncio是Python中异步编程变得更加流行的部分原因。本文将解释什么是异步编程，并且比较一些异步编程库。让我们走过历史，看看异步编程在Python中是如何发展的。
  
[用于文本处理的解析和可视化神经网络](https://civisanalytics.com/blog/data-science/2016/09/22/neural-network-visualization/)  
  
[PyData Chicago 2016视频集](https://www.youtube.com/playlist?list=PLGVZCDnMOq0pMUpVnQ8h5byVzIJNZCo2S)  
  
  
# 好玩的项目，工具和库 
  
[finmarketpy](https://github.com/cuemacro/finmarketpy)  

finmarketpy是一个基于Python的库，它让你能够分析市场数据，还能通过简单易用的API来回溯测试交易策略，API为你提供了预置的模板来定义回溯测试。
  
[Web-PDB](https://github.com/romanvm/python-web-pdb)  

Web-PDB是一个用于Python内置PDB调试器的web界面。它允许我们在web浏览器中远程调试Python脚本。
  
[Neural Photo Editor](https://github.com/ajbrock/Neural-Photo-Editor)  

一个使用生成神经完了编辑自然照片的简单界面。 
  
[whereami](https://github.com/kootenpv/whereami)  

使用WiFi信号和机器学习来预测你的所在地
  
[facegen](https://github.com/zo7/facegen)  

使用反卷积网络生成人脸。
  
[CommAI-env](https://github.com/facebookresearch/CommAI-env)  

一个开发在[面向机器智能的路线图](http://arxiv.org/abs/1511.08130)中描述的AI系统的平台  
  
[django-perf-rec](https://github.com/YPlan/django-perf-rec)  

保持详细记录你的Django代码的性能。
  
[traces](https://github.com/datascopeanalytics/traces)  

一个用于不均匀间隔时间序列分析的Python库。
  
[django-map-widgets](https://github.com/erdem/django-map-widgets) 

用于Django PostGIS域的可配置、可插拔，以及更友好的地图控件。
  
  
# 最新发布
  
[Django安全版本发布：1.9.10和1.8.15](https://www.djangoproject.com/weblog/2016/sep/26/security-releases/)  
  
[Spyder IDE 3.0.0](https://github.com/spyder-ide/spyder/releases/tag/v3.0.0)  
  
