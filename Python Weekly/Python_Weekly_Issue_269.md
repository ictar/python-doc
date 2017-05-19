原文：[Python Weekly Issue 269](http://eepurl.com/cn3QCX)

---
  
欢迎来到Python周刊第269期。让我们直奔主题。
  
# 来自赞助商

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/b1a844b2-fee2-4dd3-b56c-341147f4c21b.png)](https://software.intel.com/en-us/intel-sdp-home)

参加SC'16？注册[Intel HPCDevCon](http://www.intel.com/content/www/us/en/events/hpcdevcon/overview.html) 以及全天关于 [用于科学计算的高性能Python](http://sc16.supercomputing.org/presentation/?id=tut172&sess=sess193)的SC'16教程。参加我们的演讲，展会以及专家访谈，学习可以如何从我们为Python设计的性能优化工具受益。

  
# 文章，教程和讲座 
  
[Texas死囚数据探索](http://signal-to-noise.xyz/texas-death-row.html)  

在[德州刑事司法部门](http://www.tdcj.state.tx.us)网站上，有一个页面，上面列举了所有自1982年起被执行死刑的人，恢复死刑的时间，以及他们的最后陈述。在这个项目中，我们将探索该数据，并且看看是否可以应用主题模型到陈述中。
  
[通过Chainer，让复杂神经网络变得轻松起来](https://www.oreilly.com/learning/complex-neural-networks-made-easy-by-chainer)  

Chainer是一个开源框架，专为高效率研究和深度学习算法的发展而设。在这篇文章中，我们简单通过几个例子介绍了Chainer，并将其与其他框架，例如Caffe,
Theano, Torch, 和Tensorflow，进行对比。  
  
[Pandas教程：Python中的DataFrame](https://www.datacamp.com/community/tutorials/pandas-tutorial-dataframe-python)  

本教程介绍了11种最流行的Pandas DataFrame问题，从而让你理解，以及避免，那些已经走在你之前的Python人的疑惑。
  
[OSMnx: 用于街道网络的Python包](http://geoffboeing.com/2016/11/osmnx-python-street-networks/)  

OSMnx是一个Python包，用于从OpenStreetMap下载行政边界形状和街道网络。它让你能够轻松地在Python中利用NetworkX构造、设计、可视化以及分析复杂的街道网络。仅需一行Python代码，你就可以获得一个城市或者街区的步行、驾驶或者自行车网络。然后，你可以简单地可视化死胡同或者单向街道，绘制最短路径路线，或者计算统计数据，例如路口密度，平均节点连接，或者中介中心性。 
  
[离Django的GenericForeignKey远点](http://lukeplant.me.uk/blog/posts/avoid-django-genericforeignkey/)  

在Django中GenericForeignKey是这样的一个特性，它允许一个模型与系统中的其他模型相关联，与与特定的模型相关联的ForeignKey相对。这篇文章是关于为什么通常有时你应该离GenericForeignKey远点。
  
[利用LightFM，探索Learning to Rank Sketchfab模型](http://blog.ethanrosenthal.com/2016/11/07/implicit-mf-part-2/)  

在这片文章中，我们将在上一篇介绍隐式矩阵分解的文章之后，做一堆很酷的事。我们将探索
Learning to Rank，它是一个不同的隐式矩阵分解的方法，然后使用LightFM库来将边信息合并到我们的推荐系统中。接下来，我们会使用比网格搜索更智能的scikit-optimize来进行交叉验证超参数。最后，我们会看到，我们可以超越简单的用户到项和项到项推荐，现在，我们已经将边信息嵌入到yu与我们到用户和项一致到空间中了。
  
[后异步/等待世界中，关于异步API设计的一些思考](https://vorpus.org/blog/some-thoughts-on-asynchronous-api-design-in-a-post-asyncawait-world/)  
  
[分析Berkeley的住房价格](http://aakashjapi.com/housing-prices-in-berkeley/)  
  
[Celery 4.0新特性](http://docs.celeryproject.org/en/latest/whatsnew-4.0.html)  
  
[为了乐趣与获利的Python中的计时测试](https://hackernoon.com/timing-tests-in-python-for-fun-and-profit-1663144571)  
  
[用Python实现Raspberry Pi条码扫描仪](https://medium.com/@yushulx/raspberry-pi-barcode-scanner-in-python-927839100c6b)  
  
  
# 好玩的项目，工具和库   
  
[stack-overflow-import](https://github.com/drathier/stack-overflow-import)  

从Stack Overflow中把任意代码当成Python模块导入。
  
[Python cheatsheet](https://www.pythonsheets.com/) 

该工程试图提供大量的让生活更轻松的Python代码片段。
  
[The Knowledge Repository](https://github.com/airbnb/knowledge-repo) 

为数据科学家和其他技术专家提供的下一代整理知识共享平台。

[PyFME](https://github.com/AeroPython/PyFME)  

PyFME是Python Flight Mechanics Engine（Python飞行力学引擎）的简称。PyFME背后的目的是构建一个库，它能模拟飞行器在空中的动作，以及进行飞行中所有涉及的物理学建模。
  
[Rewrite](https://github.com/kaonashi-tyc/Rewrite) 

汉字的神经式转换。
  
[Kappa](https://github.com/garnaat/kappa)

Kappa是一个致力于让部署、更新和测试AWS Lambda函数更简单的命令行工具。
  
[JamDocs](http://jam-py.com/jamdocs/)  

JamDocs是一个用于Sphinx文档生成的Jam.py web界面。
  
[Pyonic-interpreter](https://github.com/inclement/Pyonic-interpreter)  

Android上的Python解释器。  
  
[Colorful-Image-Colorization](https://github.com/cameronfabbri/Colorful-Image-Colorization)  

一种用以对图像着色的深度学习方法。
  
  
# 近期活动和网络研讨会 
  
[Numba - 加速你的Python原来是辣么简单！ - New York, NY](https://www.meetup.com/NYDataScientists/events/235403768/)  
  
[Edmonton Python 2016年11月聚会 - Edmonton, AB](https://www.meetup.com/startupedmonton/events/234340991/)  
  
[PyHou 2016年聚会 - Houston, TX](https://www.meetup.com/python-14/events/234195007/)  
 