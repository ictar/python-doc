原文：[Testing in Django](https://docs.djangoproject.com/en/1.9/topics/testing/)

---

对于当代的Web开发者来说，自动化测试是一个非常有用的bug专杀工具。你可以使用一个测试集合 —— 一个**测试套件** —— 来解决，或者避免许多问题：

*   当你编写新代码的时候，可以使用测试来验证你的代码如预期一样工作。
*   当你重构或修改旧代码时，可以使用测试来确保你的修改不会意外的影响应用程序的行为。

测试Web应用是一项复杂的任务，因为Web应用程序是由几个逻辑层组成的 - 从HTTP级别的请求处理，到表单验证和处理，再到模板渲染。使用Django的测试执行框架和各种实用工具，你可以模拟请求，插入测试数据，检查你的应用程序的输出，以及一般性验证你的代码确实在什么它应该做的事。

最在，它真的很容易。

在Django中编写测试的首选方法是使用内置在Python标准库中的[`unittest`](https://docs.python.org/3/library/unittest.html#module-unittest "(in Python v3.5)")模块。这在[_编写及运行测试_](https://docs.djangoproject.com/en/1.9/topics/testing/overview/)文档中详细的进行了说明。

你也可以使用任何其他的Python测试框架；Django为这种类型的集成提供了一个API和工具。在[_高级测试主题_](https://docs.djangoproject.com/en/1.9/topics/testing/advanced/)的[使用不同的测试框架](https://docs.djangoproject.com/en/1.9/topics/testing/advanced/#other-testing-frameworks)部分中对其进行了描述。


*   [编写及运行测试](https://docs.djangoproject.com/en/1.9/topics/testing/overview/)
*   [测试工具](https://docs.djangoproject.com/en/1.9/topics/testing/tools/)
*   [高级测试主题](https://docs.djangoproject.com/en/1.9/topics/testing/advanced/)
