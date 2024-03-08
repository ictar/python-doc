原文：[Python Weekly - Issue 641](http://eepurl.com/iLzPwU)

---

欢迎来到《Python周刊》第 641 期。让我们直奔主题。

# 文章，教程和讲座  
  
[Python 升级手册](https://eng.lyft.com/python-upgrade-playbook-1479145d52f4)  
在这篇文章中，我们将介绍 Lyft 如何大规模升级 Python（涵盖 150 多个团队的 1500 多个存储库），以及我们为优化升级所需的总体时间以及我们工程师所需的工作而构建的工具和策略的最新迭代。我们通过多次升级（从 Python 2 到 Python 3.10）成功地使用（并改进）了这个手册。  
  
[构建一个 LLM 微调数据集](https://www.youtube.com/watch?v=pCX_3p40Efc) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
该视频分享了有关使用 Reddit 评论构建用于 AI 模型训练的综合数据集的见解，强调了诸如处理大型数据集和过滤掉低质量评论这样的挑战。作者讨论了加载、排序和过滤数据等方法来创建训练样本和模型，强调了可用于分析的注释样本丰富性以及微调模型以实现高效训练的过程。最终，他们决定使用 llama 27b 模型，并讨论利用 480 超级 GPU 更快地微调和上传数据集的计划。  
  
[使用 Python 和 Grafana 实现更好的 PC 冷却](https://calbryant.uk/blog/better-pc-cooling-with-python/)  
本文讨论了使用 Python 和 Grafana 改进 PC 冷却，关注于通过利用热质量并以经验得出的速度运行风扇，找到最小和最大风扇速度来优化冷却性能，从而提高冷却效率并降低冷噪音水平。  
  
[使用 HTMX 和 Django，在六分钟内创建一个测验应用程序](https://www.photondesigner.com/articles/quiz-htmx)  
本指南向您展示如何在 6 分钟内使用 Django 和 HTMX 创建一个简单的测验应用程序。HTMX 非常适合用来创建动态 Web 应用程序，无需编写 JavaScript。  
  
[Django 中的多语言支持](https://medium.com/@sakhawy/multilingual-support-in-django-5706e1e144a8)  
本文深入探讨了在 Django 中实现多语言支持的复杂性，探讨了国际化 (i18n) 和本地化所涉及的挑战和流程。它提供了有关 Django 如何促进字符串翻译、GNU gettext 框架的使用以及 Django 项目中支持多种语言的整体框架的见解。  
  
[2024 年的机器学习 —— 新手课程](https://www.youtube.com/watch?v=bmmQA8A-yUA) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
该机器学习课程是为 2024 年学习机器学习的初学者创建的。该课程从 2024 年机器学习路线图开始，强调职业道路和适合初学者的理论。然后，课程将转向实际应用和使用 Python 的综合端到端项目。  
  
[DSPy 简介：再见 Prompting，你好 Programming！](https://towardsdatascience.com/intro-to-dspy-goodbye-prompting-hello-programming-4ca1c6ce3eb9)  
DSPy 框架是如何通过用编程和编译代替提示，来解决基于 LLM 的应用程序中的脆弱性问题的。  
  
[处理一个 CSV 文件能有多快](https://datapythonista.me/blog/how-fast-can-we-process-a-csv-file)  
本文探讨了处理 CSV 文件的速度，重点介绍了如何使用 PyArrow 来显着提高 CSV 读取速度。它比较了来自使用 C 引擎的 pandas、纯 Python 循环以及使用 PyArrow 引擎的 pandas 的不同方法，展示了 PyArrow 在更快、更有效地处理 CSV 文件方面的效率。  
  
[复杂度级别：RAG 应用](https://jxnl.github.io/blog/writing/2024/02/28/levels-of-complexity-rag-applications)  
这篇文章是一个综合指南，用以理解和实现不同复杂程度下的 RAG 应用程序。无论您是渴望学习基础知识的初学者，还是希望加深专业知识的经验丰富的开发人员，您都会找到宝贵的见解和实践知识来帮助您完成您的旅程。让我们一起开始这一激动人心的探索，释放 RAG 应用程序的全部潜力吧。  
  
[将 Rust 和 Python 组合在一起：两全其美？](https://www.youtube.com/watch?v=lyG6AKzu4ew) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
该视频向您展示如何使用 Pyo3 将 Rust 与 Python 无缝集成在一起。该库允许您使用 Rust 编写 Python 模块。这意味着我们同时可以获得 Rust 的速度和安全性以及 Python 易于使用的功能！  
  
[在 Kubernetes 中部署 Django 应用](https://blog.jetbrains.com/pycharm/2024/03/deploying-django-apps-in-kubernetes/)  
了解无论您是 Django 开发人员还是 Kubernetes 爱好者，您都可以在 Kubernetes 环境中无缝优化 Django 部署。  
  
[conda 环境入门](https://www.dataschool.io/intro-to-conda-environments/)  
这篇文章解释了虚拟环境的好处，以及如何在 conda 中使用虚拟环境。  
  
[从 Beeps 到 Toots：用 Python 和 Mastodon 复兴寻呼机](https://finnley.dolphinhome.net/2024/02/25/from-beeps-to-toots-reviving-pagers-with-python-and-mastodon/)  
本文展示了 Python 编程的一个独特应用，将寻呼机与 Mastodon 连接起来，将寻呼机的可靠性与现代社交媒体交互融为一体。它强调了 Python 在桥接寻呼机等模拟设备与数字平台方面的多功能性，展示了传统通信方法在当今数字时代的持久相关性。  
  
[使用 JupyterLab Desktop CLI 进行 Python 环境管理](https://blog.jupyter.org/python-environment-management-using-jupyterlab-desktop-cli-e57485c9287c)  
这篇文章讨论了使用 JupyterLab Desktop CLI 进行 Python 环境管理的方法，该工具提供了各种命令和选项，以便在应用程序内有效地管理 Python 环境。其内容涵盖了设置 JupyterLab Desktop CLI、创建新的 Python 环境以及利用捆绑的环境安装程序或从注册中心下载软件包来增强开发过程。  
  
[改善你的 Python 项目架构的六种方式（使用 import-linter）](https://www.piglei.com/articles/en-6-ways-to-improve-the-arch-of-you-py-project/)  

这篇文章讨论了六种增强Python项目架构的方法，重点是维护包和模块之间清晰的依赖关系，以避免纠缠在一起的模块间依赖。它解决了像新手对高架构的理解成本，以及在大型项目中难以定位代码导致开发效率降低这样的挑战。  
  
  
# 好玩的项目，工具和库  
  
[Hatchet](https://github.com/hatchet-dev/hatchet)  
一个分布式、容错的任务队列。  
  
[BlendSQL](https://github.com/parkervg/blendsql)  
统一的语言，用于协调 SQLite 逻辑和 LLM 推理。  
  
[django-admin-shellx](https://github.com/adinhodovic/django-admin-shellx)  
一个使用 Xterm.js 和 Django Channels 的 Django 管理后台 Web Shell。  
  
[Bonito](https://github.com/BatsResearch/bonito)  
一个轻量级的库，用于为您的数据生成合成的指令调整数据集，无需使用 GPT。  
  
[FastUI](https://github.com/pydantic/FastUI)  
更快更好的构建 UI。  
  
[Hancho](https://github.com/aappleby/hancho)  
一个简单而愉快的构建系统，用 Python 编写。  
  
[Cadwyn](https://github.com/zmievsa/cadwyn)  
生产就绪的、由社区驱动的、现代的类似于Stripe的API，在 FastAPI 框架中对此 API 进行版本管理和控制。 
  
[flect](https://github.com/Chaoyingz/flect)  
受 Next.js 启发的纯 Python 全栈 Web 应用程序框架。  
  
[pfl](https://github.com/apple/pfl-research)  
用于私有联合学习模拟的 Python 框架。  
  
[EvalPlus](https://github.com/evalplus/evalplus)  
EvalPlus 用于对 LLM 合成代码进行严格评估。  
  
[polars_ds_extension](https://github.com/abstractqqq/polars_ds_extension)  
适用于一般数据科学用例的 Polars 扩展。  
  
  
# 最新发布  
  
[Django 安全版本已发布：5.0.3，4.2.11 和 3.2.25](https://www.djangoproject.com/weblog/2024/mar/04/security-releases/)  
  
  
 # 近期活动和网络研讨会  
  
[San Francisco Python 2024 年 3 月聚会](https://www.meetup.com/sfpython/events/297988890/)  
将会有以下演讲：
* 电子表格有什么作用？！？
* 非结构化：LLM的 ETL
* 数据管道记分卡
* 帮助开发者自助
  
[Django London 2024 年 3 月聚会](https://www.meetup.com/djangolondon/events/299189999/)  
将会有以下演讲：
* WASM 支持的 Django 应用程序，使用 PyScript
* 2024 年求职指南
  
[PuPPy 2024 年 3 月聚会](https://www.meetup.com/psppython/events/299203397/)  
将会有以下演讲：
* 在量子计算机上模拟 3 偏振器实验
* 为什么我喜欢计划，你也应该如此！
* 上升的海（The Rising Sea）

  
[Virtual: PyMNtos Python 演示之夜 #123](https://www.meetup.com/pymntos-twin-cities-python-user-group/events/299328386/)  
将会有以下演讲：
* 关于《Python 简介》第三版的想法
* 使用 CLIP 进行多模态图像搜索
  
[PyData NYC 2024 年 3 月聚会](https://www.meetup.com/pydatanyc/events/299507856/)  
将会有以下演讲：
* 在生产中构建LLM应用程序
* 但是，您对超参数的调整够了吗？
  
[PyData Johannesburg 2024 年 3 月聚会](https://www.meetup.com/pydata-johannesburg/events/299175850/)  
将会有一场演讲：揭秘 Apache Nifi 的数据摄取挑战。  
  
[PyData Zurich 2024 年 3 月聚会](https://www.meetup.com/pydata-zurich/events/298797804/)  
将会有一场演讲：基于注意力的自回归模型的多功能性。  
