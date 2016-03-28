原文：[Scrapy Tips from the Pros: March 2016 Edition](https://blog.scrapinghub.com/2016/03/23/scrapy-tips-from-the-pros-march-2016-edition/)

---


![Scrapy-Tips-March-2016](https://scrapinghub.files.wordpress.com/2016/03/scrapy-tips-march-2016.png?w=648)

欢迎来到三月份版本的[Scrapy技巧](https://blog.scrapinghub.com/category/scrapy-tips-from-the-pros/)! 每个月，我们都会发布一些我们开发的技巧和hack，来帮助你，使得你的Scrapy工作流更顺畅。

这个月，我们将涵盖如何和CookiesMiddleware一起使用[cookiejar](http://doc.scrapy.org/en/latest/topics/downloader-middleware.html#std:reqmeta-cookiejar)来绕过那些不允许你使用相同的cookie同时爬取多个页面的网站。我们还将分享一个一个好用的技巧，这个技巧关于如何和[item loader](http://doc.scrapy.org/en/latest/topics/loaders.html)一起使用多个备用的XPath/CSS表达式，来从网站上更可靠地获取数据。

**学生请阅读以下：我们正参与[2016年Google编程之夏](https://blog.scrapinghub.com/2016/03/14/join-scrapinghub-for-google-summer-of-code-2016/)，而我们一部分的项目点子使用了Scrapy! 如果你感兴趣，那么看一看[我们的点子](http://gsoc2016.scrapinghub.com/ideas/)，并记得[在3.25，也就是周五之前申请](https://wiki.python.org/moin/SummerOfCode/2016#How_do_I_Apply.3F)!**

**如果你不是学生，那么请与你的学生朋友分享。他们会获得一份夏天津贴，甚至最后我们可能聘用他们。**

# 使用CookieJar解决站点怪异会话行为

那些将你的UI状态存储在自己的服务器的会话中的网站是难以导航的，更别说抓取。你有没有遇到过那些在同一个网站上打开的一个选项卡会影响其他选项卡的网站？那么，你可能会碰到这个问题。

虽然这是令人沮丧的，它甚至对于网络爬虫更糟糕。它会严重阻碍网络爬虫会话。不幸的是，这是ASP.Net和基于J2EE的网站的通用模式。而这正是[cookiejars](http://doc.scrapy.org/en/latest/topics/downloader-middleware.html#std:reqmeta-cookiejar)的用处所在。虽然不是经常需要cookiejar，但是对于那些意想不到的情况，你会很高兴拥有它。

当你的爬取一个网站时，Scrapy自动为你处理cookie，存储并在随后的请求到将其发送到同一站点。但是，正如你可能知道的，Scrapy请求是异步的。这意味着，你可能有发到相同的网站上的多个请求被同时处理，同时共享相同的cookie。为避免在爬取这些类型的网站时，请求相互影响，你必须为不同的请求设置不同的Cookie。

您可以通过使用一个[cookiejar](http://doc.scrapy.org/en/latest/topics/downloader-middleware.html#std:reqmeta-cookiejar)为同一网站中的不同页面存储单独的cookie来做到这点。该cookiejar只是在Scrapy爬取会话期间保持的一个cookie键值集合。你只需要为每个你想要存储的cookie定义一个唯一标识符，然后当你想要使用特定的cookie时，使用它的标识符。

例如，假设你想抓取一个网站上的多个类别，但这个网站存储与你在服务器会话中爬行/浏览的类别相关的数据。要同时爬取这些类别，则需要通过将类别名称作为cookiejar元参数的标识符来为每个类别创建一个cookie：
```py
class ExampleSpider(scrapy.Spider):
    urls = [
        'http://www.example.com/category/photo',
        'http://www.example.com/category/videogames',
        'http://www.example.com/category/tablets'
    ]

    def start_requests(self):
        for url in urls:
            category = url.split('/')[-1]
            yield scrapy.Request(url, meta={'cookiejar': category})
```

在此情况下，将管理三种不同的Cookie（‘photo’, ‘videogames’ 和‘tablets’）。每当你传递一个不存在的键作为cookiejar元值（例如，当一个类别名称尚未访问）时，你可以创建一个新的Cookie。当我们传递的键已经存在时，[Scrapy](http://scrapy.org/)使用该请求相应的cookie。

所以，例如，如果你想重新使用已被用来抓取‘videogames’页面的cookie，那么你只需要将‘videogames’作为唯一键传递给cookiejar。它将使用先用的cookie，而不是创建一个新的cookie：
```py
yield scrapy.Request('http://www.example.com/atari2600', meta={'cookiejar': 'videogames'})
```

# 添加备用的CSS/XPath规则

当你需要完成比简单地填充字典或带有你的spider收集的数据的Item对象更多的东西时，[Item Loader](http://doc.scrapy.org/en/latest/topics/loaders.html)是有用的。例如，你可能需要将一些后处理逻辑添加到你刚刚收集的数据中。你可能对某些如将标题中的每个单词首字母大写一样简单的事，甚至是更复杂的操作有兴趣。使用ItemLoader，你可以从spider中解耦这种后处理逻辑，以便拥有一个更易于维护的设计。

这个技巧说明如何将额外的功能添加到一个Item Loader中。比方说，你正爬取Amazon.com，并且提取每个产品的价格。你可以使用Item Loader来为ProductItem对象填充产品数据：

```py
class ProductItem(scrapy.Item):
    name = scrapy.Field()
    url = scrapy.Field()
    price = scrapy.Field()


class AmazonSpider(scrapy.Spider):
    name = "amazon"
    allowed_domains = ["amazon.com"]

    def start_requests(self):
        ...

    def parse_product(self, response):
        loader = ItemLoader(item=ProductItem(), response=response)
        loader.add_css('price', '#priceblock_ourprice ::text')
        loader.add_css('name', '#productTitle ::text')
        loader.add_value('url', response.url)
        yield loader.load_item()
```

这种方法工作得很好，除非被爬取的产品是一次交易。这是因为对比那些普通的价格，Amazon以一种稍微不同的格式展示交易价格。而普通产品的价格是这样表示的：
```html
<span id="priceblock_ourprice" class="a-size-medium a-color-price">
    $699.99
</span>
```

交易价格显示稍微有点不同：
```html
<span id="priceblock_dealprice" class="a-size-medium a-color-price">
    $649.99
</span>
```

要处理这种情况的一个好方法是，为Item loader中的价格字段添加一个后备规则。这是一个只有当该字段的前一规则已经失败时才应用的规则。要用Item Loader做到这一点，你可以添加一个`add_fallback_css`方法：
```py
class AmazonItemLoader(ItemLoader):
    default_output_processor = TakeFirst()

    def get_collected_values(self, field_name):
        return (self._values[field_name]
                if field_name in self._values
                else self._values.default_factory())

    def add_fallback_css(self, field_name, css, *processors, **kw):
        if not any(self.get_collected_values(field_name)):
            self.add_css(field_name, css, *processors, **kw)
```

正如你所看到的， 如果对于该字段，没有之前收集到的值，那么`add_fallback_css`方法将使用CSS规则。现在，我们可以改变我们的spider来使用AmazonItemLoader，然后添加后备CSS规则到我们的loader中：
```py
def parse_product(self, response):
    loader = AmazonItemLoader(item=ProductItem(), response=response)
    loader.add_css('price', '#priceblock_ourprice ::text')
    loader.add_fallback_css('price', '#priceblock_dealprice ::text')
    loader.add_css('name', '#productTitle ::text')
    loader.add_value('url', response.url)
    yield loader.load_item()
```

这个技巧可以节省你的时间，让你的spider更健壮。如果有一个CSS规则无法获取数据，那么可以应用其他跪在来提取所需的数据。

如果Item Loader对于你来说是新玩意，那么[看看这个文档](http://doc.scrapy.org/en/latest/topics/loaders.html)。

# 总结

就是这样了！请跟我们分享你在网络抓取以及提取数据时碰到的任何问题。我们一直在寻找新的技巧和hack，并且在我们的每月专栏上分享我们的Scrapy技巧。在[Twitter](https://twitter.com/ScrapingHub)或者[Facebook ](https://www.facebook.com/ScrapingHub/)上联系我们，并且让我们知道我们是否帮到了你。

如果你还没有，试试[Portia](http://doc.scrapinghub.com/portia.html)，我们的开源可视化Web抓取工具。我们知道你喜欢[Scrapy](http://scrapy.org/)，但是体验从来就不是一种令人痛苦的事 ;)

请[申请加入我们的2016年Google编程之夏](http://gsoc2016.scrapinghub.com/ideas/)，截止如期是3.25，周五！

