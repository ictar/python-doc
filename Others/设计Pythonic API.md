原文：[Designing Pythonic APIs](http://noamelf.com/2016/08/05/designing-pythonic-apis/)

---

当编写一个包（库）的时候，为它提供一个良好的API，几乎与它的功能本身一样重要（好吧，至少你想要让别人使用），但怎么才算一个良好的API呢？在这篇文章中，我将尝试通过比较Requests和Urllib（Python标准库的一部分）在一些经典的HTTP场景的使用，从而提供关于这个问题的一些见解，并看看为什么Requests已经成为了Python用户中的事实上的标准。

* 在我们的探究过程中，我们会使用**Python 3.5**和**Requests 2.10.0**。

** 此博文是我上周的一个本地Python聚会（[PywebIL](http://www.meetup.com/PyWeb-IL/events/232724175/) ）上的演讲的改编。你可以[在这里](http://noamelf.com/designing-pythonic-apis-talk)找到幻灯片。

## _requests_ vs. _urllib_

### 用例1：发送一个GET请求

```python

    import urllib.request
    urllib.request.urlopen('http://python.org/')
    
```

```python

    <http.client.HTTPResponse at 0x7fdb08b1bba8>
    
```

```python

    import requests
    requests.get('http://python.org/')
    
```

```python

    <Response [200]>
    
```

#### 显式(API端点)优于隐式

  * 注意到requests对于它要做的事更简洁（因此，更清晰）。
  * _urllib_被看成隐式发送GET请求，因为它并不接受一个`data`参数
  * _requests_函数明确表明它要做什么。

#### 有用的对象表示

  * 当检查它的时候，_requests_返回了一个带有请求状态码的帮助字符串 (通过实现`__repr__()`方法来完成)。
  * _urllib_仅仅返回默认的（不清晰的）对象表示

#### 代码片段

([requests/api.py](https://github.com/kennethreitz/requests/blob/v2.10.0/requests/api.py)):

```python

    def request(method, url, **kwargs):
        with sessions.Session() as session:
            return session.request(method=method, url=url, **kwargs)
    
    def get(url, params=None, **kwargs):
        kwargs.setdefault('allow_redirects', True)
        return request('get', url, params=params, **kwargs)
    
    def post(url, data=None, json=None, **kwargs):
        return request('post', url, data=data, json=json, **kwargs)
    
```

  * 所有的HTTP动作在发送之前都遵循相同的流程，因此，有一个`request()`主流程函数。
  * 为每个调用`request()`的动作实现一个“辅助函数”，启用我们正在寻找的明确性。

### 用例2：获取请求状态码

```python

    import urllib.request
    r = urllib.request.urlopen('http://python.org/')
    r.getcode()
    
```

```python

    200
    
```

```python

    import requests
    r = requests.get('http://python.org/')
    r.status_code
    
```

```python

    200
    
```

#### 无需getter和setter

  * 将对象属性作为实际属性访问（而不是进行方法调用）让代码更清晰些。
  * 如果你是从其他OOP语言过来的 (嗯…… Java)，那么你可能会使用getter和setter，从而允许未来对对象属性进行改变。在Python中不需要这样，仅需使用[`@property`](http://www.programiz.com/python-programming/property)装饰器。

#### 代码片段

[http/client.py](https://github.com/python/cpython/blob/3.5/Lib/http/client.py#L737):

```python

    class HTTPResponse(io.BufferedIOBase):
    
        # ...
    
        def getcode(self):
            return self.status
    
```

  * _urllib_ (或实际上是_http_)使用一个“getter”来返回类属性。

### 用例3：编码、发送和解码POST请求

```python

    import urllib.parse
    import urllib.request
    import json
    
    url = 'http://www.httpbin.org/post'
    values = {'name' : 'Michael Foord'}
    
    data = urllib.parse.urlencode(values).encode()
    response = urllib.request.urlopen(url, data)
    body = response.read().decode()
    json.loads(body)
    
```

```python

    import requests
    
    url = 'http://www.httpbin.org/post'
    data = {'name' : 'Michael Foord'}
    
    response = requests.post(url, data=data)
    response.json()
    
```

#### 轻松访问常用功能

  * _requests_为数据编码和加载JSON响应提供了一个开箱即用体验，然而在_urllib_中，你必须自己实现这些部分。
  * 在设计你的API时考虑：我的包被用的频率多高？我可以添加什么插件，从而使得使用更容易？

同时注意，_requests_还提供了一种优雅的方式来发送JSON内容：

```python

    import requests
    
    url = 'http://www.httpbin.org/post'
    data = {'name' : 'Michael Foord'}
    
    response = requests.post(url, json=data)
    response.json()
    
```

### 用例4：发送鉴权请求

下面为HTTP请求创建了持久性凭证，然后发送请求：

```python

    import urllib.request
    
    gh_url = 'https://api.github.com/user'
    
    password_mgr = urllib.request.HTTPPasswordMgrWithDefaultRealm()
    password_mgr.add_password(None, gh_url, 'user', 'pswd')
    handler = urllib.request.HTTPBasicAuthHandler(password_mgr)
    
    opener = urllib.request.build_opener(handler)
    opener.open(gh_url)
    
```

```python

    import requests
    
    session = requests.Session()
    session.auth = ('user', 'pswd')
    session.get('https://api.github.com/user')
    
```

但如果我们只是想进行一次HTTP调用呢？我们需要所有的代码吗？这里，_requests_允许你这样：

```python

    import requests
    
    requests.get('https://api.github.com/user', auth=('user', 'pswd'))
    
```

#### 为简单和高级使用提供可能性

  * 当发送单个请求，和为多个请求发送一个更详细的请求时，_requests_允许简洁使用。
  * 当用户需要一个简单的用例时，不要让他经过一个漫长的过程。

#### 比起自己创建一个，更喜欢使用Python数据类型

  * _requests_对Python数据结构的使用使得它非常容易使用。没有必要去了解内部的_requests_包。

#### 库代码

[requests/models.py](https://github.com/kennethreitz/requests/blob/v2.10.0/requests/models.py#L488):

```python

    def prepare_auth(self, auth, url=''):
        """Prepares the given HTTP auth data."""
    
        # ...
    
        if auth:
            if isinstance(auth, tuple) and len(auth) == 2:
                # special-case basic HTTP auth
                auth = HTTPBasicAuth(*auth)
    
```

  * _requests_在内部将`(user,pass)`元组转换成一个鉴权类。

### 用例5：处理错误

```python

    from urllib.request import urlopen
    response = urlopen('http://www.httpbin.org/geta')
    response.getcode()
    
```

```python

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
    
```

```python

    import requests
    r = requests.get('http://www.httpbin.org/geta')
    r.status_code
    
```

```python

    404
    
```

#### 让用户选择如何处理错误

  * 有些程序员喜欢异常，而有些喜欢检查。
  * 在某些情况下，检查更优雅，而有时正好相反。
  * 让你的用户根据实际情况选择使用哪个比较好。
  * 默认返回代码允许这样，而默认`exceptions`并不会。

使用示例：

```python

    from urllib.request import urlopen
    from urllib.error import URLError, HTTPError
    try:
        response = urlopen('http://www.httpbin.org/geta')
    except HTTPError as e:
        if e.code == 404:
            print('Page not found')
    else:
        print('All good')
    
```

```python

    Page not found
    
```

```python

    from requests.exceptions import HTTPError
    import requests
    r = requests.get('http://www.httpbin.org/posta')
    try:
        r.raise_for_status()
    except HTTPError as e:
        if e.response.status_code == 404:
            print('Page not found')
    
```

```python

    Page not found
    
```

```python

    import requests
    r = requests.get('http://www.httpbin.org/geta')
    if r.ok:
        print('All good')
    elif r.status_code == requests.codes.not_found:
        print('Page not found')
    
```

```python

    Page not found
    
```

目前就是这样了。在准备这个演讲/文章的过程中，我学到了很多（Ele注，在翻译的时候我也学到了很多，O(∩_∩)O~），我希望你也读读它。我会很高兴在下面或者在Twitter (@noamelf)上看到你的评论（Ele注：欢迎去原文评论哈）。

##### 更新 (2016年8月8日)

如果你像许多人，包括我自己一样，最终好奇为什么在Requests和Urllib之间有如此鲜明的差异。Nick Coghlan在[下面的注释](http://noamelf.com/2016/08/05/designing-pythonic-apis/#comment-2823855721)和下面的一篇博文(标题自解释): [它解决了什么问题？](http://www.curiousefficiency.org/posts/2016/08/what-problem-does-it-solve.html)(Ele注：刚好翻译了这篇的[中文版](./Requests vs. urllib：它解决了什么问题？.md))中分享了它关于这个问题的广阔的视角。

