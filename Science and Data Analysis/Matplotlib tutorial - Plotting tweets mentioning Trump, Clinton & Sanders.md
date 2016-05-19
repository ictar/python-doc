原文：[Matplotlib tutorial: Plotting tweets mentioning Trump, Clinton & Sanders](https://www.dataquest.io/blog/matplotlib-tutorial/)

---

## 使用Pandas和Matplotlib分析Tweets

Python有多种可视化库，包括[seaborn](https://stanford.edu/~mwaskom/software/seaborn/), [networkx](https://github.com/networkx/networkx), 和[vispy](http://vispy.org/index.html)。大多数的Python可视化库全部或部分基于[matplotlib](http://matplotlib.org/)，这往往是绘制简单的图的第一种手段，也是绘制那些难以在其他库绘制的图的最后一种手段。

在这个matplotlib教程中，我们将介绍该库的基本知识，并看看如何进行一些中间可视化。

我们将使用包含将近240,000条关于Hillary Clinton, Donald Trump, 和Bernie Sanders，目前所有美国总统候选人的推特的数据集。

该数据是从Twitter Streaming API拉过来的，而所有240,000条推特的csv文件可以在[这里](https://s3.amazonaws.com/dqdata/tweets.csv)下载。如果你想自己爬取更多数据，那么你可以看看[这里](https://github.com/dataquestio/twitter-scrape)的爬虫代码。

## 使用Pandas探索Tweets

在我们开始绘制之前，让我们加载数据并进行一些探索。我们可以使用[Pandas](http://pandas.pydata.org/)，这个数据分析Python库，来帮助我们。在下面的代码中，我们将：

  * 导入Pandas库。
  * 读取`tweets.csv`到一个Pandas [DataFrame](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)种。
  * 打印出该DataFrame的前`5`行。

```python

    import pandas as pd
    
    tweets = pd.read_csv("tweets.csv")
    tweets.head()
```

| id | id_str | user_location | user_bg_color | retweet_count | user_name | polarity | created | geo | user_description | user_created | user_followers | coordinates | subjectivity | text  
---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---  
0 |  1 |  729828033092149248 |  Wheeling WV |  022330 |  0 |  Jaybo26003 | 0.00 |  2016-05-10T00:18:57 |  NaN |  NaN |  2011-11-17T02:45:42 |  39 |  NaN |  0.0 |  Make a difference vote! WV Bernie Sanders Coul...  
1 |  2 |  729828033092161537 |  NaN |  C0DEED |  0 |  brittttany_ns |  0.15 | 2016-05-10T00:18:57 |  NaN |  18 // PSJAN |  2012-12-24T17:33:12 |  1175 | NaN |  0.1 |  RT @HlPHOPNEWS: T.I. says if Donald Trump wins...  
2 |  3 |  729828033566224384 |  NaN |  C0DEED |  0 |  JeffriesLori |  0.00 | 2016-05-10T00:18:57 |  NaN |  NaN |  2012-10-11T14:29:59 |  42 |  NaN |  0.0 | You have no one to blame but yourselves if Tru...  
3 |  4 |  729828033893302272 |  global |  C0DEED |  0 |  WhorunsGOVs |  0.00 | 2016-05-10T00:18:57 |  NaN |  Get Latest Global Political news as they unfold |  2014-02-16T07:34:24 |  290 |  NaN |  0.0 |  'Ruin the rest of their lives': Donald Trump c...  
4 |  5 |  729828034178482177 |  California, USA |  131516 |  0 |  BJCG0830 | 0.00 |  2016-05-10T00:18:57 |  NaN |  Queer Latino invoking his 1st amendment privil... |  2009-03-21T01:43:26 |  354 |  NaN |  0.0 |  RT @elianayjohnson: Per source, GOP megadonor ...  
  
下面是该数据中重要列的简要说明：

  * `id` – 在数据库中行的id（这并不重要）。
  * `id_str` – Twitter上推特的id。
  * `user_location` – 推特用户在他们的Twitter信息中指定的位置。
  * `user_bg_color` – 推特用户简介的背景色。
  * `user_name` – 推特用户的Twitter用户名。
  * `polarity` – 推特的情感，从`-1`到`1`。`1`表示非常积极，`-1`表示非常消极。
  * `created` – 推特发送时间
  * `user_description` – 推特用户在其简介中指定的描述。
  * `user_created` – 推特账号创建时间。
  * `user_follower` – 该推特的关注人数。
  * `text` – 推特的文本。
  * `subjectivity` – 推特的主观性和客观性。`0`表示非常可观，`1`表示非常主观。

### 生成候选人列

我们可以用这个数据集进行的最有趣的事情包括，比较关于一个候选人的推特和另一个候选人的推特。例如，我们可以比较关于Donald Trump的推特的客观性和关于Bernie Sanders的推特的客观性。

为了完成这个任务，我们首先需要生成一个列，该列表示每条推特提到了哪个候选人。在下面的代码中，我们将：

  * 创建一个函数，查找在一段文字中，哪个候选人的名字出现了。
  * 在DataFrames之上使用[apply](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.apply.html)方法来生成一个名为`candidate`的新列，该列包括该推特提到了哪个（些）候选人。

```python

    def get_candidate(row):
        candidates = []
        text = row["text"].lower()
        if "clinton" in text or "hillary" in text:
            candidates.append("clinton")
        if "trump" in text or "donald" in text:
            candidates.append("trump")
        if "sanders" in text or "bernie" in text:
            candidates.append("sanders")
        return ",".join(candidates)
    
    tweets["candidate"] = tweets.apply(get_candidate,axis=1)
```

## 绘制第一张图

现在，我们准备好了。我们已经准备好使用matplotlib绘制第一张图。在matplotlib中，绘制一张图包括：

  * 创建一个[Figure](http://matplotlib.org/api/figure_api.html)来绘制图。
  * 创建一个或多个[Axes](http://matplotlib.org/api/axes_api.html)对象来绘制该图。
  * 作为一个图像，显示该图表，以及其中的任何图。

由于其灵活性，你可以在matplotlib中把多个图绘制在一张图片中。每一个Axes对象表示一张图，例如一个柱状图或直方图。

这可能听起来很复杂，但是matplotlib具有一些方便的方法，可以为我们完成建立一个Figure和Axes对象的工作。

### 导入matplotlib

为了使用matplotlib，首先，你讲需要使用`import matplotlib.pyplot as plt`导入该库。如果你正使用Jupyter notebook，从而在该notebook内部设置使用matplotlib。

```python

    import matplotlib.pyplot as plt
    import numpy as np
    %matplotlib inline
```

我们导入`matplotlib.pyplot`，因为这包含matplotlib的绘图函数。为了方便，我们重命名它为`plt`，因此可以更快绘图。

### 绘制柱状图

一旦我们导入了matplotlib，我们就可以绘制一张关于每个候选人被提到的推特数的柱状图。为了完成这点，我们将：

  * 使用Pandas [Series](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.html)上的[value_counts](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.value_counts.html)函数来统计每个候选人有多少条提及他的推特。
  * 使用`plt.bar`来创建一个柱状图。我们将传递取值从`0`到`candidate`列的唯一值数目的数字列表，作为x轴输入，把计数当成y轴输入。
  * 显示计数，从而我们拥有更多关于每一个柱子表示什么的上下文信息。

```python

    counts = tweets["candidate"].value_counts()
    plt.bar(range(len(counts)), counts)
    plt.show()
    
    print(counts)
```

```

    trump                    119998
    clinton,trump             30521
                              25429
    sanders                   25351
    clinton                   22746
    clinton,sanders            6044
    clinton,trump,sanders      4219
    trump,sanders              3172
    Name: candidate, dtype: int64
    
```

![](https://www.dataquest.io/blog/images/matplotlib/tweet_counts.png)

关于Trump的推特比关于Sanders或者Clinton的推特多得惊人！

你可能注意到，我们并没有创建Figure或者任何Axes对象。这是因为调用`plt.bar`会自动设置一个Figure和一个Axes对象，表示该柱状图。调用[plt.show](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.show)方法会显示当前图表中的任何东西。在这种情况下，它显示一个包含了一个柱状图的图像。

在[pyplot](http://matplotlib.org/api/pyplot_api.html)模块中，matplotlib有一些方法可以使得创建常见类型的图更快和更方便，因为它们自动创建一个Figure和一个Axes对象。最广泛使用的是：

  * [plt.bar](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.bar) – 创建一个柱状图。
  * [plt.boxplot](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.boxplot) – 创建一个盒形图和须状图。
  * [plt.hist](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.hist) – 创建一个直方图。
  * [plt.plot](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.plot) – 创建一个线条图。
  * [plt.scatter](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.scatter) – 创建一个散点图。

调用任意这些方法将自动设置Figure和Axes对象，并且绘制图。这些方法的每一个都有不同的参数，可以传递它们来修改效果图。

## 自定义图

现在，我们已经有了第一个基本的图，可以继续创建第二个更个性化的图了。我们会绘制一张基本的直方图，然后修改它，以添加标签及其他信息。

我们可以看的事情之一就是发推特的用户账号年龄。我们可以找到发关于Trump的推特的用户账号和发关于Clinton的推特的用户账号的创建时间之间是否有区别。拥有更多最近创建的用户账号的候选人可能意味着使用假账号进行某种Twitter操纵。

在下面的代码中，我们会：

  * 将`created`和`user_created`列转换成Pandas datetime类型。
  * 创建一个`user_age`列，表示从该账号创建后至今的天数。
  * 创建用户年龄直方图。
  * 显示该直方图。

```python

    from datetime import datetime
    
    tweets["created"] = pd.to_datetime(tweets["created"])
    tweets["user_created"] = pd.to_datetime(tweets["user_created"])
    
    tweets["user_age"] = tweets["user_created"].apply(lambda x: (datetime.now() - x).total_seconds() / 3600 / 24 / 365)
    plt.hist(tweets["user_age"])
    plt.show()
```

![](https://www.dataquest.io/blog/images/matplotlib/user_ages.png)

### 添加标签

我们可以添加标题和轴标签到matplotlib图中。完成这件事的通用方法是：

  * [plt.title](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.title) – 添加标题到图上。
  * [plt.xlabel](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.xlabel) – 添加x轴标签。
  * [plt.ylabel](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.ylabel) – 添加y轴标签。

由于我们之前讨论到的所有方法，像`bar`和`hist`，都会在figure中自动创建一个Figure和一个Axes对象，因此当调用该方法时，这些标签将会被添加到Axes对象上。

我们可以用上面的方法添加标签到我们之前的直方图上。在下面的代码中，我们会：

  * 生成我们之前完成的相同的直方图。
  * 画一个标题到该直方图。
  * 画一个x轴标签到该直方图上。
  * 画一个y轴标签到该直方图上。
  * 显示该图。

```python

    plt.hist(tweets["user_age"])
    plt.title("Tweets mentioning candidates")
    plt.xlabel("Twitter account age in years")
    plt.ylabel("# of tweets")
    plt.show()
```

![](https://www.dataquest.io/blog/images/matplotlib/user_ages_labelled.png)

### 绘制叠加柱状图

现在的直方图可以很好的告诉我们所有的推特账户的注册年龄，但是它并没有根据候选人进行分类，这可能会更有趣。我们可以在`hist`放中添加额外的选项，以创建一个叠加柱状图。

在下面的代码中，我们会：

  * 生成三个Pandas series，每个只包含关于某个特定的候选人的推特的`user_age`数据。
  * 通过调用`hist`方法，并添加额外的选项创建一个叠加直方图。
    * 指定一个列表作为输入将绘制三组柱状图。
    * 指定`stacked=True`将叠加这三个条的集合。
    * 增加`label`选项将为图例生成正确的标签。
  * 调用[plt.legend](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.legend)方法来在右上角绘制一个图例。
  * 添加标题，x轴和y轴标签。
  * 显示该图。

```python

    cl_tweets = tweets["user_age"][tweets["candidate"] == "clinton"]
    sa_tweets = tweets["user_age"][tweets["candidate"] == "sanders"]
    tr_tweets = tweets["user_age"][tweets["candidate"] == "trump"]
    plt.hist([
            cl_tweets, 
            sa_tweets, 
            tr_tweets
        ], 
        stacked=True, 
        label=["clinton", "sanders", "trump"]
    )
    plt.legend()
    plt.title("Tweets mentioning each candidate")
    plt.xlabel("Twitter account age in years")
    plt.ylabel("# of tweets")
    plt.show()
```

![](https://www.dataquest.io/blog/images/matplotlib/user_ages_stacked.png)

### 注释直方图

我们可以利用matplotlibs在图上绘制文本的能力来添加注释。注释指向图表的特定部分，让我们一个片段来描述一些东东。

在下面的代码中，我们会创建和上面一样的直方图，但是会调用[plt.annotate](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.annotate)方法来添加注释到图中。

```python

    plt.hist([
            cl_tweets, 
            sa_tweets, 
            tr_tweets
        ], 
        stacked=True, 
        label=["clinton", "sanders", "trump"]
    )
    plt.legend()
    plt.title("Tweets mentioning each candidate")
    plt.xlabel("Twitter account age in years")
    plt.ylabel("# of tweets")
    plt.annotate('More Trump tweets', xy=(1, 35000), xytext=(2, 35000),
                arrowprops=dict(facecolor='black'))
    plt.show()
```

![](https://www.dataquest.io/blog/images/matplotlib/user_ages_annotated.png)

下面是传给`annotate`的选项的行为描述：

  * `xy` – 确定`x`和`y`坐标中箭头应该从哪里开始。
  * `xytext` – 确定`x`和`y`坐标中文本应该从哪里开始。
  * `arrowprops` – 指定箭头相关的选项，例如颜色。

正如你所见的，关于Trump的推特明显比其他候选人更多，但是在账号注册年龄上，看不出显著的差异。

## 多个子图

目前为止，我们使用了一些方法，像`plt.bar`和`plt.hist`，它们会自动创建一个Figure对象和一个Axes对象。然而，当我们想获得关于图的更多控制时，我们可以显式创建这些对象。我们可能想要更多控制的场景之一是，当我们想要在同张图上并排放置多个图表。

通过调用[plt.subplots](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.subplots)方法，我们可以生成一个Figure和多个Axes对象。传递两个参数，`nrows`和`ncols`，它们定义在Figure中Axes对象的布局。例如，`plt.subplots(nrows=2, ncols=2)`会生成` 2x2`网格的Axes对象。`plt.subplots(nrows=2, ncols=1)`会生成`2x1`网格的Axes对象，然后将这两个Axes对象垂直堆积在一起。

每个Axes对象支持`pyplot`中的大多数方法。例如，我们可以在一个Axes对象上调用`bar`方法来生成一个柱状图。

### 提取颜色

我们将生成`4`张图，用来那些发关于Trump推特的用户的Twitter背景色中的红色和蓝色的数量。这可能显示，确定为共和党派的推特用户是否更倾向于在他们的个人资料中使用红色。

首先，我们要生成两列，`red`和`blue`，用来表示在每个推特用户的个人资料背景中，每种颜色的多少，从`0`到`1`。

在下面的代码中，我们将：

  * 使用`apply`方法来遍历`user_bg_color`列中的每一行，然后提取其中的红色总数。
  * 使用`apply`方法来遍历`user_bg_color`列中的每一行，然后提取其中的蓝色总数。

```python

    import matplotlib.colors as colors
    
    tweets["red"] = tweets["user_bg_color"].apply(lambda x: colors.hex2color('#{0}'.format(x))[0])
    tweets["blue"] = tweets["user_bg_color"].apply(lambda x: colors.hex2color('#{0}'.format(x))[2])
```

### 创建图

一旦我们拥有了数据，我们就可以创建图。每张图将会是一个直方图，用以显示个人资料背景包含特定数量的蓝色或红色的推特用户数。

在下面的代码中，我们：

  * 使用`subplots`方法生成一个Figure和多个Axes。Axes将作为数组返回。
  * Axes在一个2x2 [NumPy](http://www.numpy.org/)数组中返回。通过使用数组的[flat](http://docs.scipy.org/doc/numpy-1.10.1/reference/generated/numpy.ndarray.flat.html)属性，提取每个Axes对象。这为我们提供了`4`个Axes对象用以工作。
  * 使用[hist](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.hist)方法在第一个Axes中绘制一个直方图。
  * 使用[set_title](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.set_title)方法，设置第一个Axes的标题为`Red in all backgrounds`。这与`plt.title`功能一致。
  * 使用[hist](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.hist)方法在第二个Axes中绘制一个直方图。
  * 使用[set_title](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.set_title)方法，设置第二个Axes的标题为`Red in Trump tweeters`。
  * 使用[hist](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.hist)方法在第三个Axes中绘制一个直方图。
  * 使用[set_title](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.set_title)方法，设置第三个Axes的标题为`Blue in all backgrounds`。这与`plt.title`功能一致。
  * 使用[hist](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.hist)方法在第四个Axes中绘制一个直方图。
  * 使用[set_title](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.set_title)方法，设置第四个Axes标题为`Blue in Trump tweeters`。
  * 调用[plt.tight_layout](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.tight_layout)方法来减少图间的填充并调整所有元素。
  * 显示该图。

```python

    fig, axes = plt.subplots(nrows=2, ncols=2)
    ax0, ax1, ax2, ax3 = axes.flat
    
    ax0.hist(tweets["red"])
    ax0.set_title('Red in backgrounds')
    
    ax1.hist(tweets["red"][tweets["candidate"] == "trump"].values)
    ax1.set_title('Red in Trump tweeters')
    
    ax2.hist(tweets["blue"])
    ax2.set_title('Blue in backgrounds')
    
    ax3.hist(tweets["blue"][tweets["candidate"] == "trump"].values)
    ax3.set_title('Blue in Trump tweeters')
    
    plt.tight_layout()
    plt.show()
```

![](https://www.dataquest.io/blog/images/matplotlib/bg_colors.png)

### 移除共同的背景色

Twitter有默认的个人资料背景颜色，我们或许应该移除它，这样才能通过消除噪音，以生成一个更准确的图。该演示是十六进制格式的，其中，`#000000`是黑色，而`#ffffff`是白色。

下面是如何查找背景颜色中的最常见的颜色：

```python

    tweets["user_bg_color"].value_counts()
```

```python

        C0DEED    108977
        000000     31119
        F5F8FA     25597
        131516      7731
        1A1B1F      5059
        022330      4300
        0099B9      3958
    
```

现在，我们可以删除三种最常见的颜色，然后只画出那些有唯一背景颜色的用户。下面的代码大多数我们之前做过的，但是我们会：

  * 从`user_bg_color`中移除`C0DEED`, `000000`, 和`F5F8FA`。
  * 创建一个函数，不在最后一个图表中绘制逻辑。
  * 绘制和前面`4`个图一样的图，除了`user_bg_color`中最常见的颜色。

```python

    tc = tweets[~tweets["user_bg_color"].isin(["C0DEED", "000000", "F5F8FA"])]
    
    def create_plot(data):
        fig, axes = plt.subplots(nrows=2, ncols=2)
        ax0, ax1, ax2, ax3 = axes.flat
    
        ax0.hist(data["red"])
        ax0.set_title('Red in backgrounds')
    
        ax1.hist(data["red"][data["candidate"] == "trump"].values)
        ax1.set_title('Red in Trump tweets')
    
        ax2.hist(data["blue"])
        ax2.set_title('Blue in backgrounds')
    
        ax3.hist(data["blue"][data["candidate"] == "trump"].values)
        ax3.set_title('Blue in Trump tweeters')
    
        plt.tight_layout()
        plt.show()
    
    create_plot(tc)
```

![](https://www.dataquest.io/blog/images/matplotlib/bg_colors_sel.png)


正如你所看到的，发布关于Trump的推特的用户的背景颜色中，红色和蓝色的分布几乎与所有推特用户的分布相同。

## 绘制情绪

We generated sentiment scores for each tweet using
[TextBlob](http://textblob.readthedocs.io/en/dev/), which are stored in the
`polarity` column. We can plot the mean value for each candidate, along with
the standard deviation. The standard deviation will tell us how wide the
variation is between all the tweets, whereas the mean will tell us how the
average tweet is.

In order to do this, we can add 2 Axes to a single Figure, and plot the mean
of `polarity` in one, and the standard deviation in the other. Because there
are a lot of text labels in these plots, we’ll need to increase the size of
the generated figure to match. We can do this with the `figsize` option in the
`plt.subplots` method.

The code below will:

  * Group tweets by candidate, and compute the mean and standard deviation for each numerical column (including `polarity`).
  * Create a Figure that’s `7` inches by `7` inches, with 2 Axes objects, arranged vertically.
  * Create a bar plot of the standard deviation the first Axes object. 
    * Set the tick labels using the [set_xticklabels](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.set_xticklabels) method, and rotate the labels `45` degrees using the `rotation` argument.
    * Set the title.
  * Create a bar plot of the mean on the second Axes object. 
    * Set the tick labels.
    * Set the title.
  * Show the plot.

```python

    gr = tweets.groupby("candidate").agg([np.mean, np.std])
    
    fig, axes = plt.subplots(nrows=2, ncols=1, figsize=(7, 7))
    ax0, ax1 = axes.flat
    
    std = gr["polarity"]["std"].iloc[1:]
    mean = gr["polarity"]["mean"].iloc[1:]
    ax0.bar(range(len(std)), std)
    ax0.set_xticklabels(std.index, rotation=45)
    ax0.set_title('Standard deviation of tweet sentiment')
    
    ax1.bar(range(len(mean)), mean)
    ax1.set_xticklabels(mean.index, rotation=45)
    ax1.set_title('Mean tweet sentiment')
    
    plt.tight_layout()
    plt.show()
```

![](https://www.dataquest.io/blog/images/matplotlib/sentiment.png)

## 生成并排条形图

We can plot tweet length by candidate using a bar plot. We’ll first split the
tweets into `short`, `medium`, and `long` tweets. Then, we’ll count up how
many tweets mentioning each candidate fall into each group. Then, we’ll
generate a bar plot with bars for each candidate side by side.

### 生成tweet长度

To plot the tweet lengths, we’ll first have to categorize the tweets, then
figure out how many tweets by each candidate fall into each bin.

In the code below, we’ll:

  * Define a function to mark a tweet as `short` if it’s less than `100` characters, `medium` if it’s `100` to `135` characters, and `long` if it’s over `135` characters.
  * Use `apply` to generate a new column `tweet_length`.
  * Figure out how many tweets by each candidate fall into each group.

```python

    def tweet_lengths(text):
        if len(text) < 100:
            return "short"
        elif 100 <= len(text) <= 135:
            return "medium"
        else:
            return "long"
    
    tweets["tweet_length"] = tweets["text"].apply(tweet_lengths)
    
    tl = {}
    for candidate in ["clinton", "sanders", "trump"]:
        tl[candidate] = tweets["tweet_length"][tweets["candidate"] == candidate].value_counts()
```

### 绘图

Now that we have the data we want to plot, we can generate our side by side
bar plot. We’ll use the `bar` method to plot the tweet lengths for each
candidate on the same axis. However, we’ll use an offset to shift the bars to
the right for the second and third candidates we plot. This will give us three
category areas, `short`, `medium`, and `long`, with one bar for each candidate
in each area.

In the code below, we:

  * Create a Figure and a single Axes object.
  * Define the `width` for each bar, `.5`.
  * Generate a sequence of values, `x`, that is `0`, `2`, `4`. Each value is the start of a category, such as `short`, `medium`, and `long`. We put a distance of `2` between each category so we have space for multiple bars.
  * Plot `clinton` tweets on the Axes object, with the bars at the positions defined by `x`.
  * Plot `sanders` tweets on the Axes object, but add `width` to `x` to move the bars to the right.
  * Plot `trump` tweets on the Axes object, but add `width * 2` to `x` to move the bars to the far right.
  * Set the axis labels and title.
  * Use `set_xticks` to move the tick labels to the center of each category area.
  * Set tick labels.

```python

    fig, ax = plt.subplots()
    width = .5
    x = np.array(range(0, 6, 2))
    ax.bar(x, tl["clinton"], width, color='g')
    ax.bar(x + width, tl["sanders"], width, color='b')
    ax.bar(x + (width * 2), tl["trump"], width, color='r')
    
    ax.set_ylabel('# of tweets')
    ax.set_title('Number of Tweets per candidate by length')
    ax.set_xticks(x + (width * 1.5))
    ax.set_xticklabels(('long', 'medium', 'short'))
    ax.set_xlabel('Tweet length')
    plt.show()
```

![](https://www.dataquest.io/blog/images/matplotlib/tweet_length.png)

## 下一步

We’ve learned quite a bit about how matplotlib generates plots, and gone
through a good bit of the dataset. If you want to read more about how
matplotlib plots internally, read
[this](http://matplotlib.org/users/artists.html).

You can make quite a few plots next:

  * Analyze user descriptions, and see how description length varies by candidate.
  * Explore time of day – do supporters of one candidate tweet more at certain times?
  * Explore user location, and see which states tweet about which candidates the most.
  * See what kinds of usernames tweet more about what kinds of candidates. 
    * Do more digits in usernames correlate with support for a candidate?
    * Which candidate has the most all caps supporters?
  * Scrape more data, and see if the patterns shift.

Hope this matplotlib tutorial was helpful, if you do any interesting analysis
with this data please leave a comment and link below - we’d love to know!
