原文：[What's New in Python](https://docs.python.org/3.6/whatsnew/index.html)

# Python 3.6新特性

发布版本：| 3.6.0b2  
---|---  
日期：| 2016年10月11日 
  
本文介绍了与3.5相比，Python 3.6的新特性。

有关详情，见[更新日志](https://docs.python.org/3.6/whatsnew/changelog.html#changelog)。

>注意
>
>抢鲜用户应该注意，本文档目前还处于草稿阶段。随着Python 3.6的发布，将会大幅度更新，因此，甚至是在阅读早期版本后，都值得回来再看看。

## 摘要 - 版本亮点

新语法特性：

  * A `global` or `nonlocal` statement must now textually appear before the first use of the affected name in the same scope. Previously this was a SyntaxWarning.
  * PEP 498: 格式化字符串
  * PEP 515: 数字文本中的下划线
  * PEP 526: 变量注解语法
  * PEP 525: 异步生成器
  * PEP 530: 异步推导式

标准库改进：

安全性改进：

  * On Linux, [`os.urandom()`](https://docs.python.org/3.6/library/os.html#os.urandom "os.urandom" ) now blocks until the system urandom entropy pool is initialized to increase the security. See the [**PEP 524**](https://www.python.org/dev/peps/pep-0524) for the rationale.
  * [`hashlib`](https://docs.python.org/3.6/library/hashlib-blake2.html#module-hashlib "hashlib: BLAKE2 hash function for Python" ) and [`ssl`](https://docs.python.org/3.6/library/ssl.html#module-ssl "ssl: TLS/SSL wrapper for socket objects" ) now support OpenSSL 1.1.0.
  * The default settings and feature set of the [`ssl`](https://docs.python.org/3.6/library/ssl.html#module-ssl "ssl: TLS/SSL wrapper for socket objects" ) have been improved.
  * The [`hashlib`](https://docs.python.org/3.6/library/hashlib-blake2.html#module-hashlib "hashlib: BLAKE2 hash function for Python" ) module has got support for BLAKE2, SHA-3 and SHAKE hash algorithms and [`scrypt()`](https://docs.python.org/3.6/library/hashlib.html#hashlib.scrypt "hashlib.scrypt" ) key derivation function.

Windows改进：

  * PEP 529: Change Windows filesystem encoding to UTF-8
  * PEP 528: Change Windows console encoding to UTF-8
  * The `py.exe` launcher, when used interactively, no longer prefers Python 2 over Python 3 when the user doesn't specify a version (via command line arguments or a config file). Handling of shebang lines remains unchanged - "python" refers to Python 2 in that case.
  * `python.exe` and `pythonw.exe` have been marked as long-path aware, which means that when the 260 character path limit may no longer apply. See [removing the MAX_PATH limitation](https://docs.python.org/3.6/using/windows.html#max-path) for details.
  * A `._pth` file can be added to force isolated mode and fully specify all search paths to avoid registry and environment lookup. See [the documentation](https://docs.python.org/3.6/using/windows.html#finding-modules) for more information.
  * A `python36.zip` file now works as a landmark to infer [`PYTHONHOME`](https://docs.python.org/3.6/using/cmdline.html#envvar-PYTHONHOME). See [the documentation](https://docs.python.org/3.6/using/windows.html#finding-modules) for more information.

新的内置特性：

  * PEP 520: Preserving Class Attribute Definition Order
  * PEP 468: Preserving Keyword Argument Order

Python 3.6中实现的PEP完整列表：

  * [**PEP 468**](https://www.python.org/dev/peps/pep-0468), Preserving Keyword Argument Order
  * [**PEP 487**](https://www.python.org/dev/peps/pep-0487), Simpler customization of class creation
  * [**PEP 495**](https://www.python.org/dev/peps/pep-0495), Local Time Disambiguation
  * [**PEP 498**](https://www.python.org/dev/peps/pep-0498), Formatted string literals
  * [**PEP 506**](https://www.python.org/dev/peps/pep-0506), Adding A Secrets Module To The Standard Library
  * [**PEP 509**](https://www.python.org/dev/peps/pep-0509), Add a private version to dict
  * [**PEP 515**](https://www.python.org/dev/peps/pep-0515), Underscores in Numeric Literals
  * [**PEP 519**](https://www.python.org/dev/peps/pep-0519), Adding a file system path protocol
  * [**PEP 520**](https://www.python.org/dev/peps/pep-0520), Preserving Class Attribute Definition Order
  * [**PEP 523**](https://www.python.org/dev/peps/pep-0523), Adding a frame evaluation API to CPython
  * [**PEP 524**](https://www.python.org/dev/peps/pep-0524), Make os.urandom() blocking on Linux (during system startup)
  * [**PEP 525**](https://www.python.org/dev/peps/pep-0525), Asynchronous Generators (provisional)
  * [**PEP 526**](https://www.python.org/dev/peps/pep-0526), Syntax for Variable Annotations (provisional)
  * [**PEP 528**](https://www.python.org/dev/peps/pep-0528), Change Windows console encoding to UTF-8 (provisional)
  * [**PEP 529**](https://www.python.org/dev/peps/pep-0529), Change Windows filesystem encoding to UTF-8 (provisional)
  * [**PEP 530**](https://www.python.org/dev/peps/pep-0530), Asynchronous Comprehensions

## 新特性

### PEP 515: Underscores in Numeric Literals

Prior to PEP 515, there was no support for writing long numeric literals with
some form of separator to improve readability. For instance, how big is
`1000000000000000`? With [**PEP 515**](https://www.python.org/dev/peps/pep-0515), though, you can use
underscores to separate digits as desired to make numeric literals easier to
read: `1_000_000_000_000_000`. Underscores can be used with other numeric
literals beyond integers, e.g. `0x_FF_FF_FF_FF`.

Single underscores are allowed between digits and after any base specifier.
More than a single underscore in a row, leading, or trailing underscores are
not allowed.

>又见

[**PEP 515**](https://www.python.org/dev/peps/pep-0515) - Underscores in
Numeric Literals

    PEP written by Georg Brandl and Serhiy Storchaka.

### PEP 523: Adding a frame evaluation API to CPython

While Python provides extensive support to customize how code executes, one
place it has not done so is in the evaluation of frame objects. If you wanted
some way to intercept frame evaluation in Python there really wasn't any way
without directly manipulating function pointers for defined functions.

[**PEP 523**](https://www.python.org/dev/peps/pep-0523) changes this by
providing an API to make frame evaluation pluggable at the C level. This will
allow for tools such as debuggers and JITs to intercept frame evaluation
before the execution of Python code begins. This enables the use of
alternative evaluation implementations for Python code, tracking frame
evaluation, etc.

This API is not part of the limited C API and is marked as private to signal
that usage of this API is expected to be limited and only applicable to very
select, low-level use-cases. Semantics of the API will change with Python as
necessary.

>又见

[**PEP 523**](https://www.python.org/dev/peps/pep-0523) - Adding a frame
evaluation API to CPython

    PEP written by Brett Cannon and Dino Viehland.

### PEP 519: Adding a file system path protocol

File system paths have historically been represented as
[`str`](https://docs.python.org/3.6/library/stdtypes.html#str "str" ) or
[`bytes`](https://docs.python.org/3.6/library/functions.html#bytes "bytes" )
objects. This has led to people who write code which operate on file system
paths to assume that such objects are only one of those two types (an
[`int`](https://docs.python.org/3.6/library/functions.html#int "int" )
representing a file descriptor does not count as that is not a file path).
Unfortunately that assumption prevents alternative object representations of
file system paths like
[`pathlib`](https://docs.python.org/3.6/library/pathlib.html#module-pathlib
"pathlib: Object-oriented filesystem paths" ) from working with pre-existing
code, including Python's standard library.

To fix this situation, a new interface represented by
[`os.PathLike`](https://docs.python.org/3.6/library/os.html#os.PathLike
"os.PathLike" ) has been defined. By implementing the [`__fspath__()`](https:/
/docs.python.org/3.6/library/os.html#os.PathLike.__fspath__
"os.PathLike.__fspath__" ) method, an object signals that it represents a
path. An object can then provide a low-level representation of a file system
path as a [`str`](https://docs.python.org/3.6/library/stdtypes.html#str "str"
) or [`bytes`](https://docs.python.org/3.6/library/functions.html#bytes
"bytes" ) object. This means an object is considered [path-
like](https://docs.python.org/3.6/glossary.html#term-path-like-object) if it
implements
[`os.PathLike`](https://docs.python.org/3.6/library/os.html#os.PathLike
"os.PathLike" ) or is a
[`str`](https://docs.python.org/3.6/library/stdtypes.html#str "str" ) or
[`bytes`](https://docs.python.org/3.6/library/functions.html#bytes "bytes" )
object which represents a file system path. Code can use
[`os.fspath()`](https://docs.python.org/3.6/library/os.html#os.fspath
"os.fspath" ),
[`os.fsdecode()`](https://docs.python.org/3.6/library/os.html#os.fsdecode
"os.fsdecode" ), or
[`os.fsencode()`](https://docs.python.org/3.6/library/os.html#os.fsencode
"os.fsencode" ) to explicitly get a
[`str`](https://docs.python.org/3.6/library/stdtypes.html#str "str" ) and/or
[`bytes`](https://docs.python.org/3.6/library/functions.html#bytes "bytes" )
representation of a path-like object.

The built-in
[`open()`](https://docs.python.org/3.6/library/functions.html#open "open" )
function has been updated to accept
[`os.PathLike`](https://docs.python.org/3.6/library/os.html#os.PathLike
"os.PathLike" ) objects as have all relevant functions in the
[`os`](https://docs.python.org/3.6/library/os.html#module-os "os:
Miscellaneous operating system interfaces." ) and
[`os.path`](https://docs.python.org/3.6/library/os.path.html#module-os.path
"os.path: Operations on pathnames." ) modules. [`PyUnicode_FSConverter()`](htt
ps://docs.python.org/3.6/c-api/unicode.html#c.PyUnicode_FSConverter
"PyUnicode_FSConverter" ) and [`PyUnicode_FSConverter()`](https://docs.python.
org/3.6/c-api/unicode.html#c.PyUnicode_FSConverter "PyUnicode_FSConverter" )
have been changed to accept path-like objects. The
[`os.DirEntry`](https://docs.python.org/3.6/library/os.html#os.DirEntry
"os.DirEntry" ) class and relevant classes in
[`pathlib`](https://docs.python.org/3.6/library/pathlib.html#module-pathlib
"pathlib: Object-oriented filesystem paths" ) have also been updated to
implement
[`os.PathLike`](https://docs.python.org/3.6/library/os.html#os.PathLike
"os.PathLike" ).

The hope in is that updating the fundamental functions for operating on file
system paths will lead to third-party code to implicitly support all [path-
like objects](https://docs.python.org/3.6/glossary.html#term-path-like-object)
without any code changes or at least very minimal ones (e.g. calling
[`os.fspath()`](https://docs.python.org/3.6/library/os.html#os.fspath
"os.fspath" ) at the beginning of code before operating on a path-like
object).

Here are some examples of how the new interface allows for
[`pathlib.Path`](https://docs.python.org/3.6/library/pathlib.html#pathlib.Path
"pathlib.Path" ) to be used more easily and transparently with pre-existing
code:

```python

    >>> import pathlib
    >>> with open(pathlib.Path("README")) as f:
    ...     contents = f.read()
    ...
    >>> import os.path
    >>> os.path.splitext(pathlib.Path("some_file.txt"))
    ('some_file', '.txt')
    >>> os.path.join("/a/b", pathlib.Path("c"))
    '/a/b/c'
    >>> import os
    >>> os.fspath(pathlib.Path("some_file.txt"))
    'some_file.txt'
    
```

(Implemented by Brett Cannon, Ethan Furman, Dusty Phillips, and Jelle
Zijlstra.)

>又见

[**PEP 519**](https://www.python.org/dev/peps/pep-0519) - Adding a file system
path protocol

    PEP written by Brett Cannon and Koos Zevenhoven.

### PEP 498: Formatted string literals

Formatted string literals are a new kind of string literal, prefixed with
`'f'`. They are similar to the format strings accepted by
[`str.format()`](https://docs.python.org/3.6/library/stdtypes.html#str.format
"str.format" ). They contain replacement fields surrounded by curly braces.
The replacement fields are expressions, which are evaluated at run time, and
then formatted using the
[`format()`](https://docs.python.org/3.6/library/functions.html#format
"format" ) protocol:

```python

    >>> name = "Fred"
    >>> f"He said his name is {name}."
    'He said his name is Fred.'
    
```

See [**PEP 498**](https://www.python.org/dev/peps/pep-0498) and the main
documentation at [Formatted string literals](https://docs.python.org/3.6/refer
ence/lexical_analysis.html#f-strings).

### PEP 526: Syntax for variable annotations

[**PEP 484**](https://www.python.org/dev/peps/pep-0484) introduced standard
for type annotations of function parameters, a.k.a. type hints. This PEP adds
syntax to Python for annotating the types of variables including class
variables and instance variables:

```python

    primes: List[int] = []
    
    captain: str  # Note: no initial value!
    
    class Starship:
        stats: Dict[str, int] = {}
    
```

Just as for function annotations, the Python interpreter does not attach any
particular meaning to variable annotations and only stores them in a special
attribute `__annotations__` of a class or module. In contrast to variable
declarations in statically typed languages, the goal of annotation syntax is
to provide an easy way to specify structured type metadata for third party
tools and libraries via the abstract syntax tree and the `__annotations__`
attribute.

>又见

[**PEP 526**](https://www.python.org/dev/peps/pep-0526) - Syntax for variable
annotations.

    PEP written by Ryan Gonzalez, Philip House, Ivan Levkivskyi, Lisa Roach, and Guido van Rossum. Implemented by Ivan Levkivskyi.

Tools that use or will use the new syntax:
[mypy](http://github.com/python/mypy),
[pytype](http://github.com/google/pytype), PyCharm, etc.

### PEP 529: Change Windows filesystem encoding to UTF-8

Representing filesystem paths is best performed with str (Unicode) rather than
bytes. However, there are some situations where using bytes is sufficient and
correct.

Prior to Python 3.6, data loss could result when using bytes paths on Windows.
With this change, using bytes to represent paths is now supported on Windows,
provided those bytes are encoded with the encoding returned by [`sys.getfilesy
stemencoding()`](https://docs.python.org/3.6/library/sys.html#sys.getfilesyste
mencoding "sys.getfilesystemencoding" ), which now defaults to `'utf-8'`.

Applications that do not use str to represent paths should use
[`os.fsencode()`](https://docs.python.org/3.6/library/os.html#os.fsencode
"os.fsencode" ) and
[`os.fsdecode()`](https://docs.python.org/3.6/library/os.html#os.fsdecode
"os.fsdecode" ) to ensure their bytes are correctly encoded. To revert to the
previous behaviour, set [`PYTHONLEGACYWINDOWSFSENCODING`](https://docs.python.
org/3.6/using/cmdline.html#envvar-PYTHONLEGACYWINDOWSFSENCODING) or call [`sys
._enablelegacywindowsfsencoding()`](https://docs.python.org/3.6/library/sys.ht
ml#sys._enablelegacywindowsfsencoding "sys._enablelegacywindowsfsencoding" ).

See [**PEP 529**](https://www.python.org/dev/peps/pep-0529) for more
information and discussion of code modifications that may be required.

Note

This change is considered experimental for 3.6.0 beta releases. The default
encoding may change before the final release.

### PEP 487: Simpler customization of class creation

Upon subclassing a class, the `__init_subclass__` classmethod (if defined) is
called on the base class. This makes it straightforward to write classes that
customize initialization of future subclasses without introducing the
complexity of a full custom metaclass.

The descriptor protocol has also been expanded to include a new optional
method, `__set_name__`. Whenever a new class is defined, the new method will
be called on all descriptors included in the definition, providing them with a
reference to the class being defined and the name given to the descriptor
within the class namespace.

Also see [**PEP 487**](https://www.python.org/dev/peps/pep-0487) and the
updated class customization documentation at [Customizing class
creation](https://docs.python.org/3.6/reference/datamodel.html#class-
customization) and [Implementing Descriptors](https://docs.python.org/3.6/refe
rence/datamodel.html#descriptors).

(Contributed by Martin Teichmann in [issue
27366](https://bugs.python.org/issue27366))

### PEP 528: Change Windows console encoding to UTF-8

The default console on Windows will now accept all Unicode characters and
provide correctly read str objects to Python code. `sys.stdin`, `sys.stdout`
and `sys.stderr` now default to utf-8 encoding.

This change only applies when using an interactive console, and not when
redirecting files or pipes. To revert to the previous behaviour for
interactive console use, set [`PYTHONLEGACYWINDOWSIOENCODING`](https://docs.py
thon.org/3.6/using/cmdline.html#envvar-PYTHONLEGACYWINDOWSIOENCODING).

>又见

[**PEP 528**](https://www.python.org/dev/peps/pep-0528) - Change Windows
console encoding to UTF-8

    PEP written and implemented by Steve Dower.

### PYTHONMALLOC environment variable

The new [`PYTHONMALLOC`](https://docs.python.org/3.6/using/cmdline.html
#envvar-PYTHONMALLOC) environment variable allows setting the Python memory
allocators and/or install debug hooks.

It is now possible to install debug hooks on Python memory allocators on
Python compiled in release mode using `PYTHONMALLOC=debug`. Effects of debug
hooks:

  * Newly allocated memory is filled with the byte `0xCB`
  * Freed memory is filled with the byte `0xDB`
  * Detect violations of Python memory allocator API. For example, `PyObject_Free()` called on a memory block allocated by [`PyMem_Malloc()`](https://docs.python.org/3.6/c-api/memory.html#c.PyMem_Malloc "PyMem_Malloc" ).
  * Detect write before the start of the buffer (buffer underflow)
  * Detect write after the end of the buffer (buffer overflow)
  * Check that the [GIL](https://docs.python.org/3.6/glossary.html#term-global-interpreter-lock) is held when allocator functions of [`PYMEM_DOMAIN_OBJ`](https://docs.python.org/3.6/c-api/memory.html#c.PYMEM_DOMAIN_OBJ "PYMEM_DOMAIN_OBJ" ) (ex: `PyObject_Malloc()`) and [`PYMEM_DOMAIN_MEM`](https://docs.python.org/3.6/c-api/memory.html#c.PYMEM_DOMAIN_MEM "PYMEM_DOMAIN_MEM" ) (ex: [`PyMem_Malloc()`](https://docs.python.org/3.6/c-api/memory.html#c.PyMem_Malloc "PyMem_Malloc" )) domains are called.

Checking if the GIL is held is also a new feature of Python 3.6.

See the [`PyMem_SetupDebugHooks()`](https://docs.python.org/3.6/c-api/memory.h
tml#c.PyMem_SetupDebugHooks "PyMem_SetupDebugHooks" ) function for debug hooks
on Python memory allocators.

It is now also possible to force the usage of the `malloc()` allocator of the
C library for all Python memory allocations using `PYTHONMALLOC=malloc`. It
helps to use external memory debuggers like Valgrind on a Python compiled in
release mode.

On error, the debug hooks on Python memory allocators now use the
[`tracemalloc`](https://docs.python.org/3.6/library/tracemalloc.html#module-
tracemalloc "tracemalloc: Trace memory allocations." ) module to get the
traceback where a memory block was allocated.

Example of fatal error on buffer overflow using `python3.6 -X tracemalloc=5`
(store 5 frames in traces):

```python

    Debug memory block at address p=0x7fbcd41666f8: API 'o'
        4 bytes originally requested
        The 7 pad bytes at p-7 are FORBIDDENBYTE, as expected.
        The 8 pad bytes at tail=0x7fbcd41666fc are not all FORBIDDENBYTE (0xfb):
            at tail+0: 0x02 *** OUCH
            at tail+1: 0xfb
            at tail+2: 0xfb
            at tail+3: 0xfb
            at tail+4: 0xfb
            at tail+5: 0xfb
            at tail+6: 0xfb
            at tail+7: 0xfb
        The block was made by call #1233329 to debug malloc/realloc.
        Data at p: 1a 2b 30 00
    
    Memory block allocated at (most recent call first):
      File "test/test_bytes.py", line 323
      File "unittest/case.py", line 600
      File "unittest/case.py", line 648
      File "unittest/suite.py", line 122
      File "unittest/suite.py", line 84
    
    Fatal Python error: bad trailing pad byte
    
    Current thread 0x00007fbcdbd32700 (most recent call first):
      File "test/test_bytes.py", line 323 in test_hex
      File "unittest/case.py", line 600 in run
      File "unittest/case.py", line 648 in __call__
      File "unittest/suite.py", line 122 in run
      File "unittest/suite.py", line 84 in __call__
      File "unittest/suite.py", line 122 in run
      File "unittest/suite.py", line 84 in __call__
      ...
    
```

(Contributed by Victor Stinner in [issue
26516](https://bugs.python.org/issue26516) and [issue
26564](https://bugs.python.org/issue26564).)

### DTrace and SystemTap probing support

Python can now be built `--with-dtrace` which enables static markers for the
following events in the interpreter:

  * function call/return
  * garbage collection started/finished
  * line of code executed.

This can be used to instrument running interpreters in production, without the
need to recompile specific debug builds or providing application-specific
profiling/debugging code.

More details in [Instrumenting CPython with DTrace and SystemTap](https://docs
.python.org/3.6/howto/instrumentation.html#instrumentation).

The current implementation is tested on Linux and macOS. Additional markers
may be added in the future.

(Contributed by Łukasz Langa in [issue
21590](https://bugs.python.org/issue21590), based on patches by Jesús Cea
Avión, David Malcolm, and Nikhil Benesch.)

### PEP 520: Preserving Class Attribute Definition Order

Attributes in a class definition body have a natural ordering: the same order
in which the names appear in the source. This order is now preserved in the
new class's `__dict__` attribute.

Also, the effective default class _execution_ namespace (returned from
`type.__prepare__()`) is now an insertion-order-preserving mapping.

>又见

[**PEP 520**](https://www.python.org/dev/peps/pep-0520) - Preserving Class
Attribute Definition Order

    PEP written and implemented by Eric Snow.

### PEP 468: Preserving Keyword Argument Order

`**kwargs` in a function signature is now guaranteed to be an insertion-order-
preserving mapping.

>又见

[**PEP 468**](https://www.python.org/dev/peps/pep-0468) - Preserving Keyword
Argument Order

    PEP written and implemented by Eric Snow.

### PEP 509: Add a private version to dict

Add a new private version to the builtin `dict` type, incremented at each
dictionary creation and at each dictionary change, to implement fast guards on
namespaces.

(Contributed by Victor Stinner in [issue
26058](https://bugs.python.org/issue26058).)

## Other Language Changes

Some smaller changes made to the core Python language are:

  * [`dict()`](https://docs.python.org/3.6/library/stdtypes.html#dict "dict" ) now uses a "compact" representation [pioneered by PyPy](https://morepypy.blogspot.com/2015/01/faster-more-memory-efficient-and-more.html). The memory usage of the new [`dict()`](https://docs.python.org/3.6/library/stdtypes.html#dict "dict" ) is between 20% and 25% smaller compared to Python 3.5. [**PEP 468**](https://www.python.org/dev/peps/pep-0468) (Preserving the order of `**kwargs` in a function.) is implemented by this. The order-preserving aspect of this new implementation is considered an implementation detail and should not be relied upon (this may change in the future, but it is desired to have this new dict implementation in the language for a few releases before changing the language spec to mandate order-preserving semantics for all current and future Python implementations; this also helps preserve backwards-compatibility with older versions of the language where random iteration order is still in effect, e.g. Python 3.5). (Contributed by INADA Naoki in [issue 27350](https://bugs.python.org/issue27350). Idea [originally suggested by Raymond Hettinger](https://mail.python.org/pipermail/python-dev/2012-December/123028.html).)
  * Long sequences of repeated traceback lines are now abbreviated as `"[Previous line repeated {count} more times]"` (see traceback for an example). (Contributed by Emanuel Barry in [issue 26823](https://bugs.python.org/issue26823).)
  * Import now raises the new exception [`ModuleNotFoundError`](https://docs.python.org/3.6/library/exceptions.html#ModuleNotFoundError "ModuleNotFoundError" ) (subclass of [`ImportError`](https://docs.python.org/3.6/library/exceptions.html#ImportError "ImportError" )) when it cannot find a module. Code that current checks for ImportError (in try-except) will still work.

## New Modules

  * None yet.

## Improved Modules

On Linux,
[`os.urandom()`](https://docs.python.org/3.6/library/os.html#os.urandom
"os.urandom" ) now blocks until the system urandom entropy pool is initialized
to increase the security. See the [**PEP
524**](https://www.python.org/dev/peps/pep-0524) for the rationale.

### asyncio

Since the [`asyncio`](https://docs.python.org/3.6/library/asyncio.html#module-
asyncio "asyncio: Asynchronous I/O, event loop, coroutines and tasks." )
module is [provisional](https://docs.python.org/3.6/glossary.html#term-
provisional-api), all changes introduced in Python 3.6 have also been
backported to Python 3.5.x.

Notable changes in the
[`asyncio`](https://docs.python.org/3.6/library/asyncio.html#module-asyncio
"asyncio: Asynchronous I/O, event loop, coroutines and tasks." ) module since
Python 3.5.0:

  * The [`ensure_future()`](https://docs.python.org/3.6/library/asyncio-task.html#asyncio.ensure_future "asyncio.ensure_future" ) function and all functions that use it, such as `loop.run_until_complete()`, now accept all kinds of [awaitable objects](https://docs.python.org/3.6/glossary.html#term-awaitable). (Contributed by Yury Selivanov.)
  * New [`run_coroutine_threadsafe()`](https://docs.python.org/3.6/library/asyncio-task.html#asyncio.run_coroutine_threadsafe "asyncio.run_coroutine_threadsafe" ) function to submit coroutines to event loops from other threads. (Contributed by Vincent Michel.)
  * New [`Transport.is_closing()`](https://docs.python.org/3.6/library/asyncio-protocol.html#asyncio.BaseTransport.is_closing "asyncio.BaseTransport.is_closing" ) method to check if the transport is closing or closed. (Contributed by Yury Selivanov.)
  * The `loop.create_server()` method can now accept a list of hosts. (Contributed by Yann Sionneau.)
  * New `loop.create_future()` method to create Future objects. This allows alternative event loop implementations, such as [uvloop](https://github.com/MagicStack/uvloop), to provide a faster [`asyncio.Future`](https://docs.python.org/3.6/library/asyncio-task.html#asyncio.Future "asyncio.Future" ) implementation. (Contributed by Yury Selivanov.)
  * New `loop.get_exception_handler()` method to get the current exception handler. (Contributed by Yury Selivanov.)
  * New [`StreamReader.readuntil()`](https://docs.python.org/3.6/library/asyncio-stream.html#asyncio.StreamReader.readuntil "asyncio.StreamReader.readuntil" ) method to read data from the stream until a separator bytes sequence appears. (Contributed by Mark Korenberg.)
  * The `loop.getaddrinfo()` method is optimized to avoid calling the system `getaddrinfo` function if the address is already resolved. (Contributed by A. Jesse Jiryu Davis.)

### contextlib

The [`contextlib.AbstractContextManager`](https://docs.python.org/3.6/library/
contextlib.html#contextlib.AbstractContextManager
"contextlib.AbstractContextManager" ) class has been added to provide an
abstract base class for context managers. It provides a sensible default
implementation for __enter__() which returns `self` and leaves __exit__() an
abstract method. A matching class has been added to the
[`typing`](https://docs.python.org/3.6/library/typing.html#module-typing
"typing: Support for type hints \(see PEP 484\)." ) module as [`typing.Context
Manager`](https://docs.python.org/3.6/library/typing.html#typing.ContextManage
r "typing.ContextManager" ). (Contributed by Brett Cannon in [issue
25609](https://bugs.python.org/issue25609).)

### venv

[`venv`](https://docs.python.org/3.6/library/venv.html#module-venv "venv:
Creation of virtual environments." ) accepts a new parameter `--prompt`. This
parameter provides an alternative prefix for the virtual environment.
(Proposed by Łukasz.Balcerzak and ported to 3.6 by Stéphane Wirtel in [issue
22829](https://bugs.python.org/issue22829).)

### datetime

The [`datetime.strftime()`](https://docs.python.org/3.6/library/datetime.html#
datetime.datetime.strftime "datetime.datetime.strftime" ) and [`date.strftime(
)`](https://docs.python.org/3.6/library/datetime.html#datetime.date.strftime
"datetime.date.strftime" ) methods now support ISO 8601 date directives `%G`,
`%u` and `%V`. (Contributed by Ashley Anderson in [issue
12006](https://bugs.python.org/issue12006).)

### distutils.command.sdist

The `default_format` attribute has been removed from
`distutils.command.sdist.sdist` and the `formats` attribute defaults to
`['gztar']`. Although not anticipated, Any code relying on the presence of
`default_format` may need to be adapted. See [issue
27819](https://bugs.python.org/issue27819) for more details.

### email

The new email API, enabled via the _policy_ keyword to various constructors,
is no longer provisional. The
[`email`](https://docs.python.org/3.6/library/email.html#module-email "email:
Package supporting the parsing, manipulating, and generating email messages."
) documentation has been reorganized and rewritten to focus on the new API,
while retaining the old documentation for the legacy API. (Contributed by R.
David Murray in [issue 24277](https://bugs.python.org/issue24277).)

The [`email.mime`](https://docs.python.org/3.6/library/email.mime.html#module-
email.mime "email.mime: Build MIME messages." ) classes now all accept an
optional _policy_ keyword. (Contributed by Berker Peksag in [issue
27331](https://bugs.python.org/issue27331).)

The [`DecodedGenerator`](https://docs.python.org/3.6/library/email.generator.h
tml#email.generator.DecodedGenerator "email.generator.DecodedGenerator" ) now
supports the _policy_ keyword.

There is a new
[`policy`](https://docs.python.org/3.6/library/email.policy.html#module-
email.policy "email.policy: Controlling the parsing and generating of
messages" ) attribute, [`message_factory`](https://docs.python.org/3.6/library
/email.policy.html#email.policy.Policy.message_factory
"email.policy.Policy.message_factory" ), that controls what class is used by
default when the parser creates new message objects. For the [`email.policy.co
mpat32`](https://docs.python.org/3.6/library/email.policy.html#email.policy.co
mpat32 "email.policy.compat32" ) policy this is [`Message`](https://docs.pytho
n.org/3.6/library/email.compat32-message.html#email.message.Message
"email.message.Message" ), for the new policies it is [`EmailMessage`](https:/
/docs.python.org/3.6/library/email.message.html#email.message.EmailMessage
"email.message.EmailMessage" ). (Contributed by R. David Murray in [issue
20476](https://bugs.python.org/issue20476).)

### encodings

On Windows, added the `'oem'` encoding to use `CP_OEMCP` and the `'ansi'`
alias for the existing `'mbcs'` encoding, which uses the `CP_ACP` code page.

### faulthandler

On Windows, the
[`faulthandler`](https://docs.python.org/3.6/library/faulthandler.html#module-
faulthandler "faulthandler: Dump the Python traceback." ) module now installs
a handler for Windows exceptions: see [`faulthandler.enable()`](https://docs.p
ython.org/3.6/library/faulthandler.html#faulthandler.enable
"faulthandler.enable" ). (Contributed by Victor Stinner in [issue
23848](https://bugs.python.org/issue23848).)

### hashlib

[`hashlib`](https://docs.python.org/3.6/library/hashlib-blake2.html#module-
hashlib "hashlib: BLAKE2 hash function for Python" ) supports OpenSSL 1.1.0.
The minimum recommend version is 1.0.2. It has been tested with 0.9.8zc,
0.9.8zh and 1.0.1t as well as LibreSSL 2.3 and 2.4. (Contributed by Christian
Heimes in [issue 26470](https://bugs.python.org/issue26470).)

BLAKE2 hash functions were added to the module.
[`blake2b()`](https://docs.python.org/3.6/library/hashlib-
blake2.html#hashlib.blake2b "hashlib.blake2b" ) and
[`blake2s()`](https://docs.python.org/3.6/library/hashlib-
blake2.html#hashlib.blake2s "hashlib.blake2s" ) are always available and
support the full feature set of BLAKE2. (Contributed by Christian Heimes in
[issue 26798](https://bugs.python.org/issue26798) based on code by Dmitry
Chestnykh and Samuel Neves. Documentation written by Dmitry Chestnykh.)

The SHA-3 hash functions `sha3_224()`, `sha3_256()`, `sha3_384()`,
`sha3_512()`, and SHAKE hash functions `shake_128()` and `shake_256()` were
added. (Contributed by Christian Heimes in [issue
16113](https://bugs.python.org/issue16113). Keccak Code Package by Guido
Bertoni, Joan Daemen, Michaël Peeters, Gilles Van Assche, and Ronny Van Keer.)

The password-based key derivation function
[`scrypt()`](https://docs.python.org/3.6/library/hashlib.html#hashlib.scrypt
"hashlib.scrypt" ) is now available with OpenSSL 1.1.0 and newer. (Contributed
by Christian Heimes in [issue 27928](https://bugs.python.org/issue27928).)

### http.client

[`HTTPConnection.request()`](https://docs.python.org/3.6/library/http.client.h
tml#http.client.HTTPConnection.request "http.client.HTTPConnection.request" )
and [`endheaders()`](https://docs.python.org/3.6/library/http.client.html#http
.client.HTTPConnection.endheaders "http.client.HTTPConnection.endheaders" )
both now support chunked encoding request bodies. (Contributed by Demian
Brecht and Rolf Krahl in [issue 12319](https://bugs.python.org/issue12319).)

### idlelib and IDLE

The idlelib package is being modernized and refactored to make IDLE look and
work better and to make the code easier to understand, test, and improve. Part
of making IDLE look better, especially on Linux and Mac, is using ttk widgets,
mostly in the dialogs. As a result, IDLE no longer runs with tcl/tk 8.4. It
now requires tcl/tk 8.5 or 8.6. We recommend running the latest release of
either.

'Modernizing' includes renaming and consolidation of idlelib modules. The
renaming of files with partial uppercase names is similar to the renaming of,
for instance, Tkinter and TkFont to tkinter and tkinter.font in 3.0. As a
result, imports of idlelib files that worked in 3.5 will usually not work in
3.6. At least a module name change will be needed (see idlelib/README.txt),
sometimes more. (Name changes contributed by Al Swiegart and Terry Reedy in
[issue 24225](https://bugs.python.org/issue24225). Most idlelib patches since
have been and will be part of the process.)

In compensation, the eventual result with be that some idlelib classes will be
easier to use, with better APIs and docstrings explaining them. Additional
useful information will be added to idlelib when available.

### importlib

[`importlib.util.LazyLoader`](https://docs.python.org/3.6/library/importlib.ht
ml#importlib.util.LazyLoader "importlib.util.LazyLoader" ) now calls [`create_
module()`](https://docs.python.org/3.6/library/importlib.html#importlib.abc.Lo
ader.create_module "importlib.abc.Loader.create_module" ) on the wrapped
loader, removing the restriction that [`importlib.machinery.BuiltinImporter`](
https://docs.python.org/3.6/library/importlib.html#importlib.machinery.Builtin
Importer "importlib.machinery.BuiltinImporter" ) and [`importlib.machinery.Ext
ensionFileLoader`](https://docs.python.org/3.6/library/importlib.html#importli
b.machinery.ExtensionFileLoader "importlib.machinery.ExtensionFileLoader" )
couldn't be used with [`importlib.util.LazyLoader`](https://docs.python.org/3.
6/library/importlib.html#importlib.util.LazyLoader "importlib.util.LazyLoader"
).

[`importlib.util.cache_from_source()`](https://docs.python.org/3.6/library/imp
ortlib.html#importlib.util.cache_from_source
"importlib.util.cache_from_source" ), [`importlib.util.source_from_cache()`](h
ttps://docs.python.org/3.6/library/importlib.html#importlib.util.source_from_c
ache "importlib.util.source_from_cache" ), and [`importlib.util.spec_from_file
_location()`](https://docs.python.org/3.6/library/importlib.html#importlib.uti
l.spec_from_file_location "importlib.util.spec_from_file_location" ) now
accept a [path-like object](https://docs.python.org/3.6/glossary.html#term-
path-like-object).

### json

[`json.load()`](https://docs.python.org/3.6/library/json.html#json.load
"json.load" ) and
[`json.loads()`](https://docs.python.org/3.6/library/json.html#json.loads
"json.loads" ) now support binary input. Encoded JSON should be represented
using either UTF-8, UTF-16, or UTF-32. (Contributed by Serhiy Storchaka in
[issue 17909](https://bugs.python.org/issue17909).)

### os

A new [`close()`](https://docs.python.org/3.6/library/os.html#os.scandir.close
"os.scandir.close" ) method allows explicitly closing a
[`scandir()`](https://docs.python.org/3.6/library/os.html#os.scandir
"os.scandir" ) iterator. The
[`scandir()`](https://docs.python.org/3.6/library/os.html#os.scandir
"os.scandir" ) iterator now supports the [context
manager](https://docs.python.org/3.6/glossary.html#term-context-manager)
protocol. If a `scandir()` iterator is neither exhausted nor explicitly closed
a [`ResourceWarning`](https://docs.python.org/3.6/library/exceptions.html#Reso
urceWarning "ResourceWarning" ) will be emitted in its destructor.
(Contributed by Serhiy Storchaka in [issue
25994](https://bugs.python.org/issue25994).)

The Linux `getrandom()` syscall (get random bytes) is now exposed as the new
[`os.getrandom()`](https://docs.python.org/3.6/library/os.html#os.getrandom
"os.getrandom" ) function. (Contributed by Victor Stinner, part of the [**PEP
524**](https://www.python.org/dev/peps/pep-0524))

See the summary for PEP 519 for details on how the
[`os`](https://docs.python.org/3.6/library/os.html#module-os "os:
Miscellaneous operating system interfaces." ) and
[`os.path`](https://docs.python.org/3.6/library/os.path.html#module-os.path
"os.path: Operations on pathnames." ) modules now support [path-like
objects](https://docs.python.org/3.6/glossary.html#term-path-like-object).

### pickle

Objects that need calling `__new__` with keyword arguments can now be pickled
using [pickle protocols](https://docs.python.org/3.6/library/pickle.html
#pickle-protocols) older than protocol version 4. Protocol version 4 already
supports this case. (Contributed by Serhiy Storchaka in [issue
24164](https://bugs.python.org/issue24164).)

### re

Added support of modifier spans in regular expressions. Examples:
`'(?i:p)ython'` matches `'python'` and `'Python'`, but not `'PYTHON'`;
`'(?i)g(?-i:v)r'` matches `'GvR'` and `'gvr'`, but not `'GVR'`. (Contributed
by Serhiy Storchaka in [issue 433028](https://bugs.python.org/issue433028).)

Match object groups can be accessed by `__getitem__`, which is equivalent to
`group()`. So `mo['name']` is now equivalent to `mo.group('name')`.
(Contributed by Eric Smith in [issue
24454](https://bugs.python.org/issue24454).)

### readline

Added [`set_auto_history()`](https://docs.python.org/3.6/library/readline.html
#readline.set_auto_history "readline.set_auto_history" ) to enable or disable
automatic addition of input to the history list. (Contributed by Tyler
Crompton in [issue 26870](https://bugs.python.org/issue26870).)

### rlcompleter

Private and special attribute names now are omitted unless the prefix starts
with underscores. A space or a colon is added after some completed keywords.
(Contributed by Serhiy Storchaka in [issue
25011](https://bugs.python.org/issue25011) and [issue
25209](https://bugs.python.org/issue25209).)

Names of most attributes listed by
[`dir()`](https://docs.python.org/3.6/library/functions.html#dir "dir" ) are
now completed. Previously, names of properties and slots which were not yet
created on an instance were excluded. (Contributed by Martin Panter in [issue
25590](https://bugs.python.org/issue25590).)

### site

When specifying paths to add to
[`sys.path`](https://docs.python.org/3.6/library/sys.html#sys.path "sys.path"
) in a .pth file, you may now specify file paths on top of directories (e.g.
zip files). (Contributed by Wolfgang Langner in [issue
26587](https://bugs.python.org/issue26587)).

### sqlite3

[`sqlite3.Cursor.lastrowid`](https://docs.python.org/3.6/library/sqlite3.html#
sqlite3.Cursor.lastrowid "sqlite3.Cursor.lastrowid" ) now supports the
`REPLACE` statement. (Contributed by Alex LordThorsen in [issue
16864](https://bugs.python.org/issue16864).)

### socket

The [`ioctl()`](https://docs.python.org/3.6/library/socket.html#socket.socket.
ioctl "socket.socket.ioctl" ) function now supports the [`SIO_LOOPBACK_FAST_PA
TH`](https://docs.python.org/3.6/library/socket.html#socket.SIO_LOOPBACK_FAST_
PATH "socket.SIO_LOOPBACK_FAST_PATH" ) control code. (Contributed by Daniel
Stokes in [issue 26536](https://bugs.python.org/issue26536).)

The [`getsockopt()`](https://docs.python.org/3.6/library/socket.html#socket.so
cket.getsockopt "socket.socket.getsockopt" ) constants `SO_DOMAIN`,
`SO_PROTOCOL`, `SO_PEERSEC`, and `SO_PASSSEC` are now supported. (Contributed
by Christian Heimes in [issue 26907](https://bugs.python.org/issue26907).)

The socket module now supports the address family
[`AF_ALG`](https://docs.python.org/3.6/library/socket.html#socket.AF_ALG
"socket.AF_ALG" ) to interface with Linux Kernel crypto API. `ALG_*`,
`SOL_ALG` and [`sendmsg_afalg()`](https://docs.python.org/3.6/library/socket.h
tml#socket.socket.sendmsg_afalg "socket.socket.sendmsg_afalg" ) were added.
(Contributed by Christian Heimes in [issue
27744](https://bugs.python.org/issue27744) with support from Victor Stinner.)

### socketserver

Servers based on the
[`socketserver`](https://docs.python.org/3.6/library/socketserver.html#module-
socketserver "socketserver: A framework for network servers." ) module,
including those defined in
[`http.server`](https://docs.python.org/3.6/library/http.server.html#module-
http.server "http.server: HTTP server and request handlers." ),
[`xmlrpc.server`](https://docs.python.org/3.6/library/xmlrpc.server.html
#module-xmlrpc.server "xmlrpc.server: Basic XML-RPC server implementations." )
and [`wsgiref.simple_server`](https://docs.python.org/3.6/library/wsgiref.html
#module-wsgiref.simple_server "wsgiref.simple_server: A simple WSGI HTTP
server." ), now support the [context
manager](https://docs.python.org/3.6/glossary.html#term-context-manager)
protocol. (Contributed by Aviv Palivoda in [issue
26404](https://bugs.python.org/issue26404).)

The `wfile` attribute of [`StreamRequestHandler`](https://docs.python.org/3.6/
library/socketserver.html#socketserver.StreamRequestHandler
"socketserver.StreamRequestHandler" ) classes now implements the [`io.Buffered
IOBase`](https://docs.python.org/3.6/library/io.html#io.BufferedIOBase
"io.BufferedIOBase" ) writable interface. In particular, calling [`write()`](h
ttps://docs.python.org/3.6/library/io.html#io.BufferedIOBase.write
"io.BufferedIOBase.write" ) is now guaranteed to send the data in full.
(Contributed by Martin Panter in [issue
26721](https://bugs.python.org/issue26721).)

### ssl

[`ssl`](https://docs.python.org/3.6/library/ssl.html#module-ssl "ssl: TLS/SSL
wrapper for socket objects" ) supports OpenSSL 1.1.0. The minimum recommend
version is 1.0.2. It has been tested with 0.9.8zc, 0.9.8zh and 1.0.1t as well
as LibreSSL 2.3 and 2.4. (Contributed by Christian Heimes in [issue
26470](https://bugs.python.org/issue26470).)

3DES has been removed from the default cipher suites and ChaCha20 Poly1305
cipher suites are now in the right position. (Contributed by Christian Heimes
in [issue 27850](https://bugs.python.org/issue27850) and [issue
27766](https://bugs.python.org/issue27766).)

[`SSLContext`](https://docs.python.org/3.6/library/ssl.html#ssl.SSLContext
"ssl.SSLContext" ) has better default configuration for options and ciphers.
(Contributed by Christian Heimes in [issue
28043](https://bugs.python.org/issue28043).)

SSL session can be copied from one client-side connection to another with
[`SSLSession`](https://docs.python.org/3.6/library/ssl.html#ssl.SSLSession
"ssl.SSLSession" ). TLS session resumption can speed up the initial handshake,
reduce latency and improve performance (Contributed by Christian Heimes in
[issue 19500](https://bugs.python.org/issue19500) based on a draft by Alex
Warhawk.)

All constants and flags have been converted to
[`IntEnum`](https://docs.python.org/3.6/library/enum.html#enum.IntEnum
"enum.IntEnum" ) and `IntFlags`. (Contributed by Christian Heimes in [issue
28025](https://bugs.python.org/issue28025).)

Server and client-side specific TLS protocols for
[`SSLContext`](https://docs.python.org/3.6/library/ssl.html#ssl.SSLContext
"ssl.SSLContext" ) were added. (Contributed by Christian Heimes in [issue
28085](https://bugs.python.org/issue28085).)

General resource ids (`GEN_RID`) in subject alternative name extensions no
longer case a SystemError. (Contributed by Christian Heimes in [issue
27691](https://bugs.python.org/issue27691).)

### subprocess

[`subprocess.Popen`](https://docs.python.org/3.6/library/subprocess.html#subpr
ocess.Popen "subprocess.Popen" ) destructor now emits a [`ResourceWarning`](ht
tps://docs.python.org/3.6/library/exceptions.html#ResourceWarning
"ResourceWarning" ) warning if the child process is still running. Use the
context manager protocol (`with proc: ...`) or call explicitly the [`wait()`](
https://docs.python.org/3.6/library/subprocess.html#subprocess.Popen.wait
"subprocess.Popen.wait" ) method to read the exit status of the child process
(Contributed by Victor Stinner in [issue
26741](https://bugs.python.org/issue26741)).

The [`subprocess.Popen`](https://docs.python.org/3.6/library/subprocess.html#s
ubprocess.Popen "subprocess.Popen" ) constructor and all functions that pass
arguments through to it now accept _encoding_ and _errors_ arguments.
Specifying either of these will enable text mode for the _stdin_, _stdout_ and
_stderr_ streams.

### telnetlib

[`Telnet`](https://docs.python.org/3.6/library/telnetlib.html#telnetlib.Telnet
"telnetlib.Telnet" ) is now a context manager (contributed by Stéphane Wirtel
in [issue 25485](https://bugs.python.org/issue25485)).

### tkinter

Added methods `trace_add()`, `trace_remove()` and `trace_info()` in the
`tkinter.Variable` class. They replace old methods `trace_variable()`,
`trace()`, `trace_vdelete()` and `trace_vinfo()` that use obsolete Tcl
commands and might not work in future versions of Tcl. (Contributed by Serhiy
Storchaka in [issue 22115](https://bugs.python.org/issue22115)).

### traceback

Both the traceback module and the interpreter's builtin exception display now
abbreviate long sequences of repeated lines in tracebacks as shown in the
following example:

```python

    >>> def f(): f()
    ...
    >>> f()
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
      File "<stdin>", line 1, in f
      File "<stdin>", line 1, in f
      File "<stdin>", line 1, in f
      [Previous line repeated 995 more times]
    RecursionError: maximum recursion depth exceeded
    
```

(Contributed by Emanuel Barry in [issue
26823](https://bugs.python.org/issue26823).)

### typing

The [`typing.ContextManager`](https://docs.python.org/3.6/library/typing.html#
typing.ContextManager "typing.ContextManager" ) class has been added for
representing [`contextlib.AbstractContextManager`](https://docs.python.org/3.6
/library/contextlib.html#contextlib.AbstractContextManager
"contextlib.AbstractContextManager" ). (Contributed by Brett Cannon in [issue
25609](https://bugs.python.org/issue25609).)

### unicodedata

The internal database has been upgraded to use Unicode 9.0.0. (Contributed by
Benjamin Peterson.)

### unittest.mock

The [`Mock`](https://docs.python.org/3.6/library/unittest.mock.html#unittest.m
ock.Mock "unittest.mock.Mock" ) class has the following improvements:

  * Two new methods, [`Mock.assert_called()`](https://docs.python.org/3.6/library/unittest.mock.html#unittest.mock.Mock.assert_called "unittest.mock.Mock.assert_called" ) and [`Mock.assert_called_once()`](https://docs.python.org/3.6/library/unittest.mock.html#unittest.mock.Mock.assert_called_once "unittest.mock.Mock.assert_called_once" ) to check if the mock object was called. (Contributed by Amit Saha in [issue 26323](https://bugs.python.org/issue26323).)

### urllib.request

If a HTTP request has a file or iterable body (other than a bytes object) but
no Content-Length header, rather than throwing an error, `AbstractHTTPHandler`
now falls back to use chunked transfer encoding. (Contributed by Demian Brecht
and Rolf Krahl in [issue 12319](https://bugs.python.org/issue12319).)

### urllib.robotparser

[`RobotFileParser`](https://docs.python.org/3.6/library/urllib.robotparser.htm
l#urllib.robotparser.RobotFileParser "urllib.robotparser.RobotFileParser" )
now supports the `Crawl-delay` and `Request-rate` extensions. (Contributed by
Nikolay Bogoychev in [issue 16099](https://bugs.python.org/issue16099).)

### warnings

A new optional _source_ parameter has been added to the [`warnings.warn_explic
it()`](https://docs.python.org/3.6/library/warnings.html#warnings.warn_explici
t "warnings.warn_explicit" ) function: the destroyed object which emitted a [`
ResourceWarning`](https://docs.python.org/3.6/library/exceptions.html#Resource
Warning "ResourceWarning" ). A _source_ attribute has also been added to
`warnings.WarningMessage` (contributed by Victor Stinner in [issue
26568](https://bugs.python.org/issue26568) and [issue
26567](https://bugs.python.org/issue26567)).

When a [`ResourceWarning`](https://docs.python.org/3.6/library/exceptions.html
#ResourceWarning "ResourceWarning" ) warning is logged, the
[`tracemalloc`](https://docs.python.org/3.6/library/tracemalloc.html#module-
tracemalloc "tracemalloc: Trace memory allocations." ) is now used to try to
retrieve the traceback where the detroyed object was allocated.

Example with the script `example.py`:

```python

    import warnings
    
    def func():
        return open(__file__)
    
    f = func()
    f = None
    
```

Output of the command `python3.6 -Wd -X tracemalloc=5 example.py`:

```python

    example.py:7: ResourceWarning: unclosed file <_io.TextIOWrapper name='example.py' mode='r' encoding='UTF-8'>
      f = None
    Object allocated at (most recent call first):
      File "example.py", lineno 4
        return open(__file__)
      File "example.py", lineno 6
        f = func()
    
```

The "Object allocated at" traceback is new and only displayed if
[`tracemalloc`](https://docs.python.org/3.6/library/tracemalloc.html#module-
tracemalloc "tracemalloc: Trace memory allocations." ) is tracing Python
memory allocations and if the
[`warnings`](https://docs.python.org/3.6/library/warnings.html#module-warnings
"warnings: Issue warning messages and control their disposition." ) was
already imported.

### winreg

Added the 64-bit integer type
[`REG_QWORD`](https://docs.python.org/3.6/library/winreg.html#winreg.REG_QWORD
"winreg.REG_QWORD" ). (Contributed by Clement Rouault in [issue
23026](https://bugs.python.org/issue23026).)

### winsound

Allowed keyword arguments to be passed to
[`Beep`](https://docs.python.org/3.6/library/winsound.html#winsound.Beep
"winsound.Beep" ), [`MessageBeep`](https://docs.python.org/3.6/library/winsoun
d.html#winsound.MessageBeep "winsound.MessageBeep" ), and [`PlaySound`](https:
//docs.python.org/3.6/library/winsound.html#winsound.PlaySound
"winsound.PlaySound" ) ([issue 27982](https://bugs.python.org/issue27982)).

### xmlrpc.client

The module now supports unmarshalling additional data types used by Apache
XML-RPC implementation for numerics and `None`. (Contributed by Serhiy
Storchaka in [issue 26885](https://bugs.python.org/issue26885).)

### zipfile

A new [`ZipInfo.from_file()`](https://docs.python.org/3.6/library/zipfile.html
#zipfile.ZipInfo.from_file "zipfile.ZipInfo.from_file" ) class method allows
making a
[`ZipInfo`](https://docs.python.org/3.6/library/zipfile.html#zipfile.ZipInfo
"zipfile.ZipInfo" ) instance from a filesystem file. A new [`ZipInfo.is_dir()`
](https://docs.python.org/3.6/library/zipfile.html#zipfile.ZipInfo.is_dir
"zipfile.ZipInfo.is_dir" ) method can be used to check if the
[`ZipInfo`](https://docs.python.org/3.6/library/zipfile.html#zipfile.ZipInfo
"zipfile.ZipInfo" ) instance represents a directory. (Contributed by Thomas
Kluyver in [issue 26039](https://bugs.python.org/issue26039).)

The [`ZipFile.open()`](https://docs.python.org/3.6/library/zipfile.html#zipfil
e.ZipFile.open "zipfile.ZipFile.open" ) method can now be used to write data
into a ZIP file, as well as for extracting data. (Contributed by Thomas
Kluyver in [issue 26039](https://bugs.python.org/issue26039).)

### zlib

The [`compress()`](https://docs.python.org/3.6/library/zlib.html#zlib.compress
"zlib.compress" ) function now accepts keyword arguments. (Contributed by Aviv
Palivoda in [issue 26243](https://bugs.python.org/issue26243).)

### fileinput

[`hook_encoded()`](https://docs.python.org/3.6/library/fileinput.html#fileinpu
t.hook_encoded "fileinput.hook_encoded" ) now supports the _errors_ argument.
(Contributed by Joseph Hackman in [issue
25788](https://bugs.python.org/issue25788).)

## Optimizations

  * The ASCII decoder is now up to 60 times as fast for error handlers `surrogateescape`, `ignore` and `replace` (Contributed by Victor Stinner in [issue 24870](https://bugs.python.org/issue24870)).
  * The ASCII and the Latin1 encoders are now up to 3 times as fast for the error handler `surrogateescape` (Contributed by Victor Stinner in [issue 25227](https://bugs.python.org/issue25227)).
  * The UTF-8 encoder is now up to 75 times as fast for error handlers `ignore`, `replace`, `surrogateescape`, `surrogatepass` (Contributed by Victor Stinner in [issue 25267](https://bugs.python.org/issue25267)).
  * The UTF-8 decoder is now up to 15 times as fast for error handlers `ignore`, `replace` and `surrogateescape` (Contributed by Victor Stinner in [issue 25301](https://bugs.python.org/issue25301)).
  * `bytes % args` is now up to 2 times faster. (Contributed by Victor Stinner in [issue 25349](https://bugs.python.org/issue25349)).
  * `bytearray % args` is now between 2.5 and 5 times faster. (Contributed by Victor Stinner in [issue 25399](https://bugs.python.org/issue25399)).
  * Optimize [`bytes.fromhex()`](https://docs.python.org/3.6/library/stdtypes.html#bytes.fromhex "bytes.fromhex" ) and [`bytearray.fromhex()`](https://docs.python.org/3.6/library/stdtypes.html#bytearray.fromhex "bytearray.fromhex" ): they are now between 2x and 3.5x faster. (Contributed by Victor Stinner in [issue 25401](https://bugs.python.org/issue25401)).
  * Optimize `bytes.replace(b'', b'.')` and `bytearray.replace(b'', b'.')`: up to 80% faster. (Contributed by Josh Snider in [issue 26574](https://bugs.python.org/issue26574)).
  * Allocator functions of the [`PyMem_Malloc()`](https://docs.python.org/3.6/c-api/memory.html#c.PyMem_Malloc "PyMem_Malloc" ) domain ([`PYMEM_DOMAIN_MEM`](https://docs.python.org/3.6/c-api/memory.html#c.PYMEM_DOMAIN_MEM "PYMEM_DOMAIN_MEM" )) now use the [pymalloc memory allocator](https://docs.python.org/3.6/c-api/memory.html#pymalloc) instead of `malloc()` function of the C library. The pymalloc allocator is optimized for objects smaller or equal to 512 bytes with a short lifetime, and use `malloc()` for larger memory blocks. (Contributed by Victor Stinner in [issue 26249](https://bugs.python.org/issue26249)).
  * [`pickle.load()`](https://docs.python.org/3.6/library/pickle.html#pickle.load "pickle.load" ) and [`pickle.loads()`](https://docs.python.org/3.6/library/pickle.html#pickle.loads "pickle.loads" ) are now up to 10% faster when deserializing many small objects (Contributed by Victor Stinner in [issue 27056](https://bugs.python.org/issue27056)).

  * Passing [keyword arguments](https://docs.python.org/3.6/glossary.html#term-keyword-argument) to a function has an overhead in comparison with passing [positional arguments](https://docs.python.org/3.6/glossary.html#term-positional-argument). Now in extension functions implemented with using Argument Clinic this overhead is significantly decreased. (Contributed by Serhiy Storchaka in [issue 27574](https://bugs.python.org/issue27574)).

  * Optimized [`glob()`](https://docs.python.org/3.6/library/glob.html#glob.glob "glob.glob" ) and [`iglob()`](https://docs.python.org/3.6/library/glob.html#glob.iglob "glob.iglob" ) functions in the [`glob`](https://docs.python.org/3.6/library/glob.html#module-glob "glob: Unix shell style pathname pattern expansion." ) module; they are now about 3-6 times faster. (Contributed by Serhiy Storchaka in [issue 25596](https://bugs.python.org/issue25596)).
  * Optimized globbing in [`pathlib`](https://docs.python.org/3.6/library/pathlib.html#module-pathlib "pathlib: Object-oriented filesystem paths" ) by using [`os.scandir()`](https://docs.python.org/3.6/library/os.html#os.scandir "os.scandir" ); it is now about 1.5-4 times faster. (Contributed by Serhiy Storchaka in [issue 26032](https://bugs.python.org/issue26032)).

## Build and C API Changes

  * Python now requires some C99 support in the toolchain to build. For more information, see [**PEP 7**](https://www.python.org/dev/peps/pep-0007).
  * Cross-compiling CPython with the Android NDK and the Android API level set to 21 (Android 5.0 Lollilop) or greater, runs successfully. While Android is not yet a supported platform, the Python test suite runs on the Android emulator with only about 16 tests failures. See the Android meta-issue [issue 26865](https://bugs.python.org/issue26865).
  * The `--with-optimizations` configure flag has been added. Turning it on will activate LTO and PGO build support (when available). (Original patch by Alecsandru Patrascu of Intel in [issue 26539](https://bugs.python.org/issue26539).)
  * New [`Py_FinalizeEx()`](https://docs.python.org/3.6/c-api/init.html#c.Py_FinalizeEx "Py_FinalizeEx" ) API which indicates if flushing buffered data failed ([issue 5319](https://bugs.python.org/issue5319)).
  * [`PyArg_ParseTupleAndKeywords()`](https://docs.python.org/3.6/c-api/arg.html#c.PyArg_ParseTupleAndKeywords "PyArg_ParseTupleAndKeywords" ) now supports [positional-only parameters](https://docs.python.org/3.6/glossary.html#positional-only-parameter). Positional-only parameters are defined by empty names. (Contributed by Serhiy Storchaka in [issue 26282](https://bugs.python.org/issue26282)).
  * `PyTraceback_Print` method now abbreviates long sequences of repeated lines as `"[Previous line repeated {count} more times]"`. (Contributed by Emanuel Barry in [issue 26823](https://bugs.python.org/issue26823).)

## Deprecated

### Deprecated Build Options

The `--with-system-ffi` configure flag is now on by default on non-OSX UNIX
platforms. It may be disabled by using `--without-system-ffi`, but using the
flag is deprecated and will not be accepted in Python 3.7. OSX is unaffected
by this change. Note that many OS distributors already use the `--with-system-
ffi` flag when building their system Python.

### New Keywords

`async` and `await` are not recommended to be used as variable, class,
function or module names. Introduced by [**PEP
492**](https://www.python.org/dev/peps/pep-0492) in Python 3.5, they will
become proper keywords in Python 3.7.

### Deprecated Python modules, functions and methods

  * [`importlib.machinery.SourceFileLoader.load_module()`](https://docs.python.org/3.6/library/importlib.html#importlib.machinery.SourceFileLoader.load_module "importlib.machinery.SourceFileLoader.load_module" ) and [`importlib.machinery.SourcelessFileLoader.load_module()`](https://docs.python.org/3.6/library/importlib.html#importlib.machinery.SourcelessFileLoader.load_module "importlib.machinery.SourcelessFileLoader.load_module" ) are now deprecated. They were the only remaining implementations of [`importlib.abc.Loader.load_module()`](https://docs.python.org/3.6/library/importlib.html#importlib.abc.Loader.load_module "importlib.abc.Loader.load_module" ) in [`importlib`](https://docs.python.org/3.6/library/importlib.html#module-importlib "importlib: The implementation of the import machinery." ) that had not been deprecated in previous versions of Python in favour of [`importlib.abc.Loader.exec_module()`](https://docs.python.org/3.6/library/importlib.html#importlib.abc.Loader.exec_module "importlib.abc.Loader.exec_module" ).
  * The [`tkinter.tix`](https://docs.python.org/3.6/library/tkinter.tix.html#module-tkinter.tix "tkinter.tix: Tk Extension Widgets for Tkinter" ) module is now deprecated. [`tkinter`](https://docs.python.org/3.6/library/tkinter.html#module-tkinter "tkinter: Interface to Tcl/Tk for graphical user interfaces" ) users should use [`tkinter.ttk`](https://docs.python.org/3.6/library/tkinter.ttk.html#module-tkinter.ttk "tkinter.ttk: Tk themed widget set" ) instead.

### Deprecated functions and types of the C API

  * None yet.

### Deprecated features

  * The `pyvenv` script has been deprecated in favour of `python3 -m venv`. This prevents confusion as to what Python interpreter `pyvenv` is connected to and thus what Python interpreter will be used by the virtual environment. (Contributed by Brett Cannon in [issue 25154](https://bugs.python.org/issue25154).)
  * When performing a relative import, falling back on `__name__` and `__path__` from the calling module when `__spec__` or `__package__` are not defined now raises an [`ImportWarning`](https://docs.python.org/3.6/library/exceptions.html#ImportWarning "ImportWarning" ). (Contributed by Rose Ames in [issue 25791](https://bugs.python.org/issue25791).)
  * Unlike to other [`dbm`](https://docs.python.org/3.6/library/dbm.html#module-dbm "dbm: Interfaces to various Unix "database" formats." ) implementations, the [`dbm.dumb`](https://docs.python.org/3.6/library/dbm.html#module-dbm.dumb "dbm.dumb: Portable implementation of the simple DBM interface." ) module creates database in `'r'` and `'w'` modes if it doesn't exist and allows modifying database in `'r'` mode. This behavior is now deprecated and will be removed in 3.8. (Contributed by Serhiy Storchaka in [issue 21708](https://bugs.python.org/issue21708).)
  * Undocumented support of general [bytes-like objects](https://docs.python.org/3.6/glossary.html#term-bytes-like-object) as paths in [`os`](https://docs.python.org/3.6/library/os.html#module-os "os: Miscellaneous operating system interfaces." ) functions, [`compile()`](https://docs.python.org/3.6/library/functions.html#compile "compile" ) and similar functions is now deprecated. (Contributed by Serhiy Storchaka in [issue 25791](https://bugs.python.org/issue25791) and [issue 26754](https://bugs.python.org/issue26754).)
  * The undocumented `extra_path` argument to a distutils Distribution is now considered deprecated, will raise a warning during install if set. Support for this parameter will be dropped in a future Python release and likely earlier through third party tools. See [issue 27919](https://bugs.python.org/issue27919) for details.
  * A backslash-character pair that is not a valid escape sequence now generates a DeprecationWarning. Although this will eventually become a SyntaxError, that will not be for several Python releases. (Contributed by Emanuel Barry in [issue 27364](https://bugs.python.org/issue27364).)
  * Inline flags `(?letters)` now should be used only at the start of the regular expression. Inline flags in the middle of the regular expression affects global flags in Python [`re`](https://docs.python.org/3.6/library/re.html#module-re "re: Regular expression operations." ) module. This is an exception to other regular expression engines that either apply flags to only part of the regular expression or treat them as an error. To avoid distinguishing inline flags in the middle of the regular expression now emit a deprecation warning. It will be an error in future Python releases. (Contributed by Serhiy Storchaka in [issue 22493](https://bugs.python.org/issue22493).)
  * SSL-related arguments like `certfile`, `keyfile` and `check_hostname` in [`ftplib`](https://docs.python.org/3.6/library/ftplib.html#module-ftplib "ftplib: FTP protocol client \(requires sockets\)." ), [`http.client`](https://docs.python.org/3.6/library/http.client.html#module-http.client "http.client: HTTP and HTTPS protocol client \(requires sockets\)." ), [`imaplib`](https://docs.python.org/3.6/library/imaplib.html#module-imaplib "imaplib: IMAP4 protocol client \(requires sockets\)." ), [`poplib`](https://docs.python.org/3.6/library/poplib.html#module-poplib "poplib: POP3 protocol client \(requires sockets\)." ), and [`smtplib`](https://docs.python.org/3.6/library/smtplib.html#module-smtplib "smtplib: SMTP protocol client \(requires sockets\)." ) have been deprecated in favor of `context`. (Contributed by Christian Heimes in [issue 28022](https://bugs.python.org/issue28022).)
  * A couple of protocols and functions of the [`ssl`](https://docs.python.org/3.6/library/ssl.html#module-ssl "ssl: TLS/SSL wrapper for socket objects" ) module are now deprecated. Some features will no longer be available in future versions of OpenSSL. Other features are deprecated in favor of a different API. (Contributed by Christian Heimes in [issue 28022](https://bugs.python.org/issue28022) and [issue 26470](https://bugs.python.org/issue26470).)

### Deprecated Python behavior

  * Raising the [`StopIteration`](https://docs.python.org/3.6/library/exceptions.html#StopIteration "StopIteration" ) exception inside a generator will now generate a [`DeprecationWarning`](https://docs.python.org/3.6/library/exceptions.html#DeprecationWarning "DeprecationWarning" ), and will trigger a [`RuntimeError`](https://docs.python.org/3.6/library/exceptions.html#RuntimeError "RuntimeError" ) in Python 3.7. See [PEP 479: Change StopIteration handling inside generators](https://docs.python.org/3.6/whatsnew/3.5.html#whatsnew-pep-479) for details.

## Removed

### API and Feature Removals

  * `inspect.getmoduleinfo()` was removed (was deprecated since CPython 3.3). [`inspect.getmodulename()`](https://docs.python.org/3.6/library/inspect.html#inspect.getmodulename "inspect.getmodulename" ) should be used for obtaining the module name for a given path.
  * `traceback.Ignore` class and `traceback.usage`, `traceback.modname`, `traceback.fullmodname`, `traceback.find_lines_from_code`, `traceback.find_lines`, `traceback.find_strings`, `traceback.find_executable_lines` methods were removed from the [`traceback`](https://docs.python.org/3.6/library/traceback.html#module-traceback "traceback: Print or retrieve a stack traceback." ) module. They were undocumented methods deprecated since Python 3.2 and equivalent functionality is available from private methods.
  * The `tk_menuBar()` and `tk_bindForTraversal()` dummy methods in [`tkinter`](https://docs.python.org/3.6/library/tkinter.html#module-tkinter "tkinter: Interface to Tcl/Tk for graphical user interfaces" ) widget classes were removed (corresponding Tk commands were obsolete since Tk 4.0).
  * The [`open()`](https://docs.python.org/3.6/library/zipfile.html#zipfile.ZipFile.open "zipfile.ZipFile.open" ) method of the [`zipfile.ZipFile`](https://docs.python.org/3.6/library/zipfile.html#zipfile.ZipFile "zipfile.ZipFile" ) class no longer supports the `'U'` mode (was deprecated since Python 3.4). Use [`io.TextIOWrapper`](https://docs.python.org/3.6/library/io.html#io.TextIOWrapper "io.TextIOWrapper" ) for reading compressed text files in [universal newlines](https://docs.python.org/3.6/glossary.html#term-universal-newlines) mode.
  * The undocumented `IN`, `CDROM`, `DLFCN`, `TYPES`, `CDIO`, and `STROPTS` modules have been removed. They had been available in the platform specific `Lib/plat-*/` directories, but were chronically out of date, inconsistently available across platforms, and unmaintained. The script that created these modules is still available in the source distribution at [Tools/scripts/h2py.py](https://hg.python.org/cpython/file/3.6/Tools/scripts/h2py.py).

## Porting to Python 3.6

This section lists previously described changes and other bugfixes that may
require changes to your code.

### Changes in 'python' Command Behavior

  * The output of a special Python build with defined `COUNT_ALLOCS`, `SHOW_ALLOC_COUNT` or `SHOW_TRACK_COUNT` macros is now off by default. It can be re-enabled using the `-X showalloccount` option. It now outputs to `stderr` instead of `stdout`. (Contributed by Serhiy Storchaka in [issue 23034](https://bugs.python.org/issue23034).)

### Changes in the Python API

  * [`sqlite3`](https://docs.python.org/3.6/library/sqlite3.html#module-sqlite3 "sqlite3: A DB-API 2.0 implementation using SQLite 3.x." ) no longer implicitly commit an open transaction before DDL statements.

  * On Linux, [`os.urandom()`](https://docs.python.org/3.6/library/os.html#os.urandom "os.urandom" ) now blocks until the system urandom entropy pool is initialized to increase the security.

  * When [`importlib.abc.Loader.exec_module()`](https://docs.python.org/3.6/library/importlib.html#importlib.abc.Loader.exec_module "importlib.abc.Loader.exec_module" ) is defined, [`importlib.abc.Loader.create_module()`](https://docs.python.org/3.6/library/importlib.html#importlib.abc.Loader.create_module "importlib.abc.Loader.create_module" ) must also be defined.

  * [`PyErr_SetImportError()`](https://docs.python.org/3.6/c-api/exceptions.html#c.PyErr_SetImportError "PyErr_SetImportError" ) now sets [`TypeError`](https://docs.python.org/3.6/library/exceptions.html#TypeError "TypeError" ) when its **msg** argument is not set. Previously only `NULL` was returned.

  * The format of the `co_lnotab` attribute of code objects changed to support negative line number delta. By default, Python does not emit bytecode with negative line number delta. Functions using `frame.f_lineno`, `PyFrame_GetLineNumber()` or `PyCode_Addr2Line()` are not affected. Functions decoding directly `co_lnotab` should be updated to use a signed 8-bit integer type for the line number delta, but it's only required to support applications using negative line number delta. See `Objects/lnotab_notes.txt` for the `co_lnotab` format and how to decode it, and see the [**PEP 511**](https://www.python.org/dev/peps/pep-0511) for the rationale.

  * The functions in the [`compileall`](https://docs.python.org/3.6/library/compileall.html#module-compileall "compileall: Tools for byte-compiling all Python source files in a directory tree." ) module now return booleans instead of `1` or `0` to represent success or failure, respectively. Thanks to booleans being a subclass of integers, this should only be an issue if you were doing identity checks for `1` or `0`. See [issue 25768](https://bugs.python.org/issue25768).

  * Reading the `port` attribute of [`urllib.parse.urlsplit()`](https://docs.python.org/3.6/library/urllib.parse.html#urllib.parse.urlsplit "urllib.parse.urlsplit" ) and [`urlparse()`](https://docs.python.org/3.6/library/urllib.parse.html#urllib.parse.urlparse "urllib.parse.urlparse" ) results now raises [`ValueError`](https://docs.python.org/3.6/library/exceptions.html#ValueError "ValueError" ) for out-of-range values, rather than returning [`None`](https://docs.python.org/3.6/library/constants.html#None "None" ). See [issue 20059](https://bugs.python.org/issue20059).

  * The [`imp`](https://docs.python.org/3.6/library/imp.html#module-imp "imp: Access the implementation of the import statement. \(deprecated\)" ) module now raises a [`DeprecationWarning`](https://docs.python.org/3.6/library/exceptions.html#DeprecationWarning "DeprecationWarning" ) instead of [`PendingDeprecationWarning`](https://docs.python.org/3.6/library/exceptions.html#PendingDeprecationWarning "PendingDeprecationWarning" ).

  * The following modules have had missing APIs added to their `__all__` attributes to match the documented APIs: [`calendar`](https://docs.python.org/3.6/library/calendar.html#module-calendar "calendar: Functions for working with calendars, including some emulation of the Unix cal program." ), [`cgi`](https://docs.python.org/3.6/library/cgi.html#module-cgi "cgi: Helpers for running Python scripts via the Common Gateway Interface." ), [`csv`](https://docs.python.org/3.6/library/csv.html#module-csv "csv: Write and read tabular data to and from delimited files." ), [`ElementTree`](https://docs.python.org/3.6/library/xml.etree.elementtree.html#module-xml.etree.ElementTree "xml.etree.ElementTree: Implementation of the ElementTree API." ), [`enum`](https://docs.python.org/3.6/library/enum.html#module-enum "enum: Implementation of an enumeration class." ), [`fileinput`](https://docs.python.org/3.6/library/fileinput.html#module-fileinput "fileinput: Loop over standard input or a list of files." ), [`ftplib`](https://docs.python.org/3.6/library/ftplib.html#module-ftplib "ftplib: FTP protocol client \(requires sockets\)." ), [`logging`](https://docs.python.org/3.6/library/logging.html#module-logging "logging: Flexible event logging system for applications." ), [`mailbox`](https://docs.python.org/3.6/library/mailbox.html#module-mailbox "mailbox: Manipulate mailboxes in various formats" ), [`mimetypes`](https://docs.python.org/3.6/library/mimetypes.html#module-mimetypes "mimetypes: Mapping of filename extensions to MIME types." ), [`optparse`](https://docs.python.org/3.6/library/optparse.html#module-optparse "optparse: Command-line option parsing library. \(deprecated\)" ), [`plistlib`](https://docs.python.org/3.6/library/plistlib.html#module-plistlib "plistlib: Generate and parse Mac OS X plist files." ), [`smtpd`](https://docs.python.org/3.6/library/smtpd.html#module-smtpd "smtpd: A SMTP server implementation in Python." ), [`subprocess`](https://docs.python.org/3.6/library/subprocess.html#module-subprocess "subprocess: Subprocess management." ), [`tarfile`](https://docs.python.org/3.6/library/tarfile.html#module-tarfile "tarfile: Read and write tar-format archive files." ), [`threading`](https://docs.python.org/3.6/library/threading.html#module-threading "threading: Thread-based parallelism." ) and [`wave`](https://docs.python.org/3.6/library/wave.html#module-wave "wave: Provide an interface to the WAV sound format." ). This means they will export new symbols when `import *` is used. See [issue 23883](https://bugs.python.org/issue23883).

  * When performing a relative import, if `__package__` does not compare equal to `__spec__.parent` then [`ImportWarning`](https://docs.python.org/3.6/library/exceptions.html#ImportWarning "ImportWarning" ) is raised. (Contributed by Brett Cannon in [issue 25791](https://bugs.python.org/issue25791).)

  * When a relative import is performed and no parent package is known, then [`ImportError`](https://docs.python.org/3.6/library/exceptions.html#ImportError "ImportError" ) will be raised. Previously, [`SystemError`](https://docs.python.org/3.6/library/exceptions.html#SystemError "SystemError" ) could be raised. (Contributed by Brett Cannon in [issue 18018](https://bugs.python.org/issue18018).)

  * Servers based on the [`socketserver`](https://docs.python.org/3.6/library/socketserver.html#module-socketserver "socketserver: A framework for network servers." ) module, including those defined in [`http.server`](https://docs.python.org/3.6/library/http.server.html#module-http.server "http.server: HTTP server and request handlers." ), [`xmlrpc.server`](https://docs.python.org/3.6/library/xmlrpc.server.html#module-xmlrpc.server "xmlrpc.server: Basic XML-RPC server implementations." ) and [`wsgiref.simple_server`](https://docs.python.org/3.6/library/wsgiref.html#module-wsgiref.simple_server "wsgiref.simple_server: A simple WSGI HTTP server." ), now only catch exceptions derived from [`Exception`](https://docs.python.org/3.6/library/exceptions.html#Exception "Exception" ). Therefore if a request handler raises an exception like [`SystemExit`](https://docs.python.org/3.6/library/exceptions.html#SystemExit "SystemExit" ) or [`KeyboardInterrupt`](https://docs.python.org/3.6/library/exceptions.html#KeyboardInterrupt "KeyboardInterrupt" ), [`handle_error()`](https://docs.python.org/3.6/library/socketserver.html#socketserver.BaseServer.handle_error "socketserver.BaseServer.handle_error" ) is no longer called, and the exception will stop a single-threaded server. (Contributed by Martin Panter in [issue 23430](https://bugs.python.org/issue23430).)

  * [`spwd.getspnam()`](https://docs.python.org/3.6/library/spwd.html#spwd.getspnam "spwd.getspnam" ) now raises a [`PermissionError`](https://docs.python.org/3.6/library/exceptions.html#PermissionError "PermissionError" ) instead of [`KeyError`](https://docs.python.org/3.6/library/exceptions.html#KeyError "KeyError" ) if the user doesn't have privileges.

  * The [`socket.socket.close()`](https://docs.python.org/3.6/library/socket.html#socket.socket.close "socket.socket.close" ) method now raises an exception if an error (e.g. EBADF) was reported by the underlying system call. See [issue 26685](https://bugs.python.org/issue26685).

  * The _decode_data_ argument for [`smtpd.SMTPChannel`](https://docs.python.org/3.6/library/smtpd.html#smtpd.SMTPChannel "smtpd.SMTPChannel" ) and [`smtpd.SMTPServer`](https://docs.python.org/3.6/library/smtpd.html#smtpd.SMTPServer "smtpd.SMTPServer" ) constructors is now `False` by default. This means that the argument passed to [`process_message()`](https://docs.python.org/3.6/library/smtpd.html#smtpd.SMTPServer.process_message "smtpd.SMTPServer.process_message" ) is now a bytes object by default, and `process_message()` will be passed keyword arguments. Code that has already been updated in accordance with the deprecation warning generated by 3.5 will not be affected.

  * All optional parameters of the [`dump()`](https://docs.python.org/3.6/library/json.html#json.dump "json.dump" ), [`dumps()`](https://docs.python.org/3.6/library/json.html#json.dumps "json.dumps" ), [`load()`](https://docs.python.org/3.6/library/json.html#json.load "json.load" ) and [`loads()`](https://docs.python.org/3.6/library/json.html#json.loads "json.loads" ) functions and [`JSONEncoder`](https://docs.python.org/3.6/library/json.html#json.JSONEncoder "json.JSONEncoder" ) and [`JSONDecoder`](https://docs.python.org/3.6/library/json.html#json.JSONDecoder "json.JSONDecoder" ) class constructors in the [`json`](https://docs.python.org/3.6/library/json.html#module-json "json: Encode and decode the JSON format." ) module are now [keyword-only](https://docs.python.org/3.6/glossary.html#keyword-only-parameter). (Contributed by Serhiy Storchaka in [issue 18726](https://bugs.python.org/issue18726).)

  * As part of [**PEP 487**](https://www.python.org/dev/peps/pep-0487), the handling of keyword arguments passed to [`type`](https://docs.python.org/3.6/library/functions.html#type "type" ) (other than the metaclass hint, `metaclass`) is now consistently delegated to [`object.__init_subclass__()`](https://docs.python.org/3.6/reference/datamodel.html#object.__init_subclass__ "object.__init_subclass__" ). This means that `type.__new__()` and `type.__init__()` both now accept arbitrary keyword arguments, but [`object.__init_subclass__()`](https://docs.python.org/3.6/reference/datamodel.html#object.__init_subclass__ "object.__init_subclass__" ) (which is called from `type.__new__()`) will reject them by default. Custom metaclasses accepting additional keyword arguments will need to adjust their calls to `type.__new__()` (whether direct or via [`super`](https://docs.python.org/3.6/library/functions.html#super "super" )) accordingly.

  * In `distutils.command.sdist.sdist`, the `default_format` attribute has been removed and is no longer honored. Instead, the gzipped tarfile format is the default on all platforms and no platform-specific selection is made. In environments where distributions are built on Windows and zip distributions are required, configure the project with a `setup.cfg` file containing the following:
```python     [sdist]

    formats=zip
    
```

This behavior has also been backported to earlier Python versions by
Setuptools 26.0.0.

  * In the [`urllib.request`](https://docs.python.org/3.6/library/urllib.request.html#module-urllib.request "urllib.request: Extensible library for opening URLs." ) module and the [`http.client.HTTPConnection.request()`](https://docs.python.org/3.6/library/http.client.html#http.client.HTTPConnection.request "http.client.HTTPConnection.request" ) method, if no Content-Length header field has been specified and the request body is a file object, it is now sent with HTTP 1.1 chunked encoding. If a file object has to be sent to a HTTP 1.0 server, the Content-Length value now has to be specified by the caller. See [issue 12319](https://bugs.python.org/issue12319).

### Changes in the C API

  * [`PyMem_Malloc()`](https://docs.python.org/3.6/c-api/memory.html#c.PyMem_Malloc "PyMem_Malloc" ) allocator family now uses the [pymalloc allocator](https://docs.python.org/3.6/c-api/memory.html#pymalloc) rather than system `malloc()`. Applications calling [`PyMem_Malloc()`](https://docs.python.org/3.6/c-api/memory.html#c.PyMem_Malloc "PyMem_Malloc" ) without holding the GIL can now crash. Set the [`PYTHONMALLOC`](https://docs.python.org/3.6/using/cmdline.html#envvar-PYTHONMALLOC) environment variable to `debug` to validate the usage of memory allocators in your application. See [issue 26249](https://bugs.python.org/issue26249).
  * [`Py_Exit()`](https://docs.python.org/3.6/c-api/sys.html#c.Py_Exit "Py_Exit" ) (and the main interpreter) now override the exit status with 120 if flushing buffered data failed. See [issue 5319](https://bugs.python.org/issue5319).

