原文：[Analyzing iPhone Step Data](http://blog.yhat.com/posts/phone-steps-timeseries.html)

---

### 自白书

我叫Ross，沉迷于计算步数。行走的那种。这种沉迷带来的是经常性打开iPhone上的计步应用，查看步数上升，保证我的步数超过了10,000 (我妈妈说，那是一个神奇的数字)。幸运的是，在大多数的日子里，住在纽约让这个目标容易实现。

在这篇文章中，我会告诉你如何使用[pandas](http://pandas.pydata.org/)时间序列和[ggplot](http://github.com/yhat/ggplot)来分析我的iPhone步数数据。我在Python中使用[Rodeo](https://www.yhat.com/products/rodeo)来进行所有的数据科学工作，它是用于数据科学的[Yhat](https://www.yhat.com/) IDE。

### 收集数据

正如所有正经的数据迷，我希望能够将数据从手机导出以用于分析。Quantified Self Labs的牛人推出了一个名为[QS Access](http://quantifiedself.com/access-app/app)的应用，它让检索这些数据不在话下！

下面是几个导出数据的截图。

  ![](http://blog.yhat.com/static/img/steps-app-screenshot.png)


QS Access应用导出一个CSV文件，它包含3列：一个`start`时间戳，一个`finish`时间戳和期间的`steps (count)`。有一个选项，用来生成每小时/每天的数据行。为什么不从小时开始，看看情况如何 —— 更大的数据总是更好，对不对？

### TO THE DATAS!

我们的分析将利用[pandas](http://pandas.pydata.org/)中内置的时间序列工具。当[Wes McKinney](https://github.com/wesm)开始pandas项目时，他正为一家投资管理公司工作，而这个行业广泛依赖于时间序列分析。结果，pandas自带了这个领域的全面功能。

此时，有一些关于导入这个数据其他注意事项。

首先，我们已经知道我们拥有时间序列数据，因此，我们可以通过使用`parse_dates`参数告诉pandas。

CSV中的结束时间数据并不是特别有趣，因为我们有开始时间，并且还有每小时的频率，因此，可以用`usecols`来忽略它。

最后，设置开始时间 (col 0) 为索引列，获得一个`DateTimeIndex`，这在后面将让我们的工作更容易。

```python
df_hour = pd.read_csv('health_data_hour.csv', parse_dates=[0,1], names=['start_time', 'steps'], usecols=[0, 2], skiprows=1, index_col=0)
# ensure the steps col are ints - weirdness going on with this one
df_hour.steps = df_hour.steps.apply(lambda x: int(float(x)))
df_hour.head()
type(df_hour.index)
type(df_hour.steps[1])
```

start_time| steps
---|---
2014-10-24 18:00:00 |459
2014-10-24 19:00:00 |93
2014-10-24 20:00:00 |421
2014-10-24 21:00:00 |1306
2014-10-24 22:00:00 |39


注意到，start_time列的类型：`pandas.tseries.index.DatetimeIndex`。这是因为在数据采集过程中设置索引列，它为我们提供了访问所有好东西的能力 —— 重采样一个，我们稍后会看到。

### 每小时步数

快速绘制[(gg)plot](http://github.com/yhat/ggplot)来探索下我们这里拥有的数据，如何？

![](http://blog.yhat.com/static/img/hourly_step_plot.png)

我去！有点太乱了。我们可以如何提高我们的可视化呢？我想到了一个方法 —— pandas有一个名为`resample`的函数，它允许我们在更长的时间上汇总数据。

更精确地说，当你减少一个给定标志的采样率时，这就是所谓的**降低取样频率**。在这个例子中，我们将采用每小时的数据，并基于天/周/月，使用均值和总和聚合，进行重新取样。

### 降低取样频率到天步数

让我们先从每天总和开始 (注意，你可以将dataframe `__index__`传递到ggplot函数中):

```python
df_daily = pd.DataFrame()
df_daily['step_count'] = df_hour.steps.resample('D').sum()
df_daily.head()
p = ggplot(df_daily, aes(x='__index__', y='step_count')) + \
    geom_step() + \
    stat_smooth() + \
    scale_x_date(labels="%m/%Y") + \
    ggtitle("Daily Step Count") + \
    xlab("Date") + \
    ylab("Steps")
print p
```

![](http://blog.yhat.com/static/img/daily_step_plot.png)

哈哈！现在，我们取得了一些进展。这是一个更可读得多的图。**而且**，看起来有一个很好的上升趋势 (稍后我们将提到)。

### 降低取样频率到周/月步数

有了这个，我们就可以再进行每周/月的重新采样了。仅需传递`'W'`或者`'M'`到resample函数中。

因为我对每天的步数总和指标最感兴趣，因此我们可以开始使用一个均值聚合函数来获得周/月样本中的每日平均值 (一天获得10,000个！)。这只需要修改`resample`之后的`sum()`函数为`mean()`。

它看起来像这样：

```python
df_weekly['step_mean'] = df_daily.step_count.resample('W').mean()
df_monthly['step_mean'] = df_daily.step_count.resample('M').mean()
```

![](http://blog.yhat.com/static/img/weekly_step_mean_plot.png)
![](http://blog.yhat.com/static/img/monthly_step_mean_plot.png)

简短的附加说明：Pandas还可以做和我们所做相反的事情，称为上采样。如果你的项目需要的话，可以看看[这个文档](http://pandas.pydata.org/pandas-docs/version/0.17.0/generated/pandas.Panel.resample.html)。

### （稍微）更深入些

![](http://blog.yhat.com/static/img/go-deeper.jpg)

我很好奇，我在周末是不是会比工作日期间获得更多的步数。我们可以使用[Rodeo](https://www.yhat.com/products/rodeo)中的标签建议，来看看DateTimeIndex上可用的方法。

刚好有`weekday`和`weekday_name`方法，看起来有用。前者将返回一个对应每周中的一天的整数，而后者将返回那一天的字符串名称。在我们用那个信息作为一个新列后，对其应用一个辅助函数可以返回一个布尔值，用来判断它是否是周末。

```python
def weekendBool(day):
    if day not in ['Saturday', 'Sunday']:
        return False
    else:
        return True

df_daily['weekday'] = df_daily.index.weekday
df_daily['weekday_name'] = df_daily.index.weekday_name
df_daily['weekend'] = df_daily.weekday_name.apply(weekendBool)
df_daily.head()
```

start_time  |step_count |weekday  ||weekday_name  ||weekend|
---|---|---|---|---
2014-10-24  |2333 |4  |Friday |False
2014-10-25  |3085 |5  |Saturday |True
2014-10-26  |21636  |6  |Sunday |True
2014-10-27  |13776  |0  |Monday |False
2014-10-28  |5732 |1  |Tuesday  |False

ggplot有一个可用的stat_density绘图函数，对于比较周末和平日的总体非常适合。一探究竟：

```python
ggplot(aes(x='step_count', color='weekend'), data=df_daily) + \
    stat_density() + \
    ggtitle("Comparing Weekend vs. Weekday Daily Step Count") + \
    xlab("Step Count")
```

![](http://blog.yhat.com/static/img/weekend_density_plot.png)

我们还可以基于这个weekend_bool对数据进行分组，并且运行一些聚合方法来看看数据的差异。看看我之前在[grouping in padas](http://blog.yhat.com/posts/grouping-pandas.html)上的一篇文章，获取关于这个功能的说明。

```python
weekend_grouped = df_daily.groupby('weekend')
weekend_grouped.describe()

                 step_count     weekday
weekend
False   count    479.000000  479.000000
        mean   10145.832985    1.997912
        std     4962.913936    1.416429
        min      847.000000    0.000000
        25%     6345.000000    1.000000
        50%     9742.000000    2.000000
        75%    13195.000000    3.000000
        max    37360.000000    4.000000
True    count    192.000000  192.000000
        mean   11621.166667    5.500000
        std     7152.197426    0.501307
        min      641.000000    5.000000
        25%     6321.000000    5.000000
        50%    10228.000000    5.500000
        75%    15562.500000    6.000000
        max    35032.000000    6.000000

weekend_grouped.median()
            step_count  weekday
weekend
False          9742      2.0
True          10228      5.5
```

周末平均11,621步 (中位数是10,228) 对比工作日的10,146 (中位数是9,742)，看起来周末有微弱的优势！

### 现在，看趋势

让我们回到上升趋势

四月初，我从Charlotte, NC搬到了New York，在[Yhat](https://www.yhat.com/)当一个软件工程师。

我很好奇，这个位置的变化对我每天的步数有什么影响。我们可以应用与周末数据相同的方法，来看看。

我只是给你看点好东西：

![](http://blog.yhat.com/static/img/monthly_step_mean_plot_with_NYC_line.png)

![](http://blog.yhat.com/static/img/nyc_step_compare_plot.png)

上面的图看起来在搬到纽约市后，有显著的变化。比之位置的改变，还有更多的变量，像我刚开始更认真的跑步这个事实，这将贡献一些数据，但要控制其影响，需要更多的数据。也许在另一篇文章中会提到！

### 最后的思考

希望这篇分析足以让你开始检查你的步数数据，使用[Rodeo](https://www.yhat.com/products/rodeo)进行一些数据科学，探索[pandas](http://pandas.pydata.org/)的时间序列功能！如果你想对源码一探究竟，那么可以看看我关于这个项目的[repo](https://github.com/rkipp1210/data-projects)。

现在，回到统计那些步数。