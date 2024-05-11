原文：[Python Weekly - Issue 649](http://eepurl.com/iPGqBA)

---

欢迎来到《Python周刊》第 649 期。让我们直奔主题。 


# 文章，教程和讲座  
  
[ByteWax：当 Rust 当研究遇上 Python 的实用性](https://www.youtube.com/watch?v=ZRWun2MjTEg) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
Bytewax 是一种奇怪的流处理工具，它将 Python 表面与 Rust 核心混合在一起，产生与 Kafka Streams 或 Apache Flink 类似的东西，但实现方式截然不同。本周我们将了解它的作用、工作原理以及 Python 和 Rust 的结合在实践中如何工作。  
  
[Python Asyncio 的工作原理：从头开始重新创建它](https://jacobpadilla.com/articles/recreating-asyncio)  
通过使用 Python 生成器从头开始重新创建 asyncio 并使用 async/await 关键字的 __await__ 方法来了解 asyncio 的工作原理。
  
  
[使用不安全的 Python 将速度提高一百倍](https://yosefk.com/blog/a-100x-speedup-with-unsafe-python.html)  
我们将使用“不安全的 Python”将一些 numpy 代码的执行速度提高 100 倍。这与不安全的 Rust 不太一样，但有点相似，我不知道还能叫它什么……你会看到的。它不是您在大多数 Python 代码中使用的东西，但有时它很方便，而且我认为它从一个有趣的角度展示了“Python 的本质”。  
  
[AsyncIO 和事件循环解释](https://www.youtube.com/watch?v=RIVcqT2OGPA) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
多年来，我制作了好几个关于 AsyncIO 的视频。然而，今天，我将采用一种新方法，即深入解释事件循环。我将更深入异步编程，特别关注事件循环在幕后的工作原理  
  
[LLM 的工作原理（数学含量为零）](https://blog.miguelgrinberg.com/post/how-llms-work-explained-without-math)  
我认为很多人对 GenAI 革命的一个基本问题是，这些模型的明显智能从何而来呢。在本文中，我将尝试用简单的术语而不是使用高级数学来解释通用文本模型的工作原理，从而帮助您将它们视为计算机算法，而不是魔法。  
  
[会让你惊叹不已的简单交互式 Python Streamlit 地图](https://johnloewen.substack.com/p/simple-interactive-python-streamlit)  
通过来自 NASA GIS 的数据集来讲述森林火灾统计数据  
  
[掌握 Python 和 Zoom API｜构建转录录音的服务器到服务器应用](https://www.youtube.com/watch?v=sQVliRl5uKw) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
在本分步指南中了解如何通过 Python 使用 Zoom 的 API！在本教程中，您将学习如何创建强大的服务器到服务器 OAuth 应用程序，该应用程序会自动转录您的 Zoom 录音，然后将其直接打印到您的终端，并将其另存为文本文件。本教程非常适合开发人员，它将引导您从设置到执行，因此在视频结束时，您将拥有一个功能齐全的应用程序。  
  
[全同态加密的高级技术概述](https://www.jeremykun.com/2024/05/04/fhe-overview/)  
本文提供了关于全同态加密 (FHE) 的高级技术概述，这是一种功能强大的加密技术，允许对加密数据执行计算而无需先解密。它讨论了一些正在积极开发的关键 FHE 库和工具。  
  
[“请注意！”：注意力机制的视觉指南](https://codecompass00.substack.com/p/visual-guide-attention-mechanism-transformers)  
培养注意力背后的直觉：为什么它接管了机器学习+LLM，以及它实际上做了什么。  
  
  
# 好玩的项目，工具和库  
  
[gpt-home](https://github.com/judahpaul16/gpt-home)  
家里的 ChatGPT！基本上是一个更好的 Google Nest Hub 或 Amazon Alexa 家庭助理。使用 OpenAI API 在 Raspberry Pi 上构建。  
  
[Logfire](https://github.com/pydantic/logfire)   
适用于 Python 及其他领域的简单可观察性！  
  
[PgQueuer](https://github.com/janbjorge/PgQueuer)  
PgQueuer 是一个利用 PostgreSQL 实现高效作业排队的 Python 库。  
  
[relax-py](https://github.com/crpier/relax-py)  
用于 htmx 和 tailwindcss 的 Python Web 开发框架，具有热模块替换、URL 定位器、依赖项注入，由静态类型支持，构建在 Starlette 之上。  
  
[VILA](https://github.com/Efficient-Large-Model/VILA)  
一种多图像视觉语言模型，具有训练、推理和评估配方，可从云端部署到边缘（Jetson Orin 和笔记本电脑）。  
  
[prometheus-eval](https://github.com/prometheus-eval/prometheus-eval)  
使用 Prometheus 评估您的 LLM 的回应。  
  
[LlamaParse](https://github.com/run-llama/llama_parse)  
LlamaParse 是由 LlamaIndex 创建的 API，用于使用 LlamaIndex 框架高效地解析和表示文件，以实现高效检索和上下文增强。  
  
[fastapi-cli](https://github.com/tiangolo/fastapi-cli)  
使用 FastAPI CLI，从命令行运行和管理 FastAPI 应用程序。   
  
[Bytewax](https://github.com/bytewax/bytewax)  
Bytewax 是一个简化事件和流处理的 Python 框架。  
  
[django-harlequin](https://github.com/adamchainz/django-harlequin)  
使用 Django 数据库配置启动 Harlequin，终端的 SQL IDE。  
  
[LeRobot](https://github.com/huggingface/lerobot)  
适用于现实世界机器人的最先进的机器学习。  
  
[Panza](https://github.com/IST-DASLab/PanzaMail)  
个人电子邮件助理，经过训练并在设备上运行。  
  
[DrEureka](https://eureka-research.github.io/dr-eureka/)  
语言模型引导的模拟到真实的迁移。  
  
[SATO](https://sato-team.github.io/Stable-Text-to-Motion-Framework/)  
稳定的文本转动画框架。  
  
  
# 最新发布  
  
[pip 24.1 beta](https://pip.pypa.io/en/latest/news/#b1-2024-05-06)  
  
[Django 问题修复版本已发布：5.0.6 和 4.2.13](https://www.djangoproject.com/weblog/2024/may/07/bugfix-releases/)  
  
  
# 近期活动和网络研讨会  
  
[Django London 2024 年 5 月聚会](https://www.meetup.com/djangolondon/events/300467704/)  
将会有以下演讲：
* 用于大规模协作的分层 Django 项目结构
* 蛇中有蟹！

[PyLadies Berlin 2024 年 5 月聚会](https://www.meetup.com/pyladies-berlin/events/299598103/)  
将会有以下演讲：
* 嵌套娃娃效应：Python 中的范围
* `GridSearchCV` 中基于收入的评分：scikit-learn 中关于新元数据路由的一个案例

[PuPPy 2024 年 5 月聚会](https://www.meetup.com/psppython/events/300461407/)  
将会有一场演讲：编写 Python 的最佳语言是 Rust。  
  
[PyBerlin 2024 年 5 月聚会](https://www.meetup.com/pyberlin/events/291577883/)  
将会有以下演讲：
* 仅使用 Python，在 Web 上部署数据项目
* magic-di 简介：通过依赖注入器提升 Python
  
[PyData Sudwest 2024 年 5 月聚会](https://www.meetup.com/pydata-suedwest/events/299870597/)  
将会有以下演讲：
* Kickstart 大规模编码：项目模板自动化是如何释放开发人员的生产力的
* Dask DataFrame 现在速度很快啦
