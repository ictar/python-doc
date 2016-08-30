原文：[Handy Python Libraries for Formatting and Cleaning Data](https://blog.modeanalytics.com/python-data-cleaning-libraries/)

---

真实世界是杂乱的，它的数据也是。那么凌乱，[最近的一项调查](http://visit.crowdflower.com/data-science-report.html)显示，数据科学家花费60%的时间在清理数据。不幸的是，他们中57%还觉得这是他们工作中最不愉快的方面。

清理数据可能是耗时的，但是很多工具已经出现，让这个重要的任务惬意一点。Python社区提供了众多库，用来让数据有序清晰，从具有风格的DataFrame到匿名数据集。

如果你发现什么有用的库，请告诉我们，我们一直在寻找更好的库，以添加到[Mode Python Notebooks](https://about.modeanalytics.com/python/)中。

![Scrub that Data](https://blog.modeanalytics.com/images/post-images/python-data-cleaning-libraries.png) 

_太糟糕的清理对数据科学家而言就像对这个小男孩一样，并不好玩。_

## Dora

Dora是为探索性分析而生的；具体来说，是为了自动化它最痛苦的那部分，如特征选择和提取，可视化，以及，是哒，你猜到了，就是数据清理。清理功能包括：

  * 读取缺失和比例值不佳的数据
  * 输入缺失值
  * 缩放输入变量的值

**创建者：** [Nathan Epstein](https://twitter.com/epstein_n)  
**何处了解更多：** <https://github.com/NathanEpstein/Dora>

## datacleaner

令人感到惊奇的datacleaner清理你的数据 —— 但只在它是以[pandas DataFrame](https://community.modeanalytics.com/python/tutorial/pandas-dataframe/)形式出现的时候。据创建者Randy Olson说：“datacleaner并不是魔术，它不会读取文本的无组织块，然后自动的为你解析。”

但是，它会丢弃具有缺失值的行，在逐列基础上用模或者中位数来替换缺失值，并且用数值等效来编码非数值变量。这个库相当的新，但是由于在Python中，DataFrame是分析的基础，因此值得一看。

**创建者：** [Randy Olson](https://twitter.com/randal_olson)  
**何处了解更多：** <https://github.com/rhiever/datacleaner>

## PrettyPandas

DataFrame是强大的，但是它不会生成你想要展示给你老板看的那种表格。PrettyPandas利用[pandas Style API](http://pandas.pydata.org/pandas-docs/stable/style.html)来转换DataFrame成值得展示的表单。创建摘要，添加样式，并格式化数字、列和行。额外的好处：健壮、易读的[文档](http://prettypandas.readthedocs.io/en/latest/)。

**创建者：** [Henry Hammond](https://twitter.com/henryhammond92)  
**何处了解更多：** <https://github.com/HHammond/PrettyPandas>

## tabulate

tabulate让你只用一次函数调用，就可以打印小而美的表格。它让列按照十进制、数字格式和表头等等进行排列，对于让表单更易读，它是非常方便的。

其中一个最酷的功能是，能够以多种格式，例如HTML, PHP或者Markdown Extra，来输出数据，所以你可以继续在另一个工具或者语言中处理你的表单数据。

**创建者：** Sergey Astanin  
**何处了解更多：** <https://pypi.python.org/pypi/tabulate>

## scrubadub

在诸如医疗保健和金融的领域中，数据科学家经常需要匿名数据集。scrubadub从免费文本中移除了[个人身份信息 (PII)](https://en.wikipedia.org/wiki/Personally_identifiable_information)，如：

  * 姓名 (专有名词)
  * 电子邮件地址
  * 网址
  * 电话号码
  * 用户名/密码组合
  * Skype用户名
  * 社保号

该文档在显示你可能想要自定义scrubadub行为的方面（例如定义新的PII类型，或者排除某些PII类型）表现良好。

**创建者：** [Datascope Analytics](http://datascopeanalytics.com/)  
**何处了解更多：** <http://scrubadub.readthedocs.io/en/stable/index.html>

## Arrow

坦白说：在Python中处理日期和时间很痛苦。本地时区不能够被自动识别。要花几行令人不爽的代码来转换时区和时间戳。

Arrow旨在修复这些问题和插件功能上的缺陷，来帮助你用更少的代码和更少的导入来处理时间和日期。不像Python的标准库，Arrow默认意识到时区和UTC。你可以用一行代码来转换时区或者解析字符串。

**创建者：** [Chris Smith](https://twitter.com/crsmithdev)  
**何处了解更多：** <http://arrow.readthedocs.io/en/latest/>

## Beautifier

Beautifier的任务很简单：清理和美化URL和电子邮件地址。你可以通过域名和用户名来解析电子邮件；通过域名和参数（例如，UTM或者令牌）来解析URL。

**创建者：** [Sachin Philip Mathew](https://twitter.com/sachin_philip)  
**何处了解更多：** <https://github.com/sachinvettithanam/beautifier>

## ftfy

ftfy (为你修正文本)接收糟糕的Unicode，输出漂亮的Unicode。基本上，它修复了所欲的垃圾字符。`â€œquotesâ€\x9d`变成`"quotes"`; `uÌˆ`变成`ü`; `&lt;3`变成`<3`。如果每天都在处理文本，那么这个库，正如一个用户所说，是“一个方便的法宝。”

**创建者：** [Luminoso](http://www.luminoso.com/)  
**何处了解更多：** <https://github.com/LuminosoInsight/python-ftfy>

## 管理数据的更多资源

这里是几个我们最喜欢的关于改写/管理/清理数据的文章。

  * [每一个数据科学家都应该知道的关于数据匿名化的事](https://github.com/krasch/presentations/blob/master/pydata_Berlin_2016.pdf) (Katharina Rasch)
  * [在Python中清理数据](https://data.library.utoronto.ca/cleaning-data-python) (University of Toronto Map & Data Library)
  * [用Python进行数据清理 - MoMA的艺术品收藏](https://www.dataquest.io/blog/data-cleaning-with-python/) (Dataquest)