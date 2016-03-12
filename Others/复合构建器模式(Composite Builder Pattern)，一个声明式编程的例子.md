原文：[The Composite Builder Pattern, an Example of Declarative Programming](http://slott-softwarearchitect.blogspot.jp/2016/03/the-composite-builder-pattern-example.html)

---

我称其为**复合构建器(Composite Builder)**模式。它可能还有其他名字，但目前我尚未见到。有可能只是因为缺乏对现有技术的研究。我怀疑，这并不是很新的模式。但我觉得，这是进行一些声明式Python编程的很酷的方法。

下面是概念。

```py
class TheCompositeThing(Builder):
    attribute1 = SomeItem("arg0")
    attribute2 = AnotherItem("arg1")
    more_attributes = MoreItems("more args")
```

它的思想在于，当我们创建**TheCompositeThing**的一个实例时，我们得到了一个复杂对象，这个对象是根据各种数据源进行构建的。我们想在下面这一类上下文中使用它：

```py
with some_config_path.open() as config:
    the_thing = TheCompositeThing().substitute(config)
print(json.dumps(the_thing))
```

我们想打开一些配置文件 —— 一些对环境唯一的文件 —— 然后以一种平滑的方式填充该复杂对象。一旦获得了这个复杂对象，它就可以以一些方式为我们所用，例如作为JSON或者YAML文档被序列化。

每一个**Item**都有一个**get()**方法，该方法接受配置所为其输入。它们进行一些计算，然后返回一个有用的结果。在某些情况下，这些计算有点落后：

```py
class LiteralItem(Item):
    def __init__(self, value):
        self.value = value
    def get(self, config):
        return self.value
```

上面显示了我们如何将一个文本值赋给输出。其他值可能涉及复杂的计算，或者配置查找，或者两者的结合。

## 为什么要使用一种声明式风格？

当**TheCompositeThing**中的每个项都涉及到想到复杂，但完全独立的计算时，这种声明式风格可能很方便。因为不存在依赖性，所以**substitute()**方法可以以任何顺序为属性赋值。或者，可能，不赋值，直到真正需要它们的时候。这种模式即允许立即计算出属性值，又允许属性的即时计算。

作为一个例子，这种模式适用于构建复杂的[AWS云形成模板](https://aws.amazon.com/cloudformation/aws-cloudformation-templates/)。我们通常需要对大量的模板进行一个全局调整，以便于重建一个服务器场。而被填充的**Item**之间很少或没有依赖关系。由于项之间的模糊依赖关系，并不存在这种在一个地方进行的修改同时也出现在另一个地方的奇怪的“涟漪效应”。

我们可以对其进行扩展，使得在声明式风格中创建的每个阶段都带有一个管道。在这种更复杂的情况下，填充到该复杂对象的**Items**将具有多层次。第一层次的**Items**依赖于一个源。第二层次的**Items**依赖于第一层次的**Items**.

```py
class Stage1(Builder):
    item_1 = Stage_1_Item("arg")
    item_2 = Stage_1_More("another")

class Stage2(Builder):
    item_a = Stage_2_Item("some_arg")
    item_b = Stage_2_Another(355, 113)
```

然后，我们可以根据外部配置或输入创建一个**Stage1**对象。接着可以创建从该**Stage1**对象派生的**Stage2**对象。

是滴。这看起来项无用的元编程。我们可以（更简单）的这样做：：

```py
class Stage2:
    def __init__(self, stage_1, config):
        self.item_a = Stage_2_Item("some_arg", stage_1, config)
        self.item_b = Stage_2_Another(355, 113, stage_1, config)
```

我们已经急切地在`__init__()`处理阶段计算出了属性值。

或者，也许是这样：：

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

这里，我们启动即时模式，也就是说，只有在请求属性的时候，才计算属性值。

## 好处

我们已经看到了三种构建复杂对象的方法：

1.  作为独立属性，它具有一种灵活但是简洁的实现。
2.  作为在**__init__() **中使用了顺序代码的属性，它们不能保证独立性。
3.  作为使用冗余代码的属性。

哪个是倡导的方式呢？为什么这种声明式技术有趣？

我发现**声明式构建器(Declarative Builder)**模式是很方便的，因为它给我带来了以下好处。

*   属性**必须**被独立构建。我们可以（毫不犹豫地）重新排列这些属性，而不用担心计算会干扰到另一个属性。
*   属性可以立即构建或者即时构建。细节并不重要。我们并不通过**__init__**或者**@property**来公开实现细节。
*   类定义变成了一个配置项。技术支持人员不需要具备Python深层支持就可以成功的编辑**TheCompositeThing**的定义。

我认为对于一些应用而言，这种即时的声明式编程是很有用的。它在一些情况
下表现完美，例如，当我们需要彼此隔离大量的计算，从而允许软件在不被破坏的情况进行演变的时候。



这可能是一个延伸，但我认为它显示了[依赖倒置原则](https://en.wikipedia.org/wiki/Dependency_inversion_principle)。在某种程度上，我们已经将所有依赖移到这些类中属性的可见列表。这些项类不互相依赖；它们依赖于配置，或者可能是前一个接单的复合对象。由于在类定义中不包含任何方法，因此我们可以任意修改类。比起代码，每一个**Builder**子类更像一个配置项。特别是在Python中，我们可以任意修改类，而不用管重建的痛苦。
