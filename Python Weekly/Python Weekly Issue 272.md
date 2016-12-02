原文：[Python Weekly - Issue 272](http://eepurl.com/crfJ89)
  
---

欢迎来到Python Weekly第272期。本周，让我们直入主题。
  

# 文章，教程和讲座  
  
[Episode #87: PonyORM: 可能是最Pythonic的ORM](https://talkpython.fm/episodes/show/87/ponyorm-the-most-pythonic-orm-yet)  

如果你可以有任何你所想要的从Python访问数据的API，那会是什么样子的呢？而又是什么让它Pythonic呢？本周，你将会听到Pony ORM: Pony是一个Python ORM，带有漂亮的查询语法，让你能够使用Python生成器和lambda来编写你的数据库查询。
  
[和Andrew Godwin Guest聊聊Python, Django, 和Channels](https://changelog.com/podcast/229)  

Django核心贡献者Andrew Godwin加入了本次节目，来告诉我们所有关于Python和Django的东东。如果你曾经好奇过为什么人们喜爱Python，Django作为一个web框架的美德是什么，或者Django Channels是怎样够得上Phoenix的Channels和Rails' Action Cable的，那么这个节目就是为你准备的。另外：Andrew还负责了提供资金和维持开源。
  
[使用BeautifulSoup的Python Web 抓取教程](https://www.dataquest.io/blog/web-scraping-tutorial-python/)  

在这个教程中，我们将向你展示如何使用Python 3和BeautifulSoup库进行网络爬取。我们将爬取来自国家气象局的天气预报，然后使用Pandas库对其进行分析。
  
[Podcast.__init__ 第85期 - 和Eric Steele聊聊Plone](https://www.podcastinit.com/episode-85-plone-with-eric-steele/)

Plone是第一批使用Python构建的CMS项目之一，并且它仍在积极发展中。本周，Eric Steele，Plone的发行管理者，告诉我们它是如何开始的，如何架构，以及社区如何是其最大优势之一的。

[在Python中检查是否所有的项都匹配某个条件](http://treyhunner.com/2016/11/check-whether-all-items-match-a-condition-in-python/)  

在这篇文章中，我们将看看一个常见的编程模式，并且讨论当我们注意到这种模式的时候，如何重构我们的代码。
  
[入门Pytest](https://jacobian.org/writing/getting-started-with-pytest/)  

Pytest是我首选的Python测试库。它使得简单的测试非常容易编写，并且充满了高级特性 (以及大量的插件) ，这有助于更高级的测试场景。为了演示基本知识，我将带你看看我是如何使用py.test，以测试驱动的方式，解决第一对cryptopals挑战的。

[（缺乏）反对Python 3的情况](http://blog.lerner.co.il/case-python-3/)  
  
[你发现了关于python哪些很酷的特性？](https://www.reddit.com/r/Python/comments/5f3koe/what_are_some_cool_features_that_you_discovered/)  
  
[使用Python的Kanren简化复杂业务逻辑](https://jeffersonheard.github.io/2016/11/simplifying-complex-business-logic-with-pythons-kanren/)  
  
[PyCharm技巧](http://www.devdungeon.com/content/pycharm-tips)  
  
[Jupyter Notebook教程：权威指南](https://www.datacamp.com/community/tutorials/tutorial-jupyter-notebook)  
  
[你创建了哪些让你的生活更轻松的Python程序？](https://www.reddit.com/r/Python/comments/5esa4g/what_python_program_have_you_created_to_make_your/)  
  
[限制Python版本](http://www.b-list.org/weblog/2016/nov/28/break-python/)  
  
[优化Django查询集的构造](https://adamj.eu/tech/2016/11/30/optimizing-construction-of-django-querysets/)  
  
[Tensorflow: 如何冻结一个模型，并通过一个python API来提供它](https://blog.metaflow.fr/tensorflow-how-to-freeze-a-model-and-serve-it-with-a-python-api-d4f3596b3adc)  
  
  
# 书籍
  
[深入浅出Python：一个大脑友好型指南，第二版](http://amzn.to/2gKnXcs)  

基于认知科学和学习理论的最新研究成果，深入浅出Python使用了丰富的视觉形式引人入胜，而不是让你昏昏欲睡的满是文字的方法。为什么要浪费时间挣扎于新的概念呢？这种多感官学习经验是专为你的大脑实际工作的方式而设的。

  
# 好玩的项目，工具和库 
  
[DeepGraph](https://github.com/deepgraph/deepgraph)  
DeepGraph是一个可扩展的通用数据分析软件包。它基于pandas DataFrame实现了一个网络表示，并提供了构建、分区和绘制网络的方法，以及与流行网络包交互的接口等等。
  
[rapping-neural-network](https://github.com/robbiebarrat/rapping-neural-network)  

它是一个已经在说唱音乐上进行训练的神经网络，并且可以将任何你扔给它的歌词重新安排成一首押韵且（一定程度上）具有流动性的歌。

[pycodesuggest](https://github.com/uclmr/pycodesuggest)  

使用RNN语言模型学习自动完成。

[Fatiando](http://www.fatiando.org/index.html)  

一个用于地球物理建模和反演的开源Python库。 
  
[shellfuncs](https://github.com/timofurrer/shellfuncs)  

Python API，用来把shell函数充当Python函数执行。
  
[TwitterQA](https://github.com/kootenpv/twitterqa)  

"一个神经会话模型"，一个基于深度学习的聊天机器人的TensorFlow实现。
  
[djangocms-inline-comment](https://github.com/arteria/djangocms-inline-comment)  

用于django CMS的插件 - 添加注释到结构板和注释掉插件，只对员工可见。
  
[speech-to-text-wavenet](https://github.com/buriburisuri/speech-to-text-wavenet)  

基于DeepMind的WaveNet和tensorflow的端到端的句子层面的英语语音识别。

[mitmAP](https://github.com/xdavidhu/mitmAP)  

一个python程序，创建一个假的AP并嗅探数据。
  
[pix2pix-tensorflow](https://github.com/yenchenlin/pix2pix-tensorflow) 

"使用条件对抗性网络的图像到图像转换"的TensorFlow实现
  
[gcn](https://github.com/tkipf/gcn)  

TensorFlow中图卷积网络的实现。
  
  
# 近期活动和网络研讨会
  
[网络研讨会：Python中的字典](https://www.crowdcast.io/e/dictionaries)  

对于新的Python学习者，字典在理解上往往会比列表更加棘手。在即时聊天期间，我们将会看看字典是什么，以及为什么它们有用。Trey还会回答你字典相关的问题。新手应该会很欢迎这个谈话，但是高级的Pythonista或许也能学习到一些新的东西。

[PyAtl 2016年12月聚会 - Atlanta, GA](https://www.meetup.com/python-atlanta/events/231510144/)  
  
