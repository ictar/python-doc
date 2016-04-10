原文：[The Magic of RPython](https://kirbyfan64.github.io/posts/the-magic-of-rpython.html)

---

[RPython](http://rpython.readthedocs.org/en/latest/)是一个非常好的翻译框架，它将一个（非常）有限的Python 2子集转换成C代码。更重要的是，RPython将为你的解释器生成JIT。虽然有非常棒的关于如何用RPython编写解释器的文章，但是我很少发现任何描述语言本身的文章。这篇文章的目的是能够做到这一点：描述RPython自身。我将不管关于JIT的东东；RPython FAQ链接了一个关于这个的很好的教程。


## RPython进入和退出

你的RPython程序/解释器往往是这样开始的：
```py
def entry_point(argv):
    # this is your program's main function
    return 0

def target(driver, args):
    # this is run at compile time
    return entry_point, None
```

你大概会像这样运行RPython：
```sh
$ python path_to_pypy_source/rpython/bin/rpython -O0 my_program.py
```

`-O0`选项关闭了所有的优化，这使得在测试的时候，编译快得多。

如果你像我一样懒，那么可以定义一个别名：
```sh
$ alias rpython="python path_to_pypy_source/rpython/bin/rpython"
```

`target`函数让你可以设置特定的，或者检查传递给RPython的命令行参数。例如：
```py
def target(driver, args):
    # The default output file name for xyz.py is xyz-c
    if driver.exe_name == 'xyz-c':
        driver.exe_name = 'bin/xyz'
    return entry_point, None
```

虽然，我并没有关于`None`是用来干嘛的任何提示。

**编辑：** 正如Chris在评论中指出的，以及Maciej Fijalkowski在邮件中提到的，`None`表示了传递给`entry_point`的参数类型。以[rpython/translator/goal/targetrpystonex.py](https://bitbucket.org/pypy/pypy/src/tip/rpython/translator/goal/targetrpystonex.py?at=py3k)为例。


## RPython是半Python的，半非Python的，以及Python的

注意，我提到`target`是在编译时运行的。其他的Python翻译框架，例如Shedskin和Cython，分析程序的静态AST，而RPython则分析它的字节码。下面是一个例子：
```py
print 'This is run during compile time!' # guess when this is run?

def entry_point(argv):
    print 'This is run at run time!'
    return 0
```

这有非常酷的隐喻。一方面，RPython懒洋洋的编译函数。例如：
```py
def f():
    # This is never compiled by RPython because 'f' is never called
    return 123

def g():
    # This is compiled by RPython because 'g' *is* called
    return 456

def entry_point(argv):
    print g()
```

这意味着我们可以进行大量的编译时计算：
```py
import sys

if sys.platform == 'windows':
    def plat(): return 'Windows!'
elif sys.platform.startswith('linux'):
    def plat(): return 'Linux!'
else:
    def plat(): return 'Who cares?'

def entry_point(argv):
    print plat()
    return 0
```

## RPython是静态类型的

简而言之：
```py
def entry_point(argv):
    x = 123 # ok
    x = '456' # error!
```

注意，不需要任何变量注释。这是因为RPython使用类型推断。

在某些情况下，RPython还执行编译时空校验：
```py
def entry_point(argv):
    if len(argv) == 1:
        x = None
    else:
        x = 0
    print x+1+2 # compile-time error
    return 0

def target(driver, args):
    return entry_point, None
```

## RPython具有混乱的错误信息

每当在编译时出现错误，大多数的编译器将会输出类似的信息：

`error: myfile.whatever:22: variable 'xyz' may be 'null' when used here`

但是RPython不是这样的！这是当我尝试编译上面的代码片段时得到的：
```
[translation:info] Error:
[translation:info]    File "/media/ryan/stuff/pypy/rpython/translator/goal/translate.py", line 316, in main
[translation:info]     drv.proceed(goals)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/translator/driver.py", line 539, in proceed
[translation:info]     return self._execute(goals, task_skip = self._maybe_skip())
[translation:info]    File "/media/ryan/stuff/pypy/rpython/translator/tool/taskengine.py", line 114, in _execute
[translation:info]     res = self._do(goal, taskcallable, *args, **kwds)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/translator/driver.py", line 276, in _do
[translation:info]     res = func()
[translation:info]    File "/media/ryan/stuff/pypy/rpython/translator/driver.py", line 313, in task_annotate
[translation:info]     s = annotator.build_types(self.entry_point, self.inputtypes)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/annrpython.py", line 82, in build_types
[translation:info]     flowgraph, inputcells = self.get_call_parameters(function, args_s, policy)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/annrpython.py", line 103, in get_call_parameters
[translation:info]     desc.pycall(schedule, args, annmodel.s_ImpossibleValue)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/description.py", line 301, in pycall
[translation:info]     result = self.specialize(inputcells, op)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/description.py", line 297, in specialize
[translation:info]     return self.specializer(self, inputcells)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/specialize.py", line 80, in default_specialize
[translation:info]     graph = funcdesc.cachedgraph(key, builder=builder)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/description.py", line 245, in cachedgraph
[translation:info]     graph = self.buildgraph(alt_name, builder)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/description.py", line 208, in buildgraph
[translation:info]     graph = translator.buildflowgraph(self.pyobj)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/translator/translator.py", line 54, in buildflowgraph
[translation:info]     graph = build_flow(func)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/flowspace/objspace.py", line 42, in build_flow
[translation:info]     ctx.build_flow()
[translation:info]    File "/media/ryan/stuff/pypy/rpython/flowspace/flowcontext.py", line 448, in build_flow
[translation:info]     self.record_block(block)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/flowspace/flowcontext.py", line 456, in record_block
[translation:info]     next_pos = self.handle_bytecode(next_pos)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/flowspace/flowcontext.py", line 548, in handle_bytecode
[translation:info]     res = getattr(self, methodname)(oparg)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/flowspace/flowcontext.py", line 266, in BINARY_OP
[translation:info]     w_result = operation(w_1, w_2).eval(self)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/flowspace/operation.py", line 91, in eval
[translation:info]     result = self.constfold()
[translation:info]    File "/media/ryan/stuff/pypy/rpython/flowspace/operation.py", line 121, in constfold
[translation:info]     raise FlowingError(msg)
[translation:ERROR] FlowingError:
[translation:ERROR]
[translation:ERROR] add(None, 1) always raises <type 'exceptions.TypeError'>: unsupported operand type(s) for +: 'NoneType' and 'int'
[translation:ERROR]
[translation:ERROR] In <FunctionGraph of (nl:1)entry_point at 0x7f988a349090>:
[translation:ERROR] Happened at file nl.py line 6
[translation:ERROR]
[translation:ERROR]         print x+1+2 # compile-time error
[translation:ERROR]
```

哇！RPython的异常通常是这样的：

### FlowingError

RPython可以在编译时证明，一些运行时计算可能会失败。这通常意味着下述之一：

*   你引用了一个你从未定义的变量 (错误信息会像这样：`global variable 'x' is not defined`)。
*   你试图取得`None`的`len`。

### UnionError

一个类型冲突。每当你得到这个错误时，RPython将显示引发这个错误的内部类型。

就拿这个程序来说：
```py
def f(b):
    return 1 if b else None

def entry_point(argv):
    print f(len(argv)==2)+2 # compile-time error
    return 0

def target(driver, args):
    return entry_point, None
```

RPython给出了这样的错误信息：
```
[translation:info] Error:
[translation:info]    File "/media/ryan/stuff/pypy/rpython/translator/goal/translate.py", line 316, in main
[translation:info]     drv.proceed(goals)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/translator/driver.py", line 539, in proceed
[translation:info]     return self._execute(goals, task_skip = self._maybe_skip())
[translation:info]    File "/media/ryan/stuff/pypy/rpython/translator/tool/taskengine.py", line 114, in _execute
[translation:info]     res = self._do(goal, taskcallable, *args, **kwds)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/translator/driver.py", line 276, in _do
[translation:info]     res = func()
[translation:info]    File "/media/ryan/stuff/pypy/rpython/translator/driver.py", line 313, in task_annotate
[translation:info]     s = annotator.build_types(self.entry_point, self.inputtypes)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/annrpython.py", line 89, in build_types
[translation:info]     return self.build_graph_types(flowgraph, inputcells, complete_now=complete_now)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/annrpython.py", line 143, in build_graph_types
[translation:info]     self.complete()
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/annrpython.py", line 197, in complete
[translation:info]     self.complete_pending_blocks()
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/annrpython.py", line 192, in complete_pending_blocks
[translation:info]     self.processblock(graph, block)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/annrpython.py", line 338, in processblock
[translation:info]     self.flowin(graph, block)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/annrpython.py", line 473, in flowin
[translation:info]     self.follow_link(graph, link, knowntypedata)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/annrpython.py", line 561, in follow_link
[translation:info]     self.addpendingblock(graph, link.target, inputs_s)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/annrpython.py", line 185, in addpendingblock
[translation:info]     self.mergeinputargs(graph, block, cells)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/annrpython.py", line 375, in mergeinputargs
[translation:info]     unions = [annmodel.unionof(c1,c2) for c1, c2 in zip(oldcells,inputcells)]
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/model.py", line 658, in unionof
[translation:info]     s1 = pair(s1, s2).union()
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/binaryop.py", line 755, in union
[translation:info]     return obj.noneify()
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/model.py", line 126, in noneify
[translation:info]     raise UnionError(self, s_None)
[translation:ERROR] UnionError:
[translation:ERROR]
[translation:ERROR] Offending annotations:
[translation:ERROR]   SomeInteger(const=1, knowntype=int, nonneg=True, unsigned=False)
[translation:ERROR]   SomeNone()
[translation:ERROR]
[translation:ERROR] In <FunctionGraph of (nl2:1)f at 0x7f6801abdb50>:
[translation:ERROR] <return block>
[translation:ERROR] Processing block:
[translation:ERROR]  block@3 is a <class 'rpython.flowspace.flowcontext.SpamBlock'>
[translation:ERROR]  in (nl2:1)f
[translation:ERROR]  containing the following operations:
[translation:ERROR]        v0 = bool(b_0)
[translation:ERROR]  --end--
```

这告诉我们，类型冲突是位于一个整数和`None`之间的。还要注意，没有绝对的行号。RPython有时会只显示错误出现的函数 (在这个例子中，是`f`)以及内部的，出现在错误原因旁边的简化代码。

这些错误通常显示更多信息：

*   整数是常量`1`。
*   它是非负数 (`nonneg=True`)，但却是有符号的 (`unsigned=False`)。

### BlockError

这意味着类型推断失败了。就拿这个程序来说：
```py
import os

def rd():
    'Read all of stdin'
    res = ''
    while True:
        buf = os.read(0, 1)
        if buf == '': return
        res += buf
    return res

def entry_point(argv):
    data = rd()[:-1].split(' ')
    print float(data[0])+2.3
    return 0

def target(driver, args):
    return entry_point, None
```

这从标准输入中读取一个或多个数字，然后打印第一个数字加上`2.3`的和。你可能已经注意到程序中的一个错误。编译时，发生了下面的事：
```
[translation:info] Error:
[translation:info]    File "/media/ryan/stuff/pypy/rpython/translator/goal/translate.py", line 316, in main
[translation:info]     drv.proceed(goals)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/translator/driver.py", line 539, in proceed
[translation:info]     return self._execute(goals, task_skip = self._maybe_skip())
[translation:info]    File "/media/ryan/stuff/pypy/rpython/translator/tool/taskengine.py", line 114, in _execute
[translation:info]     res = self._do(goal, taskcallable, *args, **kwds)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/translator/driver.py", line 276, in _do
[translation:info]     res = func()
[translation:info]    File "/media/ryan/stuff/pypy/rpython/translator/driver.py", line 313, in task_annotate
[translation:info]     s = annotator.build_types(self.entry_point, self.inputtypes)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/annrpython.py", line 89, in build_types
[translation:info]     return self.build_graph_types(flowgraph, inputcells, complete_now=complete_now)
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/annrpython.py", line 143, in build_graph_types
[translation:info]     self.complete()
[translation:info]    File "/media/ryan/stuff/pypy/rpython/annotator/annrpython.py", line 219, in complete
[translation:info]     raise annmodel.AnnotatorError(text)
[translation:ERROR] AnnotatorError:
[translation:ERROR]
[translation:ERROR] Blocked block -- operation cannot succeed
[translation:ERROR]
[translation:ERROR]     v1 = getslice(v0, (None), (-1))
[translation:ERROR]
[translation:ERROR] In <FunctionGraph of (nn:12)entry_point at 0x7f7558a750d0>:
[translation:ERROR] Happened at file nn.py line 13
[translation:ERROR]
[translation:ERROR] ==>     data = rd()[:-1].split(' ')
[translation:ERROR]         print float(data[0])+2.3
[translation:ERROR]
[translation:ERROR] Known variable annotations:
[translation:ERROR]  v0 = SomeNone()
[translation:ERROR]
```

神马？RPython表达的是，它无法推断出`data`的类型。为嘛？因为在`rd`中的某个地方，我们放了一个普通的`return`。在Python中，这返回`None`。在RPython中呢？这是个错误。

有关这些错误的一个小问题是，当类型问题出现时，它们发生了。注意，错误并未发生在`rd`的定义中；错误在我们试图对它切片时发生。这可能有点怪，直到你得到它的窍门。


### AssertionError

不同的含义。有时，它们有一个错误信息；但有时没有。当没有的时候，你最好的办法是找到引发该错误的RPython源代码的行那里，然后找找有没有用用的评论，或者尝试自己揣摩。


### AnnotatorError

这可能具有不同的含义，但它基本上意味着在试图注释类型的时候发生了一个错误。在我的经验中，最常见的原因是一个属性错误。例如，这个：
```py
def entry_point(argv):
    print argv.x
    return 0
```

得到：
```
[translation:ERROR] AnnotatorError:
[translation:ERROR]
[translation:ERROR] Cannot find attribute 'x' on SomeList(listdef=<[SomeString(no_nul=True)]mr>)
[translation:ERROR]
[translation:ERROR]
[translation:ERROR]     v0 = getattr(argv_0, ('x'))
[translation:ERROR]
[translation:ERROR] In <FunctionGraph of (nn:1)entry_point at 0x7feeac22e090>:
[translation:ERROR] Happened at file nn.py line 2
[translation:ERROR]
[translation:ERROR] ==>     print argv.x
[translation:ERROR]
[translation:ERROR] Known variable annotations:
[translation:ERROR]  argv_0 = SomeList(listdef=<[SomeString(no_nul=True)]mr>)
[translation:ERROR]
[translation:ERROR] Processing block:
[translation:ERROR]  block@3 is a <class 'rpython.flowspace.flowcontext.SpamBlock'>
[translation:ERROR]  in (nn:1)entry_point
[translation:ERROR]  containing the following operations:
[translation:ERROR]        v0 = getattr(argv_0, ('x'))
[translation:ERROR]        v1 = str(v0)
[translation:ERROR]        v2 = simple_call((function rpython_print_item), v1)
[translation:ERROR]        v3 = simple_call((function rpython_print_newline))
[translation:ERROR]  --end--
```

再次注意到类型。这里，它告诉我们，它是一个非空字符串的列表(`SomeList`) (`listdef=<[SomeString(no_nul=True)]>`)。


## RPython接受一个提示

例如：
```py
class A(object):
    pass

class B(A):
    def x(self): return 'y'

class C(A):
    def x(self, n): return 'z'

def entry_point(argv):
    a = C() if len(argv) == 3 else B() # Ok; 'a' is of type A
    print a.x() # Error! RPython can't prove that 'a' is of type B, so it doesn't know which signature of 'x' to use
    return 0

def target(driver, args):
    return entry_point, None
```

得到：
```
[translation:ERROR] AnnotatorError:
[translation:ERROR]
[translation:ERROR] signature mismatch: x() takes exactly 2 arguments (1 given)
[translation:ERROR]
[translation:ERROR]
[translation:ERROR] Occurred processing the following simple_call:
[translation:ERROR]   <MethodDesc 'x' of <ClassDef 'nn.C'> bound to <ClassDef 'nn.C'> {}> returning
[translation:ERROR]
[translation:ERROR]     v1 = simple_call(v0)
[translation:ERROR]
[translation:ERROR] In <FunctionGraph of (nn:10)entry_point at 0x7f1c3d7081d0>:
[translation:ERROR] Happened at file nn.py line 12
[translation:ERROR]
[translation:ERROR] ==>     print a.x() # Error! RPython can't prove that 'a' is of type B
[translation:ERROR]
[translation:ERROR] Known variable annotations:
[translation:ERROR]  v0 = SomePBC(can_be_None=False, descriptions={...1...}, knowntype=instancemethod, subset_of=None)
[translation:ERROR]
[translation:ERROR] Processing block:
[translation:ERROR]  block@39 is a <class 'rpython.flowspace.flowcontext.SpamBlock'>
[translation:ERROR]  in (nn:10)entry_point
[translation:ERROR]  containing the following operations:
[translation:ERROR]        v0 = getattr(v2, ('x'))
[translation:ERROR]        v1 = simple_call(v0)
[translation:ERROR]        v3 = str(v1)
[translation:ERROR]        v4 = simple_call((function rpython_print_item), v3)
[translation:ERROR]        v5 = simple_call((function rpython_print_newline))
[translation:ERROR]  --end--
```

解决方法呢？你可以使用断言：
```py
def entry_point(argv):
    a = C() if len(argv) == 3 else B() # Ok; 'a' is of type A
    assert isinstance(a, B)
    print a.x() # Ok; this will never run if 'a' is of type 'C'
    return 0
```

或者`if`语句：
```py
def entry_point(argv):
    a = C() if len(argv) == 3 else B() # Ok; 'a' is of type A
    if isinstance(a, B):
        print a.x()
    elif isinstance(a, C):
        print a.x(1)
    return 0
```

## RPython带给你一些巧妙的信息

注意，当错误发生时，RPython会让你进入到[pdb](https://docs.python.org/2/library/pdb.html)的一个实例中。这意味着，你可以检测RPython的内部变量！这可以在调试更虚假的错误时派上用场。你可以检查各个变量，看看RPython如何理解它们。


## RPython是礼貌的

就拿这个程序来说：
```py
def entry_point(argv):
    print argv[1]
    return 0

def target(driver, args):
    return entry_point, None
```

如果你不提供任何参数，那么它会抛出一个`IndexError`，对吗？不是的！如果我在不优化的情况下构建它，那么它将打印出`None`；如果我使用优化 (`-O2`)，那么它会出现段错误。为嘛？你看，抛出异常是很粗鲁的。毕竟，你向它请求第一个参数。因此，它返回了一个安全值：`None`。然而，当你在不优化的情况下构建它，那么RPython一点儿都不在乎你的电脑内存，因此它高兴地……崩溃。然而，试试这个：
```py
def entry_point(argv):
    try:
        print argv[1]
    except:
        print 'Too few arguments!'
    return 0
```

如果为给定参数，那么这将正确的打印"Too few arguments!"。你看，现在你在它周围放置了一个`try`块，RPython知道你想要的是异常，所以它会抛出一个异常。

然而，看看这个：
```py
def f(x): return x[1]

def entry_point(argv):
    try:
        print f(argv)
    except:
        print 'Too few arguments!'
    return 0

def target(driver, args):
    return entry_point, None
```

这在与`-O2`选项一起构建的时候，会出现段错误。但是，我们放了一个`try`块呀！在这种情况下，RPython单独分析该函数，所以它并不会考虑`entry_point`中的`try`块。为了规避这一点，请把另一个`try`块放到`f`中，让它显式的重新引发错误：
```py
def f(x):
    try:
        return x[1]
    except:
        raise
```

## RPython是非常受限的

这里有一些不能用的东东：

*   在[rpython/annotator/builtin.py](https://bitbucket.org/pypy/pypy/src/default/rpython/annotator/builtin.py)中，不能作为`builtin_xxx`发现任何内建。
*   打印Unicode字符串(使用`print <span class="pre">string.encode('utf-8')`)。
*   除了`-1`以外，使用任何复数索引进行切片。如果RPython不能证明一个索引不是非负的，或者`-1`，那么将会抛出一个运行时错误。你可以使用一个断言 (例如`assert the_index >= 0`；见上面的部分)。
*   大多数Python模块，除了`os`和`math` (也许还有一些其他的)。
*   集合。
*   多重继承。
*   一些`str`方法 (例如`*just`和`zfill`)。一些其他方法接受略有不同的参数个数。
*   `with`块。使用`try..finally`。
*   `sys.stdin`, `sys.stdout`, 和`sys.stderr`。
*   `raw_input`。
*   很多很多很多的其他东西！

我相信`OrderedDict`能用，但我不能肯定。

搞清楚一些其他的限制仅仅是试错。

要避免使用`sys.std*`，你可以使用这个函数来从`stdin`中读取一行：
```py
import os

def readline():
    res = ''
    while True:
        buf = os.read(0, 16)
        if not buf: return res
        res += buf
        if res[-1] == '\n': return res[:-1]
```

要将`stdin`中所有的行读到一个列表中：
```py
import os

def readlines():
    res = []
    cur = ''
    while True:
        buf = os.read(0, 16)
        if not buf: return res
        cur += buf
        if cur[-1] == '\n': res.append(cur[:-1])
```

要读取stdin中的行到一个单一字符串中：
```py
import os

def readall():
    res = ''
    while True:
        buf = os.read(0, 16)
        if not buf: return res
        res += buf
```

要写入到stderr：
```py
import os

def write_err(msg):
    os.write(2, msg+'\n')
And for writing to stdout without any trailing newlines or spaces:

import os

def write(msg):
    os.write(1, msg)
```

## RPython是有趣的！

也许我是怪异的，但RPython确实很酷。一旦你获得了奇特的窍门，那么一切水到渠成。


## 需要帮助吗？

你可以问问[PyPy邮件列表](https://mail.python.org/mailman/listinfo/pypy-dev)。当在RPython中编写一个解释器时，他们帮助我处理了一些疏忽。


### 阅读文档！

此外，通读[RPython](http://rpython.readthedocs.org/en/latest/)文档。它非常详尽，并且提到了我在这篇短短的文章中无法提到的东东。
