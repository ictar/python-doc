原文：[Python Weekly Issue 279](http://eepurl.com/cy-5dH)

---
  
欢迎来到Python周刊第279期。本周，我想要感谢我们的赞助商，PyImageSearch的支持。一定要利用他们令人惊奇的产品。
  
# 来自赞助商 

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/ae6ac722-863b-4d3a-a227-9fa3517e252f.png)](https://www.kickstarter.com/projects/adrianrosebrock/1866482244)

[Kickstarter: 用Python进行计算机视觉深度学习（Kickstarter: Deep Learning for Computer Vision with Python）](https://www.kickstarter.com/projects/adrianrosebrock/1866482244) 

在这本来自PyImageSearch.com的新书中，你将会找到超级实用的演练，实践教程和一种非BS教学方式，保证帮你掌握计算机视觉的深度学习。要了解更多信息，[仅需点击这里](https://www.kickstarter.com/projects/adrianrosebrock/1866482244)。


# 文章，教程和讲座
  
[和Matthew Rocklin聊聊Dask](https://www.dataengineeringpodcast.com/episode-2-dask-with-matthew-rocklin/)  

处理和分析数据有大量的工具和平台。在这一集中，Matthew Rocklin谈到Dask是如何填补面向任务的工具流任务和内存处理框架之间的差距的，以及它如何使用Python之力对大数据问题产生影响的。

[实用PyTorch：使用字符级RNN分类名字](https://github.com/spro/practical-pytorch/blob/master/char-rnn-classification/char-rnn-classification.ipynb)  

我们将构建和训练一个基本的字符级RNN来分类单词。一个字符级RNN将单词作为一系列字符读取 - 每步都输出预测和“隐藏状态”，将它前一个隐藏状态输入到下一步中。我们将最终的预测作为输出，即单词所属的类别。

[使用OpenCV, Python, 和scikit-image的seam carving](http://www.pyimagesearch.com/2017/01/23/seam-carving-with-opencv-python-and-scikit-image/)  

这篇文章讨论了seam carving算法，它是如何工作的，以及如何使用Python, OpenCV, 和sickit-image来应用seam carving。

[Podcast.__init__ 第93集 - 和Paul Kehrer聊聊Cryptography](https://www.podcastinit.com/episode-93-cryptography-with-paul-kehrer/)  

迟早你会需要加密或者哈希一些数据。幸好我们有Cryptography库，以及其他一些由Python Cryptographic Authority维护的项目，来确保加密正确完成。在这一集中，Paul Kehrer谈到了PyCA是如何开始的，他们维护的项目，以及现今，你可以如何开始在你的程序中使用cryptography。
  
[如何在Python中自动记录Twitch流](https://www.junian.net/2017/01/how-to-record-twitch-streams.html) 

借助streamlink和ffmpeg，可以在每次用户上线的时候记录twitch流。
  
[Flask中的基于token的认证](https://realpython.com/blog/python/token-based-authentication-with-flask/)  

本教程使用一种测试优先方法，利用JSON网络令牌 (JWTs)，在Flask应用中实现基于token的认证。

[编写生产级PySpark招聘信息库的最佳实践](https://developerzen.com/best-practices-writing-production-grade-pyspark-jobs-cb688ac4d20f)  

如何构建你的PySpark招聘信息库和代码。

[使用全卷积网络 (FCNs) 进行图像分割](http://warmspringwinds.github.io/tensorflow/tf-slim/2017/01/23/fully-convolutional-networks-\(fcns\)-for-image-segmentation/)  

这篇文章展示了如何使用利用我们的框架在PASCAL VOC上训练的全卷积网络，进行图像分割。  
  
[Pyramid 1.8新特性](http://docs.pylonsproject.org/projects/pyramid/en/latest/whatsnew-1.8.html)  

本文解释了相较于Pyramid 1.7，Pyramid 1.8的新特性。它还记录了这两个版本之间的向后不兼容性，和添加到Pyramid 1.8点弃用特性，以及软件依赖项变更和显著的文档增加。

[使用Dask，在一个集群之上自定义并行算法](http://matthewrocklin.com/blog/work/2017/01/24/dask-custom)  

本文将Dask描述成一个适用于诸如Hadoop/Spark这样的大数据计算框架和诸如Airflow/Celery/Luigi之类的任务调度器之间某处等计算任务调度器。我们看到，通过结合来自这些类型的系统的元素，Dask如何能够特别好地处理复杂数据科学问题。
  
[使用Pytest来测试Python应用](https://semaphoreci.com/community/tutorials/testing-python-applications-with-pytest)  

Pytest的简单易用使得它在Python测试工具中脱颖而出。这个教程将会让你开始使用pytest来测试你的下一个Python项目。
  
[Python中进行字符串格式化的4大方法](https://dbader.org/blog/python-string-formatting)  

本文演示了四个字符串格式化方法如何工作，以及它们各自的优势和劣势是什么。它还提供了作者如何选取最好的通用字符串格式化方法等简单“经验法则”。
  
[使用Blender和Python来3D打印一条裙子](https://opensource.com/article/16/12/blender-python-3D-dress)  
  
[Wolfram自动机，Python的一个简单实现](http://faingezicht.com/articles/2017/01/23/wolfram/)  
  
[在Sublime Text上lint Python 3.6的三个步骤](https://janikarhunen.fi/three-steps-to-lint-python-3-6-in-sublime-text.html)  
  
[在PyPy上运行Numba](http://www.embecosm.com/2017/01/19/running-numba-on-pypy/)  
  
[在API客户端中使用Requests的一些最佳实践](http://www.coglib.com/~icordasc/blog/2017/01/some-better-practices-for-using-requests-in-api-clients.html)  
  
  
# 本周Python工作
  
[Patch招聘首席技术官](http://jobs.pythonweekly.com/jobs/chief-technology-officer/)  

Patch正在招聘一名CTO / 首席程序员。作为扩展公司的一部分，我们正在扩大我们的技术团队。这是一个对我们的电子商务平台产生巨大影响，以及帮助塑造我们正在创建的新服务的机会。我们正在寻找能把事情做到的人。我们是一个以客户为中心的公司，因此期望那些为制作伟大的最终产品，以及编写高质量代码的人可以加入。成为早期技术团队中的一员，你将有机会随着公司的成长，塑造你在公司中的角色。
  
[Texas Tribune招聘软件工程师](http://jobs.pythonweekly.com/jobs/software-engineer-16/) 

你将成为不断提高我们的新闻编辑室系统（包括一个Django CMS以及它与外部API的交互），以及原型化新工具的团队的一部分。我们使用现代化的工作流 (包括Docker, HTTPS, 和Webpack)，而你将始终面临新的挑战。
  
  

# 好玩的项目，工具和库
  
[pipenv](https://github.com/kennethreitz/pipenv)  

Pipenv是一个实验项目，旨在把所有包世界中的最好的东西带进Python世界。它将Pipfile, pip, 和virtualenv整合到单个工具链中。它具有非常漂亮的终端颜色。

[SKiDL](https://xesscorp.github.io/skidl/docs/_site/index.html)  

永不再使用一个糟糕的图解编辑器！SKiDL是一个简单的模块，它让你使用Python描述电子电路。生成的Python程序输出一个网表，让PCB布局工具用来生成一个完成的电路板。
  
[Notebook Gallery](http://nb.bianp.net/)  

到最佳IPython和Jupyter Notebook的链接。
  
[Cage](https://github.com/macostea/cage)  

在干净的Docker环境中开发和运行你的Python应用。
  
[nbtutor](https://github.com/lgpage/nbtutor)  

在Jupyter Notebook格子里可视化Python代码执行 (逐行)。
  
[practical-pytorch](https://github.com/spro/practical-pytorch)  

实用PyTorch教程，关注于将RNNs用于NLP。  
  
[Glazier](https://github.com/google/glazier)  

一个用于在各种设备平台上自动安装微软Windows操作系统的工具。
  
[dtn-tensorflow](https://github.com/yunjey/dtn-tensorflow)  

无监督跨域图像生成的TensorFlow实现。
  
[Donkey](https://github.com/wroscoe/donkey)  

Donkey是极简主义以及模块化自驱动库，用Python编写。它因爱好而开发，关注于允许快速实验和轻松社区贡献。

[jupyter-themes](https://github.com/dunovank/jupyter-themes)  

自定义Jupyter Notebook主题。

[flask-base](https://github.com/hack4impact/flask-base)  

一个使用SQLAlchemy，Redis，用户认证等等的死简单Flask样板应用。
  
[attention-transfer](https://github.com/szagoruyko/attention-transfer)  

通过注意力转移改进卷积网络。
  
[face-identification-tpe](https://github.com/meownoid/face-identification-tpe)  

人脸识别演示，实现了人脸验证和集群工作的三重概率性嵌入（Triplet Probabilistic Embedding）

[syntax_sugar_python](https://github.com/czheo/syntax_sugar_python)  

一个库，添加了一些反pythonic语法糖到Python中。这只是一个实验原型，显示Python中操作符重载到一些潜力。
  
[kawaii-player](https://github.com/kanishka-linux/kawaii-player)  

音频/视频管理器，多媒体播放器和便携式媒体服务器，基于python3和pyqt5。
  
[GpxTrackPoster](https://github.com/flopp/GpxTrackPoster)  

从你的GPX轨迹创建一个引人注目的海报。
  

