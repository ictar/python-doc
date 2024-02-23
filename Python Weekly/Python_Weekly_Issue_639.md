原文：[Python Weekly - Issue 639](http://eepurl.com/iKyMsA)

---

欢迎来到《Python周刊》第 639 期。让我们直奔主题。


# 文章，教程和讲座  
  
[让我们构建 GPT Tokenizer](https://www.youtube.com/watch?v=zduSFxRajkE) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
Tokenizer 对于大语言模型 (LLM) 至关重要，它在字符串和标记之间进行转换，作为一个具有单独训练集和算法的不同阶段运行。本讲座从头开始构建 GPT 系列 Tokenizer，揭示了 LLM 中与标记化相关的特殊行为。我们探讨这些问题，将其归因于标记化，并考虑完全消除此阶段的理想方案。”  
  
[在 Python 项目中安全使用凭证的 5 个技巧](https://www.youtube.com/watch?v=OOvvQRBcrhI) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
了解 5 个简单的技巧，以助于确保 Python 凭证安全，并在出现问题时快速解决问题.  
  
[从头开始构建一个 LLM](https://bclarkson-code.github.io/posts/llm-from-scratch-scalar-autograd/post.html)  
了解如何完全从头开始构建一个具有所有花哨功能的现代语言模型：从普通 Python 到函数式编码助手。  
  
[跟踪 Python 中的系统调用](https://blog.mattstuchlik.com/2024/02/16/counting-syscalls-in-python.html)  
本文讨论了作者开发的一个添加到 Cirron 的工具，该工具使我们能够跟踪Python代码的系统调用。它提供了一个跟踪“print”函数的示例，并说明了该工具的实现，使用strace工具进行有效分析。文章还概述了使用 ptrace 系统调用进行实现的初衷，以及随后利用 strace 工具来处理复杂性。  
  
[使用 IPython Jupyter Magic 命令改善 Notebook 体验](https://towardsdatascience.com/using-ipython-jupyter-magic-commands-to-improve-the-notebook-experience-f2c870cab356)  
一篇关于创建自定义 IPython Jupyter Magic 命令的帖子。  
  
[添加 Django 工作线程的最简单方法（使用 AWS Chalice）](https://www.photondesigner.com/articles/lambda-for-django)  
本文讨论了如何利用 AWS Chalice 来合并 Django 工作线程，从而能够使用 lambda 函数作为任何应用程序的无服务器后台工作线程。这种方法允许 lambda 函数在后台运行，而不会阻塞应用程序的主线程，并且它可以在完成后调用 Django 应用程序上的端点，从而提供在 lambda 函数中使用任何 Python 库的能力，而无需引入 lambda 层或其他特定于 AWS 的配置。  
  
[如何对一个使用了 Django、Preact 和 PostgreSQL 的应用程序进行 Docker 化](https://www.honeybadger.io/blog/dockerize-django-preact-postgres)  
对 Django 应用程序进行 Docker 化可能是一件令人生畏的事情，但回报大于风险。在本指南中，Charlie Macnamara 将引导您完成这个设置过程，以便您可以充分利用您的应用程序。  
  
[使用 Python 实现的算法艺术](https://www.youtube.com/watch?v=_XeRM-4DZz0) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
在本次演讲中，我们将从零开始，构建我们自己的工具，用 Python 进行艺术创作，无需人工智能！我们将展示 Python 的表现力可以如何让我们优雅地描述图形，并使用它以编程方式制作一些独特的艺术作品。  
  
[使用 Neon Postgres 和 AWS App Runner 部署任意规模的无服务器（Serverless） FastAPI 应用程序](https://neon.tech/blog/deploy-a-serverless-fastapi-app-with-neon-postgres-and-aws-app-runner-at-any-scale)  
使用 FastAPI 创建无服务器 API，部署在 AWS App Runner 上并由 Neon Postgres 提供支持。  
  
  
 # 好玩的项目，工具和库  
  
[uv](https://github.com/astral-sh/uv)  
一个非常快的 Python 包安装程序和解析器，用 Rust 编写。这是一篇详细介绍 uv 的[帖子](https://astral.sh/blog/uv)。  
  
[OS-Copilot](https://github.com/OS-Copilot/FRIDAY)  
一个自我改进的具体对话代理，无缝集成到操作系统中，以自动化我们的日常任务。  
  
[Owl](https://github.com/OwlAIProject/Owl)   
本地运行的个人可穿戴人工智能。  
  
[Alto](https://github.com/runprism/alto)  
面向数据从业者的 Serverless。在云中运行代码的最快方法。在虚拟机中轻松运行脚本、函数和 Jupyter Notebook。  
  
[magika](https://github.com/google/magika)  
通过深度学习检测文件内容类型。  
  
[Streamline-Analyst](https://github.com/Wilson-ZheLin/Streamline-Analyst)  
由 LLM 提供支持的人工智能代理，可简化数据分析的整个过程。  
  
[minbpe](https://github.com/karpathy/minbpe)  
LLM 标记化中常用的字节对编码 (Byte Pair Encoding，BPE) 算法的最小的干净代码。  
  
[Hyperdiv](https://github.com/hyperdiv/hyperdiv)  
使用 Python 构建响应式 Web UI。  
  
[UFO](https://github.com/microsoft/UFO)  
用于 Windows 操作系统交互的以 UI 为中心的代理。  
  
  
# 最新发布  
  
[Python 3.13.0 alpha 4](https://pythoninsider.blogspot.com/2024/02/python-3130-alpha-4-is-now-available.html)  
  
  
# 近期活动和网络研讨会  
  
[Oxford Python 2024 年 2 月聚会](https://www.meetup.com/oxfordpython/events/299181384/)  
将会有一场演讲：PyO3 入门。 
  
[PyLadies Amsterdam 2024 年 2 月聚会](https://www.meetup.com/pyladiesams/events/298654058/)  
将举办一个研讨会，微调文本到图像的扩散模型以实现个性化等。  
  
[PyData Toronto 2024 年 2 月聚会](https://www.meetup.com/pydatato/events/298869004/)  
将有以下演讲：
  * 用于与 LLM 交互的数据文本处理
  * 因果推理：创建反事实，以及其在付费营销中的应用

  
[PyData Lisbon 2024 年 2 月聚会](https://www.meetup.com/pydata-lisbon/events/299085332/)  
将有以下演讲：
  * 利用 Poetry 掌握 Python 项目
  * 生产中的 LLM，即 LLMOps

  
[PyData Prague 2024 年 2 月聚会](https://www.meetup.com/pydata-prague/events/298734567/)  
将有以下演讲：
  * 解锁效率 —— 矢量化的力量
  * Jupyter（Hub/Lab）——从本地到 AWS 之旅
