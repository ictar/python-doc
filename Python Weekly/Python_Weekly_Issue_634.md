原文：[Python Weekly - Issue 634](http://eepurl.com/iIfkrc)

---

欢迎来到《Python周刊》第 634 期。让我们直奔主题。 
 
# 文章，教程和讲座  
  
[构建可用于生产的 FastAPI 后端的 4 个技巧](https://www.youtube.com/watch?v=XlnmN4BfCxw) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
该视频讨论了您在大多数 FastAPI 在线教程中通常找不到的 4 件事。这些技巧非常有用，特别是如果您想创建可以在生产环境中使用的后端。  
  
[Python 打包，一年后：Python 打包 2023 年回顾](https://chriswarrick.com/blog/2024/01/15/python-packaging-one-year-later/)  
一年前，我写了一篇关于 Python 打包的悲催状态的文章。这个领域有大量的工具，强调编写模糊的标准而不是围绕一个真正的工具展开，还关注复杂的基于 venv 的生态系统而不是类似于 node_modules 的解决方案。那么，过去一年发生了什么变化呢？有什么改善吗？一切都还维持原样吗？或者情况比以前更糟了吗？  
  
[用于更快的 Python C 扩展的类型信息](https://bernsteinbear.com/blog/typed-c-extensions/)  
PyPy 是 Python 语言的替代实现。PyPy 的 C API 兼容性层存在着一些性能问题。Carl Friedrich Bolz-Tereick 和我正在研究一种使 PyPy 的 C API 交互速度更快的方法。它看起来非常有前途。这里是其工作原理的草图。  
  
[AlphaGeometry：奥林匹克级别的几何 AI 系统](https://deepmind.google/discover/blog/alphageometry-an-olympiad-level-ai-system-for-geometry/)  
这篇文章介绍了 AlphaGeometry，这是一个人工智能系统，可以解决复杂的几何问题，其水平接近人类奥林匹克金牌得主。本文重点介绍了该系统在标准时限内解决了 30 道奥林匹克几何问题中的 25 道的表现，反映了其在数学人工智能推理方面的重大进步。它提供了对 AlphaGeometry 的发展和功能的见解，将其定位为人工智能用于解决几何问题领域的突破性成就。  
  
[用一行代码即可节省 1 TB RAM](https://www.youtube.com/watch?v=Hgw_RlCaIds) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
Anthony Sottile 展示了他在工作中所做的一个产生巨大影响的小改变，并解释了它是如何工作的！  
  
[我们是如何对 PyTorch 执行关键的供应链攻击的](https://johnstawinski.com/2024/01/11/playing-with-fire-how-we-executed-a-critical-supply-chain-attack-on-pytorch/)   
这篇文章讨论了对 PyTorch 的供应链攻击的执行，强调了潜在影响以及破解 PyTorch 供应链的途径。它深入研究了漏洞识别、攻击执行以及此类攻击在网络安全和供应链完整性背景下的重要性。 
  
[潜伏者代理（Sleeper Agents）：训练持续通过安全性训练的欺骗性 LLM](https://arxiv.org/pdf/2401.05566.pdf)  
该研究探讨了大型语言模型 (LLM) 如何能够表现出持久性欺骗行为，例如根据指定的年份编写安全代码，但在年份发生变化时引入可利用的代码。当前的安全训练技术，包括监督微调和对抗性训练，很难检测和消除这些欺骗性策略，引发了人们对确保人工智能安全的标准方法有效性的担忧。  
  
[数据代码的结构应该有多好?](https://blog.dagworks.io/p/how-well-structured-should-your-data)  
这篇文章探讨了性能和系统可靠性之间的权衡，特别是在数据科学的背景下。它深入探讨了 ML 模型原型设计者所面临的挑战，讨论了快速行动的压力，以及放弃工作还是担任生产中机器学习工程师角色的决策过程。  
  
[GPT 中自注意力的直观指南：威尼斯假面舞会](https://twiecki.io/blog/2024/01/04)   
这篇文章讨论了 Transformer 架构中的自注意力机制（self-attention mechanism），使用威尼斯假面舞会的隐喻来解释现代人工智能中的这一概念。它的目的是通过提供直观且相关的解释，远离压倒性的数学细节和技术术语，使复杂的自注意力概念更容易理解。  
  
[Flask 中的另一个密码重置教程](https://freelancefootprints.substack.com/p/yet-another-password-reset-tutorial)  
密码重置流程的实现，有一些变化。  
  
[使用 Python 创建 UI - 使用 PyQt5 创建音乐播放器](https://www.youtube.com/watch?v=DjutoyfCl2c) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
通过使用 PyQt5 框架创建现代音乐播放器来了解如何使用 Python 创建 UI。该应用程序的一些功能包括：美丽而现代的用户界面、播放列表和最喜爱的歌曲功能、不同页面的自定义上下文菜单以及每首歌曲的背景幻灯片。 

  
[Polars 简介](https://pbpython.com/polars-intro.html)  
本文概述了 Polars DataFrame 库，强调其目标是提供快如闪电的 DataFrame 库，以优化查询、处理大型数据集并维护一致且可预测的 API。它将 Polars 与其他解决方案进行了比较，并介绍了那些使其成为 Python 中高性能数据操作和分析的引人注目的选项的设计决策。  
  
[在空气隔离系统上运行 Python](https://iahmed.me/post/python-air-gapped/)  
如何在无法访问 Internet 的系统上可重复地运行 Python 代码。  
> 译注：空气隔离指计算机或计算机网络与其他设备或网络之间没有物理连接，以防止数据泄露或恶意攻击。
  
[Python 中的同步](https://thiagowfx.github.io/2024/01/synchronized-in-python/)  
在Java中，只需添加 synchronized 关键字就可以使变量线程安全。在Python中可以通过什么东西来达到相同的结果吗？  
  
[避免黑盒 API 调用](https://rednafi.com/misc/eschewing_black_box_api_calls/)  
  
[htmx 是可组合的？？](https://timkellogg.me/blog/2024/01/17/htmx)  
  
[The curious case of Pydantic 的奇怪案例，以及 1970 年代的时间戳](https://dev.arie.bovenberg.net/blog/pydantic-timestamps/)  
  
  
 # 好玩的项目，工具和库  
  
[marimo](https://github.com/marimo-team/marimo)  
一个反应式 Python notebook，可复制、git 友好且可作为脚本或应用程序部署。  
  
[surya](https://github.com/VikParuchuri/surya)  
适用于任何语言的精确行级文本检测和识别 (OCR)。  
  
[pathway](https://github.com/pathwaycom/pathway)  
Pathway 是一个高吞吐量、低延迟的数据处理框架，可以为您处理实时数据和流。    
  
[DataMapPlot](https://github.com/TutteInstitute/datamapplot)  
创建漂亮的数据地图。  
  
[Python-Redlines](https://github.com/JSv4/Python-Redlines)  
Docx 跟踪了 Python 生态系统的变更红线。  
  
[phidata](https://github.com/phidatahq/phidata)  
使用 LLM 函数调用构建自主助手。
  
  
 # 近期活动和网络研讨会  
  
[IndyPy 2024 年 1 月聚会](https://www.meetup.com/indypy/events/297838473/)  

将会有一场演讲，高级模块和AI，针对小白。  
  
[ThaiPy - Bangkok Python 2024 年 1 月聚会](https://www.meetup.com/thaipy-bangkok-python-meetup/events/297400014/)  
将有以下演讲：
  * 使用 Python 和 Apache Kafka 进行流处理
  * 为什么大型语言模型需要稀疏向量？

  
[Python Barcelona 2024 年 1 月聚会](https://www.meetup.com/python-barcelona/events/298506663/)  
将有以下演讲：
  * 在公司内启动自己的Python库
  * 使用 PyPDF 掌握 PDF 表单填写

  
[PyLadies Paris 2024 年 1 月聚会](https://www.meetup.com/pyladiesparis/events/298401634/)  
将有以下演讲：
  * 利用机器学习进行慢性肾脏病的早期检测
  * 了解 SEO 对比测试：针对搜索引擎进行优化

  
[Virtual: PyData Chicago 2024 年 1 月聚会](https://www.meetup.com/pydatachi/events/298499243/)  
将会有一场演讲，Securday：自然语言网络扫描仪。  
  
[PyData Zurich 2024 年 1 月聚会](https://www.meetup.com/pydata-zurich/events/298392043/)  
将有以下演讲：
  * Vega-Altair：一个简单、友好且功能强大的数据可视化库
  * 关于数据科学软技能的故事

  
[PyData Milano 2024 年 1 月聚会](https://www.meetup.com/pydata-milano/events/298391373/)  
将有以下演讲：

  * 谁需要 ChatGPT？使用 Hugging Face 和 Kedro 打造坚如磐石的 AI 管道
  * 在 Vertex AI 上使用 Ray 解锁可扩展的机器学习

  
[PyData Sudwest 2024 年 1 月聚会](https://www.meetup.com/pydata-suedwest/events/296355526/)  
将有以下演讲：
  * 转变您的业务：通过数据运营实现数据驱动的路线图
  * 使用 LakeFS 和 LakeFS-Spec 进行数据版本控制
  * 把我的数据留在这里！以节省数据的方式实现人工智能助手

  
[PyData Prague 2024 年 1 月聚会](https://www.meetup.com/pydata-prague/events/298421104/)  
将有以下演讲：
  * 使用 BirdNET 进行人工智能驱动的生物声学监测
  * （不仅仅是）大型语言模型成功背后的数据