原文：[why-does-python-code-run-faster-in-a-function.md](https://stackabuse.com/why-does-python-code-run-faster-in-a-function/)

---
## 介绍

Python 不一定以其速度而闻名，但有些东西可以帮助您从代码中榨取更多性能。令人惊讶的是，其中一种做法是在函数中而不是在全局范围内运行代码。在本文中，我们将了解为什么 Python 代码在函数中运行得更快以及 Python 代码执行的工作原理。


## Python 代码执行

要理解为什么 Python 代码在函数中运行得更快，我们首先需要了解 Python 是如何执行代码的。Python 是一种解释性语言，这意味着它逐行读取并执行代码。当 Python 执行脚本时，它首先将其编译为字节码（一种更接近机器代码的中间语言），然后 Python 解释器再执行这些字节码。

```py
def hello_world():
    print("Hello, World!")

import dis
dis.dis(hello_world)
```

```
  2           0 LOAD_GLOBAL              0 (print)
              2 LOAD_CONST               1 ('Hello, World!')
              4 CALL_FUNCTION            1
              6 POP_TOP
              8 LOAD_CONST               0 (None)
             10 RETURN_VALUE

```

Python 中的 [dis](https://docs.python.org/3/library/dis.html) 模块将函数 `hello_world` 反汇编为字节码，如上所示。


> **注意：** Python 解释器是执行字节码的虚拟机。默认的 Python 解释器是 CPython，它是用 C 编写的。还有其他 Python 解释器，如 Jython（用 Java 编写）、IronPython（用 .NET 编写）和 PyPy（用 Python 和 C 编写），但 CPython 是最常用的。


## 为什么 Python 代码在函数中运行得更快

考虑一个简化的示例，其中包含一个迭代一系列数字的循环：

```py
def my_function():
    for i in range(100000000):
        pass

```

编译此函数时，字节码可能如下所示：

```
  SETUP_LOOP              20 (to 23)
  LOAD_GLOBAL             0 (range)
  LOAD_CONST              3 (100000000)
  CALL_FUNCTION           1
  GET_ITER            
  FOR_ITER                6 (to 22)
  STORE_FAST              0 (i)
  JUMP_ABSOLUTE           13
  POP_BLOCK           
  LOAD_CONST              0 (None)
  RETURN_VALUE

```

这里的关键指令是 `STORE_FAST`，它用来存储循环变量 `i`。

现在让我们考虑一下，当循环位于 Python 脚本顶层时，字节码是怎么样的：

```
  SETUP_LOOP              20 (to 23)
  LOAD_NAME               0 (range)
  LOAD_CONST              3 (100000000)
  CALL_FUNCTION           1
  GET_ITER            
  FOR_ITER                6 (to 22)
  STORE_NAME              1 (i)
  JUMP_ABSOLUTE           13
  POP_BLOCK           
  LOAD_CONST              2 (None)
  RETURN_VALUE

```

请注意，这里使用了 `STORE_NAME` 指令，而不是 `STORE_FAST`。

字节码 `STORE_FAST` 比 `STORE_NAME` 快，因为在函数中，局部变量是存储在固定大小的数组中的，而不是存储在字典中。这个数组可通过索引直接访问，使得变量检索非常快。基本上，它只是对列表进行指针查找并增加 PyObject 的引用计数，这两者都是高效操作。

另一方面，全局变量存储在字典中。当访问全局变量时，Python 必须执行哈希表查找，其中涉及哈希值的计算，然后检索与其关联的值。尽管这些操作经过优化，但它本质上仍然比基于索引的查找慢。

## Python 代码的基准测试和分析

想亲自测试一下吗？尝试对您的代码进行基准测试和分析吧。

基准测试和分析是性能优化的重要实践。它们可以帮助您了解代码的行为方式以及瓶颈所在。

基准测试就是对代码进行计时以查看运行时间。您可以使用 Python 的内置 `time` [timeit](https://docs.python.org/3/library/timeit.html)。

另一方面，分析提供了代码执行的更详细视图。它向您显示大部分时间花在执行哪些代码、调用哪些函数以及调用频率。Python 的内置 [profile](https://docs.python.org/3/library/profile.html) 或者 [cProfile](https://docs.python.org/3/library/cProfile.html) 模块可用于此目的。

以下是分析 Python 代码的一种方法：

```py
import cProfile

def loop():
    for i in range(10000000):
        pass

cProfile.run('loop()')

```

这将输出 `loop` 函数执行期间所有函数调用的详细报告。

> **注意：** 分析会给代码执行增加相当多的开销，因此分析器显示的执行时间可能会比实际执行时间长。


## 分别在函数与全局范围内对代码进行基准测试

在 Python 中，代码执行的速度可能会根据代码的执行位置（在函数中还是在全局范围内）而有所不同。让我们用一个简单的例子来对两者进行比较。

考虑以下计算数字阶乘的代码片段：

```py
def factorial(n):
    result = 1
    for i in range(1, n + 1):
        result *= i
    return result

```

现在让我们在全局范围内运行相同的代码：

```py
n = 20
result = 1
for i in range(1, n + 1):
    result *= i

```
为了对这两段代码进行基准测试，我们可以使用 Python 中的 `timeit` 模块，它提供了一种对少量 Python 代码进行计时的简单方法。

```py
import timeit

# Factorial function here...

def benchmark():
    start = timeit.default_timer()

    factorial(20)

    end = timeit.default_timer()
    print(end - start)

#
# Run benchmark on function code
#
benchmark()
# Prints: 3.541994374245405e-06

#
# Run benchmark on global scope code
#
start = timeit.default_timer()

n = 20
result = 1
for i in range(1, n + 1):
    result *= i

end = timeit.default_timer()
print(end - start) 
# Pirnts: 5.375011824071407e-06

```

您会发现函数代码比全局作用域代码执行得更快。这是因为由于我们之前讨论的原因，Python 执行函数代码的速度更快。


> **注意：** 如果在同一个脚本中运行 `benchmark()` 函数和全局作用域代码，全局作用域代码会运行得更快。这是因为 `benchmark()` 函数增加了一些执行时间的开销，并且全局代码在内部还进行了一些优化。但是，如果单独运行它们，您会发现函数代码确实运行得更快。

## 分别在函数与全局范围内对代码进行分析

Python 提供了一个用于此目的的内置模块 `cProfile`。让我们用它来分析一个新函数，该函数在局部和全局范围内计算平方和。

```py
import cProfile

def sum_of_squares():
    total = 0
    for i in range(1, 10000000):
        total += i * i

i = None
total = 0
def sum_of_squares_g():
    global i
    global total
    for i in range(1, 10000000):
        total += i * i
    
def profile(func):
    pr = cProfile.Profile()
    pr.enable()

    func()

    pr.disable()
    pr.print_stats()
#
# Profile function code
#
print("Function scope:")
profile(sum_of_squares)

#
# Profile global scope code
#
print("Global scope:")
profile(sum_of_squares_g)
```

从分析结果中，您将看到函数代码在执行时间方面更加高效。

```
Function scope:
         2 function calls in 0.903 seconds

   Ordered by: standard name

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
        1    0.903    0.903    0.903    0.903 profiler.py:3(sum_of_squares)
        1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}


Global scope:
         2 function calls in 1.358 seconds

   Ordered by: standard name

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
        1    1.358    1.358    1.358    1.358 profiler.py:10(sum_of_squares_g)
        1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}

```

我们认为 `sum_of_squares_g()` 函数是全局的，因为它使用了两个全局变量 `i` 和 `total`。正如我们之前所看到的，全局变量会减慢代码执行速度，这就是我们在此代码中将这些变量设置为全局变量的原因。

## 优化 Python 函数性能

鉴于 Python 函数往往比全局范围内的等效代码运行得更快，因此值得研究如何进一步优化函数性能。

当然，由于我们之前看到的，一种策略是使用局部变量而不是全局变量。下面是一个例子：

```py
import time

# Global variable
x = 5

def calculate_power_global():
    for i in range(10000000):
        y = x ** 2  # Accessing global variable

def calculate_power_local(x):
    for i in range(10000000):
        y = x ** 2  # Accessing local variable

start = time.time()
calculate_power_global()
end = time.time()

print(f"Execution time with global variable: {end - start} seconds")

start = time.time()
calculate_power_local(x)
end = time.time()

print(f"Execution time with local variable: {end - start} seconds")

```

在这个例子中，`calculate_power_local` 通常会比 `calculate_power_global` 跑得更快，因为它使用了一个局部变量，而不是全局变量。

```
Execution time with global variable: 1.9901456832885742 seconds
Execution time with local variable: 1.9626312255859375 seconds
```

另一种优化策略是尽可能使用内置函数和库。Python 的内置函数是用 C 实现的，比Python快得多。同样的，许多 Python 库（例如 NumPy 和 Pandas）也是用 C 或 C++ 实现的，这使得它们比同等的 Python 代码更快。

例如，考虑这个对数字列表求和的任务。您可以编写一个函数来执行此操作：

```py
def sum_numbers(numbers):
    total = 0
    for number in numbers:
        total += number
    return total

```

然而，Python 的内置 `sum` 函数也可以被用来做同样的事情，但它会更快：

```py
numbers = [1, 2, 3, 4, 5]
total = sum(numbers)
```
尝试自己计时这两个代码片段，并找出哪一个更快吧！

## 总结

在本文中，我们探索了 Python 代码执行的有趣世界，特别关注为什么封装在函数中的 Python 代码往往运行得更快。我们简要介绍了基准测试和分析的概念，提供了如何在函数和全局范围内执行这些过程的实际示例。

我们还讨论了优化 Python 函数性能的几种方法。虽然这些技巧肯定可以使您的代码运行得更快，但您应该谨慎使用某些优化，因为平衡可读性、可维护性与性能是非常重要的。