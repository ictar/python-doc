原文：[Python Weekly - Issue 308](http://eepurl.com/cZXdKD)

---

欢迎来到Python周刊第308期。让我们直奔主题。
  
# 来自赞助商

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/45a64cc4-8d9a-460d-85d2-38c82f745d31.png)](https://www.datadoghq.com/dg/apm/ts-python-performance/?utm_source=Advertisement&utm_medium=Advertisement&utm_campaign=PythonWeekly-Tshirt&utm_content=Python)

[Python应用跑得太慢了？](https://www.datadoghq.com/dg/apm/ts-python-performance/?utm_source=Advertisement&utm_medium=Advertisement&utm_campaign=PythonWeekly-Tshirt&utm_content=Python)

用Datadog跟踪每一个请求吧！使用端到端跟踪，快速定位你的引用中的Python性能问题。
  
  
# 新闻  
  
[支持良好合作关系：PyCharm和Django再次合作](https://www.djangoproject.com/weblog/2017/aug/15/support-dsf-2017/)

去年 (2016) 六月，JetBrains PyCharm和Django软件基金会合作，大力推动了Django筹款。该活动取得了巨大的成功。我们一起为Django软件基金会筹集了5万美元！今天，我们希望可以复制那些成功。在两周的活动中，利用一个具有30%折扣的折扣码购买一个新的PyCharm专业版个人许可证，所筹款项将全部用于DSF的一般筹款活动和Django奖学金计划。  
  
[PEP 550 —— 执行上下文](https://www.python.org/dev/peps/pep-0550/)

这个PEP提出了一种管理执行状态的新机制 —— 在其中执行函数、线程、生成器或者协程的逻辑环境。
  
  
# 文章，教程和讲座  
  
[Dogs vs. Cats：在Python中，使用TensorFlow，进行深度学习的图像分类](https://sandipanweb.wordpress.com/2017/08/13/dogs-vs-cats-image-classification-with-deep-learning-using-tensorflow-in-python/)  

给定一组猫和狗的已标记图像，将学习到一个机器学习模型，接着，将用它来把一组新的图像分类为猫或狗。这个问题出现在Kaggle比赛中，而图像来自于这个kaggle数据集。
  
[PyTorch vs TensorFlow —— 发现差异](https://medium.com/towards-data-science/pytorch-vs-tensorflow-spotting-the-difference-25c75777377b)

这篇文章探讨了两个流行的深度学习框架（PyTorch和TensorFlow）之间的一些关键的相似点和差异。
  
[在Python中解包嵌套数据结构](https://dbader.org/blog/python-nested-unpacking)

一篇关于Python的高级数据解包功能的教程：如何使用“=”运算符和for循环来解压缩数据。
  
[利用CoreML来使用预测模型](https://blog.pusher.com/using-prediction-models-coreml/) 

最近，Apple发布了Core ML，这是一个用于集成机器学习模型到任意iOS应用的新框架，从而可以在设备上进行预测，而无需使用任何外部服务。你可以使用来自诸如Caffe，Keras和scikit-learn这样的框架的已训练模型，并使用Apple提供的Python库coremltools，你可以将这些模型转换成CoreML格式。在这个教程中，我们将回顾一下使用scikit-learn创建一个预测模型，将其转换成Core ML格式，并把它集成到一个应用中的过程。
  
[使用Python, GeoJSON和GeoPandas，进行地理空间分析的入门指南](https://www.twilio.com/blog/2017/08/geospatial-analysis-python-geojson-geopandas.html)  

在这篇教程中，我们将会使用Python来学习获取地理空间数据、处理以及可视化这些数据的基础知识。更具体地说，我们将进行一些美国的交互式可视化！
  
[Django中的整洁架构](https://engineering.21buttons.com/clean-architecture-in-django-d326a4ab86a9)

本文将试图解释应用整洁架构到Django Restful API上的方法。
  
[Python多线程](https://www.youtube.com/playlist?list=PLGKQkV4guDKEv1DoK4LYdo2ZPLo6cyLbm)  

涵盖Python多线程的视频系列，目标人群是初学者。
   
[我们是如何用一个简单的函数替换掉几十个测试装备的](https://medium.com/@hakibenita/how-we-replaced-dozens-of-test-fixtures-with-one-simple-function-bac73bfc277d)  
  
[TensorFlow排队和线程介绍](http://adventuresinmachinelearning.com/introduction-tensorflow-queuing/)  
  
[Django Service对象](http://mitchel.me/2017/django-service-objects/)  
  
[让我们删掉全局解释锁（GIL）](https://morepypy.blogspot.com/2017/08/lets-remove-global-interpreter-lock.html)   
  
  
# 本周的Python工作  
  
[Factmata招聘高级后端和数据科学工程师](http://jobs.pythonweekly.com/jobs/senior-backend-and-data-science-engineer/)

你的工作是构建Factmata的后端。你必须每天都考虑我们要如何获得并分析大量的社交和传统媒体数据，如何扩大和建立面向客户的产品。你将成为创始软件和NLP工程师组成的平台团队的一员，来一起创建Factmata平台。这是一个加入位于NLP、ML、数据科学和软件工程前沿的早起团队的很好的机会。
  
  
# 好玩的项目，工具和库  
  
[pysc2](https://github.com/deepmind/pysc2)  

PySC2是DeepMind的“星际争霸II学习环境(SC2LE)”的Python组件。它将暴雪娱乐公司的星际争霸II机器学习API作为一个Python RL环境公开。
  
[DroidBot](https://github.com/honeynet/droidbot)  

一个Android轻量级测试输入生成器。类似于Monkey，单更具智能和酷酷的功能！
  
[Torrench](https://github.com/kryptxy/torrench/)   

命令行torrent搜索工具。
  
[Spaghetti](https://github.com/m4ll0k/Spaghetti)   

Spaghetti是一个web应用安全扫描工具。它被设计来发现各种默认和不安全的文件、配置和配置错误。
  
[abootool](https://github.com/alephsecurity/abootool)  

基于静态知识的动态发现隐藏的fastboot OEM命令的简单工具。
  
[mistletoe](https://github.com/miyuchina/mistletoe)   

mistletoe是一个纯Python的Markdown解析器，旨在快速、模块化和完全可定制。
  
[nider](https://github.com/pythad/nider)  

添加文本到图像、纹理和不同背景中的Python包。
  
[ChainerCV](https://github.com/chainer/chainercv)  

ChainerCV是一个使用Chainer，为计算机视觉任务训练和运行神经网络的工具集。
  
[TemPy](https://github.com/Hrabal/TemPy)  

Python中快速的面向对象HTML模板！

  
# 近期活动和网络研讨会  
  
[Boston Python Meetup August 2017 - Cambridge, MA](https://www.meetup.com/bostonpython/events/240446981/)  

将会有以下讲座：

  * Virtualenv
  * Items vs Attributes

  
[San Diego Python 2017年8月聚会 - San Diego, CA](https://www.meetup.com/pythonsd/events/241076005/)  

将会有以下讲座：
  * NumPy简介
  * 一个快速（免费！）的请愿web应用，使用Django + Heroku

  

 

