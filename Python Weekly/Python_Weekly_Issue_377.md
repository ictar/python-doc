原文：[Python Weekly - Issue 377](http://eepurl.com/ga8m9D)

---

欢迎来到Python周刊第 377 期。让我们直奔主题。


# 来自赞助商 

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/b88ddabb-e48c-4fbd-af9a-fe48f8a98690.png)](https://github.com/IntelPython/daal4py)

你是一名从事大型问题规模处理和使用机器学习框架的数据科学家吗？试试开源的 [daal4py](https://github.com/IntelPython/daal4py) —— 由 Intel® DAAL（Intel® 数据分析加速库）加速的闪电般快速的机器学习算法性能。
  

# 文章，教程和讲座
  
[修复 Python 中一个严重的内存泄漏](https://info.cloudquant.com/2018/12/numpyleaks)  

我们的一些高级用户报告说，长时间运行的 backtest 有时会耗尽内存。这些高级用户常常会找到新的交易策略，因此，我们希望与他们合作，以提供我们的 backtesting 工具的性能。然而，在过去的几周里，我们的高级工程师发现，问题并非出在我们的代码里，而是我们使用的一个流行的 Python 库。我们在 numpy 和 numba 中发现了这个问题。最终，泄漏是由我们使用这些库的方式引起的。我们进行的修正，并且可以从下图看到，它们确实提高了我们的交易模拟器的内存使用率。下面是我们的高级工程师的报告，以便其他人可以从我们的工程工作中学到点什么。
  
[pandas 库的未来在哪里？](https://www.dataschool.io/future-of-pandas/)  

pandas 是一个非常流行的 Python 库，用来进行数据分析、操作数据和可视化，但它还没有到 1.0 版本。所以，pandas 的下一步会是什么？
  
[gpython：一个用 Go 编写的 Python 解释器 "batteries not included"](https://blog.gopheracademy.com/advent-2018/gpython/)  

Gpython 是一个用 Go 写的 Python 3.4 解释器。这是一篇关于它是如何形成的、如何工作的以及它的发展方向的文章。包含了关于像 Python/Gpython 这样的解释型语言是如何使用虚拟机（VM）、词法分析、语法分析和字节码编译的快速预览。
  
[使用 Python 进行网络爬取介绍](https://towardsdatascience.com/an-introduction-to-web-scraping-with-python-a2601e8619e5)  

让我们用 BeautifulSoup 爬取一个虚拟书店网站！
  
[构建一个自动驾驶 Q-Bot](https://medium.com/@michael_87060/build-a-self-driving-q-bot-6aa67ba60769)  

仅需一台 3D 打印机、一些 Python 以及大量的试错即可。
  
[使用 Twilio 和 Python 构建一个 Serverless SMS Raffle](https://www.twilio.com/blog/twilio-serverless-sms-raffle-python-lambda)  

使用 Amazon Lambda、API Gateway、Twilio 可编程 SMS 和 Python 创建一个 Serverless SMS Raffle。
  
[你不知道 Python 能做的五件事](https://www.youtube.com/watch?v=WlGkBqBRsik)   
  
[如何修复你的 Python 代码样式](https://www.caktusgroup.com/blog/2018/12/10/how-to-fix-python-code-style/)  
  
[使用 Python 和 Selenium 实现免费酒店无线上网](https://gkbrk.com/2018/12/free-hotel-wifi-with-python-and-selenium/)  
  
[通过深度强化学习加速 Pong AI](https://blog.floydhub.com/spinning-up-with-deep-reinforcement-learning/)  
  
[Microsoft 的 Python：低调至极](https://medium.com/microsoft-open-source-stories/python-at-microsoft-flying-under-the-radar-eabbdebe4fb0)  
  
[Keras - 保存并保存你的深度学习模型](https://www.pyimagesearch.com/2018/12/10/keras-save-and-load-your-deep-learning-models/)  
  
[如何从 Jupyter Notebook 扩展整洁的软件架构](https://github.com/guillaume-chevalier/How-to-Grow-Neat-Software-Architecture-out-of-Jupyter-Notebooks)  
  
[使用 Python 3、AWS Lambda、Twilio Lookup 和 SMS 识别未知电话号码](https://www.twilio.com/blog/identify-unknown-phone-numbers-python-3-aws-lambda-lookup-sms)  
  
[从 Jupyter Notebook 安装 Python 包](https://jakevdp.github.io/blog/2017/12/05/installing-python-packages-from-jupyter/)  
  
  
# 好玩的项目，工具和库  
  
[PracticalAI](https://github.com/GokuMohandas/practicalAI)  

学习机器学习的实用方法。
  
[loguru](https://github.com/Delgan/loguru)  

让 Python 日志变得炒鸡简单。

[DGL](https://github.com/dmlc/dgl)  

Python 包，旨在在现有的 DL 框架上简化图形深度学习。
  
[completion_utils](https://github.com/CarvellScott/completion_utils)  

协助使用 python 替代 bash、fish、zsh 等编写 shell 补全的小工具。

[Athena](https://github.com/M4cs/Athena)  

Theos Tweak 开发框架的通用用户界面。

[DynamicForms](https://github.com/velis74/DynamicForms)  

DynamicForms 具有 DRF Serializer 和 ViewSet 的所有可视化和数据输入功能，并且加了点料：它是一个 django 库，提供动态显示表单字段、自动填充默认值、动态记录加载等等功能。
  
[crudcast](https://github.com/chris104957/crudcast)  

只用几行 YAML 就能创建并部署 RESTful API。
  
[secure](https://github.com/cakinney/secure)  

Python web 框架的安全 🔒 头和 cookie

[Varken](https://github.com/Boerderij/Varken)  

独立的命令行实用程序，用于将 Plex 生态系统中的数据聚合到 InfluxDB 中。
  
[terracotta](https://github.com/DHI-GRAS/terracotta)  

一个轻量级多功能 XYZ tile 服务器，使用 Flask 和 Rasterio 构建
  
[pyAudioClassification](https://github.com/98mprice/pyAudioClassification)  

超级简单的音频分类 🎶
  
[python-chirp](https://github.com/concretecloud/python-chirp)  

给大家的消息传递。

[wtfiswronghere](https://github.com/qxf2/wtfiswronghere)  

初学者开始写 Python 时可能会遇到的一系列简单错误。
  
[SSD](https://github.com/lufficc/SSD)  

SSD 的高质量快速模块化参考实现，使用 PyTorch 1.0。
  
  
# 最新发布  
  
[PyTorch 1.0](https://github.com/pytorch/pytorch/releases/tag/v1.0.0)  
  
[Python 3.7.2rc1 和 3.6.8rc1](https://blog.python.org/2018/12/python-372rc1-and-368rc1-now-available.html)   