原文：[Matplotlib tutorial: Plotting tweets mentioning Trump, Clinton & Sanders](https://www.dataquest.io/blog/matplotlib-tutorial/)

---

## 使用Pandas和Matplotlib分析Tweets

Python有多种可视化库，包括[seaborn](https://stanford.edu/~mwaskom/software/seaborn/), [networkx](https://github.com/networkx/networkx), 和[vispy](http://vispy.org/index.html)。大多数的Python可视化库全部或部分基于[matplotlib](http://matplotlib.org/)，这往往是绘制简单的图的第一种手段，也是绘制那些难以在其他库绘制的图的最后一种手段。

在这个matplotlib教程中，我们将介绍该库的基本知识，并看看如何进行一些中间可视化。

我们将使用包含将近240,000条关于Hillary Clinton, Donald Trump, 和Bernie Sanders，目前所有美国总统候选人的微博的数据集。

该数据是从Twitter Streaming API拉过来的，而所有240,000条微博的csv文件可以在[这里](https://s3.amazonaws.com/dqdata/tweets.csv)下载。如果你想自己爬取更多数据，那么你可以看看[这里](https://github.com/dataquestio/twitter-scrape)的爬虫代码。

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
  * `id_str` – Twitter上微博的id。
  * `user_location` – 微博用户在他们的Twitter信息中指定的位置。
  * `user_bg_color` – 微博用户简介的背景色。
  * `user_name` – 微博用户的Twitter用户名。
  * `polarity` – 微博的情感，从`-1`到`1`。`1`表示非常积极，`-1`表示非常消极。
  * `created` – 微博发送时间
  * `user_description` – 微博用户在其简介中指定的描述。
  * `user_created` – 微博账号创建时间。
  * `user_follower` – 该微博的关注人数。
  * `text` – 微博的文本。
  * `subjectivity` – 微博的主观性和客观性。`0`表示非常可观，`1`表示非常主观。

### 生成候选人列

我们可以用这个数据集进行的最有趣的事情包括，比较关于一个候选人的微博和另一个候选人的微博。例如，我们可以比较关于Donald Trump的微博的客观性和关于Bernie Sanders的微博的客观性。

为了完成这个任务，我们首先需要生成一个列，该列表示每条微博提到了哪个候选人。在下面的代码中，我们将：

  * 创建一个函数，查找在一段文字中，哪个候选人的名字出现了。
  * 在DataFrames之上使用[apply](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.apply.html)方法来生成一个名为`candidate`的新列，该列包括该微博提到了哪个（些）候选人。

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
  * 作为一个图像，显示该figure，以及其中的任何图。

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

一旦我们导入了matplotlib，我们就可以绘制一张关于每个候选人被提到的微博数的柱状图。为了完成这点，我们将：

  * 使用Pandas [Series](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.html)上的[value_counts](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.value_counts.html)函数来统计每个候选人有多少条提及他的微博。
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

关于Trump的微博比关于Sanders或者Clinton的微博多得惊人！

你可能注意到，我们并没有创建Figure或者任何Axes对象。这是因为调用`plt.bar`会自动设置一个Figure和一个Axes对象，表示该柱状图。调用[plt.show](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.show)方法会显示当前figure中的任何东西。在这种情况下，它显示一个包含了一个柱状图的图像。

在[pyplot](http://matplotlib.org/api/pyplot_api.html)模块中，matplotlib有一些方法可以使得创建常见类型的图更快和更方便，因为它们自动创建一个Figure和一个Axes对象。最广泛使用的是：

  * [plt.bar](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.bar) – 创建一个柱状图。
  * [plt.boxplot](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.boxplot) – 创建一个盒形图和须状图。
  * [plt.hist](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.hist) – 创建一个直方图。
  * [plt.plot](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.plot) – 创建一个线条图。
  * [plt.scatter](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.scatter) – 创建一个散点图。

调用任意这些方法将自动设置Figure和Axes对象，并且绘制图。这些方法的每一个都有不同的参数，可以传递它们来修改效果图。

## 自定义图

Now that we’ve made a basic first plot, we can move on to creating a more
customized second plot. We’ll make a basic histogram, then modify it to add
labels and other information.

One of the things we can look at is the age of the user accounts that are
tweeting. We’ll be able to find if there differences in when the accounts of
users who tweet about Trump and when the accounts of users who tweet about
Clinton were created. One candidate having more user accounts created recently
might imply some kind of manipulation of Twitter with fake accounts.

In the code below, we’ll:

  * Convert the `created` and `user_created` columns to the Pandas datetime type.
  * Create a `user_age` column that is the number of days since the account was created.
  * Create a histogram of user ages.
  * Show the histogram.

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

We can add titles and axis labels to matplotlib plots. The common methods with
which to do this are:

  * [plt.title](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.title) – adds a title to the plot.
  * [plt.xlabel](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.xlabel) – adds an x-axis label.
  * [plt.ylabel](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.ylabel) – adds a y-axis label.

Since all of the methods we discussed before, like `bar` and `hist`,
automatically create a Figure and a single Axes object inside the figure,
these labels will be added to the Axes object when the method is called.

We can add labels to our previous histogram using the above methods. In the
code below, we’ll:

  * Generate the same histogram we did before.
  * Draw a title onto the histogram.
  * Draw an x axis label onto the histogram.
  * Draw a y axis label onto the histogram.
  * Show the plot.

```python

    plt.hist(tweets["user_age"])
    plt.title("Tweets mentioning candidates")
    plt.xlabel("Twitter account age in years")
    plt.ylabel("# of tweets")
    plt.show()
```

![](https://www.dataquest.io/blog/images/matplotlib/user_ages_labelled.png)

### 绘制叠加柱状图

The current histogram does a nice job of telling us the account age of all
tweeters, but it doesn’t break it down by candidate, which might be more
interesting. We can leverage the additional options in the `hist` method to
create a stacked histogram.

In the below code, we’ll:

  * Generate three Pandas series, each containing the `user_age` data only for tweets about a certain candidate.
  * Make a stacked histogram by calling the `hist` method with additional options. 
    * Specifying a list as the input will plot three sets of histogram bars.
    * Specifying `stacked=True` will stack the three sets of bars.
    * Adding the `label` option will generate the correct labels for the legend.
  * Call the [plt.legend](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.legend) method to draw a legend in the top right corner.
  * Add a title, x axis, and y axis labels.
  * Show the plot.

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

We can take advantage of matplotlibs ability to draw text over plots to add
annotations. Annotations point to a specific part of the chart, and let us add
a snippet describing something to look at.

In the code below, we’ll make the same histogram as we did above, but we’ll
call the [plt.annotate](http://matplotlib.org/api/pyplot_api.html#matplotlib.p
yplot.annotate) method to add an annotation to the plot.

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

Here’s a description of what the options passed into `annotate` do:

  * `xy` – determines the `x` and `y` coordinates where the arrow should start.
  * `xytext` – determines the `x` and `y` coordinates where the text should start.
  * `arrowprops` – specify options about the arrow, such as color.

As you can see, there are significantly more tweets about Trump then there are
about other candidates, but there doesn’t look to be a significant difference
in account ages.

## 多个子图

So far, we’ve been using methods like `plt.bar` and `plt.hist`, which
automatically create a Figure object and an Axes object. However, we can
explicitly create these objects when we want more control over our plots. One
situation in which we would want more control is when we want to put multiple
plots side by side in the same image.

We can generate a Figure and multiple Axes objects by calling the [plt.subplot
s](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.subplots)
methods. We pass in two arguments, `nrows`, and `ncols`, which define the
layout of the Axes objects in the Figure. For example, `plt.subplots(nrows=2,
ncols=2)` will generate ` 2x2` grid of Axes objects. `plt.subplots(nrows=2,
ncols=1)` will generate a `2x1` grid of Axes objects, and stack the two Axes
vertically.

Each Axes object supports most of the methods from `pyplot`. For instance, we
could call the `bar` method on an Axes object to generate a bar chart.

### 提取颜色

We’ll generate `4` plots that show the amount of the colors red and blue in
the Twitter background colors of users tweeting about Trump. This may show if
tweeters who identify as Republican are more likely to put red in their
profile.

First, we’ll generate two columns, `red` and `blue`, that tell us how much of
each color is in each tweeter’s profile background, from `0` to `1`.

In the code below, we’ll:

  * Use the `apply` method to go through each row in the `user_bg_color` column, and extract how much red is in it.
  * Use the `apply` method to go through each row in the `user_bg_color` column, and extract how much blue is in it.

```python

    import matplotlib.colors as colors
    
    tweets["red"] = tweets["user_bg_color"].apply(lambda x: colors.hex2color('#{0}'.format(x))[0])
    tweets["blue"] = tweets["user_bg_color"].apply(lambda x: colors.hex2color('#{0}'.format(x))[2])
```

### 创建图

Once we have the data setup, we can create the plots. Each plot will be a
histogram showing how many tweeters have a profile background containing a
certain amount of blue or red.

In the below code, we:

  * Generate a Figure and multiple Axes with the `subplots` method. The axes will be returned as an array.
  * The axes are returned in a 2x2 [NumPy](http://www.numpy.org/) array. We extract each individual Axes object by using the [flat](http://docs.scipy.org/doc/numpy-1.10.1/reference/generated/numpy.ndarray.flat.html) property of arrays. This gives us `4` Axes objects we can work with.
  * Plot a histogram in the first Axes using the [hist](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.hist) method.
  * Set the title of the first Axes to `Red in all backgrounds` using the [set_title](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.set_title) method. This performs the same function as `plt.title`.
  * Plot a histogram in the second Axes using the [hist](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.hist) method.
  * Set the title of the second Axes to `Red in Trump tweeters` using the [set_title](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.set_title) method.
  * Plot a histogram in the third Axes using the [hist](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.hist) method.
  * Set the title of the third Axes to `Blue in all backgrounds` using the [set_title](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.set_title) method. This performs the same function as `plt.title`.
  * Plot a histogram in the fourth Axes using the [hist](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.hist) method.
  * Set the title of the fourth Axes to `Blue in Trump tweeters` using the [set_title](http://matplotlib.org/api/axes_api.html#matplotlib.axes.Axes.set_title) method.
  * Call the [plt.tight_layout](http://matplotlib.org/api/pyplot_api.html#matplotlib.pyplot.tight_layout) method to reduce padding in the graphs and fit all the elements.
  * Show the plot.

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

Twitter has default profile background colors that we should probably remove
so we can cut through the noise and generate a more accurate plot. The colors
are in hexadecimal format, where `#000000` is black, and `#ffffff` is white.

Here’s how to find the most common colors in background colors:

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

Now, we can remove the three most common colors, and only plot out users who
have unique background colors. The code below is mostly what we did earlier,
but we’ll:

  * Remove `C0DEED`, `000000`, and `F5F8FA` from `user_bg_color`.
  * Create a function with out plotting logic from the last chart inside.
  * Plot the same `4` plots from before without the most common colors in `user_bg_color`.

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

As you can see, the distribution of blue and red in background colors for
users that tweeted about Trump is almost identical to the distribution for all
tweeters.

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
