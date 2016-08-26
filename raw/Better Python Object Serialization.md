原文：[Better Python Object Serialization](https://hynek.me/articles/serialization/)

---

The Python standard library is full of underappreciated gems. One of them
allows for simple and elegant function dispatching based on argument types.
This makes it perfect for serialization of arbitrary objects – for example to
JSON in web APIs and structured logs.

Who hasn’t seen it:

```python

    TypeError: datetime.datetime(...) is not JSON serializable
    
```

While this shouldn’t be a big deal, it is. The `json` module – that inherited
its API from `simplejson` – offers two ways to serialize objects:

  1. Implement a `default()` _function_ that takes an object and returns something that [`JSONEncoder`](https://docs.python.org/3/library/json.html#json.JSONEncoder) understands.
  2. Implement or subclass a `JSONEncoder` yourself and pass it as `cls` to the dump methods. You can implement it on your own or just override the `JSONEncoder.default()` _method_.

And since alternative implementations want to be drop-in, they imitate the
`json` module’s API to various degrees1.

## Expandability

What both approaches have in common is that they’re not expandable: adding
support for new types is not provided for. Your single `default()` fallback
has to know about all custom types you want to serialize. Which means you
either write functions like:

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

Which is painful since you have to add serialization for all objects in one
place2.

Alternatively you can try to come up with general solutions on your own like
Pyramid’s JSON renderer did in [`JSON.add_adapter`](http://docs.pylonsproject.
org/projects/pyramid/en/latest/narr/renderers.html#using-the-add-adapter-
method-of-a-custom-json-renderer) which uses the widely underappreciated
`zope.interface`’s adapter registry3.

Django on the other hand satisfies itself with a `DjangoJSONEncoder` that is a
subclass of `json.JSONEncoder` and knows how to encode dates, times, UUIDs,
and promises. But other than that, you’re on your own again. If you want to go
further with Django and web APIs, you’re probably already using the Django
REST framework anyway. They came up with a whole [serialization
system](http://www.django-rest-framework.org/api-guide/serializers/) that does
a lot more than just making data `json.dumps()`-ready.

Finally for the sake of completeness I feel like I have to mention my own
solution in [`structlog`](http://www.structlog.org/en/stable/) that I fiercely
hated from day one: adding a `__structlog__` method to your classes that
return a serializable representation in the tradition of `__str__`. Please
don’t repeat my mistake; hashtag [software clown](https://softwareclown.com).

* * *

Given how prevalent JSON is, it’s surprising that we have only siloed
solutions so far. What _I_ personally would like to have is a way to register
serializers in a central place but in a decentralized fashion that doesn’t
require any changes to my (or worse: third party) classes.

## Enter PEP 443

Turns out, Python 3.4 came with a nice solution to this problem in the form of
[PEP 443](https://www.python.org/dev/peps/pep-0443/): [`functools.singledispat
ch`](https://docs.python.org/3/library/functools.html#functools.singledispatch
) (also available on [PyPI](https://pypi.org/project/singledispatch/) for
legacy Python versions).

Put simply, you define a default function and then register additional
versions of that functions depending on the type of the first argument:

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

Now you can call `to_serializable()` on `datetime` instances too and single
dispatch will pick the correct function:

```python

    >>> json.dumps({"msg": "hi", "ts": datetime.now()},
    ...            default=to_serializable)
    '{"ts": "2016-08-20T13:08:59.153864Z", "msg": "hi"}'
    
```

This gives you the power to put your serializers wherever you want: along with
the classes, in a separate module, or along with JSON-related code? _You_
choose! But your _classes_ stay clean and you don’t have a huge `if-elif-else`
branch that you cargo-cult between your projects.

## Going Further

Obviously the utility of `@singledispatch` goes far beyond JSON. Binding
different behaviors to different types in general and object serialization in
particular are universally useful4. Some of my proofreaders mentioned they
tried a ghetto approximation using `dict`s of classes to callables and other
similar atrocities.

In other words, `@singledispatch` just may be the function that you’ve been
missing although it was there all along.

P.S. Of course there’s also a `*multiple*dispatch` on
[PyPI](https://pypi.org/project/multipledispatch/).

## Footnotes

* * *

  1. However, from the popular ones: [UltraJSON](https://github.com/esnme/ultrajson) doesn’t support custom object serialization at all and [`python-rapidjson`](https://github.com/kenrobbins/python-rapidjson) only supports the `default()` function. ↩︎
  2. Although as you can see it’s manageable with `attrs`; maybe [you should use `attrs`](https://glyph.twistedmatrix.com/2016/08/attrs.html)! ↩︎
  3. Unfortunately the API Pyramid uses is currently [undocumented](https://github.com/zopefoundation/zope.interface/issues/41) after being transplanted from [`zope.component`](https://docs.zope.org/zope.component/). ↩︎
  4. I’ve been told the original incentive for adding single dispatch to the standard library was a more elegant reimplementation of [`pprint`](https://docs.python.org/3.5/library/pprint.html) (that never happened). ↩︎
