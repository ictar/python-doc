原文：[Scrapy Tips from the Pros May 2016 Edition](https://blog.scrapinghub.com/2016/05/18/scrapy-tips-from-the-pros-may-2016-edition/)

---

Welcome to Scrapy Tips from the Pros! Every month we release a few tricks and
hacks to help speed up your web scraping and data extraction activities. As
the lead Scrapy maintainers, we have run into every obstacle you can imagine
so don’t worry, you’re in great hands. Feel free to reach out to us on
[Twitter](https://twitter.com/ScrapingHub) or
[Facebook](https://www.facebook.com/ScrapingHub/) with suggestions for future
topics.

![Scrapy Tips](https://scrapinghub.files.wordpress.com/2016/05/scrapy-tips.png?w=648)

# 如何调试你的爬虫

Your spider isn’t working and you have no idea why. One way to quickly spot
potential issues is to add a few print statements to find out what's
happening. This is often my first step and sometimes all I need to do to
uncover the bugs that are preventing my spider from running properly. If this
method works for you, great, but if it’s not enough, then read on to learn
about how to deal with the nastier bugs that require a more thorough
investigation. In this post, I’ll introduce you to the tools that should be in
the toolbelt of every Scrapy user when it comes to debugging spiders.

## Scrapy Shell是你的好基友

Scrapy shell is a full-featured Python shell loaded with the same context that
you would get in your spider callback methods. You just have to provide an URL
and Scrapy Shell will let you interact with the same objects that your spider
handles in its callbacks, including the response object.

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

After loading it, you can start playing around with the response in order to
build the selectors to extract the data that you need:

```python

    >>> response.css("div.post-header > h2 ::text").extract()
    ...
    
```

If you're not familiar with Scrapy Shell, give it a try. It's a perfect fit
for your development workflow, sitting right after the page inspection in the
browser. You can create and test your spider's extraction rules and use them
in your spider's code once you've built the ones you need.

Learn more about [Scrapy Shell through the official documentation](http://doc.scrapy.org/en/latest/topics/shell.html).

### 从你的Spider代码中启动Scrapy Shell

If your spider has been behaving unexpectedly for certain responses, you can
quickly see what's happening using the `scrapy.shell.inspect_response` method
in your spider code. This will open a Scrapy shell session that will let you
interact with the current response object.

For example, imagine that your spider is not extracting the expected amount of
items from certain pages and you want to see what's wrong with the response
returned by the website:

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

Once the execution hits the inspect_response call, Scrapy Shell is opened and
you can interact with the response to see what's happening.

## 快速绑定一个调试器到你的Spider

Another approach to debugging spiders is to use a regular Python debugger such
as pdb or PuDB. I use [PuDB](https://pypi.python.org/pypi/pudb) because it's
quite a powerful yet easy-to-use debugger and all I need to do to activate it
is to put this code in the line where I want a breakpoint:

```python

    import pudb; pudb.set_trace()
```

And when the breakpoint is reached, PuDB opens up a cool text-mode UI in your
terminal that will bring back fond memories from the old days of using the
Turbo Pascal debugger.

Take a look:![image00](https://scrapinghub.files.wordpress.com/2016/05/image00
1.png?w=648)

You can install PuDB using pip:

```python

    $ pip install pudb
```

Check out this video where our very own
[@eliasdorneles](https://twitter.com/eliasdorneles) demonstrates a few tips on
how to use PuDB: <https://vimeo.com/166584837>

## Scrapy解析CLI命令

There are certain scraping projects where you need your spiders to run for a
long time. However, after a few hours of running, you might sadly see in the
logs that one of your spiders had issues scraping specific URLs. You want to
debug the spider, but you certainly don’t want to run the whole crawling
process again and have to wait until that specific callback is called for that
specific URL so that you can start your debugger.

Don't worry, the [parse command](http://doc.scrapy.org/en/latest/topics/commands.html#std:command-parse) from Scrapy CLI is here to save the day! You just need to provide the
spider name, the callback from the spider that should be used and the URL that
you want to parse:

```python

    $ scrapy parse https://blog.scrapinghub.com/comments/bla --spider blog -c parse_comments
```

In this case, Scrapy is going to call the parse_comments method from the blog
spider to parse the blog.scrapinghub.com/comments/bla URL. If you don't
specify the spider, Scrapy will search for a spider capable of handling this
URL in your project based on the spiders' allowed_domains settings.

It will then show you a summary of your callback's execution:

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

You can also attach a debugger inside the method to help you figure out what's
happening (see the previous tip).

## Scrapy fetch and view commands

Inspecting page contents in browsers might be deceiving since their JavaScript
engine could render some content that the Scrapy downloader will not do. If
you want to quickly check exactly how a page will look when downloaded by
Scrapy, you can use these commands:

  * **fetch**: downloads the HTML using Scrapy Downloader and prints to stdout.
  * **view**: downloads the HTML using Scrapy Downloader and opens it with your default browser.

**Examples**:

```ssh

    $ scrapy fetch http://blog.scrapinghub.com > blog.html
    $ scrapy view http://scrapy.org
```

## Post-Mortem Debugging Over Spiders with --pdb Option

Writing fail-proof software is nearly impossible. This situation is worse for
web scrapers since they deal with web content that is frequently changing (and
breaking). It's better to accept that our spiders will eventually fail and to
make sure that we have the tools to quickly understand why it's broken and to
be able to fix it as soon as possible.

Python tracebacks are great, but in some cases they don't provide us with
enough information about what happened in our code. This is where post-mortem
debugging comes into play. Scrapy provides the `--pdb` command line option
that fires a pdb session right where your crawler has broken, so you can
inspect its context and understand what happened:

```ssh

    $ scrapy crawl blog -o blog_items.jl --pdb
```

If your spider dies due to a fatal exception, the pdb debugger will open and
you can thoroughly inspect its cause of death.

# 总结

And that’s it for the Scrapy Tips from the Pros May edition. Some of these
debugging tips are also available in [Scrapy official
documentation](http://scrapy.readthedocs.io/en/latest/topics/debug.html
#debugging-spiders).

[Please let us know](https://twitter.com/ScrapingHub) what you'd like to see
in the future since we're here to help you scrape the web more effectively.
We'll see you next month!

