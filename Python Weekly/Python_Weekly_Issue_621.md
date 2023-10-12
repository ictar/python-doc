原文：[Python Weekly - Issue 621](http://eepurl.com/iBCBtY)

---

欢迎来到Python周刊第 621 期。让我们直奔主题。


# 文章、教程和讲座 
  
[几分钟内构建您的第一个 Pytorch 模型！](https://www.youtube.com/watch?v=tHL5STNJKag) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
在本视频中，我们将通过实践来学习！构建您的第一个 PyTorch 模型（可以对扑克牌图像进行分类）。
  
[使用单个 GPU，在 Python 代码上微调 Mistral7B！](https://wandb.ai/byyoung3/ml-news/reports/Fine-Tuning-Mistral7B-on-Python-Code-With-A-Single-GPU---Vmlldzo1NTg0NzY5)  
虽然 Mistral 7B 的开箱即用令人印象深刻，但其微调能力仍有巨大潜力。本教程旨在指导您完成针对特定用例（Python 编码）微调 Mistral 7B 的过程！我们将利用 HuggingFace 的 Transformers 库、DeepSpeed（用于优化）和 Choline（用于简化在 Vast.ai 上部署）等强大工具。
  
[使用 Python 管道方法，优雅地进行模块化数据处理](https://github.com/dkraczkowski/dkraczkowski.github.io/tree/main/articles/crafting-data-processing-pipeline)  
深入研究错综复杂的数据处理通常感觉就像在错综复杂的迷宫中航行。我们构建了这些复杂的流程，却只是为了避免破坏它们而对其保持原样。但如果我们可以改进它呢？以下是我对用 Python 构建更易于维护、模块化的数据处理工作流程的看法，该工作流程倾向于“管道和过滤器”架构模式。
  
[使用 Pandas Dropna 处理缺失数据](https://ponder.io/professional-pandas-handling-missing-data-with-pandas-dropna/)  
在这篇文章中，我们将通过探索世界幸福报告来学习如何使用 pandas dropna 处理缺失数据。
  
[从 Python 调用 Rust](https://blog.frankel.ch/rust-from-python/)  
从这篇文章学习从 Python 调用 Rust 的三种不同方法：HTTP、IPC 和 FFI。  
  
[如何在 SaaS 平台中使用 LLM](https://www.youtube.com/watch?v=fH8fJYWfJcg) ![](https://mcusercontent.com/e2e180baf855ac797ef407fc7/images/af76283a-6e65-436c-967a-900427cf6399.png)  
该视频将引导您了解在名为 Learntail 的 SaaS 平台中使用了多大的语言模型。Learntail 是一款易于使用的人工智能测验生成工具。
  
[Django 中的 RegisterFields](https://www.better-simple.com/django/2023/10/03/registerfields-in-django/)  
对一个 Django 模型字段的解释，该字段根据键返回类的实例。
  
[Python 类型提示：pyastgrep 案例学习](https://lukeplant.me.uk/blog/posts/python-type-hints-pyastgrep-case-study/)  
作者分享了他们在工具 pyastgrep 中向 Python 代码添加类型提示的经验。他们讨论了使用静态类型检查和交互式编程来帮助捕获错误并提高代码可读性的挑战和好处。
  
# 好玩的项目，工具和库  
  
[LeptonAI](https://github.com/leptonai/leptonai)  
一个用于简化 AI 服务构建的 Pythonic 框架。 
  
[RealtimeSTT](https://github.com/KoljaB/RealtimeSTT)   
强大、高效、低延迟的语音转文本库，具有先进的语音活动检测、唤醒词激活和即时转录功能。专为诸如语音助手这类实时应用程序而设计。
  
[ziggy-pydust](https://github.com/fulcrum-so/ziggy-pydust)   
用于在 Zig 中构建 Python 扩展的工具包。

[LLM-scientific-feedback](https://github.com/Weixin-Liang/LLM-scientific-feedback)  
大型语言模型能否为研究论文提供有用的反馈？大规模的实证分析。 
  
[genai-stack](https://github.com/docker/genai-stack)  
这个 GenAI 应用程序栈将让您立即开始构建自己的 GenAI 应用程序。演示应用程序可以作为灵感或起点。
  
[Chrome-GPT](https://github.com/richardyc/Chrome-GPT)   
控制桌面版 Chrome 的 AutoGPT 代理。

[streaming-llm](https://github.com/mit-han-lab/streaming-llm)  
具有注意力接收器的高效流式语言模型。
  
[torch2jax](https://github.com/samuela/torch2jax)  
在 JAX 中运行 PyTorch。
  
[swiss_army_llama](https://github.com/Dicklesworthstone/swiss_army_llama)  
一种用于语义文本搜索的 FastAPI 服务，使用预先计算的嵌入和高级相似性度量，提供通过 textract 内置对各种文件类型的支持。
  
  
# 最新发布  
  
[Visual Studio Code 中的 Python —— 2023 年 10 月版本](https://devblogs.microsoft.com/python/python-in-visual-studio-code-october-2023-release/)  
此版本包括以下声明：
  * Python 调试器扩展更新
  * 弃用对 Python 3.7 的支持
  * Pylint 扩展的更改选项上的 Lint
  * Mypy 扩展报告范围和守护进程模式
  * Grace Hopper 会议和开源日参与

  
  
# 近期活动和网络研讨会  
  
[ThaiPy #96](https://www.meetup.com/thaipy-bangkok-python-meetup/events/295498832/)  
将有以下讲座：
* Python GIL。过去与未来
* 利用 GPU 加速 100 倍

  
[PyLadies 2023 年 10 月都柏林聚会](https://www.meetup.com/pyladiesdublin/events/295990212/)  
将进行以下演讲：
* 想再次成为一个小朋友吗？年龄永远不是问题！
* 打印您自己的冒险游戏

  
[PyData 2023 年 10 月南安普顿聚会](https://www.meetup.com/pydata-southampton/events/296057081/)  
将进行以下演讲：
* 地理空间数据和处理简介
* MoleGazer：天文学与皮肤学的结合

  
[PyData 2023 年 10 月柏林聚会](https://www.meetup.com/pydata-berlin/events/296680621/)  
将有一场演讲：利用开源 LLMs 进行生产。
  
[PyData 2023 年 10 月剑桥聚会](https://www.meetup.com/pydata-cambridge-meetup/events/296429788/)  
将有一场演讲：使用 AI 技术设计和测试现代桌面棋盘游戏。
     