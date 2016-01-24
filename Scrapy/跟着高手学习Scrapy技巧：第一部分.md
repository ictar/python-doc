原文：[跟着高手学习Scrapy技巧：第一部分](http://blog.scrapinghub.com/2016/01/19/scrapy-tips-from-the-pros-part-1/)

---

[Scrapy](http://scrapy.org)是[Scrapinghub](http://scrapinghub.com)的关键部分。我们广泛地采用此框架，并已积累了许多各种不同的快捷方法来解决常见问题。我们推出了一个系列来与大家分享这些Scrapy的技巧，这样，你就可以在你的日常工作流程中最有效的使用它。每一个博文将给出两到三个提示，敬请关注。

![scrapylogo](https://scrapinghub.files.wordpress.com/2016/01/scrapylogo.png?w=648)

## 使用Extruct从网站中提取微观数据(Microdata)

我相信网络爬虫的每一个开发者都会有理由来咒骂那些对他们的网站使用凌乱的布局的Web开发者。没有语义标记的网站，特别是那些基于HTML表格的网站，绝对是槽糕透顶。这些类型的网站使得爬取更加困难，因为几乎没有关于每一个元素代表什么的提示。有时候，你甚至不得不相信每个页面上的元素顺序将保持不变，从而抓取你需要的数据。

这就是为什么我们如此感激[Schema.org](https://schema.org/), 共同努力来使得语义标记在网页上。该项目为Web开发者提供了在他们的网站上展示一定范围的不同对象（包括Person, Product, 和Review）的架构，并使用例如[Microdata](http://www.w3.org/TR/microdata/), [RDFa](https://rdfa.info/), [JSON-LD](http://json-ld.org/)等的任何元数据格式。这使得搜索引擎工作更加容易，因为它们可以从网站上提取有用信息，而不必深入到他们所抓取网站的HTML结构中。

例如，[AggregateRating](https://schema.org/AggregateRating)是网上零售商用来展示他们产品的用户评级的架构。下面是描述一个使用[Microdata format](http://www.w3.org/TR/microdata/)的网上商店中的一个产品的用户评级的标记：
```html
<div itemprop="aggregateRating" itemscope="" itemtype="http://schema.org/AggregateRating">
    <meta itemprop="worstRating" content="1">
    <meta itemprop="bestRating" content="5">
    <div class="bbystars-small-yellow">
        <div class="fill" style="width: 88%"></div>
    </div>
    <span itemprop="ratingValue" aria-label="4.4 out of 5 stars">4.4</span>
    <meta itemprop="reviewCount" content="305733">
</div>
```

通过这种方式，搜索引擎可以在搜索结果中同时展示一个产品的评级及其URL，而不需要为每一个网站编写特定的爬虫：

![Example of a google search showing ratings for a product](https://scrapinghub.files.wordpress.com/2016/01/selection_096.png?w=648)

你还可以受益于一些网站使用的语义标记。我们推荐使用[Extruct](https://github.com/scrapinghub/extruct), 一个从HTML文档中中提取[嵌入式元数据](http://blog.scrapinghub.com/2014/06/18/extracting-schema-org-microdata-using-scrapy-selectors-and-xpath/)的库。它分析整个HTML并返回一个包含微观数据（microdata）的Python字典。看看我们是如何用它来提取展示用户评级的微观数据的：
```py
>>> from extruct.w3cmicrodata import MicrodataExtractor
>>> mde = MicrodataExtractor()
>>> data = mde.extract(html_content)
>>> data
{
  'items': [
    {
      'type': 'http://schema.org/AggregateRating',
      'properties': {
        'reviewCount': '305733',
        'bestRating': '5',
        'ratingValue': u'4.4',
        'worstRating': '1'
      }
    }
  ]
}
>>> data['items'][0]['properties']['ratingValue']
u'4.4'
```

现在，让我们建立一个使用Extruct的爬虫，它从[苹果产品网站](http://www.apple.com/shop/mac/mac-accessories)上提取价格和评级。 该网站使用了微观数据来存储所列出的产品信息。它为每个产品使用这个结构T：
```html
<div itemtype="http://schema.org/Product" itemscope="itemscope">
  <img src="/images/MLA02.jpg" itemprop="image" />
  <a href="/shop/product/MLA02/magic-mouse-2?" itemprop="url">
    <span itemprop="name">Magic Mouse 2</span>
  </a>
  <div class="as-pinwheel-info">
    <div itemprop="offers" itemtype="http://schema.org/Offer" itemscope="itemscope">
      <meta itemprop="priceCurrency" content="USD">
      <span class="as-pinwheel-pricecurrent" itemprop="price">
        $79.00
      </span>
    </div>
  </div>
</div>
```

有了这个设置，你不需要使用XPath或者CSS选择器来提取所需数据。你只需要在你的爬虫中使用Extruct的MicrodataExtractor：
```py
import scrapy
from extruct.w3cmicrodata import MicrodataExtractor

class AppleSpider(scrapy.Spider):
    name = "apple"
    allowed_domains = ["apple.com"]
    start_urls = (
        'http://www.apple.com/shop/mac/mac-accessories',
    )

    def parse(self, response):
        extractor = MicrodataExtractor()
        items = extractor.extract(response.body_as_unicode(), response.url)['items']
        for item in items:
            if item.get('properties', {}).get('name'):
                properties = item['properties']
                yield {
                    'name': properties['name'],
                    'price': properties['offers']['properties']['price'],
                    'url': properties['url']
                }
```

此爬虫会生成这样的项：
```py
{
    "url": "http://www.apple.com/shop/product/MJ2R2/magic-trackpad-2?fnode=4c",
    "price": u"$129.00",
    "name": u"Magic Trackpad 2"
}
```

所以，当你爬取的网站使用微观数据来将语义信息添加到它的内容中时，使用[Extruct](https://github.com/scrapinghub/extruct)。这是一个比依赖统一的页面布局或者浪费时间分析HTML源代码更健壮的解决方案。

## 使用js2xml抓取嵌入在JavaScript代码段中的数据

你是否曾经受挫于你的浏览器呈现的网页与Scrapy下载的网页之间的差距？这可能是因为该网页中的一些内容并不在服务器发送给你的响应中。相反，它们是由你的浏览器通过JavaScript代码生成的。

你可以通过将此请求传递给一个例如[Splash](http://scrapinghub.com/splash/)的JavaScript渲染服务来解决此问题。 Splash运行页面上的JavaScript，然后返回最终的页面结构以供你的爬虫使用。

Splash专门为此设计，并[与Scrapy很好的整合在一起](http://blog.scrapinghub.com/2015/03/02/handling-javascript-in-scrapy-with-splash/)。然而，在某些情况下，你需要的是一些简单功能，例如从一个JavaScript段中获取一个变量的值，所以使用这种强大的工具将大材小用。而这恰恰是[js2xml](https://github.com/redapple/js2xml)的用武之地。它是一个将JavaScript代码转换成XML数据的库。

例如，假设一个在线零售商网站通过JavaScript加载产品评级。混合在该HTML中有这样一段JavaScript代码：
```html
<script type="text/javascript">
    var totalReviewsValue = 32;
    var averageRating = 4.5;
    if(totalReviewsValue != 0){
        events = "...";
    }
    ...
</script>
```

要使用js2xml提取`averageRating`的值，我们首选需要提取`<script>`块，然后使用js2xml将此代码转换成XML：
```py
>>> js_code = response.xpath("//script[contains(., 'averageRating')]/text()").extract_first()
>>> import js2xml
>>> parsed_js = js2xml.parse(js_code)
>>> print js2xml.pretty_print(parsed_js)
<program>
  <var name="totalReviewsValue">
    <number value="32"/>
  </var>
  <var name="averageRating">
    <number value="4.5"/>
  </var>
  <if>
    <predicate>
      <binaryoperation operation="!=">
        <left><identifier name="totalReviewsValue"/></left>
        <right><number value="0"/></right>
      </binaryoperation>
    </predicate>
    <then>
      <block>
        <assign operator="=">
          <left><identifier name="events"/></left>
          <right><string>...</string></right>
        </assign>
      </block>
    </then>
  </if>
</program>
```

现在，只需要建立一个Scrapy的Selector，然后使用XPath获取我们想要的值：
```py
>>> js_sel = scrapy.Selector(_root=parsed_js)
>>> js_sel.xpath("//program/var[@name='averageRating']/number/@value").extract_first()
u'4.5'
```

虽然你可能用思考的速度就可以编写一个正则表达式来解决这个问题，但是，一个JavaScript解析器将会更可靠。我们这里使用的例子是非常简单的，但在一些更复杂的例子中，正则表达式可能更难以维护得多。

## 使用w3lib.url中的函数来从URL中抓取数据

有时候，你感兴趣的数据段并不单独在一个HTML标签内。通常，你需要从页面上列出的URL中获取一些参数的值。例如，你可能对获取在HTML中列出的URL中的‘username’的值感兴趣：
```html
<div class="users">
  <ul>
    <li><a href="/users?username=johndoe23">John Doe</li>
    <li><a href="/users?active=0&username=the_jan">Jan Roe</li>
     …
    <li><a href="/users?active=1&username=janie&ref=b1946ac9249&gas=_ga=1.234.567">Janie Doe</li>
  </ul>
</div>
```

也许你会试图使用[正则表达式的超能力](https://xkcd.com/208/), 但是，请淡定，这里的[w3lib](https://github.com/scrapy/w3lib)有一个更可靠的解决方案可以挽救局面：
```py
>>> from w3lib.url import url_query_parameter
>>> url_query_parameter('/users?active=0&username=the_jan', 'username')
    'the_jan'
```

假如你对[w3lib](https://github.com/scrapy/w3lib)感到陌生，那么看一看[文档](http://w3lib.readthedocs.org/en/latest/w3lib.html)。稍后，我们将在我们的“跟着高手学习Scrapy技巧”系列中覆盖此Python库的一些其他功能。
