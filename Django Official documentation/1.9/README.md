# Django 文档

原文： [Django documentation](https://docs.djangoproject.com/en/1.9/)

---

你所需要知道的关于Django的一切。

## 该文档如何组织

Django has a lot of documentation. A high-level overview of how it’s organized will help you know where to look for certain things:

* [Tutorials](https://docs.djangoproject.com/en/1.9/intro/) take you by the hand through a series of steps to create a Web application. Start here if you’re new to Django or Web application development. Also look at the “First steps” below.
* [Topic guides](https://docs.djangoproject.com/en/1.9/topics/) discuss key topics and concepts at a fairly a fairly high level and provide useful background information and explanation.
* [Reference guides](https://docs.djangoproject.com/en/1.9/ref/) contain technical reference for APIs and other aspects of Django’s machinery. They describe how it works and how to use it but assume that you have a basic understanding of key concepts.
* [How-to guides](https://docs.djangoproject.com/en/1.9/howto/) are recipes. They guide you through the steps involved in addressing key problems and use-cases. They are more advanced than tutorials and assume some knowledge of how Django works.


----------


## 第一步

你是Django或者编程的新手？这里就是开始的地方！

* **From scratch**: Overview | Installation
* **Tutorial: Part 1**: Requests and responses | Part 2: Models and the admin site | Part 3: Views and templates | Part 4: Forms and generic views | Part 5: Testing | Part 6: Static files | Part 7: Customizing the admin site
* **Advanced Tutorials**: How to write reusable apps | Writing your first patch for Django


----------


## 模型层

Django provides an abstraction layer (the “models”) for structuring and manipulating the data of your Web application. Learn more about it below:

* **Models**: Introduction to models | Field types | Meta options | Model class
* **QuerySets**: Executing queries | QuerySet method reference | Lookup expressions
* **Model instances**: Instance methods | Accessing related objects
* **Migrations**: Introduction to Migrations | Operations reference | SchemaEditor | Writing migrations
* **Advanced**: Managers | Raw SQL | Transactions | Aggregation | Custom fields | Multiple databases | Custom lookups | Query Expressions | Conditional Expressions | Database Functions
* **Other**: Supported databases | Legacy databases | Providing initial data | Optimize database access | PostgreSQL specific features


----------


## 视图层

Django has the concept of “views” to encapsulate the logic responsible for processing a user’s request and for returning the response. Find all you need to know about views via the links below:

* **The basics**: URLconfs | View functions | Shortcuts | Decorators
* **Reference**: Built-in Views | Request/response objects | TemplateResponse objects
* **File uploads**: Overview | File objects | Storage API | Managing files | Custom storage
* **Class-based views**: Overview | Built-in display views | Built-in editing views | Using mixins | API reference | Flattened index
* **Advanced**: Generating CSV | Generating PDF
Middleware: Overview | Built-in middleware classes


----------


## 模板层

模板层提供用于渲染要呈现给用户的信息的设计者友好语法。了解设计师如何使用这一语法师以及如何通过编程进行扩展：

* **The basics**: Overview
* **For designers**: Language overview | Built-in tags and filters | Humanization
* **For programmers**: Template API | Custom tags and filters


----------


## 表单

Django提供了一个丰富的框架，以帮助创建表格和操作表格数据。

* **The basics**: Overview | Form API | Built-in fields | Built-in widgets
* **Advanced**: Forms for models | Integrating media | Formsets | Customizing validation


----------


## 开发过程

了解各种组件和工具，以助您进行Django应用程序的开发和测试：

* **设置**: [概述](./topics/设置.md) | [Full list of settings](https://docs.djangoproject.com/en/1.9/ref/settings/)
* **应用**: [概述](https://docs.djangoproject.com/en/1.9/ref/applications/)
* **异常**: [概述](https://docs.djangoproject.com/en/1.9/ref/exceptions/)
* **django-admin 和 manage.py**: [概述](https://docs.djangoproject.com/en/1.9/ref/django-admin/) | Adding custom commands
* **测试**: [简介](https://docs.djangoproject.com/en/1.9/topics/testing/) | Writing and running tests | Included testing tools | Advanced topics
* **部署**: [概述](https://docs.djangoproject.com/en/1.9/howto/deployment/) | WSGI servers | Deploying static files | Tracking code errors by email


----------


## admin站点

所有你所需要知道的自动管理接口，Django最流行的功能之一：

* [Admin站点](https://docs.djangoproject.com/en/1.9/ref/contrib/admin/)
* [Admin动作](https://docs.djangoproject.com/en/1.9/ref/contrib/admin/actions/)
* [Admin文档生成器](https://docs.djangoproject.com/en/1.9/ref/contrib/admin/admindocs/)


----------


## 安全

安全性是至关重要的Web应用程序的开发主题，Django提供多种保护手段和机制：

* [安全概述](https://docs.djangoproject.com/en/1.9/topics/security/)
* [Django已披露的安全问题](https://docs.djangoproject.com/en/1.9/releases/security/)
* [点击劫持保护](https://docs.djangoproject.com/en/1.9/ref/clickjacking/)
* [跨站请求伪造保护](https://docs.djangoproject.com/en/1.9/ref/csrf/)
* [加密签名](https://docs.djangoproject.com/en/1.9/topics/signing/)
* [安全中间件](https://docs.djangoproject.com/en/1.9/ref/middleware/#security-middleware)


----------


## 国际化和本地化

Django还提供了一个强大的国际化和本地化的框架，以帮助您的用于多国语言和世界各地区的应用的发展：

* [概述](./topics/i18n/国际化和本地化.md) | [国际化](./topics/i18n/翻译（转换）.md) | [本地化](https://docs.djangoproject.com/en/1.9/topics/i18n/translation/#how-to-create-language-files) | [本地的Web用户界面格式及表单输入](https://docs.djangoproject.com/en/1.9/topics/i18n/formatting/)
* [时区](https://docs.djangoproject.com/en/1.9/topics/i18n/timezones/)


----------


## 性能和优化

有各种各样的技术和工具，可以帮助你的代码的运行更有效率 - 速度更快，并且使用更少的系统资源。

* [性能和优化概述](https://docs.djangoproject.com/en/1.9/topics/performance/)


----------


##Python兼容性

Django的目标是兼容不同Python风味及版本：

* [Jython的支持](https://docs.djangoproject.com/en/1.9/howto/jython/)
* [Python 3 兼容性](https://docs.djangoproject.com/en/1.9/topics/python3/)


----------


## 地理框架

[GeoDjango](https://docs.djangoproject.com/en/1.9/ref/contrib/gis/)意图成为一个世界级的地理Web框架。它的目标是尽可能容易地构建GIS Web应用程序，并加强空间功能数据的能力。


----------


## 常见的Web应用程序的工具

Django还提供了多种通常在Web应用程序开发过程中需要的工具：

* **认证**: Overview | Using the authentication system | Password management | Customizing authentication | API Reference
* [Caching]()
* [Logging]()
* [Sending emails]()
* [Syndication feeds (RSS/Atom)]()
* [Pagination]()
* [Messages framework]()
* [Serialization]()
* [Sessions]()
* [Sitemaps]()
* [Static files management]()
* [Data validation]()


----------


##其他核心功能

了解Django框架的其他一些核心功能：

* [Conditional content processing]()
* [Content types and generic relations]()
* [Flatpages]()
* [Redirects]()
* [Signals]()
* [System check framework]()
* [The sites framework]()
* [Unicode in Django]()


----------


## Django的开源项目

了解了Django项目本身以及您如何参与到开发过程：

* **社区**: How to get involved | The release process | Team organization | Meet the team | Current roles | The Django source code repository | Security policies | Mailing lists
* **设计理念**: Overview
* **文档**: About this documentation
* **第三方发行版本**: Overview
* **Django over time**: API stability | Release notes and upgrading instructions | Deprecation Timeline
