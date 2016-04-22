原文；[Scrapy Tips from the Pros: April 2016 Edition](https://blog.scrapinghub.com/2016/04/20/scrapy-tips-from-the-pros-april-2016-edition/)

---

![Scrapy Tips](https://scrapinghub.files.wordpress.com/2016/04/scrapy-tips.png?w=648)

欢迎来到四月版本的[Scrapy技巧](https://blog.scrapinghub.com/category/scrapy-tips-from-the-pros/)。每个月我们都会发布一些我们发现的技巧和hack，以帮助你的Scrapy工作流更加顺利。

这个月，我们只给你带来了一个提示，但是，不是这样的哦！所以，如果你发现你在爬取一个需要通过表单提交数据的ASP.Net页面，那么，就回来看看这篇文章吧。

# 处理ASP.Net页面，PostBack和视图状态

使用ASP.Net技术构建的网站对于web爬虫开发者来说通常是一场噩梦，这主要是由于它们处理表单的方式。

这类网站通常在请求和响应中发送状态，以便跟踪客户端的UI状态。想想那些你浏览许多页面，在HTML表单中填写你的数据来注册的网站吧。一个ASP.Net网站通常存储那些在前一个页面填写的数据到一个名为“__VIEWSTATE”的隐藏字段中，这个字段包含了像下面显示的一个巨大的字符串：

[![ViewState example](https://scrapinghub.files.wordpress.com/2016/04/image032.png?w=648&amp;h=408)](https://scrapinghub.files.wordpress.com/2016/04/image032.png)

_我不是在开玩笑，它真的很大！ (有时是数十kB)_

这是一个Base64编码字符串，它表示客户端UI状态，包括来自表单的值。这在表单中的用户动作触发POST请求返回给服务器以获取其他字段的数据的web应用中，这种设置尤为常见。

每次浏览器向服务器发起POST请求时，就会带着这个__VIEWSTATE字段。然后，服务器根据该数据解码并加载客户端的UI状态，执行一些处理，基于新值为新的视图状态计算值，然后将这个新的视图状态作为隐藏字段渲染结果页面。

如果__VIEWSTATE没有发回给服务器，那么你可能会看到一个空白表单，因为服务器完全失去了客户端UI状态。所以，为了爬取像这样的根据表单生成的页面，你必须确保你的爬虫在它发送的请求中带有这个状态，否则，页面将不会加载它应该加载的内容。

这里有一个具体的例子，你可以亲眼看到如何处理这类情况。

# 抓取一个基于视图状态的网站

今天抓取的小白鼠是[spidyquotes.herokuapp.com/search.aspx](http://spidyquotes.herokuapp.com/search.aspx)。SpidyQuotes列出了来自名人的引言，而它的搜索页面允许你根据作者和标签过滤引言：

[![image05](https://scrapinghub.files.wordpress.com/2016/04/image052.png?w=300&amp;h=246)](https://scrapinghub.files.wordpress.com/2016/04/image052.png)

**Author**字段的改变触发了一个到服务器的POST请求，以使用与所选的用户相关的标签来填充**Tag**选择框。点击**Search**，显示与所选作者的标签相对应的引言：

[![image04](https://scrapinghub.files.wordpress.com/2016/04/image041.png?w=295&amp;h=300)](https://scrapinghub.files.wordpress.com/2016/04/image041.png)

为了爬取这些引言，我们的爬虫必须模拟用户选择一个作者，一个标签并提交表单。通过使用[Network Panel](https://developer.chrome.com/devtools)（你可以通过浏览器的开发者工具访问）来仔细看看这个流程的每一步。首先，访问[spidyquotes.herokuapp.com/search.aspx](http://spidyquotes.herokuapp.com/search.aspx)，然后按下F12或Ctrl+Shift+I (如果你使用的是Chrome)来加载工具，接着点击Network选项卡。

[![image00](https://scrapinghub.files.wordpress.com/2016/04/image001.png?w=648&amp;h=430)](https://scrapinghub.files.wordpress.com/2016/04/image001.png)

从列表中选择一个作者，然后你将看到生成了一个发往“/filter.aspx”的请求。点击资源名 (filter.aspx) ，你就可以看到请求细节，其中包括你选择的作者，以及在来自于服务器的原始响应中的__VIEWSTATE数据。

[![image02](https://scrapinghub.files.wordpress.com/2016/04/image022.png?w=648&amp;h=209)](https://scrapinghub.files.wordpress.com/2016/04/image022.png)

选择一个标签并点击Search。你会看到你的浏览器发送了在表单中选择的值，以及一个与前面不同的__VIEWSTATE值。这是因为，当你选择作者时，服务器包含了一些新的信息在视图状态中。

[![image01](https://scrapinghub.files.wordpress.com/2016/04/image011.png?w=648&amp;h=234)](https://scrapinghub.files.wordpress.com/2016/04/image011.png)

现在，你只需要构建一个爬虫，这个爬虫完成与你的浏览器做的事情。

# 构建爬虫

这里是你的爬虫应该遵循的步骤：

1.  抽取spidyquotes.herokuapp.com/filter.aspx
2.  对于每一个在表单作者列表中找到的**Author**：

    *   创建一个到/filter.aspx的POST请求，同时传递选择的**Author**和__VIEWSTATE值

3.  对于在结果页面中找到的每一个**Tag**：

    *   发送一个到/filter.aspx的POST请求，同时传递选择的**Author**，选择的**Tag**和视图状态

4.  抓取结果页面

### 爬虫编码

这里是我开发的从该网站抓取引言的爬虫，遵循了刚刚描述的步骤：
```py
import scrapy

class SpidyQuotesViewStateSpider(scrapy.Spider):
    name = 'spidyquotes-viewstate'
    start_urls = ['http://spidyquotes.herokuapp.com/search.aspx']
    download_delay = 1.5

    def parse(self, response):
        for author in response.css('select#author > option ::attr(value)').extract():
            yield scrapy.FormRequest(
                'http://spidyquotes.herokuapp.com/filter.aspx',
                formdata={
                    'author': author,
                    '__VIEWSTATE': response.css('input#__VIEWSTATE::attr(value)').extract_first()
                },
                callback=self.parse_tags
            )

    def parse_tags(self, response):
        for tag in response.css('select#tag > option ::attr(value)').extract():
            yield scrapy.FormRequest(
                'http://spidyquotes.herokuapp.com/filter.aspx',
                formdata={
                    'author': response.css(
                        'select#author > option[selected] ::attr(value)'
                    ).extract_first(),
                    'tag': tag,
                    '__VIEWSTATE': response.css('input#__VIEWSTATE::attr(value)').extract_first()
                },
                callback=self.parse_results,
            )

    def parse_results(self, response):
        for quote in response.css("div.quote"):
            yield {
                'quote': response.css('span.content ::text').extract_first(),
                'author': response.css('span.author ::text').extract_first(),
                'tag': response.css('span.tag ::text').extract_first(),
            }
```

**步骤1**由Scrapy完成，它读取start_urls，然后生成一个到/search.aspx的GET请求。

parse()方法负责**步骤2**。它遍历了在第一个选择框中找到的**Authors**，然后为每一个**Author**创建一个到/filter.aspx的[FormRequest](http://doc.scrapy.org/en/latest/topics/request-response.html#formrequest-objects)，模拟用户点击了列表中的每一个元素。值得注意的是，parse()方法从它所收到的表单中读取__VIEWSTATE字段，然后将其传回给服务器，所以服务器可以跟踪我们位于哪个页面流。

**步骤3**由parse_tags()方法来处理。它与parse()方法非常类似，因为它提取了所列的**Tags**，然后创建POST请求来传递每一个**Tag**，在前一个步骤中选择的**Author**以及从服务器收到的__VIEWSTATE。

最后，在**步骤4**中，parse_results()方法解析页面展示的引言列表，然后从中生成项。

### 使用FormRequest.from_response()简化你的爬虫

你也许注意到，在发送POST请求到服务器之前，我们的爬虫抽取了那些它从服务器收到的表单中的预填值，并在它将创建的请求中包含了这些值。

我们不需要对其手工编码，因为[Scrapy](http://scrapy.org/)提供了[FormRequest.from_response()](http://doc.scrapy.org/en/latest/topics/request-response.html#scrapy.http.FormRequest.from_response)方法。该方法读取response对象，创建一个`FormRequest`，它自动包含表单所有的预填值以及隐藏值。这是我们的爬虫的parse_tags()方法：
```py
def parse_tags(self, response):
    for tag in response.css('select#tag > option ::attr(value)').extract():
        yield scrapy.FormRequest.from_response(
            response,
            formdata={'tag': tag},
            callback=self.parse_results,
        )
```

所以，无论何时你处理包含隐藏值和预填值的表单，使用`from_response`方法，因为这样你的代码会看起来干净得多。

# 总结

好了，这就是这个月的技巧了。你可以[在这里读取更多关于ViewStates](http://msdn.microsoft.com/en-us/library/ms972976.aspx)的信息。我们希望你觉得这个技巧有用，并且很高兴看到你用它来做点什么。我们一直在寻找新的hack，所以如果你在爬取web的时候遇到了什么困难，请告诉我们。

随意在[Twitter](https://twitter.com/scrapinghub)或者[Facebook](https://www.facebook.com/ScrapingHub/)上告诉我们，你未来想看到什么吧。
