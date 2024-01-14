原文：[Python Weekly - Issue 633](http://eepurl.com/iHOxDc)

---

欢迎来到《Python周刊》第 633 期。让我们直奔主题。 

# 文章，教程和讲座  
  
[Python 3.13 有 JIT 啦](https://tonybaloney.github.io/posts/python-gets-a-jit.html)  
2023 年 12 月下旬的时候，CPython 核心开发人员 Brandt Bucher 向 Python 3.13 分支提交了一个小的 Pull 请求，添加了 JIT 编译器。这一更改一旦被接受，就将是自 Python 3.11 中添加专用自适应解释器（pecializing Adaptive Interpreter）以来，CPython 解释器最大的更改之一。在这篇文章中，我们将了解这个 JIT，它是什么、它如何工作以及有什么好处。  
  
[通过一个共享库集成 Rust 和 Python](https://www.youtube.com/watch?v=K9dUqGyQ9pw) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
了解如何在用 Rust 构建计算和内存密集型函数，并将它们无缝集成到 Python 脚本中。该视频内容涵盖了使用 pyo3 利用 Rust 创建共享 Python 模块、将 Rust 代码编译到共享对象文件中、在 Python 中导入该文件以及从 Python 调用高性能的 Rust 函数。  
  
[保护你的 Flask 应用的最佳实践](https://escape.tech/blog/best-practices-protect-flask-applications/)  
通过我们的专家实践教程，学习如何有效保护您的 Flask 应用程序。只需几个步骤即可增强项目的安全性！  
  
[Knuckledragger：实验 Python 证明助手](https://www.philipzucker.com/python-itp/)  
我正在修改的东西是，用 python 制作一个证明助手。  
  
[CI 上 Scrapy 链接验证的乐趣](https://www.mattlayman.com/blog/2024/fun-scrapy-validation-ci/)  
如何自动确保站点内链接到其他内部页面的所有链接继续有效？在本文中，我们将了解如何使用 Scrapy（一种网页抓取工具）和 GitHub Actions（一种持续集成系统）来实现此目标。  
  
[使用格式化程序和 linter 管理大型代码库](https://tech.octopus.energy/news/2024/01/05/linting-and-formatting.html)  
在 Kraken Tech，我们拥有一支由 500 多名开发人员组成的大型全球开发团队，其中大多数人都在包含超过 400 万行 Python 代码的单体代码库上工作。我们每天发布新代码超过 100 次，在此过程中运行数十万次测试。那么，在变更如此频繁的情况下，要如何在分布式团队中确保高编码质量呢？此外，我们如何才能让新加入者更容易地加入并做出贡献？   
  
[深入探讨 Python 的 functools.wraps 装饰器](https://jacobpadilla.com/articles/Functools-Deep-Dive)  
本文讨论了 Python 中 functools.wraps 装饰器的重要性。它解释了在将一个对象包装在另一个对象上时，特别是在开发 Python 装饰器时，装饰器是如何帮助保留元数据的。作者强调了这个装饰器的重要性，因为封装对象可能会丢失有价值的元数据。   
  
[纯 Python 中的 SIMD](https://www.da.vidbuchanan.co.uk/blog/python-swar.html)  
这篇文章探讨了 Python 环境下的 SWAR（寄存器中的 SIMD）及其变体 SWAB（Bigint 中的 SIMD）的概念。讨论了 SWAB（最大化每条 VM 指令完成的工作量）是如何在 Python 中发挥作用、减少解释器开销、并允许 CPU 将大部分时间花在实现整数运算的快速本机代码上。  
  
[实验《纽约时报填字游戏》上的手写识别](https://open.nytimes.com/experimenting-with-handwriting-recognition-for-new-york-times-crossword-a78e08fec08f)  
本文讨论了 MakerWeek 2023 黑客马拉松期间，针对《纽约时报填字游戏》应用程序中手写识别的探索。该项目涉及在 iOS 和 Android 平台上实现用于手写识别的设备端机器学习，它具有诸如“乱写乱画”检测和应用内自我训练机制​​等交互功能的潜力。  
  
[Viberary 回顾](https://vickiboykis.com/2024/01/05/retro-on-viberary/)  
本文反映了作者使用 Viberary 的经验，Viberary 是一个旨在根据特定氛围查找书籍的副项目。该项目的创建是为了探索机器学习副项目，以及搜索和推荐的交叉点，作为作者最近深入研究嵌入（embeddings）的生产级补充。  
  
[将 AI 驱动的 Django 应用程序部署到 Modal.com](https://tolkunov.dev/posts/django-on-modal/)  
Modal 是部署使用 AI 模型并需要 GPU 的 Python 应用程序的一个好地方。以下是如何将 Modal 与 Django 应用程序一起使用。  
  
[NumPy 2 来啦：防止损坏，更新代码](https://pythonspeed.com/articles/numpy-2/)  
NumPy 2 即将推出，并且向后不兼容。了解如何防止代码被破坏以及如何升级。  
  
[Pandas 分析：详细说明](https://www.influxdata.com/blog/pandas-profiling-tutorial/)  
Pandas 分析或现在所谓的 ydata-profiling，是通过 Python 提供的一个包，我们将在本文中介绍它并介绍如何使用它。  
  
[数据分析师初学者训练营（SQL、Tableau、Power BI、Python、Excel、Pandas、项目等）](https://www.youtube.com/watch?v=PSNXoAs2FtQ) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
跟随这个大型课程成为一名数据分析师。您将学习数据分析师需要了解的核心主题。在此过程中，您会通过构建大量项目来获得实践经验。  
  
# 好玩的项目，工具和库  
  
[Open Interpreter](https://github.com/KillianLucas/open-interpreter)  
计算机自然语言界面。  
  
[Harlequin](https://github.com/tconbeer/harlequin)  
适用于您的终端的 SQL IDE。  
  
[crewAI](https://github.com/joaomdmoura/crewAI)  
用于编排角色扮演、自主人工智能代理的尖端框架。通过促进协作智能，CrewAI 使代理能够无缝协作，以及处理复杂的任务。  
  
[skfolio](https://github.com/skfolio/skfolio)  
Python 库，用于构建基于 scikit-learn 的投资组合优化。  
  
[aloha](https://github.com/tonyzhaozh/aloha)  
用于双手远程操作的低成本开源硬件系统。  
  
[selfextend](https://github.com/sdan/selfextend)  
自扩展的实现，通过分组注意力扩展上下文窗口  
  
[langgraph](https://github.com/langchain-ai/langgraph)  
将语言代理构建为图表。  
  
[psst](https://github.com/Sjlver/psst)  
基于纸质的秘密共享技术。  
  
[van-gonography](https://github.com/JoshuaKasa/van-gonography)  
在您选择的图像中隐藏任何类型的文件。  
  
  
# 近期活动和网络研讨会  
  
[DragonPy 2024 年 1 月聚会](https://www.meetup.com/ljubljana-python-group/events/298186535/)  
将有以下演讲：
* 原谅我的 Python
* 从 P 到 P – 不同类型的 p2p
* Python 和能量学
  
[Bangalore 2024 年 1 月聚会](https://www.meetup.com/bangpypers/events/297639118/)  
将有以下演讲：
* Micropython - 用于微控制器的 Python
* 是时候抛弃 requirements.txt 和 VENV 了，现在就开始使用Poetry
* 我是如何制作 dunderhell 的：最丑陋的 python 压缩器
  
[PyData Munich 2024 年 1 月聚会](https://www.meetup.com/pydata-munchen/events/298185256/)  
将有以下演讲：
* 用于图像字幕的不同方法，为给定视觉输入预测字幕的任务
* 从 LLM 到业务 - 在业务流程中扩展 LLM 应用程序。
  
[PyData Southampton 2024 年 1 月聚会](https://www.meetup.com/pydata-southampton/events/298281776/)  
将有以下演讲：
* OpenAI 的函数调用：它是什么以及我们要如何利用它？
* 为非政府组织构建数据科学解决方案，即使你不知道它会在什么样的基础设施上运行：预测导师供需不匹配的案例研究