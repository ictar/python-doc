原文：[Python Weekly - Issue 640](http://eepurl.com/iK4M4-/)

---

欢迎来到《Python周刊》第 640 期。让我们直奔主题。

# 文章，教程和讲座  
  
[生成人工智能完整课程 – Gemini Pro、OpenAI、Llama、Langchain、Pinecone、矢量数据库等](https://www.youtube.com/watch?v=mEsleV16qdo) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
了解生成式模型和不同的框架，研究人工智能生成的文本和视觉材料的生成。  
  
[GPT Pilot ——  我们在CodeGen 结对程序员 GPT Pilot 上工作 6 个月期间，学到了什么](https://blog.pythagora.ai/2024/02/19/gpt-pilot-what-did-we-learn-in-6-months-of-working-on-a-codegen-pair-programmer/)  
本文讨论了在 CodeGen 结对程序员 GPT Pilot 上工作 6 个月的经验教训，旨在让人类开发人员理解代码库，并详细解释了关于添加代码以促进人类开发人员和 AI 在编码任务中的协作。  
  
[七分钟解释依赖注入](https://www.youtube.com/watch?v=DpMGEhwuuyA) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
该视频解释了为什么依赖注入会改变您的编码项目的游戏规则。创建松散耦合的代码，是使代码更灵活、更可维护的关键。依赖关系的隐式使用使得这一切成为了可能。  
  
[将 Mistral Large 部署到 Azure，并使用 Python 和 LangChain 创建对话](https://neon.tech/blog/deploy-mistral-large-to-azure-and-chat-with-langchain)  
将 Mistral Large 部署到 Azure 的分步指南。  
  
[requests 回顾](https://blog.ian.stapletoncordas.co/2024/02/a-retrospective-on-requests)  
python-requests 已经存在很长时间了。而我已经担任它的维护者很多年了，我分享了一些对该项目的回顾性想法。  
  
[Mamba：艰难之路](https://srush.github.io/annotated-mamba/hard.html)  
一篇关于 Mamba 的文章，Mamba 是一种最新的神经架构，可以粗略地看作是一种现代循环神经网络 (RNN)。该模型的效果非常好，也是无处不在的 Transformer 架构的合法竞争对手。它已经引起了很多关注。  
  
[为什么来自 R 的人会觉得 Pandas 笨重](https://www.sumsar.net/blog/pandas-feels-clunky-when-coming-from-r)  
这篇文章讨论了从 R 过渡到 Python 中的 Pandas 的挑战和看法，探讨了习惯使用 R 的用户可能会觉得 Pandas 笨重的原因。作者提供了见解和技巧，以简化过渡过程并改进 Pandas 的使用体验。  
  
[高级检索增强生成：从理论到 LlamaIndex 实现](https://towardsdatascience.com/advanced-retrieval-augmented-generation-from-theory-to-llamaindex-implementation-4de1464a9930)  
如何通过在 Python 中实现有针对性的高级 RAG 技术，来解决原始 RAG 管道的局限性。  
  
[使用 Mistral 7B 和 Ollama 构建一个打字助手](https://www.youtube.com/watch?v=IUTFrexghsQ) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
在本 Python 教程中，我们将使用 Mistral 7B 和 Ollama 构建一个在本地运行的打字助手。您还将学习如何使用 Python 实现热键侦听器和键盘控制器。请按照此分步编码教程进行操作。  
  
[使用 Python 的高级 Web 抓取：从任何站点提取数据](https://jacobpadilla.com/articles/advanced-web-scraping-techniques)  
本文介绍了如何获取和管理 cookie 和自定义标头、避免 TLS 指纹识别、识别要发送请求的重要 HTTP 标头，以及如何实现指数退避的 HTTP 请求重试。  
  
[在 5 分钟内构建检索增强一代聊天机器人](https://www.youtube.com/watch?v=N_OOfkEWcOk) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
NVIDIA 高级解决方案架构师 Rohan Rao 在 5 分钟内，仅用了 100 行 Python 代码，就演示了如何为 AI 聊天机器人应用程序开发和部署大型语言模型 (LLM)，而无需使用您自己的 GPU 基础设施。    
  
[Python 依赖项是可以修复的](https://matduggan.com/everyone-is-wrong-but-you/)  
本文批评了 Python 依赖项管理的当前状态，强调需要更好的默认值和用户体验。与 Golang 的成功设计相似，作者主张 Pypa 生态系统内的心态转变，以改善 Pip 等工具的默认行为，并建议在必要时探索替代方案。  
  
  
# 好玩的项目，工具和库  
  
[ingestr](https://github.com/bruin-data/ingestr)  
ingestr 是一个 CLI 工具，可以使用单个命令在任何数据库之间无缝复制数据。    
  
[ok-robot](https://github.com/ok-robot/ok-robot)  
一个开放的模块化框架，用于在任意家庭中执行零样本、语言条件的拾取和放置任务。   
  
[Evo](https://github.com/evo-design/evo)  
从分子到基因组规模的 DNA 基础模型。  
  
[AutoPrompt](https://github.com/Eladlev/AutoPrompt)  
Auto Prompt 是一个 prompt 优化框架，旨在增强和完善现实世界用例的提示。  
  
[PyRIT](https://github.com/Azure/PyRIT)  
用于生成 AI 的 Python 风险识别工具 (PyRIT) 是一个开放访问的自动化框架，使安全专业人员和机器学习工程师能够主动发现他们的生成式 AI 系统中的风险。  
  
[gemma_pytorch](https://github.com/google/gemma_pytorch)  
Google Gemma 模型的官方 PyTorch 实现。  
  
[justpath](https://github.com/epogrebnyak/justpath)  
检查并优化 Windows 和 Linux 上的 PATH 环境变量。  
  
[Mountaineer](https://github.com/piercefreeman/mountaineer)  
Mountaineer 是一个包含开箱即用功能的 Python 和 React Web 框架。  
  
[sqlbind](https://github.com/baverman/sqlbind)  
基于文本的轻量级 SQL 参数绑定。  
  
[hotpdf](https://github.com/weareprestatech/hotpdf)  
hotpdf 是一个快速的 PDF 解析库，用于在 PDF 文档中提取文本并查找文本，在 pdfminer.six 之上构建。  
  
[Sensei](https://github.com/migtissera/Sensei)  
使用 OpenAI 或 MistralAI 生成合成数据。  
  
  
# 最新发布  
  
[JupyterLab 4.1 和 Notebook 7.1 来咯](https://blog.jupyter.org/jupyterlab-4-1-and-notebook-7-1-are-here-20bfc3c10217)  
JupyterLab 4.1 和 Notebook 7.1 现已推出！这些版本包括一些新功能、错误修复和扩展开发人员的增强功能。此版本兼容支持 JupyterLab 4.0 和 Notebook 7.0 的扩展。  
  
[Visual Studio Code 中的 Python - 2024 年 3 月版](https://devblogs.microsoft.com/python/python-in-visual-studio-code-march-2024-release/)  
此版本包括以下公告：
* 新添加导入代码操作启发式设置
* 调试 Django 或 Flask 应用程序时，自动启动浏览器
* Python REPL 的 Shell 集成
* 对本地运行的 Jupyter 服务器的语言支持
  
  
# 近期活动和网络研讨会  
  
[PyData London 2024 年 3 月聚会](https://www.meetup.com/pydata-london-meetup/events/299219741/)  
将有以下演讲：
* 构建单一事实来源：开发大规模实体解析系统
* 人工智能的未来：开源 LLM

  
[IndyPy 2024 年 3 月聚会](https://www.meetup.com/indypy/events/298740418/)  
将会有一场演讲：Eclipse Insights：人工智能如何改变太阳天文学。  
  
[Python Milano 2024 年 3 月聚会](https://www.meetup.com/python-milano/events/299383407/)  
将有以下演讲：
* Python 和 （棋盘）游戏设计
* Python 和设计师

  
[Cleveland PyLadies 2024 年 3 月聚会](https://www.meetup.com/cle-pyladies/events/298846189/)  
将进行有关 Prompt 工程领域的讨论和学习。  
  
[PyData Sudwest 2024 年 3 月聚会](https://www.meetup.com/pydata-suedwest/events/299017089/)  
将有以下演讲：
* 超越 Parquet 的默认设置 – 您会得到令人惊讶的结果 - Uwe Korn 
* 分析和优化模型预测服务 -Paolo Rechia

[PyData Manchester 2024 年 3 月聚会](https://www.meetup.com/pydata-manchester/events/299463978/)  
将会有一场演讲：开发有价值的 ML 产品。  
  
[PyData Atlanta 2024 年 3 月聚会](https://www.meetup.com/pydata-atlanta/events/299285864/)  
将会有一场演讲：释放商业应用中生成式 AI 的力量。  
     