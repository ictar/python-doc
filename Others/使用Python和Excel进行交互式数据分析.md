原文：[Interactive Data Analysis with Python and Excel](http://pbpython.com/xlwings-pandas-excel.html)

---

      ![article header image](http://pbpython.com/images/article-overview.png)

## 简介

我已经写了[好几](http://pbpython.com/improve-pandas-excel-output.html) [次](http://pbpython.com/advanced-excel-workbooks.html)关于[pandas](http://pandas.pydata.org/)作为一个数据操纵/扯皮工具，以及它是如何能够有效地从Excel读取或写入数据，是多么的好用。但是，在有些你需要为数据分析提供一个交互式环境的情况下，试图在纯Python中，以一种用户友好的方式将它们拖到一起，将是困难的。这篇文章将会讨论如何使用[xlwings](http://xlwings.org/)来将Excel, Python和pandas绑定在一起，以构建一个数据分析工具，这个工具从一个外部的数据库中提取信息，操纵它，并且以一种熟悉的电子表格格式将其呈现给用户。


## 一个快速的Excel自动化简介

Excel使用VBA支持一些自动化选项。用户定义函数(UDF，User Defined Functions)是相当简单的，它们接收输入，然后返回一个简单的值。更强大的选择是一个宏（或过程），它们可以自动完成几乎任何Excel可以做到的事。

尽管UDF和宏是强大的，但是它们仍然是用VBA写的，而有时候，将Python的力量带给我们基于Excel的解决方法会非常有用。这就是xlwings的用武之地。最简单地说，xlwings允许我们以两种主要方式粘合python和Excel：

*   在Python中控制Excel
*   在Excel中调用自定义的Python代码

本文将重点构建一个调用你自定义的Python代码的Excel工作表。


## 问题是……

在这个例子中，我们将开发一个简单的建模应用，它将允许某人输入一个账号以及日期范围，然后返回一些已经通过pandas转化的总结的销售信息。解决方法是简单的，但它显示了这种结合的威力，以及执行一些更复杂的数据分析有多么简单。

这里是一个我们正在试图做的事情的图表：

![data flow](http://pbpython.com/images/python-data-flow.png)

下面的例子可以很容易地扩展到查询多个数据库，或者与任何Python可以读取的文件(CSV, Excel, json等等)进行交互。


## 安装环境

为了达到这篇文章的目的，我将假设你在一个基于Windows的系统上运行该应用。我强烈建议你使用[anaconda](https://www.continuum.io/downloads) (或者[miniconda](http://conda.pydata.org/miniconda.html))作为你选择的发行版本。

我们需要做的第一件事是安装xlwings（假设python+pandas已经安装好了）：
```sh
conda install xlwings
```

>版本警告
>xlwings在不断的更新中。这个代码时基于0.7.1版本的。

有一个漂亮的名为`quickstart`的xlwings帮助函数，它会为你创建一个Excel文件样例和Python文件桩。
```sh
c:\>xlwings quickstart pbp_proj
```

如果你看一看新创建的pbp_proj目录，那么你将看到两个文件：
```
pbp_proj.py
pbp_proj.xlsm
```

其中，Python文件是空的，而Excel看上去也是空的，但是，幕后已经完成了一些工作，以使得excel到Python接口更加容易。

要看看Excel文件中有啥，请在Excel中打开你新创建的文件，然后选择Developer -> Visual Basic，然后你应该可以看到一些像这样的东东：

![vba setup](http://pbpython.com/images/excel-vba-setup.png)

你会发现有两个模块 - `xlwings`和`Module1`。xlwings模块包含所有的VBA代码，从而使得你自定义的代码可用。在大多数情况下，你应该不要管它。然而，如果你的配置有问题(例如无法找到Python)，那么你可以在这里更新配置信息。

![config](http://pbpython.com/images/excel-config.png)

`Module1`将有一些默认代码，它们看起来是这样的：

![sample module](http://pbpython.com/images/excel-sample-call.png)

一会，我们将修改它来调用我们自定义的代码。首先，我想要创建Excel输入文件。

对于这个应用，我们将允许用户输入一个账号，开始日期和结束日期，然后将基于这些输入操纵销售日期。

下面是简单的电子表格：

![simple spreadsheet](http://pbpython.com/images/excel-base.png)

我只是做了一些小的格式修改，这些格子中并没有公式。请务必将修改保存到Excel文件中。

下一步，我将创建一个简短的Python函数，它说明了如何从Excel中读取数据，然后将数据写回到Excel中。我将保存它到一个名为`pbp_proj.py`的空文件中。
```py
import pandas as pd
from xlwings import Workbook, Range


def summarize_sales():
    """
    Retrieve the account number and date ranges from the Excel sheet
    """
    # Make a connection to the calling Excel file
    wb = Workbook.caller()

    # Retrieve the account number and dates
    account = Range('B2').value
    start_date = Range('D2').value
    end_date = Range('F2').value

    # Output the data just to make sure it all works
    Range('A5').value = account
    Range('A6').value = start_date
    Range('A7').value = end_date
```

该程序是简单的，但现在还没啥用。我想，为了确保所有的“管道”各就各位，开发一个框架程序更容易些。要记住的关键一点是，这个文件名为`pbp_proj.py`，而函数名为`summarize_sales`。

要将这一切写在一起，我们需要定义一个Excel过程来运行我们的代码：

![simple spreadsheet](http://pbpython.com/images/excel-run-code.png)

代码时相当简洁的，它只是导入该模块，然后执行函数：
```
Sub RetrieveSales()
    RunPython ("import pbp_proj;pbp_proj.summarize_sales()")
End Sub
```

最后一块是添加一个按钮到我们的表单中，然后将其分配给程序/宏`RetrieveSales`。

![assign the macro](http://pbpython.com/images/assign-macro.png)

一旦你有了这个，你应该可以按下按钮，然后看到像这样的东东：

![simple example](http://pbpython.com/images/simple-demo.png)

基本过程已经有了，我们可以从Excel读取到Python程序中，然后使用读取的数据来输出数据到Excel。现在，让我们使其更有用些。


## 从数据库中读取数据

在这个例子中，我将使用[sqlalchemy](http://www.sqlalchemy.org/)来查询一个小的sqlite db，然后直接将读取查询到一个pandas dataframe中。这种方法的好处是，如果你决定要查询另一个数据库，那么你可以只是修改slqlalchemy引擎，然后保持你的代码的剩余部分不变。作为参考，xlwings网站给出了另一个[例子](http://xlwings.org/examples/)，作为进一步的参考，这个例子应该很有帮助。

在处理代码之前，确保安装了sqlalchemy：
```py
conda install sqlalchemy
```

下面是如何通过使用到数据库的完整路径来连接到该sqlite引擎：
```py
from sqlalchemy import create_engine

# Connect to sqlite db
db_file = os.path.join(os.path.dirname(wb.fullname), 'pbp_proj.db')
engine = create_engine(r"sqlite:///{}".format(db_file))
```

现在，我们有引擎了，我们可以构建并执行查询，然后将结果读取到一个dataframe中：
```py
# Create SQL query
sql = 'SELECT * from sales WHERE account="{}" AND date BETWEEN "{}" AND "{}"'.format(account, start_date, end_date)

# Read query directly into a dataframe
sales_data = pd.read_sql(sql, engine)
```

一旦我们将数据保存在`sales_data` dataframe中，我们可以做任何我们想做的事情。为了简便起见，我会做一个简单的`groupby`，然后是总支出的`sum`：
```py
# Analyze the data however we want
summary = sales_data.groupby(["sku"])["quantity", "ext-price"].sum()
total_sales = sales_data["ext-price"].sum()
```

幸运的是，xlwings“理解”pandas dataframe，因此将值返回到Excel工作表中很简单：
```py
Range('A5').value = summary
Range('E5').value = "Total Sales"
Range('F5').value = total_sales
```

这样就完成数据Excel -> Python -> Excel的往返。

### 完整的程序

这里是包含在`pbp_proj.py`中的全功能代码：
```py
import pandas as pd
from sqlalchemy import create_engine
from xlwings import Workbook, Range
import os


def summarize_sales():
    """
    Retrieve the account number and date ranges from the Excel sheet
    Read in the data from the sqlite database, then manipulate and return it to excel
    """
    # Make a connection to the calling Excel file
    wb = Workbook.caller()

    # Connect to sqlite db
    db_file = os.path.join(os.path.dirname(wb.fullname), 'pbp_proj.db')
    engine = create_engine(r"sqlite:///{}".format(db_file))

    # Retrieve the account number from the excel sheet as an int
    account = Range('B2').options(numbers=int).value

    # Get our dates - in real life would need to do some error checking to ensure
    # the correct format
    start_date = Range('D2').value
    end_date = Range('F2').value

    # Clear existing data
    Range('A5:F100').clear_contents()

    # Create SQL query
    sql = 'SELECT * from sales WHERE account="{}" AND date BETWEEN "{}" AND "{}"'.format(account, start_date, end_date)

    # Read query directly into a dataframe
    sales_data = pd.read_sql(sql, engine)

    # Analyze the data however we want
    summary = sales_data.groupby(["sku"])["quantity", "ext-price"].sum()

    total_sales = sales_data["ext-price"].sum()

    # Output the results
    if summary.empty:
        Range('A5').value = "No Data for account {}".format(account)
    else:
        Range('A5').options(index=True).value = summary
        Range('E5').value = "Total Sales"
        Range('F5').value = total_sales
```

这里是结果样例：

![full example](http://pbpython.com/images/final-result.png)

All of the data, including the sqlite db is in my github 所有的数据，包括sqlite db，都在我的github [repo](https://github.com/chris1610/pbpython/tree/master/code/pbp_proj)中


### 总结

xlwings提供了从Python与Excel无缝交互的一个有用的功能。通过使用此代码，你可以为你自己或那些从多个源获取数据，并且在非常熟悉的Excel环境中分析数据的非技术用户，轻松构建交互式工具。一旦建立起了结构，那么将所有复杂的逻辑和数据分析放到Python文件中，并利用所有在Python生态系统中提供的工具，这将是非常有用的。我希望，一旦你开始玩这个，那么你会发现有很多机会来使用这个方法将Python解决方法带给你的一些陷入使用Excel作为他们唯一的数据分析工具的非技术用户。
