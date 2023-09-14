原文：[Python Weekly - Issue 617](http://eepurl.com/izHuCA)

---

欢迎来到Python周刊第 617 期。让我们直奔主题。  


# 文章、教程和讲座

[向量嵌入教程 – 使用 GPT-4 和自然语言处理创建 AI 助手](https://www.youtube.com/watch?v=yfHHvmaMkcA) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
 
了解向量嵌入，以及如何在机器学习和人工智能项目中使用它们。了解如何使用矢量嵌入创建 AI 助手。

[Python 数据帧交换协议（Dataframe Interchange Protocol）是如何让生活更美好的](https://ponder.io/how-the-python-dataframe-interchange-protocol-makes-life-better/)  
 
在本文中，我们回答了有关 Python 数据帧交换协议的三个问题：它是什么 + 它解决了什么问题；它是怎么运行的; 以及它被广泛采用的程度。

[flake8-logging 介绍](https://adamj.eu/tech/2023/09/07/introducing-flake8-logging/)  

本文介绍 flake8-logging，一个可帮你改进 Python 代码中的日志记录的 Flake8 插件。Flake8 是一个检查 Python 代码是否有错误和样式违规的 linter。flake8-logging 通过添加检查日志代码的规则来扩展 Flake8。

[我们是如何使用 LLM 嵌入来构建 AI 搜索引擎的](https://www.youtube.com/watch?v=ZCPUmC37HLU) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)   

演示并解释如何使用 Python 的 sentence-transformers 库，通过 Django ORM 和 pgvector 来生成、存储和查询 LLM 嵌入。该视频演示了一个原型应用，这个应用可以使用求职者的非结构化英语描述来进行职位描述的“人工智能驱动搜索”。

[调试 Python 中正则表达式的灾难性回溯](https://krishnanchandra.com/posts/regex-catastrophic-backtracking/)  

这篇文章讨论了正则表达式中的灾难性回溯问题，并提供了一些有关如何避免该问题的提示。它还讨论了正则表达式引擎处理回溯的不同方式，以及不同方法之间的权衡。

[使用 Coiled、Dask 和 Xarray 处理 250 TB 数据集](https://blog.coiled.io/blog/coiled-xarray.html)  

作者使用 Xarray、Dask 和 Coiled，在 20 分钟内成功处理了大小为 250 TB 的地理空间云数据，强调了所涉及的挑战和优化，同时将成本保持在大约 25 美元左右。这一成就证明了大规模数据处理的可行性，暴露了可扩展性问题，并探索了此类任务的成本效益策略。

[使用 Kamal 部署 Django (mrsk)](https://anthonynsimon.com/blog/kamal-deploy/)  

如果你只想在远程计算机上部署容器，Kamal 可能是你工具带的一个不错的补充。将容器部署到一台或多台远程计算机时，它会自动执行许多常见步骤，而不会引入诸如 Kubernetes 这样的复杂性，也无需使用托管服务。

[使用 Django 和 HTMX 添加数据库搜索](https://www.photondesigner.com/articles/database-search-django-htmx)  

我们将使用 Django 和 HTMX 来创建快速且简单的数据库搜索。使用 HTMX 可以轻松快速地完成此操作。将有 6 个步骤。

[何时使用 Python 中的类？当您重复相同的函数时](https://death.andgravity.com/same-functions)  
 
在本文中，我们将了解在 Python 中使用类的另一种启发式方法，其中包含来自实际代码的示例以及一些需要记住的事项。

[迈向新的 SymPy：第 1 部分 - 概述](https://oscarbenjamin.github.io/blog/czi/post1.html)  

第一篇文章将概述像 SymPy 这样的计算机代数系统 (CAS) 的基础结构，描述 SymPy 目前存在的一些问题以及解决这些问题的方法。接着，后续的文章将更详细地关注特定组件、已完成的工作以及将来要做什么。
  * [第 2 部分 - 多项式](https://oscarbenjamin.github.io/blog/czi/post2.html) - 本文将描述 SymPy 的多项式计算代数系统，以及如何应用每个步骤来加速 SymPy。我会谈谈 FLINT 和 python-flint，但我也会写一篇关于这些的单独的文章，因为我知道有些人对使用 python-flint 比 SymPy 本身更感兴趣，我希望鼓励他们为 python-flint 做出贡献。


[如何通过 PyObjC，使用 Apple Vision 框架进行文本识别](https://yasoob.me/posts/how-to-use-vision-framework-via-pyobjc/)  

本文讨论如何通过 PyObjC（允许你通过 Python 使用 Objective-C 框架），使用 Vision 框架。Vision 框架是一个机器学习框架，可用于执行人脸检测、对象检测和文本识别等任务。 

[可视化 CPython 发布过程](https://sethmlarson.dev/security-developer-in-residence-weekly-report-9)  

[为模块改变 Python 属性处理](https://lwn.net/SubscriberLink/943619/eaa8a4496fcba1fd/)  

[如何使用 Python 和 Django 对类似于 Twitter 和 Instagram 的后续系统进行建模](https://uhtred.dev/insights/how-to-model-a-following-system-similar-to-twitter-and-instagram-with-python-and-django)  

  
# 好玩的项目，工具和库  

[Litestar](https://github.com/litestar-org/litestar)  
  
Litestar 是一个功能强大、灵活但有自己想法的 ASGI 框架，专注于构建 API，并提供高性能的数据验证和解析、依赖项注入、第一类（first-class）ORM 集成、授权原语以及启动和运行应用程序所需的更多功能。

[InstaGraph](https://github.com/yoheinakajima/instagraph)  
  
将文本输入或 URL 转换为知识图表并显示。

[Prompt flow](https://github.com/microsoft/promptflow)  

构建高质量的 LLM 应用 - 从原型设计、测试到生产部署和监控。

[kr8s](https://github.com/kr8s-org/kr8s)  

一个即拆即用（batteries-included）的 Kubernetes Python 客户端库，对于已经知道如何使用 kubectl 的人来说会感觉到很熟悉。  

[Pyflyby](https://github.com/deshaw/pyflyby)  

一套 Python 生产力工具。

[pai](https://github.com/AlexWiles/pai)  

具有内置 AI 代理和代码生成功能的 Python REPL。  

[view.py](https://github.com/ZeroIntensity/view.py)  

快如闪电的现代 Web 框架。

[django-send-sms](https://github.com/hizbul25/django-send-sms)  

只需编写一行代码，就可以使用任何短信服务提供商，从 Django 应用程序发送短信。

[WhatsApp-Llama](https://github.com/Ads-cmu/WhatsApp-Llama/)  

根据你的 WhatsApp 对话，微调 LLM，让它像您一样说话。 

[textual-web](https://github.com/Textualize/textual-web)  
 
在浏览器中运行 TUI 和终端。

[LiteLLM](https://github.com/BerriAI/litellm)  

使用 OpenAI 格式，调用所有 LLM API（包括 Anthropic、Huggingface、Cohere、TogetherAI、Azure、OpenAI 等）

[blip-caption](https://github.com/simonw/blip-caption)  

使用 Salesforce BLIP 为图像生成标题。

[Vanna](https://github.com/vanna-ai/vanna)  
 
个性化 AI SQL 代理。

[Medusa](https://github.com/FasterDecoding/Medusa)  

用于通过多个解码头加速 LLM 生成的简单框架。

  
# 最新发布  

[Visual Studio Code 中的 Python - 2023 年 9 月版本](https://devblogs.microsoft.com/python/python-in-visual-studio-code-september-2023-release/)  

此版本包括以下更新：
* Python 增加了“重新创建（Recreate）”或者“使用现有（Use Existing）”的选项：创建环境命令
* 使用环境变量进行实验性终端激活
* 社区贡献的 yapf 扩展
  

# 近期活动和网络研讨会  

[2023 年 9 月的 PyData Berlin Meetup](https://www.meetup.com/pydata-berlin/events/295877988/)  

将进行以下演讲：
* 随机梯度朗之万动力学（Stochastic Gradient Langevin Dynamics，SGLD） —— 动机、基础以及 DL 可以获得什么
* OpenAI 开源语音识别模型Whisper：最先进的语音转录和语音界面革命


[2023 年 9 月的 PyData Zurich Meetup](https://www.meetup.com/pydata-zurich/events/295909252/)  

将有以下演讲：
* 如何（不）在机器学习中使用公平性指标
* 我们可以从 Python 的类型系统中挤出更多的东西吗？Tensor Shape Annotations 的挑战。