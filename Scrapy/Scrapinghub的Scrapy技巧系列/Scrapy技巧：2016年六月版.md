原文：[Scrapy Tips from the Pros June 2016](https://blog.scrapinghub.com/2016/06/22/scrapy-tips-from-the-pros-june-2016/)

---

欢迎来到Scrapy技巧系列！每个月，我们会发布一些技巧和hack，来帮助你加快网页抓取和数据提取。作为牵头的Scrapy维护者，你可以想象的任何障碍我们都遇到过了，所以别担心，你能在这获益良多。随意到[Twitter](https://twitter.com/ScrapingHub)或者[Facebook](https://www.facebook.com/ScrapingHub/)访问我们，提出对未来主题的建议吧。

![Scrapy Tips](https://scrapinghub.files.wordpress.com/2016/05/scrapy-tips.png?w=648)

## 抓取无限滚动页面

在单页应用以及每页具有大量AJAX请求的时代，很多网站已经用花哨的无限滚动机制取代了“前一个/下一个”分页按钮。使用这种技术的网站每当用户滚动到页面的底部的时候加载新项（想想微博，Facebook，谷歌图片）。虽然[UX专家](https://www.smashingmagazine.com/2013/05/infinite-scrolling-lets-get-to-the-bottom-of-this/)认为，无限滚动为用户提供了海量数据，但是我们看到越来越多的web页面诉诸于展示这种无休止的结果列表。

在开发web爬虫时，我们要做的第一件事就是找到能将我们引导到下一页结果的带有链接的UI组件。不幸的是，这些链接在无限滚动页面上不存在。

虽然这种场景可能看起来像诸如[Splash](http://scrapinghub.com/splash/)或者[Selenium](http://www.seleniumhq.org/)这样的JavaScript引擎的一个经典案例，但是它实际上是一个简单的修复。你所需要做的是在你滚动目标页面的时候检查浏览器的AJAX请求，然后在Scrapy spider中重新创建这些请求，而不是模拟用于与此类引擎的交互。

让我们以[Spidy Quotes](http://spidyquotes.herokuapp.com/scroll)为例，构建一个爬虫来获取上面列出来的所有的项。

## 审查页面

先说重要的事，我们需要理解无限滚动是如何在这个页面工作的，我们可以通过[浏览器的开发者工具](https://developer.chrome.com/devtools#access)中的Network面板来完成此项工作。打开该面板，然后滚动页面，看看浏览器发送了什么请求：

![scrapy tips from the pros june](https://scrapinghub.files.wordpress.com/2016/06/scrapy-tips-from-the-pros-june.png?w=648)

点开一个请求仔细看看。浏览器发送了一个请求到`/api/quotes?page=x`，然后接收诸如以下的一个JSON对象作为响应：

```python

    {
       "has_next":true,
       "page":8,
       "quotes":[
          {
             "author":{
                "goodreads_link":"/author/show/1244.Mark_Twain",
                "name":"Mark Twain"
             },
             "tags":["individuality", "majority", "minority", "wisdom"],
             "text":"Whenever you find yourself on the side of the ..."
          },
          {
             "author":{
                "goodreads_link":"/author/show/1244.Mark_Twain",
                "name":"Mark Twain"
             },
             "tags":["books", "contentment", "friends"],
             "text":"Good friends, good books, and a sleepy ..."
          }
       ],
       "tag":null,
       "top_ten_tags":[["love", 49], ["inspirational", 43], ...]
    }
    
```

这就是我们的爬虫需要的信息了。它所需要做的仅是生成到"/api/quotes?page=x"的请求，其中，`x`的值不断增加，直到`has_next`字段为false。这样做最棒的是，我们甚至无需爬取HTML内容以获取所需数据。这些数据都在一个漂亮的机器可读的JSON中。

## 构建Spider

下面是我们的spider。它从服务器返回的JSON内容提取目标数据。这种方法比挖掘页面的HTML树更容易并且更健壮，相信布局的改变不会搞挂我们的spider。

```python

    import json
    import scrapy
    
    
    class SpidyQuotesSpider(scrapy.Spider):
        name = 'spidyquotes'
        quotes_base_url = 'http://spidyquotes.herokuapp.com/api/quotes?page=%s'
        start_urls = [quotes_base_url % 1]
        download_delay = 1.5
    
        def parse(self, response):
            data = json.loads(response.body)
            for item in data.get('quotes', []):
                yield {
                    'text': item.get('text'),
                    'author': item.get('author', {}).get('name'),
                    'tags': item.get('tags'),
                }
            if data['has_next']:
                next_page = data['page'] + 1
                yield scrapy.Request(self.quotes_base_url % next_page)
    
```

要进一步联系这个技巧，你可以做个实验，爬取我们的博客，因为它也是使用无限滚动来加载旧博文的。

## 总结

如果你被爬取无限滚动网站的前景吓到，那么希望现在你可以有点信心了。下一次你需要处理那种基于用户操作引发的AJAX调用的页面时，看一看你的浏览器发送的请求吧，然后将其重放到你的spider中。响应往往是JSON的格式，这使得你的spider甚至更简单了。

好啦，这就是六月份的！请在[Twitter](https://twitter.com/ScrapingHub)上联系我们，让我们知道未来你希望看到什么技巧。最近，我们还发布了一个[数据集目录](https://blog.scrapinghub.com/2016/06/09/introducing-the-new-open-data-catalog/)，所以，如果你还苦思要爬取什么，那么看看这个目录获取一些灵感吧。