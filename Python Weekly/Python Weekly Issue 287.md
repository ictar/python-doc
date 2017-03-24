原文：[Python Weekly - Issue 287](http://eepurl.com/cHK54v)

---

欢迎来到Python Weekly第287期。本周干货满满。尽情享用！

  
# 文章，教程和讲座  
  
[构建安全A.I.](https://iamtrask.github.io/2017/03/17/safe-ai/)

In this post, we're going to train a neural network that is fully encrypted
during training (trained on unencrypted data). The result will be a neural
network with two beneficial properties. First, the neural network's
intelligence is protected from those who might want to steal it, allowing
valuable AIs to be trained in insecure environments without risking theft of
their intelligence. Secondly, the network can only make encrypted predictions
(which presumably have no impact on the outside world because the outside
world cannot understand the predictions without a secret key). This creates a
valuable power imbalance between a user and a superintelligence. If the AI is
homomorphically encrypted, then from it's perspective, the entire outside
world is also homomorphically encrypted. A human controls the secret key and
has the option to either unlock the AI itself (releasing it on the world) or
just individual predictions the AI makes (seems safer).  
  
[How to mine newsfeed data and extract interactive insights in Python](http://ahmedbesbes.com/how-to-mine-newsfeed-data-and-extract-interactive-insights-in-python.html)  

In this tutorial, we will also make sense of the data for a specific use case.
We will collect news feeds from 60 different sources (Google News, The BBC,
Business Insider, BuzzFeed, etc). We will ingest them in a usable format.
We'll then apply some machine learning techniques to cluster the articles by
their similarity and we'll finally visualize the results to get high level
insights. This will give us a visual sense of the grouping clusters and the
underlying topics. These techniques are part of what we call topic mining.  
  
[Podcast.__init__ 第101集 - 与Tobias Oberstein和Alexander Godde聊聊Crossbar.io](https://www.podcastinit.com/episode-101-crossbar-io-with-tobias-oberstein-and-alexander-goedde/)  

As our system architectures and the Internet of Things continue to push us
towards distributed logic we need a way to route the traffic between those
various components. Crossbar.io is the original implementation of the Web
Application Messaging Protocol (WAMP) which combines Remote Procedure Calls
(RPC) with Publish/Subscribe (PubSub) communication patterns into a single
communication layer. In this episode Tobias Oberstein describes the use cases
and design patterns that become possible when you have event-based RPC in a
high-throughput and low-latency system.  
  
[Python和JSON：使用Pandas处理大型数据集](http://www.dataquest.io/blog/python-json-tutorial/)  

In this post, we'll look at how to leverage tools like Pandas to explore and
map out police activity in Montgomery County, Maryland. We'll start with a
look at the JSON data, then segue into exploration and analysis of the JSON
with Python.  
  
[Episode #103：通过PyLLVM和MongoDB，为数据科学家编译Python](https://talkpython.fm/episodes/show/103/compiling-python-through-pyllvm-and-mongodb-for-data-scientists)  

We begin looking at optimizing a subset of Python code for machine learning
using the LLVM compiler with a project called PyLLVM which takes plain python
code, compiles it to optimized machine instructions and distributes it across
a cluster. In the second half, we look at a fabulous new way to work with
MongoDB for Python writing data scientists. The project is called bson-numpy
and provides a direct connection between MongoDB and NumPy and is 10x faster
than standard pymongo.  
  
[Turbocharge Your Data Acquisition using the data.world Python Library](https://www.dataquest.io/blog/datadotworld-python-tutorial/)  

When working with data, a key part of your workflow is finding and importing
data sets. Being able to quickly locate data, understand it and combine it
with other sources can be difficult. One tool to help with this is data.world,
where you can search for, copy, analyze, and download data sets. In addition,
you can upload your data to data.world and use it to collaborate with others.
In this tutorial, we're going to show you how to use data.world's Python
library to easily work with data from your python scripts or Jupyter
notebooks.  
  
[Deep Learning without Backpropagation](https://iamtrask.github.io/2017/03/21/synthetic-gradients/)  

In this post, we're going to prototype (from scratch) and learn the intuitions
behind DeepMind's recently proposed Decoupled Neural Interfaces Using
Synthetic Gradients paper.  
  
[Five video classification methods implemented in Keras and TensorFlow](https://medium.com/@harvitronix/five-video-classification-methods-implemented-in-keras-and-tensorflow-99cad29cc0b5)  

In this post, we'll take a look at different strategies for classifying videos
using Keras with the TensorFlow backend. We'll attempt to learn how to apply
five deep learning models to the challenging and well-studied UCF101 dataset.  
  
[Python的函数是一类对象](https://dbader.org/blog/python-first-class-functions)  

Python's functions are first-class objects. You can assign them to variables,
store them in data structures, pass them as arguments to other functions, and
even return them as values from other functions. In this tutorial I'll guide
you through a number of examples to help you develop this intuitive
understanding. The examples will build on top of one another, so you might
want to read them in sequence and even to try out some of them in a Python
interpreter session as you go along.  
  
[Predicting Yelp Stars from Reviews with scikit-learn and Python](http://www.developintelligence.com/blog/2017/03/predicting-yelp-star-ratings-review-text-python/)  
In this post, we'll look at reviews from the Yelp Dataset Challenge. We'll
train a machine learning system to predict the star-rating of a review based
only on its text. For example, if the text says "Everything was great! Best
stay ever!!" we would expect a 5-star rating. If the text says "Worst stay of
my life. Avoid at all costs", we would expect a 1-star rating. Instead of
writing a series of rules to work out whether some text is positive or
negative, we can train a machine learning classifier to "learn" the difference
between positive and negative reviews by giving it labelled examples.  
  
[Running Jupyter notebooks on GPU on AWS: a starter guide](https://blog.keras.io/running-jupyter-notebooks-on-gpu-on-aws-a-starter-guide.html)  

This is a step by step guide to start running deep learning Jupyter notebooks
on an AWS GPU instance, while editing the notebooks from anywhere, in your
browser. This is the perfect setup for deep learning research if you do not
have a GPU on your local machine.  
  
[Essential Statistics for Data Science: A Case Study using Python, Part I](http://www.learndatasci.com/data-science-statistics-using-python/)  

Our last post dove straight into linear regression. In this post, we'll take a
step back to cover essential statistics that every data scientist should know.
To demonstrate these essentials, we'll look at a hypothetical case study
involving an administrator tasked with improving school performance in
Tennessee.  
  
[Spice Up Thy Jupyter Notebooks With mypy](http://journalpanic.com/post/spice-up-thy-jupyter-notebooks-with-mypy/)  

Add type-checking to Jupyter Notebook cells.  
  
[Self-Organising Maps: In Depth](http://blog.yhat.com/posts/self-organizing-maps-2.html)  

In Part 1, I introduced the concept of Self-Organising Maps (SOMs). Now in
Part 2 I want to step through the process of training and using a SOM - both
the intuition and the Python code. At the end I'll also present a couple of
real life use cases, not just the toy example we'll use for implementation.  
  
[Filtering startup news with Machine Learning - Part 1](https://blog.monkeylearn.com/filtering-startup-news-machine-learning/)  

On this new post series, we will analyze hundreds of thousands of articles
from TechCrunch, VentureBeat and Recode to discover cool trends and insights
about startups. On this first post, we will cover how Scrapy can be used to
get all the articles ever published on these tech news sites and how
MonkeyLearn can be used for filtering these crawled articles by whether they
are about startups or not. We want to create a dataset of startup news
articles that can be used for studying trends later on  
  
[Hack The Virtual Memory: Python bytes](https://blog.holbertonschool.com/hack-the-virtual-memory-python-bytes/)  
  
[Advanced Web Scraping: Bypassing "403 Forbidden," captchas, and more](http://sangaline.com/post/advanced-web-scraping-tutorial/)  
  
[Duplicate image detection with perceptual hashing in Python](http://tech.jetsetter.com/2017/03/21/duplicate-image-detection/)  
  
[在Dask中开发Convex优化算法](https://matthewrocklin.com/blog//work/2017/03/22/dask-glm-1)  
  
[随机步进贝叶斯深层网络（Random-Walk Bayesian Deep Networks）：处理非固定数据](http://twiecki.github.io/blog/2017/03/14/random-walk-deep-net/)  
  
[分析和优化你的Python代码](https://toucantoco.com/back/2017/01/16/python-performance-optimization.html)  
  
[Pandas和Seaborn - 优雅处理和可视化数据的指南](https://tryolabs.com/blog/2017/03/16/pandas-seaborn-a-guide-to-handle-visualize-data-elegantly/)  
  
[添加AJAX交互到Django中，不用写Javascript啦！](https://petercuret.com/add-ajax-to-django-without-writing-javascript/)  
  
  
# 好玩的项目，工具和库  
  
[aeneas](https://github.com/readbeyond/aeneas)  

aeneas是一个Python/C库和一组工具，用来自动化同步音频和文本 (又名强制排列)。
  
[visdom](https://github.com/facebookresearch/visdom)  

用于创建、组织和共享实时丰富数据的可视化的灵活工具。支持Torch和Numpy。
  
[better-exceptions](https://github.com/Qix-/better-exceptions)  

Python中自动的漂亮而有用的异常。  
  
[Instant-Lyrics](https://github.com/bhrigu123/Instant-Lyrics)  

显示当前播放的Spotify歌曲，或者任何歌曲的歌词。
  
[flango](https://github.com/kennethreitz/flango)  

A Django template for using Flask for the frontend, Django for the backend.  
  
[Bit](https://github.com/ofek/bit)  
Bit is Python's fastest Bitcoin library and was designed from the beginning to
feel intuitive, be effortless to use, and have readable source code. It is
heavily inspired by Requests and Keras.  
  
[bulbea](https://github.com/achillesrasquinha/bulbea)  

Deep Learning based Python Library for Stock Market Prediction and Modelling.  
  
[PytchControl](https://github.com/WyattWismer/PytchControl)  

通过基于python的音调检测控制你的电脑
  
[pythonnet](https://github.com/pythonnet/pythonnet)  

Python for .NET is a package that gives Python programmers nearly seamless
integration with the .NET Common Language Runtime (CLR) and provides a
powerful application scripting tool for .NET developers. It allows Python code
to interact with the CLR, and may also be used to embed Python into a .NET
application  
  
[selenium-respectful](https://github.com/SerpentAI/selenium-respectful)  

Minimalist Selenium WebDriver wrapper to work within rate limits of any amount
of websites simultaneously. Parallel processing friendly.  
  
[python-cx_Oracle](https://github.com/oracle/python-cx_Oracle)  

Python interface to Oracle Database conforming to the Python DB API 2.0
specification.  
  
[flask-rest-jsonapi](https://github.com/miLibris/flask-rest-jsonapi)  

根据JSONAPI 1.0规范构建REST API的Flask扩展。
  
[plugin.video.netflix](https://github.com/asciidisco/plugin.video.netflix)  

Kodi的基于Inputstream的Netflix插件。
  
[DiscoGAN-pytorch](https://github.com/carpedm20/DiscoGAN-pytorch) 

"Learning to Discover Cross-Domain Relations with Generative Adversarial Networks"的PyTorch实现。
  
  
# 最新发布  
  
[Python 3.6.1](https://docs.python.org/3.6/whatsnew/changelog.html#python-3-6-1)  
  
[Django 1.11 候选版本1](https://www.djangoproject.com/weblog/2017/mar/21/django-111-rc-1-released/)  
  
[PyPy2.7和PyPy3.5 v5.7 - 二合一版本](https://morepypy.blogspot.nl/2017/03/pypy27-and-pypy35-v57-two-in-one-release.html)  
  
  
# 近期活动和网络研讨会  
  
[SoCal Python 2017年3月聚会 - Los Angeles, CA](https://www.meetup.com/socalpython/events/238244773/) 
 
将会有以下讲座

  * Magic Method, on the wall, who, now, is the `__fairest__` one of all? 
  * Cooking with Bio-Python

  
[模块生命周期：如何测试和部署你的Python Web应用 - Philadelphia, PA](https://www.meetup.com/phillypug/events/237877302/)  

本次技术研讨会将会遵循开发者在使用一个Python模块时所采用的步骤：单元测试代码的修改；构建该模块的部署版本；使用已部署的版本，对应不同的OS运行集成测试。单元测试也将会作为集成测试，针对多个Python版本运行。与会者将会更加了解如何测试和部署Python Web应用。
