原文：[Python Weekly - Issue 645](http://eepurl.com/iNp38k)

---

欢迎来到《Python周刊》第 645 期。让我们直奔主题。 
      
# 新闻  
  
[针对 Python 开发人员的域名仿冒运动](https://blog.phylum.io/typosquatting-campaign-targets-python-developers/) 
 
Phylum 的自动风险检测平台发现了一场针对 PyPI 上流行 Python 库的新域名仿冒运动，迄今为止已发布了 500 多个域名仿冒变体。 PyPI 已立即删除恶意软件包，并暂时停止新项目和帐户创建，以防止此次攻击造成进一步影响。  
  
  
# 文章，教程和讲座  
  
[关于在 2024 年构建大型语言模型的一个小指南](https://www.youtube.com/watch?v=2-SPH9hIKT8) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
这是两部分系列的第一个视频，涵盖了在 2024 年训练具备良好性能 LLM 的所有概念。  
  
[为什么 Python 列表会奇怪地倍增？探索 CPython 源代码](https://codeconfessions.substack.com/p/why-do-python-lists-multiply-oddly)  
看看 CPython 中列表实现的内部结构，以了解它们的这个怪癖。  
  
[使用 Pyodide 和 WebAssembly，为 Python 引入 Workers](https://blog.cloudflare.com/python-workers)  
这篇文章讨论了 Cloudflare 对 Python Workers 的实现，重点介绍了它们在无服务器计算方面的优势和用例。它探讨了 Python Workers 如何帮助开发人员在 Cloudflare 网络上高效构建和部署轻量级、可扩展的应用程序。  
  
[制作（Make）Python DevEx](https://tech.target.com/blog/make-python-devex)  
本文讨论了建立高效的 Python 开发环境的挑战，以及如何使用 Make 通过自动准备开发环境和加快测试驱动的开发周期来帮助缓解这些障碍。作者提供了一个示例项目，演示如何使用 Make 改善跨多个代码库的 Python 开发人员体验。  
  
[通过内省，在 Django 项目中强制执行约定](https://lukeplant.me.uk/blog/posts/enforcing-conventions-in-django-projects-with-introspection/)  
结合 Python 和 Django 内省 API，在 Django 模型中强制执行命名约定的一些代码和技巧。  
  
[构建用于代码修复的 LLM](https://blog.replit.com/code-repair)  
本文讨论了 Replit 的代码修复功能，该功能可自动修复代码中的常见编程错误和问题。它探讨了通过为常见编码问题提供自动化解决方案，代码修复可以如何帮助开发人员节省时间并提高代码质量。  
  
[利用断点探索代码](https://www.mostlypython.com/using-breakpoints-to-explore-your-code/)  
本文指导 Python 开发人员有效利用断点来调试和探索代码执行流程。它提供了关于利用断点来更好地理解 Python 代码并进行故障排除的实用技巧和示例。  
  
[Django 的 ASGI 部署选项](https://fly.io/django-beats/asgi-deployment-options-for-django/)  
本文探讨了 Django 应用程序的 ASGI 部署选项，提供了有关使用 ASGI 服务器部署 Django 的见解。  
  
[Python 3.12 泛型简述](https://www.youtube.com/watch?v=TkDg3EHwC1g) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
Python 3.12 中的泛型可以通过允许类与不同的数据类型一起使用来转换代码，从而增强灵活性以及及早发现错误。该视频深入介绍了通用类和子类，并向您展示它们可以如何简化您的开发过程。  
  
[使用 Google Cloud 自动化 Python](https://www.scipress.io/post/rSp9Rov4ppvHgpHQaRPy/Automating-Python-with-Google-Cloud)  
一个教程系列，关于如何使用 Cloud Functions 和/或 Cloud Run 在 Google Cloud 中自动化 Python 脚本。   
  
[GPT 可以优化我的税务吗？](https://finedataproducts.com/posts/2024-03-10-tax-scenarios-with-ai/)  
这篇文章描述了一个使用 GPT-4 和 10 个 Python 库的 Web 应用程序，它允许用户输入税务场景并接收优化的税务计算和建议。与传统税务软件相比，该应用程序旨在提供更加灵活和个性化的税务咨询体验。  
  
[Python 项目 —— 本地虚拟环境管理 Redux](https://hynek.me/articles/python-virtualenv-redux/)  
  
[2024 年我如何管理 Python](https://outlore.dev/blog/python-dev-2024/)  
  
  
# 好玩的项目，工具和库  
  
[SWE-agent](https://github.com/princeton-nlp/SWE-agent)  
SWE-agent 将 LM（例如 GPT-4）转变为软件工程代理，可以修复真实 GitHub 存储库中的错误和问题。  
  
[DBRX](https://github.com/databricks/dbrx)  
DBRX（ Databricks 开发的大型语言模型） 代码示例和资源。  
  
[thepipe](https://github.com/emcf/thepipe)  
通过一行代码将任何文件、文件夹、网站或存储库导出到 GPT-4-Vision。  
  
[Sparrow](https://github.com/katanaml/sparrow)  
使用 ML 和 LLM 进行数据处理。  
  
[Nava](https://github.com/openscilab/nava)  
用 Python 播放声音。  
  
[IPEX-LLM](https://github.com/intel-analytics/ipex-llm)  
一个 PyTorch 库，用于在 Intel CPU 和 GPU（例如，具有 iGPU 的本地 PC、Arc、Flex 和 Max 等独立 GPU）上运行 LLM，延迟非常低。  
  
[RAGFlow](https://github.com/infiniflow/ragflow)  
RAGFlow 是一个基于深度文档理解的开源 RAG（检索增强生成）引擎。  
  
  
# 最新发布  
  
[Django 问题修复版本已发布：5.0.4](https://www.djangoproject.com/weblog/2024/apr/03/bugfix-release/)  
  
  
# 近期活动和网络研讨会  
  
[San Francisco Python 2024 年 4 月聚会](https://www.meetup.com/sfpython/events/298868858/)  
将会有以下演讲：
* Prompt 工程：适用于 0.1 版代码 - 获得更快结果的途径
* 更快运行 Pytest 的策略

  
[PyData NYC 2024 年 4 月聚会](https://www.meetup.com/pydatanyc/events/300039833/)  
将会有以下演讲：
* 时间序列预测简介
* 使用 STUMPY 进行时间序列 EDA

  
[Pyladies Munich 2024 年 4 月聚会](https://www.meetup.com/pyladiesmunich/events/299309240/)  
将会有以下演讲：
* 可观察性：原理与 Python 中的应用
* 使用 Python 进行无缝云基础设施管理
* 责任因素：同伴支持可以如何改变你的学习之旅

  
[Freiburg Python 2024 年 4 月聚会](https://www.meetup.com/python-user-group-freiburg/events/299919455/)  
将会有以下演讲：
* 事件系统 - JobRad 软件的演变
* Docker 化 Python 应用程序及其安全性 

  
[PyData Munich 2024 年 4 月聚会](https://www.meetup.com/pydata-munchen/events/300083533/)  
将会有以下演讲：
* 使用 dstack Sky 访问多个提供商的 GPU
* 更多标签或案例？评估自然语言推理中的标签变化
* p(doom) —— 一个开放的、完全去中心化的全球人工智能研究实验室
