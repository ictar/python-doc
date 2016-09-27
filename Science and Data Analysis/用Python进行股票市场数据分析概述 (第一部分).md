原文：[An Introduction to Stock Market Data Analysis with Python (Part 1)](https://ntguardian.wordpress.com/2016/09/19/introduction-stock-market-data-python-1/)

---

_T这篇文章是使用Python进行股票数据分析系列的两部分中的第一个部分，基于我在Utah大学为MATH 3900（数据科学）课题提供的一个讲座。在这些文章中，我会讨论到基础知识，例如使用pandas从Yahoo! Finance获取数据，可视化股票数据，移动均值，制定一个移动平均交叉策略，回测和基准。最后的一篇文章会包含实际问题。这第一篇文章讨论的主题到介绍移动均值。_

**_注：这篇文章中的信息普遍包含来自作者角度的信息和观点。这篇文章的任何内容都不应该被视为金融建议。此外，这里写的任何代码没有任何形式的保证。因此，选择使用的人请自担风险。_**

## 概述

高等数学和统计目前已存在金融行业一段时间了。在20世纪80年代之前，银行和金融以“无聊”为众所知；投资银行有别于商业银行，而该行业的主要作用是处理“简单的”（至少相较于今天）金融工具，例如贷款。里根政府下的放松管制，加上数学人才的涌入，将该行业从银行“无聊”的业务转型为它今天这种情况，从那时起，金融作为数学研究和进步的动力加入到其他科学中。例如，最近数学最大的成就之一是[Black-Scholes公式](https://en.wikipedia.org/wiki/Black%E2%80%93Scholes_model)的推导，它有利于股票期权（一种给予持有人权利购买或以特定价格出售给股票发行人的合同）的定价。也就是说，[糟糕的统计模型，包括Black-Scholes公式，要为2008年金融危机负部分责任](https://www.theguardian.com/science/2012/feb/12/black-scholes-equation-credit-crunch)。

近年来，计算机科学已经在彻底改革的金融和**交易**（以谋取利润为目的的金融资产的购买和抛售实践）方面，加入到了高等数学中。在最近几年中，计算机已经主导了交易；算法负责比人类更快地做出瞬间交易决定 (如此快速，以至于[当设计系统的时候，光传播速度成为了限制](http://www.nature.com/news/physics-in-finance-trading-at-the-speed-of-light-1.16872))。此外，在金融领域，[机器学习和数据挖掘技术日益普及](http://www.ft.com/cms/s/0/9278d1b6-1e02-11e6-b286-cddde55ca122.html#axzz4G8daZxcl)，并且可能还会继续这样。事实上，算法交易有一种称呼：**高频交易(high-frequency trading，HFT)**。虽然算法可能超越人类，但是这项技术仍然是崭新的，并且在著名的动荡且高风险的竞技场中使用。HFT要为诸如[2010年闪电崩盘](https://en.wikipedia.org/wiki/2010_Flash_Crash)和由一条关于对白宫的攻击的被黑的[美联社微博](http://money.cnn.com/2013/04/23/technology/security/ap-twitter-hacked/index.html?iid=EL)引发的[2013年闪电崩盘](http://money.cnn.com/2013/04/24/investing/twitter-flash-crash/)这样的现象负责。

然而，本次讲座并不是关于如何使用糟糕的数学模型或者交易算法来让股市崩盘。相反，我打算为你使用Python提供处理和分析股市数据的基本工具。我还会讨论到移动平均线，如何使用移动平均线构建交易策略，如何根据进入位置制定退出策略，以及如何使用回溯测试评估一个策略。

**免责声明：这并不是金融建议！！！此外，作为证券交易人，我是零经验的（有很多这方面的知识来自于我在Salt Lake社区大学参加的一个关于股票交易的一学期课程）！这纯粹是入门知识，不足以让你以股票交易为生。人们可能进行赔本的股票交易，你需要自担风险！**

## 获取和可视化股票数据

### 使用pandas获取来自于Yahoo! Finance的数据

在我们开始玩股票数据之前，需要以某种可行的格式获得它们。股票数据可以从[Yahoo! Finance](http://finance.yahoo.com)，[Google Finance](http://finance.google.com)，或者许多其他来源获得，而**pandas**包提供了到Yahoo! Finance和Google Finance data，以及其他源的简单访问。在这次讲座中，我们将从Yahoo! Finance获取我们的数据。

下面的代码演示了如何直接创建一个包含股票信息的`DataFrame`对象。 ([在这里](http://pandas.pydata.org/pandas-docs/stable/remote_data.html)，你可以读到更多关于远程数据访问的信息)

```python

    import pandas as pd
    import pandas.io.data as web   # Package and modules for importing data; this code may change depending on pandas version
    import datetime
    
    # We will look at stock prices over the past year, starting at January 1, 2016
    start = datetime.datetime(2016,1,1)
    end = datetime.date.today()
    
    # Let's get Apple stock data; Apple's ticker symbol is AAPL
    # First argument is the series we want, second is the source ("yahoo" for Yahoo! Finance), third is the start date, fourth is the end date
    apple = web.DataReader("AAPL", "yahoo", start, end)
    
    type(apple)
    
```

```

    C:\Anaconda3\lib\site-packages\pandas\io\data.py:35: FutureWarning: 
    The pandas.io.data module is moved to a separate package (pandas-datareader) and will be removed from pandas in a future version.
    After installing the pandas-datareader package (https://github.com/pydata/pandas-datareader), you can change the import ``from pandas.io import data, wb`` to ``from pandas_datareader import data, wb``.
      FutureWarning)
    
    
    
    
    
    pandas.core.frame.DataFrame
    
```

```python

    apple.head()
    
```

| Open | High | Low | Close | Volume | Adj Close  
---|---|---|---|---|---|---  
Date |  |  |  |  |  |  
2016-01-04 | 102.610001 | 105.370003 | 102.000000 | 105.349998 | 67649400 |
103.586180  
2016-01-05 | 105.750000 | 105.849998 | 102.410004 | 102.709999 | 55791000 |
100.990380  
2016-01-06 | 100.559998 | 102.370003 | 99.870003 | 100.699997 | 68457400 |
99.014030  
2016-01-07 | 98.680000 | 100.129997 | 96.430000 | 96.449997 | 81094400 |
94.835186  
2016-01-08 | 98.550003 | 99.110001 | 96.760002 | 96.959999 | 70798000 |
95.336649  
  
让我们简单讨论下。**Open**是股票在交易日开始的价格 (不一定是前一个交易日的收盘价)，**high**是在该交易日的最高股票价格，**low**是在该交易日的最低股票价格，而**close**是在闭市时的股票价格。**Volume**表示交易了多少股票。**Adjusted close**是为公司行为调整股票价格的股票收盘价。虽然股票价格被认为大多数是由交易员设定的，但是**股票分割** (当该公司让每个现存股票价值两个半价格时) and **股息** (每股公司的利润派息)也影响了股票的价格，因此也应该考虑。

### 可视化股票数据

现在，我们有了股票价格，相对其进行可视化。首先，我演示了如何使用**matplotlib**包来做到这点。注意，`apple` `DataFrame`对象有一个方便的方法，`plot()`，它让创建图更容易。

```python

    import matplotlib.pyplot as plt   # Import matplotlib
    # This line is necessary for the plot to appear in a Jupyter notebook
    %matplotlib inline
    # Control the default size of figures in this Jupyter notebook
    %pylab inline
    pylab.rcParams['figure.figsize'] = (15, 9)   # Change the size of plots
    
    apple["Adj Close"].plot(grid = True) # Plot the adjusted closing price of AAPL
    
```

```
    Populating the interactive namespace from numpy and matplotlib
    
```

![Alt](https://ntguardian.files.wordpress.com/2016/09/output_4_2.png?w=680)

折线图可以，但对于每个数据，至少涉及四个变量(open, high, low, 和close)，而我们希望有一些直观的方法a可以看看所有这四个变量，而无需绘制四条单独的线。金融数据通常用**日本阴阳烛图**来绘制，如此命名是因为它最早是由18世纪的日本米商所创。这样的图可以用**matplotlib**来创建，尽管此过程中需要相当大的努力。

我已经写了一个函数，欢迎你使用它来更容易地从**pandas**数据帧创建阴阳烛图，并且用它来绘制我们的股票数据。(代码是基于[这个例子](http://matplotlib.org/examples/pylab_examples/finance_demo.html)写的，你可以[在这里](http://matplotlib.org/api/finance_api.html)读到涉及的函数文档。)

```python

    from matplotlib.dates import DateFormatter, WeekdayLocator,\
        DayLocator, MONDAY
    from matplotlib.finance import candlestick_ohlc
    
    def pandas_candlestick_ohlc(dat, stick = "day", otherseries = None):
        """
        :param dat: pandas DataFrame object with datetime64 index, and float columns "Open", "High", "Low", and "Close", likely created via DataReader from "yahoo"
        :param stick: A string or number indicating the period of time covered by a single candlestick. Valid string inputs include "day", "week", "month", and "year", ("day" default), and any numeric input indicates the number of trading days included in a period
        :param otherseries: An iterable that will be coerced into a list, containing the columns of dat that hold other series to be plotted as lines
    
        This will show a Japanese candlestick plot for stock data stored in dat, also plotting other series if passed.
        """
        mondays = WeekdayLocator(MONDAY)        # major ticks on the mondays
        alldays = DayLocator()              # minor ticks on the days
        dayFormatter = DateFormatter('%d')      # e.g., 12
    
        # Create a new DataFrame which includes OHLC data for each period specified by stick input
        transdat = dat.loc[:,["Open", "High", "Low", "Close"]]
        if (type(stick) == str):
            if stick == "day":
                plotdat = transdat
                stick = 1 # Used for plotting
            elif stick in ["week", "month", "year"]:
                if stick == "week":
                    transdat["week"] = pd.to_datetime(transdat.index).map(lambda x: x.isocalendar()[1]) # Identify weeks
                elif stick == "month":
                    transdat["month"] = pd.to_datetime(transdat.index).map(lambda x: x.month) # Identify months
                transdat["year"] = pd.to_datetime(transdat.index).map(lambda x: x.isocalendar()[0]) # Identify years
                grouped = transdat.groupby(list(set(["year",stick]))) # Group by year and other appropriate variable
                plotdat = pd.DataFrame({"Open": [], "High": [], "Low": [], "Close": []}) # Create empty data frame containing what will be plotted
                for name, group in grouped:
                    plotdat = plotdat.append(pd.DataFrame({"Open": group.iloc[0,0],
                                                "High": max(group.High),
                                                "Low": min(group.Low),
                                                "Close": group.iloc[-1,3]},
                                               index = [group.index[0]]))
                if stick == "week": stick = 5
                elif stick == "month": stick = 30
                elif stick == "year": stick = 365
    
        elif (type(stick) == int and stick >= 1):
            transdat["stick"] = [np.floor(i / stick) for i in range(len(transdat.index))]
            grouped = transdat.groupby("stick")
            plotdat = pd.DataFrame({"Open": [], "High": [], "Low": [], "Close": []}) # Create empty data frame containing what will be plotted
            for name, group in grouped:
                plotdat = plotdat.append(pd.DataFrame({"Open": group.iloc[0,0],
                                            "High": max(group.High),
                                            "Low": min(group.Low),
                                            "Close": group.iloc[-1,3]},
                                           index = [group.index[0]]))
    
        else:
            raise ValueError('Valid inputs to argument "stick" include the strings "day", "week", "month", "year", or a positive integer')
    
    
        # Set plot parameters, including the axis object ax used for plotting
        fig, ax = plt.subplots()
        fig.subplots_adjust(bottom=0.2)
        if plotdat.index[-1] - plotdat.index[0] < pd.Timedelta('730 days'):
            weekFormatter = DateFormatter('%b %d')  # e.g., Jan 12
            ax.xaxis.set_major_locator(mondays)
            ax.xaxis.set_minor_locator(alldays)
        else:
            weekFormatter = DateFormatter('%b %d, %Y')
        ax.xaxis.set_major_formatter(weekFormatter)
    
        ax.grid(True)
    
        # Create the candelstick chart
        candlestick_ohlc(ax, list(zip(list(date2num(plotdat.index.tolist())), plotdat["Open"].tolist(), plotdat["High"].tolist(),
                          plotdat["Low"].tolist(), plotdat["Close"].tolist())),
                          colorup = "black", colordown = "red", width = stick * .4)
    
        # Plot other series (such as moving averages) as lines
        if otherseries != None:
            if type(otherseries) != list:
                otherseries = [otherseries]
            dat.loc[:,otherseries].plot(ax = ax, lw = 1.3, grid = True)
    
        ax.xaxis_date()
        ax.autoscale_view()
        plt.setp(plt.gca().get_xticklabels(), rotation=45, horizontalalignment='right')
    
        plt.show()
    
    pandas_candlestick_ohlc(apple)
    
```

![png](https://ntguardian.files.wordpress.com/2016/09/output_6_0.png?w=680)

在阴阳烛图中，阴烛表示收盘价高于开盘价（增益）的一天，而阳烛表示开盘价高于收盘价（亏损）。灯芯表示高和低，而主体表示开盘价和收盘价(使用色调来确定哪一个主体一端是开盘价，哪个是收盘价)。阴阳烛图在金融界是流行的，而[技术分析](https://en.wikipedia.org/wiki/Technical_analysis)的一些策略使用它们，根据蜡烛的形状、颜色和位置来做出交易决定。今天，我不会涵盖这样的策略。

我们不妨将多个金融工具绘制在一起；我们可能要比较股票，比较它们的市场，或者看看其他证券，例如[交易所交易基金(ETFs)](https://en.wikipedia.org/wiki/Exchange-traded_fund)。稍后，我们还将想要看看如何对一些指标，例如移动平均线，来绘制金融工具。为此，宁可使用折线图，而不是阴阳烛图。(你要如何绘制多个阴阳烛图而不弄乱图表呢？)

下面，我获得了一些其他高科技公司的股票数据，并把它们紧密地绘制在了一起。

```python

    microsoft = web.DataReader("MSFT", "yahoo", start, end)
    google = web.DataReader("GOOG", "yahoo", start, end)
    
    # Below I create a DataFrame consisting of the adjusted closing price of these stocks, first by making a list of these objects and using the join method
    stocks = pd.DataFrame({"AAPL": apple["Adj Close"],
                          "MSFT": microsoft["Adj Close"],
                          "GOOG": google["Adj Close"]})
    
    stocks.head()
    
```

| AAPL | GOOG | MSFT  
---|---|---|---  
Date |  |  |  
2016-01-04 | 103.586180 | 741.840027 | 53.696756  
2016-01-05 | 100.990380 | 742.580017 | 53.941723  
2016-01-06 | 99.014030 | 743.619995 | 52.961855  
2016-01-07 | 94.835186 | 726.390015 | 51.119702  
2016-01-08 | 95.336649 | 714.469971 | 51.276485

```python

    stocks.plot(grid = True)
    
```  
  
![png](https://ntguardian.files.wordpress.com/2016/09/output_9_1.png?w=680)

这张图表有什么不对？虽然绝对价格是重要的（价格昂贵的股票难以购买，这不仅影响它们的波动性，还影响_你_交易该股票的能力），在交易的时候，我们更关注资产的相对变化，而不是它们的绝对价格。Google的股票比Apple或者Microsoft的要贵得多，而这种差异让Apple和Microsoft的股票比它们实际上的波动要小得多。

一种解决方案是在绘制数据的时候使用两种不同的尺度；一种尺度用于Apple和Microsoft股票，另一种用于Google。

```python

    stocks.plot(secondary_y = ["AAPL", "MSFT"], grid = True)
    
```

![png](https://ntguardian.files.wordpress.com/2016/09/output_11_1.png?w=680)

不过，可以用一种“更好”的解决方案来绘制我们真正想要的信息：股票的收益。这涉及到将数据转换成对我们的目的更有用的东西。我们可以应用多种变换。

一种变换是考虑股票自所关注时期开始的收益。换句话说，我们绘制：

![\\text{return}_{t,0} = \\frac{\\text{price}_t}{\\text{price}_0} ](https://s0.wp.com/latex.php?latex=%5Ctext%7Breturn%7D_%7Bt%2C0%7D+%3D+%5Cfrac%7B%5Ctext%7Bprice%7D_t%7D%7B%5Ctext%7Bprice%7D_0%7D+&bg=ffffff&fg=444444&s=0)

这将需要转换`stocks`对象中的数据，这是我接下来要做的。

```python

    # df.apply(arg) will apply the function arg to each column in df, and return a DataFrame with the result
    # Recall that lambda x is an anonymous function accepting parameter x; in this case, x will be a pandas Series object
    stock_return = stocks.apply(lambda x: x / x[0])
    stock_return.head()
    
```

| AAPL | GOOG | MSFT  
---|---|---|---  
Date |  |  |  
2016-01-04 | 1.000000 | 1.000000 | 1.000000  
2016-01-05 | 0.974941 | 1.000998 | 1.004562  
2016-01-06 | 0.955861 | 1.002399 | 0.986314  
2016-01-07 | 0.915520 | 0.979173 | 0.952007  
2016-01-08 | 0.920361 | 0.963105 | 0.954927

```python

    stock_return.plot(grid = True).axhline(y = 1, color = "black", lw = 2)
    
```  
  
![png](https://ntguardian.files.wordpress.com/2016/09/output_14_1.png?w=680)

这是一张有用得多的图表。现在，我们可以看到自这一时期开始，每只股票的盈利能力。此外，我们看到，这些股票是高度相关的；它们一般在同个方向移动，这是在其他图表中很难看到的事实。

另外，我们可以绘制出每个股票每天的变化。这样做的一个方法是使用下面的公式来比较第t天和第t+1，绘制股票增加百分比：

![\\text{growth}_t = \\frac{\\text{price}_{t + 1} -
\\text{price}_t}{\\text{price}_t} ](https://s0.wp.com/latex.php?latex=%5Ctext%7Bgrowth%7D_t+%3D+%5Cfrac%7B%5Ctext%7Bprice%7D_%7Bt+%2B+1%7D+-+%5Ctext%7Bprice%7D_t%7D%7B%5Ctext%7Bprice%7D_t%7D+&bg=ffffff&fg=444444&s=0)

但可以认为变化是不同的：

![\\text{increase}_t = \\frac{\\text{price}_{t} -
\\text{price}_{t-1}}{\\text{price}_t} ](https://s0.wp.com/latex.php?latex=%5Ctext%7Bincrease%7D_t+%3D+%5Cfrac%7B%5Ctext%7Bprice%7D_%7Bt%7D+-+%5Ctext%7Bprice%7D_%7Bt-1%7D%7D%7B%5Ctext%7Bprice%7D_t%7D+&bg=ffffff&fg=444444&s=0)

这些公式并不一样，并可能导致不同的结论，但还有另一种方式来描述股票的增长模式：使用对数差。

![\\text{change}_t = \\log\(\\text{price}_{t}\) - \\log\(\\text{price}_{t -1}\) ](https://s0.wp.com/latex.php?latex=%5Ctext%7Bchange%7D_t+%3D+%5Clog%28%5Ctext%7Bprice%7D_%7Bt%7D%29+-+%5Clog%28%5Ctext%7Bprice%7D_%7Bt+-+1%7D%29+&bg=ffffff&fg=444444&s=0)

(这里，![\\log](https://s0.wp.com/latex.php?latex=%5Clog&bg=ffffff&fg=444444&s=0)是自然对数，而我们的定义不是强依赖于我们是否使用![\\log\(\\text{price}_{t}\) - \\log\(\\text{price}_{t - 1}\)](https://s0.wp.com/latex.php?latex=%5Clog%28%5Ctext%7Bprice%7D_%7Bt%7D%29+-+%5Clog%28%5Ctext%7Bprice%7D_%7Bt+-+1%7D%29&bg=ffffff&fg=444444&s=0)或者![\\log\(\\text{price}_{t+1}\) - \\log\(\\text{price}_{t}\)](https://s0.wp.com/latex.php?latex=%5Clog%28%5Ctext%7Bprice%7D_%7Bt%2B1%7D%29+-+%5Clog%28%5Ctext%7Bprice%7D_%7Bt%7D%29&bg=ffffff&fg=444444&s=0)。)使用对数的优势是这种差可以解释为股票的百分比变化，但不依赖于分数的分母。

我们可以获得并绘制`stocks`中数据的对数差，如下所示：

```python

    # Let's use NumPy's log function, though math's log function would work just as well
    import numpy as np
    
    stock_change = stocks.apply(lambda x: np.log(x) - np.log(x.shift(1))) # shift moves dates back by 1.
    stock_change.head()
    
```

| AAPL | GOOG | MSFT  
---|---|---|---  
Date |  |  |  
2016-01-04 | NaN | NaN | NaN  
2016-01-05 | -0.025379 | 0.000997 | 0.004552  
2016-01-06 | -0.019764 | 0.001400 | -0.018332  
2016-01-07 | -0.043121 | -0.023443 | -0.035402  
2016-01-08 | 0.005274 | -0.016546 | 0.003062

```python

    stock_change.plot(grid = True).axhline(y = 0, color = "black", lw = 2)
    
```  
  
![png](https://ntguardian.files.wordpress.com/2016/09/output_17_1.png?w=680)

你更喜欢哪种转换？由于时期开始是的问题中证券的总体趋势更为明显，因此看看收益。不过，天之间的变化是更先进的方法在模型化股票行为的时候实际考虑的问题。因此，不应该忽略它们。

## 移动平均线

图表是非常有用的。事实上，有些交易员的策略几乎完全基于图表 (这些是“技术员”，因为基于查找图表中的模式的交易策略是贸易学说的一部分，被称为**技术分析**)。现在，让我们考虑可以如何找到股票中的趋势。

一条**![q](https://s0.wp.com/latex.php?latex=q&bg=ffffff&fg=444444&s=0)-日均线**是对于序列![x_t](https://s0.wp.com/latex.php?latex=x_t&bg=ffffff&fg=444444&s=0)和时间点![t](https://s0.wp.com/latex.php?latex=t&bg=ffffff&fg=444444&s=0)，过去q天的均值：也就是说，如果![MA^q_t](https://s0.wp.com/latex.php?latex=MA%5Eq_t&bg=ffffff&fg=444444&s=0)表示移动平均过程，那么：

![MA^q_t = \\frac{1}{q} \\sum_{i = 0}^{q-1} x_{t - i} ](https://s0.wp.com/latex.php?latex=MA%5Eq_t+%3D+%5Cfrac%7B1%7D%7Bq%7D+%5Csum_%7Bi+%3D+0%7D%5E%7Bq-1%7D+x_%7Bt+-+i%7D+&bg=ffffff&fg=444444&s=0)

移动平均平滑一个序列，并且帮助识别趋势。对于序列![x_t](https://s0.wp.com/latex.php?latex=x_t&bg=ffffff&fg=444444&s=0)中的短期波动，![q](https://s0.wp.com/latex.php?latex=q&bg=ffffff&fg=444444&s=0)越大，移动平均过程越少响应。这里的思想是，移动平均过程帮助从“噪声”识别趋势。**快速的**移动平均拥有较小的![q](https://s0.wp.com/latex.php?latex=q&bg=ffffff&fg=444444&s=0)，并且更紧随股票，而**缓慢的**移动平均拥有较大的![q](https://s0.wp.com/latex.php?latex=q&bg=ffffff&fg=444444&s=0)，结果是它们更少响应股票波动，并且更稳定。

**pandas**提供方便计算移动平均的函数。我通过为Apple数据创建一个20天（一个月）的移动平均，并将其绘制在股票旁边，来演示它是使用。

```python

    apple["20d"] = np.round(apple["Close"].rolling(window = 20, center = False).mean(), 2)
    pandas_candlestick_ohlc(apple.loc['2016-01-04':'2016-08-07',:], otherseries = "20d")
    
```

![png](https://ntguardian.files.wordpress.com/2016/09/output_19_0.png?w=680)

注意移动平均开始的位置有多后。直到20天过去了，才能计算它。对于更长的移动平均，这种限制变得更加严重。因为我想要能够计算200天的移动平均，所以我将扩充我们所有的AAPL数据。尽管如此，我们仍然将主要集中在2016年。

```python

    start = datetime.datetime(2010,1,1)
    apple = web.DataReader("AAPL", "yahoo", start, end)
    apple["20d"] = np.round(apple["Close"].rolling(window = 20, center = False).mean(), 2)
    
    pandas_candlestick_ohlc(apple.loc['2016-01-04':'2016-08-07',:], otherseries = "20d")
    
```

![png](https://ntguardian.files.wordpress.com/2016/09/output_21_0.png?w=680)

你会返现，一条移动平均线比实际的股票数据平滑得多。此外，这是一个顽固的指标；一只股票需要位于移动平均线的上方或下方，以便改变线的方向。因此，交叉一条移动平均线预示趋势的可能变化，应该引起注意。

交易员通常对多条移动平均线感兴趣，例如20日，50日和200日移动平均线。很容易一次性检查多条移动平均线。

```python

    apple["50d"] = np.round(apple["Close"].rolling(window = 50, center = False).mean(), 2)
    apple["200d"] = np.round(apple["Close"].rolling(window = 200, center = False).mean(), 2)
    
    pandas_candlestick_ohlc(apple.loc['2016-01-04':'2016-08-07',:], otherseries = ["20d", "50d", "200d"])
    
```

![png](https://ntguardian.files.wordpress.com/2016/09/output_23_0.png?w=680)

20日的移动平均线对局部变化最为敏感，而200日的移动平均线则最不敏感。这里，200日的移动平均线表示总体的**熊市**趋势：股票随着时间下降的趋势。而20日移动平均线偶尔看跌，偶尔**看涨**，其中，预期正摆动。你还可以看到交叉的移动平均线表示趋势的改变。这些是我们可以当成**交易信号**或者金融安全正改变方向，并且可能作出获利的交易的指示来使用的交叉。T

_下周访问本网站来看看如何使用移动平均线来设计和测试一个交易策略。_
