原文：[Python Weekly Issue 252](http://us2.campaign-archive2.com/?u=e2e180baf855ac797ef407fc7&id=816c3bd748&e=148158c7b4)

---

欢迎来到Python周刊第252期。

# 来自赞助商

[![](https://gallery.mailchimp.com/e2e180baf855ac797ef407fc7/images/866e6c32-8d91-41b2-a2f3-9abc496b7fc8.png)](https://software.intel.com/en-us/intel-sdp-home)

像专家一样进行性能配置！Intel® VTune™ Amplifier使用行级配置细节，低开销和调用堆栈列举，快速准确地识别Python/ C/C++代码和扩展中的热点。省了时间和猜测。更快地直达根因。[点击这里](https://software.intel.com/en-us/python-profiling)，进行免费使用吧。


# 文章，教程和讲座

[Pythonic代码的10个技巧](https://www.youtube.com/watch?v=_O23jIXsshs)

在这个演讲中，Michael Kennedy给你带来示范Pythonic代码的10个较流行有用的代码样例。在这些例子中，你将首先看到不Pythonic的代码，然后是更自然的Pythonic版本。涵盖的主题有：字典的广泛使用，通过slot来hack Python的内存使用，使用生成器、推导式和生成器表达式，通过切片创建集合子集（一直到数据库）等等。它们其中有些是Python 3的特性，因此，在你的下一个项目中，你会更有理由选择Python 3.

[第66集：更快的Python程序：度量，而不是猜测](https://talkpython.fm/episodes/show/66/faster-python-programs-measure-don-t-guess)

通常，我们认为Python是强大快捷的。但是当它不够快的时候，发生了什么事呢？你有过必须停止并用C, C#或者Java重写它的经验吗？好吧，在你做一些激烈的改动之前，Mike Mueller在这里教给我们一些用来确定我们的Python程序为什么会慢的技术和步骤，并给我们一些如何让它们更快的技巧。

[Python的"with open"和genexps教程(处理数百万行的文件)](https://www.youtube.com/watch?v=HpUokG6zZj0)

通过创建和读取一个10亿行的文本文件，我努力说明重要但鲜为人知的"with open"和生成器表达式技术，可以用来处理具有数百万行（或列）的数据文件，同时仍保持内存友好。

[和Tony D一起，Raspberry Pi关于Jupyter/IPython notebooks的快速预览!](https://www.youtube.com/watch?v=S0VmWFIXOjg)

学习如何在Pi上安装和运行Jupyter，以及如何用它来进行一些诸如图形传感数据这样的简单任务。

[Podcast.__init__ 第65集 - 与David Fisher和Greg Price谈谈MyPy](http://podcastinit.com/david-greg-mypy.html)

作为Python开发者，我们都喜欢这门语言的动态特性。虽然，有时，它有点太动态了，而这就是一些类型信息会派上用场的地方。MyPy是这样一个项目，它旨在添加细节的缺失层到函数和变量定义中，这样你就无需深入五层堆栈去理解数据结构应该长啥样了。本周，我们与David Fisher和Greg Price谈谈他们关于Mypy的工作，以及它在Dropbox和更广泛的社区中的使用。他们解释了它是如何开始的，它在幕后是如何工作的，以及为什么你应该考虑把它添加到你的项目中。

[第67集：使用Hypothesis进行基于属性的测试](https://talkpython.fm/episodes/show/67/property-based-testing-with-hypothesis)

[我勒个去，怎样构建一个Django, Django REST Framework, Angular 1.1.x和Webpack项目？](http://gregblogs.com/how-the-do-i-build-a-django-django-rest-framework-angular-1-1-x-and-webpack-project/)

[使用str.encode和threads冻结你的Python](https://blog.sqreen.io/freeze-python-str-encode-threads/)

[预览用于AWS的Python无服务器微框架](https://medium.com/@raiderrobert/how-to-make-a-webhook-receiver-in-django-1ce260f4efff)

[如何在DigitalOcean上部署一个Django应用](https://www.codementor.io/python/tutorial/how-to-deploy-a-django-application-on-digitalocean)

[与mypy的一天：第二部分](http://www.machinalis.com/blog/a-day-with-mypy-part-2/)

[Python中的小波图像哈希](https://fullstackml.com/2016/07/02/wavelet-image-hash-in-python/)

[如何在Django中制作一个Webhook Receiver](https://medium.com/@raiderrobert/how-to-make-a-webhook-receiver-in-django-1ce260f4efff)

[Python的反向调试](https://morepypy.blogspot.com/2016/07/reverse-debugging-for-python.html)

[PyConSG 2016视频集](https://www.youtube.com/playlist?list=PLECEw2eFfW7iTsIrldRO2b6NLEuRQYD2L)


# 书籍

[掌握Python数据分析(Mastering Python Data Analysis)](http://amzn.to/29PceZc)

通过这个全面的指南，你将以一种有意义的方式，利用统计分析来探索数据，以及展示结果和结论。你将能够快速准确地进行手动排序、还原和后续的分析，并充分理解数据分析方法能够如何支持业务决策。


# 好玩的项目，工具和库

[debpackager](https://github.com/urban48/debpackager)

debpackager是一个CLI工具，用于创建debian软件包，灵感来自于fpm和maven

[tplmap](https://github.com/epinna/tplmap)

Tplmap (Template Mapper，模板映射器的简称)是一个自动化检测和利用服务端模板注入漏洞(SSTI)过程的工具。开发者、渗透测试者和安全研究者可以用它来检查和利用与模板注入工具相关的漏洞。

[Beckett](http://phalt.co/beckett/)

Beckett是一个基于约定的框架，用于在HTTP API的基础上构建Python接口。

[docsbox](https://github.com/docsbox/docsbox)

带RESTful API的自托管文档转换服务。

[Lango](https://github.com/ayoungprogrammer/Lango)

Lango是一个自然语言处理库，用于语言的构造块。

[chalice](https://github.com/awslabs/chalice)

用于AWS的Python无主机微框架，允许你快速创建和部署使用Amazon API Gateway和AWS Lambda的应用。

[Githeat](https://github.com/AmmsA/Githeat)

为你的git repo提供互动式热图。

[filecrypt](https://github.com/massenz/filecrypt)&nbsp;

使用OpenSSL的全自动化文件加密。

[AntiRansom](https://github.com/YJesus/AntiRansom)

AntiRansom是一个能够使用蜜罐检测和阻止勒索软件攻击的工具。

[PytheM](https://github.com/m4n3dw0lf/PytheM)

PytheM是一个Python的渗透测试框架。


# 最新发布

[IPython 5.0](http://blog.jupyter.org/2016/07/08/ipython-5-0-released/)

这个版本有一些激动人心的新功能，以及许多新的开发（超过191 PR的27个贡献者的227次提交）。最重要的是，经典的IPython命令行界面带有了显著的改善。

[MicroPython 1.8.2](https://github.com/micropython/micropython/releases/tag/v1.8.2)

[ggplot 0.10.0](http://blog.yhat.com/posts/new-ggplot.html)


# 近期活动和网络研讨会

[DC Python Meetup July 2016 - Washington, DC](http://www.meetup.com/DCPython/events/230682987/)

[PyHou Meetup July 2016 - Houston, TX](http://www.meetup.com/python-14/events/226999484/)
