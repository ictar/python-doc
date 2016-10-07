原文：[Understanding Python Class Instantiation](http://amir.rachum.com/blog/2016/10/03/understanding-python-class-instantiation/)

---

Let’s say you have a class `Foo`:

```python
class Foo(object):
    def __init__(self, x, y=0):
        self.x = x
        self.y = y
```

What happens when you instantiate it (create an instance of that class)?

```python
f = Foo(1, y=2)
```

That call to `Foo` - what function or method is being called there? Most beginners and even many experienced Python programmers will immediately answer that `__init__` is called. If you stop to think about it for a second, this is far from being a correct answer.

`__init__` doesn’t return an object, but calling `Foo(1, y=2)` **does** return an object. Also, `__init__` expects a `self` parameter, but there is no such parameter when calling `Foo(1, y=2)`. There is something more complex at work here. In this post we’ll investigate together what happens when you instantiate a class in Python.

# Construction Sequence

Instantiating an object in Python consists of a few stages, but the beauty of it is that they are Pythonic in themselves - understanding the steps gives us a little bit more understanding of Python in general. `Foo` is a class, but classes in Python are objects too! Classes, functions, methods and instances are all objects and whenever you put parentheses after their name, you invoke their `__call__` method. So `Foo(1, y=2)` is equivalent to `Foo.__call__(1, y=2)`. That `__call__` is the one defined by `Foo`’s class. What is `Foo`’s class?

```python
>>> Foo.__class__
<class 'type'>
```

So `Foo` is an object of type `type` and calling `__call__` returns an object of class `Foo`. Next, let’s look at what the `__call__` method for `type` looks like. This method is fairly complicated, but we’ll try to simplify it. Below I have pasted both the CPython `C` and the PyPy Python implementation. I find that looking at the original source code is very interesting, but feel free to skip to my simplification of it below:

### CPython

[Link to source](https://github.com/python/cpython/blob/master/Objects/typeobject.c#L876).

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

[Link to source](https://bitbucket.org/pypy/pypy/src/87c5d21350cdad5ab2ff0c0b8e2e412f0ca85ddb/pypy/objspace/std/typeobject.py?at=default&amp;fileviewer=file-view-default#typeobject.py-599).

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

If we ignore error checking for a minute, then for regular class instantiation this is roughly equivalent to:

```python
def __call__(obj_type, *args, **kwargs):
    obj = obj_type.__new__(*args, **kwargs)
    if obj is not None and issubclass(obj, obj_type):
        obj.__init__(*args, **kwargs)
    return obj
```

`__new__` allocates memory for the object, constructs it as an “empty” object and then `__init__` is called to initialize it.

In conclusion:

1.  `Foo(*args, **kwargs)` is equivalent to `Foo.__call__(*args, **kwargs)`.
2.  Since `Foo` is an instance of `type`, `Foo.__call__(*args, **kwargs)` calls `type.__call__(Foo, *args, **kwargs)`.
3.  `type.__call__(Foo, *args, **kwargs)` calls `type.__new__(Foo, *args, **kwargs)` which returns `obj`.
4.  `obj` is then initialized by calling `obj.__init__(*args, **kwargs)`.
5.  `obj` is returned.

# Customization

Now we turn our attention to the `__new__` method. Essentially, it is the method responsible for actual object creation. We won’t go in detail into the base implementation of `__new__`. The gist of it is that it allocates space for the object and returns it. The interesting thing about `__new__` is that once you realize what it does, you can use it to customize instance creation in interesting ways. It should be noted that while `__new__` is a static method, you don’t need to declare it with `@staticmethod` - it is special-cased by the Python interpreter.

A nice example of the power of `__new__` is using it to implement a Singleton class:

```python
class Singleton(object):
    _instance = None
    
    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            cls._instance = super().__new__(cls, *args, **kwargs)
        return cls._instance
```

Then:

```python
>>> s1 = Singleton()
... s2 = Singleton()
... s1 is s2
True
```

Notice that in this Singleton implementation, `__init__` will be called each time we call `Singleton()`, so care should be taken.

Another similar example is implementing the [Borg design pattern](https://www.safaribooksonline.com/library/view/python-cookbook/0596001673/ch05s23.html):

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

Then:

```python
>>> b1 = Borg()
... b2 = Borg()
... b1 is b2
False
>>> b1.x = 8
... b2.x
8
```

One final note - the examples above show the power of `__new__`, but just because you _can_ use it, doesn’t mean you _should_:
> `__new__` is one of the most easily abused features in Python. It’s obscure, riddled with pitfalls, and almost every use case I’ve found for it has been better served by another of Python’s many tools. However, when you do need `__new__`, it’s incredibly powerful and invaluable to understand.
> 
> – Arion Sprague, [Python’s Hidden New](https://concentricsky.com/articles/detail/pythons-hidden-new)

It is rare to come across a problem in Python where the best solution was to use `__new__`. The trouble is that if you have a hammer, every problem starts to look like a nail - and you might “suddenly” come across many problem that `__new__` can solve. **Always prefer a better design over a shiny new tool**. `__new__` is not always better.

# Sources

*   [The Python Language Reference / Data Model](https://docs.python.org/3/reference/datamodel.html?highlight=__new__#basic-customization)
*   [Eli Bendersky / Python Object Creation Sequence](http://eli.thegreenplace.net/2012/04/16/python-object-creation-sequence)<div class="divider"></div>