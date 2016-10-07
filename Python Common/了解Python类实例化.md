原文：[Understanding Python Class Instantiation](http://amir.rachum.com/blog/2016/10/03/understanding-python-class-instantiation/)

---

假设你有一个类`Foo`：

```python
class Foo(object):
    def __init__(self, x, y=0):
        self.x = x
        self.y = y
```

在你实例化它的时候（创建该类的实例），会发生什么呢？

```python
f = Foo(1, y=2)
```

对`Foo`的调用 - 这里会调用什么函数或方法呢？大多数的新手或者甚至许多有经验的Python程序员将会立即回答：调用了`__init__`。如果你停下来再想想，会发现这离正确的答案还远着呢。

`__init__`并不范湖一个对象，但是调用`Foo(1, y=2)` **的确**会返回一个对象。另外，`__init__`需要一个`self`参数，但在调用`Foo(1, y=2)`的时候，并没有这样的一个参数。这里的工作会更复杂些。在这篇文章中，我们将一起调查，当你在Python中实例化一个类的时候，发生了什么。

# 构造链

在Python中初始化一个对象由几个阶段组成，但它的美在于它们自身是Pythonic的 - 了解这些步骤让我们更了解一般情况下的Python。`Foo`是一个类，但Python中的类也是对象！类，函数，方法和实例都是对象，无论何时你在它们名字后面放一对括号，都会调用它们的`__call__`方法。因此，`Foo(1, y=2)`等价于`Foo.__call__(1, y=2)`。这里的`__call__`是由`Foo`的类定义的一个方法。`Foo`的类是什么呢？

```python
>>> Foo.__class__
<class 'type'>
```

因此，`Foo`是类型`type`的一个对象，而调用`__call__`返回类`Foo`的一个对象。接下来，看看`type`的`__call__`方法长得啥样。这个方法是相当复杂的，但我们会试着简化它。下面，我贴了CPython `C`和PyPy Python这两个实现。我发现看原始源代码是非常有趣的，但请随意跳到下面我对它的简化：

### CPython

[链接到源](https://github.com/python/cpython/blob/master/Objects/typeobject.c#L876)。

```python
static PyObject *
type_call(PyTypeObject *type, PyObject *args, PyObject *kwds)
{
    PyObject *obj;

    if (type->tp_new == NULL) {
        PyErr_Format(PyExc_TypeError,
                     "cannot create '%.100s' instances",
                     type->tp_name);
        return NULL;
    }

    obj = type->tp_new(type, args, kwds);
    obj = _Py_CheckFunctionResult((PyObject*)type, obj, NULL);
    if (obj == NULL)
        return NULL;

    /* Ugly exception: when the call was type(something),
       don't call tp_init on the result. */
    if (type == &PyType_Type &&
        PyTuple_Check(args) && PyTuple_GET_SIZE(args) == 1 &&
        (kwds == NULL ||
         (PyDict_Check(kwds) && PyDict_Size(kwds) == 0)))
        return obj;

    /* If the returned object is not an instance of type,
       it won't be initialized. */
    if (!PyType_IsSubtype(Py_TYPE(obj), type))
        return obj;

    type = Py_TYPE(obj);
    if (type->tp_init != NULL) {
        int res = type->tp_init(obj, args, kwds);
        if (res < 0) {
            assert(PyErr_Occurred());
            Py_DECREF(obj);
            obj = NULL;
        }
        else {
            assert(!PyErr_Occurred());
        }
    }
    return obj;
}
```

### PyPy

[[链接到源](https://bitbucket.org/pypy/pypy/src/87c5d21350cdad5ab2ff0c0b8e2e412f0ca85ddb/pypy/objspace/std/typeobject.py?at=default&amp;fileviewer=file-view-default#typeobject.py-599).

```python
def descr_call(self, space, __args__):
    promote(self)
    # invoke the __new__ of the type
    if not we_are_jitted():
        # note that the annotator will figure out that self.w_new_function
        # can only be None if the newshortcut config option is not set
        w_newfunc = self.w_new_function
    else:
        # for the JIT it is better to take the slow path because normal lookup
        # is nicely optimized, but the self.w_new_function attribute is not
        # known to the JIT
        w_newfunc = None
    if w_newfunc is None:
        w_newtype, w_newdescr = self.lookup_where('__new__')
        if w_newdescr is None:    # see test_crash_mro_without_object_1
            raise oefmt(space.w_TypeError, "cannot create '%N' instances",
                        self)
        w_newfunc = space.get(w_newdescr, self)
        if (space.config.objspace.std.newshortcut and
            not we_are_jitted() and
            isinstance(w_newtype, W_TypeObject)):
            self.w_new_function = w_newfunc
    w_newobject = space.call_obj_args(w_newfunc, self, __args__)
    call_init = space.isinstance_w(w_newobject, self)

    # maybe invoke the __init__ of the type
    if (call_init and not (space.is_w(self, space.w_type) and
        not __args__.keywords and len(__args__.arguments_w) == 1)):
        w_descr = space.lookup(w_newobject, '__init__')
        if w_descr is not None:    # see test_crash_mro_without_object_2
            w_result = space.get_and_call_args(w_descr, w_newobject,
                                               __args__)
            if not space.is_w(w_result, space.w_None):
                raise oefmt(space.w_TypeError,
                            "__init__() should return None")
    return w_newobject
```

* * *

如果我们忽略一下错误检查，那么对于常规的类实例化，它大致相当于：

```python
def __call__(obj_type, *args, **kwargs):
    obj = obj_type.__new__(*args, **kwargs)
    if obj is not None and issubclass(obj, obj_type):
        obj.__init__(*args, **kwargs)
    return obj
```

`__new__`为对象分配内存，将它构造成一个“空的”对象，然后调用`__init__`来实例化它。

总结：

1.  `Foo(*args, **kwargs)`等价于`Foo.__call__(*args, **kwargs)`。
2.  由于`Foo`是`type`的一个实例，因此`Foo.__call__(*args, **kwargs)`调用`type.__call__(Foo, *args, **kwargs)`。
3.  `type.__call__(Foo, *args, **kwargs)`调用`type.__new__(Foo, *args, **kwargs)`，它返回`obj`。
4.  然后，通过调用`obj.__init__(*args, **kwargs)`来实例化`obj`。
5.  返回`obj`。

# 自定义

现在，我们把注意力放在`__new__`方法上。本质上，它是负责实际对象创建的方法。我们不会进入`__new__`的基础实现细节。它的要点是，为对象分配空间，并返回它。对于`__new__`，有趣的是，一旦你意识到它做了什么，那么你就能用它以好玩的方式自定义实例创建。应当指出的是，虽然`__new__`是一个静态方法，但是你无须使用`@staticmethod`来声明它 - 对于Python解释器来说，它是一个特例。

`__new__`的能力的一个很好的例子是，将它用来实现一个单例类：

```python
class Singleton(object):
    _instance = None
    
    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            cls._instance = super().__new__(cls, *args, **kwargs)
        return cls._instance
```

然后：

```python
>>> s1 = Singleton()
... s2 = Singleton()
... s1 is s2
True
```

注意，在这个单例实现中，每次我们调用`Singleton()`的时候，都会调用`__init__`，因此必须小心。

另一个类似的例子是实现[Borg设计模式](https://www.safaribooksonline.com/library/view/python-cookbook/0596001673/ch05s23.html)：

```python
class Borg(object):
    _dict = None

    def __new__(cls, *args, **kwargs):
        obj = super().__new__(cls, *args, **kwargs)
        if cls._dict is None:
            cls._dict = obj.__dict__
        else:
            obj.__dict__ = cls._dict
        return obj
```

然后：

```python
>>> b1 = Borg()
... b2 = Borg()
... b1 is b2
False
>>> b1.x = 8
... b2.x
8
```

最后要注意 - 上面的例子显示了`__new__`的强大，但是只因为你_可以_使用它，并不意味着你_应该_使用它：

> `__new__`是Python中最容易滥用的特性之一。它晦涩难懂，缺陷百出，并且几乎我所找到的每一个关于它的用例都已经被Python的许许多多个工具中的某个更好的完成了。然后，在你真的需要`__new__`的时候，它是令人难以置信的强大和难能可贵。
> 
> – Arion Sprague, [Python的隐藏new](https://concentricsky.com/articles/detail/pythons-hidden-new)

在Python中遇到一个最好的解决方法是使用`__new__`的问题是罕见的。麻烦的是，如果你有了一个锤子，那么每个问题都开始看起来像一个钉子 —— 并且你可能“突然”碰到许多`__new__`可以解决的问题。 **永远不要重造轮子**。`__new__`并非总是更好的方法。

# 来源

*   [Python语言参考 / 数据模型](https://docs.python.org/3/reference/datamodel.html?highlight=__new__#basic-customization)
*   [Eli Bendersky / Python对象创建链](http://eli.thegreenplace.net/2012/04/16/python-object-creation-sequence)