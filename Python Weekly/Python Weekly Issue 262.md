原文：[Python Weekly - Issue 262](http://eepurl.com/cgPbe9)
---
  
欢迎来到Python周刊第262期。
  
# 来自赞助商  

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/7394541b-6b55-4fde-8756-6b7547029f1b.png)](https://hired.com/?utm_source=newsletters&utm_medium=pythonweekly&utm_campaign=q3-16) 

为什么还用老方法找工作？[试试Hired吧](https://hired.com/?utm_source=newsletters&utm_medium=pythonweekly&utm_campaign=q3-16)，把你的申请放在4,000+公司面前。没有爱出风头的招聘人员，没有无穷无尽的申请和不匹配的公司，Hired把权力交到你手里。获得前期薪水、股票以及个性化支持，帮助你找到理想的工作。  
  
# 文章，教程和讲座
  
[用Python进行股票市场数据分析概述 (第一部分)](https://ntguardian.wordpress.com/2016/09/19/introduction-stock-market-data-python-1/)  

这篇文章是使用Python进行股票数据分析系列的两部分中的第一个部分，基于我在Utah大学为MATH 3900（数据科学）课题提供的一个讲座。在这些文章中，我会讨论到基础知识，例如使用pandas从Yahoo! Finance获取数据，可视化股票数据，移动均值，制定一个移动平均交叉策略，回测和基准。最后的一篇文章会包含实际问题。这第一篇文章讨论的主题到介绍移动均值。
  
[如何用100刀和TensorFlow构一个能“看”的机器人](https://www.oreilly.com/learning/how-to-build-a-robot-that-sees-with-100-and-tensorflow)  

深度学习、廉价硬件和目标识别大冒险。
  
[压缩和增强手写笔记](https://mzucker.github.io/2016/09/20/noteshrink.html)  | [中文版](../Image Processing/压缩和增强手写笔记.md)

这篇文章将向你展示如何写个程序来清理手写笔记扫描件，同时减少文件大小。
  
[Episode #76: 可再生Python](https://talkpython.fm/episodes/show/76/renewable-python)  

即使你的屋顶上用了太阳能电池板，但是也有可能你的家仍然用化石燃料提供动力。气候创新者和Python开发者Anna Schneider正在她的公司WattTime尝试改变这种现状。在这一集中，我们讨论关于Python是如何驱动WattTime的，一些关注于可再生能源的流行开源项目，以及其他一些基于Python的可再生能源初创公司。
  
[Podcast.__init__ 第75集 - 和Asheesh Laroia聊聊Sandstorm.io](https://podcastinit.com/asheesh-laroia-sandstorm.html)  

Sandstorm.io是一个创新平台，旨在让自托管应用对于普通人来说更容易，更好维护。本周，我们和Asheesh Laroia聊聊关于为什么运行你自己的服务是可取的，他们如何将安全当成第一要务，如何架构Sandstorm，以及安装过程是什么样子的。
  
[用Theano来训练神经网络](http://blog.asidatascience.com/training-neural-networks-with-theano/)  
 
训练神经网络涉及不少棘手的东西。我们试图使一切清晰且易于理解，让你尽可能快地训练你的神经网络。Theano允许我们编写遵循基本数学结构的相对简洁的代码。 
  
[Python包生态圈](http://www.curiousefficiency.org/posts/2016/09/python-packaging-ecosystem.html)  

最近已经有一些文章从最终用户的角度反映Python包生态圈的现状，因此，作为该生态圈的主架构师之一，对我来说，值得从我的角度写写我如何描述软件出版发行的整体问题空间，此刻我认为我们所处的境地，以及我所希望看到的未来的发展。
  
[使用Python，分析23AndMe数据，获取遗传起源](http://online.cambridgecoding.com/notebooks/cca_admin/genetic-ancestry-analysis-python)  

你的DNA包含了关于你的主线，易患疾病以及复杂特性，包括身高、体重、五官和行为等丰富的信息。使用来自23andMe，一家直接面向消费者的遗传学公司，的公众可获取数据，我们将展示如何确定在网上找到的来自23andMe的一份匿名样本的祖先。
  
[创建准备好发布的Python Notebook](http://blog.juliusschulz.de/blog/ultimate-ipython-notebook)  

Python的notebook功能提供了一种在同个地方分析数据和写报告的非常了不起的方式。然而，在标准配置中，Python notebook的pdf导出有点丑，并且不实用。下面，我将介绍我从IPython/Jupyter notebook中创建几乎准备好发布的报告的方法。
  
[Python asyncio备忘单](https://github.com/crazyguitar/pysheeet/blob/master/docs/notes/python-asyncio.rst)  
  
[无需任意窗口的事件频率分析](http://databozo.posthaven.com/deep-in-the-weeds-event-frequency-analysis-without-arbitrary-windows)  
  
[PyBay2016视频集](https://www.youtube.com/watch?v=voXVTjwnn-U&list=PL85KuAjbN_gtGn4v1ELSWJlTFZF_5Ciog)  
  
[Python中的图像处理](https://www.codementor.io/python/tutorial/image-manipulation-in-python)  
  
[我使用Python调试器的坑爹经历](https://benbernardblog.com/my-startling-encounter-with-python-debuggers/)  
  
[让我们构建一个简单的解释器。第11部分](https://ruslanspivak.com/lsbasi-part11/)  
  
[使用pkgsrc部署现代Python应用到古老的基础设施上](http://pythonsweetness.tumblr.com/post/150466265417/deploying-modern-python-apps-to-ancient)  
  
  
# 好玩的项目，工具和库  
  
[asynq](https://github.com/quora/asynq)  

asynq是一个用于在Python中异步编程的库，关注于对外部服务的批量请求。它还提供与同步代码的无缝互操作，支持异步上下文管理，以及提供让编写和测试异步代码更容易的工具。asynq是在Quora开发的，并且是Quora架构的一个核心组件。
  
[Flowblade](https://github.com/jliljebl/flowblade)  

Flowblade是一个用于Linux的多轨非线性视频编辑器。Flowblade提供强大的工具来混合和过滤视频和音频。
  
[VR Zero](https://github.com/WayneKeenan/python-vrzero)  

树莓派上的VR开发，用Python实现。
  
[Motorway ](https://github.com/plecto/motorway) 

Motorway是一个实时的数据管道，就像Apache Storm，但是是用Python写的。
  
[Waldo](https://github.com/anfederico/Waldo)  

设计来识别和监控股票动向的社会/历史线索的软件。
  
[SQLiScanner](https://github.com/0xbug/SQLiScanner)  

使用Charles和sqlmapapi进行自动化SQL注入。
  
[simulacrum](https://github.com/jbrambleDC/simulacrum)  

Simulacrum是带列名和对应数据类型床底字典对象，然后输出一个具有随机数据的pandas DataFrame的一种简单方式。这对创建一个假的数据集，或者测试一个需要测试通过概况的有效性的数据科学脚本。
  
[fast-wavenet](https://github.com/tomlepaine/fast-wavenet)  

Wavenet生成的高效实现。
  
[Yellowbrick](https://github.com/DistrictDataLabs/yellowbrick)  

Yellowbrick 是可视化分析和诊断工具的套件，帮助你为机器学习进行特征选择、模型选择和参数调整。所有的可视化都是用Matplotlib生成的。
  
[packyou](https://github.com/llazzaro/packyou)  

从github上下载一个python项目，然后允许从任何地方导入它。当你下载的repo不是一个包时，它非常有用。
  
[PyPattyrn](https://github.com/tylerlaberge/PyPattyrn) 

PyPattyrn是一个Python包，旨在更容易更快地实现你自己项目的设计模式。
  
  
# 近期活动和网络研讨会 
  
[在线活动：学习Django：你希望你知道什么？](https://www.crowdcast.io/e/learning-django/register)  
犯错误是学习的好方法，但有些错误让人觉得痛苦。如果你是一个新手Django开发者，希望从别人的错误中吸取教训，那么跟Melanie Crutchfield和我一起加入到这个聊天中吧，我们将讨论那些在你用Django制作第一个网站的适合，你希望你能早点知道的事。  
  
[Python Web开发者之夜#29 - Minneapolis, MN](https://www.meetup.com/PyMNtos-Twin-Cities-Python-User-Group/events/233522646/)  
  