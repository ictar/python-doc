原文：[Scrapy Tips from the Pros May 2016 Edition](https://blog.scrapinghub.com/2016/05/18/scrapy-tips-from-the-pros-may-2016-edition/)

---

欢迎来到Scrapy技巧系列！每个月，我们会发布一些技巧和hack，来帮助你加快网页抓取和数据提取。作为牵头的Scrapy维护者，你可以想象的任何障碍我们都遇到过了，所以别担心，你能在这获益良多。随意到[Twitter](https://twitter.com/ScrapingHub)或者[Facebook](https://www.facebook.com/ScrapingHub/)访问我们，提出对未来主题的建议吧。

![Scrapy Tips](https://scrapinghub.files.wordpress.com/2016/05/scrapy-tips.png?w=648)

# 如何调试你的爬虫

你的爬虫不工作了，但是你想不明白为啥。一个快速识别潜在问题的方法是添加一些打印语句，以找出发生了什么。这通常是我的第一个步骤，而有时我所需要做的是发现那些妨碍我的爬虫正常运行的错误。如果这个方法对你有效，那就太棒了，但是如果这个方法还不够，那么读下去，学学如何处理那些需要更加彻底调查的令人讨厌的bug。在这篇文章中，我将向你介绍一些工具，当涉及到调试爬虫时，它们应该在每个Scrapy用户的工作区中。

## Scrapy Shell是你的好基友

Scrapy shell是一个全功能的Python shell，它加载了与你在你的爬虫的回调方法中得到的上下文相同的上下文。你只需要提供一个URL，Scrapy Shell就会让你与那个你的爬虫在它的回调中处理的相同的对象进行交互，包括response对象。

```python

    $ scrapy shell http://blog.scrapinghub.com
    [s] Available Scrapy objects:
    [s]   crawler    <scrapy.crawler.Crawler object at 0x7f0638a2cbd0>
    [s]   item       {}
    [s]   request    <GET http://blog.scrapinghub.com>
    [s]   response   <200 https://blog.scrapinghub.com/>
    [s]   settings   <scrapy.settings.Settings object at 0x7f0638a2cb50>
    [s]   spider     <DefaultSpider 'default' at 0x7f06371f3290>
    [s] Useful shortcuts:
    [s]   shelp()           Shell help (print this help)
    [s]   fetch(req_or_url) Fetch request (or URL) and update local objects
    [s]   view(response)    View response in a browser
    >>>
    
```

加载它之后，你可以开始玩玩response，以构建选择器来提取所需的数据。

```python

    >>> response.css("div.post-header > h2 ::text").extract()
    ...
    
```

如果你不熟悉Scrapy Shell，那么不妨试一试。它与你的开发工作流程可以完美契合，它位于在浏览器中进行页面检查的动作之后。你可以创建并测试爬虫的抽取规则，而一旦你构建了所需的规则，就可以在爬虫代码中使用它们。

通过官方文档，了解更多关于[Scrapy Shell的细节](http://doc.scrapy.org/en/latest/topics/shell.html)。

### 从你的Spider代码中启动Scrapy Shell

如果对于某些响应，你的爬虫表现异常，那么在爬虫代码中使用`scrapy.shell.inspect_response`方法，你可以很快地看到发生了什么事。这将打开一个Scrapy shell会话，以让你与当前的response对象进行交互。

例如，假设你的爬虫不从某些页面中提取所期望数量的项，而你想要看看网站返回的响应有啥问题：

```python

    
    from scrapy.shell import inspect_response
    
    def BlogSpider(scrapy.Spider)
        ...
        def parse(self, response):
            if len(response.css('div.post-header > h2 ::text')) > EXPECTED:
                # generate the items
            else:
                inspect_response(response, self)
            ...
    
```

一旦执行这个inspect_response调用，Scrapy Shell就会被打开，而你就能与response进行交互，从而看看发生了啥事。

## 快速绑定一个调试器到你的Spider

另一个调试爬虫的方法是使用常规的Python调试器，例如pdb或者PuDB。我使用[PuDB](https://pypi.python.org/pypi/pudb)，因为它是一个相当强大且易于使用的调试器，而要激活它，我所需要的只是将这行代码放在我想要断点的那一行：

```python

    import pudb; pudb.set_trace()
```

当到达断点的时候，PuDB在你的终端中打开一个很酷的文本模式的用户界面，它将带你回到使用Turbo Pascal调试器的那些美好的旧时光。

看一看：![image00](https://scrapinghub.files.wordpress.com/2016/05/image00
1.png?w=648)

你可以使用pip安装PuDB：

```python

    $ pip install pudb
```

看看这个视频，其中，我们自己的[@eliasdorneles](https://twitter.com/eliasdorneles)演示了使用PuDB的几个小技巧：<https://vimeo.com/166584837>

## Scrapy解析CLI命令

有些情况下，你需要你的爬虫很长一段时间运行某些爬取项目。但是，在运行了几个小时后，你可能会悲催地在日志中看到，对于一些特​​定的URL，爬虫之一有爬取问题。你想要调试爬虫，但你肯定不希望再运行整个抓取过程，并且要等到为该特定的URL调用的具体的回调，这样你就可以启动你的调试器。

别担心，Scrapy CLI的[parse命令](http://doc.scrapy.org/en/latest/topics/commands.html#std:command-parse)就是为了让你节约时间的！你只需要提供该爬虫的名字，应该使用的爬虫的回调，以及你想要解析的URL：

```python

    $ scrapy parse https://blog.scrapinghub.com/comments/bla --spider blog -c parse_comments
```

在这种情况下，Scrapy将会调用blog爬虫的parse_comments方法来解析blog.scrapinghub.com/comments/bla URL。如果你不指定爬虫，那么Scrapy将会在你的项目中，基于爬虫的allowed_domains设置，搜寻能够处理这个URL的爬虫。

然后，它将会向你显示回调的执行摘要：

```python

    >>> STATUS DEPTH LEVEL 1 <<<
    # Scraped Items  ------------------------------------------------------------
    [{'comments': [
        {'content': u"I've seen this language ...",
         'username': u'forthemostpart'},
        {'content': u"It's a ...",
         'username': u'YellowAfterlife'},
        ...
        {'content': u"There is a macro for ...",
        'username': u'mrcdk'}]}]
    # Requests  -----------------------------------------------------------------
    []
    
```

你也可以在方法里面附加一个调试器，以帮助你弄清楚发生了什么（见前面的提示）。

## Scrapy fetch和view命令

在浏览器中检查页面内容可能会被欺骗，因为它们的JavaScript引擎可能渲染某些Scrapy下载器不会做的内容。如果你想快速检查当一个页面被Scrapy下载后，该页面会看起来是什么样的，那么你可以使用下面这些命令：

  * **fetch**: 使用Scrapy下载器下载HTML，然后打印到标准输出。
  * **view**: 使用Scrapy下载器下载HTML，然后用你的默认浏览器打开它。

**例如**:

```ssh

    $ scrapy fetch http://blog.scrapinghub.com > blog.html
    $ scrapy view http://scrapy.org
```

## 使用--pdb选项，对爬虫进行事后剖析侦错

编写防故障软件几乎是不可能的。这种情况对于网络爬虫更加糟糕，因为它们处理的网页内容是经常变化的（和损坏的）。最好接受我们的爬虫最后将会失败，并确保我们有工具来快速了解为什么它挂了，并能尽快解决这个问题。

Python的回溯是棒棒哒，但在某些情况下，它们不向我们提供关于在我们的代码中发生了什么的足够信息。这就是事后剖析侦错的用武之地。Scrapy提供了-- `--pdb`命令行选项，它在你的爬虫挂掉的地方打开一个pdb会话，这样你就可以检查它的上下文，从而明白发生了什么：

```ssh

    $ scrapy crawl blog -o blog_items.jl --pdb
```

如果你的爬虫由于致命异常而挂了，那么pdb调试器将会打开，这样你就可以仔细检查其死因。

# 总结

好啦，这就是Scrapy技巧的五月版。在[Scrapy官方文档](http://scrapy.readthedocs.io/en/latest/topics/debug.html #debugging-spiders)，你也可以看到其中一些调试技巧。

因为这里，我们是要帮助你更有效地爬取网页的，所以[请让我们知道](https://twitter.com/ScrapingHub)你希望在将来看到什么。那就下个月再见啦！

