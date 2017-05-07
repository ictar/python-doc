# 快速安装指南

在你开始使用 Django 之前，你需要先安装它。我们有一个 [完整安装指南](https://docs.djangoproject.com/en/1.11/topics/install/) 它涵盖了所有的安装步骤和可能遇到的问题；本指南将会给你在浏览这些介绍时需要的一个最简单、简洁的安装指引。

## 安装 Python

作为一个 Python Web 框架，Django 需要使用 Python 。查阅[Django可以使用什么版本的Python？](https://docs.djangoproject.com/en/1.11/faq/install/#faq-python-version-support)来获取详细内容。Python包含一个轻量级的数据库名叫 [SQLite](https://sqlite.org/) 。因此你现在还不需要安装一个数据库。

在 https://www.python.org/download/ 获取最新版本的Python ，或者使用你的操作系统的包管理器来安装。

>在 Jython 使用 Django
如果你使用 [Jython](http://www.jython.org/) (一个在 Java 平台上实现的 Python )，你需要遵循一些额外的步骤。查看 [在 Jyton 上运行 Python](https://docs.djangoproject.com/en/1.11/howto/jython/) 获取详细信息。

你可以通过在终端命令行（shell）中输入`python`来验证Python是否被安装；如果是，你应该会看见如下内容：
```bash
Python 3.4.x
[GCC 4.x] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

## 设置数据库
这一步仅仅在你需要使用类似PostgreSQL, MySQL, 或 Oracle这样的的“大型”数据库引擎时必要。如果要安装这样一个数据库，参考[数据库安装信息](https://docs.djangoproject.com/en/1.11/topics/install/#database-installation)

## 移除旧版本的Django
如果你是从旧版本的Django升级安装，你需要[在安装新版本之前卸载旧版本的Django](https://docs.djangoproject.com/en/1.11/topics/install/#removing-old-versions-of-django)。

## 安装Django
你有三个简单的选择来安装Django：
- 安装[官方发布的版本](https://docs.djangoproject.com/en/1.11/topics/install/#installing-official-release)，这是大多数用户最好的选择。
- 安装你[的操作系统所提供的发行包](https://docs.djangoproject.com/en/1.11/topics/install/#installing-distribution-package) 版本。
- 安装最新的开发版本。这个选择是给那些想要最新和最棒的特性。并且不害怕运行全新的代码的热情的用户。在开发版本中，你可能会遭遇bug，但是报告bug会帮助Django的开发。同时，与最新稳定版本相比，第三方包与开发版本的兼容性会更低。

>**总是参考你所使用的对应版本的 Django 文档！**
如果采用了前两种方式进行安装，你需要注意在文档中标明**在开发版中新增**的标记。这个标记表明这个特性仅适用开发版的 Django ，而在官方发布版本中可能不起作用。

## 验证
要验证Django是否成功被安装到Python中，先在终端命令行中输入`python`，在Python提示符下，尝试导入Django：
```bash
>>> import django
>>> print(django.get_version())
1.11
```
你可能已经安装了其他版本的Django。

## 安装完成！
安装完成，现在你可以继续阅读[教程](https://docs.djangoproject.com/en/1.11/intro/tutorial01/)部分。