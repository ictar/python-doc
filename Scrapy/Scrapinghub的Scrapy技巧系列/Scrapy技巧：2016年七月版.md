原文：[Scrapy Tips from the Pros: July 2016](https://blog.scrapinghub.com/2016/07/20/scrapy-tips-from-the-pros-july-2016/)

---

[Scrapy](http://scrapy.org)被设计成可扩展，并且组件之间松耦合。你可以轻松地使用自己的中间件或者pipeline扩展Scrapy的功能。

这使得Scrapy社区可以很容易地开发新的插件来改善现有功能，而不需改变Scrapy自身。

在这篇文章中，我们将向你展示如何利用DeltaFetch插件来进行增量爬取。

## 使用Deltafetch进行增量爬取

我们开发的一些爬虫设计成一次性爬取并抓取我们所需的数据。另一方面，许多爬虫需要周期性地爬取，以便让我们的数据集保持最新。

在这些周期爬虫中，我们只对最后一次爬取的最新页面感兴趣。例如，我们有一个从一堆网络媒体网点爬取文章的爬虫。该爬虫一天执行一次，并且它们首先从预定义的首页检索文章URL。然后，从每篇文章上提取标题、作者、日期和内容。这种方法通常会导致许多重复结果，并且使得每次我们运行爬虫时，爬取的数量越来越多。

幸运的是，我们并不是第一个有这个问题的人。社区已经有了解决方法：[scrapy-deltafetch插件](https://github.com/scrapy-plugins/scrapy-deltafetch)。你可以用这个插件进行增量爬取。DeltaFetch的主要目的是避免请求那些之前已经爬过的页面，即使它在之前的执行中已经出现了。它只会对那些之前没有提取任何项的页面、爬虫的`start_urls`属性中的URL、或者在爬虫的`start_requests`方法中生成的Request进行请求。

DeltaFetch的工作原理是，对爬虫回调中生成的每一个Item和Request对象进行拦截。对于Item，它计算相关的Request标识符(又名，[指纹(fingerprint)](https://github.com/scrapy/scrapy/blob/master/scrapy/utils/request.py#L19))，并将其存储到一个本地数据库中。对于Request，Deltafetch计算Request fingerprint，并在在其已存在数据库的时候丢弃该Request。

现在，看看如何为你的Scrapy爬虫设置Deltafetch。

### 开始使用DeltaFetch

首先，用pip安装DeltaFetch：

```python

    $ pip install scrapy-deltafetch
```

然后，你必须在你的项目的settings.py文件中启用它：

```python

    SPIDER_MIDDLEWARES = {
        'scrapy_deltafetch.DeltaFetch': 100,
    }
    DELTAFETCH_ENABLED = True
    
```

### 使用DeltaFetch

[这个爬虫](https://github.com/stummjr/books_crawler/)有一个爬取[books.toscrape.com](http://books.toscrape.com)的蜘蛛。它通过所有列出的页面进行导航，访问每本书的详细页面，获取一些数据，例如书标题、描述和目录。该爬虫每天执行一次，以捕获对应目录中包含的新书。无需访问那些已经爬过的书页面，因为由爬虫收集的数据通常不会改变。

想看看Deltafetch的使用，[clone这个repo](https://github.com/stummjr/books_crawler/)，其中，已经在settings.py启用了DeltaFetch，然后运行：

```python

    $ scrapy crawl toscrape
```

等它结束，然后看看Scrapy在最后记录的统计数据：

```python

    2016-07-19 10:17:53 [scrapy] INFO: Dumping Scrapy stats:
    {
        'deltafetch/stored': 1000,
        ...
        'downloader/request_count': 1051,
        ...
        'item_scraped_count': 1000,
    }
```

除此之外，你会看到爬虫进行了1051次请求来爬取1000个项，而DeltaFetch存储了1000个请求的fingerprint。这意味着，只有51个页面请求没有生成item，因此下次还会继续访问他们。

现在，再次运行该爬虫，你会看到许多像这样的日志信息：

```python

    2016-07-19 10:47:10 [toscrape] INFO: Ignoring already visited: 
    <GET http://books.toscrape.com/....../index.html>
```

而在统计数据中，你会看到，跳过了1000个请求，因为在之前的爬取中，已经爬到了item。现在，该爬虫并未提取任何item，并且它只进行了51次请求，它们所有都是之前没有爬取到item的页面：

```python

    2016-07-19 10:47:10 [scrapy] INFO: Dumping Scrapy stats:
    {
        'deltafetch/skipped': 1000,
        ...
        'downloader/request_count': 51,
    }
```

### 修改数据库键

默认情况下，DeltaFetch使用一个Request fingerprint来区分Request。该fingerprint是一个基于规范URL、HTTP方法和请求体计算的哈希值。

一些网站对于相同的数据会有多个URL。例如，一个电子商务网站可能有指向同一个产品的URL，如下所示：

  * <http://www.example.com/product?id=123>
  * <http://www.example.com/deals?id=123>
  * <http://www.example.com/category/keyboards?id=123>
  * <http://www.example.com/category/gaming?id=123>

在这些情况下，Request fingerprint并不适用，因为规范的URL将会不同，即使item是相同的。在这个例子中，我们可以使用产品的ID作为DeltaFetch键。

DeltaFetch允许我们在初始化Request时，通过传递一个名为`deltafetch_key`的元参数来自定义键：

```python

    from w3lib.url import url_query_parameter
    
    ...
    
    def parse(self, response):
        ...
        for product_url in response.css('a.product_listing'):
            yield Request(
                product_url,
                meta={'deltafetch_key': url_query_parameter(product_url, 'id')},
                callback=self.parse_product_page
            )
        ...
    
```

通过这种方式，DeltaFetch将会忽略对重复页面进行请求，即使它们有不同的URL。

### 重置DeltaFetch

如果你想要重新爬取页面，可以通过传递一个`deltafetch_reset`参数给你的爬虫，来重置DeltaFetch缓存：

```python

    $ scrapy crawl example -a deltafetch_reset=1
```

### 在Scrapy Cloud上使用DeltaFetch

你也可以对运行在[Scrapy Cloud](https://app.scrapinghub.com/account/signup/)之上的爬虫使用DeltaFetch。仅需在你项目的Addons页面启用DeltaFetch和DotScrapy Persistence插件。后者是用来允许你的爬虫访问.scrapy文件夹，该文件夹是DeltaFetch存储其数据库的地方。

![image00](https://scrapinghub.files.wordpress.com/2016/07/image00.png?w=648)

Deltafetch在我们已经看到的那些情况下是非常方便的。**请记住，Deltafetch只是避免了发送请求到之前已经生成了item的页面，并且仅当这些请求尚未由爬虫的start_urls或者start_requests生成。**那些来自于没有直接爬取到item的页面，在每一次你运行你的爬虫的时候，将仍会抓取。

你可以看看github上该项目页以获取更多信息：<http://github.com/scrapy-plugins/scrapy-deltafetch>


## 总结一下

你可以在Github的[scrapy-plugins](https://github.com/scrapy-plugins)页面上找到许多有趣的Scrapy插件，你还可以在那里包含自己的插件来回馈社区。

如果你有问题，或者你想在这个每月专栏上看到某个主题，请在这里（Ele注，到原文留言哈）留下评论，让我们知道，或者通过在Twitter上[@scrapinghub](http://twitter.com/scrapinghub)来找到我们。
