原文地址： [Python async/await Tutorial](http://stackabuse.com/python-async-await-tutorial/)

---

在过去的几年里，由于很好的原因，异步编程获得了大量的关注。虽然它比传统的线性编程更难，但是也比其有效得多。

例如，不是在继续执行前等待一个HTTP请求结束，而是在Python异步协程的帮助下，你可以提交请求，然后在等待HTTP请求完成的同时，执行其他等待在队列中的工作。为了保证逻辑正确，你可能需要多想一点，但是你也将可以使用更少的资源处理更多的工作。

即便如此，在一些语言中，例如Python，异步函数的语法和执行其实并不难。现在，JavaScript另一说，但是Python似乎执行得相当好。

异步性似乎是Node.js之所以如此受服务器端编程的一大原因。我们所编写的很多代码，特别是在有大量IO的应用（例如网站）中，依赖于外部资源。这可能是从一个远端数据库调用，到提交到一个REST服务的任何一项。一旦你请求这些资源，你的代码会等待，不做任何其他的操作。

使用异步编程，则允许你的代码在等待资源响应的时候处理其他任务。

## 协程（Coroutines）
Python中的异步函数通常被称为“协程”，它仅仅是一个使用`async`关键字，或者被`@asyncio.coroutine`装饰的函数。下面任一函数将作为一个协程进行工作，并在类型上实际上等价：
```python
import asyncio

async def ping_server(ip):  
    pass

@asyncio.coroutine
def load_file(path):  
    pass
```
这些特殊函数在调用时返回协程对象。如果你熟悉JavaScript Promise，那么你可以认为这个返回对象几乎像一个Promise。调用它们中任意一个实际上并不运行它们，而是返回一个[coroutine](https://docs.python.org/3/library/asyncio-task.html#coroutines)对象，这个对象会被传给事件循环，然后稍后执行。

如果你需要确定一个函数是否为一个协程，`asyncio`提供[asyncio.iscoroutine(obj)](https://docs.python.org/3/library/asyncio-task.html#asyncio.iscoroutine)方法可以用来确定。

## Yield from
有一些方法可以用来实际调用一个协程，其中一个是`yield from`方法。这个方法在Python 3.3引进，并在Python 3.5通过`async/await`的方式（稍后我们将提到）进一步改进。

`yield from`表达式可以像下面一样使用：
```python
import asyncio

@asyncio.coroutine
def get_json(client, url):  
    file_content = yield from load_file('/Users/scott/data.txt')
```
你可以看到，`yield from`在一个被`@asyncio.coroutine`装饰的函数中使用。如果你试图在这个函数外使用`yield from`，那么你会从Python那里得到如下的错误：
```python
  File "main.py", line 1
    file_content = yield from load_file('/Users/scott/data.txt')
                  ^
SyntaxError: 'yield' outside function  
```
为了使用这种语法，它必须在另一个函数（通常使用协程装饰器）中。

## Async/await
更新更简洁的语法是使用`async/await`关键字。在Python 3.5中引入，`async`被用来声明一个函数是协程，就像`@asyncio.coroutine`装饰器所做的一样。可以通过将它放置在函数定义的前面来应用它：
```python
async def ping_server(ip):  
    # ping code here...
```
实际调用此函数，我们使用await，而不是yield from，但都是大致相同的：
```python
async def ping_local():  
    return await ping_server('192.168.1.1')
```
再次说明，正如`yield from`，你不能在其他协程之外使用它，否则，你将得到一个语法错误。

在Python 3.5中，调用协程的这两种方式都支持，但`async/await`方式是主要的语法。

## 运行事件循环
如果你不知道如何启动和运行一个[事件循环（event loop）](https://docs.python.org/3/library/asyncio-eventloop.html)的话，我上面描述的关于协程的东西将没有用（或工作）。时间循环是执行异步函数的中心，所以当你要实际执行协程时，这就是你将要使用的东西。

时间循环为你提供了相当多的功能：

* 注册，执行和取消延迟调用（异步函数）
* 创建用于通信的客户端和服务器传输
* 创建子进程和传输，用于与另一个程序进行通信
* 委托线程池进行函数调用

虽然实际上你可以使用许多配置和时间循环类型，你所写的大部分程序将只需要使用如下的方法来调度一个函数：
```python
import asyncio

async def speak_async():  
    print('OMG asynchronicity!')

loop = asyncio.get_event_loop()  
loop.run_until_complete(speak_async())  
loop.close()  
```
最后三行是我们这里需要关注的地方。它首先获取默认的时间循环(`asyncio.get_event_loop()`),调度和运行异步任务，然后当循环结束运行的时候关闭循环。

`loop.run_until_complete()`函数实际上是阻塞的，所以直到所有的一步方法完成后它才返回。由于我们只在一个线程中运行它，当循环正在运行的时候，是没有办法继续执行下去的。

现在，你可能会想，这也不是很有用呀，因为我们最终还是阻塞在事件循环（而不是只是IO调用），但是想象一下，将你的整个程序包含在一个异步函数中，这将允许你同时运行许多异步请求，就像在一个web服务器上一样。

你甚至可以在事件循环自己的线程中中断它，让它处理所有的长IO请求，而主线程处理程序逻辑或UI。

## 一个例子
好了，让我们来看看一个可以实际运行的稍微大一点的例子。下面的代码是一个相当简单的异步程序，它从Reddit中获取JSON，解析JSON，并打印出/r/python, /r/programming, 和 /r/compsci中的一天置顶帖。

所示的第一种方法，`get_json()`，由`get_reddit_top()`调用，只是为适当的Reddit URL创建了一个HTTP GET请求。当它被`await`调用时，事件循环可以在等待返回HTTP响应时继续并服务于其他协程。一旦如此，返回JSON给`get_reddit_top()`，解析并打印。
```python
import signal  
import sys  
import asyncio  
import aiohttp  
import json

loop = asyncio.get_event_loop()  
client = aiohttp.ClientSession(loop=loop)

async def get_json(client, url):  
    async with client.get(url) as response:
        assert response.status == 200
        return await response.read()

async def get_reddit_top(subreddit, client):  
    data1 = await get_json(client, 'https://www.reddit.com/r/' + subreddit + '/top.json?sort=top&t=day&limit=5')

    j = json.loads(data1.decode('utf-8'))
    for i in j['data']['children']:
        score = i['data']['score']
        title = i['data']['title']
        link = i['data']['url']
        print(str(score) + ': ' + title + ' (' + link + ')')

    print('DONE:', subreddit + '\n')

def signal_handler(signal, frame):  
    loop.stop()
    client.close()
    sys.exit(0)

signal.signal(signal.SIGINT, signal_handler)

asyncio.ensure_future(get_reddit_top('python', client))  
asyncio.ensure_future(get_reddit_top('programming', client))  
asyncio.ensure_future(get_reddit_top('compsci', client))  
loop.run_forever()  
```
这与我们前面展示的例子有点不同。为了让事件循环运行多个协程，我们使用`asyncio.ensure_future()`，然后运行无限循环来处理一切。

要运行它，你需要先安装`aiohttp`，你可以使用PIP：
```
$ pip install aiohttp
```
现在，只要确保你使用Python 3.5或更高的版本来运行它，你应该会得到像这样的输出：
```
$ python main.py
46: Python async/await Tutorial (http://stackabuse.com/python-async-await-tutorial/)  
16: Using game theory (and Python) to explain the dilemma of exchanging gifts. Turns out: giving a gift probably feels better than receiving one... (http://vknight.org/unpeudemath/code/2015/12/15/The-Prisoners-Dilemma-of-Christmas-Gifts/)  
56: Which version of Python do you use? (This is a poll to compare the popularity of Python 2 vs. Python 3) (http://strawpoll.me/6299023)  
DONE: python

71: The Semantics of Version Control - Wouter Swierstra (http://www.staff.science.uu.nl/~swier004/Talks/vc-semantics-15.pdf)  
25: Favorite non-textbook CS books (https://www.reddit.com/r/compsci/comments/3xag9e/favorite_nontextbook_cs_books/)  
13: CompSci Weekend SuperThread (December 18, 2015) (https://www.reddit.com/r/compsci/comments/3xacch/compsci_weekend_superthread_december_18_2015/)  
DONE: compsci

1752: 684.8 TB of data is up for grabs due to publicly exposed MongoDB databases (https://blog.shodan.io/its-still-the-data-stupid/)  
773: Instagram's Million Dollar Bug? (http://exfiltrated.com/research-Instagram-RCE.php)  
387: Amazingly simple explanation of Diffie-Hellman. His channel has tons of amazing videos and only a few views :( thought I would share! (https://www.youtube.com/watch?v=Afyqwc96M1Y)  
DONE: programming  
```
注意，如果你运行了几次，打印的subreddit数据的顺序将会改变。这是因为每一个调用都会释放（yield）线程的控制权，允许处理另一个HTTP调用。哪一个最先返回则最先打印哪一个。

## 总结
虽然Python内置的一步功能并不如JavaScript一般平滑，但这不意味着你不能将其用于有趣且有效的应用。仅需30分钟，了解它的来龙去脉，你就会对于如何将其整合到你自己的应用程序有一个更好的感知。

*对于Python的async/await你是怎么想的? 过去你是如何使用它的？欢迎评论！*