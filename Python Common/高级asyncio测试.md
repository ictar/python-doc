原文：[Advanced asyncio testing](https://stefan.sofa-rockers.org/2016/03/10/advanced-asyncio-testing/)

---

在我[上一篇文章中](https://stefan.sofa-rockers.org/2015/04/22/testing-coroutines/)，我显示了pytest的Fixture系统和插入式基础架构是如何帮你编写更加干净优秀的测试的。Fixture允许你为每个测试用例创建一个干净的事件循环实例。而插入式系统允许你编写实际上市asyncio协程的测试函数。在我写那篇文章时，_Tin Tvrtkovic_ 创建了插入式[pytest-asyncio](https://pypi.python.org/pypi/pytest-asyncio)。

总之，它让你可以这样：
```py
import asyncio
import time

import pytest

@pytest.mark.asyncio
def test_coro(event_loop):
    before = time.monotonic()
    await asyncio.sleep(0.1, loop=event_loop)
    after = time.monotonic()
    assert after - before >= 0.1
```

来取代这样：
```py
import asyncio
import time

def test_coro():
    loop = asyncio.new_event_loop()
    try:
        asyncio.set_event_loop(loop)

        before = time.monotonic()
        loop.run_until_complete(asyncio.sleep(0.1, loop=loop))
        after = time.monotonic()
        assert after - before >= 0.1
    finally:
        loop.close()
```

因此，使用_pytest-asyncio_显然改善你的测试 (当然，这个插件还能做更多东西！)。

在我努力做[aiomas](https://aiomas.readthedocs.org)时，一些无法简单涵盖的额外需求出现了。_aiomas_基本上做的是在asyncio传输周围增加三个抽象层：

1.  _channel_层允许你以一种请求-应答方式发送JSON或者MsgPack编码消息。这一层使用了与不同种类的传输一起工作的自定义协议：TCP套接字，Unix域套接字和名为_本地队列_的自定义传输。
2.  _RPC_层在_channel_层之上创建了一个远程过程调用系统。
3.  _agent_层（为多代理系统）隐藏了更多的网络相关的东西，并基本上让你编写那些通过网络连接调用其他类方法的类。

这里是_channel_层如何工作的一个简单例子：
```py
import aiomas


async def handle_client(channel):
    """Handle a client connection."""
    req = await channel.recv()
    print(req.content)
    await req.reply('cya')
    await channel.close()


async def client():
    """Client coroutine: Send a greeting to the server and wait for a
    reply."""
    channel = await aiomas.channel.open_connection(('localhost', 5555))
    rep = await channel.send('ohai')
    print(rep)
    await channel.close()


server = aiomas.run(aiomas.channel.start_server(
    ('localhost', 5555), handle_client))
aiomas.run(client())

server.close()
aiomas.run(server.wait_closed())
```

## 对于我们的测试的要求

所以，考虑到这一点，对于我的测试，我有以下要求：

1.  对于每个测试，我需要一个干净的事件循环实例。

    这可以使用_pytest-asyncio_提供的`event_loop`来解决。

2.  每一个测试都应该使用一个可用的传输来运行 (TCP socket, Unix
domain socket, …).

    这在理论上可以使用`pytest.mark.parametrize()`装饰器解决 (稍后我们会看到，在我的例子中并不是这样的)。

3.  每一个测试需要一个客户端协程。理想情况下，这将是测试本身。

    _pytest-asyncio的_ `pytest.mark.asyncio`装饰器解决了这个问题。

4.  每个测试需要一个带有与客户端连接相对应的自定义回调的服务器。不管测试输出是什么，都必须彻底关闭服务器

    看起来一个fixture可以做到这点，但每个服务器都需要一个特定测试回调来处理客户端连接。这使得它困难得多。

5.  如果一个测试失败了，我不希望看到任何“address already in use”错误。

    _pytest-asyncio的_`unused_tcp_port`fixture可以一用。

6.  我不想一直使用`loop.run_until_complete()`。

    再次，`pytest.mark.asyncio`装饰器解决了这个问题。

总结有待解决的问题：每个测试都需要至少两个fixture（一个用于事件循环，另一个用于地址类型），但我想将它们结合成一个单一的fixture。为建立服务器创建一个fixture也是不错的，但如何才能做到这一点呢？


## 第一个种方法

我们能做的第一件事是将循环和地址类型都放在一个fixture中。我们将称其为_ctx_(_测试上下文(test context)_的缩写)。使用fixture参数，也可以容易地为每个地址类型创建一个fixture实例：
```py
import tempfile
import py
import pytest


class Context:
    def __init__(self, loop, addr):
        self.loop = loop
        self.addr = addr


@pytest.fixture(params=['tcp', 'unix'])
def ctx(request, event_loop, unused_tcp_port, short_tmpdir):
    """Generate tests with TCP sockets and Unix domain sockets."""
    addr_type = request.param
    if addr_type == 'tcp':
        addr = ('127.0.0.1', unused_tcp_port)
    elif addr_type == 'unix':
        addr = short_tmpdir.join('sock').strpath
    else:
        raise RuntimeError('Unknown addr type: %s' % addr_type)

    ctx = Context(event_loop, addr)
    return ctx


@pytest.yield_fixture()
def short_tmpdir():
    """Generate a short temp. dir for Unix domain sockets.  The paths
    provided by ptest's tmpdir fixture are too long on some platforms."""
    with tempfile.TemporaryDirectory() as tdir:
        yield py.path.local(tdir)
```

这让我们这样编写我们的测试：
```py
import aiomas

@pytest.mark.asyncio
async def test_channel(ctx):
    results = []

    async def handle_client(channel):
        req = await channel.recv()
        results.append(req.content)
        await req.reply('cya')
        await channel.close()


    server = await aiomas.channel.start_server(ctx.addr, handle_client)
    try:
        channel = await aiomas.channel.open_connection(ctx.addr)
        rep = await channel.send('ohai')
        results.append(rep)
        await channel.close()

    finally:
        server.close()
        await server.wait_closed()

    assert results == ['ohai', 'cya']
```

This works already very nicely and every test using the 这已经工作良好，而且使用`ctx`fixture的每个测试都为每个地址类型运行一次。

然而，有两个问题仍然存在：

1.  我们的`ctx`fixture总是需要一个未使用的TCP端口以及一个临时目录 —— 虽然在每种情况下，我们只需要其中之一。
2.  建立服务器 (和关闭它) 也涉及一些代码，这些代码对于每个测试都是一样的，因此应该被移到一个fixture中。然而，一个`server`fixture并不直接工作，因为每个服务器需要一个指定测试的回调，正如你在我们创建服务器的那一行(`server = await ...`)可以看到的。但没有`server`fixture，对此我们就无法拆除……

让我们看看我们如何能够解决这些问题。


## 第二种方法

第一个问题可以通过我们的fixture接收的_request_对象的`getfuncargvalue()`方法来解决。使用这个方法，我们可以手工调用一个fixture函数：
```py
@pytest.fixture(params=['tcp', 'unix'])
def ctx(request, event_loop):
    """Generate tests with TCP sockets and Unix domain sockets."""
    addr_type = request.param
    if addr_type == 'tcp':
        port = request.getfuncargvalue('unused_tcp_port')
        addr = ('127.0.0.1', port)
    elif addr_type == 'unix':
        tmpdir = request.getfuncargvalue('short_tmpdir')
        addr = tmpdir.join('sock').strpath
    else:
        raise RuntimeError('Unknown addr type: %s' % addr_type)

    ctx = Context(event_loop, addr)
    return ctx
```

要解决第二个问题，我们可以扩展传递给每个测试的`Context`类。我们添加一个方法`Context.start_server(client_handler)`，在我们的测试中，我们可以调用这个方法。我们还添加了一个finalize/teardown部分到我们的`ctx` fixture中，一旦完成了，它将关闭服务器。而我们还需要创建一些快捷功能：
```py
import asyncio
import tempfile
import py
import pytest


class Context:
    def __init__(self, loop, addr):
        self.loop = loop
        self.addr = addr
        self.server = None

    async def connect(self, **kwargs):
        """Create and return a connection to "self.addr"."""
        return (await aiomas.channel.open_connection(
            self.addr, loop=self.loop, **kwargs))

    async def start_server(self, handle_client, **kwargs):
        """Start a server with the callback *handle_client* listening on
        "self.addr"."""
        self.server = await aiomas.channel.start_server(
            self.addr, handle_client, loop=self.loop, **kwargs)

    async def start_server_and_connect(self, handle_client,
                                       server_kwargs=None,
                                       client_kwargs=None):
        """Shortcut for::

            await ctx.start_server(...)
            channel = await ctx.connect()"

        """
        if server_kwargs is None:
            server_kwargs = {}

        if client_kwargs is None:
            client_kwargs = {}

        await self.start_server(handle_client, **server_kwargs)
        return (await self.connect(**client_kwargs))

    async def close_server(self):
        """Close the server."""
        if self.server is not None:
            server, self.server = self.server, None
            server.close()
            await server.wait_closed()


@pytest.yield_fixture(params=['tcp', 'unix'])
def ctx(request, event_loop):
    """Generate tests with TCP sockets and Unix domain sockets."""
    addr_type = request.param
    if addr_type == 'tcp':
        port = request.getfuncargvalue('unused_tcp_port')
        addr = ('127.0.0.1', port)
    elif addr_type == 'unix':
        tmpdir = request.getfuncargvalue('short_tmpdir')
        addr = tmpdir.join('sock').strpath
    else:
        raise RuntimeError('Unknown addr type: %s' % addr_type)

    ctx = Context(event_loop, addr)

    yield ctx

    # Shutdown the server and wait for all pending tasks to finish:
    aiomas.run(ctx.close_server())
    aiomas.run(asyncio.gather(*asyncio.Task.all_tasks(event_loop),
                              return_exceptions=True))
```

使用这个额外的功能，我们的测试用例变得短得多，容易读得多，并且更加可靠：
```py
import aiomas

@pytest.mark.asyncio
async def test_channel(ctx):
    results = []

    async def handle_client(channel):
        req = await channel.recv()
        results.append(req.content)
        await req.reply('cya')
        await channel.close()


    channel = await ctx.start_server_and_connect(handle_client)
    rep = await channel.send('ohai')
    results.append(rep)
    await channel.close()

    assert results == ['ohai', 'cya']
```

`ctx` fixture (和相关的`Context`类)确实不是我写过的最短的fixture，但它帮助我从我的测试中移除了约200行的样板文件代码（除了让它们更加可读和可维护）。
