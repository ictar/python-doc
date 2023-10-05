原文：[Python Weekly - Issue 620](http://eepurl.com/iA9nDI)

---

欢迎来到Python周刊第 620 期。让我们直奔主题。


# 文章、教程和讲座 
  
[Python 3.12：您需要了解的所有新功能！](https://www.youtube.com/watch?v=udHmeAmOlbI) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
该视频不仅将深入探讨 Python 3.12 中即将推出的令人兴奋的新功能和改进，还将讨论即将发布的版本中将删除的一些东西。
  
[度量 Python 执行时间的 5 种方法](https://superfastpython.com/benchmark-execution-time/)  
您可以使用标准库中的“time”模块对 Python 代码的执行进行基准测试。在本教程中，您将了解如何使用一套不同的技术来计时 Python 代码的执行时间。
  
[使用 FastAPI 掌握集成测试](https://alex-jacobs.com/posts/fastapitests/)  
集成测试 FastAPI：使用 MongoMock、MockS3 等来利用模拟后端服务的强大功能。
  
[LangChain 初始者速成班](https://www.youtube.com/watch?v=lG7Uxts9SXs) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
LangChain 是一个旨在简化使用大型语言模型创建应用程序的框架。它让你能够轻松地将 AI 模型与大量不同的数据源连接起来，以便您可以创建定制的 NLP 应用程序。  
  
[使用 Django REST 框架构建 API](https://blog.jetbrains.com/pycharm/2023/09/building-apis-with-django-rest-framework/)  
本教程演示了如何在 PyCharm 中使用 Python 和 Django REST 框架来开发 API
  
[我所学到的有关在 Python 中构建 CLI 工具的那些事](https://simonwillison.net/2023/Sep/30/cli-tools-python/)  
我用 Python 构建了很多命令行工具。它已成为我最喜欢的快速将一段代码变成我可以自己使用并打包供其他人使用的方法。以下是我迄今为止在 Python 中设计和实现 CLI 工具所学到的一些东西的笔记。 
  
[用不到 200 行代码在云中构建 API](https://aeturrell.com/blog/posts/build-a-cloud-api/build-a-cloud-api.html)
云工具和 Python 包已经变得非常强大，让您可以用不到 200 行代码构建（可扩展的）基于云的 API。在本文中，您将了解如何使用 Google Cloud、Terraform 和 FastAPI，在云上部署可查询数据 API。
  
[如何在 Django 中安全存储用户的 API 密钥](https://www.photondesigner.com/articles/store-api-keys-securely)  
加密用户的密钥以提高安全性。
  
[使用 PyTorch Lightning 扩展大型（语言）模型](https://lightning.ai/blog/scaling-large-language-models-with-pytorch-lightning/)  
了解使用 PyTorch Lightning 训练 Llama 和 Stable Diffusion 等大型模型的技术。
  
[探索 Wordle](https://www.georgevreilly.com/2023/09/26/ExploringWordle.html)  
本文将向您展示如何使用 Python，以编程方式解决 Wordle。
  
  
# 好玩的项目，工具和库  
  
[mistral-src](https://github.com/mistralai/mistral-src)  
Mistral AI 7B v0.1 模型的参考实现。
  
[kernel-hardening-checker](https://github.com/a13xp0p0v/kernel-hardening-checker)  
用于检查 Linux 内核安全强化选项的工具。

[dreamgaussian](https://github.com/dreamgaussian/dreamgaussian)  
用于高效 3D 内容创建的生成 Gaussian Splatting。
  
[cloud_benchmarker](https://github.com/Dicklesworthstone/cloud_benchmarker)  
Cloud Benchmarker 自动化执行云实例的性能测试，提供富有洞察力的图表并随时间进行跟踪。
  
[DSPy](https://github.com/stanfordnlp/dspy)  
使用基础模型进行编程的框架。
  
[cloudgrep](https://github.com/cado-security/cloudgrep)  
cloudgrep 是用于云存储的 grep。
  
[Octogen](https://github.com/dbpunk-labs/octogen)  
Octogen 是一款由 GPT3.5/4 和 Codellama 提供支持的开源代码解释器。
  
[stepping](https://stepping.site/)  
给 Python 应用开发者的增量视图维护。
  
[BoTorch](https://botorch.org/)  
BoTorch 是一个基于 PyTorch 构建的贝叶斯优化研究库。
  
[cappa](https://github.com/dancardin/cappa)  
声明式 CLI 参数解析器。
  
# 最新发布  
  
[Python 3.12.0](https://www.python.org/downloads/release/python-3120/)  
Python 3.12.0 中的一些主要变化包括
  * 更灵活的 f 字符串解析
  * Python 代码对缓冲区协议的支持
  * 新的调试/分析 API 
  * 支持具有单独全局解释器锁（GIL）的隔离子解释器 
  * 更多改进的错误消息。 
  * 支持 Linux 性能分析器报告跟踪中的 Python 函数名称。
  * 许多大大小小的性能改进 
  
[Flask 3.0.0](https://flask.palletsprojects.com/en/3.0.x/changes/)  
  
[Django 安全版本已发布：4.2.6、4.1.12 和 3.2.22](https://www.djangoproject.com/weblog/2023/oct/04/security-releases/)  
  
  
 # 近期活动和网络研讨会  
  
[Hybrid IndyPy - 利用 AI 进行创新：构建类似 ChatGPT 的应用程序](https://www.meetup.com/indypy/events/294548715/)  
希望通过创建自己的类似 ChatGPT 的应用程序来释放 AI 和 LLM 的潜力吗？在本次演讲和现场演示中，您将学习如何提取专有数据见解、加速数据驱动的决策、提高生产力并推动创新。
  
[虚拟（Virtual）：克利夫兰 Python 2023 年 10 月聚会](https://www.meetup.com/cleveland-area-python-interest-group/events/295681934/)  
将有一场演讲：使用网络抓取，解析和收集亚马逊产品列表的评论数据。
