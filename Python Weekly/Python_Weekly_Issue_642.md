原文：[Python Weekly - Issue 642](http://eepurl.com/iL4r1k)

---

欢迎来到《Python周刊》第 642 期。让我们直奔主题。


# 新闻  
  
[DjangoCon US 2024 CFP](https://pretalx.com/djangocon-us-2024/cfp)  
DjangoCon US 2024 CFP 现已开放。请在美国东部时间 2024 年 4 月 24 日中午 12 点之前提交您的演讲或教程提案。  
  
  
# 文章，教程和讲座  
  
[为了50,000美元，我们黑进了谷歌A.I.](https://www.landh.tech/blog/20240304-google-hack-50000)  
这篇文章讨论了作者参加拉斯维加斯的一个黑客活动的经历，在活动中他们发现了漏洞，从而成功黑进了谷歌。尽管最初取得了成就，但谷歌VRP团队延长了比赛截止日期，以鼓励更多创造性的发现，突显了网络安全领域不断面临的挑战和机遇。  
  
[为什么说在 2024 年你应该使用 Pydantic](https://www.youtube.com/watch?v=502XOB0u8OY) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
在这个更新的Pydantic教程中，我将介绍所有新功能及其给项目带来的好处。尽管Python的动态类型系统是用户友好的，但它并非没有数据处理问题。这就是Pydantic发挥作用的地方，它为无缝数据管理提供了必要的结构和验证。  
  
[GGUF，迂回的长途](https://vickiboykis.com/2024/02/28/gguf-the-long-way-around/)  
这是一篇关于GGUF的文章，它是一种用于机器学习模型的文件格式。文章讨论了什么是机器学习模型以及它们是如何生成的。  
  
[实践中的Python Gevent：需要牢记的常见陷阱](https://upsun.com/blog/python-gevent-best-practices/)  
在这篇文章中，了解使用异步Python库Gevent的常见陷阱，以及如何解决这些问题。  
  
[使用Collectfasta加速Django的collectstatic命令](https://jasongi.com/2024/03/04/speed-up-djangos-collectstatic-command-with-collectfasta/)  
这篇文章介绍了Collectfasta，这是Collectfast的更新版本，旨在提高Django的collectstatic命令的性能。通过优化存储库并提高性能，Collectfasta提供了比标准Django命令更快的执行和效率，为寻求提高Django项目性能的开发人员提供了有价值的工具。  
  
[创建一个基于机器学习的NCAA比赛预测模型](https://www.youtube.com/watch?v=cHtAEWkvSMU) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
深入探讨机器学习和人工智能的迷人世界，我们将指导您开发一个旨在预测NCAA锦标赛结果的模型。从初始设置到最终预测，我们将覆盖您创建自己强大模型所需的一切内容。  
  
[使用Win32 App隔离为Python创建沙箱环境](https://blogs.windows.com/windowsdeveloper/2024/03/06/sandboxing-python-with-win32-app-isolation/)   
这篇文章探讨了为Python中创建沙箱环境的挑战和好处，特别是在网站执行用户代码或防止针对大型语言模型的攻击等情况下。Win32 App隔离提供了一种在应用程序/操作系统级别隔离Python的独特方法，它创建了一个安全边界来防止应用程序破坏操作系统。  
  
[使用LLMs生成模糊生成器的](https://verse.systems/blog/post/2024-03-09-using-llms-to-generate-fuzz-generators)  
这篇文章探讨了大型语言模型（LLMs）在生成库API模糊驱动程序方面的有效性。它讨论了基于LLM的模糊驱动程序生成的挑战和好处，突出了其实用性、复杂API使用的策略以及基于全面研究和评估的改进方向。  
  
[Python中的时间序列分析指南](https://www.timescale.com/blog/how-to-work-with-time-series-in-python/)  
探讨了 Python 为何是进行时间序列分析的好语言。另外，还有一些入门建议。  
  
[使用MediaPipe和TensorFlow Lite在设备上进行的大型语言模型](https://developers.googleblog.com/2024/03/running-large-language-models-on-device-with-mediapipe-andtensorflow-lite.html)  
文章讨论了实验性的MediaPipe LLM推理API的发布，该API使大型语言模型（LLMs）能够完全在设备上跨平台运行。这一变革性功能解决了LLMs的显著内存和计算需求，这些需求是传统设备上模型的100多倍，通过新的运算、量化、缓存和权重共享等优化实现。
  
[Homebrew 所有 Python 的东西](https://blog.davep.org/2024/03/10/homebrew-all-the-python-things.html)  
  
[不安全感和 Python 对象序列化](https://lwn.net/SubscriberLink/964392/498a12fe44f51139/)  
  
[理解上下文管理器及其语法糖](https://bjoernricks.github.io/posts/python/context-manager/)  
  
[Python 有指针嘛？](https://nedbatchelder.com/blog/202403/does_python_have_pointers.html)  
  
  
 # 好玩的项目，工具和库  
  
[openllmetry](https://github.com/traceloop/openllmetry)  
LLM 申请的开源可观察性。  
  
[CBScript](https://github.com/SethBling/cbscript)  
CBScript 是一种转译语言，由 SethBling 设计。该编译器会将 CBScript 文件编译为 Minecraft 数据包 zip 文件。它具有许多 Minecraft 命令级别所不存在的高级语言功能。  
  
[SQLMesh](https://github.com/TobikoData/sqlmesh)   
向后兼容 dbt 的高效数据转换和建模框架。  
  
[Dataverse](https://github.com/UpstageAI/dataverse)  
数据宇宙。关于数据、数据科学和数据工程。  
  
[LlamaGym](https://github.com/KhoomeiK/LlamaGym)  
利用在线强化学习微调 LLM 代理。  
  
[chedule-texts-from-txt](https://github.com/reidjs/schedule-texts-from-txt)  
根据 .txt 文件安排 iMessage 或 SMS 文本。  
  
[fructose](https://github.com/bananaml/fructose)  
像调用强类型函数那样调用 LLM。  
  
[R2R](https://github.com/SciPhi-AI/R2R)  
快速开发和部署生产可用的 RAG 系统的框架。  
  
[python-docstring-highlighter](https://github.com/rodolphebarbanneau/python-docstring-highlighter)  
VSCode 中 Python 文档字符串的语法高亮显示。  
  
[Ludic](https://github.com/paveldedik/ludic)  
纯 Python 方式构建 HTML 页面的轻量级框架。  
  
  
  
# 最新发布  
  
[Python 3.13.0 alpha 5 现已发布](https://pythoninsider.blogspot.com/2024/03/python-3130-alpha-5-is-now-available.html)  
  
  
# 近期活动和网络研讨会  
  
[PyLadies London 2024 年 3 月聚会](https://www.meetup.com/pyladieslondon/events/299658808/)  
将会有以下演讲：
  * 指导职业道路决策
  * Spotify 是如何通过机器学习个性化您的搜索结果的

  
[Python Barcelona 2024 年 3 月聚会](https://www.meetup.com/python-barcelona/events/299261127/)  
将会有以下演讲：
  * Python 和 Rust
  * 动手操作 Polars，Rust 中数据框架的替代方案

  
[BayPIGgies 2024 年 3 月聚会](https://www.meetup.com/baypiggies/events/299305900/)   
将会有以下演讲：
  * SRE 的趣味世界
  * 更好地结合起来：释放 Pandas、Polars 和 Apache Arrow 的协同作用
  * 模拟直到成功：如何在不离开单元测试的情况下验证您的外部模拟

  
[PyData Stockholm 2024 年 3 月聚会](https://www.meetup.com/pydatastockholm/events/299375069/)   
将会有以下演讲：
  * 微调您自己的 Stable Diffusion 模型 - 包括提示和技巧
  * 实际应用中的人工智能工具：Polestar 是如何利用 LLM 增强客户体验的

  
[PyData Southampton 2024 年 3 月聚会](https://www.meetup.com/pydata-southampton/events/298930338/)   
将会有以下演讲：
  * Python 支持的现代数据堆栈
  * 讲透 Transformer
  * 使用脑电图评估认知工作量
  * 时间序列数据库基准测试
  
[PyData Ireland 2024 年 3 月聚会](https://www.meetup.com/pydataireland/events/299173763/)  
将会有一场演讲：生成式人工智能企业格局——过去、现在和未来。  
  
[PyData Paris 2024 年 3 月聚会](https://www.meetup.com/pydata-paris/events/299457555/)  
将会有以下演讲：
  * 仅用 Python，将你的数据项目部署在网上
  * 充分利用 scikit-learn 分类器：可信概率和最优二元决策
