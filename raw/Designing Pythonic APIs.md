Toggle navigation [noamelf](http://noamelf.com/)

  * [Home](http://noamelf.com/)
  * [About](http://noamelf.com/about/)
  * [Talks](http://noamelf.com/talks/)

# Designing Pythonic APIs

Posted by noamelf on August 5, 2016

# Designing Pythonic APIs

_Learning from Kenneth Reitz’s [Requests](http://docs.python-
requests.org/en/master/)_

When writing a package (library), providing it with a good API, is almost as
important as its functionality itself (well, at least if you want some
adoption), but what makes a good API? In this post, I’ll try to provide some
insights on that question by comparing _Requests_ and _Urllib_ (part of
Python’s standard library) in a few typical HTTP usage scenarios and see why
_Requests_ has become the de facto standard among Python users.

* Throughout our investigation we’ll be using **Python 3.5** and **Requests 2.10.0**.

** This blog post is an adaptation of a talk I gave at our local Python meetup - [PywebIL](http://www.meetup.com/PyWeb-IL/events/232724175/) \- last week. You can find the slides [here](http://noamelf.com/designing-pythonic-apis-talk).

## _Requests_ vs. _Urllib_

### Use case #1: sending a GET request

[code]

    import urllib.request
    urllib.request.urlopen('http://python.org/')
    
[/code]

[code]

    <http.client.HTTPResponse at 0x7fdb08b1bba8>
    
[/code]

[code]

    import requests
    requests.get('http://python.org/')
    
[/code]

[code]

    <Response [200]>
    
[/code]

#### Explicit (API endpoints) is better than implicit

  * Notice how requests is more concise (hence, clear) about what it will do.
  * _Urllib_ is getting told implicitly to send a GET request since it didn’t receive a `data` argument
  * _Requests_ function name explicitly mark what it will do.

#### Helpful object representation

  * _Requests_ returns a helpful string with the request status code when examining it (this done by implementing the `__repr__()` method).
  * _Urllib_ just returns the default (unclear) object representation

#### Code snippet

([requests/api.py](https://github.com/kennethreitz/requests/blob/v2.10.0/reque
sts/api.py)):

[code]

    def request(method, url, **kwargs):
        with sessions.Session() as session:
            return session.request(method=method, url=url, **kwargs)
    
    def get(url, params=None, **kwargs):
        kwargs.setdefault('allow_redirects', True)
        return request('get', url, params=params, **kwargs)
    
    def post(url, data=None, json=None, **kwargs):
        return request('post', url, data=data, json=json, **kwargs)
    
[/code]

  * All the HTTP verbs follow a similar flow prior to sending, hence there is a the `request()` main flow function.
  * Implementing a “helper function” for each verb that calls `request()`, enables the explicitness we are looking for.

### Use case #2: getting a request status code

[code]

    import urllib.request
    r = urllib.request.urlopen('http://python.org/')
    r.getcode()
    
[/code]

[code]

    200
    
[/code]

[code]

    import requests
    r = requests.get('http://python.org/')
    r.status_code
    
[/code]

[code]

    200
    
[/code]

#### No need for getters and setters

  * Accessing an object property as an actual property (and not a method call) makes the code a little clearer.
  * If you come from other OOP language (hmmm… Java), you might be tempted to use getters and setters to allow future changes to the object’s properties. No need for that in Python, just use the [`@property`](http://www.programiz.com/python-programming/property) decorator.

#### Code snippet

[http/client.py](https://github.com/python/cpython/blob/3.5/Lib/http/client.py
#L737):

[code]

    class HTTPResponse(io.BufferedIOBase):
    
        # ...
    
        def getcode(self):
            return self.status
    
[/code]

  * _Urllib_ (or actually _http_) is using a “getter” to return a class property.

### Use case #3: encoding, sending and decoding a POST request

[code]

    import urllib.parse
    import urllib.request
    import json
    
    url = 'http://www.httpbin.org/post'
    values = {'name' : 'Michael Foord'}
    
    data = urllib.parse.urlencode(values).encode()
    response = urllib.request.urlopen(url, data)
    body = response.read().decode()
    json.loads(body)
    
[/code]

[code]

    import requests
    
    url = 'http://www.httpbin.org/post'
    data = {'name' : 'Michael Foord'}
    
    response = requests.post(url, data=data)
    response.json()
    
[/code]

#### Easy access to common functionality

  * _Requests_ provides an out-of-the-box experience for the encoding of the data and loading the JSON response while in _Urllib_ you have to implement those parts yourself.
  * When designing your API think: how will my package be commonly use? What plugs can I add to make that usage easier?

On the same note, _Requests_ also provides an elegant way to send JSON
content:

[code]

    import requests
    
    url = 'http://www.httpbin.org/post'
    data = {'name' : 'Michael Foord'}
    
    response = requests.post(url, json=data)
    response.json()
    
[/code]

### Use case #4: sending authenticated request

The following creates persistence credentials for HTTP requests, and sends a
request:

[code]

    import urllib.request
    
    gh_url = 'https://api.github.com/user'
    
    password_mgr = urllib.request.HTTPPasswordMgrWithDefaultRealm()
    password_mgr.add_password(None, gh_url, 'user', 'pswd')
    handler = urllib.request.HTTPBasicAuthHandler(password_mgr)
    
    opener = urllib.request.build_opener(handler)
    opener.open(gh_url)
    
[/code]

[code]

    import requests
    
    session = requests.Session()
    session.auth = ('user', 'pswd')
    session.get('https://api.github.com/user')
    
[/code]

But what if we just want to do a single HTTP call? Do we need all that code?
_Requests_ have you covered here:

[code]

    import requests
    
    requests.get('https://api.github.com/user', auth=('user', 'pswd'))
    
[/code]

#### Provide possibilities for simple and advanced usage

  * _Requests_ allow concise usage, when sending a single request, and a more verbose one for multiple requests.
  * Don’t make the user go through a lengthy process when he needs a simple use case.

#### Prefer Python data types over self-made ones

  * _Requests_ usage of Python’s data structures makes it very easy to use. No need to get to know internal _Requests_ packages.

#### Libraries code

[requests/models.py](https://github.com/kennethreitz/requests/blob/v2.10.0/req
uests/models.py#L488):

[code]

    def prepare_auth(self, auth, url=''):
        """Prepares the given HTTP auth data."""
    
        # ...
    
        if auth:
            if isinstance(auth, tuple) and len(auth) == 2:
                # special-case basic HTTP auth
                auth = HTTPBasicAuth(*auth)
    
[/code]

  * _Requests_ internally converts the `(user,pass)` tuple to an authentication class.

### Use case #5: handling errors

[code]

    from urllib.request import urlopen
    response = urlopen('http://www.httpbin.org/geta')
    response.getcode()
    
[/code]

[code]

    ---------------------------------------------------------------------------
    
    HTTPError                                 Traceback (most recent call last)
    
    <ipython-input-45-5fba039d189a> in <module>()
          1 from urllib.request import urlopen
    ----> 2 response = urlopen('http://www.httpbin.org/geta')
          3 response.getcode()
    
    
    /usr/lib/python3.5/urllib/request.py in urlopen(url, data, timeout, cafile, capath, cadefault, context)
        161     else:
        162         opener = _opener
    --> 163     return opener.open(url, data, timeout)
        164
        165 def install_opener(opener):
    
    
    /usr/lib/python3.5/urllib/request.py in open(self, fullurl, data, timeout)
        470         for processor in self.process_response.get(protocol, []):
        471             meth = getattr(processor, meth_name)
    --> 472             response = meth(req, response)
        473
        474         return response
    
    
    /usr/lib/python3.5/urllib/request.py in http_response(self, request, response)
        580         if not (200 <= code < 300):
        581             response = self.parent.error(
    --> 582                 'http', request, response, code, msg, hdrs)
        583
        584         return response
    
    
    /usr/lib/python3.5/urllib/request.py in error(self, proto, *args)
        508         if http_err:
        509             args = (dict, 'default', 'http_error_default') + orig_args
    --> 510             return self._call_chain(*args)
        511
        512 # XXX probably also want an abstract factory that knows when it makes
    
    
    /usr/lib/python3.5/urllib/request.py in _call_chain(self, chain, kind, meth_name, *args)
        442         for handler in handlers:
        443             func = getattr(handler, meth_name)
    --> 444             result = func(*args)
        445             if result is not None:
        446                 return result
    
    
    /usr/lib/python3.5/urllib/request.py in http_error_default(self, req, fp, code, msg, hdrs)
        588 class HTTPDefaultErrorHandler(BaseHandler):
        589     def http_error_default(self, req, fp, code, msg, hdrs):
    --> 590         raise HTTPError(req.full_url, code, msg, hdrs, fp)
        591
        592 class HTTPRedirectHandler(BaseHandler):
    
    
    HTTPError: HTTP Error 404: NOT FOUND
    
[/code]

[code]

    import requests
    r = requests.get('http://www.httpbin.org/geta')
    r.status_code
    
[/code]

[code]

    404
    
[/code]

#### Let the user choose how to handle errors

  * Some programmers prefer exceptions, some prefer checks.
  * In some situations a check is much more elegant and sometimes it’s the other way around.
  * It’s good to let your users to choose what to use when.
  * Defaulting to return codes allow that, while defaulting to `exceptions` do not.

Usage examples:

[code]

    from urllib.request import urlopen
    from urllib.error import URLError, HTTPError
    try:
        response = urlopen('http://www.httpbin.org/geta')
    except HTTPError as e:
        if e.code == 404:
            print('Page not found')
    else:
        print('All good')
    
[/code]

[code]

    Page not found
    
[/code]

[code]

    from requests.exceptions import HTTPError
    import requests
    r = requests.get('http://www.httpbin.org/posta')
    try:
        r.raise_for_status()
    except HTTPError as e:
        if e.response.status_code == 404:
            print('Page not found')
    
[/code]

[code]

    Page not found
    
[/code]

[code]

    import requests
    r = requests.get('http://www.httpbin.org/geta')
    if r.ok:
        print('All good')
    elif r.status_code == requests.codes.not_found:
        print('Page not found')
    
[/code]

[code]

    Page not found
    
[/code]

That’s it for now. I learned quite a bit preparing this talk/post, and I hope
you did too reading it. I’ll be glad to hear your comments down bellow or on
Twitter (@noamelf).

##### Update (August 8th, 2016)

If you end up wondering how come there is such a stark usability difference
between Requests and Urllib like many, including myself, do. Nick Coghlan
shares his wide perspective on the subject, in a [comment
bellow](http://noamelf.com/2016/08/05/designing-pythonic-
apis/#comment-2823855721) and a following blog post (with the self-explanatory
title): [what problem does it
solve?](http://www.curiousefficiency.org/posts/2016/08/what-problem-does-it-
solve.html).

* * *

Please enable JavaScript to view the [comments powered by
Disqus.](https://disqus.com/?ref_noscript)

  * [&lt;- Previous Post](http://noamelf.com/2016/03/18/api-and-microservices-management-with-kong/ "API and Microservices Management with Kong" )

* * *

  * [ __ __ ](http://noamelf.com/feed.xml)
  * [ __ __ ](https://twitter.com/noamelf)
  * [ __ __ ](https://www.facebook.com/noamelf)
  * [ __ __ ](https://github.com/noamelf)
  * [ __ __ ](mailto:noamelf@gmail.com)

Copyright (C) Noam Elfanbaum 2016

