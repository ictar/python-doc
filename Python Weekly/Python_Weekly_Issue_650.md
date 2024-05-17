原文：[Python Weekly - Issue 650](http://eepurl.com/iP72qw)

---

欢迎来到《Python周刊》第 650 期。让我们直奔主题。

# 文章，教程和讲座  
  
[工作单元（Unit of Work）设计模式详解](https://www.youtube.com/watch?v=HX6vkP-QD7U) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
该视频解释了工作单元设计模式，对于经常与数据库交互的人来说，这是一个至关重要的概念。这种模式通过累积所有事务并集中执行它们来发挥关键作用。但为什么这是必要的呢？在此视频中找出答案吧。  
  
[同像 Python](https://aljamal.substack.com/p/homoiconic-python)  
本文探讨了编程语言中的同像概念，其中代码和数据是可以互换的，如 Lisp 所示。它提供了“Lisp in Lisp”代码子集的一个 Python 实现，展示了如何通过将代码视为可以操作和执行的数据结构来使 Python 变得同像。  
  
[从头开始创建 DSPy 代理](https://learnbybuilding.ai/tutorials/dspy-agents-from-scratch)  
本文将使用 DSPy，首次从头开始​​创建代理。出于教育目的，以及探索如何使用 DSPy 从头开始构建代理。  
  
[滥用实际上是 Python 表达式的 Conda YAML 注释](https://astrid.tech/2024/02/24/0/conda-recipe-selector-abuse/)  
我最喜欢的构建系统 jinja-preprocessed-eval-preprocessed YAML  
  
[用 Mojo 解析 PNG 图像](https://fnands.com/blog/2024/mojo-png-parsing/)  
这篇文章详细介绍了作者使用 Mojo 编程语言实现 PNG 解析器的经验。它涵盖了所面临的挑战，例如处理无符号 8 位整数，以及将字节转换为字符串，同时探索为此任务编写 Mojo 代码的惯用方法。  
  
[Tezos 区块链开发人员课程 – Python Web3 开发](https://www.youtube.com/watch?v=pHQfw1W7V8s) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
了解如何在 Tezos 上开发分布式应用程序，从设置钱包到有效理解和管理智能合约。该课程涵盖了 Tezos 开发人员必需的各种工具和技术，并重点介绍了支持平台发展的社区驱动创新。    
  
[高影响力 Python Streamlit：漂亮的交互式地图和图表](https://johnloewen.substack.com/p/high-impact-python-streamlit-beautiful)  
一种逐步模块化方法，采用 UNFAO 全球粮食不安全数据。  
  
[如何将 Postgres 用作 Django 的简单任务队列](https://www.youtube.com/watch?v=kNGOcI_qqYo) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
将 Postgres 当作 Django 的任务队列是可以快速添加的。与传统方法（例如 Celery 和 Redis）相比，还有其他很大的优势。  
  
[简介：使用 Python 和 Loguru 进行日志记录](https://www.blog.pythonlibrary.org/2024/05/15/an-intro-to-logging-with-python-and-loguru/)  
Python 的日志记录模块并不是创建日志的唯一方法。您还可以使用几个第三方包。最受欢迎的包之一是 Loguru。 Loguru 打算删除使用 Python 日志记录 API 获得的所有样板文件。您会发现，Loguru 极大地简化了在 Python 中创建日志的过程。  
  
[重塑 Python notebook 的经验教训](https://marimo.io/blog/lessons-learned)  
本文讨论了开发 marimo（一个新的 Python 笔记本环境）时的关键原则和经验教训。它强调忠于项目的可重复性、可维护性和多用途设计的核心支柱，即使面临可能损害这些原则的功能需求。  
  
[实时脑电波可视化：将 Muse EEG 与 Python 集成](https://ai9.notion.site/Real-Time-Brainwave-Visualization-Integrating-Muse-EEG-with-Python-54391bc09bf04b95a117d0fcf41ba351)  
  
[Django：根据子查询获取完整等模型实例](https://blog.bmispelon.rocks/articles/2024/2024-05-09-django-getting-a-full-model-instance-from-a-subquery.html)  
  
  
# 好玩的项目，工具和库  
  
[storm](https://github.com/stanford-oval/storm)  
一个由 LLM 驱动的知识管理系统，用于研究主题并生成带有引文的完整报告。  
  
[Frame](https://github.com/frame-lang/frame_transpiler)  
Frame 是一种 Markdown 语言，用于在 Python 中创建状态机（自动机）以及生成 UML 文档。  
  
[Pipecat](https://github.com/pipecat-ai/pipecat)  
用于语音和多模式会话 AI 的开源框架。  
  
[UXsim](https://github.com/toruseo/UXsim)  
道路网络中的车辆交通流模拟器，纯Python编写。  
  
[itrm](https://gitlab.com/davidwoodburn/itrm)  
该库提供了多个函数，可以将数据很好地打印到终端。    
  
[drf-api-action](https://github.com/Ori-Roza/drf-api-action)  
利用 action_api 夹具提升了 Django Rest Framework 的测试，将 REST 端点测试简化为无缝、类似函数的体验。  
  
[TimesFM](https://github.com/google-research/timesfm)  
TimesFM（时间序列基础模型）是 Google Research 开发的用于时间序列预测的预训练时间序列基础模型。  
  
[MindNLP](https://github.com/mindspore-lab/mindnlp)  
基于MindSpore的易于使用的高性能NLP和LLM框架，兼容Huggingface的模型和数据集。  
  
[llmware](https://github.com/llmware-ai/llmware)  
提供企业级基于LLM的开发框架、工具和微调模型。  
  
  
# 近期活动和网络研讨会  
  
[Hybrid: PyMunich 2024 年 5 月聚会](https://www.meetup.com/pymunich/events/299650733/)  
将会有以下演讲：
* 利用 Streamlit 成为数据故事讲述者
* 自动发布技术文档（Git-to-Confluence）
* 融合视角：为当今项目中的不同团队编写干净的 Python 代码
  
[PyLadies Paris 2024 年 5 月聚会](https://www.meetup.com/pyladiesparis/events/300641629/)  
将有一场演讲：从 ML 模型调试到机器人检测 - Sliceline 的故事。  
  
[Python Barcelona 2024 年 5 月聚会](https://www.meetup.com/python-barcelona/events/300940146/)  
将会有以下演讲：
* 在 AI 中使用 OpenCV：Python 中的实用图像处理
* cluster-experiments，一个用于设计 AB 测试的模拟库

  
[BAyPIGgies 2024 年 5 月聚会](https://www.meetup.com/baypiggies/events/300647354/)  
将会有以下演讲：
* 使用 Pandas 和 Pyspark 对仪器数据进行时间序列处理。
* 使用 Reflex 开发全栈 Python Web 应用程序

  
[PyData Berlin 2024 年 5 月聚会](https://www.meetup.com/pydata-berlin/events/300633031/)  
将会有以下演讲：
* 超越连续体：深度学习中量化的重要性
* 思想碰撞