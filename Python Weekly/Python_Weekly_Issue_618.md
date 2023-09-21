原文：[Python Weekly - Issue 618](http://eepurl.com/iz-KzU)

---

欢迎来到Python周刊第 618 期。让我们直奔主题。


# 文章、教程和讲座 
  
[为了好玩，将 ML 模型编译为 C](https://bernsteinbear.com/blog/compiling-ml-models/)  
 
ML 模型可以编译为图形，从而被用来遍历以执行前向和后向传递。这种方法可以提高性能并使调试 ML 模型变得更加容易。
  
[从数据集角度优化 LLM](https://sebastianraschka.com/blog/2023/optimizing-LLMs-dataset-perspective.html)  

本文重点介绍如何使用精心制作的数据集对 LLM 进行微调，从而提高 LLM 的建模性能。具体来说，本文重点介绍了涉及修改、利用或操作数据集以进行基于指令的微调的策略，而不是更改模型架构或训练算法（后者将是未来文章的主题）。本文还会解释如何准备自己的数据集来微调开源LLM。
  
[为什么有这么多 Python 数据帧(DataFrame)？](https://ponder.io/why-are-there-so-many-python-dataframes/)  

这篇文章探讨了 Python 数据帧(DataFrame)的激增，剖析了它们在数据科学和分析中盛行背后的原因，揭示了有助于丰富其内容的各种库和框架。

  
[我手工制作了一个 Transformer（无需训练！） ](https://vgel.me/posts/handmade-transformer/)   
 
本文深入探讨了如何创建手工制作的 Transformer 模型，提供了从头开始构建这种流行的深度学习架构的详细演练，深入了解了其内部工作原理和结构。
  
[EuroPython 2023 视频](https://www.youtube.com/playlist?list=PL8uoeex94UhFcwvAfWHybD7SfNgIUBRo-) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
  
这里是由 EuroPython 2023 团队和 EuroPython 协会为您带来的会议的所有视频。
  
[如何在 6 分钟内向 Django 添加无服务器函数（使用 HTMX 和 AWS Lambda）](https://www.photondesigner.com/articles/serverless-functions-django)  

本文讨论无服务器函数（serverless functions）与 Django 的集成，重点介绍开发人员可以如何利用无服务器计算（serverless computing）的优势来执行 Django 应用程序中的特定任务。它探讨了无服务器架构的优势，并为实现提供了实用的见解。
  
[使用 Python 模拟蒙提霍尔问题（Monty Hall problem）](https://www.dataschool.io/python-probability-simulation/)  

使用 Python 解决这个困扰数学家和诺贝尔奖获得者的经典概率难题！

> Ele 注：[蒙提霍尔问题](https://zh.wikipedia.org/zh-hans/%E8%92%99%E6%8F%90%E9%9C%8D%E7%88%BE%E5%95%8F%E9%A1%8C)
  
[PyO3 异常 🎦](https://www.youtube.com/watch?v=UaeOdVwNNpI) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  

该视频讨论了使用 Rust 时，在 Python 中处理异常的问题。在 Rust 中，错误的处理方式不同，它使用响应类型，而 Python 则使用异常。该视频演示了如何通过创建 Python 异常实例并引发它，使用 Rust 在 Python 中引发异常。它还展示了如何通过使用映射函数将 Rust 错误转换为 Python 异常来处理异常。 
  
[Django：将模板标签库移至内置模块中](https://adamj.eu/tech/2023/09/15/django-move-template-tag-library-builtins/)  

Django 的模板引擎有一个未被充分重视的内置选项，该选项可以选择要在每个模板中预加载的库。将库设为内置对象可以避免在使用其标签或过滤器时需要显式使用 {% load %} 标签。将关键库放入内置模块中可以缩短模板并使开发速度更快一些。在这篇文章中，我们将介绍如何向内置模块添加模板库以及如何从模板中删除现有的 {% load %} 标签。
  
[加速 Floyd-Steinberg 抖动：一个优化练习](https://pythonspeed.com/articles/optimizing-dithering/)  

一个已解决实例：优化低层代码以获得显着的性能和内存改进。

> Ele 注：根据google，Floyd-Steinberg抖动算法是图像抖动算法的一种，其思想是将误差传播到邻近的像素点。 Floyd-Steinberg算法思想是将误差传播到邻近的像素点，误差的计算非常简单，即误差为像素点灰度值与该像素点最后取值的差值。
  
[Python 初学者教程（带迷你项目）🎦](https://www.youtube.com/watch?v=qwAFL1597eM) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  

在这个面向初学者的完整课程中学习 Python 编程。本教程自始至终都以迷你项目为特色，因此您可以立即将学到的知识运用到实践中。
  
[快 19 倍的响应时间](https://lincolnloop.com/insights/optimizing-response-time-19x-faster/)  

Lincoln Loop 优化了大型发布平台的数据库性能。总体而言，数据库性能提高了 19 倍。
  
[构建适用于生产的基于 RAG 的 LLM 应用程序（第 1 部分）](https://www.anyscale.com/blog/a-comprehensive-guide-for-building-rag-based-llm-applications-part-1)  

在本指南中，我们将学习如何开发和生产基于检索增强生成 (RAG) 的 LLM 应用程序，关注规模、评估和路由。
  
  
# 好玩的项目，工具和库  
  
[EvoDiff](https://github.com/microsoft/evodiff)   
通过离散扩散模型生成蛋白质序列和进化比对。
  
[every-breath-you-take](https://github.com/kbre93/every-breath-you-take)  
使用 Polar H10 监测仪进行心率变异训练。
  
[Galactic](https://github.com/taylorai/galactic)  
Galicate 为海量非结构化文本数据集提供清理和管理工具。它旨在帮助您管理微调数据集、创建用于检索增强生成 (RAG) 的文档集合，甚至为 LLM 预训练进行网络规模数据集的重复数据删除。
  
[Logparser](https://github.com/logpai/logparser)  
Logparser 为自动化日志解析（结构化日志分析的关键步骤）提供了一个机器学习工具包和基准

[HTTP-Shell](https://github.com/JoelGMSec/HTTP-Shell)  
多平台 HTTP 反向 Shell。
  
[llm-guard](https://github.com/laiyer-ai/llm-guard)  
用于 LLM 交互的安全工具包。
  
[Temporian](https://github.com/google/temporian)  
Temporian 是一个 Python 库，用于机器学习应用中时态数据（例如时间序列、事务）的特征工程和数据增强。

[vpselector](https://github.com/manumerous/vpselector)  
Visual Pandas Selector：可视化及交互式选择时间序列数据。
  
[QuasiQueue](https://github.com/tedivm/quasiqueue)  
QuasiQueue 是一个 Python 多处理库，它使长时间运行的多进程作业变得非常容易。QuasiQueue 处理进程创建和清理、信号管理、跨进程通信以及所有其他让人讨厌处理多处理的糟心玩意。

[PyGraft](https://github.com/nicolas-hbt/pygraft)  
触手可及的模式和知识图可配置生成。

[PYOBD](https://github.com/barracuda-fsh/pyobd)  
开源 obd2 汽车诊断程序。

[reinette-II-plus-dot-py](https://github.com/ArthurFerreira2/reinette-II-plus-dot-py)  
用 Python 实现的 Apple II 模拟器

[PyLLMCore](https://github.com/paschembri/py-llm-core)  
提供轻量级 LLM 的 python 库。
  
[HomeHarvest](https://github.com/ZacharyHampton/HomeHarvest)  
用于房地产抓取的 Python 包，支持 Zillow，Realtor.com 和 Redfin。
  
  
# 最新发布  
  
[Django 5.0 alpha 1 发布](https://www.djangoproject.com/weblog/2023/sep/18/django-50-alpha-1-released/)  
  
[Python 3.12.0 候选版本 3 现已推出](https://pythoninsider.blogspot.com/2023/09/python-3120-release-candidate-3-now.html)  
  
  
# 近期活动和网络研讨会  
  
[2023 年 9 月 PyLadies 伦敦研讨会](https://www.meetup.com/pyladieslondon/events/295976344/)  

将有以下演讲：

  * 外部技术：在工程师和非工程师之间架起桥梁
  * 使用 Django 进行快速原型设计

  
[2023 年 9 月 Python 巴塞罗那研讨会](https://www.meetup.com/python-barcelona/events/296203267/)  

将有以下演讲：

  * 向量化Python表达式中的两种语言问题
  * AI图像生成算法在服装设计中的应用

  
[2023 年 9 月 PyLadies 巴黎研讨会](https://www.meetup.com/pyladiesparis/events/295911409/)  

将有以下演讲：

  * 机器学习和因果推断：寻找新的临床证据？
  * 项目反馈：减少权限管理时间，同时保护您的用户！

  
[2023 年 9 月 VilniusPy 研讨会](https://www.meetup.com/vilniuspy/events/296100222/)  

将有以下演讲：

  * 我们是如何将我们的 Django 测试套件加速 10 倍的
  * Python 生成器：内存占用少，功耗大
  * Deadcode —— 一个查找和修复死的（未使用）Python 代码的工具

  
[2023 年 9 月 PyData 斯德哥尔摩研讨会](https://www.meetup.com/pydatastockholm/events/295730658/)  

将有以下演讲：

  * 如何通过桥接跨领域知识来开发正确的解决方案，从而实现人工智能的价值？
  * 自动化一切：使用 Python 扩展 Voi 的 dbt 功能