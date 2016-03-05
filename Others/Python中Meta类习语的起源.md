原文：[The origins of the class Meta idiom in python](http://mapleoin.github.io/perma/python-class-meta)

---
所以最近，我一直在Python的API们中发现`class Meta`这个习语。我在[factory-boy](http://factoryboy.readthedocs.org/en/latest/index.html)和[WTForms](https://wtforms.readthedocs.org/en/latest/meta.html)中发现了它，并且怀疑它们都是来自[Django](https://docs.djangoproject.com/en/1.9/topics/db/models/#meta-options)，但是我Google了下，却没有找到对其原因，它来自哪里，或者为什么称之为`class Meta`的任何解释。所以就有了这篇文章了！

#### TL;DR 它是什么

内部`Meta`类与Python的[元类(metaclasses)](https://www.python.org/doc/essays/metaclasses/)没有半毛钱关系。它的名字只是一个历史巧合（下面你就可以读到）。

这个语法半点魔法都没有，下面是Django文档中的一个例子：
```py
class Ox(models.Model):
    horn_length = models.IntegerField()
    class Meta:
        ordering = ["horn_length"]
        verbose_name_plural = "oxen"
```

拥有一个内部的`Meta`类会使得用户和ORM更容易分辨关于该模型，哪些是模型中的字段以及哪些只是其他信息（或者元数据，如果你喜欢的话）。ORM可以简单的通过`your_model.pop('Meta')`来检索它需要的信息。你也可以在任何你事先的库中这样做，正如`factory-boy`和`WTForms`所做的那样。

#### 一些早期的Django历史

现在是较长的故事。我做了一些软件考古学，这完全是应该的！并且发现第一次提交在Django中提到了`class Meta` (实际上是`class META`[1](#fnaf4ee8d80aa04674b5bd7d121b1a5a8e))：[commit 25264c86](https://github.com/django/django/commit/25264c86048d442a4885dfebae94510e2fa0c1e4)。有一个[版本说明wiki页面](https://code.djangoproject.com/wiki/ModelSyntaxChangeInstructions)，其中包括了该改变。

从那里，我们可以看到，在内部的`class Meta`被引入之前，是如何声明Django模型的。一个Django模型类具有一些特殊的属性。`db_table`属性保存SQL表名。`fields`属性是字段类型实例的元组(!)（如`CharField`, `IntegerField`, `ForeignKey`）。这些映射到SQL表列。另外一个有趣的属性是`admin`，它大部分是用来描述模型将在Django的管理界面中如何表现。现在，所有这些类都在`django.core.meta`包中被定义，即`meta.Model`, `meta.CharField`, `meta.ForeignKey`, `meta.Admin`。这是就是元数据呀！（也许这就是名字的最终来源）。

在2005年7月份标题为[ORM字段描述的更干净的方法](https://groups.google.com/forum/#%21msg/django-developers/IZdH8K8IbLA/AIJGlt8d3icJ)中的一个`django-developers`邮件列表线程里，用户`deelan`建议引进一些[SQLObject](https://en.wikipedia.org/wiki/SQLObject)的思想到Django的ORM中。这似乎就是在Django中声明一个内部类来保存模型的部分信息这种想法的第一颗种子：

>需要避免字段间的命名冲突，所以通过一种方式将字段包裹到一个私有的名字空间可能是不错的。
 
>— `deelan`, [Cleaner approach to ORM fields description](https://groups.google.com/forum/#%21msg/django-developers/IZdH8K8IbLA/AIJGlt8d3icJ)

在该线程的末尾，[django ticket 122](https://code.djangoproject.com/ticket/122)被创建，它似乎包含了一个单独的内部`Meta`类的第一次提出。

它开始时作为一个向后兼容的变化，很快变成向后不兼容，它是“Django第一个真正的大社区驱动的改进”，Adrian Holovaty接着在包含该改动的发行公告中这样描述它。

Matthew Marshall提供了[ticket 122](:https://code.djangoproject.com/ticket/122)的第一个补丁，暗示字段应当能够直接作为类属性（使用fieldname=FieldClass构建模型）在模型类中定义而不是在`fields`列表中。所以：
```py
class Poll(meta.Model):
    question = meta.CharField(maxlength=200)
    pub_date = meta.DateTimeField('date published')
```

而不是：
```py
class Poll(meta.Model):
    fields = (
        meta.CharField(maxlength=200),
        pub_date = meta.DateTimeField('date published'),
    )
```

而且，应该有两种方式定义一个`ForeignKey`：
```py
ForeignKey = Poll, {'edit_inline':True, 'num_in_admin':3}
.
#the attribute name is irrelevant here:
anything = ForeignKey(Poll, edit_inline=True, num_in_admin=3)
```

在他的第一个评论中，`mmarshall`引进了内部`class Meta`来保存任何不适字段的数据：表名(奇怪的重命名为`module_name`)和admin选项。这些字段将是类属性。

关于在一个内部类中发生什么，以及在外部类中发生什么的决定似乎被留给了用户。支持一个可选的`class Field`内部类，这样，字段会存在那里，而元数据将作为类属性存在（这似乎提供了向后兼容的优势，允许`admin`类属性，同时允许表中有一个名字也为`admin`的列。

一些其他的想法被抛出，并且`ForeignKey`的语法也被讨论。一度，Adrian Holovaty (`adrian`)干预道(关于原始的`class Meta/class Field`建议):

>它太灵活了，以至到了混乱的地步。它可以是Meta类，或者Field类，又或者是普通的类属性，这感觉只是有点像“不只一种方法来做这件事。”但是，应该只有一个，清晰明显的方式来做。如果我们决定修改模型语法，那么为非字段信息提供Meta类，而让所有的字段都只是该类的属性。
> 
> — Adrian Holovaty, [django ticket 122, comment 9](https://code.djangoproject.com/ticket/122#comment%3A9)

此线程从那里接着下去。对于此想法，有一些批评者（援引性能和与其他Python API的一致性为证），也有一些关于实现细节的语法讨论和关于`ForeignKey`语法的再次谈论。

然后，此事峰回路转，Adrian Holovaty **以无法修改为由关闭了这个ticket !**:

> Jacob [Kaplan-Moss]和我详细的讨论了这件事，然后我们已经决定了模型的语法不应改变。使用fieldname=FieldClass语法幕后将需要太多的“魔法”，而获益极小。
> 
> — Adrian Holovaty, [django ticket 122, comment 33](https://code.djangoproject.com/ticket/122#comment%3A33)

这很有趣，因为恕我直言，在让Django的模型API更人性化方面，这是一个巨大的区别，同时，这也是其它框架，例如Rails和SQLObject当时正在做的事。

一个[IRC讨论](https://code.djangoproject.com/attachment/ticket/122/modeldiscuss_cleanedup.txt)稍后在该ticket中被引用。[2](#fn34b8a5e31ddd4cd4b540e944f5f3f4b7)根据那个讨论，似乎`adrian`关闭的理由大多是对`ForeignKey`语法和对模型进行向后兼容的修改的关注。`rmunn`在缓和该讨论和弄清情况及每个人的意见的过程中做得极好，同时他大力推动新语法。

结果，该ticket被重新打开，并且从那时起，它看起来一帆风顺。几天后，新的语法被合并进来，而该ticket再次被关闭，此时，解决方法设置为了已修改。

Adrian稍后会在`django-developers`邮件列表文章中宣布此修改。下面是那个邮件列表文章中的一些有趣片段：

> 我为向后不兼容道歉，但这仍是非官方软件。;-)一旦到达了1.0版本 — 它会更接近现在修改的模型语法 — 我们将会非常专注向后兼容。
> 
> 我想不出在1.0之前还会有什么其他的向后不兼容的改变（敲木头）。如果这不是最后一个，那么至少它是最后一个主要的。
> 
> — Adrian Holovaty, [重要: Django模型语法变化](https://groups.google.com/d/msg/django-developers/z_eGMWTJBqk/Fa3xur7J0jwJ)

事情并不如计划那样进行。2006年5月，[commit f69cf70e](https://github.com/django/django/commit/f69cf70ed813a8cd7e1f963a14ae39103e8d5265)出现，而这正是另一种_让我们在一个大分支中修改一切_的提交，它作为Django **0.95**的一部分发布。作为该API改动的一部分，`class META`被重命名为`class Meta` (因为这更赏心悦目些)。你可以在[RemovingTheMagic维基页面](https://code.djangoproject.com/wiki/RemovingTheMagic)上找到相关细节。有趣的是，[ticket 122](https://code.djangoproject.com/ticket/122)中所有的讨论都使用`Meta`，除了最后一个人（我猜就是提交该补丁的那个人）使用`META`。无论是在ticket还是在IRC中都有一些关于它的讨论，并且有那么几个人担心Django的用户可能实际上会想要在他们的模型中声明一个名为`Meta`的属性，而该内部类的名字将与之冲突。

#### 就是这样。几乎……

无论如何，这就是Django如何获得`class Meta`这个故事的结尾。现在，如果我告诉你，这一切都发生在SQLObject项目的一年多之前呢？记得吗，发给`django-developers`的第一篇文章中说，Django应该在单独的内部类中保存一些自己的属性，就像SQLObject已经做的那样？

2004年4月，Ian Bicking (SQLObject的创作者)发了一封电子邮件给`sqlobject-discuss`邮件列表：

> 有一堆元数据现在正存储在不同的实例变量中，所有都特别像，并且没有内省接口。我想将这些整合为一个单一的对象/类，将其与SQLObject类分离开来。这样，我就不必担心命名冲突，并且我不觉得每一个新增的小接口将会污染大家的类。（虽然现在在那里的大多数公共方法仍将是SQLObject子类的方法，就像它们现在是一样）所以，我正寻找关于它应该如何工作的反馈。
> 
> — Ian Bicking, [元数据容器](https://sourceforge.net/p/sqlobject/mailman/message/7522562/)

他的代码示例：
```py
class Contact(SQLObject):
     class sqlmeta(SQLObject.sqlmeta):
         table = 'contact_table'
         cacheInstances = False
     name = StringCol()
```

SQLObject社区似乎并不像Django社区那样活跃。`sqlobject-discuss`邮件列表上有几个来自Ian Bicking的邮件，其中包括了该提案，并要求反馈。我怀疑一些讨论通过一些其他渠道进行，当这个社区既不大，也不如Django社区一样 擅长记录它的功能。(sourceforge关于该邮件列表归档和cvs日志的接口并不易于浏览)。

一年后，Ian Bicking参与了`django-developers`邮件列表讨论，在那里，他提出了一些小的语法建议，但似乎不认为他对Django模型API的设计做了其他贡献。

#### 结论

据我所知，Ian Bicking是在元数据容器内部类中存储元数据这种理念的鼻祖。虽然是Django项目决定使用`class Meta`名，并且在它自己的社区之外对其进行推广。

无论如何，这就是故事的结尾了。对我来说，它表明了开源和开放互联网可以有多棒。我可以在11年之后找到完整的原始源代码，提交日志，以及关于该问题的跟踪执行情况，邮件列表和IRC日志的所有讨论，这仅仅是惊人的社区工作，使我热泪盈眶。

希望你享受了此探索之旅！

1 因为在2005年，人们说话不太轻声细语

2 这非常有趣，你应该读一下。在某些时候，有人的猫抓了一只蓝松鸦(原文是someone’s cat catches a blue jay。这里不知道有啥深意)。我想就是字面意思。
