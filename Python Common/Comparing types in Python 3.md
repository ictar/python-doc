原文：[Comparing types in Python 3](http://eli.thegreenplace.net/2016/comparing-types-in-python-3/)

---

Say you're set to do some Python metaprogramming, and find yourself in a need of
sorting a sequence of types. Not objects, _types_. Let's fire up a Python 2
prompt to try it:
```py
$ python2.7
Python 2.7.6 (default, Jun 22 2015, 17:58:13)
[GCC 4.8.2] on linux2
>>> class Foo(object): pass
...
>>> class Bar(object): pass
...
>>> class Baz(object): pass
...
>>> sorted([Bar, Baz, Foo])
[<class '__main__.Foo'>, <class '__main__.Bar'>, <class '__main__.Baz'>]
```

Looks good. How about Python 3?
```py
$ python3.4
Python 3.4.3+ (3.4:4255ca2f5314, Apr  2 2015, 05:00:06)
[GCC 4.8.2] on linux
>>> class Foo: pass
...
>>> class Bar: pass
...
>>> class Baz: pass
...
>>> sorted([Bar, Baz, Foo])
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unorderable types: type() < type()
```

Oops, what's going on? A bit of digging quickly reveals that comparisons for
[type objects](http://eli.thegreenplace.net/2012/03/30/python-objects-types-classes-and-instances-a-glossary)
were removed in Python 3. In Python 2, the type of `type` has the
`tp_richcompare` slot populated with:
```py
static PyObject*
type_richcompare(PyObject *v, PyObject *w, int op)
{
    PyObject *result;
    Py_uintptr_t vv, ww;
    int c;

    /* Make sure both arguments are types. */
    if (!PyType_Check(v) || !PyType_Check(w) ||
        /* If there is a __cmp__ method defined, let it be called instead
           of our dumb function designed merely to warn.  See bug
           #7491. */
        Py_TYPE(v)->tp_compare || Py_TYPE(w)->tp_compare) {
        result = Py_NotImplemented;
        goto out;
    }

    /* Py3K warning if comparison isn't == or !=  */
    if (Py_Py3kWarningFlag && op != Py_EQ && op != Py_NE &&
        PyErr_WarnEx(PyExc_DeprecationWarning,
                   "type inequality comparisons not supported "
                   "in 3.x", 1) < 0) {
        return NULL;
    }

    /* Compare addresses */
    vv = (Py_uintptr_t)v;
    ww = (Py_uintptr_t)w;
    switch (op) {
    case Py_LT: c = vv <  ww; break;
    case Py_LE: c = vv <= ww; break;
    case Py_EQ: c = vv == ww; break;
    case Py_NE: c = vv != ww; break;
    case Py_GT: c = vv >  ww; break;
    case Py_GE: c = vv >= ww; break;
    default:
        result = Py_NotImplemented;
        goto out;
    }
    result = c ? Py_True : Py_False;

  /* incref and return */
  out:
    Py_INCREF(result);
    return result;
}
```

Note that type objects are compared by numerically comparing their `PyObject`pointers. Also note the deprecation warning about type inequalities going away
in Python 3. And indeed, if we peek into `typeobject.c` in Python 3, the
`tp_richcompare` slot is empty.

But what is a poor meta-programmer to do? Use metaclasses, what else!

The solution is very simple, actually. All Python needs to generate full rich
comparisons is for the types to have `__lt__` defined. Again, not the objects,
_types_. Defining `__lt__` in `Foo`, `Bar` and friends will make objects
of these types comparable. We're not concerned with that right now. We want the
_types_ themselves to be comparable. Therefore, `__lt__` has to be defined
in the type of `Foo`, which is `type` by default; we can use a metaclass
to amend that.

Here's one possible solution:
```py
>>> class _ComparableTypeMeta(type):
...   def __lt__(self, other):
...     return id(self) < id(other)
...
>>> class Foo(metaclass=_ComparableTypeMeta): pass
...
>>> class Bar(metaclass=_ComparableTypeMeta): pass
...
>>> class Baz(metaclass=_ComparableTypeMeta): pass
...
>>> sorted([Bar, Baz, Foo])
[<class '__main__.Foo'>, <class '__main__.Bar'>, <class '__main__.Baz'>]
```

Ah, much better. To make some family of types comparable, we provide a common
metaclass that defines `__lt__`. When Python invokes `Foo &lt; Bar` (which is
called in the process of sorting), `_ComparableTypeMeta.__lt__` fires, and
compares the types based on their `id` - similarly to the address comparison
in Python 2.

As with anything involving metaclasses, this is a "power feature" that should
be used sparingly.

To be fair, there's a much simpler solution for this particular problem. We can
just pass a `key` argument to `sorted` as follows:
```py
sorted([Bar, Baz, Foo], key=id)
```

And it will then work without special metaclasses. However, `sorted` is not
the only place where we may want to sort and compare types, and moreover,
it may be buried in some generic code that handles all kinds of objects; the
metaclass-based approach is more general.

For more information see:

*   [PEP 207](https://www.python.org/dev/peps/pep-0207/) that introduced rich
comparisons back in Python 2.1.
*   Documentation of [rich comparison methods in Python 3](https://docs.python.org/3.4/reference/datamodel.html#object.__lt__).
*   [Type objects in the Python 3 C API](https://docs.python.org/3/c-api/typeobj.html).
*   [Python objects, types, classes and instances - a glossary](http://eli.thegreenplace.net/2012/03/30/python-objects-types-classes-and-instances-a-glossary).
*   [The fundamental types of Python - a diagram](http://eli.thegreenplace.net/2012/04/03/the-fundamental-types-of-python-a-diagram).
*   [Python object creation sequence](http://eli.thegreenplace.net/2012/04/16/python-object-creation-sequence).
*   [Python metaclasses by example](http://eli.thegreenplace.net/2011/08/14/python-metaclasses-by-example).

Finally, please let me know if you manage to dig up the original discussion (on
python-dev perhaps, or an issue) where removing `tp_richcompare` was decided
upon. I'm curious to see what led to the decision.