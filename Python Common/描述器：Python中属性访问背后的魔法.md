原文：[Descriptors: The magic behind attribute access in Python](http://nbviewer.jupyter.org/github/akittas/presentations/blob/master/pythess/descriptors/descriptors.ipynb)

---

# 什么是封装？ (IMNSHO)

  1. _封装_**不是**关于隐藏数据。
  2. _访问控制_**才是**关于隐藏数据。
  3. 封装和访问控制是两个不同的独立的事情。
    * You **don't need** access control to have encapsulation.
    * You can encapsulate behavior **without** having to restrict access.
  4. Encapsulation separates the concept of **what something does** from **how it is implemented**.
  5. Encapsulation decouples a programming construct's **public interface/API** from its **implemenation**.
  6. When calling code wants to retrieve a value, it should not depend on from where the value comes. Internally, the class can store the value in a field or retrieve it from some external resource (such as a file or a database). Perhaps the value is not stored at all, but calculated on-the-fly. This should not matter to the calling code.

# 在我们开始之前：Python中的属性里的下划线

  1. Single underscore before a name (e.g. `_foo`)
    * Used as a convention, these attributes should be treated as a non-public part of the API (whether it is a function, a method or a data member) and considered an implementation detail and subject to change without notice (source: [Python documentation](https://docs.python.org/3/tutorial/classes.html#tut-private)).
    * It's more than a convention and actually does mean something to the interpreter; if you `from <module/package> import *`, none of the names that start with an `_` will be imported unless the module's/package's `__all__` list explicitly contains them.
  2. Double underscore before a name (e.g. `__foo`)
    * This is not a convention, any identifier of the form `__foo` (at least two leading underscores, at most one trailing underscore) is textually replaced with `_classname__foo`, where classname is the current class name with leading underscore(s) stripped. This is called _name mangling_. (source: [Python documentation](https://docs.python.org/3/tutorial/classes.html)).
    * Name mangling is helpful for letting subclasses override methods without breaking intraclass method calls.
  3. Double underscore before and after a name (e.g. `__foo__`)
    * Methods that use this naming format are called _special_ or _magic_ methods and are automatically invoked when certain syntax is used. We typically override these methods to implement the desired behaviour in classes (e.g. constructors, operator overloading, indexing etc).
    * _Special_ attributes that provide access to the implementation and are not intended for general use. Examples from class special attributes: `__name__` is the class name, `__module__` is the module name in which the class was defined, `__dict__` is the dictionary containing the class’s namespace, `__bases__` is a tuple containing the base classes.

In [1]:



    # name mangling mechanism

    class Mapping:
        def __init__(self, iterable):
            self.items_list = []
            # self.__update inside the class is equivalent to self._Mapping__update
            # the same function will be called even if __update is overridden in inheriting classes
            self.__update(iterable)

        def __update(self, iterable):
            for item in iterable:
                self.items_list.append(item)

    class MappingSub(Mapping):
        def __update(self, keys, values):
            # provides new signature for __update() but does not break __init__()
            for item in zip(keys, values):
                self.items_list.append(item)


In [2]:



    m = Mapping([1, 2])
    ms = MappingSub([1,2])
    print('__update' in dir(m), '__update' in dir(ms))

    m._Mapping__update([3, 4])
    print(m.items_list)

    ms._Mapping__update([3, 4])  # call update function of Mapping class
    ms._MappingSub__update([5, 6], ['five', 'six'])  # call update of MappingSub class
    print(ms.items_list)



    False False
    [1, 2, 3, 4]
    [1, 2, 3, 4, (5, 'five'), (6, 'six')]


# 一个属性是什么？

  * Quite simply, an attribute is a way to get from one object to another.
  * Apply the power of the almighty dot `objectname.attributename` and voila! you now have the handle to another object.
  * You also have the power to create attributes, by assignment: `objectname.attributename = anotherobject`.
  * Which object does an attribute access return, though? And where does the object set as an attribute end up?

取决于编程语言：

  * You don't have any control to attribute access (Java plebs).
  * You control attribute access through properties (C# cool kids).
  * You can completely customize attribute access in addition to properties (Python master race).

![](http://nbviewer.jupyter.org/github/akittas/presentations/blob/master/pythe
ss/descriptors/figures/brace_attrs.jpg)

# 实例属性访问

  * When we access an instance we actually call its `__getattribute__` method, i.e. `a.x -> a.__getattribute__(x)`.
  * `__getattribute__` has an order of priority that describes where to look for attributes and how to react to them.
  * Classes and instances have a `__dict__` where user provided attributes are stored and looked up.
    * Python provides extra attributes, most of which are not stored in `__dict__` (e.g. special methods).
    * `__dict__` is looked up first and this is how we override special methods.
    * We can also override this behavior to save memory for classes with a few fields using `__slots__`. However we cannot add new attributes to `__slots__`.

In [3]:



    class B:
        x = 1

    class A(B):
        y = 2
        def __getattr__(self, value):
            return str(value)

    a = A()
    print("a.x: {}, a.y: {}".format(a.x, a.y))  # x from B, y from A
    a.y = 3
    print("a.x: {}, a.y: {}, A.y: {}".format(a.x, a.y, A.y))  # x from B, y from a (overrides y in A)
    print("a.z: {}".format(a.z))  # call __getattr__

    print(A.__dict__)
    print(a.__dict__)



    a.x: 1, a.y: 2
    a.x: 1, a.y: 3, A.y: 2
    a.z: z
    {'__module__': '__main__', 'y': 2, '__getattr__': <function A.__getattr__ at 0x0000022EE39A7EA0>, '__doc__': None}
    {'y': 3}


# 描述器协议

Raymond Hettinger ([Python文档](https://docs.python.org/3/howto/descriptor.html)):

> In general, a descriptor is an object attribute with "binding behavior", one
whose attribute access has been overridden by methods in the descriptor
protocol.

  * Those methods are `__get__`, `__set__` and `__delete__`. If any of those methods are defined for an object, it is said to be a **descriptor**.
  * Only one of the methods _needs_ to be implemented in order to be considered a descriptor, but any number of them _can_ be implemented.
  * There are two types of descriptors based on which sets of these methods are implemented: **data** and **non-data** descriptors.
    1. A **data** descriptor implements at least `__set__` or `__delete__`, but can include both. They also often include `__get__`, since it's rare to want to set something without also being able to get it too.
    2. A **non-data** descriptor only implements `__get__`. If it adds `__set__` or `__delete__`to its method list, it becomes a data descriptor.

# `__get__(self, instance, owner)`

  1. `self` is the descriptor instance.
  2. `owner` is the class the descriptor is accessed _from_.
    * When you call `A.x`, where `x` is a descriptor object with `__get__`, it's called with `A` as owner and `instance` as `None`.
    * This lets the descriptor know that `__get__` is being called from a _class_, not an _instance_.
    * `A.x` is translated to `A.__dict__['x'].__get__(None, A)`.
  3. `instance` is the instance that the descriptor is accessed _from_.
    * If the discriptor is accessed from an _instance_ it receives it as `instance` and the class of the instance as `owner`.
    * `a.x` is translated to `type(a).__dict__['x'].__get__(a, type(a))`
    * Note that the call starts with `type(a)`, not just `a`, because descriptors are stored on _classes_ not _instances_.

两个要点：

  1. In order to be able to apply per-instance as well as per-class functionality, descriptors are given `instance` and `owner` (the class of the instance).
  2. It is not the _instance_ that the descriptor is being called from, but instead, the `instance` _parameter_ is the instance the descriptor is being called from. It is actually being called from the instance class.

# `__set__(self, instance, value)`

  1. `__set__` does not have an owner parameter that accepts a class and does not need it, since data descriptors are generally designed for storing per-instance data.
  2. `A.x = value` does not get translated to anything; `value` replaces the descriptor object stored in `x` (however, see note below).
  3. `a.x = value` is translated to `type(a).__dict__['x'].__set__(a, value)`

# `__delete__(self, instance)`

  1. invoked when `del a.x` is called.
  2. `del a.x` is translated to `type(a).__dict__['x'].__delete__(a)`

**Note**: If we want a descriptor's `__set__` or `__delete__` methods to work from the _class_ level, the descriptor must be created on the class's _metaclass_. When doing so, everything that refers to `owner` is referring to the _metaclass_, while a reference to `instance` refers to the _class_. After all, classes are just instances of metaclasses.

# 实例和类属性访问

  * Descriptors are invoked by the `__getattribute__` method.
  * Overriding `__getattribute__` prevents automatic descriptor calls.
  * Class attribute access still uses `__getattribute__`, but it's the one defined on its _metaclass_.
  * Priorities when an _instance_ attribute is looked up:
    1. Data descriptors in its class (up the MRO).
    2. Instance attributes.
    3. Non-data descriptors in its class / class attributes (up the MRO).
    4. The `__getattr__` method.
  * Priorities when an _class_ attribute is looked up:
    1. Data descriptors in its metaclass (up the MRO).
    2. Class attributes (up the MRO).
    3. Non-data descriptors in its metaclass / metaclass attributes (up the MRO).
    4. The `__getattr__` method.

# 实例属性访问优先级 (`a.x`)

  1. Look in the class `__dict__`, working up the MRO.
    * If found, check if it's a data descriptor.
      * If it has a `__get__` method, call it and return the result.
  2. Look in the instance `__dict__`.
    * If found, return the value in `__dict__`.
  3. Check class `__dict__` again, working up the MRO.
    * If found, check if it's a descriptor.
      * If it has a `__get__` method, call it and return the result.
      * If it doesn't have a `__get__` method, return the descriptor object itself.
    * If found and not a descriptor, return the value in `__dict__`.
  4. Call `__getattr__` if it exists and return the result.
  5. If everything up to this point has failed, raise `AttributeError`.

# 类属性访问优先级 (`A.x`)

  1. Look in the metaclass `__dict__`, working up the MRO.
    * If found, check if it's a data descriptor.
      * If it has a `__get__` method, call it and return the result.
  2. Look in the class `__dict__`, working up the MRO.
    * If found, check if it's a descriptor.
      * If it has a `__get__` method, call it and return the result.
      * If it doesn't have a `__get__` method, return the descriptor object itself.
    * If found and not a descriptor, return the value in `__dict__`.
  3. Check metaclass `__dict__` again, working up the MRO.
    * If found, check if it's a descriptor.
      * If it has a `__get__` method, call it and return the result.
      * If it doesn't have a `__get__` method, return the descriptor object itself.
    * If found and not a descriptor, return the value in `__dict__`.
  4. Call `__getattr__` if it exists and return the result.
  5. If everything up to this point has failed, raise `AttributeError`.

# 实例属性访问优先级：

## `__set__` &amp; `__delete__` (`a.x = value` &amp; `del a.x`)

  1. Look in the class `__dict__`, working up the MRO.
    * If found, check if it's a data descriptor.
      * If it has a `__set__` or `__delete__` method, call `__set__` or `__delete__`.
      * If it doesn't have the corresponding method, raise `AttributeError`.
  2. Look in the instance `__dict__`.
    * `a.x = value`
      * Set attribute to value.
    * `del a.x`
      * If found, delete attribute.
      * If not found, raise `AttributeError`.

# 类属性访问优先级：

## `__set__` &amp; `__delete__` (`A.x = value` &amp; `del A.x`)

  1. Look in the metaclass `__dict__`, working up the MRO.
    * If found, check if it's a data descriptor.
      * If it has a `__set__` or `__delete__` method, call `__set__` or `__delete__`.
      * If it doesn't have the corresponding method, raise `AttributeError`.
  2. Look in the class `__dict__`.
    * `A.x = value`
      * Set attribute to value.
    * `del A.x`
      * If found, delete attribute.
      * If not found, raise `AttributeError`.

![](http://nbviewer.jupyter.org/github/akittas/presentations/blob/master/pythe
ss/descriptors/figures/sink_in.jpg)

In [4]:



    # implementation of classmethod and staticmethod, equivalent to the standard library

    class MyClassmethod:
        def __init__(self, func):
             self.func = func

        # ignore the instance, provide the class as first argument (usually named cls) so the
        # returned function can be called with the arguments the user wants to explicitly provide
        def __get__(self, instance, owner):
            def cls_wrapper(*args, **kwargs):
                return self.func(owner, *args, **kwargs)  # what if I put cls=owner?
            return cls_wrapper

    class MyStaticmethod:
        def __init__(self, func):
            self.func = func

        # essentially just accepts a function and then returns it when __get__ is called
        def __get__(self, instance, owner):
            return self.func


In [5]:



    class A:
        def foo(self):
            print(self)

        @MyClassmethod  # same as: bar = MyClassmethod(bar)
        def bar(cls):
            print(cls)

        @MyStaticmethod  # same as: baz = MyStaticmethod(baz)
        def baz():
            print('static method')

        # both methods are accessed through their respective descriptors


In [6]:



    a = A()

    # instance method, business as always
    a.foo()
    print()

    # access the method object, descriptor is called and returns the cls_wrapper method object
    print(A.bar)

    # call the method, instance in __get__ is None (don't care), owner is A
    A.bar()

    # run it on the instance, instance in __get__ is a (don't care), owner is A
    a.bar()
    print()

    # access the method object, descriptor returns a function with no arguments
    print(A.baz)

    # call the method without any instance (self) or class (cls) object
    # of course same result if we call it in the instance
    A.baz()
    a.baz()



    <__main__.A object at 0x0000022EE39CCF60>

    <function MyClassmethod.__get__.<locals>.cls_wrapper at 0x0000022EE39D2510>
    <class '__main__.A'>
    <class '__main__.A'>

    <function A.baz at 0x0000022EE39D2268>
    static method
    static method


In [7]:



    # implementation of property, equivalent to the standard library

    class MyProperty:
        def __init__(self, fget=None, fset=None, fdel=None):
            self.fget = fget
            self.fset = fset
            self.fdel = fdel

        def __get__(self, instance, owner):
            if instance is None:  # this was called from the class, not the instance
                return self
            elif self.fget is None:
                raise AttributeError("unreadable attribute")
            else:
                return self.fget(instance)

        def __set__(self, instance, value):
            if self.fset is None:
                raise AttributeError("can't set attribute")
            else:
                self.fset(instance, value)

        def __delete__(self, instance):
            if self.fdel is None:
                raise AttributeError("can't delete attribute")
            else:
                self.fdel(instance)

        def getter(self, fget):
            return type(self)(fget, self.fset, self.fdel)

        def setter(self, fset):
            return type(self)(self.fget, fset, self.fdel)

        def deleter(self, fdel):
            return type(self)(self.fget, self.fset, fdel)


In [8]:



    class A:
        def __init__(self, x):
            self._x = x

        @MyProperty
        def x(self):
            print("returning _x: {}".format(self._x))
            return self._x

        @x.setter
        def x(self, value):
            print("setting _x to {}".format(value))
            self._x = value


In [9]:



    a = A(1)
    print(A.x)  # call the descriptor from the class, returns the descriptor object
    print(a.x)
    print()
    a.x = 2
    print(a.x)
    print()
    A.x = 'bye bye descriptor'
    print(a.x)  # descriptor is gone from class, instance gets attribute from class



    <__main__.MyProperty object at 0x0000022EE39EBF28>
    returning _x: 1
    1

    setting _x to 2
    returning _x: 2
    2

    bye bye descriptor


![](http://nbviewer.jupyter.org/github/akittas/presentations/blob/master/pythe
ss/descriptors/figures/useful_morpheus.jpg)

# 这有用吗？

  * Why do I need to know this? Can't I just use `property`?
    * No problem. `property` is awesome! Use `property` for greater good!
  * However there are times where logic needs to be repeated in properties.
  * This can lead to code duplication.
  * We can try to fix this by writing helper methods.
  * But then in each property, code for these method calls will be duplicated.
  * Descriptors allow us to **capture the logic** for attribute access and **re-use** it for different attributes.

In [10]:



    class BasketballGame:
        def __init__(self, points, rebounds, steals):
            self.points = points
            self.rebounds = rebounds
            self.steals = steals

        @property
        def points(self):
            return self._points

        @points.setter
        def points(self, value):
            if value < 0:
                raise ValueError('Positive values only!')
            self._points = value

        @property
        def rebounds(self):
            return self._rebounds

        @rebounds.setter
        def rebounds(self, value):
            if value < 0:
                raise ValueError('Positive values only!')
            self._rebounds = value

        @property
        def steals(self):
            return self._steals

        @steals.setter
        def steals(self, value):
            if value < 0:
                raise ValueError('Positive values only!')
            self._steals = value


In [11]:



    class NonNegativeField:
        def __init__(self, name=''):
            # need to store the field name on the descriptor object itself
            # as descriptors are defined on the class level
            self.name = name

        def __get__(self, instance, owner):
            return instance.__dict__[self.name]

        def __set__(self, instance, value):
            if value < 0:
                raise ValueError('Positive values only!')
            instance.__dict__[self.name] = value


In [12]:



    class BasketballGame:
        # is there a better way, so that we don't have to repeat the field name?
        points = NonNegativeField('points')
        rebounds = NonNegativeField('rebounds')
        steals = NonNegativeField('steals')

        def __init__(self, points, rebounds, steals):
            self.points = points
            self.rebounds = rebounds
            self.steals = steals


In [13]:



    a = BasketballGame(points=100, rebounds=30, steals=10)
    print("points: {}, rebounds: {}".format(a.points, a.rebounds))
    try:
        a.points = -5
    except ValueError as e:
        print("Error! {}".format(e))



    points: 100, rebounds: 30
    Error! Positive values only!


In [14]:



    def named_descriptors(cls):
        for name, attr in cls.__dict__.items():
            if isinstance(attr, NonNegativeField):
                attr.name = name
        return cls

    @named_descriptors
    class BasketballGame:
        points = NonNegativeField()
        rebounds = NonNegativeField()
        steals = NonNegativeField()

        def __init__(self, points, rebounds, steals):
            self.points = points
            self.rebounds = rebounds
            self.steals = steals


In [15]:



    a = BasketballGame(points=100, rebounds=30, steals=10)
    print("points: {}, rebounds: {}".format(a.points, a.rebounds))
    try:
        a.points = -5
    except ValueError as e:
        print("Error! {}".format(e.args))



    points: 100, rebounds: 30
    Error! ('Positive values only!',)


In [16]:



    class NonNegativeField:
        def __get__(self, instance, owner):
            return instance.__dict__[self.name]

        def __set__(self, instance, value):
            if value < 0:
                raise ValueError('Positive values only!')
            instance.__dict__[self.name] = value

        # new in Python 3.6
        def __set_name__(self, owner, name):
            self.name = name

    class BasketballGame:
        points = NonNegativeField()
        rebounds = NonNegativeField()
        steals = NonNegativeField()

        def __init__(self, points, rebounds, steals):
            self.points = points
            self.rebounds = rebounds
            self.steals = steals


In [17]:



    a = BasketballGame(points=100, rebounds=30, steals=10)
    print("points: {}, rebounds: {}".format(a.points, a.rebounds))
    try:
        a.points = -5
    except ValueError as e:
        print("Error! {}".format(e))



    points: 100, rebounds: 30
    Error! Positive values only!


![](http://nbviewer.jupyter.org/github/akittas/presentations/blob/master/pythe
ss/descriptors/figures/thank_you.jpg)

# 参考

  1. [StackOverflow - What is encapsulation? How does it actually hide data?](http://stackoverflow.com/questions/5673829/what-is-encapsulation-how-does-it-actually-hide-data)
  2. [Shahriar Tajbakhsh - Underscores in Python](https://shahriar.svbtle.com/underscores-in-python)
  3. [Shalabh Chaturvedi - Python Attributes and Methods](http://www.cafepy.com/article/python_attributes_and_methods/python_attributes_and_methods.html)
  4. [Raymond Hettinger - Descriptor HowTo Guide](https://docs.python.org/3/howto/descriptor.html)
  5. Simeon Franklin - Python Descriptors [video](https://www.youtube.com/watch?v=ZdvpNaWwx24) &amp; [presentation](http://simeonfranklin.com/talk/descriptors.html)
  6. [Laura Rupprecht - Describing Descriptors - PyCon 2015](https://www.youtube.com/watch?v=h2-WPwGnHqE)
  7. Jacob Zimmerman - Python Descriptors, Apress Publishing (2006)
  8. [Dan Sackett - An introduction to Python descriptors](http://programeveryday.com/post/an-introduction-to-python-descriptors/)
