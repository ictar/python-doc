原文：[Python Weekly - Issue 626](http://eepurl.com/iD6vfU)

---

欢迎来到Python周刊第 626 期。让我们直奔主题。

# 新闻  
  
[PyPI 已经完成了首次安全审计](https://blog.pypi.org/posts/2023-11-14-1-pypi-completes-first-security-audit/)  
此次审计由网络安全公司 Trail of Bits 负责，重点关注 Warehouse 代码库以及 cabotage 容器编排框架。审计人员发现了29个建议，但没有一个被分类为高危。PyPI 团队已纠正了所有构成重大风险的建议。  
  
  
# 文章，教程和讲座  
  
[什么是 Monad？](https://www.youtube.com/watch?v=Q0aVbqim5pE) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
Monad 是函数式编程语言（如Haskell）中的一个众所周知的概念，但它们在其他情境中是否同样有用呢？请继续关注，到本视频结束时，您将了解什么是 Monad。

  
[让我们一起创造一个 Python 调试器吧](https://mostlynerdless.de/blog/2023/09/20/lets-create-a-python-debugger-together-part-1/)  
您是否曾想过调试器是如何工作的？设置断点并稍后命中它时会发生什么呢？调试器是我们作为开发人员在日常工作中经常使用的工具，但很少有人知道它们实际上是如何实现的。以下是有关从零开始编写 Python 调试器系列，包含四部分。  
  
[快速浏览目标驱动的代码生成](https://bernsteinbear.com/blog/ddcg/)  
想象一下：你坐在那里，正在写一个编译器，突然间你不得不生成汇编。你有一些中间表示（IR），但现在你必须将虚拟寄存器转换为机器寄存器。这就是所谓的寄存器分配（register allocation）。寄存器分配很棘手。它也很慢。即使像线性扫描这样非常快的寄存器分配器也可能占据大部分编译时间。所以让我们跳过它。让我们用 Python 写一个愚蠢的编译器，看看我们是否可以在不进行完全寄存器分配的情况下改进生成的代码。  
  
[在Python软件包索引中查询每个发布的每一个文件](https://sethmlarson.dev/security-developer-in-residence-weekly-report-18)  
本文介绍了查询 Python 软件包信息数据集的问题。它讨论了如何下载数据集以及其中包含了什么信息。该数据集可用于回答有关 Python 软件包趋势的问题。例如，它可用于跟踪新的打包元数据标准的采用情况。  
  
[使用 django-watson 为 Django 应用添加全文搜索](https://idiomaticprogrammers.com/post/django-watson-full-text-search-guide/)  
学习如何通过 Django-Watson 在 Django 应用中添加全文搜索，深入了解 Postgres 的奥秘并提升搜索功能。  
  
[深入研究 PyPI 软件包名称占用](https://blog.orsinium.dev/posts/py/pypi-squatting/)  
本文讨论了 PyPI 软件包名称占用的问题，以及攻击者可以如何利用它来分发恶意代码。  
  
[解混淆 World of Warships 的 Python 脚本](https://landaire.net/world-of-warships-deobfuscation/)  
对World of Warships 如何混淆其游戏脚本以及如何在很大程度上对其进行解混淆的深入分析。   
  
[Python 中基于属性的测试](https://www.se-radio.net/2023/11/se-radio-589-zac-hatfield-dodds-on-property-based-testing-in-python/) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/9a9a57d0-eb4b-47f8-8af4-55ba50e8c350.png)  
Anthropic 的保障团队负责人 Zac Hatfield-Dodds 与主持人 Gregory M. Kapfhammer 讨论了基于属性的测试技术，以及如何在名为 Hypothesis 的开源工具中使用它们。他们讨论了如何为 Python 函数定义属性并在 Hypothesis 中实现一个测试用例。他们还探讨了 Hypothesis 中的一些高级功能（可以自动生成测试用例并执行模糊测试活动）。  
  
  
# 好玩的项目，工具和库  
  
[MonkeyPatch](https://github.com/monkeypatch/monkeypatch.py)  
构建可扩展的 LLM 驱动应用程序的最简单方法，随着时间推移变得更便宜和更快。  
  
[Movis](https://github.com/rezoo/movis)  
用代码的方式编辑视频

[dpoint](https://github.com/Jcparkyn/dpoint)  
使用相机跟踪和惯性测量的开源数字触控笔。  
  
[narrator](https://github.com/cbh123/narrator)  
大卫·艾滕伯勒为您叙述生活。  
  
[mirror](https://github.com/cocktailpeanut/mirror)  
在您的笔记本电脑上的可黑客化 AI 驱动镜子。  
  
[filequery](https://github.com/MarkyMan4/filequery)  
使用 SQL 查询 CSV、JSON 和 Parquet 文件。 
  
[bulk_transcribe_youtube_videos_from_playlist](https://github.com/Dicklesworthstone/bulk_transcribe_youtube_videos_from_playlist)  
使用 Whisper，轻松将整个 YouTube 播放列表转换为高质量的脚本。  
  
[vimGPT](https://github.com/ishan0102/vimGPT)  
使用 GPT-4V 和 Vimium 浏览网络。  
  
[multi-object-tracking-in-python](https://github.com/kharitonov-ivan/multi-object-tracking-in-python)  
在 Python 中实现多目标跟踪算法，包括 PMBM（泊松多伯努利混合滤波器）。  
  
  
# 近期活动和网络研讨会  
  
[PyLadies Dublin Meetup 2023 年 11 月](https://www.meetup.com/pyladiesdublin/events/296639107/)  
将有以下演讲：
* Pinkie Pacts - 基于消费者的合同测试
* 关于再保险风险建模的介绍，使用 Python

[PyLadies London Meetup 2023 年 11 月](https://www.meetup.com/pyladieslondon/events/297095588/)  
将有以下演讲：
* 使用 ClinicalTrials.gov API 解锁有关妇女健康的见解
* 使用 Neo4j Vector Search 将患者匹配到临床试验

  
[Python Milano Meetup 2023 年 11 月](https://www.meetup.com/python-milano/events/297364921/)  
将有以下演讲：
* Kubernetes Operators：用 Pythonic 的方式
* 边缘设备上的视觉体系结构

  
[PyData Prague Meetup 2023 年 11 月](https://www.meetup.com/pydata-prague/events/297072175/)  
将有以下演讲：
* 成为 Streamlit 的数据叙述者！
* 检索增强生成（RAG）在实践中的应用

  
[PyData Southampton Meetup 2023 年 11 月](https://www.meetup.com/pydata-southampton/events/296812566/)  
将有以下演讲：
* 谨上您的真诚，Streamlit
* 车队监控：使用 Django 和 Plotly Dash 构建可扩展的云应用程序

  
[PyData Lisbon Meetup 2023 年 11 月](https://www.meetup.com/pydata-lisbon/events/297070615/)  
将有以下演讲：
* LLM 系统的趋势和挑战
* AI 启示还是 AI 革命？- 产品经理作为 AI 守护者的角色

  
[PyData Stockholm Meetup 2023 年 11 月](https://www.meetup.com/pydatastockholm/events/297112148/)  
将有以下演讲：
* FastAPI 入门：ML 的技巧和窍门
* 谁需要 ChatGPT？使用 Hugging Face 和 Kedro 打造坚固的 AI 管道
