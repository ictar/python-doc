原文：[Idiomatic Python: EAFP versus LBYL](https://blogs.msdn.microsoft.com/pythonengineering/2016/06/29/idiomatic-python-eafp-versus-lbyl/)

---

Python中的一个惯用手法，也是常常为那些使用异常被当成，好吧，异常的编程语言的人们所惊讶的是，[EAFP](https://docs.python.org/3.5/glossary.html#term-eafp): "比之请求许可，请求原谅更容易些"。简单说一下，EAFP意味着你应该只做那些期望做的事，并且当异常从操作中抛出来的时候捕获它们，然后处理之。而人们通常使用的是[LBYL](https://docs.python.org/3.5/glossary.html#term-lbyl): "三思而后行"。相比EAFP，LBYL是你搜下检查某些事情是否会成功，然后仅当你知道它能工作的时候才继续。

如果这一切在散文层面没有任何意义，那么别担心，代码将使其显而易见。让我们考虑一个例子，接收一个可能（或者可能不会）具有某个特定键的字典。在LBYL中，你将在第一次使用这个键之前检查它：

```python

    if "key" in dict_:

        value += dict_["key"]

    
```

这阻止了抛出一个`KeyError`异常的可能，这似乎是符合逻辑的。但是这个代码给用户的感觉是，异常情况时该字典拥有这个键，而不是该键可能不存在，这可能不是真的。没有注释说明一般情况，那么如果你不是这个代码的作者，那么你可能就不知道什么才是期望为真的。或者换句话说，这个代码似乎强调该键在该字典中确实有些特殊，而未必真的会出现这种情况或者有那么重要。

但如果该键一般会存在于该字典中，或者不应该被认为是任何形式的异常呢？EAFP让你以一种不一样的方式编写同样的代码，它淡化了该键存在于该字典中的重要性：

```python

    try:

        value += dict_["key"]

    except KeyError:

        pass

    
```

阅读这段代码，它告诉了你什么？似乎表明，该键通常会存在于该字典中，但有时候不是。如果该键缺失，那么这也不是什么大事，但如果它确实存在，那么应该用它来调用一个函数。这种将代码意味着什么清晰的传达给开发者对Python来说是非常重要的，因此它优于LBYL风格，后者可能错误的传达了该键的存在多么常见/罕见/重要。

有些人读到这里，会说相比之LBYL，EAFP版本更长更明显，这显然是正确的。但“显式优于隐式”这种思想是关键的[Python自身的设计方针](https://www.python.org/dev/peps/pep-0020/)，因此，该代码的明确性是有意为之的。

除了潜在的明确性/冗长性，人们往往对EAFP的另一个担忧是性能。如果你是从一种触发异常是非常昂贵的编程语言来到Python的，那么这种担忧是可以理解的。但由于异常像EAFP这样用于控制流程，Python实现努力使异常开销变小，因此编写代码时，你不应该担心异常的成本。

虽然，使用EAFP要提醒一下，适用于任何处理捕获异常的代码的是，不要在你的`try`中的代码之上放置过于宽泛的代码。例如，可能像这样开始写代码是很有诱惑力的：

```python

    

    try:

        do_something(dict_["key"])

    except KeyError:

        pass

    
```

该代码的问题是，如果do_something()自身抛出了一个你不想抑制的`KeyError`，会发生什么呢？在这种情况下，你应该更明确了解你正考虑的特殊情况是什么：

```python

    

    try:

        value = dict_["key"]

    except KeyError:

        pass

    else:

        do_something(value)

    
```

使用任何你有可能抑制异常的代码块，你想要尽可能多的本地化在`try`块中执行的代码。现在，如果在像EAFP似乎过于明确的这种情况中，你更倾向于LBYL，那么使用LBYL也没有什么错误。

```python

    

    if "key" in dict_:

        do_something(dict["key"])

    
```

你在本文中应该获得的关键是，EAFP在Python中是一种合法的编码风格，并且当其有意义时，你应该随意使用它。
