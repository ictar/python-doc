<!-- 
#+TITLE: Python新增的secrets模块
#+URL: http://www.blog.pythonlibrary.org/2017/02/16/pythons-new-secrets-module/
#+AUTHOR: lujun9972
#+TAGS: Python Common
#+DATE: [2017-03-23 四 15:29]
-->

原文链接: http://www.blog.pythonlibrary.org/2017/02/16/pythons-new-secrets-module/

- [使用secrets生成令牌](#orga8639ea)
- [摘要](#org42cb0a3)

Python 3.6新增了一个名为 `secrets` 的模块. 他可以很直观的生成密码强度的伪随机数. 这些伪随机数足以用于管理像账户认证,令牌之类的机密信息. Python的 `random` 模块,其强度只适用于仿真与建模,而并不能胜任真正加密工作. 当然,你始终可以用os模块中的 `urandom()` 函数:

```python
>>> import os
>>> os.urandom(8)
'\x9c\xc2WCzS\x95\xc8'
```

不过现在有了 `secrets` 模块, 我们可以创建自己的"密码强度的伪随机值"了. 下面是一个简单的例子:

```python
>>> import secrets
>>> import string
>>> characters = string.ascii_letters + string.digits
>>> bad_password = ''.join(secrets.choice(characters) for i in range(8))
>>> bad_password
'SRvM54Z1'
```

在这个例子中,我们导入了 `secrets` 模块和 `string` 模块. 然后用大写字母和数字组成了一个字符串. 最后使用 `secrets` 模块的 `choice()` 方法随机地抽取出其中的字符,从而生成一个不够好的密码. 之所以说这个密码不够好是因为密码中没有特殊字符, 但实际上它已经要好过很多人使用的密码了. 还有读者指出,除此之外,这个密码也比较难记,所以很可能会被记录在纸上被人发现. 这当然也对,但是使用字典里的单词作为密码是非常不建议的,你最好习惯这种密码或者使用密码管理软件管理你的密码.

---


<a id="orga8639ea"></a>

# 使用secrets生成令牌

`secrets` 模块还提供了许多生成令牌的方法. 下面来看一些例子:

```python
>>>: secrets.token_bytes()
b'\xd1Od\xe0\xe4\xf8Rn</U\xf4G\xdb\x08\xa8\x85\xeb\xba>\x8cO\xa7XV\x1cb\xd6\x11\xa0\xcaK'

>>> secrets.token_bytes(8)
b'\xfc,9y\xbe]\x0e\xfb'

>>> secrets.token_hex(16)
'6cf3baf51c12ebfcbe26d08b6bbe1ac0'

>>> secrets.token_urlsafe(16)
'5t_jLGlV8yp2Q5tolvBesQ'
```

`token_bytes` 函数会返回一段随机的包含了n个字节的字节字符串. 我一开始没有指定要生成多少个字节,所以Python自动为我选择了一个合适的长度. 随后我又调用了一次这个函数并且明确指定了生成8个字节长度的字符串. 然后我们又试了一把 `token_hex` 函数, 它会返回一段随机的十六进制形式的字符串. 最后的 `token_urlsafe` 函数会返回一段随机的 URL安全的文本字符串. 其文本已经用 Base64 编码过了! 要注意,在实际使用中,你应该至少生成32字节长度的令牌才能够防止被暴力破解所攻破([source](https://docs.python.org/3.6/library/secrets.html#how-many-bytes-should-tokens-use)).

---


<a id="org42cb0a3"></a>

# 摘要

新增的 `secrets` 模块非常有价值. 老实说,我觉得这玩意早就该有了. 好在最终我们终于有了这么一个模块,可以用来安全地生成密码强度哦令牌和密码了. 可以花点时间看看该模块的文档,其中有一些好玩的应用例子.

---

相关阅读资料

-   The secrets module [documentation](https://docs.python.org/3.6/library/secrets.html)
-   What’s new in Python 3.6: [secrets module](https://docs.python.org/3.6/library/secrets.html#module-secrets)
-   PEP 506 — [Adding A Secrets Module To The Standard Library](https://www.python.org/dev/peps/pep-0506/)
