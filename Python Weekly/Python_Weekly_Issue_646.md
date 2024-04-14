原文：[Python Weekly - Issue 646](http://eepurl.com/iNSLLo)

---

欢迎来到《Python周刊》第 646 期。让我们直奔主题。  


# 文章，教程和讲座  
  
[SQLAlchemy：Python 中最好的 SQL 数据库库](https://www.youtube.com/watch?v=aAy-B6KPld8) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
你听说过 SQLAlchemy 并觉得它听起来像中世纪的魔药吗？嗯，事实并非如此！ SQLAlchemy 将 SQL 的稳健性与 Python 的灵活性相结合，使数据库管理不仅更容易，而且也很有趣！在本视频中，我将仔细看看这个很棒的工具。  
  
[优秀表格的设计理念](https://posit-dev.github.io/great-tables/blog/design-philosophy)  
本文讨论了精心设计的表格对于有效呈现数据的重要性，并从历史表格设计原则中汲取灵感。它强调了 Great Tables 库专注于提供各种自定义选项，以帮助用户为出版物、报告和其他数据驱动内容创建具有视觉吸引力和结构化的表格。  
  
[14 个 LLM 参加了 314 场街头霸王比赛。这里是赢家。](https://community.aws/content/2dbNlQiqKvUtTBV15mHBqivckmo/14-llms-fought-314-street-fighter-matches-here-s-who-won)探索问答任务之外的大型语言模型的新基准。了解 LLM 如何使用 Amazon Bedrock 在街头霸王 III 中竞争。  
  
[没有 API 的客户端库会更好](https://csvbase.com/blog/7)  
本文讨论了作者为 csvbase 构建客户端库的方法，重点是利用现有的文件系统抽象库（如 fsspec）来提供无缝的用户体验，而无需自定义 API。它强调了这种方法的好处，例如可以与已经支持 fsspec 接口的各种工具和库集成。
  
  
[使用 Django、Channels 和 HTMX 构建流式 ChatGPT 克隆](https://www.youtube.com/watch?v=8JSiiPW4S0A) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
此视频将引导您逐步使用 Django、websockets 和 HTMX 构建 ChatGPT 克隆。每个功能都被分解为一个提交，然后进行解释和演示。最后，一个功能齐全的流式聊天机器人应用程序就准备好了！  
  
[如何利用单行 Python 代码挺过编码面试](https://ivaniscoding.github.io/posts/codeinterview/)  
本文讨论了使用简洁的单行 Python 代码解决编码面试问题的技术。  
  
[Python 和 OpenAI 中的余弦相似度和文本嵌入](https://earthly.dev/blog/cosine_similarity_text_embeddings/)  
本文讨论了如何使用余弦相似度来比较文本嵌入（文本嵌入是捕获语义的文本向量表示），以确定不同文本输入之间的相似度。它提供了示例代码，计算使用 OpenAI API 生成的文本嵌入之间的余弦相似度。  
  
[六分钟内的 七个 Django GeneratedFiel 示例](https://www.youtube.com/watch?v=b7VkVHBX47w) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
我们将在 6 分钟内完成 7 个简短的示例。看完它们后，您将了解如何使用 Django 的 GeneratedField - 快速且整齐地使用数据库自动进行计算。  
  
[使用 Python、Datalore 和 AI Assistant，回测交易策略](https://blog.jetbrains.com/datalore/2024/04/05/backtesting-a-trading-strategy-in-python-with-datalore-and-ai-assistant/)  
本文将向您介绍在 Datalore 笔记本中使用 Python 回测每日道琼斯均值回归策略的过程。为了让编码经验有限的人也能使用它，作者利用了 Datalore 的 AI Assistant 功能。  
  
[使用 Python 预测日食](https://erikbern.com/2024/04/07/predicting-solar-eclipses-with-python)  
本文介绍了作者如何利用 Astropy 库和其他 Python 包，编写一个 Python 脚本来计算太阳和月亮的位置并预测日全食的位置，实现所有功能仅用了大约 100 行代码。   
  
  
# 好玩的项目，工具和库  
  
[AutoCodeRover](https://github.com/nus-apr/auto-code-rover)  
一个了解项目结构的自主软件工程师，旨在改进自主程序。  
  
[FreeAskInternet](https://github.com/nashsu/FreeAskInternet)  
FreeAskInternet 是一个完全免费、私有且本地运行的搜索聚合器，并使用 LLM 生成答案，无需 GPU。用户可以提出问题，然后系统会进行多引擎搜索，并将搜索结果合并到ChatGPT3.5 LLM中，再根据搜索结果生成答案。  
  
[Loki](https://github.com/Libr-AI/OpenFactVerification)  
用于事实验证的开源工具。  
  
[schedule_free](https://github.com/facebookresearch/schedule_free)  
PyTorch 中的无计划优化。  
  
[Mantis](https://github.com/PhonePe/mantis)  
Mantis 是一个安全框架，可自动执行发现、侦察和漏洞扫描的工作流程。
  
[open-parse](https://github.com/Filimoa/open-parse)  
改进了 LLM 的文件解析。  
  
  
# 最新发布  
  
[Visual Studio Code 中的 Python - 2024年四月版本](https://devblogs.microsoft.com/python/python-in-visual-studio-code-april-2024-release/)  
此版本包括以下内容：
* 改进了 Flask 和 Django 的调试配置流程
* 使用 Pylance,对 Jupyter 的运行依赖单元进行模块和导入分析
* 孵化环境发现
* Pipenv、pyenv 和 Poetry 项目的自动环境选择
* 报告问题命令的改进

  
[Python 3.11.9 现已发布](https://pythoninsider.blogspot.com/2024/04/python-3119-is-now-available.html)  
  
[Python 3.12.3 和 3.13.0a6 已发布](https://pythoninsider.blogspot.com/2024/04/python-3123-and-3130a6-released.html)  
  
  
# 近期活动和网络研讨会  
  
[Django London 2024 年 4 月聚会](https://www.meetup.com/djangolondon/events/299793290/)  
将会有一场演讲：面向数据的 Django Deux。  
  
[PyLadies London 2024 年 4 月聚会](https://www.meetup.com/pyladieslondon/events/299931279/)  
将会有以下演讲：
* 使用 Elasticsearch 和 Python 进行语义搜索
* Elastic 招聘 - 给求职者的提示和技巧
* 高效的 Python 项目设置：展示 Cookiecutter 在 Kedro 中的潜力 

  
[Portland Python 2024 年 4 月聚会](https://www.meetup.com/pdxpython/events/300085006/)  
将会有一场演讲：Python 中的生成器和迭代器。  
   
[PyData Southampton 2024 年 4 月聚会](https://www.meetup.com/pydata-southampton/events/299810812/)  
将会有以下演讲：
* 沙漠岛 Docker：Python 版本
* 流数据帧：Python 中处理流数据的新方法

  
[PyData Milano 2024 年 4 月聚会](https://www.meetup.com/pydata-milano/events/299954337/)  
将会有以下演讲：
* 能源市场的波动期权定价：一个动态规划方法
* 可解释人工智能：机遇与挑战

  
[PyData Stockholm 2024 年 4 月聚会](https://www.meetup.com/pydatastockholm/events/300163813/)  
将会有以下演讲：
* 用于基础模型预训练的大规模数据管理
* Sana AI - 如何使用生成式人工智能构建消费级企业产品

  
[PyData Manchester 2024 年 4 月聚会](https://www.meetup.com/pydata-manchester/events/299810769/)  
将会有以下演讲：
* 关于制表的传奇故事
* 开放数据和方法在气候价值分析中的重要性

