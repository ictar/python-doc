原文：[Python Weekly - Issue 290](http://eepurl.com/cK06Gn)

---

欢迎来到Python Weekly第290期。本周，让我们直入主题。 
  
# 来自赞助商  

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/be2db49d-26ff-4973-8f45-7f5e5f877422.png)](https://software.intel.com/en-us/intel-distribution-for-python?utm_source=April%202017%20Image%20link%20Python%20weekly&utm_medium=email&utm_campaign=Python%20Weekly%20newsletter)

[Intel® Distribution for Python*](https://software.intel.com/en-us/intel-distribution-for-python?utm_source=April%202017%20ad%20Python%20weekly&utm_medium=email&utm_campaign=Python%20Weekly%20newsletter) 已经**免费**可用了，并且许可证允许商业和非商业用途。就是这样！试试在NumPy, SciPy和scikit-learn中的性能优化吧，所有的加速都在后台。你的代码保持不变 —— 仅需在Intel Python环境中运行它，即可获得加速。


# 文章，教程和讲座  
  
[数据整理101：使用Python来获取、操纵和可视化NBA数据](http://blog.yhat.com/posts/visualize-nba-pipelines.html)  

这是一篇使用pandas和其他一些包来为获取NBA数据构建一个简单的数据管道的教程。即使这个教程是使用NBA数据来完成的，但是你无需是个NBA粉就可以跟着它走。相同的概念和技术可以应用于你选择的任何项目。
  
[使用PyTorch的递归神经网络](https://devblogs.nvidia.com/parallelforall/recursive-neural-networks-pytorch/)  

这篇文章详述了通过一个循环跟踪器和TreeLSTM节点的递归神经网络的PyTorch实现，也称为SPINN，这是来自神经语言处理的一个深度学习模型的样例，在很多流行框架中都是很难建立的。我所描述的实现也是部分批量的，因此，它可以利用GPU加速来比那些不使用批量的版本运行的更快一点。
  
[递归、连续和Trampoline](http://eli.thegreenplace.net/2017/on-recursion-continuations-and-trampolines/)  

尾部递归和常规递归如何不同？连续与此有什么关系，CPS是什么，以及trampoline如何助益？这篇文章提供了介绍，包含了Python和Clojure的代码样例。
  
[语言创建傻瓜教程](https://ralsina.me/weblog/posts/creating-languages-for-dummies.html)  

这篇文章解释了使用Python和PyParsing，如何从无到一个函数式、可扩展语言。
  
[你该相信谁的评分？IMDB，烂番茄，Metacritic，还是Fandango？](https://medium.freecodecamp.com/whose-reviews-should-you-trust-imdb-rotten-tomatoes-metacritic-or-fandango-7d1010c6cf19)  

你应该看某部电影吗？嗯，有很多因素要考虑，比方说导演、演员、还有电影的预算。我们大多数都是基于一篇评论、一个简短的预告片、或者只是通过看看这个电影的评级来做决定的。这篇文章旨在推荐一个单一的网站，来快速获取准确的电影评级，并为其提供强大的数据驱动的论证。
  
[Podcast.__init__ 第104集 - Oscar电子商务](https://www.podcastinit.com/episode-104-oscar-ecommerce-with-david-winterbottom-and-michael-van-tellingen/)  

如果你要销售一个产品，无论是个实体产品还是订阅服务，那么你需要有一种管理交易的方法。Django的Oscar电子商务框架是一个灵活、可扩展的完善方法，让你添加这种功能到你的网站上。本周，David Winterbottom和Michael van Tellingen谈到了这个项目是怎样开始的，其覆盖范围，以及如何开始使用它。
  
[Django Rest框架入门](http://www.projectforrest.com/path/70)  

如果你期待在Django中构建REST API，并且之前没啥经验，那么，这个为你指了一条明路。
  
[zi2zi：使用条件对话网络掌握中国书法](https://kaonashi-tyc.github.io/2017/04/06/zi2zi.html)  
  
[婚礼规模：我是如何使用Twilio, Python和Google来自动化我的婚礼的](https://www.twilio.com/blog/2017/04/wedding-at-scale-how-i-used-twilio-python-and-google-to-automate-my-wedding.html)  | [中文版](../Others/婚礼规模：我是如何使用Twilio,%20Python和Google来自动化我的婚礼的.md)
  
[使用Context Free Grammar和Pyparsing构建Slack Bot CLI](https://hashedin.com/2017/03/29/build-slack-bot-cli-using-cfg-pyparsing/)  
  
[Episode #106：使用Python发明你自己的电脑游戏](https://talkpython.fm/episodes/show/106/invent-your-own-computer-games-with-python)  
  
  
# 书籍  
  
[Python数据分析 —— 第二版](http://amzn.to/2pvtbfv)  

通过这本书，你将学习如何使用Python来处理和操作数据，以进行复杂的分析和建模。我们使用NumPy和Pandas，学习数据操作，例如聚合、连接、附加、清理和处理缺失值。本书介绍了如何从各种数据源（例如SQL和NoSQL, CSV文件和HDF5）来存储和检索数据。我们学习如何使用可视化库来可视化数据，以及一些高级话题，例如信号处理、时间序列、文本数据分析、机器学习和社交媒体分析。
  
  
# 本周的Python工作  
  
[Wevolver招聘全栈Python开发](http://jobs.pythonweekly.com/jobs/full-stack-python-developer-to-build-github-for-hardware/)  

Wevolver是硬件开发的一个协作平台。(硬件界的Github) 我们构建了Wevolver，让来自四海八荒的每一个人都可以开发硬件技术。我们的平台被来自五湖四海的工程师用来在开源和私人项目上进行合作。我们对开源深刻承诺，我们分析并回馈我们的社区。我们正在寻找想要对我们的世界产生巨大积极影响的雄心勃勃的人。
  
[Money Mover招聘后端开发](http://jobs.pythonweekly.com/jobs/back-end-developer-3/)  

如果你想要在一个于2014年全休投资达到68亿美元的有前途的FinTech创业公司一展拳脚，那么，我们找的就是你。你将与我们的主要开发人员紧密合作，专注于后端开发和我们的web应用、合作伙伴门户应用、合作伙伴集成以及营销网站的服务端逻辑。
  
  
# 好玩的项目，工具和库  
  
[Tinfoil Chat](https://github.com/maqp/tfc)  

Tinfoil Chat (TFC) 是一个高度保密的加密消息系统，可以在现有的IM客户端上运行。它被用于保护用户免受被动窃听、有组织犯罪和民族国家攻击者实施的MITM工具和远程CNE工具之害。
  
[DjangoQL](https://github.com/ivelum/djangoql)  

带自动完成功能的Django高级搜索语言。支持逻辑运算符、括号、表连接，可与任何Django模型配合使用。
  
[imgaug](https://github.com/aleju/imgaug)  

用于机器学习实验的图像增强。
  
[python-alexa](https://github.com/nmyster/python-alexa)  

一个简单的Python库，使得旨在在Lambda中使用时，让Alexa技能发展变得容易。
  
[kim](https://github.com/mikeywaites/kim)  

一个JSON序列化和调度框架。
  
[Punter](https://github.com/nethunteros/punter)  

Punter (被动猎手) 帮你迈出涉足某个域的第一步。它的想法不是触及目标域，而是被动地找到一个好的初始量信息，然后将其放在一个易于查看的报告中。
  
[pytorch-rl](https://github.com/jingweiz/pytorch-rl)  

使用pytorch和visdom的深度强化学习。
  
[drawlikebobross](https://github.com/kendricktan/drawlikebobross)  

借助神经网络的神力（使用PyTorch），像Bob Ross那样作画！
  
  
# 近期活动和网络研讨会  
  
[PyHou 2017年4月聚会 - Houston, TX](https://www.meetup.com/python-14/events/238248013/)  
  



