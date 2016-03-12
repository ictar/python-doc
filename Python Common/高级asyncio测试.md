原文：[Advanced asyncio testing](https://stefan.sofa-rockers.org/2016/03/10/advanced-asyncio-testing/)

---

In my [last article](https://stefan.sofa-rockers.org/2015/04/22/testing-coroutines/), I showed how pytest’s fixture system and plug-in
infrastructure can help you with writing cleaner and better tests.  Fixtures
allow you to create a clean event loop instance for every test case.  The
plug-in system allows you to write test functions that are actually asyncio
coroutines.  While I was working on that articel, _Tin Tvrtkovic_ created the
plug-in [pytest-asyncio](https://pypi.python.org/pypi/pytest-asyncio).

In short, it lets you do this:
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

Instead of this:
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

So using _pytest-asyncio_ clearly improves your test (and there is even more,
what this plug-in does!).

While I have been working on [aiomas](https://aiomas.readthedocs.org), some additional requirements came up
that were not so easily covered.  What _aiomas_ basically does is adding three
layers of abstraction around the asyncio transports:

1.  The _channel_ layer lets you send JSON or MsgPack encoded messages in
a request-reply manner.  This layer uses a custom protocol that works with
different kinds of transports: TCP sockets, Unix domain sockets and custom
transport called _local queue_.
2.  The _RPC_ layer creates a remote-procedure-call system on top of the
_channel_ layer.
3.  The _agent_ layer (for multi-agent systems) hides even more of the
networking-related stuff and lets you basically write classes that call
methods of other classes over a network connection.

Here is a simple example of how the _channel_ layer works:
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

## Requirements for our tests

So with this in mind, I had the following requirements for my tests:

1.  I need a clean event loop instance for every test.

    This can be solved with the `event_loop` fixture provided by
_pytest-asyncio_.

2.  Every test should be run with every transport available (TCP socket, Unix
domain socket, …).

    This could in theory be solved with the `pytest.mark.parametrize()`
decorator (but not in my case as we will see later).

3.  Every test needs a client coroutine.  Ideally, this would be the test itself.

    _pytest-asyncio’s_ `pytest.mark.asyncio` decorator solves this.

4.  Every test needs a server with a custom callback for client connections.
Servers must be cleanly shut down no matter what the outcome of the test is.

    It would seem that a fixture would do the job, but every server needs a test
specific callback for handling client connections.  This makes it a lot harder.

5.  I don’t want any “address already in use” errors if one test fails badly.

    _pytest-asyncio’s_ `unused_tcp_port` fixture comes to help.

6.  I don’t want to use `loop.run_until_complete()` all the time.

    Again, the `pytest.mark.asyncio` decorator solves this.

To wrap up what remains to be solved:  Every test needs at least two fixtures
(one for the event loop, one for the address type), but I want to combine them
as a single fixture.  Creating a fixture for setting up a server would also be
nice, but how can we do this?


## Our first approach

The first thing we can do is to wrap the loop and the address type in
a fixture.  We’ll call it _ctx_ (short for _test context_).  With fixture
parameters, it is also easy to create one fixture instance for every address type:
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

This lets us write our tests like this:
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

This works already very nicely and every test using the `ctx` fixture is run
once for every address type.

However, two problems remain:

1.  Our `ctx` fixture always requires an unused TCP port _and_ a temporary
directory – although we only need one of both in each case.
2.  Setting up the server (and closing it) also involves some code which will be
the same for every test and should thus be moved into a fixture.  However,
a `server` fixture won’t work directly, because every server needs a test
specific callback as you can see in the line where we create the server
(`server = await ...`).  But without a `server` fixture, we can’t have
a tear-down method for it …

Let’s see how we can tackle these issues.


## Approach number two

The first problem can be solved by using the `getfuncargvalue()` method of
the _request_ object that our fixture receives.  Using this method, we can
manually call a fixture function:
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

To help with issue number two, we can extend our `Context` class that is
passed into every test.  We add a method
`Context.start_server(client_handler)` that we can call from within our
tests.  We also add a finalize/teardown part to our `ctx` fixture that will
close the server once we are done.  And while we are at it, we’ll also create
some more shortcut functions:
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

With this extra functionality, our test case becomes a lot shorter, easier to
read, and more reliable:
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

The `ctx` fixture (and the associated `Context` class) is indeed not the
shortest fixture I ever wrote, but it helped me to remove approx. 200 lines of
boilerplate code from my tests (apart from making them more readable and maintainable).
