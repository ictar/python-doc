原文：[\*args and **kwargs in python explained](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-explained/)

---

嘿，大家好呀。我发现，大多数的Python新手程序员很难搞清楚\*args和\*\*kwargs魔术变量。所以，它们是什么呢？首先，让我告诉你，写\*args或者\*\*kwargs并不是必须的。只有\*（星号）是必须的。你也可以写成\*var和\*\*vars。写成\*args和\*\*kwargs仅仅是一个惯例。所以，让我们先来看看*args。

** *args的使用 **  
\*args和\*\*kwargs最常用于函数定义。\*args和\*\*kwargs允许你传递数量不定的参数给一个函数。这里的变量的意思是，你事先不知道用户会传递多少个变量给你的函数，因此，在这种情况下，你使用这两个关键字。\*args是用来发送一个**非关键字**可变长度的变量列表给函数。这里是一个例子，以助你弄明白：

```python
def test_var_args(f_arg, *argv):
    print "first normal arg:", f_arg
    for arg in argv:
        print "another arg through *argv :", arg

test_var_args('yasoob','python','eggs','test')
```

这生成以下结果：

```
first normal arg: yasoob
another arg through *argv : python
another arg through *argv : eggs
another arg through *argv : test
```

我希望这解开了你的困惑。现在，让我们谈谈\*\*kwargs

**\*\*kwargs的使用**  
\*\*kwargs运行你传递**关键字**可变长度参数给函数。如果你想要处理函数中的**命名参数**，那么你应该使用\*\*kwargs。下面是一个例子，以帮助理解它：

```python
def greet_me(**kwargs):
    if kwargs is not None:
        for key, value in kwargs.iteritems():
            print "%s == %s" %(key,value)
 
>>> greet_me(name="yasoob")
name == yasoob
```

所以，你可以看到我们是如何在函数中处理一个关键字参数列表的。这仅仅是\*\*kwargs的基础知识，而你可以看到它多有用。现在，让我们聊聊你可以如何使用\*args和\*\*kwargs，利用一个参数列表或者参数字典来调用一个函数。

**使用*args和\*\*kwargs来调用一个函数**  
所以在这里，我们会看到如何使用\*args和\*\*kwargs来调用一个函数。试想，你有这么一个小函数：

```python
def test_args_kwargs(arg1, arg2, arg3):
    print "arg1:", arg1
    print "arg2:", arg2
    print "arg3:", arg3
```

现在，你可以使用\*args或者\*\*kwargs来传递参数给这个小函数。以下是如何做到这点：

```python
# first with *args
>>> args = ("two", 3,5)
>>> test_args_kwargs(*args)
arg1: two
arg2: 3
arg3: 5

# now with **kwargs:
>>> kwargs = {"arg3": 3, "arg2": "two","arg1":5}
>>> test_args_kwargs(**kwargs)
arg1: 5
arg2: two
arg3: 3
```

**使用*args、\*\*kwargs以及普通参数的顺序**  
所以，如果你想要在函数中同时使用这三个，那么顺序是

```python
some_func(fargs,*args,**kwargs)
```

我希望你明白了\*args和\*\*kwargs的使用。如果你对其有任何疑问或困惑，那么请随意在下面留言。要进一步研究，我推荐官方的[关于定义函数的python文档](http://docs.python.org/tutorial/controlflow.html#more-on-defining-functions)，以及[stackoverflow上的\*args和**kwargs](http://stackoverflow.com/questions/3394835/args-and-kwargs)。

