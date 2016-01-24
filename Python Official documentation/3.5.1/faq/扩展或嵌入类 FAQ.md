# 扩展/嵌入类 FAQ

原文：[Extending/Embedding FAQ](https://docs.python.org/3/faq/extending.html)

---

目录：

*   [扩展/嵌入类 FAQ](#extending-embedding-faq)
    *   [我可以用C创建自己的函数吗？?](#can-i-create-my-own-functions-in-c)
    *   [我可以用C++创建自己的函数吗？](#id1)
    *   [编写C是很难的；有替代方法吗?](#writing-c-is-hard-are-there-any-alternatives)
    *   [如何从C中执行任意Python语句?](#how-can-i-execute-arbitrary-python-statements-from-c)
    *   [如何评估C中的任意Python表达式?](#how-can-i-evaluate-an-arbitrary-python-expression-from-c)
    *   [如何从一个Python对象中提取C的值?](#how-do-i-extract-c-values-from-a-python-object)
    *   [如何使用Py_BuildValue()来创建一个任意长度的元组?](#how-do-i-use-py-buildvalue-to-create-a-tuple-of-arbitrary-length)
    *   [如何调用C中一个对象的方法?](#how-do-i-call-an-object-s-method-from-c)
    *   [如何捕获来自PyErr_Print()的输出（或打印到stdout/stderr的任何东西）?](#how-do-i-catch-the-output-from-pyerr-print-or-anything-that-prints-to-stdout-stderr)
    *   [如何用C访问一个用Python编写的模块?](#how-do-i-access-a-module-written-in-python-from-c)
    *   [如何用Python接入C++对象?](#how-do-i-interface-to-c-objects-from-python)
    *   [我通过使用Setup文件来增加一个模块，但make的时候失败了；为什么？?](#i-added-a-module-using-the-setup-file-and-the-make-fails-why)
    *   [如何调试扩展?](#how-do-i-debug-an-extension)
    *   [我想在自己的Linux系统上编译一个Python模块，但缺少某些文件。为什么？?](#i-want-to-compile-a-python-module-on-my-linux-system-but-some-files-are-missing-why)
    *   [如何从“无效输入”中辨别出“不完整输入”?](#how-do-i-tell-incomplete-input-from-invalid-input)
    *   [如何查找未定义的g++符号__builtin_new或者__pure_virtual?](#how-do-i-find-undefined-g-symbols-builtin-new-or-pure-virtual)
    *   [我可以创建一个某些方法用C实现，某些方法用Python实现的对象类吗（例如，通过继承）?](#can-i-create-an-object-class-with-some-methods-implemented-in-c-and-others-in-python-e-g-through-inheritance)

---

## [我可以用C创建自己的函数吗？?](#id3)[¶](#can-i-create-my-own-functions-in-c "Permalink to this headline")

是滴，你可以用C创建包含函数，变量，异常，甚至是类型的模块。见文档[扩展及内嵌Python解释器](../extending/index.html#extending-index)中的解释。

大多数的中高级Python书籍也将覆盖这个主题。


## [我可以用C++创建自己的函数吗？](#id4)[¶](#id1 "Permalink to this headline")

是的，使用C++中的C兼容性功能。把`extern "C" {
... }`放在Python包含文件的周围，并把`extern "C"`放在每一个将被Python解释器调用的函数之前。拥有构造器的全局或静态C++对象可能不是一个好主意。


## [编写C是很难的；有替代方法吗?](#id5)[¶](#writing-c-is-hard-are-there-any-alternatives "Permalink to this headline")

有大量替代品可以用来编写你自己的C扩展，取决于你想干嘛。

[Cython](http://cython.org) 及相关的[Pyrex](http://www.cosc.canterbury.ac.nz/greg.ewing/python/Pyrex/)是这样的编译器，它们接受Python的一个稍作修改的形式，并生成对应的C代码。Cython和Pyrex使得不需要学习Python的C API就能编写扩展成为可能。

如果你需要一些当前不存在Python扩展的C或C++库接口，那么可以尝试使用类似于[SWIG](http://www.swig.org)的工具来包装该库的数据类型和函数。  [SIP](http://www.riverbankcomputing.co.uk/software/sip/intro), [CXX](http://cxx.sourceforge.net/) [Boost](http://www.boost.org/libs/python/doc/index.html), 或者 [Weave](http://docs.scipy.org/doc/scipy-dev/reference/tutorial/weave.html)也可以用来包装C++库。


## [如何从C中执行任意Python语句?](#id6)[¶](#how-can-i-execute-arbitrary-python-statements-from-c "Permalink to this headline")

来做到这一点的最高级的函数是[`PyRun_SimpleString()`](../c-api/veryhigh.html#c.PyRun_SimpleString "PyRun_SimpleString")，它接收一个在模块`__main__`上下文执行的字符串参数，并在成功时返回0，发生异常（包括`SyntaxError`）时返回-1。如果想要更多的控制权，使用[`PyRun_String()`](../c-api/veryhigh.html#c.PyRun_String "PyRun_String"); 见`Python/pythonrun.c`中 [`PyRun_SimpleString()`](../c-api/veryhigh.html#c.PyRun_SimpleString "PyRun_SimpleString")的源代码。

## [如何评估C中的任意Python表达式?](#id7)[¶](#how-can-i-evaluate-an-arbitrary-python-expression-from-c "Permalink to this headline")

Call the function [`PyRun_String()`](../c-api/veryhigh.html#c.PyRun_String "PyRun_String") from the previous question with the
start symbol [`Py_eval_input`](../c-api/veryhigh.html#c.Py_eval_input "Py_eval_input"); it parses an expression, evaluates it and
returns its value.


## [如何从一个Python对象中提取C的值?](#id8)[¶](#how-do-i-extract-c-values-from-a-python-object "Permalink to this headline")

That depends on the object’s type.  If it’s a tuple, [`PyTuple_Size()`](../c-api/tuple.html#c.PyTuple_Size "PyTuple_Size")
returns its length and [`PyTuple_GetItem()`](../c-api/tuple.html#c.PyTuple_GetItem "PyTuple_GetItem") returns the item at a specified
index.  Lists have similar functions, `PyListSize()` and
[`PyList_GetItem()`](../c-api/list.html#c.PyList_GetItem "PyList_GetItem").

For bytes, [`PyBytes_Size()`](../c-api/bytes.html#c.PyBytes_Size "PyBytes_Size") returns its length and
[`PyBytes_AsStringAndSize()`](../c-api/bytes.html#c.PyBytes_AsStringAndSize "PyBytes_AsStringAndSize") provides a pointer to its value and its
length.  Note that Python bytes objects may contain null bytes so C’s
`strlen()` should not be used.

To test the type of an object, first make sure it isn’t _NULL_, and then use
[`PyBytes_Check()`](../c-api/bytes.html#c.PyBytes_Check "PyBytes_Check"), [`PyTuple_Check()`](../c-api/tuple.html#c.PyTuple_Check "PyTuple_Check"), [`PyList_Check()`](../c-api/list.html#c.PyList_Check "PyList_Check"), etc.

There is also a high-level API to Python objects which is provided by the
so-called ‘abstract’ interface – read `Include/abstract.h` for further
details.  It allows interfacing with any kind of Python sequence using calls
like [`PySequence_Length()`](../c-api/sequence.html#c.PySequence_Length "PySequence_Length"), [`PySequence_GetItem()`](../c-api/sequence.html#c.PySequence_GetItem "PySequence_GetItem"), etc. as well
as many other useful protocols such as numbers ([`PyNumber_Index()`](../c-api/number.html#c.PyNumber_Index "PyNumber_Index") et
al.) and mappings in the PyMapping APIs.


## [如何使用Py_BuildValue()来创建一个任意长度的元组?](#id9)[¶](#how-do-i-use-py-buildvalue-to-create-a-tuple-of-arbitrary-length "Permalink to this headline")

You can’t.  Use [`PyTuple_Pack()`](../c-api/tuple.html#c.PyTuple_Pack "PyTuple_Pack") instead.


## [如何调用C中一个对象的方法?](#id10)[¶](#how-do-i-call-an-object-s-method-from-c "Permalink to this headline")

The [`PyObject_CallMethod()`](../c-api/object.html#c.PyObject_CallMethod "PyObject_CallMethod") function can be used to call an arbitrary
method of an object.  The parameters are the object, the name of the method to
call, a format string like that used with [`Py_BuildValue()`](../c-api/arg.html#c.Py_BuildValue "Py_BuildValue"), and the
argument values:
```python
PyObject *
PyObject_CallMethod(PyObject *object, const char *method_name,
                    const char *arg_format, ...);
```                    

This works for any object that has methods – whether built-in or user-defined.
You are responsible for eventually [`Py_DECREF()`](../c-api/refcounting.html#c.Py_DECREF "Py_DECREF")‘ing the return value.

To call, e.g., a file object’s “seek” method with arguments 10, 0 (assuming the
file object pointer is “f”):
```python
res = PyObject_CallMethod(f, "seek", "(ii)", 10, 0);
if (res == NULL) {
        ... an exception occurred ...
}
else {
        Py_DECREF(res);
}
```
Note that since [`PyObject_CallObject()`](../c-api/object.html#c.PyObject_CallObject "PyObject_CallObject") _always_ wants a tuple for the
argument list, to call a function without arguments, pass “()” for the format,
and to call a function with one argument, surround the argument in parentheses,
e.g. “(i)”.


## [如何捕获来自PyErr_Print()的输出（或打印到stdout/stderr的任何东西）?](#id11)[¶](#how-do-i-catch-the-output-from-pyerr-print-or-anything-that-prints-to-stdout-stderr "Permalink to this headline")

In Python code, define an object that supports the `write()` method.  Assign
this object to [`sys.stdout`](../library/sys.html#sys.stdout "sys.stdout") and [`sys.stderr`](../library/sys.html#sys.stderr "sys.stderr").  Call print_error, or
just allow the standard traceback mechanism to work. Then, the output will go
wherever your `write()` method sends it.

The easiest way to do this is to use the [`io.StringIO`](../library/io.html#io.StringIO "io.StringIO") class:
```python
>>> import io, sys
>>> sys.stdout = io.StringIO()
>>> print('foo')
>>> print('hello world!')
>>> sys.stderr.write(sys.stdout.getvalue())
foo
hello world!
```

A custom object to do the same would look like this:
```python
>>> import io, sys
>>> class StdoutCatcher(io.TextIOBase):
...     def __init__(self):
...         self.data = []
...     def write(self, stuff):
...         self.data.append(stuff)
...
>>> import sys
>>> sys.stdout = StdoutCatcher()
>>> print('foo')
>>> print('hello world!')
>>> sys.stderr.write(''.join(sys.stdout.data))
foo
hello world!
```

## [如何用C访问一个用Python编写的模块?](#id12)[¶](#how-do-i-access-a-module-written-in-python-from-c "Permalink to this headline")

You can get a pointer to the module object as follows:

`module = PyImport_ImportModule("<modulename>");`

If the module hasn’t been imported yet (i.e. it is not yet present in
[`sys.modules`](../library/sys.html#sys.modules "sys.modules")), this initializes the module; otherwise it simply returns
the value of `sys.modules["&lt;modulename&gt;"]`.  Note that it doesn’t enter the
module into any namespace – it only ensures it has been initialized and is
stored in [`sys.modules`](../library/sys.html#sys.modules "sys.modules").

You can then access the module’s attributes (i.e. any name defined in the
module) as follows:

`attr = PyObject_GetAttrString(module, "<attrname>");`

Calling [`PyObject_SetAttrString()`](../c-api/object.html#c.PyObject_SetAttrString "PyObject_SetAttrString") to assign to variables in the module
also works.


## [如何用Python接入C++对象?](#id13)[¶](#how-do-i-interface-to-c-objects-from-python "Permalink to this headline")

Depending on your requirements, there are many approaches.  To do this manually,
begin by reading [the “Extending and Embedding” document](../extending/index.html#extending-index).  Realize that for the Python run-time system, there isn’t a
whole lot of difference between C and C++ – so the strategy of building a new
Python type around a C structure (pointer) type will also work for C++ objects.

For C++ libraries, see [编写C是很难的；有替代方法吗?](#c-wrapper-software).


## [我通过使用Setup文件来增加一个模块，但make的时候失败了；为什么？?](#id14)[¶](#i-added-a-module-using-the-setup-file-and-the-make-fails-why "Permalink to this headline")

Setup must end in a newline, if there is no newline there, the build process
fails.  (Fixing this requires some ugly shell script hackery, and this bug is so
minor that it doesn’t seem worth the effort.)


## [如何调试扩展?](#id15)[¶](#how-do-i-debug-an-extension "Permalink to this headline")

When using GDB with dynamically loaded extensions, you can’t set a breakpoint in
your extension until your extension is loaded.

In your `.gdbinit` file (or interactively), add the command:

`br _PyImport_LoadDynamicModule`

Then, when you run GDB:
```sh
$ gdb /local/bin/python
gdb) run myscript.py
gdb) continue # repeat until your extension is loaded
gdb) finish   # so that your extension is loaded
gdb) br myfunction.c:50
gdb) continue
```

## [我想在自己的Linux系统上编译一个Python模块，但缺少某些文件。为什么？?](#id16)[¶](#i-want-to-compile-a-python-module-on-my-linux-system-but-some-files-are-missing-why "Permalink to this headline")

Most packaged versions of Python don’t include the
`/usr/lib/python2._x_/config/` directory, which contains various files
required for compiling Python extensions.

For Red Hat, install the python-devel RPM to get the necessary files.

For Debian, run `apt-get install python-dev`.


## [如何从“无效输入”中辨别出“不完整输入”?](#id17)[¶](#how-do-i-tell-incomplete-input-from-invalid-input "Permalink to this headline")

Sometimes you want to emulate the Python interactive interpreter’s behavior,
where it gives you a continuation prompt when the input is incomplete (e.g. you
typed the start of an “if” statement or you didn’t close your parentheses or
triple string quotes), but it gives you a syntax error message immediately when
the input is invalid.

In Python you can use the [`codeop`](../library/codeop.html#module-codeop "codeop: Compile (possibly incomplete) Python code.") module, which approximates the parser’s
behavior sufficiently.  IDLE uses this, for example.

The easiest way to do it in C is to call [`PyRun_InteractiveLoop()`](../c-api/veryhigh.html#c.PyRun_InteractiveLoop "PyRun_InteractiveLoop") (perhaps
in a separate thread) and let the Python interpreter handle the input for
you. You can also set the [`PyOS_ReadlineFunctionPointer()`](../c-api/veryhigh.html#c.PyOS_ReadlineFunctionPointer "PyOS_ReadlineFunctionPointer") to point at your
custom input function. See `Modules/readline.c` and `Parser/myreadline.c`
for more hints.

However sometimes you have to run the embedded Python interpreter in the same
thread as your rest application and you can’t allow the
[`PyRun_InteractiveLoop()`](../c-api/veryhigh.html#c.PyRun_InteractiveLoop "PyRun_InteractiveLoop") to stop while waiting for user input.  The one
solution then is to call `PyParser_ParseString()` and test for `e.error`
equal to `E_EOF`, which means the input is incomplete).  Here’s a sample code
fragment, untested, inspired by code from Alex Farber:
```python
#include <Python.h>
#include <node.h>
#include <errcode.h>
#include <grammar.h>
#include <parsetok.h>
#include <compile.h>

int testcomplete(char *code)
  /* code should end in \n */
  /* return -1 for error, 0 for incomplete, 1 for complete */
{
  node *n;
  perrdetail e;

  n = PyParser_ParseString(code, &_PyParser_Grammar,
                           Py_file_input, &e);
  if (n == NULL) {
    if (e.error == E_EOF)
      return 0;
    return -1;
  }

  PyNode_Free(n);
  return 1;
}
```

Another solution is trying to compile the received string with
[`Py_CompileString()`](../c-api/veryhigh.html#c.Py_CompileString "Py_CompileString"). If it compiles without errors, try to execute the
returned code object by calling [`PyEval_EvalCode()`](../c-api/veryhigh.html#c.PyEval_EvalCode "PyEval_EvalCode"). Otherwise save the
input for later. If the compilation fails, find out if it’s an error or just
more input is required - by extracting the message string from the exception
tuple and comparing it to the string “unexpected EOF while parsing”.  Here is a
complete example using the GNU readline library (you may want to ignore
**SIGINT** while calling readline()):
```python
#include <stdio.h>
#include <readline.h>

#include <Python.h>
#include <object.h>
#include <compile.h>
#include <eval.h>

int main (int argc, char* argv[])
{
  int i, j, done = 0;                          /* lengths of line, code */
  char ps1[] = ">>> ";
  char ps2[] = "... ";
  char *prompt = ps1;
  char *msg, *line, *code = NULL;
  PyObject *src, *glb, *loc;
  PyObject *exc, *val, *trb, *obj, *dum;

  Py_Initialize ();
  loc = PyDict_New ();
  glb = PyDict_New ();
  PyDict_SetItemString (glb, "__builtins__", PyEval_GetBuiltins ());

  while (!done)
  {
    line = readline (prompt);

    if (NULL == line)                          /* Ctrl-D pressed */
    {
      done = 1;
    }
    else
    {
      i = strlen (line);

      if (i > 0)
        add_history (line);                    /* save non-empty lines */

      if (NULL == code)                        /* nothing in code yet */
        j = 0;
      else
        j = strlen (code);

      code = realloc (code, i + j + 2);
      if (NULL == code)                        /* out of memory */
        exit (1);

      if (0 == j)                              /* code was empty, so */
        code[0] = '\0';                        /* keep strncat happy */

      strncat (code, line, i);                 /* append line to code */
      code[i + j] = '\n';                      /* append '\n' to code */
      code[i + j + 1] = '\0';

      src = Py_CompileString (code, "<stdin>", Py_single_input);

      if (NULL != src)                         /* compiled just fine - */
      {
        if (ps1  == prompt ||                  /* ">>> " or */
            '\n' == code[i + j - 1])           /* "... " and double '\n' */
        {                                               /* so execute it */
          dum = PyEval_EvalCode (src, glb, loc);
          Py_XDECREF (dum);
          Py_XDECREF (src);
          free (code);
          code = NULL;
          if (PyErr_Occurred ())
            PyErr_Print ();
          prompt = ps1;
        }
      }                                        /* syntax error or E_EOF? */
      else if (PyErr_ExceptionMatches (PyExc_SyntaxError))
      {
        PyErr_Fetch (&exc, &val, &trb);        /* clears exception! */

        if (PyArg_ParseTuple (val, "sO", &msg, &obj) &&
            !strcmp (msg, "unexpected EOF while parsing")) /* E_EOF */
        {
          Py_XDECREF (exc);
          Py_XDECREF (val);
          Py_XDECREF (trb);
          prompt = ps2;
        }
        else                                   /* some other syntax error */
        {
          PyErr_Restore (exc, val, trb);
          PyErr_Print ();
          free (code);
          code = NULL;
          prompt = ps1;
        }
      }
      else                                     /* some non-syntax error */
      {
        PyErr_Print ();
        free (code);
        code = NULL;
        prompt = ps1;
      }

      free (line);
    }
  }

  Py_XDECREF(glb);
  Py_XDECREF(loc);
  Py_Finalize();
  exit(0);
}
```

## [如何查找未定义的g++符号__builtin_new或者__pure_virtual?](#id18)[¶](#how-do-i-find-undefined-g-symbols-builtin-new-or-pure-virtual "Permalink to this headline")

To dynamically load g++ extension modules, you must recompile Python, relink it
using g++ (change LINKCC in the Python Modules Makefile), and link your
extension module using g++ (e.g., `g++ -shared -o mymodule.so mymodule.o`).


## [我可以创建一个某些方法用C实现，某些方法用Python实现的对象类吗（例如，通过继承）?](#id19)[¶](#can-i-create-an-object-class-with-some-methods-implemented-in-c-and-others-in-python-e-g-through-inheritance "Permalink to this headline")

Yes, you can inherit from built-in classes such as [`int`](../library/functions.html#int "int"), [`list`](../library/stdtypes.html#list "list"),
[`dict`](../library/stdtypes.html#dict "dict"), etc.

The Boost Python Library (BPL, [http://www.boost.org/libs/python/doc/index.html](http://www.boost.org/libs/python/doc/index.html))
provides a way of doing this from C++ (i.e. you can inherit from an extension
class written in C++ using the BPL).
