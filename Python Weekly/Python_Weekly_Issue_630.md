原文：[Python Weekly - Issue 630](http://eepurl.com/iF-TCw)

---

欢迎来到Python周刊第 630 期。让我们直奔主题。    

  
# 文章，教程和讲座  
  
[Vector Search RAG 教程 —— 通过高级搜索，将您的数据与 LLMs 结合起来](https://www.youtube.com/watch?v=JEBDfGqrAUA) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
了解如何使用矢量搜索（Vector Search）和嵌入轻松地将数据与 GPT-4 等大型语言模型相结合。您将首先学习概念，然后创建三个项目。  
  
[在运行时注释](https://blog.glyph.im/2023/12/annotated-at-runtime.html)  
PEP 593 对于如何实际使用 Annotated 参数这一点有些含糊其辞；这是我的建议。  
  
[pytest 守护进程：10 倍本地测试迭代速度](https://discord.com/blog/pytest-daemon-10x-local-test-iteration-speed)  
在 Discord，他们的 Python 测试套件本地测试运行缓慢，每次测试需要 13 秒。他们构建了一个 pytest 守护进程，将本地测试迭代速度提高了 10 倍，显着缩短了开发时间。该解决方案涉及将繁重的工作卸载到后台进程并缓存结果，绕过缓慢的导入和固定装置。  
  
[在 Python 中执行 a + b 需要多少行 C 代码？](https://codeconfessions.substack.com/p/cpython-dynamic-dispatch-internals)  
了解 CPython 中动态调度实现的机制。  
  
[为什么使用 TYPE_CHECKING？](https://vickiboykis.com/2023/12/11/why-if-type_checking/)  
本文讨论了 Python 中条件导入的使用，特别是 if TYPE_CHECKING 模式，以解决 mypy 等工具强制执行的类型检查和运行时类型检查的差异。它探讨了在处理存在大量相互依赖并可能导致循环依赖的自定义类时，是否需要此模式。  
  
[对扇出模式（Fanout Pattern）的解释](https://www.better-simple.com/django/2023/12/06/fanout-pattern-explained)  
本文讨论了扇出模式在 Celery 任务上下文中的使用，其中单个任务可以替换为数量可变的其他任务。它提供了一个关于扇出模式的实际示例，及其在处理复杂任务签名中的应用，强调了它在 Celery 框架内的任务设计和管理中的强大功能。通过代码示例说明了扇出模式，展示了其处理顺序和并行任务执行的能力，提供了对于其任务编排和工作负载分配的有效性的一些见解。  
  
[Django：使用 nh3 清理传入的 HTML 片段](https://adamj.eu/tech/2023/12/13/django-sanitize-incoming-html-nh3/)  
让我们看看如何在 Django 表单中使用 nh3 进行 HTML 清理。您可以将此方法应用于其他情况，例如在 DRF 序列化器中。  
  
[现实中的 match/case](https://nedbatchelder.com/blog/202312/realworld_matchcase.html)  
Python 3.10 引入了结构模式匹配，称为 match/case，它允许匹配数据结构中的模式。本文提供了一个使用匹配/大小写来简化对从 GitHub 机器人接收的复杂 JSON 有效负载的检查的真实示例，展示了其在处理深度嵌套数据结构方面的实际应用。作者强调了与传统的分离有效负载的方法相比，match/case 如何使任务变得更加简单  
  
  
# 好玩的项目，工具和库  
  
[coffee](https://github.com/Coframe/coffee)  
使用 AI 直接在您自己的 IDE 上构建和迭代您的 UI，速度提高 10 倍。  
  
[µHTTP](https://github.com/0x67757300/uHTTP)  
µHTTP 的出现源于对简单 Web 框架的需求。它非常适合微服务、单页应用程序以及单体（架构）这类庞然大物。  
  
[PurpleLlama](https://github.com/facebookresearch/PurpleLlama)  
用于评估和提高 LLM 安全性的工具集。  
  
[Arrest](https://github.com/s-bose/arrest)  
Arrest 是一个小型的实用程序，用以使用 pydantic 和 httpx 轻松构建和验证 REST API 调用。  
  
[LLMCompiler](https://github.com/SqueezeAILab/LLMCompiler)  
用于并行函数调用的 LLM 编译器。 
  
[Pearl](https://github.com/facebookresearch/Pearl)  
由 Meta 的应用强化学习团队带来的可投入生产的强化学习 AI 代理库。  
  
[Mamba-Chat](https://github.com/havenhq/mamba-chat)  
Mamba-Chat 是第一款基于状态空间模型架构而非转换器的聊天语言模型。  
  
[Netchecks](https://github.com/hardbyte/netchecks)  
Netchecks 是一组用于测试网络状况，并断言它们是否符合预期的工具。  
  
[concordia](https://github.com/google-deepmind/concordia)  
用于生成社会模拟的库。  
  
[Cyclopts](https://github.com/BrianPugh/cyclopts)  
基于 python 类型提示的直观、简单的 CLI。  
  
[UniDep](https://github.com/basnijholt/unidep)  
具有 pip 和 conda 要求的单一事实来源。  
  
  
# 最新发布  
  
[Visual Studio Code 中的 Python - 2023 年 12 月版本](https://devblogs.microsoft.com/python/python-in-visual-studio-code-december-2023-release/)  
此版本包括以下声明：
* 运行按钮菜单中添加了可配置的调试选项
* 使用 Pylance 显示类型层次结构
* 停用对终端中自动激活的虚拟环境的命令支持
* 可设置打开/关闭 REPL 智能发送以及不支持时的消息

  
[Python 3.12.1](https://www.python.org/downloads/release/python-3121/)  