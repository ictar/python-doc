原文：[The Magic of RPython](https://kirbyfan64.github.io/posts/the-magic-of-rpython.html)

---

[RPython](http://rpython.readthedocs.org/en/latest/) is a really nice translation framework that converts a (very) restricted subset of Python 2 to C code. Better yet, RPython will generate JITs for your interpreters. Although there are very good articles on how to write interpreters with RPython, I don't often find anything that describes the language itself. My goal with this post is to do just that: describe RPython itself. I'm going to leave out the things about the JITs; the RPython FAQ links to a good tutorial about that.


## RPython enters and exits

Your RPython programs/interpreters will often begin like this:
```py
def entry_point(argv):
    # this is your program's main function
    return 0

def target(driver, args):
    # this is run at compile time
    return entry_point, None
```

You'd run RPython kind of like this:
```sh
$ python path_to_pypy_source/rpython/bin/rpython -O0 my_program.py
```

The `-O0` turns off all optimizations, which makes compile times _much_ faster while testing.

If you're lazy like me, you can define an alias:
```sh
$ alias rpython="python path_to_pypy_source/rpython/bin/rpython"
```

The `target` function lets you set certain or check command-line arguments passed to RPython. For instance:
```py
def target(driver, args):
    # The default output file name for xyz.py is xyz-c
    if driver.exe_name == 'xyz-c':
        driver.exe_name = 'bin/xyz'
    return entry_point, None
```

I have _no_ clue what the `None` is for, though.

**EDIT:** As Chris pointed out in the comments and Maciej Fijalkowski in an e-mail, the `None` represents the type of the arguments that are given to `entry_point`. See [rpython/translator/goal/targetrpystonex.py](https://bitbucket.org/pypy/pypy/src/tip/rpython/translator/goal/targetrpystonex.py?at=py3k) for an example.


## RPython is half-Python, half-not-Python, and Python

Notice that I said that `target` is run at compile time. While other Python translation frameworks, such as Shedskin and Cython, analyse the program's static AST, RPython analyses its bytecode. Here's an example:
```py
print 'This is run during compile time!' # guess when this is run?

def entry_point(argv):
    print 'This is run at run time!'
    return 0
```

This has really cool implications. For one thing, RPython lazily compiles functions. For instance:
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

That means we can do lots of compile-time computations:
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

## RPython is statically-typed

In short:
```py
def entry_point(argv):
    x = 123 # ok
    x = '456' # error!
```

Notice that no variable annotations were needed. This is because RPython uses type inference.

RPython also performs compile-time null checking under certain situations:
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

## RPython has confusing error messages

Whenever an error occurs during compilation, most compilers will output something like:

`error: myfile.whatever:22: variable 'xyz' may be 'null' when used here`

Not RPython! This is what I get when I try to compile the above snippet:
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

Wow! RPython's exceptions generally go like this:


### FlowingError

RPython can prove at compile-time that some run-time computation may fail. This usually means one of:

*   You're referencing a variable you never defined (the error message will go something like `global variable 'x' is not defined`).
*   You're trying to get the `len` of `None`.

### UnionError

A type conflict. Whenever you get this, RPython will show the internal types that caused the error.

Take this program:
```py
def f(b):
    return 1 if b else None

def entry_point(argv):
    print f(len(argv)==2)+2 # compile-time error
    return 0

def target(driver, args):
    return entry_point, None
```

RPython gives this error message:
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

This tells us that the type conflict is between an integer and `None`. Also note that there are no absolute line numbers. RPython will sometimes show just the function where the error occurred (in this case, `f`) and the internal, simplified code that is near the cause of there error.

These errors often show much more info:

*   The integer is the constant `1`.
*   It is non-negative (`nonneg=True`) but signed (`unsigned=False`).

### BlockError

This means that type inference couldn't succeed. Take this program:
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

This reads one or more numbers from <cite>stdin</cite> and prints the first one added to `2.3`. You may have noticed an error in the program. When compiling, this happens:
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

What?? What RPython means is that it can't infer the type of `data`. Why? Because somewhere in `rd` we put a plain `return`. In Python, this returns `None`. In RPython? It's an error.

One gotcha about these errors is that they occur when the type problems surface. Notice that the error didn't occur in `rd`'s definition; it occurred when we tried to slice it. This can be a little odd until you get the hang of it.


### AssertionError

Various meanings. Sometimes they have an error message; sometimes they don't. When they don't, your best bet is to go to the line in RPython source that raised the error and look for any helpful comments or try to figure out on your own.


### AnnotatorError

This may have various meanings, but it basically means that an error occurred while trying to annotate the types. The most common reason in my experience is an attribute error. For instance, this:
```py
def entry_point(argv):
    print argv.x
    return 0
```

Gives:
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

Also note the types again. Here, it's telling us it's a list (`SomeList`) of non-nullable strings (`listdef=&lt;[SomeString(no_nul=True)]&gt;`).


## RPython takes a hint

For instance:
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

This gives:
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

The solution? You can use an assertion:
```py
def entry_point(argv):
    a = C() if len(argv) == 3 else B() # Ok; 'a' is of type A
    assert isinstance(a, B)
    print a.x() # Ok; this will never run if 'a' is of type 'C'
    return 0
```

Or an `if` statement:
```py
def entry_point(argv):
    a = C() if len(argv) == 3 else B() # Ok; 'a' is of type A
    if isinstance(a, B):
        print a.x()
    elif isinstance(a, C):
        print a.x(1)
    return 0
```

## RPython drops you some neat info

Notice that, when an error occurs, RPython drops you into an instance of [pdb](https://docs.python.org/2/library/pdb.html). This means you can inspect the variables of RPython's internals! This can come in handy for debugging the more spurious errors. You can inspect the various variables and see what RPython thinks things are.


## RPython is polite

Take this program:
```py
def entry_point(argv):
    print argv[1]
    return 0

def target(driver, args):
    return entry_point, None
```

If you give it no arguments, it'll throw an `IndexError`, right? WRONG! If I build it without optimizations, it'll print `None`; if I use optimizations (`-O2`), it'll segfault. Why? See, it would be rude to throw an exception! After all, you asked it for the first argument. Therefore, it returns a safe value: `None`. However, when you build it with optimizations, RPython couldn't care less about your computers memory, so it happily...crashes. However, try this:
```py
def entry_point(argv):
    try:
        print argv[1]
    except:
        print 'Too few arguments!'
    return 0
```

This will correctly print "Too few arguments!" if given no arguments. See, now that you put a `try` block around it, RPython knows you want an exception, so it'll throw one.

However, take this:
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

This will segfault when build with `-O2`. But we put a `try` block! RPython analyses the function individually in this case, so it doesn't pick up the `try` block in `entry_point`. To circumvent this, put another `try` block around `f` that explicitly re-raises any errors:
```py
def f(x):
    try:
        return x[1]
    except:
        raise
```

## RPython is very restricted

Here are a few things that don't work:

*   Any builtins not found as `builtin_xxx` in [rpython/annotator/builtin.py](https://bitbucket.org/pypy/pypy/src/default/rpython/annotator/builtin.py).
*   Printing unicode strings (use `print <span class="pre">string.encode('utf-8')`).
*   Slicing any negative indices other than `-1`. If RPython can't prove an index isn't non-negative or `-1`, a compile-time error will be thrown. You can use an assertion (like `assert the_index &gt;= 0`; see the above section on hints).
*   Most Python modules other than `os` and `math` (and maybe a few others).
*   Sets.
*   Multiple inheritance.
*   Several `str` methods (such as `*just` and `zfill`). Some others work take slightly different argument counts.
*   `with` blocks. Use `try..finally`.
*   `sys.stdin`, `sys.stdout`, and `sys.stderr`.
*   `raw_input`.
*   Lots and lots and lots of other stuff!

I believe `OrderedDict` works, but I'm not quite sure.

Figuring some of the other restrictions is simply trial-and-error.

For getting around `sys.std*`, you can use this function to read a line from `stdin`:
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

For reading all the lines in `stdin` into a list:
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
For reading the lines in stdin into a single string:
```py
import os

def readall():
    res = ''
    while True:
        buf = os.read(0, 16)
        if not buf: return res
        res += buf
```
For writing to stderr:
```py
import os

def write_err(msg):
    os.write(2, msg+'\n')
And for writing to stdout without any trailing newlines or spaces:

import os

def write(msg):
    os.write(1, msg)
```

## RPython is fun!

Maybe I'm weird, but RPython is still really cool. Once you get the hang of the oddities, everything else kind of starts to fall into place.


## Need help?

You can ask the [PyPy mailing list](https://mail.python.org/mailman/listinfo/pypy-dev). They helped me with several slip-ups while writing an interpreter in RPython.


### Read the docs!

Also, read through the [RPython](http://rpython.readthedocs.org/en/latest/) documentation. It's very exhaustive and mentions stuff that I can't in this short space.
