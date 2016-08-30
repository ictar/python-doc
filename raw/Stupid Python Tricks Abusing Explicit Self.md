原文：[Stupid Python Tricks: Abusing Explicit Self](https://medium.com/@hwayne/stupid-python-tricks-abusing-explicit-self-53d46b72e9e0)

---

在Ruby中，这样定义一个方法：

```ruby
class Foo
  def bar(baz)
    baz
  end
end
```

在Python中，这样定义一个方法：

```python
class Foo:
    def bar(self, baz): # What's self doing there?
        return baz # Not even using it
```
这个“显式self”是许多许多许多讨论的主题，而大量的人觉得它相当令人困惑。我想提供一个简短的解释，并且找到一些你永远不应该做的滥用显式self的方式。准备好了吗？那就开始吧。

# 奇怪的实例方法

让我们定义以下类：

```python
class Number:
    def __init__(self, x: int) -> None:
        self.x = x
        self.minus = lambda y: self.x - y # we'll come back to this
    def plus(self, y: int) -> int:
        return self.x + y
one = Number(1)
```

one.plus(2)是什么？那相当简单：

```python
>>> print(one.plus(2))
3
```

但如果是Number.plus(one, 2)呢？

```python
>>> print(Number.plus(one, 2)) # ???
3 # !!!
```

Here, plus is a “bound method”. The object doesn’t *furious air quotes* really have a method called ‘plus’. Instead, “one.plus(N)” is a shorthand for “Number.plus(one, N)”, the “unbound method”. With that in mind, it’s pretty obvious why self is needed: plus is a class method that takes two parameters: the object we’re using, and the number we’re adding.
这里，plus是一个“绑定方法”。

It’s a little more complicated than that (Python 3 simplifies things a bit), but that’s a pretty useful lie that makes it a lot easier to work out the logic here. Let’s provide a slightly more complex example:

```python
>>> Number.times = lambda self, y: self.x * y
>>> print(one.times(2)) # automatically valid
2
```

When we call “one.times(2)”, it automatically translates that to “Number.times(one, 2)”, which we just defined. This becomes a lot easier to reason about with the explicit self.

Now let’s double back to that “self.minus” in the initializer. There, we’re not defining the method “properly”. It’s not part of the class definition. We’re just assigning a property to the object which also happens to be callable. Since this isn’t a proper method, it shouldn’t be translated to a bound method in the actual object, so we can drop the self. Similarly, there shouldn’t be an unbound version on the class, so trying to use that will be an error. Let’s give that a test:

```python
>>> print(one.minus(2))
-1
>>> print(Number.minus(one, 2))
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: type object 'Number' has no attribute 'minus'
```

Boom.

# 回调地狱

Quick aside before the really stupid stuff. Recall that in Python, functions are first class objects. That means that you can pass functions to other functions like any other kind of object. This means you can do the following:

```python
>>> def subtract_two(minus):
...     return(minus(2))
...
>>> print(subtract_two(one.minus))
-1
```

The function we pass in is the bound method of our object. This means that when we call it, it has access to the original object. We can use this for callbacks. Instead of giving a function complete access to our object, we give it only minimal access: just one method. Sometimes this is a good idea. Other times, it means you have to rethink your architecture. If you don’t see why, spend a few weeks writing Javascript front-ends and get back to me.

(Aside on the aside: you should probably check out Javascript anyway, because object prototyping is really close to what we’re doing with unbound methods and JS uses prototyping much more openly and explicitly.)

# 真正愚蠢的东西

Okay, back on track. We’ve seen that if you create a new unbound method, it adds a bound method to all existing objects. It also works with modification: change the unbound method and you change all the bound methods. This means you can dynamically adjust a class based on the runtime. For example, if you’re getting a ton of calls to a particular method, you can start caching it.

Here’s a toy example. We want to add a division method to Number. We want to start logging all divisions if and only if at some point we try to divide by zero. We can do it like this:

```python
def _divide(self, y):
    try:
        return self.x / y
    except ZeroDivisionError:
        print("Tried to divide {} by 0".format(self.x))
        def __newdivide(self, y):
            print("{} / {}".format(self.x, y))
            return _divide(self, y)
        self.__class__.divide = __newdivide
Number.divide = _divide
>>> six = Number(6)
>>> eight = Number(8)
>>>
>>> print(six.divide(3))
2.0
>>> print(eight.divide(0))
Tried to divide 8 by 0
None
>>> print(six.divide(3))
6 / 3
2.0
```

One object throwing an error triggered improved logging on a completely different one. There’s a few extremely specific cases where manipulating unbound methods might be a good idea. Practically, though, don’t do this in production code. Programming is hard enough as it is. You don’t need your method definitions changing underneath you.

# 总结

当你定义一个类上的实例方法时，它创建了该对象上的一个对应的“绑定”方法。这是类上的非绑定方法的别名。如果你直接在对象上定义实例方法，那么类上就没有对应的非绑定方法。

修改非绑定方法也会改变所有现有的绑定方法。你可以用它来动态修改类。

不要。

真的，不要。

# 进一步阅读

* [Python method对象](https://docs.python.org/3/tutorial/classes.html#method-objects)
* [Javascript中的原型](http://javascriptissexy.com/javascript-prototype-in-plain-detailed-language/)
* [静态、类和抽象方法](https://julien.danjou.info/blog/2013/guide-python-static-class-abstract-methods) (还有一个关于Py2是如何有点复杂的重要注解)
* [回调地狱](http://callbackhell.com/)
* [回调地狱的一个反证](http://thecodebarbarian.com/2015/03/20/callback-hell-is-a-myth)