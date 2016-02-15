原文：[How to Verify Phone numbers in Python with the Twilio Lookup API](https://www.twilio.com/blog/2016/02/how-to-verify-phone-numbers-in-python-with-the-twilio-lookup-api.html)

---

![Screen-Shot-2016-02-05-at-10.59.24-AM](https://www.twilio.com/blog/wp-content/uploads/2016/02/Screen-Shot-2016-02-05-at-10.59.24-AM1.png)

[Twilio Lookup](https://www.twilio.com/docs/api/lookups)是一个具备大量工具的简单的REST API。你可以使用Lookup来检查一个号码是否存在，将国际号码格式化为当地标准，确定一个电话是否是一个固定电话或者是否可以接受短信，甚至还能发现与那个电话号码相关的运营商信息。

In this post, we’re going to learn how to deal with valid and invalid numbers using the 在这篇文章中，我们将学习如何使用[Twilio Python library](https://www.twilio.com/docs/python/install)来处理有效和无效的号码。这个代码可以在任何情况下工作，无论你是用它在生产的Django应用中查找客户数量，还是只是运行一个简单的脚本来在bending数据库中检查好吗。而无论你使用的是Python 2还是Python 3，它都应该可以工作。

在开始之前，你需要安装了[Python](https://www.python.org/downloads/)以及[pip](http://pip.readthedocs.org/en/stable/installing/)，其中，在大多数的Python版本中应该都自带了pip。你也将需要注册一个[免费的Twilio 账号](http://twilio.com/try-twilio)。

### 开始吧

为了干净的使用pip安装python库，你首先应该运行一个[virtualenv](https://virtualenv.pypa.io/en/latest/index.html)。如果你不熟悉pip和virtualenv，可以看看[这个指南](https://www.twilio.com/docs/quickstart/python/devenvironment)。

进入终端，然后安装Twilio Python模块：
```sh
pip install twilio>=5.0.0
```

你还需要准备好你的账户SID和Auth Token，这些都可以通过[Twilio用户面板](https://www.twilio.com/user/account/settings)得到。

![twilio-credentials.gif](https://www.twilio.com/blog/wp-content/uploads/2016/02/iDAQcmxBYn7TF204ZeIOPwciQV2Zn-HWiflo7X8xgRgTZ9hMq8Oqq2iUQ9y6ANSMbQ0BD8WsrgyCxbBRX779U0ObFGckOoJ6888N5jBDat2jZKcm4TzJqVsqAwbI4-MfyDY6Kuc9.png)

现在，得到这些值，然后将它们设置为环境变量：

```sh
export TWILIO_ACCOUNT_SID='Replace this with your Twilio Account SID*'
export TWILIO_AUTH_TOKEN='*Replace this with your Twilio Auth Token*'
```

通过将其设置为环境变量，Twilio Python模块将能够访问它们，这样就不会出现意外将你的账户凭证提交到版本控制的问题。你还可以看看[这个指南](https://www.twilio.com/blog/2015/02/managing-development-environment-variables-across-multiple-ruby-applications.html)，它是关于如何管理你的环境变量的。

### 查找有效的电话号码

如果你想要玩一玩API，并看看一个请求将返回什么类型的数据，那么你可以看看[Lookup页面](https://www.twilio.com/lookup)。尝试输入一个电话号码，然后看看JSON响应对象。

![lookup-number.gif](https://www.twilio.com/blog/wp-content/uploads/2016/02/bSki52NVUzHJs3rjtvShdOiqNkuAlhBl578Qev9R9kFsJ0nE1IyZ5oFyjJjMxcembNO7qiYS5QDueaLYkUmBOJPU-JYzNaRCG87Yi1HuZZvwf2-ZhUh6fIi0xxCly8IzaRmtAGLk.png)

下面是不获得运营商信息的同类型的查找的一个快速的代码示例：
```py
from twilio.rest.lookups import TwilioLookupsClient

client = TwilioLookupsClient()
number = client.phone_numbers.get("15108675309")
print(number.national_format)  # => (510) 867-5309
```

基本功能是免费的，但是你可以通过进行运营商查找来获得更多的信息，这[花费不多](https://www.twilio.com/lookup#pricing)。运营商查找通常用于确定一个号码是否可以接受短信/彩信。

运营商查找包含更多有用信息，但是需要一个额外的参数：
```py
# Download the Python helper library from twilio.com/docs/python/install
from twilio.rest.lookups import TwilioLookupsClient

# Your Account Sid and Auth Token from twilio.com/user/account
# Store them in the environment variables:
# "TWILIO_ACCOUNT_SID" and "TWILIO_AUTH_TOKEN"
client = TwilioLookupsClient()

number = client.phone_numbers.get("15108675309", include_carrier_info=True)
print(number.carrier['name'])  # => Sprint Spectrum, L.P.
```

### 查找无效的电话号码

你可以把Twilio Lookup想象为一个电话簿REST API。在这个“网上电话簿”，电话号码作为唯一的ID。当你尝试查找一个不存在的电话号码时，你将得到一个404响应。

回过头再来看看Lookup主页，当你选中一个不存在的号码时：

![invalid-phone-lookup.gif](https://www.twilio.com/blog/wp-content/uploads/2016/02/ZixwNsMm7CVar6mxoRfvpPlv-XSUticVG5Pc3Lfv9Fk-JziTt0iJdzg6zG-pX_TZP3CqU_N2O64UdsFQNkBtsB4r84EDd98q2KVkWBCD14eIrNtoK2lRAOx8j7U3SN6EkEMBxK9s.png)

当Twilio Python库遇到一个404，它将抛出一个“TwilioRestException”异常。如果你修改我们当前的代码来查找一个不正确的电话号码，那么你将看到：

![Screen Shot 2016-02-01 at 4.42.10 PM.png](https://www.twilio.com/blog/wp-content/uploads/2016/02/lGYgVMRoiFS31cW1jvjJvqrXbnBOzvXkPazQTxLxUtMP62QLefs3Z5ZojZfqNJThLl8UWhtr8lxwhChtCIdGytUwxpCKDuZr4hdVlLNx5cLoN8v7fUtXWkHc1EZilXdK27ooMZxi.png)

此错误代码是[20404](https://www.twilio.com/docs/api/errors/20404)，所以如果我们想要编写一个函数来验证电话号码的有效性，那么我们可以检查是否引发了一个错误码为20404的异常。

创建一个名为“lookup.py”的文件，然后试试这个代码：
```py
# Download the Python helper library from twilio.com/docs/python/install
from twilio.rest.lookups import TwilioLookupsClient
from twilio.rest.exceptions import TwilioRestException

# Your Account Sid and Auth Token from twilio.com/user/account
# Store them in the environment variables:
# "TWILIO_ACCOUNT_SID" and "TWILIO_AUTH_TOKEN"
client = TwilioLookupsClient()


def is_valid_number(number):
    try:
        response = client.phone_numbers.get(number, include_carrier_info=True)
        response.phone_number  # If invalid, throws an exception.
        return True
    except TwilioRestException as e:
        if e.code == 20404:
            return False
        else:
            raise e

print(is_valid_number('19999999999'))  # False
print(is_valid_number('15108675309'))  # True
```

现在你有了一个可以在代码的任何地方使用的用于验证电话号码的函数。

### 展望未来

所以现在你知道了如何使用Twilio Lookup这个REST API电话簿。 你用它来验证电话号码，但是你也可以做一些很酷的事，譬如电话号码格式化和坚持一个号码是否可以接受短信。

你可能想要使用这些比较常见的情况的一种，但是我总是对其他人关于新的API的疯狂的想法感到兴奋不已。我已经迫不及待看到你使用Twilio Lookup做些什么了。

如果你有任何疑问，或者想要炫耀下你做的东东，不要犹豫，随时联系我：（下面是原文作者的信息^_^）

*   Email: sagnew@twilio.com
*   Twitter: [@Sagnewshreds](http://twitter.com/sagnewshreds)
*   Github: [Sagnew](https://github.com/sagnew)
