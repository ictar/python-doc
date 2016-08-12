原文：[How a template engine works](https://fengsp.github.io/blog/2016/8/how-a-template-engine-works/)

---  

我已经使用模板引擎很长一段时间了，现在终于有时间来了解一下模板引擎是如何工作的。

### 概述

简单地说，模板引擎是一个工具，你可以用它来进行涉及到很多的文本数据的编程任务。最常见的用法是Web应用程序中的HTML生成。尤其是在Python中，如果你想要使用一个模板引擎，那么现在我们有几种选择，例如[jinja](http://jinja.pocoo.org/)或者[mako](http://www.makotemplates.org/)。在这里，我们要通过深入tornado web框架的template模块，找出一个模板引擎是如何工作的，这是一个简单的系统，这样我们就可以专注于过程的基本思路。

进入实现细节之前，让我们首先来看看简单的API使用：

```python

    from tornado import template
    
    PAGE_HTML = """
    <html>
      Hello, {{ username }}!
      <ul>
        {% for job in job_list %}
          <li>{{ job }}</li>
        {% end %}
      </ul>
    </html>
    """
    t = template.Template(PAGE_HTML)
    print t.generate(username='John', job_list=['engineer'])
    
```

这里，用户名在PAGE_HTML中是动态的，工作列表也是。你可以安装`tornado`，然后运行代码来看看输出。

### 实现

如果我们进一步看看`PAGE_HTML`，那我们很容易就可以发现，一个模板字符串有两个部分，静态文本部分和动态部分。我们使用特殊标记来区分开动态部分。总的来说，模板引擎应该接受模板字符串，然后原样输出静态部分，它还需要利用给定的上下文来处理动态部分，然后生成正确的字符串结果。所以，基本上，一个模板引擎就是一个Python函数：

```python

    def template_engine(template_string, **context):
        # process here
        return result_string
    
```

在处理过程中，模板引擎有两个阶段：

  * _解析_
  * _渲染_

解析阶段接受模板字符串，然后生成可以渲染的结果。将模板字符串想成源代码的话，解析工具可以是一个编程语言解释器或者编程语言编译器。如果工具是解释器，那么解析会生成一个数据结构，而渲染工具将会根据这个结构来生成结果文本。Django模板引擎解析工具就是这么一个解释器。另外，解析生成一些可执行代码，那么渲染工具仅仅执行代码并生成结果。Jinja2, Mako和Tornado的template模块都使用编译器作为解析工具。

### 编译

如上所述，现在，我们需要解析模板字符串，而tornado的template模块中的解析工具将模板编译成Python代码。我们的解析工具只是一个Python函数，它生成Python代码：

```python

    def parse_template(template_string):
        # compilation
        return python_source_code
    
```

在我们进入`parse_template`的实现之前，让我们看看它所生成的代码，下面是一个样例模板源字符串：

```python

    <html>
      Hello, {{ username }}!
      <ul>
        {% for job in jobs %}
          <li>{{ job.name }}</li>
        {% end %}
      </ul>
    </html>
    
```

我们的`parse_template`函数将把这个模板编译成Python代码，它仅仅是一个函数，简化版本如下：

```python

    def _execute():
        _buffer = []
        _buffer.append('\n<html>\n  Hello, ')
        _tmp = username
        _buffer.append(str(_tmp))
        _buffer.append('!\n  <ul>\n    ')
        for job in jobs:
            _buffer.append('\n      <li>')
            _tmp = job.name
            _buffer.append(str(_tmp))
            _buffer.append('</li>\n    ')
        _buffer.append('\n  </ul>\n</html>\n')
        return ''.join(_buffer)
    
```

现在，我们的模板被解析到一个名为`_execute`的函数中，该函数访问全局命名空间的所有上下文变量。该函数创建一个字符串列表，然后将它们连在一起变成结果字符串。`username`位于局部变量`_tmp`中，查询局部变量比查询全局变量快得多。这里，还可以做一些其他的优化，例如：

```python

    _buffer.append('hello')
    
    _append_buffer = _buffer.append
    # faster for repeated use
    _append_buffer('hello')
    
```

`{{ ... }}`中的字符串被分析附加到字符串缓冲列表中。在tornado template模块中，对你的语句中可以包含的表达式并无限制，if和for块直接被转换成Python。

### 代码

现在，让我们看看实际实现。我们所使用的核心接口是`Template`类，当我们创建一个`Template`对象时，会对模板字符串进行编译，接着可以用它来渲染一个给定的上下文。我们仅需一次编译，就可以把该模板对象缓存起来，构造器的简化版本如下：

```python

    class Template(object):
        def __init__(self, template_string):
            self.code = parse_template(template_string)
            self.compiled = compile(self.code, '<string>', 'exec')
    
```

The `compile` will compile the _source_ into a code object. We can execute it
later with an `exec` statement. Now let's build the `parse_template` function,
firstly we need to parse our template string into a list of nodes that knows
how to generate Python code, we need a function called `_parse`, we will see
the function later, we need some helpers now, to help with reading through the
template file, we have the `_TemplateReader` class, which handles the reading
for us as we consume the template file. We need to start from the begining and
keep going ahead to find some special notations, the `_TemplateReader` will
keep the current position and give us ways to do it:

```python

    class _TemplateReader(object):
        def __init__(self, text):
            self.text = text
            self.pos = 0
    
        def find(self, needle, start=0, end=None):
            pos = self.pos
            start += pos
            if end is None:
                index = self.text.find(needle, start)
            else:
                end += pos
                index = self.text.find(needle, start, end)
            if index != -1:
                index -= pos
            return index
    
        def consume(self, count=None):
            if count is None:
                count = len(self.text) - self.pos
            newpos = self.pos + count
            s = self.text[self.pos:newpos]
            self.pos = newpos
            return s
    
        def remaining(self):
            return len(self.text) - self.pos
    
        def __len__(self):
            return self.remaining()
    
        def __getitem__(self, key):
            if key < 0:
                return self.text[key]
            else:
                return self.text[self.pos + key]
    
        def __str__(self):
            return self.text[self.pos:]
    
```

To help with generating the Python code, we need the `_CodeWriter` class, this
class writes lines of codes and manages indentation, also it is one Python
context manager:

```python

    class _CodeWriter(object):
        def __init__(self):
            self.buffer = cStringIO.StringIO()
            self._indent = 0
    
        def indent(self):
            return self
    
        def indent_size(self):
            return self._indent
    
        def __enter__(self):
            self._indent += 1
            return self
    
        def __exit__(self, *args):
            self._indent -= 1
    
        def write_line(self, line, indent=None):
            if indent == None:
                indent = self._indent
            for i in xrange(indent):
                self.buffer.write("    ")
            print self.buffer, line
    
        def __str__(self):
            return self.buffer.getvalue()
    
```

在`parse_template`的开头，我们首先创建一个模板读取器：

```python

    def parse_template(template_string):
        reader = _TemplateReader(template_string)
        file_node = _File(_parse(reader))
        writer = _CodeWriter()
        file_node.generate(writer)
        return str(writer)
    
```

Then we pass the reader to the `_parse` function and produces a list of nodes.
All of there nodes are the child nodes of the template file node. We create
one CodeWriter object, the file node writes Python code into the CodeWriter,
and we return the generated Python code. The `_Node` class would handle the
Python code generation for a specific case, we will see it later. Now let's go
back to our `_parse` function:

```python

    def _parse(reader, in_block=None):
        body = _ChunkList([])
        while True:
            # Find next template directive
            curly = 0
            while True:
                curly = reader.find("{", curly)
                if curly == -1 or curly + 1 == reader.remaining():
                    # EOF
                    if in_block:
                        raise ParseError("Missing {%% end %%} block for %s" %
                                         in_block)
                    body.chunks.append(_Text(reader.consume()))
                    return body
                # If the first curly brace is not the start of a special token,
                # start searching from the character after it
                if reader[curly + 1] not in ("{", "%"):
                    curly += 1
                    continue
                # When there are more than 2 curlies in a row, use the
                # innermost ones.  This is useful when generating languages
                # like latex where curlies are also meaningful
                if (curly + 2 < reader.remaining() and
                    reader[curly + 1] == '{' and reader[curly + 2] == '{'):
                    curly += 1
                    continue
                break
    
```

We loop forever to find a template directive in the remaining file, if we
reach the end of the file, we append the text node and exit, otherwise, we
have found one template directive.

```python

            # Append any text before the special token
            if curly > 0:
                body.chunks.append(_Text(reader.consume(curly)))
    
```

在我们处理了特殊的token后，如果有静态部分，则将其附加到文本节点。

```python

            start_brace = reader.consume(2)
    
```

获取起始括号，它应该是`'{{'`或者`'{%'`。

```python

            # Expression
            if start_brace == "{{":
                end = reader.find("}}")
                if end == -1 or reader.find("\n", 0, end) != -1:
                    raise ParseError("Missing end expression }}")
                contents = reader.consume(end).strip()
                reader.consume(2)
                if not contents:
                    raise ParseError("Empty expression")
                body.chunks.append(_Expression(contents))
                continue
    
```

起始括号是`'{{'`，说明这里有一个表达式，仅需获取表达式内容，然后附加一个`_Expression`节点。

```python

            # Block
            assert start_brace == "{%", start_brace
            end = reader.find("%}")
            if end == -1 or reader.find("\n", 0, end) != -1:
                raise ParseError("Missing end block %}")
            contents = reader.consume(end).strip()
            reader.consume(2)
            if not contents:
                raise ParseError("Empty block tag ({% %})")
            operator, space, suffix = contents.partition(" ")
            # End tag
            if operator == "end":
                if not in_block:
                    raise ParseError("Extra {% end %} block")
                return body
            elif operator in ("try", "if", "for", "while"):
                # parse inner body recursively
                block_body = _parse(reader, operator)
                block = _ControlBlock(contents, block_body)
                body.chunks.append(block)
                continue
            else:
                raise ParseError("unknown operator: %r" % operator)
    
```

We have a block here, normally we would get the block body recursively and
append a `_ControlBlock` node, the block body should be a list of nodes. If we
encounter an `{% end %}`, the block ends and we exit the function.

It is time to find out the secrets of `_Node` class, it is quite simple:

```python

    class _Node(object):
        def generate(self, writer):
            raise NotImplementedError()
    
```

```python

    class _ChunkList(_Node):
        def __init__(self, chunks):
            self.chunks = chunks
    
        def generate(self, writer):
            for chunk in self.chunks:
                chunk.generate(writer)
    
```

一个`_ChunkList`仅是一个节点列表。

```python

    class _File(_Node):
        def __init__(self, body):
            self.body = body
    
        def generate(self, writer):
            writer.write_line("def _execute():")
            with writer.indent():
                writer.write_line("_buffer = []")
                self.body.generate(writer)
                writer.write_line("return ''.join(_buffer)")
    
```

`_File`节点将`_execute`函数写入到CodeWriter。

```python

    class _Expression(_Node):
        def __init__(self, expression):
            self.expression = expression
    
        def generate(self, writer):
            writer.write_line("_tmp = %s" % self.expression)
            writer.write_line("_buffer.append(str(_tmp))")
    
    
    class _Text(_Node):
        def __init__(self, value):
            self.value = value
    
        def generate(self, writer):
            value = self.value
            if value:
                writer.write_line('_buffer.append(%r)' % value)
    
```

The `_Text` and `_Expression` node are also really simple, just append what
you get from the template source.

```python

    class _ControlBlock(_Node):
        def __init__(self, statement, body=None):
            self.statement = statement
            self.body = body
    
        def generate(self, writer):
            writer.write_line("%s:" % self.statement)
            with writer.indent():
                self.body.generate(writer)
    
```

For a `_ControlBlock` node, we need to indent and write our child node list
with the indentation.

Now let's get back to the rendering part, we render a context by using the
`generate` method of `Template` object, the `generate` function just call the
compiled Python code:

```python

    def generate(self, **kwargs):
        namespace = {}
        namespace.update(kwargs)
        exec self.compiled in namespace
        execute = namespace["_execute"]
        return execute()
    
```

`exec`函数在给定的全局命名空间内执行编译好的代码，然后，从全局命名空间内抓取`_execute`函数，然后调用它。

### 下一步

就是这样啦，将模板编译成Python函数，然后执行以获取结果。tornado的template模块比我们这里讨论的特性要多得多，但我们已经了解到了基本思想，如果感兴趣的话，你可以发现更多：

  * 模板继承
  * 模板包含
  * 更多控制逻辑，例如else, elif, try, 等等
  * 空格控制
  * 转义
  * 更多模板指令
