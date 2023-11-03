原文：[Python Gotcha: Join vs Concat](https://andrewwegner.com/python-gotcha-join-vs-concat.html)

---

## 问题[¶](#the-problem "Permanent link")

这是一个有点人为的示例，用来演示问题所在。我需要创建一个包含 100,000 个 `word` 的字符串。生成该字符串的最快方法是什么呢？我可以使用字符串连接（只是用 `+` 将字符串相互连接）。我还可以在包含 100,000 个元素的列表上使用 [`join`](https://docs.python.org/3/library/stdtypes.html#str.join)。

为此，我的示例代码如下

```py
def concat_string(word: str, iterations: int = 100000) -> str:
    final_string = ""
    for i in range(iterations):
        final_string += word
    return final_string

def join_string(word: str, iterations: int = 100000) -> str:
    final_string = []
    for i in range(iterations):
        final_string.append(word)
    return "".join(final_string)
```

## 结果[¶](#results "Permanent link") 

在 `concat_string` 中，我迭代了 100,000 个元素，每次迭代都将 我的 `word` 添加到字符串末尾。在 `join_string` 中，我在每次迭代时将我的 `word` 附加到列表中，然后在末尾将列表连接成字符串。

通过内置分析器 (`cProfile`) 运行每个函数会显示这两个函数的执行情况。


```sh
>>> cProfile.run('concat_string("word ")')

      4 function calls in 1.026 seconds

Ordered by: standard name

ncalls  tottime  percall  cumtime  percall filename:lineno(function)
     1    0.000    0.000    1.026    1.026 <string>:1(<module>)
     1    1.026    1.026    1.026    1.026 test.py:9(concat_string)
     1    0.000    0.000    1.026    1.026 {built-in method builtins.exec}
     1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}

>>> cProfile.run('join_string("word ")')

      100005 function calls in 0.013 seconds

Ordered by: standard name

ncalls  tottime  percall  cumtime  percall filename:lineno(function)
     1    0.000    0.000    0.013    0.013 <string>:1(<module>)
     1    0.009    0.009    0.013    0.013 test.py:16(join_string)
     1    0.000    0.000    0.013    0.013 {built-in method builtins.exec}
100000    0.004    0.000    0.004    0.000 {method 'append' of 'list' objects}
     1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}
     1    0.000    0.000    0.000    0.000 {method 'join' of 'str' objects}
```

## 这里发生了什么？[¶](#whats-happening-here "Permanent link")


`join` 比连接方法快了 75 倍以上。

为什么呢？

字符串是 Python 中不可变的对象。我在上一篇[关于默认参数的陷阱文章](https://andrewwegner.com/python-gotcha-optional-default-arguments.html)中对其进行了讨论。这种不变性意味着不能更改字符串。`concat_string` 看起来好像每次 `+` 操作都会更改字符串，但实际上，Python 必须在循环的每次迭代中创建一个新的字符串对象。这意味着有 99,999 个临时字符串值 - 几乎所有这些值都在连接操作期间的下一次迭代中被立即创建和丢弃。

另一方面，`join_string` 将 100,000 个字符串对象附加到列表中。但在此过程中仅创建了一个列表。最终的 `join` 仅对所有 100,000 个字符串进行*一次*串联。



## 这意味着什么？[¶](#what-are-the-implications-of-this "Permanent link")

虽然这是一个展示问题的人为示例，对字符串不变性的实际性能影响可能并不明显。Python 中还有其他常用的创建新字符串的地方。例如`f-strings`，`%s` 格式说明符和 `.format()`。它们中每一个都会创建一个全新的字符串。

这并不意味着您应该避免使用这些，因为只有在将*大量*字符串附加在一起的情况下，性能影响才会真正明显。但是，如果循环中有字符串格式化行，那么它就是一个需要重点关注的性能改进之处。