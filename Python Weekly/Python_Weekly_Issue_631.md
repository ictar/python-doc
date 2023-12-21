原文：[Python Weekly - Issue 631](http://eepurl.com/iGGpqI)

---

欢迎阅读《Python 周刊》第 631 期。这是2023年的最后一期。我们将在假期结束后回来。祝您及家人节日快乐！（译注：我也要去过节啦，大家节日快乐！！）  

# 新闻  
  
[PyPI 的 2FA 要求，开始于 2024-01-01](https://blog.pypi.org/posts/2023-12-13-2fa-enforcement/)  
从 2024 年 1 月 1 日开始，所有用户必须为其 PyPI 帐户启用双因素身份验证 (2FA) 才能执行任何管理操作或上传文件。此要求是 PyPI 为增强安全性所做的努力的一部分，仅需要浏览、下载和安装包的用户不会受到此更改的影响。  
  
  
# 文章，教程和讲座  
  
[Requests vs Httpx vs Aiohttp | 选哪个？](https://www.youtube.com/watch?v=OPyoXx0yA0I) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
该视频讨论了对应用程序中 API 通信的探索，比较了 requests、httpx 和 aiohttp 的使用。它展示了作者的首选项以及考虑该选择的理由。    
  
[掌握使用 GitHub Copilot 进行 AI 配对编程的方法](https://github.com/microsoft/Mastering-GitHub-Copilot-for-Paired-Programming)  
6 节课的课程，教授有关利用 GitHub Copilot 和 AI 配对编程资源所需了解的所有信息。  
  
[Python 应用程序中的配置：没有魔法，只是必要的实践](https://robertrode.com/2023/10/02/configuration-in-python-applications-no-magic-just-necessary-practice.html)  
本文探讨了 Python 应用程序中的配置实践，强调透明性的重要性并避免在此过程中使用魔法。它深入探讨了管理配置设置的实用方法，提倡 Python 应用程序开发中的清晰度和必要实践。  
  
[GILad 中的 Balm：CPython 扩展的快速字符串构造](https://blog.vito.nyc/posts/gil-balm/)  
一种优化在 Python 字符串上运行的 Python C 扩展的非正统方法。  
  
[构建个人预测文本引擎](https://jamesg.blog/2023/12/15/auto-write/)  
本文讨论了名为 AutoWrite 的个人预测文本引擎的开发过程，该引擎会考虑文档中已编写的单词，从而提供特定于上下文的自动完成功能。  
  
[我是如何使用 Python 构建 Okta 文档聊天机器人的](https://developer.okta.com/blog/2023/12/20/okta-documentation-chatbot)  
本文介绍了 Oktanaut 的开发过程，Oktanaut 是一个 Python 聊天机器人，可简化对 Okta 开发人员文档信息的访问。该聊天机器人是使用 OpenAI 和 Jupyter Notebook 创建的，并开发了两个版本来提供特定的方法和常识。第一个版本使用 LlamaIndex 并根据 Okta 的开发人员文档进行训练，基于其精度而生成准确的响应。  
  
[安全哈希冲突](https://www.da.vidbuchanan.co.uk/blog/colliding-secure-hashes.html)  
本文讨论了这么一个问题：作者使用 pyopencl 移植算法以在 GPU 上运行，导致安全哈希冲突。作者选择截断散列的中间部分而不是末尾部分，试图在视觉上欺骗某人比较全长散列的开头和结尾。   
  
[可重用 Django 应用的设置模式](https://overtag.dk/v2/blog/a-settings-pattern-for-reusable-django-apps)  
本文介绍了可重用 Django 应用程序的新设置模式，解决了使用项目设置覆盖应用程序默认设置的挑战。作者讨论了对清晰一致模式的需求，文章概述了建议的解决方案，其中涉及检查设置的前缀以避免返回 Django 设置的随机属性。  
  
[DjangoCon US 2023 视频集](https://www.youtube.com/playlist?list=PL2NFhrDSOxgX41jqYSi0HmO9Wsf6WDSmf) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
DjangoCon US 2023 的 YouTube 视频集合，包含该活动的各种视频。它涵盖了与 Django 开发相关的一系列主题，包括在会议上展示的演讲、研讨会和讨论。  
  
[异步任务取消的最佳实践](https://superfastpython.com/asyncio-task-cancellation-best-practices/)  
在本教程中，您将发现在 Python 中取消异步任务的最佳实践。 
  
[使用 Django 构建即时通讯工具（6 分钟内）](https://www.youtube.com/watch?v=-9h3Sjr2WKk) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
该视频提供了有关使用 Django 创建即时通讯工具的快速教程。本教程旨在以简洁的方式演示该过程，使其成为那些对使用 Django 开发消息应用程序感兴趣的人来说有用的资源。  
  
[理解 GPU 内存 1：可视化随时间变化的所有分配](https://pytorch.org/blog/understanding-gpu-memory-1/)  
第一部分显示了使用内存快照工具的方法。 


  * [第二部分](https://pytorch.org/blog/understanding-gpu-memory-2/) - 在这一部分中，我们将使用内存快照来可视化由循环引用引起的 GPU 内存泄漏，然后使用引用循环检测器在代码中定位并删除它们。

  
[使用 APIFlask 来保持数据 DRY](https://buildwithlayer.github.io/buildwithlayer/blog/dry_with_apiflask/)  
这篇文章讨论了如何使用 APIFlask（它的路由和模式符合 OpenAPI 规范）从而允许 API 文档的自动编写。它强调了保持数据 DRY（Don't Repeat Yourself，不要重复自己）的好处，以及在编写 API 时轻松记录 API 的好处。这篇文章深入介绍了 APIFlask 是如何帮助避免冗余代码，以及维护清晰简洁的代码库的。  
  
[Python 陷阱：列表复制问题](https://andrewwegner.com/python-gotcha-list-copy.html)  
复制 Python 列表（或任何可变对象）并不是将一个列表设置为另一个值那么简单。让我们讨论一个更好的方法来做到这一点。  
  
[适用于 LLMs 的 Bash One-Liners](https://justine.lol/oneliners/)  
六个可靠示例，说明 llamafile 是如何帮助您提高命令行使用效率的。  
  
[实际上，你可以并行使用多少个 CPU 核心？](https://pythonspeed.com/articles/cpu-thread-pool-size/)  
要弄清楚你的程序可以使用多少并行度是非常棘手的。  
  
[Python 中 `key` 参数的关键之处](https://www.thepythoncodingstack.com/p/the-key-to-the-key-parameter-in-python)  
名为 `key` 的参数存在于多个 Python 函数中，例如 `sorted()`。让我们探讨一下它是什么以及如何使用它。  
  
  
# 好玩的项目，工具和库  
  
[OpenVoice](https://github.com/myshell-ai/OpenVoice)  
一种多功能的即时语音克隆方法，只需要参考说话者的一个简短的音频剪辑即可复制他们的声音并生成多种语言的语音。  
  
[PromptBench](https://github.com/microsoft/promptbench)  
用于评估和理解大型语言模型的统一库。  
  
[generative-ai-python](https://github.com/google/generative-ai-python)  
Google AI Python SDK 使开发人员能够使用 Google 最先进的生成式 AI 模型（如 Gemini 和 PaLM）来构建 AI 驱动的功能和应用程序。  
  
[TwitchDropsMiner](https://github.com/DevilXD/TwitchDropsMiner)  
一款应用，允许您 AFK 挖掘定时 Twitch 掉落物，具有自动掉落物认领和频道切换功能。  
  
[feud](https://github.com/eonu/feud/)  
使用简单的惯用 Python 构建强大的 CLI，由类型提示驱动。  
  
[microagents](https://github.com/aymenfurter/microagents)  
能够自编辑提示/Python 代码的代理。  
  
[django-ninja-crud](https://github.com/hbakri/django-ninja-crud)  
声明式 CRUD 端点和测试，使用 Django Ninja。  
  
[skytrack](https://github.com/ANG13T/skytrack)  
使用 Python 制作的基于命令行的飞机定位和飞机 OSINT 侦察工具。  
  
[resemble-enhance](https://github.com/resemble-ai/resemble-enhance)  
AI 支持的语音去噪和增强。  
  
[whisper-plus](https://github.com/kadirnar/whisper-plus)  
高级语音到文本处理。  
  
[mergekit](https://github.com/cg123/mergekit)  
用于合并预训练大型语言模型的工具。  
  
[llm-mistral](https://github.com/simonw/llm-mistral)  
LLM 插件，提供对使用 Mistral API 的 Mistral 模型的访问。  
  
[tinyzero](https://github.com/s-casci/tinyzero)  
在您想的任何环境中轻松训练类似 AlphaZero 的智能体！     
