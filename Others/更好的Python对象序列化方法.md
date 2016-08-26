原文：[Better Python Object Serialization](https://hynek.me/articles/serialization/)

---

Python标准库充满了蒙尘的宝石。其中一个允许基于参数类型的简单优雅的函数调度。这使得它对任意对象的序列化是完美的 —— 例如，web API和结构化日志的JSON化。

谁没有看过它：

```python

    TypeError: datetime.datetime(...) is not JSON serializable
    
```

虽然这应该不是一个大问题，但是它是。`json`模块 —— 从`simplejson`继承了其API —— 提供了两种序列化对象的方法：

  1. 实现一个`default()` _函数_，该函数接收一个对象，然后返回[`JSONEncoder`](https://docs.python.org/3/library/json.html#json.JSONEncoder)能够理解的东东。
  2. 自己实现或子类化`JSONEncoder`，然后将其当做`cls`传递给dump方法。你可以自己实现它，或者重载`JSONEncoder.default()` _方法_。

而由于替代实现想要混进去，所以它们不同程度地模仿了`json`模块的API。[1]

## 可扩展性

这两种方法的共同点是，它们是不可扩展的：未提供新类型的添加支持。你单一的`default()`备用必须知道所有你想要序列化的类型。这意味着你要么写像这样的函数：

```python

    def to_serializable(val):
        if isinstance(val, datetime):
            return val.isoformat() + "Z"
        elif isinstance(val, enum.Enum):
            return val.value
        elif attr.has(val.__class__):
            return attr.asdict(val)
        elif isinstance(val, Exception):
            return {
                "error": val.__class__.__name__,
                "args": val.args,
            }
        return str(val)
    
```

这很痛苦，因为你必须在同一个地方为所有对象添加序列化。[2]

或者，你可以尝试自己拿出解决方案，就如Pyramid的JSON渲染器在[`JSON.add_adapter`](http://docs.pylonsproject.org/projects/pyramid/en/latest/narr/renderers.html#using-the-add-adapter-method-of-a-custom-json-renderer)中做的那样，它使用了待在冷宫中的`zope.interface`的适配器注册表。[3]

另一方面，Django使用了一个`DjangoJSONEncoder`来解决，这是`json.JSONEncoder`的一个子类，并且它知道如何解码日期、时间、UUID，并保证（可以）。但除此之外，你又要靠自己了。如果你想更进一步使用Django和web API，那么，反正你可能已经使用Django REST框架了。它们提出了一个完整的[序列化系统](http://www.django-rest-framework.org/api-guide/serializers/)，这个系统可不仅仅做了让数据准备好`json.dumps()`。

最后，为了完整起见，我觉得我必须提一提自己在[`structlog`](http://www.structlog.org/en/stable/)的解决方法，这一方法从一开始我就深深地讨厌：添加一个`__structlog__`方法到你的类中，它按照`__str__`返回一个序列化表示。请不要重蹈我的覆辙；标签 [软件小丑](https://softwareclown.com)。

* * *

鉴于JSON相当普遍，令人惊讶的是，目前，我们只有孤立的解决方案。我个人希望的是，有一种方法，可以在一个地方统一注册序列器，但是以一种分散的方式，而无需对我的（或者更糟糕：第三方）类进行任何改变。

## 进入PEP 443

原来，对这个问题，Python 3.4想出了一个很好的解决方法，参见[PEP 443](https://www.python.org/dev/peps/pep-0443/): [`functools.singledispatch`](https://docs.python.org/3/library/functools.html#functools.singledispatch) (对于Python遗留版本，也可见[PyPI](https://pypi.org/project/singledispatch/))。

简单地说，定义一个默认的函数，然后基于第一个参数类型，注册该函数的额外版本：

```python

    from datetime import datetime
    from functools import singledispatch
    
    @singledispatch
    def to_serializable(val):
        """Used by default."""
        return str(val)
        
    @to_serializable.register(datetime)
    def ts_datetime(val):
        """Used if *val* is an instance of datetime."""
        return val.isoformat() + "Z"
    
```

现在，你也可以在`datetime`实例上调用`to_serializable()`，而单一的调度将选择正确的函数：

```python

    >>> json.dumps({"msg": "hi", "ts": datetime.now()},
    ...            default=to_serializable)
    '{"ts": "2016-08-20T13:08:59.153864Z", "msg": "hi"}'
    
```

这给了你将你的序列器改造成你所想要的权力：和类一起，在一个单独的模块，或者和JSON相关的代码放在一起？任君选择！但是你的_类_保持干净，你的项目之间没有庞大的`if-elif-else`分支。

## 更进一步

显然，`@singledispatch`的适用范围不仅是JSON。一般的绑定不同行为到不同类型上 ，以及特别的对象序列化是普遍有用的[4]。我的一些校对人员提到，他们使用在可调用对象上使用类的`dict`，尝试了贫民窟近似和其他类似的暴行。(Ele注，原文是“Some of my proofreaders mentioned they tried a ghetto approximation using dicts of classes to callables and other similar atrocities.”。有更好的翻译，欢迎贡献~~)

换句话说，`@singledispatch`只可能是那个你一直想要的函数，虽然它一直都在。

P.S. 当然，在[PyPI](https://pypi.org/project/multipledispatch/)上，还有一个`*multiple*dispatch`。

## 脚注

* * *

  1. 然而，有个流行的替代实现：[UltraJSON](https://github.com/esnme/ultrajson)完全不支持自定义对象序列化，而[`python-rapidjson`](https://github.com/kenrobbins/python-rapidjson)只支持`default()`函数。
  2. 虽然你可以看到，使用`attrs`可管理；也许[你应该使用`attrs`](https://glyph.twistedmatrix.com/2016/08/attrs.html)! 
  3. 不幸的是，在从[`zope.component`](https://docs.zope.org/zope.component/)移植过来后，当前API Pyramid的使用是[无正式文档的](https://github.com/zopefoundation/zope.interface/issues/41)。
  4. 有人告诉我，添加单一调度到标准库的原始动力是[`pprint`](https://docs.python.org/3.5/library/pprint.html) 的一个更加优雅的重新实现(从未发生过)。
