<!-- 
#+TITLE: Python中的assert语句
#+URL: https://dbader.org/blog/python-assert-tutorial
#+AUTHOR: lujun9972
#+TAGS: Python Common
#+DATE: [2017-03-21 二 18:29]
-->
原文地址: https://dbader.org/blog/python-assert-tutorial

- [什么是断言 & 断言擅长什么?](#org49b25fa)
- [Assert的一个例子](#org1be508a)
- [Assert的语法](#org565d224)
- [使用Assert时常见的坑](#orgeae94a8)
  - [警告 #1 – 不要将Assert用于数据验证](#orgf6294c0)
  - [警告 #2 – 小心写出永远不会失败的Assert语句](#orgaab9ee3)
- [总结](#org9e1fa7b)

如何利用断言自动探测Python程序中的错误使之更可靠且易于调试.

![img](//dbader.org/blog/figures/python-assert.png)


<a id="org49b25fa"></a>

# 什么是断言 & 断言擅长什么?

Python的 `assert` 语句可以用于调试. 它会测试一个条件,若这个条件为真,则它什么也不做,你的程序照常往下执行. 但若条件为假,则它会抛出一个 `AssertionError` 异常,该异常甚至还能包括一个错误信息.

`assertions` 的正确使用方法是用它来通知开发者程序中出现了无法回复的异常. 你不能用它来处理类似"文件没找到"这类可以预见的错误,因为用户可以采取行动修正这一错误,然后重试.

另一种看法是把 `assertions` 看成是程序的内部检查工具. 它标注某些情况是不可能在代码中出现的. 如果真的出现了其中的情况,那表示程序中肯定有bug.

若你的程序中没有bug, 就不会触发这些条件. 但如果真的触发了这些条件,那么程序就会崩溃,并显示一条断言错误信息告诉你是触发了哪一条"不该出现的"条件. 这有利于追踪和修复bug.

总结起来就是: Python的 `assert` 是一种调试的工具而不适于用来处理运行时错误的. 使用断言是为了让开发者快速找到bug产生的根源. 应该要做到只有当你的程序中出现bug时才能抛出断言错误.


<a id="org1be508a"></a>

# Assert的一个例子

下面是一个简单的例子,展示一下断言应该怎么用. 我尽量让这个例子更实际一点.

假设你在用Python搭建一个在线商场. 你需要为它添加一项折扣券的功能因此写了下面这个 `apply_discount` 功能:

```python
def apply_discount(product, discount):
    price = int(product['price'] * (1.0 - discount))
    assert 0 <= price <= product['price']
    return price
```

注意到了那个 `assert` 语句了么? 它会保证,不管怎么样,打折后的价格都不会低于0元,也不会高于原价.

让我们用各种折扣来调用该函数,确保该函数能如我们所愿那边地正常工作.

```python
#
# Our example product: Nice shoes for $149.00
#
>>> shoes = {'name': 'Fancy Shoes', 'price': 14900}

#
# 25% off -> $111.75
#
>>> apply_discount(shoes, 0.25)
11175
```

不错,挺正常的. 现在再试试其他非法的折扣:

```python
#
# A "200% off" discount:
#
>>> apply_discount(shoes, 2.0)
Traceback (most recent call last):
  File "<input>", line 1, in <module>
    apply_discount(prod, 2.0)
  File "<input>", line 4, in apply_discount
    assert 0 <= price <= product['price']
AssertionError

#
# A "-30% off" discount:
#
>>> apply_discount(shoes, -0.3)
Traceback (most recent call last):
  File "<input>", line 1, in <module>
    apply_discount(prod, -0.3)
  File "<input>", line 4, in apply_discount
    assert 0 <= price <= product['price']
AssertionError
```

如你所见,当传入一个非法的折扣时会引发 `AssertionError` 异常,并指出异常的行以及不匹配的断言条件. 当我们在测试在线商场时,若出现了这种错误,通过查看 traceback 可以很方便地找出错误原因.

这就是断言的威力.


<a id="org565d224"></a>

# Assert的语法

开始使用某项语言特性前最好先了解它是如何实现的. 下面我们就来看一下 [Python文档中assert语句的语法是怎样的](https://docs.python.org/3/reference/simple_stmts.html#the-assert-statement):

```python
assert_stmt ::= "assert" expression1 ["," expression2]
```

其中 `expression1` 就是我们要测试的条件, 而可选的 `expression2` 是断言失败时要显示的错误信息.

在执行期间,Python解释器会将每个 `assert` 语句都转换成类似下面这样:

```python
if __debug__:
    if not expression1:
        raise AssertionError(expression2)
```

你也可以通过 `expression2` 传递一个可选的错误信息, 当触发 `AssertionError` 异常时, 该错误信息也会一同在 traceback 中显示出来. 它可以进一步简化调试. 举个例子,我曾见过这样的代码:

```python
if cond == 'x':
    do_x()
elif cond == 'y':
    do_y()
else:
    assert False, ("This should never happen, but it does occasionally. "
                   "We're currently trying to figure out why. "
                   "Email dbader if you encounter this in the wild.")
```

这段代码丑吗? 是的,的确很丑. 但是当你遇见 [heisenbug](https://en.wikipedia.org/wiki/Heisenbug) 这样的问题时,这种技术都很有用了. 😉


<a id="orgeae94a8"></a>

# 使用Assert时常见的坑

在你继续往下读之前,有两点需要注意:

第一点告诉你如何防止引入安全风险和bug,第二点 about a syntax quirk that makes it easy to write useless assertions.

这两条警告听起来(而且实际上也是)听恐怖的, 所以请你至少了解一下这两条警告或者至少读一下后面的总结.


<a id="orgf6294c0"></a>

## 警告 #1 – 不要将Assert用于数据验证

**Python解释器可以全面关闭Assert特性. 因此不要依赖 `assert` 表达式来进行数据验证和数据处理.**

使用 `asserts` 一定要注意,通过给CPython提供命令行参数(或者修改PYTHONOPTIMIZE环境变量) `-O` 或 `-OO` 可以 [全面禁止断言生效](https://docs.python.org/3/library/constants.html#__debug__)

这会将所有的 `assert` 语句都转换成空操作: 断言在编译时会被丢弃,而且也不会被执行,因此里面的条件表达式也就不会执行了.

许多语言都有类似的这种设计. 这带来的一个后果就是用 `assert` 语句来校验输入的数据是很危险的一件事情.

也就是说,如果你想用 `assert` 来检查函数参数的合法性,那恐怕会事与愿违,而且很容易产生bug和安全漏洞.

让我们来看一个简单的例子. 假设你在用python创建一个在线商场的应用. 在代码中有一个函数用来响应用户请求删除一个产品:

```python
def delete_product(product_id, user):
    assert user.is_admin(), 'Must have admin privileges to delete'
    assert store.product_exists(product_id), 'Unknown product id'
    store.find_product(product_id).delete()
```

仔细看看这个函数. 如果断言被禁用了会怎样?

这个函数虽然只有短短的三行,但是由于误用 `assert` 语句,产生了两个严重的问题:

1.  使用 `assert` 语句来验证管理员特权是很危险的. Python解释器禁用断言会让它变成一个空操作. 由于根本不会运行特权检查语句,这使得任何用户都能够删除产品. 这样一来就引入了一个安全问题,允许攻击者损坏你的客户或公司的在线商城中的数据. 这可不妙啊.
2.  禁用断言后, `product_exists()` 检查会被跳过. 也就是说 `find_product()` 的参数可能是一个非法的产品id号—这可能会产生很严重的bug. 在最坏的情况下, 它会导致针对我们商场的拒绝服务的攻击. 若删除未知的商品会导致商场崩溃的话,攻击者就可以通过发起age无效的删除请求让我们的商场失去服务能力.

那我们该怎么办呢? 答案是不要用断言来做数据验证, 而改用普通的if语句+抛出验证异常的方法来验证. 像这样子:

```python
def delete_product(product_id, user):
    if not user.is_admin():
        raise AuthError('Must have admin privileges to delete')

    if not store.product_exists(product_id):
        raise ValueError('Unknown product id')

    store.find_product(product_id).delete()
```

相比之下,修改后的函数不再只是抛出泛泛的 `AssertionError` 异常,它能够根据实际情况抛出像 `ValueError` 或 `AuthError` 这类更贴合实际意义的异常(当然 [这些异常需要我们自己定义](https://dbader.org/blog/python-custom-exceptions)).


<a id="orgaab9ee3"></a>

## 警告 #2 – 小心写出永远不会失败的Assert语句

一不小心就会写出永远为真的assert语句. 我之前也着过这个道. 还专门写过 [一篇相关的文章](https://dbader.org/blog/catching-bogus-python-asserts).

总结起来就是:

**当你使用一个元组作为assert语句的第一个参数时,该断言永远为真不会失败.**

比如, 这个断言就永远不会失败:

```python
assert(1 == 2, 'This should fail')
```

这是因为非空的元组在Python中总是为真. 传递一个元组给 `assert` 语句会让该 `assert` 的条件永远为真—这样以来上面的 `assert` 语句也就没啥用了,它永远不可能失败然后触发异常.

这种反直觉的行为很容易使得在写多行的assert时出错. 它会破坏测试代码中的测试案例,使它们失去保障安全的能力. 比如你可能在单元测试集中有下面这样一个断言:

```python
assert (
    counter == 10,
    'It should have counted all the items'
)
```

初看起来貌似没什么问题. 然而该测试案例永远无法捕捉到错误的结果:不管 `counter` 变量的值是什么,它的结果总为真.

就像我说过的,你很容易就误用了assert. 不过好在有一些措施能够帮助你避开这些问题:

[>> Read the full article on bogus assertions to get the dirty details.](https://dbader.org/blog/catching-bogus-python-asserts)


<a id="org9e1fa7b"></a>

# 总结

虽然有写警告,我依然认为Python的断言是一个非常强力的调试工具,然而该工具尚未被开发者们充分地使用起来.

理解断言的工作原理以及学会在恰当的时候使用断言会帮助你写出更加可维护也更加易于调试的程序. 学会这项技能会提升你的Python代码水平,让你成为一个更成熟的Python开发者.
