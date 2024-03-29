原文：[Python Weekly - Issue 644](http://eepurl.com/iMZAHE)

---

欢迎来到《Python周刊》第 644 期。让我们直奔主题。 

# 文章，教程和讲座  
  
[设计一个纯 Python Web 框架](https://reflex.dev/blog/2024-03-21-reflex-architecture/)  
本文解释了Reflex（一个纯 Python 的 Web 框架），是如何让用户可以在无需学习新语言的情况下构建网络应用的。它详细介绍了 Reflex 都工作原理，重点介绍了其将 UI 编译为 JavaScript 的独特方法，同时在服务器上使用 Python 保留应用程序逻辑和状态管理。  
  
[修复 PyPy 增量垃圾收集器中的一个错误](https://www.pypy.org/posts/2024/03/fixing-bug-incremental-gc.html)  
本文讨论了作者是如何修复 PyPy 增量垃圾收集器中的一个错误的，这个错误在 CI 环境中 pytest 的 AST 重写阶段会导致崩溃。它详细介绍了 PyPy 增量 GC 的技术背景，以及在写屏障实现中发现和解决的具体问题。  
  
[针对执行速度慢的函数调用的一个更好的 Python 缓存](https://docs.sweep.dev/blogs/file-cache)  
本文介绍了一种 Python 文件缓存，它将函数值存储在文件中而不是存储在内存中，从而提供了更持久的缓存解决方案。通过使用 file_cache 装饰器，开发人员可以节省运行 LLM 基准测试等函数的时间并将缓存作为 Python 的模块共享，从而提高性能。  
  
[Django：关于优化系统检查框架的文章](https://adamj.eu/tech/2024/03/23/django-optimizing-system-checks/)  
这篇文章讨论了对 Django 系统检查框架的优化，该框架因速度慢而闻名。这些优化将在示例客户端项目上的检查操作运行时间从 37 毫秒减少到 18 毫秒，共减少了 50%。  
  
[八分钟 Python Poetry](https://www.youtube.com/watch?v=Ji2XDxmXSOM) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
该视频将指导您歇息管理 Python 虚拟环境的方方面面，同时还向您介绍了 Poetry。您将学习大量技巧和策略，以更有效地管理您的项目。  
  
[伪造短信：兔子洞到底有多深？](https://medium.com/@aleksamajkic/fake-sms-how-deep-does-the-rabbit-hole-really-go-17e25c42f986)  
通过模糊的恶意软件代码迷宫追踪不良行为者。  
  
[使用 Numba 加速代码的错误方式](https://pythonspeed.com/articles/slow-numba/)  
Numba 可以使您的数字代码更快，但前提是您正确使用它。  
  
[不必要的 else 语句](https://www.pythonmorsels.com/unnecessary-else-statements/)  
让我们来谈谈 Python 中不必要的 else 语句。  
  
[使用 Apache Superset 和 PostgreSQL 实现数据可视化](https://www.timescale.com/blog/data-visualization-in-postgresql-with-apache-superset/)  
正在寻找适用于 PostgreSQL 的数据可视化工具吗？我们讨论了一些选项，并提供了有关 PostgreSQL 和 Apache Superset 的分步指南。  
  
[为移动开发人员构建 Django API](https://www.youtube.com/playlist?list=PLgRx2Eap1Wm2W-ozbwAZwffEwTTy8xS5g) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
深入了解应用程序开发的后端，重点关注使用 Django 和 Django Rest Framework 来构建强大的 API。  
  
[安全的 LLM 架构 —— 测试 LLM Guard](https://www.youtube.com/watch?&v=C_5KRqQrGD4) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
该视频探讨了 LLM 架构，解决了安全问题并批评了无效的安全工具。它介绍了 LLM Guard，这是一种开源工具，旨在通过检查输入是否存在恶意意图和输出是否敏感数据来增强 LLM 的安全性，并通过实际示例对其进行了演示，还强调输出监控和权限在保护数据方面的重要性。  
  
[谓词下推（predicate pushdown）的威力](https://pola.rs/posts/predicate-pushdown-query-optimizer/)  
谓词下推（Predicate pushdown）是查询引擎最重要的优化之一。通过本文了解更多相关信息。  
  
[带原生 Python 扩展和 Dispatch 的分布式协程](https://stealthrocket.tech/blog/distributed-coroutines-in-python/)  
本文讨论了分布式协程是如何与 Dispatch 等分布式调度程序配合使用的，从而通过允许函数在另一个进程中挂起、序列化和恢复来简化可扩展且可靠的软件的创建。它重点介绍了分布式协程如何利用 Python 对协程和异步函数的原生支持，使用常规编程语言结构和控制流对动态工作流程进行编码。  
  
[AWS Lambda + Bedrock 教程](https://www.youtube.com/watch?v=vQ9BUc-UmXY) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
了解如何使用 AWS Lambda 从 AWS Bedrock 调用 API。本视频手把手教您使用Python和boto3的Bedrock客户端调用InvokeModel API。它还向您展示设置 IAM 权限并修改 Lambda 超时值以应对 Bedrock 的缓慢响应的方法。   
  
  
# 好玩的项目，工具和库  
  
[open-interpreter](https://github.com/OpenInterpreter/open-interpreter)  
计算机自然语言界面。  
  
[Devika](https://github.com/stitionai/devika)  
Agentic 人工智能软件工程师。 Devika 的目标是成为 Cognition AI 开发的 Devin 的有竞争力的开源替代品。  
  
[T-Rex](https://github.com/IDEA-Research/T-Rex)  
通过文本-视觉提示协同，实现通用物体检测。  
  
[OpenDevin](https://github.com/OpenDevin/OpenDevin)  
一个旨在复制 Devin 的开源项目，Devin 是一位自主人工智能软件工程师，能够执行复杂的工程任务并在软件开发项目上与用户积极协作。  
  
[VoiceCraft](https://github.com/jasonppy/VoiceCraft)  
零次语音编辑和文本转语音。  
  
[lightning-thunder](https://github.com/Lightning-AI/lightning-thunder)  
让 PyTorch 模型快如闪电！ Thunder 是一个针对 PyTorch 的源到源编译器。它允许同时使用不同的硬件执行器。
 
  
[reverser_ai](https://github.com/mrphrazer/reverser_ai)  
过在消费类硬件上使用本地大语言模型 (LLM)，提供自动化逆向工程协助。  
  
[Leaping](https://github.com/leapingio/leaping)  
Leaping 的 pytest 调试器是一个简单、快速、轻量级的 Python 测试调试器。 Leaping 跟踪代码的执行，并允许您使用基于 LLM 的自然语言调试器，随时追溯检查程序的状态。
  
[Tracecat](https://github.com/TracecatHQ/tracecat)  
Tines / Splunk SOAR 的 AI 原生开源替代品。 
  
[rag-search](https://github.com/thinkany-ai/rag-search)  
RAG 搜索 API。  
  
[FeatUp](https://mhamilton.net/featup.html)  
一个与模型无关的框架，适用于任何分辨率的特征。
  
  
# 近期活动和网络研讨会  
  
[PyData London 2024 年 4 月聚会](https://www.meetup.com/pydata-london-meetup/events/299970694/)  
将会有以下演讲：
  * 构建检索增强生成 (RAG) 支持的应用程序
  * 将 ML 模型从研究转移到生产时，让 Python 成为一种可能。深入探讨开放神经网络交换 (ONNX)
  
[Michigan Python 2024 年 4 月聚会](https://www.meetup.com/michigan-python/events/299683479/)  
将会有一场演讲：使用 GeoPandas 对空间数据进行可视化。 
  
[PyData Amsterdam 2024 年 4 月聚会](https://www.meetup.com/pydata-nl/events/299967889/)  
将会有以下演讲：
  * 欺诈与否：听起来很简单，对吧？
  * 使用 OSS Metaflow 构建 GenAI 和 ML 系统
   
[PyData Tel Aviv 2024 年 4 月聚会](https://www.meetup.com/pydata-tel-aviv/events/299310820/)  
将会有以下演讲：
  * 投标还是不投标 —— 针对实时投标的强化学习
  * 使用 Rebuff 保护 LangChain 应用程序以免受即时注入
  * Polars是Pandas杀手
