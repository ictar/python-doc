原文：[Python Weekly - Issue 627](http://eepurl.com/iEB-HQ)

---

欢迎来到Python周刊第 627 期。让我们直奔主题。
  
  
# 文章，教程和讲座  
  
[为 CPython 开发的 JIT 编译器](https://www.youtube.com/watch?v=HxSHIpEQRjs) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
Brandt Bucher 讨论了为 CPython 开发的即时（Just-In-Time，JIT）编译器。演讲深入探讨了专门为 CPython（默认的Python解释器）实现 JIT 编译器的挑战和复杂性。  
  
[给初学者的生成式 AI 教程](https://microsoft.github.io/generative-ai-for-beginners/)  
一门包含 12 课的课程，教授构建生成式 AI 应用程序所需的一切知识。  
  
[大规模编写和linting Python](https://engineering.fb.com/2023/11/21/production-engineering/writing-linting-python-at-scale-meta/) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/9a9a57d0-eb4b-47f8-8af4-55ba50e8c350.png)  
在 Meta 公司，Python 在 Instagram 的后端中起着关键作用，为 Python 3.12 做出贡献，并驱动着诸如配置系统和 AI 工作等关键方面。在 Meta Tech Podcast中，Pascal Hartig 和 Amethyst Reese 深入探讨了 Python 基础团队所做的努力，开源的 Fixit 2 linter 框架，以及关于在 Meta 所担任的生产工程师角色的见解。  
  
[是时候改变了：datetime.utcnow() 现在已弃用](https://blog.miguelgrinberg.com/post/it-s-time-for-a-change-datetime-utcnow-is-now-deprecated)  
本文介绍了关于这些函数被弃用的原因的更多信息，以及替换它们的方法。  
  
[通过 AWS 发布 280 亿分子嵌入](https://ashvardanian.com/posts/usearch-molecules/)  
宣布了一个涉及收集、指纹识别和索引 70 亿种小分子（具有各种结构嵌入，例如 MACCS、PubChem、ECFP4 和 FCFP4）的项目的完成。该数据集使用 Unum 的 USearch 针对分子搜索进行了优化，现在可以通过 AWS Open Data 在全球范围内免费访问，它还通过 GitHub 提供了全面的数据表和可视化脚本。  
  
[Python 全局解释器锁提供的不断变化的“保证”](https://stefan-marr.de/2023/11/python-global-interpreter-lock/)  
本文探讨了 CPython 全局解释器锁 (Global Interpreter Lock，GIL) 的实现细节，以及它们在 Python 3.9 和当前开发分支（将成为 Python 3.13）之间的变化。 
  
[让我们使用 LLM Embeddings、Django 和 pgvector 编写一个 AI 搜索引擎](https://www.youtube.com/watch?v=OPy4dLHdZng) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
大型语言模型 (Large Language Model，LLM) 可用于业务应用程序，例如内容匹配和职位搜索。William Huster 演示了如何构建利用 LLM 进行职位搜索的原型应用程序。 
  
[两种线程池，以及为什么需要这两种](https://pythonspeed.com/articles/two-thread-pools/)  
线程池应该有多大？这取决于您的用例。  
  
[Python 3.12 范型类型解释](https://www.youtube.com/watch?v=q6ujWWaRdbA) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
该视频探讨了 Python 3.12 中的泛型类型的工作原理，以及它的优势（相对于仅使用 Any 类型）。  
  
[使用 Polars 在云端处理数百 GB 的数据](https://blog.coiled.io/blog/coiled-functions-polars.html)  
由于内存和网络限制，本地计算机可能难以处理大型数据集。Coiled Functions 提供了一种基于云的解决方案，可以高效且经济高效地处理如此广泛的数据集，克服本地硬件对复杂数据处理任务的限制。融入像 Polars 这样的库可以进一步增强这种方法，利用优化的计算功能，从而更快、更高效地处理数据。在这篇文章中，我们将使用 Coiled Functions，在带有 Polars 的单个云计算机上处​​理 150 GB 大小的 Uber-Lyft 数据集。  
  
[Python 应用程序中的错误类别](https://threeofwands.com/the-types-of-errors-in-python-apps/)  
编写 Python 程序时，错误是不可避免的。然而，我们可以管理我们所产生的错误类型。让我们探索一个简单的模型，它将这些错误按照从最好到最差进行分类，然后讨论谨慎使用工具可以如何提高软件质量。  
  
[GPU 上的 Pandas Dataframes（使用或不使用 CuDF）](https://www.youtube.com/watch?v=OnYGtKQT-rU) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
使用 CuDF 的 Pandas 加速器的概述和一些快速示例，以及与普通 Pandas 相比，使用它进行数据分析要快多少。  
  
[使用 PyTorch 构建一个神经网络](https://haydenjames.io/building-a-neural-network-with-pytorch/)  
构建第一个神经网络似乎是一项艰巨的任务，但像 PyTorch 这样的深度学习框架使得这项任务比以往任何时候都更容易完成。本文介绍了如何使用 PyTorch 构建神经网络。  
  
[Python Flask 应用程序中的 GitHub OAuth](https://supabase.com/blog/oauth2-login-python-flask-apps)  
有关在 Python 应用程序中构建使用 Github 进行登录的分步指南。  
  
[如何使用 Django 和 Stripe 创建订阅 SaaS 应用程序](https://www.saaspegasus.com/guides/django-stripe-integrate/)  
使用基于 Python 的 Django Web 框架和 Stripe 支付处理器创建订阅 SaaS 业务的所有技术细节。  
  
[四种优化](https://tratt.net/laurie/blog/2023/four_kinds_of_optimisation.html)  
本文讨论了四种优化程序的方法：使用更好的算法、使用更好的数据结构、使用较低级别的系统，或接受不太精确的解决方案。  
  
[使用 Python 通过 PostgREST API 插入数据](https://www.dataroc.ca/blog/inserting-data-via-the-postgrest-api-using-python)   
  
[CPython 软件物料清单提案](https://sethmlarson.dev/security-developer-in-residence-weekly-report-19)  
  
[有多少 Python 核心开发人员使用类型注释？](https://blog.orsinium.dev/posts/py/core-devs-typing/)  
  
  
# 好玩的项目，工具和库  
  
[LoRAX](https://github.com/predibase/lorax)  
在生产中为 100 个经过微调的 LLM 提供服务，成本为 1.  
  
[AIConfig](https://github.com/lastmile-ai/aiconfig)  
配置驱动、源代码控制友好的 AI 应用程序开发。  
  
[Frigate](https://github.com/blakeblackshear/frigate)  
Frigate 是一款围绕实时 AI 对象检测构建的开源 NVR。所有处理都是在您自己的硬件上本地执行的，并且您的相机馈送永远不会离开您的家。  
  
[PyNest](https://github.com/PythonNest/PyNest)  
PyNest 是一个构建在 FastAPI 之上的 Python 框架，遵循 NestJS 的模块化架构。   
  
[ai-exploits](https://github.com/protectai/ai-exploits)  
真实世界 AI/ML 漏洞利用的集合，用于负责任地披露的漏洞。  
  
[pytest-patterns](https://github.com/flyingcircusio/pytest-patterns)  
pytest-patterns 是 pytest 的插件，提供专门针对测试优化的模式匹配引擎。  
  
[Google-Colab-Selenium](https://github.com/jpjacobpadilla/Google-Colab-Selenium)  
在 Google Colab 笔记本中使用 Selenium 的最佳方式！  
  
[stateless](https://github.com/suned/stateless)  
Python 的静态类型、纯函数效果。  
  
[sqlalchemy_data_model_visualizer](https://github.com/Dicklesworthstone/sqlalchemy_data_model_visualizer)  
自动将您的 SQLalchemy 数据模型转换为漂亮的 SVG 图  
  
[StyleTTS 2](https://github.com/yl4579/StyleTTS2)  
通过风格扩散（Style Diffusion）和大型语音语言模型的对抗性训练实现人类水平的文本到语音转换  
  
[screenshot-to-code](https://github.com/abi/screenshot-to-code)  
放入屏幕截图并将其转换为干净的 HTML/ Tailwind/JS 代码。 
  
[NeumAI](https://github.com/NeumTry/NeumAI)  
Neum AI 是一个一流的框架，用于管理大规模矢量嵌入的创建和同步。  
  
  
# 最新发布  
  
[Python 3.13.0 alpha 2 现已推出](https://pythoninsider.blogspot.com/2023/11/python-3130-alpha-2-is-now-available.html)  
  
[Django 5.0 候选版本 1 已发布](https://www.djangoproject.com/weblog/2023/nov/20/django-50-rc1/)  
  
  
# 近期活动和网络研讨会  
  
[Virtual：PyMunich Meetup 2023 年 11 月](https://www.meetup.com/pymunich/events/296949399/)  
将有以下演讲：
* Python 元编程简介
* 数据科学家的知识产权
* 生产中利用开源 LLM
  
[PyBerlin 42](https://www.meetup.com/pyberlin/events/296945261/)  
将有以下演讲：
* 浏览器中的 CPU：WebAssembly 揭秘
* 敏捷交付的四个关键问题
* 将 LLM 纳入实际的 NLP 工作流程中
* Web 黑客：通过单个 Python 漏洞接管服务器
  
[PyData Copenhagen Meetup 2023 年 11 月](https://www.meetup.com/pydata-copenhagen/events/297375748/)  
将有一场演讲，加速 ML 原型设计：利用 HiPlot 和 Patsy 以思维的速度进行特征工程。  
