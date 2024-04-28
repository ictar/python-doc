原文：[Python Weekly - Issue 647](http://eepurl.com/iOMKec)

---

欢迎来到《Python周刊》第 647 期。让我们直奔主题。

# 文章，教程和讲座  
  
[从头开始学习 RAG](https://www.youtube.com/watch?v=sVcwVQRHIc8) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
直接从一位 LangChain 软件工程师那里学习如何从头开始实现 RAG（Retrieval Augmented Generation，检索增强生成）。本 Python 课程教您如何使用 RAG 将您自定义的数据与大型语言模型 (LLM) 的强大功能相结合。 
  
[py2wasm 发布：一个从 Python 到 Wasm 的编译器](https://wasmer.io/posts/py2wasm-a-python-to-wasm-compiler)  
py2wasm 将您的 Python 程序转换为 WebAssembly，运行速度提高了 3 倍。  
  
[7 个令人兴奋的 Kubernetes Hack](https://overcast.blog/7-mind-blowing-kubernetes-hacks-36037e59bb54)  
Kubernetes 拥有的一些功能，即使是经验丰富的开发人员也可能没有完全意识到。这些 Hack 深入研究了更深奥但非常有效的技巧，能让掌握它们的人的能力得到显着增强。这些不是您的日常技巧，而是让 Kubernetes 做出惊人事情的深刻见解。  
  
[Python Big O：Python 中不同数据结构的时间复杂度](https://www.pythonmorsels.com/time-complexities/)  
本文主要是为那些已经了解时间复杂度的概念以及操作的时间复杂度如何影响代码的人提供的一份 Python 时间复杂度备忘单。   
  
[Django 的基本原则](https://www.mostlypython.com/django-from-first-principles-2/)  
许多人没有意识到使用单个文件就可以启动 Django 项目。本系列将逐步介绍从单个文件开始构建一个简单但不平凡的项目的过程。仅当需要将代码移出主文件时，该项目才会使用第二个文件。在本系列结束时，我们的项目都结构将类似于 startproject 和 startapp 生成的项目结构。  
  
[使用 Ollama 和 LlamaEdge，在本地运行 Llama 3](https://www.youtube.com/watch?v=wPuoMaD_SnY) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
Meta 推出了 Llama3，现在您可以使用 Ollama 在本地运行它。在本视频中，我将讲解如何使用 Ollama 操作各种语言模型，着重介绍 Llama2 和 Llama3。我还将指导您完成该项目的 WebUI，演示如何使用 Ollama 提供模型并使用 Python 与它们进行交互。  
  
[Django：使用 Git 精确定位上游更改](https://adamj.eu/tech/2024/04/24/django-pinpoint-upstream-git/)  
在这篇文章中，我们将介绍 Django 的分支结构、确定和搜索这些提交、一个有效的示例以及使用 git bisect 进行高级行为搜索。  
  
[使用 Django 和 OpenAI 构建一个语音笔记应用程序](https://circumeo.io/blog/entry/building-a-voice-notes-app-with-django-and-openai/)  
我们将构建一个使用 OpenAI 执行语音转文本的语音笔记应用程序。作为奖励，我们将使用 AlpineJS 来管理前端状态。  
  
[8 分钟内使用 HTMX 和 Django 构建一个四子棋游戏](https://www.photondesigner.com/articles/connect4-htmx)  
最后，您将利用 HTMX 构建一个多人游戏，使用简洁的服务器端逻辑并将所有结果存储在数据库中。 HTMX 是一种无需编写 JavaScript 即可使用 JavaScript 的好方法。  
  
[使用 Langfuse 装饰器 (Python) 跟踪复杂的 LLM 应用程序](https://langfuse.com/blog/2024-04-python-decorator)  
在构建 RAG 或代理时，大量 LLM 调用和非 LLM 输入会输入到最终输出中。 Langfuse装饰器可以让您进行整体追踪和评估。  
  
[通过构建您自己的 ChatGPT，学习如何将 Websockets 与 Django 结合使用](https://www.saaspegasus.com/guides/django-websockets-chatgpt-channels-htmx/)  
您需要了解的有关 Websockets 的所有信息，以便在您的应用程序中使用它们，包括 Django、通道和 HTMX。  
  
[我不小心搞了一个 meme 搜索引擎](https://harper.blog/2024/04/12/i-accidentally-built-a-meme-search-engine)  
又名：如何了解 Clip/siglip 和矢量编码图像。  
  
[Llama 3：五分钟内搞定 LLM 构建](https://www.denoise.digital/llama-3-get-started-with-llms/)  
使用 Llama 3、Ollama 和 Python，在 5 分钟内开始构建变革性的 AI 驱动功能。 
  
  
# 好玩的项目，工具和库  
  
[CoreNet](https://github.com/apple/corenet)  
Apple 用于训练深度神经网络的库。  
  
[Cria](https://github.com/leftmove/cria)  
Cria 是一个通过 Python 以编程方式运行大型语言模型的库。 Cria 的构建让您使用尽可能少的配置 - 即使是使用更高级的功能。  
  
[bridge](https://github.com/Never-Over/bridge)  
Django 的自动化基础设施。  
  
[Penzai](https://github.com/google-deepmind/penzai)  
用于构建、编辑和可视化神经网络的 JAX 研究工具包。  
  
[BeyondLLM](https://github.com/aiplanethub/beyondllm)  
构建、评估和观察 LLM 应用程序。  
  
[Hashquery](https://github.com/hashboard-hq/hashquery)  
用于定义和查询数据仓库中的 BI 模型的 Python 框架。  
  
[torchtune](https://github.com/pytorch/torchtune)  
用于 LLM 微调的 Native-PyTorch 库。  
  
[InstructLab](https://github.com/instructlab/instructlab)  
命令行界面。使用它与模型聊天或训练模型（训练消耗分类数据）  
  
[Portr](https://github.com/amalshaji/portr)  
专为团队设计的开源 ngrok 替代方案  
  
[anthropic-cookbook](https://github.com/anthropics/anthropic-cookbook)  
notebooks/使用案例的集合，展示了使用 Claude 的一些有趣且有效的方法。 
  
  
# 最新发布  
  
[llama3](https://github.com/meta-llama/llama3)  
parameters.此版本包括用于预训练和指令调整的 Llama 3 语言模型的模型权重和起始代码 - 包括 8B 到 70B 参数的大小。  
  
[PyTorch 2.3](https://pytorch.org/blog/pytorch2-3/)  
PyTorch 2.3 支持用户在 torch.compile 中定义 Triton 内核，允许用户在不经历性能回归或图形中断的清空下，从 eager 迁移自己的 Triton 内核。Tensor 并行改进了使用本机 PyTorch 函数训练大型语言模型的体验，该功能已在包含 100B 个参数模型的训练运行中得到验证。此外，半结构化稀疏性将半结构化稀疏性实现为Tensor子类，观察到的加速比密集矩阵乘法高达 1.6。  
  
# 近期活动和网络研讨会  
  
[PyData Amsterdam 2024 年 4 月聚会](https://www.meetup.com/pydata-nl/events/300230051/)  
将会有以下演讲：
  * 生成式人工智能将如何使史基浦机场的客户服务提升到新高度
  * 释放武士：Albert Heijn 是如何让员工轻松检索内容的
  
[Hybrid: Michigan Python 2024 年 4 月聚会](https://www.meetup.com/michigan-python/events/299947904/)  
将会有一场演讲：利用 DuckDB 加速 Python 数据分析。  
  
[PyData Seattle 2024 年 4 月聚会](https://www.meetup.com/pydata_seattle/events/299433457/)  
将会有以下演讲：
  * DBRX 简介：Databricks 推出的一个新的 SOTA 开放 LLM
  * Fidelius DBRXus：建立您自己私人霍格沃茨

