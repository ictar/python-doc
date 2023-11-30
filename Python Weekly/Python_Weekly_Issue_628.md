原文：[Python Weekly - Issue 628](http://eepurl.com/iE8zjE)

---

欢迎来到Python周刊第 628 期。**Y Combinator** 公司中的其中一家正在寻找一名具有 6 年以上经验、在 ETL、图形 DB 和基于 Python 的 REST 后端方面拥有专业知识，并且准备为 B2B 销售技术构建尖端解决方案的**创始工程师**。这个角色在美国是远程工作的。This role is remote in the US. 我很了解这家公司的创始人。如果您拥有必要的技能和经验并且有兴趣，请向我发送您的简历。  
     

# 文章，教程和讲座  
  
[大语言模型简介](https://www.youtube.com/watch?v=zjkBMFhNj_g) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
Andrej Karpathy 的大型语言模型简介讲座涵盖了 ChatGPT、Claude 和 Bard 等系统背后的核心技术组件。它们是什么、它们的发展方向、与当今操作系统的比较和类比，以及这种新计算范式的一些与安全相关的挑战。 
  
[Rust std fs 比 Python 慢！？不，是硬件的锅！](https://xuanwo.io/2023/04-rust-std-fs-slower-than-python/)  
加入我们，踏上一段富有启发性的旅程，从 opendal 的 op.read() 开始，走向惊人，作者沿途会分享他们的经验教训。  
  
[使用 Numba 解锁 pandas.DataFrame.apply 中的 C 级性能](https://labs.quansight.org/blog/unlocking-c-level-performance-in-df-apply)  
快速概述 DataFrame.apply 中的新的 Numba 引擎。  
  
[Spinning Up in Deep RL](https://spinningup.openai.com/en/latest/)  
由 OpenAI 制作的教育资源，让您可以更轻松地了解深度强化学习（deep RL）。 
  
[Python 作为值的错误：比较来自 Go 和 Rust 的有用模式](https://www.inngest.com/blog/python-errors-as-values)  
更安全的错误处理，受 Go 和 Rust 启发。  
  
[在 Python 中构建一个小型 REPL](https://bernsteinbear.com/blog/simple-python-repl/)  
在我之前用 Python 编写的许多解释器/编译器项目中，我手动编写了一个 REPL（read-eval-print-loop，读取-评估-打印-循环）。事实证明，Python 附带了一堆完备的功能，以至于它们显得完全没有必要——你免费就能获得很多好东西。让我们看看如何使用它们，就从在项目中嵌入普通的 Python REPL 开始。  
  
[在 vanilla Django 中构建 Bootstrap 风格的表单](https://smithdc.uk/blog/2023/bootstrap_form_in_vanilla_django)  
在这篇文章中，David Smith 探索了在 vanilla Django 中创建 Bootstrap 风格的表单而不依赖第三方包的方法。他讨论了自定义表单小部件、添加 CSS 类以及创建自定义字段模板。  
  
[使用 Sub Interpreter 运行 Python 并行应用](https://tonybaloney.github.io/posts/sub-interpreter-web-workers.html)  
Python 3.12 引入了一个新的“sub interpreter” API，这是一种不同的 Python 并行执行模型，它在多处理的真正并行性之间提供了很好的折衷，但启动时间更快。这篇文章将解释什么是sub interpreter为什么它对于 Python 中的并行代码执行很重要、以及它与其他方法之间的比较。  
  
[CPython 对象系统内部结构：了解 PyObject 的角色](https://codeconfessions.substack.com/p/cpython-object-system-internals-understanding)  
了解 CPython 中是如何实现对象的，以及 CPython 如何使用结构嵌入模拟 C 中的继承和多态性。  
  
[Robot Dad](https://blog.untrod.com/2023/11/robot-dad.html)  
这是一篇关于一位父亲的文章，他创建了一个名为 Robot Dad 的聊天机器人来回答他儿子的问题。这个聊天机器人能够通过 Google 搜索来访问和处理来自现实世界的信息。Robot Dad 还可以用来翻译语言。 
  
[Python 中简单的 WebSocket 基准测试](https://lemire.me/blog/2023/11/28/a-simple-websocket-benchmark-in-python)  
  
[Python，Asyncio 和 Footguns](https://ryanc118.medium.com/python-asyncio-and-footguns-8ebdb4409122)  
  
[LMQL —— 用于语言模型的 SQL](https://towardsdatascience.com/lmql-sql-for-language-models-d7486d88c541)  
  
  
 # 好玩的项目，工具和库  
  
[rags](https://github.com/run-llama/rags)  
在您的数据上构建 ChatGPT，全部使用自然语言。  
  
[easylkb](https://github.com/deepseagirl/easylkb)  
易用的 Linux Kernel Builder。  
  
[kanban-python](https://github.com/Zaloog/kanban-python)  
用 Python 编写的终端看板应用，可用来提高您的工作效率。  
  
[ProAgent](https://github.com/OpenBMB/ProAgent)  
从机器人流程自动化到代理流程自动化。  
  
[autometrics-py](https://github.com/autometrics-dev/autometrics-py)  
轻松将指标添加到您的代码中，这些指标实际上可以帮助您发现和调试生产中的问题。基于 Prometheus 和 OpenTelemetry 构建。  
  
[Subtitle](https://github.com/innovatorved/subtitle)  
开源字幕生成，用于无缝内容翻译。  
  
[Breezy](https://github.com/breezy-team/breezy)  
具有用户友好界面的分布式版本控制系统。  
  
[Flask-Muck](https://github.com/dtiesling/flask-muck)  
Flask-Muck 是一个功能齐全的框架，用于在 Flask/SqlAlchemy 应用程序堆栈中，自动生成带有创建、读取、更新和删除 (CRUD) 端点的 RESTful API。 
  
[self-operating-computer](https://github.com/OthersideAI/self-operating-computer)  
使多模式模型能够操作计算机的框架。 
  
[apple-home-key-reader](https://github.com/kormax/apple-home-key-reader)  
该项目提供了使用 Python 构建 Apple Home Key Reader 的演示。  
  
[sysaidmin](https://github.com/skorokithakis/sysaidmin/)  
Sysaidmin 是为你的机器准备的，由 GPT 提供支持的系统管理员。你可以要求它解决问题，它会在您的系统上运行命令（经过您的许可）来调试正在发生的情况。 
  
[Meditron](https://github.com/epfLLM/meditron)  
Meditron 是一套开源医疗大语言模型（LLM）。  
  
[pip.wtf](https://pip.wtf/)  
小型 Python 脚本的内联依赖项。
  
  
 # 近期活动和网络研讨会  
  
[PyData Cambridge Meetup 2023 年 12 月](https://www.meetup.com/pydata-cambridge-meetup/events/297337394/)  
将会有一场演讲：使用 Polars 进行网络安全中的实用检测工程。
  
[PyData Amsterdam Meetup 2023 年 12 月](https://www.meetup.com/pydata-nl/events/297098652/)  
将有以下演讲：
  * 智能银行：释放 AI 的潜力
  * 支付处理中的多模型优化
  * 金融科技的模型选择：优缺点
  
[PyData London Meetup 2023 年 12 月](https://www.meetup.com/pydata-london-meetup/events/297585435/)  
将举行以下会议：
  * Python 依赖管理器大对决
  * 为什么我切换到 Windows

  
[VilniusPY #24](https://www.meetup.com/vilniuspy/events/297507169/)  
将有以下演讲：
  * 重新认证：如何成为一名成功的入门级开发人员
  * 将 Python 项目迁移到 K8s
