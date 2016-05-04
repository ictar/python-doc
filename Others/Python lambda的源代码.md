原文：[Source code of a Python lambda](http://xion.io/post/code/python-get-lambda-code.html)

---

_…或者：（几乎是）我做过的最邪恶的hack_

在[callee](http://github.com/Xion/callee)，[我最近发布的](http://xion.io/post/news/callee-intro.html)[Python的参数匹配库](http://callee.readthedocs.org)中，对于一个看似简单的功能，还有一个可爱的[`TODO`注意](https://github.com/Xion/callee/blob/f695ff4e1c45bfd45445ebb8014a202029a93dce/callee/general.py#L55)。当和一个简单的`lambda`断言一起使用[`Matching`构造](http://callee.readthedocs.org/en/stable/reference/general.html#callee.general.Matching)时：
```python
mock_foo.assert_called_with(Matching(lambda x: x % 2 == 0))
```

如果断言失败，那么在错误信息中看到它的_代码_将是棒棒哒。现在，它只是会打印出一些像`<Matching <function <lambda> at 0x7f5d8a06eb18>>`这样的东西。假设你不具备在你的脑袋解引用指针的超自然能力，那么对于什么地方出错了，这将不会给你带来任何直接的提示。如果它会打印出，比方说，`<Matching \x: x % 2>`，岂不是棒棒哒？[1](#fn:1)

所以我想：为嘛不试着实现这样一个机制？毕竟，这是Python呀 — 它是这样一种语言，你可以在运行时生成[全新的类](https://blog.ionelmc.ro/2015/02/09/understanding-python-metaclasses/#simple-metaclass-use)，[向后](https://docs.python.org/2/library/inspect.html#the-interpreter-stack)(或者甚至是[向前](https://docs.python.org/2/library/sys.html#sys.settrace))遍历堆栈，以及读取局部变量或者改变[import系统自身](http://xion.org.pl/2012/05/06/hacking-python-imports/)的行为。所以当然咯，获得一个短短的lambda函数的源代码是可能的，甚至是简单的，不是吗？

亲，是我_错_了。

虽然，这样没有问题：至少在我想它完成的范围中，这项任务是完全可行的。但是，一个不仅涉及普通的Python方法，还涉及AST检查，作为文本_和_字节码的源代码转换，你绝咋样？

#### 代码，所有的代码，以及……不仅仅是代码

好吧，让我们从头开始。这里是一个短短的lambda函数，也就是我们想要获得源代码的那个：

```python
is_even = lambda x: x % 2 = 0
```

如果Python标准库中的文件是可信的，这应该是很容易的。在[`inspect`模块](https://docs.python.org/2/library/inspect.html)中，有一个名为与[`getsource`](https://docs.python.org/2/library/inspect.html#inspect.getsource)没啥不同 的函数。然而，对于我们的目的，[`getsourcelines`](https://docs.python.org/2/library/inspect.html#inspect.getsourcelines)则多了几分便利，因为当lambda太长的时候，我们可以很容易的区分：

```python
def get_short_lambda_source(lambda_func):
    try:
        source_lines, _ = inspect.getsourcelines(lambda_func)
    except IOError:
        return None
    if len(source_lines) > 1:
        return None
    return source_lines[0].strip()
```

当然，如果你已经长时间使用Python编程了，那么你就会清楚，标识的文档是_不_能信任的。`except`还应该包含`TypeError`，因为当你试图传递任何Python内建给`getsourcelines`时，将会抛出这个错误。

更重要的是，“一个对象的源代码行”实际上意味着什么的模糊性。“源代码_包括_对象定义”会准确得多，而在这里，这个看似微小的区别是相当关键的。传递一个lambda函数给`getsourcelines`或者`getsource`，然后我们将获得其源代码_及其它一切东东_，包括返回行。

好啦。跟完整的`is_even =`赋值和整个`assert_called_with`调用打声招呼吧！而如果你在想：是哒，结果还将包括任何行尾注释。那么不要留下任何标记！

#### 修剪结果

显然，这超乎我们意料。也许有一种方法可以去除不必要的东东。毕竟，Python确实知道怎样解析自己：标准的[`ast`模块](https://docs.python.org/2/library/ast.html)是这方面知识的体现。或许，我们可以使用它来检查`lambda` AST节点，以便于把它 —— 只是它变回到Python代码？……

```python
def get_short_lambda_ast_node(lambda_func):
    source_text = get_short_lambda_source(lambda_func)
    if source_text:
        source_ast = ast.parse(source_text)
        return next((node for node in ast.walk(source_ast)
                     if isinstance(node, ast.Lambda)), None)
```

但事实证明，这种方式得到源文本大多可能仅是可能的。

你看，每一个实质的AST节点 — 要么是一个表达式(`ast.expr`)，要么是一个声明 (`ast.stmt`) — 有两个共同的熟悉：`lineno`和`col_offset`。合并后，它们指向原始源代码中的一个地方，这个地方就是解析该节点地方。这就是我们可以怎样找到在哪里查找我们的lambda函数定义。

看来有戏，对吧？唯一的问题是，我们不知道何时_停止_查找。这是对的：`ast.parse`创建的节点与它们的起始偏移量一起标记，而不是和其长度或结束偏移量。结果是，当涉及到从最开始的例子中取得lambda源代码时，我们能做的最好是这样：

```python
lambda x: x % 2 == 0))
```

很接近了！那些挂在那的括号显然正在嘲笑着我们，但是我们怎样移除它们呢？`lambda`基本上只是一个Python表达式，因此原则上，它可以跟集合所有东西。这对于`Matching`构造中的lambda那可是加倍的对，因为它们也许是一些较大的模拟断言的一部分：

```python
mock_foo.assert_called_with(Matching(lambda x: x % 2 == 0), Integer() & GreaterThan(42))
```

这里，无关后缀是整个`), Integer()  GreaterThan(42))`，远不止`))`。而且，当然还有无限种可能：一个是，那儿可能还会有更多的`lambda`!

#### _慢慢滴_，退回去

不过，看来，那些麻烦的小尾巴有个共同点：_它们不是语法有效的_。

直观地看，嵌套在一些其他语法结构中的`lambda`节点在其尾部的某个地方都有它们自己的关闭段 (如， `)`)。如果没有对应的起始段 (如，`Matching(`)，那么那些段将无法解析。

因此，有一个疯狂的想法。我们所拥有的是无效的Python，而只是因为一些数目不详的多余字符。要不我们试着一个一个地移除它们，知道获得一些语法正确的东西，你觉得呢？如果我们不犯错，那么最后，这将是我们的lambda，仅此而已。

财富眷顾勇者，所以让我们继续前进，尝试一下：

```python
# ... continuing get_short_lambda_source() ...

source_text = source_lines[0].strip()
lambda_node = get_short_lambda_ast_node(lambda_func)

lambda_text = source_text[lambda_node.col_offset:]
min_length = len('lambda:_')  # shortest possible lambda expression
while len(lambda_text) > min_length:
    try:
        ast.parse(lambda_text)
        return lambda_text
    except SyntaxError:
        lambda_text = lambda_text[:-1]
return None
```

考虑到，我们基本是从霍格沃茨图书馆的禁书区中尘封的大部头上学到的，这里的魔法看起来很简单。只有它能通过lambda定义，那么我们就试着解析它，并看看是否成功。写着`except SyntaxError:`的那一行显然不是救命药，但是，至少我们指定了预期缓存的[_what_异常](https://docs.python.org/2/howto/doanddont.html#except)。

结果呢？_有用_。我的意思是，对于几个明显和不那么明显的测试用例，它并没有返回垃圾结果，这已经比你通常从这种规模的hack中期望返回的好多了。例如，直到这里，所有定义的lambda都能无差错提取它们的源代码。

#### 还有一个东东

所以……搞定啦？不完全呢。聪明的读者可能还记得我对一些字节码奥秘的承诺，现在就轮到它了。

尽管，我们循序渐进，字符丢弃方法取得了初步的成功，但是在有些情况下，并不会产生正确的结果。举个例子，嵌套在一个元祖中的lambda定义[2](#fn:2):

```python
>>> x = lambda _: True, 0
>>> get_short_lambda_source(x[0])
lambda _: True, 0
```

当然，我们会期待结果是`lambda _: True`，不带逗号或者0。

不幸的是，这就是我们前面的假设失败的地方。从AST抽取的代码行在语法上是有效的，及时_有_多余的字符。结果是，`ast.parse`过早的成功并返回一个不正确的定义。这应该是包含在一个元祖中的一个lambda，但是元祖显然是lambda_返回的_。

你可能会说，这是一个少见的边缘情况，而那些像这样定义函数的人罪有应得。当然，如果我们只是摆摆手，然后告诉他们我们根本无法在这里检索源代码，那么我是不会介意的。但是我的看法是，给他们提供显然_错误的_结果是不合理的！

#### 一个停止问题

反正，如果我们可以解决这个问题，就要解决。并排地看看预期的源代码和我们已经提取出来的源代码：

```python
lambda _: True
lambda _: True, 0
```

第二行不只是更长：它还_做得更多_。它不仅是定义一个lambda；它定义了lambda，糅合了一个常量`0`，然后将它们都打包到一个元祖中。相对于初始的，至少有两个额外的步骤。

这些步骤还有更准确的名字：它们是_字节码指令_。在执行之前，每一片Python源代码被编译为二进制字节码，因为解释器只能与这种表现形式一起工作。编译通常发生在一个Python模块被首次导入，产生对应_.py_文件的_.pyc_文件时，才会发生。随后的导入将简单地重用缓存的字节码。

此外，任何函数或类对象在运行时都访问其字节码（只读）。甚至有一个[专门的数据类型](http://late.am/post/2012/03/26/exploring-python-code-objects.html)来保存它 —— 简称`code` —— 在其属性之一下带有原始字节的缓冲区。

最后，该字节码编译器自身也作为内置的[`compile`函数](https://docs.python.org/2/library/functions.html#compile)提供给Python程序。与其同类[`eval`](https://docs.python.org/2/library/functions.html#eval)
和[`exec`](https://docs.python.org/2/reference/simple_stmts.html#exec)（希望是少见的现象）相比，它并不常见，但是它接近相同的Python内部机制。

所以，怎样把它们全都加在一起呢？想法是，基本上，交叉检查lambda所谓的源代码及其自身的_字节_码。即使语法有效。仍然需要裁减多余部分。因此，我们可以简单的继续去除字符，知道字节码匹配：

```python
lambda_text = source_text[lambda_node.col_offset:]
lambda_body_text = source_text[lambda_node.body.col_offset:]
min_length = len('lambda:_')  # shortest possible lambda expression
while len(lambda_text) > min_length:
    try:
        code = compile(lambda_body_text, '<unused filename>', 'eval')
        if len(code.co_code) == len(lambda_func.__code__.co_code):
            return lambda_text
    except SyntaxError:
        pass
    lambda_text = lambda_text[:-1]
    lambda_body_text = lambda_body_text[:-1]
return None
```

好了，也许没有确切的字节[3](#fn:3)，但停止在相同的字节码长度也是不错的侧脸。作为明显的奖励，`compile`也将会注意检测候选源代码中的语法错误，所以我们不再需要`ast`解析了。

#### 快速升级！

信不信由你，但是对于这个解决方案，已经没有更多的异议了，你可以通过查看[这个要点](https://gist.github.com/Xion/617c1496ff45f3673a5692c3b0e3f75a)来看看它完整的部分。

这是否意味着它也能在[_callee_库](https://github.com/Xion/callee)中客串一下？……

不，恐怕不行。

通常情况下，我并不是那种，嗯，对于难题的_大胆_解决方案退避三舍的人。但在这个例子中，需要的hack幅度太大了，结果不够理想，该功能的优先级不是真的那么高，而它所引入的维护负担最有可能过大。

最后，想到这个超棒：关于你能如何使用Python来做一些基础工作的另一个例子。尽管如此，我们必须不能陷入我们是否能够做什么，而忘记我们应该做什么。


* * *

1.  反斜杠 (`\`) 是lambda函数在Haskell中的表示方式。我们想要简短而亲切，因此它感觉是一个自然的选择。 [↩](#fnref:1 "Jump back to footnote 1 in the text")

2.  这不是来自一个Python REPL的一个实际片段，因为`inspect.getsourcelines`要求该对象要在_.py_文件中定义。 [↩](#fnref:2 "Jump back to footnote 2 in the text")

3.  为什么我们不会总是得到相同的字节码？简单回答是，一些指令可能被换成它们的近似等值。

    详细回答是，使用`compile`，我们不能够原始lambda的确切封闭的环境。当一个函数指向一个_自由变量_（例如，`lambda x: x + foo`中的`foo`）时，该变量值来自其闭包。对于临时的lambda来说，这通常是它的_外函数_的局部范围。

    然而，`compile`生成的代码与这些局部范围没有任何关联。因此，所有的自由名都假设指向_全局_变量。由于Python为引用局部名和全局名使用不同的字节码指令 (`LOAD_FAST` vs `LOAD_GLOBAL`)，`compile`的结果会区别于常规方法生成的字节码。 [↩](#fnref:3 "Jump back to footnote 3 in the text")