原文：[Python Weekly - Issue 632](http://eepurl.com/iHlBRk)

---

欢迎阅读《Python 周刊》第 632 期。新年快乐！希望您度过了一个愉快的假期。

      
# 新闻  
  
[DjangoCon Europe 2024 CFP](https://pretalx.evolutio.pt/djangocon-europe-2024/cfp)  
 DjangoCon Europe 2024 参与呼吁，邀请提交演讲，其主题应涉及 Django 和 Python 开发。提案截止日期为 2024 年 3 月 1 日。 
  
  
# 文章，教程和讲座  
  
[用 Python 读取 Excel 最快的方法](https://hakibenita.com/fast-excel-python)  
用不到 4 秒读取 500K 行。  
  
[面向 Python 爱好者的 Rust 编码简介 ](https://www.youtube.com/watch?v=MoqtsYLGCC4) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
该视频首次探讨了使用 Rust 进行编码，深入探讨了它的炫酷功能，并与 Python 进行了比较。  
  
[Microdot：另一个 Python Web 框架](https://blog.miguelgrinberg.com/post/microdot-yet-another-python-web-framework)  
本文介绍了 Microdot，这是一个 Python Web 框架，具有类似 Flask 的语法、与 MicroPython 和 CPython 兼容、支持 asyncio 以及为 MicroPython 提供了一个最小的 Web 服务器，旨在满足 MicroPython 生态系统中对 Web 框架的需求。这篇文章还概述了 Microdot 的开发历史及其最近发布的完全异步的 2.0 版本。  
  
[Fontimize：仅将字体精确地设置为您网站使用的字符](https://daveon.design/introducing-fontimize-subset-fonts-to-exactly-and-only-your-websites-used-characters.html)  
Fontimize 是一个 Python 库，可以创建仅包含文本或 HTML 所需的特定字形的字体子集，从而减少网站的初始下载大小，进而优化带宽使用。 
  
[如何让 LLM 更快](https://vgel.me/posts/faster-inference)  
这篇文章是一篇长期而广泛的调查，探讨了一系列使 LLM 取得进展的不同方法（从更好的硬件利用率到巧妙的解码技巧）。  
  
[Django：检测全局隐私控制信号](https://adamj.eu/tech/2023/12/27/django-global-privacy-control/)  
在这篇文章中，我们将研究如何在 Django 项目中实现 GPC，并使用您可以进行调整的代码示例。由于 GPC 很简单，但需要非常依赖于具体情况的操作，因此很难为 Django 或第三方包构建任何特定的支持。  
  
[不使用 git 进行提交](https://matheustavares.gitlab.io/posts/committing-without-git)   
本文提供了一个实践教程，内容是使用 Python 创建具有两次提交的分支，旨在了解 Git 中的主要数据结构（称为“git 对象”）及其相互关系。这篇文章提供一些见解，涉及 Git 对象的不变性、使用 DEFLATE 算法进行压缩、以及通过其内容的 SHA-1 哈希进行引用，同时文中还警告不要在生产中使用该方法，因为由git 命令执行的安全检查和特别处理。  
  
[针对 Flask、Django 和 FastAPI 微调 Python WSGI 和 ASGI 应用程序](https://tonybaloney.github.io/posts/fine-tuning-wsgi-and-asgi-applications.html)  
在这篇文章中，重点是研究配置 Python Web 服务器（例如 Gunicorn、Uvicorn 和 Hypercorn）的最佳实践。它将总结Python代码和用户之间的组件架构，并讨论负载测试等验证方法，以确保配置能够承受用户流量。  
  
[使用两塔方法（Two Tower Approach）和 Expedia Group 旅行者数据生成候选者](https://medium.com/expedia-group-tech/candidate-generation-using-a-two-tower-approach-with-expedia-group-traveler-data-ca6a0dcab83e)  
建模环境和项目功能，以改进旅行者推荐。  
  
[Python 陷阱：在迭代时修改列表](https://andrewwegner.com/python-gotcha-modify-list-while-iterating.html)  
Python 可以让您在迭代列表元素时轻松修改列表。这种操作会反咬你一口。请继续阅读以了解它是如何阴你的，以及可以采取什么措施。  
  
[AutoGluon-TimeSeries：创建强大的集合预测（Ensemble Forecast） - 完整教程](https://aihorizonforecast.substack.com/p/autogluon-timeseries-creating-powerful)  
Amazon 的时间序列预测框架应有尽有。  
  
[如何在 mac 上使用 cli 或免费使用 Python 进行 ocr ](https://blog.greg.technology/2024/01/02/how-do-you-ocr-on-a-mac.html)  
  
[一个实验性的pip子命令，用于Unix的Python启动器](https://snarky.ca/an-experimental-pip-subcommand-for-the-python-launcher-for-unix/)  
  
[在2024年的头几天学习 LLM 和编程](http://antirez.com/news/140)  
  
  
# 好玩的项目，工具和库  
  
[DocFlow](https://github.com/jiisanda/docflow)  
DocFlow 是一个强大的文档管理 API，旨在简化文档处理，包括无缝上传、下载、组织、版本控制、共享等功能。  
  
[Jake](https://github.com/thevahidal/jake)  
在 GitHub 上轻松创建和部署您自己的单链接网站。  
  
[Paracelsus](https://github.com/tedivm/paracelsus)  
Paracelsus 通过读取 SQLAlchemy 模型来生成实体关系图。  
  
[falco](https://github.com/tobi-de/falco)  
增强您的 Django 开发体验：现代 Django 开发者的 CLI 和指南。  
  
[AnyText](https://github.com/tyxsspa/AnyText)  
多语言可视文本生成和编辑。  
  
[mixtral-offloading](https://github.com/dvmazur/mixtral-offloading)  
在 Colab 或者消费者桌面中运行 Mixtral-8x7B 模型。  
  
[UForm](https://github.com/unum-cloud/uform)  
袖珍多模态 AI，用于内容理解和生成，跨多语言文本、图像和视频，速度比 OpenAI CLIP 和 LLaVA 快了多至 5 倍。  
  
[MotionCtrl](https://github.com/TencentARC/MotionCtrl)  
用于视频生成的统一且灵活的运动控制器。  
  
[semantic-router](https://github.com/aurelio-labs/semantic-router)  
语义路由器是您的 LLM 和代理的超快速决策层。相比等待缓慢的 LLM 一代做出工具使用决策，我们利用语义向量空间的魔力来做出这些决策——使用语义来路由我们的请求。  
  
[Autograd-from-scratch](https://github.com/eduardoleao052/Autograd-from-scratch)  
从头开始​​记录并经过单元测试的教育性深度学习框架，仅使用 Numpy 构建。  
  
[MobileVLM](https://github.com/Meituan-AutoML/MobileVLM)  
适用于移动设备的快速、强大且开放的视觉语言助手。  
  
  
# 最新发布  
  
[Django 错误修复版本已发布：4.2.9 和 5.0.1](https://www.djangoproject.com/weblog/2024/jan/02/bugfix-release/)  
  
  
# 近期活动和网络研讨会  
  
[Virtual: Cleveland Python 2024 年 1 月聚会](https://www.meetup.com/cleveland-area-python-interest-group/events/297549665/)  
将会有一场演讲：使用开源包构建 LLM 支持的数据应用程序。  
  
[Python Milano 2024 年 1 月聚会](https://www.meetup.com/python-milano/events/298086176/)  
将有以下演讲：
  * 少写，多测试 - 基于属性的测试简介
  * Dagster：现代数据编排器

  
[PyData Toronto 2024 年 1 月聚会](https://www.meetup.com/pydatato/events/297368081/)  
将有以下演讲：
  * NumFOCUS - 那是什么？ 
  * PyTorch 2.0 - 为什么你应该关心它？

