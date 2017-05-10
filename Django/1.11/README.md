# Django 文档

原文： [Django documentation](https://docs.djangoproject.com/en/1.11/)

---

你所需要知道的关于Django的一切。

## 该文档如何组织

Django有大量的文档。关于如何组织的高层次概述可以帮助你知道到哪查找你想要的东西：

* [教程](./intro/) take you by the hand through a series of steps to create a Web application. Start here if you’re new to Django or Web application development. Also look at the “First steps” below.
* [主题指导](https://docs.djangoproject.com/en/1.11/topics/) discuss key topics and concepts at a fairly a fairly high level and provide useful background information and explanation.
* [参考指导](https://docs.djangoproject.com/en/1.11/ref/) contain technical reference for APIs and other aspects of Django’s machinery. They describe how it works and how to use it but assume that you have a basic understanding of key concepts.
* [How-to指导](https://docs.djangoproject.com/en/1.11/howto/) are recipes. They guide you through the steps involved in addressing key problems and use-cases. They are more advanced than tutorials and assume some knowledge of how Django works.


----------


## 第一步

你是Django或者编程的新手？这里就是开始的地方！

* **从无到有**: [概述](./intro/overview.md) | [安装](./intro/install.md)
* **教程: 第一部分**:[第1部分：请求和响应](./intro/tutorial01.md) |
[第2部分：模型和admin站点](https://docs.djangoproject.com/en/1.11/intro/tutorial02/) |
[第3部分：视图和模板](https://docs.djangoproject.com/en/1.11/intro/tutorial03/) |
[第4部分：表单和通用视图](https://docs.djangoproject.com/en/1.11/intro/tutorial04/) |
[第5部分：测试](https://docs.djangoproject.com/en/1.11/intro/tutorial05/) |
[第6部分：静态文件](https://docs.djangoproject.com/en/1.11/intro/tutorial06/) |
[第7部分：定制admin站点](https://docs.djangoproject.com/en/1.11/intro/tutorial07/)
* **高级教程**: [如何编写可复用的应用程序](https://docs.djangoproject.com/en/1.11/intro/reusable-apps/) |
[编写第一个Django补丁](https://docs.djangoproject.com/en/1.11/intro/contributing/)

----------


## 模型层

Django提供了一个用于构建以及操作Web应用数据的抽象层（“模型(model)”）。下面学习关于它的更多内容：

* **模型(model)**: [模型入门](./topics/db/模型.md) |
[(field)类型](https://docs.djangoproject.com/en/1.11/ref/models/fields/) |
[Meta选项](https://docs.djangoproject.com/en/1.11/ref/models/options/) |
[Model类](https://docs.djangoproject.com/en/1.11/ref/models/class/)
* **查询集(QuerySet)**: [执行查询](https://docs.djangoproject.com/en/1.11/topics/db/queries/) |
[QuerySet方法参考](https://docs.djangoproject.com/en/1.11/ref/models/querysets/) |
[查询表达式](https://docs.djangoproject.com/en/1.11/ref/models/lookups/)
* **Model实例**: [实例方法](https://docs.djangoproject.com/en/1.11/ref/models/instances/) |
[访问相关对象](https://docs.djangoproject.com/en/1.11/ref/models/relations/)
* **移植**: [移植简介](https://docs.djangoproject.com/en/1.11/topics/migrations/) |
[操作参考](https://docs.djangoproject.com/en/1.11/ref/migration-operations/) |
[SchemaEditor](https://docs.djangoproject.com/en/1.11/ref/schema-editor/) |
[编写移植](https://docs.djangoproject.com/en/1.11/howto/writing-migrations/)
* **高级**: [_Managers_](https://docs.djangoproject.com/en/1.11/topics/db/managers/) |
[原始SQL](https://docs.djangoproject.com/en/1.11/topics/db/sql/) |
[_Transactions_](https://docs.djangoproject.com/en/1.11/topics/db/transactions/) |
[聚合](https://docs.djangoproject.com/en/1.11/topics/db/aggregation/) |
[定制域(field)](https://docs.djangoproject.com/en/1.11/howto/custom-model-fields/) |
[_Multiple databases_](https://docs.djangoproject.com/en/1.11/topics/db/multi-db/) |
[自定义查询](https://docs.djangoproject.com/en/1.11/howto/custom-lookups/) |
[查询表达式](https://docs.djangoproject.com/en/1.11/ref/models/expressions/) |
[条件表达式](https://docs.djangoproject.com/en/1.11/ref/models/conditional-expressions/) |
[数据库函数](https://docs.djangoproject.com/en/1.11/ref/models/database-functions/)
* **其他**: [支持的数据库](https://docs.djangoproject.com/en/1.11/ref/databases/) |
[旧版数据库](https://docs.djangoproject.com/en/1.11/howto/legacy-databases/) |
[提供初始数据](https://docs.djangoproject.com/en/1.11/howto/initial-data/) |
[优化数据库访问](https://docs.djangoproject.com/en/1.11/topics/db/optimization/) |
[PostgreSQL特定功能](https://docs.djangoproject.com/en/1.11/ref/contrib/postgres/)


----------


## 视图层

Django有“视图(view)”的概念，用来封装负责处理用户请求并返回响应的逻辑。通过下面链接，可以找到你所需要知道的关于视图的一切：

* **基础知识**: [_URLconfs_](https://docs.djangoproject.com/en/1.11/topics/http/urls/) |
[视图函数](https://docs.djangoproject.com/en/1.11/topics/http/views/) |
[快捷键](https://docs.djangoproject.com/en/1.11/topics/http/shortcuts/) |
[装饰器](https://docs.djangoproject.com/en/1.11/topics/http/decorators/)
* **参考**: [内置视图](https://docs.djangoproject.com/en/1.11/ref/views/) |
[Request/response对象](https://docs.djangoproject.com/en/1.11/ref/request-response/) |
[TemplateResponse对象](https://docs.djangoproject.com/en/1.11/ref/template-response/)
* **文件上传**: [概述](https://docs.djangoproject.com/en/1.11/topics/http/file-uploads/) |
[File对象](https://docs.djangoproject.com/en/1.11/ref/files/file/) |
[存储API](https://docs.djangoproject.com/en/1.11/ref/files/storage/) |
[管理文件](https://docs.djangoproject.com/en/1.11/topics/files/) |
[自定义存储](https://docs.djangoproject.com/en/1.11/howto/custom-file-storage/)
* **基于类的视图**: [概述](https://docs.djangoproject.com/en/1.11/topics/class-based-views/) |
[内置展示视图](https://docs.djangoproject.com/en/1.11/topics/class-based-views/generic-display/) |
[内置编辑视图](https://docs.djangoproject.com/en/1.11/topics/class-based-views/generic-editing/) |
[_Using mixins_](https://docs.djangoproject.com/en/1.11/topics/class-based-views/mixins/) |
[API参考](https://docs.djangoproject.com/en/1.11/ref/class-based-views/) |
[_Flattened index_](https://docs.djangoproject.com/en/1.11/ref/class-based-views/flattened-index/)
* **高级**: [生成CSV](./howto/使用Django输出CSV.md) |
[生成PDF](./howto/使用Django输出PDF.md)
*   **Middleware:**
[概述](./topics/http/中间件.md) |
[内置中间件类](https://docs.djangoproject.com/en/1.11/ref/middleware/)

----------


## 模板层

模板层提供用于渲染要呈现给用户的信息的设计者友好语法。了解设计师如何使用这一语法师以及如何通过编程进行扩展：

* **基础知识**: [概述](https://docs.djangoproject.com/en/1.11/topics/templates/)
* **对于初学者**: [语言概述](https://docs.djangoproject.com/en/1.11/ref/templates/language/) |
[内置标签和过滤器](https://docs.djangoproject.com/en/1.11/ref/templates/builtins/) |
[人性化](https://docs.djangoproject.com/en/1.11/ref/contrib/humanize/)
* **对于程序员**: [模板API](https://docs.djangoproject.com/en/1.11/ref/templates/api/) |
[定制标签和过滤器](https://docs.djangoproject.com/en/1.11/howto/custom-template-tags/)


----------


## 表单

Django提供了一个丰富的框架，以帮助创建表格和操作表格数据。

* **基础知识**: [概述](./topics/forms/使用表单.md) |
[Form API_](https://docs.djangoproject.com/en/1.11/ref/forms/api/) |
[内置域(field)](https://docs.djangoproject.com/en/1.11/ref/forms/fields/) |
[内置小工具(widget)](https://docs.djangoproject.com/en/1.11/ref/forms/widgets/)
* **高级**: [_Forms for models_](https://docs.djangoproject.com/en/1.11/topics/forms/modelforms/) |
[_Integrating media_](https://docs.djangoproject.com/en/1.11/topics/forms/media/) |
[_Formsets_](https://docs.djangoproject.com/en/1.11/topics/forms/formsets/) |
[自定义验证](https://docs.djangoproject.com/en/1.11/ref/forms/validation/)


----------


## 开发过程

了解各种组件和工具，以助您进行Django应用程序的开发和测试：

* **设置**: [概述](./topics/设置.md) | [设置完整列表](https://docs.djangoproject.com/en/1.11/ref/settings/)
* **应用**: [概述](https://docs.djangoproject.com/en/1.11/ref/applications/)
* **异常**: [概述](https://docs.djangoproject.com/en/1.11/ref/exceptions/)
* **django-admin 和 manage.py**: [概述](https://docs.djangoproject.com/en/1.11/ref/django-admin/) | [_Adding custom commands_](https://docs.djangoproject.com/en/1.11/howto/custom-management-commands/)
* **测试**: [简介](./topics/testing/在Django中测试.md) | [编写和运行测试](./topics/testing/编写和运行测试.md) |
[_Included testing tools_](https://docs.djangoproject.com/en/1.11/topics/testing/tools/) |
[高级主题](https://docs.djangoproject.com/en/1.11/topics/testing/advanced/)
* **部署**: [概述](./howto/部署Django.md) | [_WSGI servers_](./howto/如何使用WSGI进行部署.md) |
[部署静态文件](https://docs.djangoproject.com/en/1.11/howto/static-files/deployment/) |
[通过email跟踪代码错误](https://docs.djangoproject.com/en/1.11/howto/error-reporting/)


----------


## admin站点

所有你所需要知道的自动管理接口，Django最流行的功能之一：

* [Admin站点](https://docs.djangoproject.com/en/1.11/ref/contrib/admin/)
* [Admin动作](https://docs.djangoproject.com/en/1.11/ref/contrib/admin/actions/)
* [Admin文档生成器](https://docs.djangoproject.com/en/1.11/ref/contrib/admin/admindocs/)


----------


## 安全

安全性是至关重要的Web应用程序的开发主题，Django提供多种保护手段和机制：

* [安全概述](https://docs.djangoproject.com/en/1.11/topics/security/)
* [Django已披露的安全问题](https://docs.djangoproject.com/en/1.11/releases/security/)
* [点击劫持保护](https://docs.djangoproject.com/en/1.11/ref/clickjacking/)
* [跨站请求伪造保护](https://docs.djangoproject.com/en/1.11/ref/csrf/)
* [加密签名](https://docs.djangoproject.com/en/1.11/topics/signing/)
* [安全中间件](https://docs.djangoproject.com/en/1.11/ref/middleware/#security-middleware)


----------


## 国际化和本地化

Django还提供了一个强大的国际化和本地化的框架，以帮助您的用于多国语言和世界各地区的应用的发展：

* [概述](./topics/i18n/国际化和本地化.md) | [国际化](./topics/i18n/翻译（转换）.md) | [本地化](./topics/i18n/翻译（转换）.md/#how-to-create-language-files) | [本地的Web用户界面格式及表单输入](https://docs.djangoproject.com/en/1.11/topics/i18n/formatting/)
* [时区](https://docs.djangoproject.com/en/1.11/topics/i18n/timezones/)


----------


## 性能和优化

有各种各样的技术和工具，可以帮助你的代码的运行更有效率 - 速度更快，并且使用更少的系统资源。

* [性能和优化概述](./topics/性能和优化概述.md)


----------


##Python兼容性

Django的目标是兼容不同Python风味及版本：

* [Jython的支持](https://docs.djangoproject.com/en/1.11/howto/jython/)
* [Python 3 兼容性](https://docs.djangoproject.com/en/1.11/topics/python3/)


----------


## 地理框架

[GeoDjango](https://docs.djangoproject.com/en/1.11/ref/contrib/gis/)意图成为一个世界级的地理Web框架。它的目标是尽可能容易地构建GIS Web应用程序，并加强空间功能数据的能力。


----------


## 常见的Web应用程序的工具

Django还提供了多种通常在Web应用程序开发过程中需要的工具：

* **认证**: [概述](https://docs.djangoproject.com/en/1.11/topics/auth/) |
[使用认证系统](https://docs.djangoproject.com/en/1.11/topics/auth/default/) |
[密码管理](https://docs.djangoproject.com/en/1.11/topics/auth/passwords/) |
[自定义验证](https://docs.djangoproject.com/en/1.11/topics/auth/customizing/) |
[API参考](https://docs.djangoproject.com/en/1.11/ref/contrib/auth/)
*   [缓存](https://docs.djangoproject.com/en/1.11/topics/cache/)
*   [日志](./topics/logging/) 0%
*   [发送电子邮件](https://docs.djangoproject.com/en/1.11/topics/email/)
*   [联合订阅(RSS/Atom)](https://docs.djangoproject.com/en/1.11/ref/contrib/syndication/)
*   [分页](https://docs.djangoproject.com/en/1.11/topics/pagination/)
*   [消息框架](https://docs.djangoproject.com/en/1.11/ref/contrib/messages/)
*   [序列化](https://docs.djangoproject.com/en/1.11/topics/serialization/)
*   [_Sessions_](https://docs.djangoproject.com/en/1.11/topics/http/sessions/)
*   [站点地图](https://docs.djangoproject.com/en/1.11/ref/contrib/sitemaps/)
*   [静态文件管理](https://docs.djangoproject.com/en/1.11/ref/contrib/staticfiles/)
*   [数据验证](https://docs.djangoproject.com/en/1.11/ref/validators/)


----------


##其他核心功能

了解Django框架的其他一些核心功能：

*   [有条件的内容处理](https://docs.djangoproject.com/en/1.11/topics/conditional-view-processing/)
*   [_Content types and generic relations_](https://docs.djangoproject.com/en/1.11/ref/contrib/contenttypes/)
*   [_Flatpages_](https://docs.djangoproject.com/en/1.11/ref/contrib/flatpages/)
*   [重定向](https://docs.djangoproject.com/en/1.11/ref/contrib/redirects/)
*   [信号](https://docs.djangoproject.com/en/1.11/topics/signals/)
*   [_System check framework_](https://docs.djangoproject.com/en/1.11/topics/checks/)
*   [站点框架](https://docs.djangoproject.com/en/1.11/ref/contrib/sites/)
*   [Django中的Unicode](https://docs.djangoproject.com/en/1.11/ref/unicode/)


----------


## Django的开源项目

了解了Django项目本身以及您如何参与到开发过程：

* **社区**: [如何参与](internals/contributing/) |
[发布过程](internals/release-process/) |
[团队组织](internals/organization/) |
[认识我们的团队](internals/team/) |
[当前绝色](internals/roles/) |
[Django源代码库](internals/git/) |
[安全策略](internals/security/) |
[邮件列表](internals/mailing-lists/)
* **设计理念**: [概述](misc/design-philosophies/)
* **文档**: [关于此文档](internals/contributing/writing-documentation/)
* **第三方发行版本**: [概述](misc/distributions/)
* **Django over time**: [API的稳定性](misc/api-stability/) |
[发行说明和升级说明](releases/) |
[弃用时间表](internals/deprecation/)
