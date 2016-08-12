原文：[What problem does it solve?](http://www.curiousefficiency.org/posts/2016/08/what-problem-does-it-solve.html)

---

对于Python新手来说，Python较令人费解的方面之一就是，当涉及到编写HTTP(S)协议客户端时，标准库的`urllib`模块和流行的（及备受推崇的）第三方模块`requests`之间鲜明的可用性差异。当你的问题是“与HTTP服务器进行通信”时，可用性方面的差异并不是那么明显，但一涉及到额外的需求，像SSL/TLS、鉴权、重定向处理、会话管理和JSON请求/响应主题，差异就明显起来。

It's tempting, and entirely understandable, to want to [chalk this difference](http://noamelf.com/2016/08/05/designing-pythonic-apis/) in ease of
use up to `requests` being "Pythonic" (in 2016 terms), 而`urllib`现在已经不Pythonic了 (尽管被包含在了标准库中)。

While there are certainly a few elements of that (e.g. the `property` builtin
was only added in Python 2.2, while `urllib2` was included in the original
Python 2.0 release and hence couldn't take that into account in its API
design), the vast majority of the usability difference relates to an entirely
different question we often forget to ask about the software we use: _What problem does it solve?_

That is, many otherwise surprising discrepancies between `urllib`/`urllib2`
and `requests` are best explained by the fact that they _solve different
problems_, and the problems most HTTP client developers have today are closer
to those Kenneth Reitz designed `requests` to solve in 2010/2011, than they
are to the problems that Jeremy Hylton was aiming to solve more than a decade
earlier.

### It's all in the name

引用当前的Python 3 `urllib`包文档：“urllib是一个收集几个处理URL模块的包”。

以及来自Jeremy的添加`urllib2`到CPython的[原始提交信息](https://hg.python.org/cpython/rev/b800e36aed4e)的文档字符串：“使用各种协议，用于打开URL的可扩展库”。

等等，神马？我们只是想写一个HTTP客户端，所以为什么文档谈到一般的URL相关工作？

While it may seem strange to developers accustomed to the modern HTTPS+JSON
powered interactive web, it wasn't always clear that that was how things were
going to turn out.

At the turn of the century, the expectation was instead that we'd retain a
rich variety of data transfer protocols with different characteristics
optimised for different purposes, and that the most useful client to have in
the standard library would be one that could be used to talk to multiple
different kinds of servers (like HTTP, FTP, NFS, etc), without client
developers needing to worry too much about the specific protocol used (as
indicated by the URL schema).

In practice, things didn't work out that way (mostly due to restrictive
institutional firewalls meaning HTTP servers were the only remote services
that could be accessed reliably), so folks in 2016 are now regularly comparing
the usability of a dedicated HTTP(S)-only client library with a general
purpose URL handling library that needs to be configured to specifically be
using HTTP(S) before you gain access to most HTTP(S) features.

When it was written, `urllib2` was a square peg that was designed to fit into
the square hole of "generic URL processing". By contrast, most modern client
developers are looking for a round peg to fit into the round hole that is
HTTPS+JSON processing - `urllib`/`urllib2` will fit if you shave the corners
off first, but `requests` comes pre-rounded.

### 所以，为什么不把requests添加到标准库中呢？

对"它解决了什么问题？"这个不那么明显的问题的回答，会到一个明显得多的后续问题：如果`urllib`/`urllib2`被设计来解决的问题不再常见，而`requests`解决的问题是常见的，那么为什么不把`requests`添加到标准库中呢？

If I recall correctly, Guido gave in-principle approval to this idea at a
language summit back in 2013 or so (在`requests` 1.0发布后), and
it's a fairly common assumption amongst the core development team that either
`requests` itself (perhaps as a bundled snapshot of an independently
upgradable component) or a compatible subset of the API with a different
implementation will eventually end up in the standard library.

However, even putting aside the [misgivings of the requests developers](https://github.com/kennethreitz/requests/issues/2424) about the
idea, there are still some non-trivial system integration problems to solve in
getting `requests` to a point where it would be acceptable as a standard
library component.

In particular, one of the things that `requests` does to more reliably handle
SSL/TLS certificates in a cross-platform way is to bundle the Mozilla
Certificate Bundle included in the `certifi` project. This is a sensible thing
to do by default (due to the difficulties of obtaining reliable access to
system security certificates in a cross-platform way), but it conflicts with
the security policy of the standard library, which specifically aims to
delegate certificate management to the underlying operating system. That
policy aims to address two needs: allowing Python applications access to
custom institutional certificates added to the system certificate store (most
notably, private CA certificates for large organisations), and avoiding adding
an additional certificate store to end user systems that needs to be updated
when the root certificate bundle changes for any other reason.

These kinds of problems are technically solvable, but they're not fun to
solve, and the folks in a position to help solve them already have a great
many other demands on their time.This means we're not likely to see much in
the way of progress in this area as long as most of the CPython and `requests`
developers are pursuing their upstream contributions as a spare time activity,
rather than as something they're specifically employed to do.
