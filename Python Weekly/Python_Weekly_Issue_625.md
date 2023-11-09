原文：[Python Weekly - Issue 625](http://eepurl.com/iDALBg)

---

欢迎来到Python周刊第 625 期。让我们直奔主题。
  
  
# 文章，教程和讲座  
  
[在另一个进程内存中苏醒](https://www.youtube.com/watch?v=0ihChIaN8d0) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
在本视频中，我们学习如何通过从头开始构建内存转储器来读取属于其他进程的内存。关键组件之一是 /proc 文件系统：一个内核为内省进程提供的接口。结合 ptrace（一个允许附加并控制另一个进程的系统调用），我们编写了一个程序来自动提取信息（这些信息有可能完全对我们隐藏！）

[构建 Python 编译器和解释器](https://mathspp.com/blog/tag:bpci)  
一个有关在 Python 中从头开始实现 Python 编程语言的系列。本系列的最终目标是探索和尝试实现像 Python 这样的编程语言所需的概念和算法。为此，我们将创建一种具有 Python 功能子集的编程语言，并且在此过程中，我们将使用分词器、解析器、编译器和解释器！
  
[数据库的生成列：Django 和 SQLite](https://www.paulox.net/2023/11/07/database-generated-columns-part-1-django-and-sqlite/)  
介绍数据库的生成列，使用 SQLite 和 Django 5.0 中添加的新的 GeneratedField 字段。
  
[为什么说在 Windows 上，SciPy 为 Python 3.12 构建是一个小奇迹](https://labs.quansight.org/blog/building-scipy-with-flang)   
将 SciPy 迁移到 Meson 意味着在 Windows 上找到不同的 Fortran 编译器，这对于 conda-forge 来说特别棘手。这篇文章讲述了对于 Python 3.12 版本来说，情况是怎样看起来非常严峻的，以及事情如何在关键时刻得到解决的故事。
  
[机器学习软件和 pickles 有什么关系？](https://blog.nelhage.com/post/pickles-and-ml)  
本文讨论了作者对在机器学习生态系统中使用 Python 的 pickle 模块的不断发展的看法。它强调了与 pickle 相关的问题、安全问题和脆弱性，深入了解其广泛使用背后的原因以及它在机器学习领域要解决的挑战。
  
[构建一个人工智能工具即时总结书籍](https://levelup.gitconnected.com/build-an-ai-tool-to-summarize-books-instantly-828680c1ceb4)  
无需从头到尾阅读即可了解任何书籍的要点。
  
[使用 Python 的 bisect 模块可以做的每一件事](https://martinheinz.dev/blog/106)  
了解如何使用“bisect”模块在 Python 中优化搜索并保持数据排序。
  
[构建 Python 数据科学项目的 7 个技巧](https://www.youtube.com/watch?v=xVuqDBCQAYc) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
该视频将介绍简化 Python 数据科学项目结构的 7 个技巧。通过正确的设置和详尽的软件设计，您将能够更有效地修改和增强您的项目。
  
[Python 混淆陷阱](https://checkmarx.com/blog/python-obfuscation-traps/)  
在软件开发领域，开源工具和软件包在简化任务和加速开发过程方面发挥着关键作用。然而，随着社区的发展，想要利用社区的不良行为者的数量也在增加。最近的一个例子涉及开发人员成为看似合法的 Python 混淆包的目标，这些混淆包包含恶意代码。
  
[调试 Django 中的 CSRF 失败 / 403 Forbidden 错误](https://www.better-simple.com/django/2023/11/04/debugging-csrf-error-in-production/)  
指导性深入理解 Django 源代码，以了解应用程序未通过 CSRF 验证的原因。
  
[Django 5.0 中的新功能](https://fly.io/django-beats/new-goodies-in-django-50/)  
本文重点介绍了 Django 5.0 中添加的新功能。
  
[Python 中性能最高的时间戳函数：EXTENDED](https://www.dataroc.ca/blog/most-performant-timestamp-functions-python-2)  
第 2 部分介绍了跨 Python 版本和机器类型的不同时间戳函数性能。获取当前时间的最快方法是什么呢？
  
  
# 好玩的项目，工具和库  
  
[LocalAIVoiceChat](https://github.com/KoljaB/LocalAIVoiceChat)   
使用基于 Zephyr 7B 模型的自定义语音进行本地 AI 对话。使用 RealtimeSTT 和 fast_whisper 进行转录，使用 RealtimeTTS 和 Coqui XTTS 进行合成。
  
[DeepSeek-Coder](https://github.com/deepseek-ai/DeepSeek-Coder)   
让代码自己写代码。
  
[tiger](https://github.com/tigerlab-ai/tiger)  
开源 LLM 工具包，用于构建 LLM 应用程序。TigerRAG（嵌入、RAG）、TigerTune（微调）、TigerArmor（AI 安全）。
  
[lm-format-enforcer](https://github.com/noamgat/lm-format-enforcer)  
强制执行语言模型的输出格式（JSON 模式、正则表达式等）。
  
[autollm](https://github.com/safevideo/autollm)   
在几秒钟内交付基于 RAG 的 LLM Web 应用程序。
  
[lato](https://github.com/pgorecki/lato)  
Python 微框架，用于模块化整体和松散耦合的应用程序。
  
[giskard](https://github.com/Giskard-AI/giskard)  
ML 模型的测试框架，从表格到 LLMs。
  
[RoboGen](https://github.com/Genesis-Embodied-AI/RoboGen)  
一种生成式、自我引导的机器人代理，可以不断地提出和掌握新技能。
  
[error-links](https://pypi.org/project/error-links/)  
在发生异常时向 REPL 添加有用的链接。 
  
[Hexabyte](https://github.com/thetacom/hexabyte)   
一个现代、模块化且强大的 TUI 十六进制编辑器。
  
  
 # 最新发布  
  
[Visual Studio Code 中的 Python – 2023 年 11 月版本](https://devblogs.microsoft.com/python/python-in-visual-studio-code-november-2023-release/)  
本版本包括以下声明：
  * 对终端中 Shift + Enter 运行的改进
  * 已弃用的内置 linting 和格式化功能
  * Python linting 扩展的改进
  * 重新配置测试输出
  * 虚拟环境停用帮助
  * 宣布 VS Code 中的 Python 的发布视频

  
  
# 近期活动和网络研讨会  
  
[旧金山 Python 2023 年 11 月 聚会](https://www.meetup.com/sfpython/events/296321305/)  
将会有以下演讲：
  * 在 Snowflake 中使用 Python、PyTorch、OpenAI 和 Streamlit 进行图像识别
  * 自动照片修饰
  * 咨询数据库和 Dependabot：以及它们如何应用于 Python

  
[Virtual: PyLadies 柏林 2023 年 11 月 聚会](https://www.meetup.com/pyladies-berlin/events/296907222/)  
将会有以下演讲：
  * 赋能未来：德国女孩节
  * 开发大规模图像知识共享数据集

  
[PyLadies 巴黎 2023 年 11 月 聚会](https://www.meetup.com/pyladiesparis/events/297190950/)  
将会有以下演讲：
  * Django 无停机迁移
  * 调试的科学

  
[PyData 阿姆斯特丹 2023 年 11 月 聚会](https://www.meetup.com/pydata-nl/events/297111947/)  
将会有以下演讲：
  * 如何使用机器学习构建出色的风电预测 
  * 使用机器学习为电动汽车智能充电

  
[PyData 布里斯托尔 2023 年 11 月 聚会](https://www.meetup.com/pydata-bristol/events/296142623/)  
将会有以下演讲：
  * 自然语言处理 —— 从学术理论到商业应用
  * TweetNLP

  
[PyData 蒙特利尔 2023 年 11 月 聚会](https://www.meetup.com/pydata-mtl/events/297096826/)  
将会有以下演讲：
  * 人类/人工智能超级团队：通过人机循环学习构建协作系统
  * 大型语言模型简介