原文：[Understanding the Math behind FP: The Monads](http://hkupty.github.io/2016/Understanding-the-math-behind-fp-monads/)

---

在我[之前的文章中](http://hkupty.github.io/2016/Functional-Programming-Concepts-Idioms-and-Philosophy/)（Ele注：这个之前也翻过，地址是[函数式编程：概念，惯用语和理念](./函数式编程：概念，惯用语和理念.md)），我谈了一些关于函数式编程的东西，希望将其介绍给那些之前没有函数式编程经历的新手和开发者。

一些人说它不是非常精确的，并且它并没有解释一些细节所需水平的概念（例如，单子（Monad）或函子（Functor））。好吧，我真的希望在这篇文中能够说明这些细节。尽管如此，我还是会尽量使得它对于那些之前从未进行函数式编程的人来说，是轻量的。

与前面提到的一篇博文一起，我希望让更多的人对函数式编程的概念以及它们如何被应用在“非函数式”语言上感兴趣。我坚持从特定的语言中分离函数式编程的概念，因为它大部分的概念可以（有时作为最佳实践被强制）用于任何语言。

我将写一系列的文章来解释结构，从今天的单子（Monad）开始，并将尝试在一个非函数式语言上导入（或实现）它们。

我将在scala中解释这些结构，并且编写Python代码作为在其他语言中等价性的证明。我不希望这些Python代码成为生产类，而是希望它们是学习函数式编程的工具。

另外，如果你想要看看这些概念在其他语言中是什么样子，或者对于下一个主题有什么想法，不要犹豫，请告诉我。

## 单子（Monad）和它们的法则

如前面所解释的，单子（Monad）是容器。对于面向对象开发者来说，它们看起来非常像[范型](https://docs.oracle.com/javase/tutorial/extra/generics/)。事实上，它们是范型的一个带有[具体法则](https://wiki.haskell.org/Monad_laws)的更为特殊的版本。这些法则存在，以允许你在和单子（Monad）打交道的时候，可以总是期待相同的行为：

*   左单位元(Left Identity);
*   右单位元(Right Identity);
*   关联性

在解释该法则之前，我还会定义类型必须是一个单子的一组操作，使用额外的通用签名，使用`arguments -> return`:

*   返回(return) `a -> M[a]`;
*   绑定(bind)函数 `(M[a], a -> M[b]) -> M[b]`

虽然类型构造和返回函数似乎很明显，但是绑定函数(在scala中，称为`flatMap`)需要一点解释：这是一个接受用`a`创建的单子的函数，以及一个接受`a`和返回用`b`创建的单子来返回用`b`创建的单子（原文是：It is a function that takes a Monad of `a`, and a function that takes `a` and returns a Monad of `b` to return a Monad of `b`）。

这可以如下在代码中写成：
```
val m: List[Int] = List(12, 34)
// m is our Monad, bein List a monadic type

val n = m flatMap (i => i.toString)
// toString is an operation that returns a String, which can be also read as a list of characters

n == List[Char]('1', '2', '3', '4')
```

为嘛？该函数接受一个Int并返回一个String（字符表），它符合上面定义的`bind`签名。它接受一个值（列表中的第n个位置）和一个接受该值的函数，并用所提供函数的结果生成一个单子（与我们使用的单子相同的类型）。

有了这个解释，让我们继续讲法则：

### 左单位元(Left Identity)

该法则规定，`(return a) bind f == f a`或者`用'a'创建一个单子并用'f'绑定，与调用'f(a)'相同`。

我们可以证明这个法则：
```
def toListString: Int => List[String] = i => List(i.toString)

val leftProperty = List(1) flatMap (toListString)
val rightProperty = toListString(1)

leftProperty == rightProperty
```

### 右单位元(Right Identity)

该法则规定，`m bind return == m`或者`在一个单子上绑定return返回相同的单子`。

我们可以证明这个法则：
```
val monad = List(1)

val boundValue = monad flatMap (List(_))

boundValue == monad
```

### 关联性

该法则规定，`m bind f bind g == m bind (i -> f(i) bind g)`或者`绑定f到一个单子，然后绑定g，与绑定一个函数（这个函数使用f生成一个单子然后绑定g到这个单子）到相同的单子相同`。

它也许听起来有点复杂，但是我们可以证明这个法则：
```
def double(i: Int): List[Int] = List(i * 2)
def triple(i: Int): List[Int] = List(i * 3)

val monad = List(1)

val leftProperty = monad flatMap double flatMap triple
val rightProperty = monad flatMap {i => double(i) flatMap triple}

leftProperty == rightProperty
```

## 你在Python中的第一个单子(Monad)

下面，我在Python中定义了Monad类，暴露了上面定义的法则，以及一些使用样例。
```py
## Monad
class Monad(object):

  """A Monadic container in python."""

  def __init__(self, containedValue):
    """init is our 'return' function here."""
    self.__value = containedValue

  def flat_map(self, f):
    return f(self.__value)

  # Overriding '==' so we can prove the laws
  def __eq__(self, o):
    return isinstance(o, type(self)) and o.__value == self.__value

  def __repr__(self):
    return "{}({})".format(self.__class__.__name__, self.__value)

## Helper functions

def to_monad(i):
  return Monad(i)

def to_doubled_monad(i):
  return Monad(i * 2)

## Proving the laws
# Left Identity
Monad(1).flat_map(to_doubled_monad) == to_doubled_monad(1)
Monad("something").flat_map(to_doubled_monad) == to_doubled_monad("something")

# Right Identity
m = Monad(1)
m == m.flat_map(lambda k: Monad(k))

m2 = to_monad({1: 2})
m2 == m2.flat_map(to_monad)

# Associativity
m = Monad(1)
left = m.flat_map(to_monad).flat_map(to_doubled_monad)
right = m.flat_map(lambda k: to_monad(k).flat_map(to_doubled_monad))
```

## 你在Python中的第二个单子(Monad)

正如上面的例子所描述的，`Monad`类型并未做什么特别的事，它只是像一个容器一样，包装实际的值。可以创建一些从Monad继承的有意义的类，例如下面的Try Monad：
```py
class Try(Monad):

    class Success(Monad):
        pass

    class Failure(Monad):
        pass

    def flat_map(self, f):
        try:
            return super(Try, self).flat_map(f)
        except Exception as e:
            return Try.Failure(e)
```

请注意，这个实现是基本的，并缺乏类型正确性，但是它对我们探索单子法则仍旧足够有趣。

## 总结

我知道我们还缺少一些重要的答案，如“为什么我需要一个单子”和“我怎么使用这一切来工作”，但是再一次说明，这是一个关于函数式编程的系列文章的第一篇（实际上是第二篇），它具有唯一目的，即向那些对函数式编程有兴趣，但觉得它是非常复杂，或可能并不需要它的人阐明函数式编程。

我会再写一些文章，涵盖其他与函数式编程密切相关的数学主题，例如函子(Functor)，Monoid，加强版函子(Applicative)。因为我有我的ISP的问题，所以我可能会推迟这写文章，因此，如果你渴望了解更多，我在推荐优秀的[为了更大的好处，学些Haskell](http://learnyouahaskell.com/a-fistful-of-monads)。

我希望你喜欢这篇文章，但如果你不喜欢（或不同意）我写的东西，那么随意在tweet上告诉我或发送电子邮件给我。

:x
