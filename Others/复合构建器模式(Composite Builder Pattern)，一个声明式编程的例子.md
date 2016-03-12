原文：[The Composite Builder Pattern, an Example of Declarative Programming](http://slott-softwarearchitect.blogspot.jp/2016/03/the-composite-builder-pattern-example.html)

---

I'm calling this the **Composite Builder** pattern. This may have other names, but I haven't seen them. It could simply be lack of research into prior art. I suspect this isn't very new. But I thought it was cool way to do some declarative Python programming.

Here's the concept.

```py
class TheCompositeThing(Builder):
    attribute1 = SomeItem("arg0")
    attribute2 = AnotherItem("arg1")
    more_attributes = MoreItems("more args")
```

The idea is that when we create an instance of **TheCompositeThing**, we get a complex object, built from various data sources.  We want to use this in the following kind of context:

```py
with some_config_path.open() as config:
    the_thing = TheCompositeThing().substitute(config)
print(json.dumps(the_thing))
```

We want to open some configuration file -- something that's unique to an environment -- and populate the complex object in one smooth motion. Once we have the complex object, it can then be used in some way, perhaps serialized as a JSON or YAML document.

Each **Item** has a **get()** method that accepts the configuration as input. These do some computation to return a useful result. In some cases, the computation is kind of degenerate case:

```py
class LiteralItem(Item):
    def __init__(self, value):
        self.value = value
    def get(self, config):
        return self.value
```

This shows how we jam a literal value into the output. Other values might involve elaborate computations, or lookups in the configuration, or a combination of the two.

## Why Use a Declarative Style?

This declarative style can be handy when each of the Items in **TheCompositeThing** involves rather complex, but completely independent computations. There's no dependency here, so the **substitute()** method can fill in the attributes in any order. Or -- perhaps -- not fill the attributes until they're actually requested. This pattern allows eager or lazy calculation of the attributes.

This pattern applies to building complex [AWS Cloud Formation Templates](https://aws.amazon.com/cloudformation/aws-cloudformation-templates/) as an example. We often need to make a global tweak to a large number of templates so that we can rebuild a server farm. There's little or no dependency among the **Item** values being filled in. There's no strange "ripple effect" of a change in one place also showing up in another place because of an obscure dependency between items.

We can extend this to have a kind of pipeline with each stage created in a declarative style. In this more complex situation, we'll have several tiers of **Items** that fill in the composite object. The first-stage **Items** depend on one source. The second stage **Items** depend on the first-stage **Items**.

```py
class Stage1(Builder):
    item_1 = Stage_1_Item("arg")
    item_2 = Stage_1_More("another")

class Stage2(Builder):
    item_a = Stage_2_Item("some_arg")
    item_b = Stage_2_Another(355, 113)
```

We can then create a **Stage1** object from external configuration or inputs. We can create the derived **Stage2** object from the **Stage1** object.

And yes. This seems like useless metaprogramming.  We could -- more simply -- do something like this::

```py
class Stage2:
    def __init__(self, stage_1, config):
        self.item_a = Stage_2_Item("some_arg", stage_1, config)
        self.item_b = Stage_2_Another(355, 113, stage_1, config)
```

We've eagerly computed the attributes during `__init__()` processing.

Or perhaps this::

```py
class Stage2:
    def __init__(self, stage_1, config):
        self.stage_1= stage_1
        self.config= config
    @property
    def item_a(self):
        return Stage_2_Item("some_arg", self.stage_1, self.config)
    @property
    def item_b(self):
        return Stage_2_Another(355, 113, self.stage_1, self.config)
```

Here we've been lazy and only computed attribute values as they are requested.

## Benefits

We've looked at three ways to build composite objects:

1.  As independent attributes with an flexible but terse implementation.
2.  As attributes during **__init__() **using sequential code that doesn't assure independence.
3.  As properties using wordy code. 
What's the value proposition? Why is this declarative technique interesting?

I find that the the **Declarative Builder** pattern is handy because it gives me the following benefits.

*   The attributes **must** be built independently. We can -- without a second thought -- rearrange the attributes and not worry about one calculation interfering with another attribute. 
*   The attributes can be built eagerly or lazily. Details don't matter. We don't expose the implementation details via **__init__** or **@property**.
*   The class definition becomes a configuration item. A support technician without deep Python knowledge can edit the definition of **TheCompositeThing** successfully.

I think this kind of lazy, declarative programming is useful for some applications. It's ideal in those cases where we need to isolate a number of computations from each other to allow the software to evolve without breaking.




It may be a stretch, but I think this shows the [Depedency Inversion Principle](https://en.wikipedia.org/wiki/Dependency_inversion_principle). To an extent, we've moved all of the dependencies to the visible list of attributes within these classes. The items classes do not depend on each other; they depend on configuration or perhaps previous stage composite objects. Since there are no methods involved in the class defintion, we can change the class freely. Each subclass of **Builder** is more like a configuration item than it is like code. In Python, particularly, we can change the class freely without the agony of a rebuild.
