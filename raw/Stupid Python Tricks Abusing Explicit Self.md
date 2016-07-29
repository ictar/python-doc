原文：[Stupid Python Tricks: Abusing Explicit Self](https://medium.com/@hwayne/stupid-python-tricks-abusing-explicit-self-53d46b72e9e0)

---

In Ruby, you define a method like this:

```ruby
class Foo
  def bar(baz)
    baz
  end
end
```

In Python, you define it like this:

```python
class Foo:
    def bar(self, baz): # What's self doing there?
        return baz # Not even using it
```
That “explicit self” is the subject of many, many, many discussions, and tons of people find it really confusing. I want to provide a brief explanation, as well as find some ways to abuse that explicit self that you should never, ever do. Ready? Let’s go.

# Instance Methods Are Weird

Let’s define the following class:

```python
class Number:
    def __init__(self, x: int) -> None:
        self.x = x
        self.minus = lambda y: self.x - y # we'll come back to this
def plus(self, y: int) -> int:
        return self.x + y
one = Number(1)
```

What’s one.plus(2)? That’s pretty easy:

```python
>>> print(one.plus(2))
3
```

But what if we do Number.plus(one, 2)?

```python
>>> print(Number.plus(one, 2)) # ???
3 # !!!
```

Here, plus is a “bound method”. The object doesn’t *furious air quotes* really have a method called ‘plus’. Instead, “one.plus(N)” is a shorthand for “Number.plus(one, N)”, the “unbound method”. With that in mind, it’s pretty obvious why self is needed: plus is a class method that takes two parameters: the object we’re using, and the number we’re adding.

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

# Callback Hell

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

# The Really Stupid Stuff

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

# Summary

When you define an instance method on a class, it creates a corresponding “bound” method on the object. This aliases to the unbound method on the class.
If you define an instance method directly on the object, there’s no corresponding unbound method on the class.
Modifying the unbound method will also change all existing bound methods.
You can use this to dynamically modify classes.
Don’t.
No really, don’t.

# Further Reading

* [Python method objects](https://docs.python.org/3/tutorial/classes.html#method-objects)
* [Prototyping in Javascript](http://javascriptissexy.com/javascript-prototype-in-plain-detailed-language/)
* [Static, class, and abstract methods](https://julien.danjou.info/blog/2013/guide-python-static-class-abstract-methods) (also has an important note on how Py2 is a little more complicated)
* [Callback hell](http://callbackhell.com/)
* [A rebuttal to callback hell](http://thecodebarbarian.com/2015/03/20/callback-hell-is-a-myth)