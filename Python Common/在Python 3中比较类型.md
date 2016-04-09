原文：[Comparing types in Python 3](http://eli.thegreenplace.net/2016/comparing-types-in-python-3/)

---

好啦，你要开始进行一些Python元编程，然后发现自己需要对类型序列进行排序。不是对象哦，是_类型_。让我们启动一个Python 2提示来试一试：
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

看起来还不错，那么在Python 3中呢？
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

哎呀，怎么回事？稍微深入挖掘一下，可以快速的发现，Python 3移除了[type对象](http://eli.thegreenplace.net/2012/03/30/python-objects-types-classes-and-instances-a-glossary)的比较。在Python 2中，`type`的类型具有`tp_richcompare`函数来进行比较：
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

需要注意的是，type对象是通过数值比较它们的`PyObject`指针来进行比较的。还要注意，在Python 3中，关于type不等的弃用警告消失了。事实上，如果我们窥窃Python 3中的`typeobject.c`，那么你会发现，`tp_richcompare`函数是空的。

但是可怜的元编程程序员要怎么办呢？还可以使用元类呀！

实际上，解决方法非常简单。对于那些定义了`__lt__`的类型，Python都需要生成完整的比较方法。再次说明，不是对象，是_类型_。在`Foo`、`Bar`及其友元中定义`__lt__`将使得这些类型的对象可以进行比较。我们现在不关心这一点。我们想让_类型_本身可比较，因此，必须在`Foo`的类型（默认是`type`）中定义`__lt__`；我们可以使用一个元类来进行修改。

这里是一个可能的解决方案：
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

啊，好多了。要让某些类型家族可进行比较，我们提供了一个定义了`__lt__`的公共元类。当Python调用`Foo < Bar` (在排序的过程中被调用), `_ComparableTypeMeta.__lt__`发挥作用，它会基于它们的`id`对类型进行比较 - 类似于Python 2中的地址比较。

与任何涉及元类的东东一样，这是一个“强大的特性”，应该谨慎使用。

为了公平起见，这个特殊的问题有一个更为简单的解决方法。我们可以只是传递一个`key`参数给`sorted`，就像下面一样：
```py
sorted([Bar, Baz, Foo], key=id)
```

然后，它将在不需要特殊的元类的情况下进行工作。然而，`sorted`不仅仅是我们可能想要排序和比较类型的唯一的地方，而且，它可能会被掩埋在一些处理所有种对象的通用代码中；另外，基于元类的方法会更加通用。

欲了解更多信息，请参阅：

*   [PEP 207](https://www.python.org/dev/peps/pep-0207/)，介绍了在Python 2.1中重新出现的富比较。
*   [Python 3中的富比较方法](https://docs.python.org/3.4/reference/datamodel.html#object.__lt__)文档。
*   [Python 3 C API中的type对象](https://docs.python.org/3/c-api/typeobj.html)。
*   [Python对象，类型，类和实例 —— 词汇](http://eli.thegreenplace.net/2012/03/30/python-objects-types-classes-and-instances-a-glossary).
*   [Python的基本类型 - 图表](http://eli.thegreenplace.net/2012/04/03/the-fundamental-types-of-python-a-diagram)。
*   [Python对象创建顺序](http://eli.thegreenplace.net/2012/04/16/python-object-creation-sequence)。
*   [Python元类的一些例子](http://eli.thegreenplace.net/2011/08/14/python-metaclasses-by-example)。

最后，如果你设法挖掘到了关于决定移除`tp_richcompare`的原始讨论（也许在python-dev上，或者是由于一个问题），请让我知道。对于是什么导致了这个决定，我很好奇。