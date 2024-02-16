原文：[Python Weekly - Issue 638](http://eepurl.com/iJ9pdc)

---

欢迎来到《Python周刊》第 638 期。让我们直奔主题。

  
# 文章，教程和讲座  
  
[为什么 AI 会遇到 Python 问题](https://www.youtube.com/watch?v=cGgTvMmtzNU) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
人工智能 (AI) 已将 Python 变得前所未有的流行，使其成为全球开发人员和研究人员的首选语言。然而，繁荣背后，一个巨大的挑战隐而未现。让我们通过现实世界的示例和技术见解来了解 Python 给人工智能发展带来的具体困难。 
  
[Meta 超爱 Python 的](https://engineering.fb.com/2024/02/12/developer-tools/meta-loves-python/) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/9a9a57d0-eb4b-47f8-8af4-55ba50e8c350.png)  
Meta 工程师讨论了他们对 Python 3.12 的贡献，包括自定义 JIT hook、永久对象、类型系统改进和更快的理解等新功能，强调了他们与 Python 社区的合作以及公司对开源计算的支持 
  
[计算 Python 中使用的 CPU 指令](https://blog.mattstuchlik.com/2024/02/08/counting-cpu-instructions-in-python.html)  
您知道用 Python 打印（“Hello”）需要大约 17,000 个 CPU 指令吗？而导入 Seaborn 则需要大约 20 亿个？  
  
[不仅仅是 NVIDIA：可以在任何地方运行的 GPU 编程](https://pythonspeed.com/articles/gpu-without-cuda/)  
如果您想在 CI、Mac 等设备上运行 GPU 程序，wgu-py 是一个不错的选择。  
  
[部署模型的多种方法](https://outerbounds.com/blog/the-many-ways-to-deploy-a-model)  
部署模型和执行推理的方法有很多种。在这里，我们以 LLM 推理为例分享模型部署的决策准则。  
  
[Adam 优化器背后的数学](https://towardsdatascience.com/the-math-behind-adam-optimizer-c41407efe59b)  
为什么 Adam 是深度学习中最受欢迎的优化器？让我们通过深入研究其数学并重新创建算法来理解它。  
  
[可视化神经网络内部结构](https://www.youtube.com/watch?v=ChfEO8l-fas) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
可视化神经网络在训练和推理过程中的一些内部结构。  
  
[LLM 应用开发的工程实践](https://martinfowler.com/articles/engineering-practices-llm.html)  
LLM 工程涉及的不仅仅是 prompt 设计或 prompt 工程。在本文中，我们分享了一组工程实践，帮助我们在最近的项目中快速可靠地交付原型 LLM 应用程序。我们将分享 LLM 应用程序的自动化测试和对抗性测试、重构技术，以及构建 LLM 应用程序和负责任的 AI 所需要注意的事项。  
  
[Python 的 textwrap 模块可以做到的一切](https://martinheinz.dev/blog/108)  

Python 有许多用于格式化字符串和文本的选项，包括 f 字符串、format() 函数、模板等。然而，有一个模块很少有人知道，它叫做 textwrap。该模块是专门为帮助您进行换行、缩进、修剪等操作而构建的，在本文中我们将向您介绍您可以使用它来完成的所有操作。  
  
[如果可以的话，如何避免在 Django 中进行计数查询](https://www.peterbe.com/plog/how-to-avoid-a-count-query-in-django-if-you-can)  
  
[像专家一样处理 Asyncio 中的任务](https://jacobpadilla.com/articles/handling-asyncio-tasks)  
  
  
# 好玩的项目，工具和库  
  
[modguard](https://github.com/Never-Over/modguard)  
一个用于强制执行模块化、解耦包架构的 Python 工具。  
  
[metavoice-src](https://github.com/metavoiceio/metavoice-src)  
像人一样的富有表现力的 TTS 的基础模型。  
  
[logot](https://github.com/etianen/logot)  
测试您的代码是否正确进行了日志记录。  
  
[TriOTP](https://github.com/linkdd/triotp)  
Python Trio 的 OTP 框架。  
  
[Toolong](https://github.com/textualize/toolong)  
用于查看、追踪、合并和搜索日志文件（以及 JSONL）的终端应用程序。 
  
[django-queryhunter](https://github.com/PaulGilmartin/django-queryhunter)  
寻找 Django 应用程序代码中负责执行最多次查询的行。  
  
[Lag-Llama](https://github.com/time-series-foundation-models/lag-llama)  
面向概率时间序列预测的基础模型。  
  
[HypoFuzz](https://github.com/Zac-HD/hypofuzz)  
用于 Python 最佳测试工作流程的开源智能模糊测试。  
  
[mwmbl](https://github.com/mwmbl/mwmbl)  
一个用 Python 实现的开源、非盈利搜索引擎。  
  
[instld](https://github.com/pomponchik/instld)  
最简单的包管理。  
  
  
 # 近期活动和网络研讨会  
  
[PyLadies Dublin 2024 年 2 月聚会](https://www.meetup.com/pyladiesdublin/events/298929924/)  
将会有一场演讲：当网络安全碰上 Python。  
  
[Spokane Python 2024 年 2 月聚会](https://www.meetup.com/python-spokane/events/298213203/)  
将会有一场演讲：介绍如何通过使用 PyO3 创建 Rust 绑定，从而将 Rust 集成到您的 Python 工作流程中。
  
[Python Barcelona 2024 年 2 月聚会](https://www.meetup.com/python-barcelona/events/299074873/)  
将有以下演讲：
  * Pytest，短途远足。
  * 《查询地图（Queering The Map）》的使用与话语分析

  
[PyData Southampton 2024 年 2 月聚会](https://www.meetup.com/pydata-southampton/events/298595661/)  
将有以下演讲：
  * 将 3D 及以上的地理空间数据与 TileDB 数组相结合
  * 利用 GPU 计算搜索太空中的伽马射线源

  
[PyData Berlin 2024 年 2 月聚会](https://www.meetup.com/pydata-berlin/events/298730602/)  
将有以下演讲：
  * 通过基于扩散的图神经网络利用数据结构和几何
  * 大型语言模型的采样策略示例

  
[PyData Stockholm 2024 年 2 月聚会](https://www.meetup.com/pydatastockholm/events/299095628/)  
将有以下演讲：
  * 爬取 130 万条房价信息 —— 并且逃脱惩罚
  * BYOSC：
  * 交通地图
  * DeLight - 延误航班预测器
  * 这张照片是在哪里拍摄的？ - 深度学习时代的视觉地理定位。
