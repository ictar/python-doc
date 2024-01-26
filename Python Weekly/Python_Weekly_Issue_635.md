原文：[Python Weekly - Issue 635](http://eepurl.com/iIKcaE)

---

欢迎来到《Python周刊》第 635 期。让我们直奔主题。


# 文章，教程和讲座  
  
[andas 中的方法链](https://www.youtube.com/watch?v=39MEeDLxGGg) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
什么是 Pandas 中的方法链？它是如何工作的以及如何使用它？ 
    
[在 Python 中，构建优先级-到期时间（priority-expiry） LRU 缓存，无需使用堆或者树](https://death.andgravity.com/lru-cache)  
了解如何仅使用 Python 标准库，实现具有优先级和到期时间的最近最少使用缓存。  
  
[scrapscript.py](https://bernsteinbear.com/blog/scrapscript/)  
文章介绍了一种小型、纯粹、函数式、内容可寻址、网络优先的编程语言，旨在创建小型、可共享的程序。它讨论了该语言的功能及其实现，强调了其目的及其开发背后的协作努力。本文深入探讨了 Scrapscript 的动机和设计原则，以及其创建和实施过程中涉及的协作过程。  
  
[矢量数据库的性能](https://www.youtube.com/watch?v=-MYYB0QjV6I) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
本次演讲探讨了矢量数据库在增强检索增强生成（Retrieval-Augmented Generation，RAG）等人工智能应用中的作用，重点关注对机器学习至关重要的高维嵌入。Egor Romanov 深入研究创建一个与 pgvector 集成的 Postgres 提供程序，利用 Python 性能评估框架来模拟相似性搜索测试并揭示潜在的性能潜力。  
  
[Python 的 dict() 和 {} 的性能分析](https://madebyme.today/blog/python-dict-vs-curly-brackets/)  
这篇文章探讨了在 Python 中使用 dict() 和 {} 创建字典的差异。它深入研究了它们的功能、性能和最佳实践，提供了有关在 Python 编码中使用每种方法的场景的见解。  
  
[自动化部署的可怕之处](https://slack.engineering/the-scary-thing-about-automating-deploys/)  
Slack Engineering 的文章讨论了对破坏生产的恐惧，这种恐惧阻碍了许多团队实现部署自动化。它强调了了解部署监控与正常监控有何不同的重要性，以及为缓解这些担忧而采取的迭代方法，从而最终实现成功的自动化部署。本文深入探讨了自动化部署流程的挑战和好处，解决了与网络安全和应用程序部署等各个领域的自动化系统相关的恐惧和担忧。  
  
[Python 打包一定会变得更好 —— 一个数据点](https://lukeplant.me.uk/blog/posts/python-packaging-must-be-getting-better-a-datapoint/)  
我在 Windows 上 “pip install”了我的应用，一切都正常了。事情进展顺利。  
  
[增强 Markdown 语言以实现出色的 Python 图形界面](https://www.taipy.io/posts/augmenting-the-markdown-language-for-great-python-graphical-interfaces)  
本文探讨了 Taipy 研发团队开发的增强 Markdown API，该 API 通过添加标签以直接在内容中生成图形界面元素，从而扩展了 Markdown 的简单性。这项创新旨在增强 Python 开发人员创建基于 Web 的界面的能力，提供一种在 Markdown 文档中集成图形元素的独特方法。  
  
[使用语义图和 RAG，生成知识](https://neuml.hashnode.dev/generate-knowledge-with-semantic-graphs-and-rag)  
使用语义图和 RAG 进行知识探索和发现。  
  
[Python 数据分析和可视化课程 —— 天文数据](https://www.youtube.com/watch?v=H9KefzbryEw) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
使用真实世界的天文数据学习数据分析、数据可视化和图像处理技术。该课程提供了一种实用的实践方法来简化数据分析中的复杂概念，非常适合初学者。    
  
[Django 中简单的 Google 登录](https://www.youtube.com/watch?v=NM9BE0iUB5Q) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
我们将以最简单的方式将 Google 登录添加到 Django 中（不使用 Django-all-auth 或 Django-social-auth 或任何其他大的包）。  
  
  
 # 书籍  
  
[ml-engineering](https://github.com/stas00/ml-engineering)  
机器学习工程开放书籍。  
  
  
 # 好玩的项目，工具和库  
  
[DataTrove](https://github.com/huggingface/datatrove)  
通过提供一组与平台无关的可定制管道处理块，将数据处理从疯狂的脚本中解放出来。  
  
[Granian](https://github.com/emmett-framework/granian)  
用于 Python 应用程序的 Rust HTTP 服务器。  
  
[InstantID](https://github.com/InstantID/InstantID)  
在几秒钟内 Zero-shot 生成身份保留。  
  
[finagg](https://github.com/theOGognf/finagg)  
一个 Python 包，用于聚合来自流行且免费的金融 API 的历史数据，并将该数据转换为适用于 AI/ML 的特征。  
  
[Python-Type-Challenges](https://github.com/laike9m/Python-Type-Challenges)  
通过交互式在线练习，掌握 Python 类型（类型提示）！  
  
[django-webhook](https://github.com/danihodovic/django-webhook)  
传出 Django webhook，由模型更改触发。  
  
[ULWGL-launcher](https://github.com/Open-Wine-Components/ULWGL-launcher)  
统一 Linux Wine 游戏启动器。  
  
[RAGxplorer](https://github.com/gabrielchua/RAGxplorer)  
可视化并探索您的 RAG 文档。  
  
[FastHX](https://github.com/volfpeter/fasthx)  
FastAPI 和 HTMX，正确之道。  
  
[TaskingAI](https://github.com/TaskingAI/TaskingAI)  
为 AI 原生应用程序开发的开源平台。   
  
[Applio](https://github.com/IAHispano/Applio)  
Ultimate 语音克隆工具，经过精心优化，具有无与伦比的功能、模块化和用户友好的体验。  
  
[wafer](https://github.com/sysdig/wafer)  
Wafer 是一个简单但有效的 Web 应用程序防火墙 (web application firewall，WAF) 模糊测试工具。    

> 译注：模糊测试（fuzz testing, fuzzing）是一种软件测试技术。 其核心思想是将**自动或半自动生成的随机数据**输入到一个程序中，并监视程序异常，如崩溃，断言（assertion）失败，以发现可能的程序错误，比如内存泄漏。 模糊测试常常用于检测软件或计算机系统的安全漏洞。 —— 来自维基百科

[SGLang](https://github.com/sgl-project/sglang)  
SGLang 是一种专为大型语言模型 (LLM) 设计的结构化生成语言。它使您与 LLM 的互动更快、更可控。  
  
  
 # 近期活动和网络研讨会  
  
[PyBerlin 43](https://www.meetup.com/pyberlin/events/297958692/)  
将有以下演讲：
  * 生成结果的会议
  * f-strings：它们还能变得更好吗？
  * 在 doctari 衡量软件交付性能

  
[PyData Lancaster 2024 年 1 月聚会](https://www.meetup.com/pydata-lancaster/events/298462888/)  
将有以下演讲：
  * Earth My Friend：使用开源工具绘制 20 世纪 50 年代的环球航行图
  * 从代码库到软件
  
[PyData Copenhagen 2024 年 2 月聚会](https://www.meetup.com/pydata-copenhagen/events/298422850/)  
将有一场演讲，即技术领域的增强检索：LLM 在实践中的挑战和机遇。  
  
[PyData Lausanne 2024 年 2 月聚会](https://www.meetup.com/pydata-lausanne/events/298636167/)  
将有以下演讲：
  * 使用 DVC 和 CML 的 MLOps
  * 抑制测试噩梦：使用 pytest 进行简单可靠的测试
  * 忘记打印语句：如何使用 VSCode 的调试器来简化你的生活

  
[PyData Montreal 2024 年 2 月聚会](https://www.meetup.com/pydata-mtl/events/298407655/)  
将有以下演讲：
  * 理解语义搜索
  * 加拿大贝尔 NLP 语音团队对提取式问答变压器的探索
  * LLM 微调和部署简介
