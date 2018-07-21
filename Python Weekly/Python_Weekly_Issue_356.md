原文：[Python Weekly - Issue 356 ](http://eepurl.com/dBzcPb)

---

欢迎来到Python周刊第 356 期。让我们直奔主题。


# 来自赞助商 
[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/f25123ca-75a9-4b27-af3b-518500134fcd.png)](https://www.datadoghq.com/dg/apm/ts-python-error-tracking/?utm_source=Advertisement&utm_medium=Advertisement&utm_campaign=PythonWeekly-Tshirt&utm_content=Python)

使用 Datadog 的分布式跟踪和 APM，全面了解你的 Python 应用。这个开源代理自动监测框架和库（例如 Django、Redis 和 asyncio），这样，你就可以在不更改代码的情况下开始。然后，在分布式请求跟踪、指标和日志之间进行关联和转换，从而无需切换工具或上下文即可对问题进行故障排除。[今天就开始 14 天的免费试用吧](https://www.datadoghq.com/dg/apm/ts-python-error-tracking/?utm_source=Advertisement&utm_medium=Advertisement&utm_campaign=PythonWeekly-Tshirt&utm_content=Python).   

  
# 新闻  
  
[权力转移](https://mail.python.org/pipermail/python-committers/2018-July/005664.html)  

Guido 宣布，他将作为 Python 的 BDFL 退休。
  
[ython 的安全漏洞警报](https://blog.github.com/2018-07-12-security-vulnerability-alerts-for-python/)  

如果你使用 Python，那么现在 Github 可以在你依赖易受攻击的软件包时示警了。
  
  
# 文章，教程和讲座
  
[机器学习基础](https://bloomberg.github.io/foml/#home)  

理解机器学习专家使用的概念、技术和数学框架。

[Python 中的基本统计：概率](https://www.dataquest.io/blog/basic-statistics-in-python-probability/)  

在这篇文章中，我们将讨论概率的概念、正态分布和 z-score —— 以葡萄酒数据。
  
[Python 中的列表和元组](https://realpython.com/python-lists-tuples/)  

你将学习到 Python 3 中的列表和元组的重要特性。你将学习如何定义它们和操作它们。结束之际，你应该对何时以及如何在一个 Python 程序中使用这些对象类型有所了解。
  
[OpenCV 显著性检测](https://www.pyimagesearch.com/2018/07/16/opencv-saliency-detection/)  

本教程将向你展示如何使用 OpenCV 的“saliency” 模块和 Python 来进行显著性检测。

[使用 Kubernetes 在 GCP 上进行 Django 生产部署](https://www.agiliq.com/blog/2018/07/django-on-kubernetes/)  

如何以生产模式，在 kubernetes 上部署一个 Django 应用 —— 最佳实践。
  
[我是如何使用 Python、Twilio 和 Google Calendar，让我的母上大人了解我的旅行计划的](https://www.twilio.com/blog/2018/06/how-i-keep-my-mom-updated-on-my-travel-schedule-with-twilio-and-google-calendar.html)

在这篇教程中，你将学习如何使用 Python 和 Twilio 构建一个短信机器人，它会连接到你的 Google Calendar，并且能够对即将发生的事作出响应。
  
[学习（不）处理异常](https://www.pythonforthelab.com/blog/learning-not-to-handle-exceptions/)  
我们将从错误处理的基础知识一直讲到定义你自己的异常。你将了解为什么不捕获异常更好的原因，以及如何开发对你的代码的未来用户有用的模式。异常是任何代码的关键部分，而优雅地处理它们可以提高代码的价值。

[使用 Ray，在 15 行 Python 中实现参数服务器](https://rise.cs.berkeley.edu/blog/implementing-a-parameter-server-in-15-lines-of-python-with-ray/)  

参数服务器是许多机器学习应用的核心部分。它们的作用是存储机器学习模型的参数（例如，神经网络的权重），并将其提供给客户端（客户端通常是处理数据和计算参数更新的 worker）。参数服务器（如数据库）通常作为独立系统构建和发布。这篇文章描述了如何使用 Ray，在几行代码中实现参数服务器。

[Python 中的自行车控制设计](https://plot.ly/ipython-notebooks/bicycle-control-design/)  

使用 NumPy、SciPy、Python Control 和 Plotly，设计自行车的顺序双环控制器
  
[比 Go 快得多的多核 Python HTTP 服务器（剧透：Cython）](https://www.nexedi.com/NXD-Blog.Multicore.Python.HTTP.Server)  

可以依靠 Cython 语言和 LWAN C 库构建出比 Go 快约 40% 到 110% 到多核 Python HTTP 服务器。概念验证证实了使用 Cython 语言的高性能系统编程的可能性。
  
[Python post-Guido](https://lwn.net/SubscriberLink/759756/d9b3110d86b9d373/)  
  
[SciPy 2018 视频集](https://www.youtube.com/playlist?list=PLYx7XA2nY5Gd-tNhm79CNMe_qvi35PgUR)  
  
[Flask 教程：简单用户注册和登录](https://developer.okta.com/blog/2018/07/12/flask-tutorial-simple-user-registration-and-login)  
  
[使用 Python、BeautifulSoup 和 Requests 查找和修复网站链接机器人](https://www.twilio.com/blog/2018/07/find-fix-website-link-rot-python-beautifulsoup-requests.html)  
  
[使用 Python 进行股票数据分析（第二版）](https://ntguardian.wordpress.com/2018/07/17/stock-data-analysis-python-v2/)  
  
[高效使用 Django Rest Framework 所需要了解的 10 件事](https://medium.com/profil-software-blog/10-things-you-need-to-know-to-effectively-use-django-rest-framework-7db7728910e0)  
  
  
# 好玩的项目，工具和库  
  
[XAR](https://github.com/facebookincubator/xar)  

XAR 允许你将多个文件打包到一个独立的可执行文件中。这使得分发和安装变得容易。
  
[Mu](https://codewith.mu/en/)   

给初学者的简单 Python 编辑器。

[repo2docker](https://github.com/jupyter/repo2docker)  

将 git 存储库转换成启用 Jupyter 的 Docker Image。 
  
[sorcery](https://github.com/alexmojaki/sorcery)  

python 中令人愉悦的黑魔法。这个包让你可以使用和编写名为“spells”的可调用对象，它们知道调用点，并且可以使用这些信息来做其他不可能完成的事情。
  
[PyFuck](https://github.com/Shubbler/PyFuck)  

命令行 brainfuck 解释器，用 Python 3 编写。
  
[Hamburglar](https://github.com/needmorecowbell/Hamburglar)  

从 URL、目录和文件中收集有用的信息。
  
[lagom](https://github.com/zuoxingdong/lagom)  

快速构建强化学习算法原型的轻量级 PyTorch 基础设施。

[Terminus](https://github.com/randy3k/Terminus)  

给 Sublime Text 的真正终端。

[ph0neutria](https://github.com/phage-nz/ph0neutria)  

ph0neutria 是一个恶意软件 zoo builder，直接从外部采集样本。所有内容都存储在 Viper 中，以便于访问和管理。

[TextWorld](https://github.com/Microsoft/TextWorld)  

TextWorld 是一个沙盒学习环境，用于基于文本的游戏中的强化学习（RL）代理的训练和评估。
  
[ansible-jupyter-kernel](https://github.com/ansible/ansible-jupyter-kernel)  

运行 Ansible 任务和 playbook 的 Jupyter Notebook 内核。

[Nagini](https://github.com/marcoeilers/nagini)  

Nagini 是一个基于 Viper 验证基础架构的 python 3 静态验证器。
  
[cc.py](https://github.com/si9int/cc.py)  

基于“commoncrawl.org” 的结果，提取特定目标的 URL。
  
  
# 最新发布  
  
[Jupyter Notebook 5.6.0](https://jupyter-notebook.readthedocs.io/en/stable/changelog.html)  
  
[Django 2.1 release candidate 1](https://www.djangoproject.com/weblog/2018/jul/18/django-21-rc1/)  
  