原文：[Building Read It Later App with Python Newspaper Library](https://github.com/shekhargulati/52-technologies-in-2016/blob/master/16-newspaper/README.md)

---

欢迎来到第16周的[2016年的52个技术](https://github.com/shekhargulati/52-technologies-in-2016)博客系列。本周，我将像你展示如何使用Twitter的喜欢(likes)或者收藏(favorites))构建一个简单但是可以工作的**稍后读取(Read It Later)**应用。我依赖我的Twitter订阅以获取我每日的阅读推荐。每天，我都刷Twitter几次，而每当我发现任何我感兴趣的文章，我都会赞一下，这样稍后我就可以阅读它了。在本教程中，你将学习如何使用Python编程语言构建`Read It Later`应用。我们将利用文章提取来从url中提取相关的内容。

要构建这个应用，我们将使用下面这些Python库：

1. **Flask**: 我们将使用`Flask`框架来处理该应用的web特性，例如，处理请求，以及响应阅读推荐。
2. **Tweepy**: `Tweepy`是一个非常容易使用的库，我们将用它来与Twitter Streaming API进行通信。我们将关注一个用户的Twitter stream，这样的话，一旦用户赞了一条tweet，就会通知我们的应用。
3. **Newspaper**: [newspaper](https://github.com/codelucas/newspaper)是一个用Python写的执行文章提取的棒棒哒的库。它利用流行的Python库，例如`beautifulsoup4`, `lxml`, `nltk`，来完成工作。

在本教程的最后，你将获得一个简单但是能用的应用，来查看想稍后阅读的文章。下面是我们应用的截图。正如你可以在下面看到的，我们从url提取主要的图片，摘要文本和标题。

![](https://github.com/shekhargulati/52-technologies-in-2016/raw/master/16-newspaper/images/stories.jpg)

> **本文是我长达一年的博客系列，[2016年的52个技术](https://github.com/shekhargulati/52-technologies-in-2016)中的一部分**

## Newspaper是啥？

在本教程中，我将谈一谈一个名为[Newspaper](http://newspaper.readthedocs.org/)的Python包。Newspaper是一个开源的新闻全文和文章元数据提取库，用Python 3编写。它有一个非常易于使用的API，可以帮助你分分钟上手。它可以用于提取文章的主要文本，文章的主图片，文章中的视频，元描述和文章中的元标签。Newspaper立足于`beautifulsoup4`, `lxml`, 和`nltk`库坚实的基础之上。

从一个URL中提取文本就如下面一般简单。

```python
>>> from newspaper import Article

>>> url = 'http://firstround.com/review/the-remarkable-advantage-of-abundant-thinking/'
>>> article = Article(url)
>>> article.build()

>>> article.title
'The Remarkable Advantage of Abundant Thinking'

>>> article.text.split('\n\n')[0]
"If you consider yourself to be ambitious, this has happened to you. Your alarm goes off, and you're ambushed by thoughts of the grind ahead; finding that needle in a haystack; denting the universe; the roller coaster that never ends and many more horrible but unfortunately apt cliches. Today, the groupthink in tech largely believes that you have to suffer and barely survive to succeed. But this is a trap, says sought-after executive coach Katia Verresen, who counsels leaders at Facebook, Stanford, Airbnb, Twitter, and a number of prominent startups."
```

## 前提条件

要跟着本文做下去，你需要在你的机器上遵守以下步骤：

1. **Python**: 你可以从[https://www.python.org/downloads/](https://www.python.org/downloads/)上面为你的操作系统下载Python可执行文件。我将使用Python `3.4.2`.

2. **Virtualenv**: Virtualenv工具允许你创建隔离的Python环境，从而不污染全局Python安装。这允许你轻松地在单一机器上使用多个Python版本。请参考官方文档以获得[安装](https://virtualenv.pypa.io/en/latest/installation.html)说明。

3. 新建一个Twitter应用[http://dev.twitter.com/apps](http://dev.twitter.com/apps)，并记下`Consumer Key (API Key)`, `Consumer Secret (API Secret)`, `Access Token`, 和`Access Token Secret`。我们将使用它们为一个用户连接到Twitter API。

## Github仓库

演示应用程序的代码可以在github的[dailyreads](https://github.com/shekhargulati/dailyreads)上找到。

## 步骤1：环境配置

我们将开始建立开发环境，这样我们才能够构建`dailyreads`应用。对于我的大多数Python应用，我使用Python的`virtualenv`。它帮助我分离我的工程环境，不会污染全局Python安装。

打开一个命令行终端，切换到你的文件系统上的一个方便的目录。创建一个名为`dailyreads`的新目录，然后切换到该目录下。

```bash
$ mkdir dailyreads && cd dailyreads
```

在`dailyreads`目录下，创建件一个Python 3 virtualenv然后激活它。

```bash
$ virtualenv venv --python=python3
$ source venv/bin/activate
```

你可以通过执行`which python`命令来验证该Python安装。它应该指向`venv`目录中的Python安装。

## 步骤2：下载并安装依赖

正如上面所提到的，我们将利用`flask`, `tweepy`, 和`newspaper`库。我们可以使用`pip`来下载它们。`pip`是一个包管理器，用来安装和管理用Python写的软件包。

```bash
$ pip install flask
$ pip install tweepy
$ pip install newspaper3k
```

This will download all the required dependencies and their transitive dependencies.  To view all the installed packages, you can use the `pip list` command.

## 步骤3：为用户已赞微博编写twitter stream监听器

在`dailyreads`目录中创建一个新的文件`app.py`。我们的应用的第一个任务是监听用户的喜欢tweet stream。我们会利用`Tweepy`库来连接到用户的tweet stream，并选择类型为`favorite`的事件，如下所示：

```python
from __future__ import absolute_import, print_function
from tweepy.streaming import StreamListener
from tweepy import OAuthHandler
from tweepy import Stream
import os

consumer_key=os.getenv("TWITTER_CONSUMER_KEY")
consumer_secret=os.getenv("TWITTER_CONSUMER_SECRET")
access_token=os.getenv("TWITTER_ACCESS_TOKEN")
access_token_secret=os.getenv("TWITTER_ACCESS_SECRET")

class LikedTweetsListener(StreamListener):
    def on_data(self, data):
        tweet = json.loads(data)
        if 'event' in tweet and tweet['event'] == "favorite":
            print(tweet)
        return True

    def on_error(self, status):
        print("Error status received : {0}".format(status))

if __name__ == '__main__':
    l = LikedTweetsListener()
    auth = OAuthHandler(consumer_key, consumer_secret)
    auth.set_access_token(access_token, access_token_secret)

    stream = Stream(auth, l)
    stream.userstream()
```

在上面的代码中，我们做了以下这些事：

1. 我们导入了所有连接到Twitter API需要用的类和方法。
2. 我们从环境变量中抽取twitter keys，并将其设置为脚本变量。从一开始就使用环境变量是一个好主意，它能确保你不会将你的keys提交到版本控制系统，从而导致它们被公开。
3. 我们创建了一个新的`StreamListener`监听器。`StreamListener`公开了几个`on_*`方法，当特定的事件发生时，它们将会被调用。我们重写了`on_data`和`on_error`处理器。从它们的名字显然可知，只要新数据可用，`on_data`就会被调用；只要有任何异常发生，`on_error`就会被调用。在`on_data`方法中，我们只打印出类型为`favorite`的事件。对于like tweet，Twitter也是用favorite事件类型。
4. 最后，我们创建了`Stream`对象的实例，然后在它上面调用`userstream`方法。

你可以使用`python app.py`来运行`app.py`。在运行脚本之前，确保设置环境变量。

```
export TWITTER_CONSUMER_KEY=*********************
export TWITTER_CONSUMER_SECRET=******************************************
export TWITTER_ACCESS_TOKEN=**************************************************
export TWITTER_ACCESS_SECRET=******************************************
```

## 步骤4：从微博中提取文章正文

这是该应用的主要部分。现在，我们已经有了对于已赞tweet的处理，我们必须从中抽取内容。我们将使用`newspaper`库来为我们执行文章抽取。

```python
from __future__ import absolute_import, print_function
from tweepy.streaming import StreamListener
from tweepy import OAuthHandler
from tweepy import Stream
import os

consumer_key=os.getenv("TWITTER_CONSUMER_KEY")
consumer_secret=os.getenv("TWITTER_CONSUMER_SECRET")
access_token=os.getenv("TWITTER_ACCESS_TOKEN")
access_token_secret=os.getenv("TWITTER_ACCESS_SECRET")

articles = []

class LikedTweetsListener(StreamListener):
    def on_data(self, data):
        tweet = json.loads(data)
        if 'event' in tweet and tweet['event'] == "favorite":
            liked_tweet = tweet["target_object"]
            liked_tweet_text = liked_tweet["text"]
            story_url = extract_url(liked_tweet)
            if story_url:
                article = extract_article(story_url)
                if article:
                    article['story_url'] = story_url
                    article['liked_on'] = time.time()
                    articles.append(article)
        return True

    def on_error(self, status):
        print("Error status received : {0}".format(status))


def extract_url(liked_tweet):
    url_entities = liked_tweet["entities"]["urls"]
    if url_entities and len(url_entities) > 0:
        return url_entities[0]['expanded_url']
    else:
        return None    


from newspaper import Article

def extract_article(story_url):
    article = Article(story_url)
    article.download()
    article.parse()
    title = article.title
    img = article.top_image
    publish_date = article.publish_date
    text = article.text.split('\n\n')[0] if article.text else ""
    return {
        'title':title,
        'img':img,
        'publish_date':publish_date,
        'text':text.encode('ascii','ignore')
    }
```

上面显示的`extract_article`方法使用newspaper库完成所有重要工作。要使用它，首先从`newspaper`模块导入`Article`类，然后，首先使用`url`进行实例化，接着调用`download`和`parse`方法，从而构建article。`download`方法下载页面内容，而`parse`方法从页面中抽取相关信息。最后，我们创建了一个带有所有相关信息的`dict`对象，然后返回它。

## 步骤5：使用Flask渲染文章

现在，我们将构建一个简单的web应用，它会渲染这些文章。这也将位于`app.py`文件内。

```python
from flask import Flask, render_template

app = Flask(__name__)


@app.route("/")
def index():
    return render_template("index.html", articles=sorted(articles, key=lambda article: article["liked_on"], reverse=True))

if __name__ == '__main__':
    l = LikedTweetsListener()
    auth = OAuthHandler(consumer_key, consumer_secret)
    auth.set_access_token(access_token, access_token_secret)

    stream = Stream(auth, l)
    stream.userstream(async=True)

    app.run(debug=True)    
```


当请求是到`\`时，我们将渲染`index.html`。`index.html`将使用我们在前面步骤中填充的`articles`。

对于样式，我使用了一个[免费的Twitter bootstrap主题](http://startbootstrap.com/template-overviews/1-col-portfolio/)。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="description" content="">
    <meta name="author" content="">
    <title>Daily Reads</title>
    <link href="{{ url_for('static', filename='css/bootstrap.min.css') }}" rel="stylesheet">
    <link href="{{ url_for('static', filename='css/1-col-portfolio.css') }}" rel="stylesheet">
</head>

<body>
    <div class="container">

        {% for article in articles %}
            <div class="row">
            <div class="col-md-7">
                <a href="#">
                    <img class="img-responsive" src="{{ article.img }}" alt="">
                </a>
            </div>
            <div class="col-md-5">
                <h3>{{ article.title }}</h3>
                <p>{{ article.text.decode("utf-8") }}</p>
                <a class="btn btn-primary" href="{{ article.story_url }}" target="_blank">Read Full Article <span class="glyphicon glyphicon-chevron-right"></span></a>
            </div>
        </div>
        <hr><hr>
        {% endfor %}

    </div>
</body>

</html>
```

-----

本周就是这样啦。

你可以在[https://github.com/shekhargulati/52-technologies-in-2016/issues/20](https://github.com/shekhargulati/52-technologies-in-2016/issues/20)上面发表评论，以提供你宝贵的意见。

[![Analytics](https://ga-beacon.appspot.com/UA-59411913-2/shekhargulati/52-technologies-in-2016/16-newspaper)](https://github.com/igrigorik/ga-beacon)