Skip to main content

Toggle navigation [ Curious Efficiency ](http://www.curiousefficiency.org/)

  * [About](http://www.curiousefficiency.org/pages/about.html)
  * [Archives](http://www.curiousefficiency.org/archive.html)
  * [Tags](http://www.curiousefficiency.org/categories/index.html)
  * [RSS](http://www.curiousefficiency.org/rss.xml)
  * [Python Notes](http://python-notes.curiousefficiency.org)

  * [Source](http://www.curiousefficiency.org/posts/2016/08/what-problem-does-it-solve.md)

# What problem does it solve?

Nick Coghlan

2016-08-06 11:05

[Comments](http://www.curiousefficiency.org/posts/2016/08/what-problem-does-
it-solve.html#disqus_thread)

[Source](http://www.curiousefficiency.org/posts/2016/08/what-problem-does-it-
solve.md)

One of the more puzzling aspects of Python for newcomers to the language is
the stark usability differences between the standard library's `urllib` module
and the popular (and well-recommended) third party module, `requests`, when it
comes to writing HTTP(S) protocol clients. When your problem is "talk to a
HTTP server", the difference in usability isn't immediately obvious, but it
becomes clear as soon as additional requirements like SSL/TLS, authentication,
redirect handling, session management, and JSON request and response bodies
enter the picture.

It's tempting, and entirely understandable, to want to [chalk this
difference](http://noamelf.com/2016/08/05/designing-pythonic-apis/) in ease of
use up to `requests` being "Pythonic" (in 2016 terms), while `urllib` has now
become un-Pythonic (despite being included in the standard library).

While there are certainly a few elements of that (e.g. the `property` builtin
was only added in Python 2.2, while `urllib2` was included in the original
Python 2.0 release and hence couldn't take that into account in its API
design), the vast majority of the usability difference relates to an entirely
different question we often forget to ask about the software we use: _What
problem does it solve?_

That is, many otherwise surprising discrepancies between `urllib`/`urllib2`
and `requests` are best explained by the fact that they _solve different
problems_, and the problems most HTTP client developers have today are closer
to those Kenneth Reitz designed `requests` to solve in 2010/2011, than they
are to the problems that Jeremy Hylton was aiming to solve more than a decade
earlier.

### It's all in the name

To quote the current Python 3 `urllib` package documentation: "urllib is a
package that collects several modules for working with URLs".

And the docstring from Jeremy's [original commit
message](https://hg.python.org/cpython/rev/b800e36aed4e) adding `urllib2` to
CPython: "An extensible library for opening URLs using a variety [of]
protocols".

Wait, what? We're just trying to write a HTTP client, so why is the
documentation talking about working with URLs in general?

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

### So why not add requests to the standard library?

Answering the not-so-obvious question of "What problem does it solve?" then
leads to a more obvious follow-up question: if the problems that `urllib`/
`urllib2` were designed to solve are no longer common, while the problems that
`requests` solves _are_ common, why not add `requests` to the standard
library?

If I recall correctly, Guido gave in-principle approval to this idea at a
language summit back in 2013 or so (after the `requests` 1.0 release), and
it's a fairly common assumption amongst the core development team that either
`requests` itself (perhaps as a bundled snapshot of an independently
upgradable component) or a compatible subset of the API with a different
implementation will eventually end up in the standard library.

However, even putting aside the [misgivings of the requests
developers](https://github.com/kennethreitz/requests/issues/2424) about the
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

  * [python](http://www.curiousefficiency.org/categories/python.html)

  * [Previous post](http://www.curiousefficiency.org/posts/2016/05/pycon-australia-education-cfp-2016.html "Propose a talk for the PyCon Australia Education Seminar!" )

## Comments

Please enable JavaScript to view the [comments powered by
Disqus.](http://disqus.com/?ref_noscript) [Comments powered by
Disqus](http://disqus.com)

Contents © 2016 [Nick Coghlan](mailto:ncoghlan@gmail.com) \-
[CC0](https://creativecommons.org/publicdomain/zero/1.0/), republish as you
wish. - Powered by [Nikola](http://nikola.ralsina.com.ar)

