原文：[Outputting CSV with Django](https://docs.djangoproject.com/en/1.9/howto/outputting-csv/)

---

本文解释如何动态的使用Django视图输出CSV (Comma Separated Values)。要做到这点，你可以使用Python CSV库，或者Django模板系统。


## 使用Python CSV库[¶](#using-the-python-csv-library "Permalink to this headline")

Python自带一个CSV库， [`csv`](https://docs.python.org/3/library/csv.html#module-csv "(in Python v3.5)")。在Django中使用它的关键在于，[`csv`](https://docs.python.org/3/library/csv.html#module-csv "(in Python v3.5)")模块作用于类文件对象的CSV创建能力，以及Django的 [`HttpResponse`](https://docs.djangoproject.com/en/1.9/ref/request-response/#django.http.HttpResponse "django.http.HttpResponse")对象时类文件对象。

下面是一个例子：

```py
import csv
from django.http import HttpResponse

def some_view(request):
    # Create the HttpResponse object with the appropriate CSV header.
    response = HttpResponse(content_type='text/csv')
    response['Content-Disposition'] = 'attachment; filename="somefilename.csv"'

    writer = csv.writer(response)
    writer.writerow(['First row', 'Foo', 'Bar', 'Baz'])
    writer.writerow(['Second row', 'A', 'B', 'C', '"Testing"', "Here's a quote"])

    return response
```

代码和注释应该是自解释的，但有几件事值得一提：

*   响应是一种特殊的MIME类型， _text/csv_。这告诉浏览器，这个文档是一个CSV文件，而不是一个HTML文件。如果不这样做的话，浏览器将可能把输出解析成HTML，这将导致在浏览器窗口中的丑陋且可怕的天书。
*   响应有一个额外的`Content-Disposition`头，它包含该CSV文件的名字。这个文件名是任意的；随便你叫它啥都行。它将会被浏览器用于“Save as...” 对话框等等。
*   钩挂到CSV生成API是容易的：只要把`response`作为第一个参数传递到`csv.writer`即可。`csv.writer`期望一个类文件对象，而[`HttpResponse`](https://docs.djangoproject.com/en/1.9/ref/request-response/#django.http.HttpResponse "django.http.HttpResponse")对象满足条件。
*   对于你的CSV文件中的每一行，调用`writer.writerow`，给它传递一个可迭代对象，例如列表或元祖。
*   CSV模块为你处理引号，所以你无需担心转义带有引号或者逗号的字符串。只要把原始字符串传递给`writerow()`，它就会正常工作。

>在Python 2中处理Unicode

Python 2的[`csv`](https://docs.python.org/3/library/csv.html#module-csv "(in Python v3.5)")模块不支持Unicode输入。由于Django内部使用Unicode，这意味着从源读取的字符串，例如[`HttpRequest`](https://docs.djangoproject.com/en/1.9/ref/request-response/#django.http.HttpRequest "django.http.HttpRequest") ，存在潜在的问题。有几个选择可以处理这个问题：

*   手动编码所有的Unicode对象到兼容的编码。
*   使用[csv模块的示例部分](https://docs.python.org/library/csv.html#examples)中提供的`UnicodeWriter`类。
*   使用[python-unicodecsv module](https://github.com/jdunck/python-unicodecsv)，它旨在成为[`csv`](https://docs.python.org/3/library/csv.html#module-csv "(在Python v3.5中)")随手可得的替代品，它可以优雅的处理Unicode。

欲了解更多信息，请参阅[`csv`](https://docs.python.org/3/library/csv.html#module-csv "(in Python v3.5)")模块的Python文档。



### 流式传输大尺寸CSV文件[¶](#streaming-large-csv-files "Permalink to this headline")

当处理生成非常大的响应的视图时，你也许会想要考虑使用Django的[`StreamingHttpResponse`](https://docs.djangoproject.com/en/1.9/ref/request-response/#django.http.StreamingHttpResponse "django.http.StreamingHttpResponse")取而代之。

例如，通过流式传输需要花费长时间生成的文件，你可以避免负载均衡器在服务器生成响应的时候断掉连接。

在这个例子中，我们利用Python的生成器来有效处理大尺寸CSV文件的拼接和传输：
```py
import csv

from django.utils.six.moves import range
from django.http import StreamingHttpResponse

class Echo(object):
    """An object that implements just the write method of the file-like
    interface.
    """
    def write(self, value):
        """Write the value by returning it, instead of storing in a buffer."""
        return value

def some_streaming_csv_view(request):
    """A view that streams a large CSV file."""
    # Generate a sequence of rows. The range is based on the maximum number of
    # rows that can be handled by a single sheet in most spreadsheet
    # applications.
    rows = (["Row {}".format(idx), str(idx)] for idx in range(65536))
    pseudo_buffer = Echo()
    writer = csv.writer(pseudo_buffer)
    response = StreamingHttpResponse((writer.writerow(row) for row in rows),
                                     content_type="text/csv")
    response['Content-Disposition'] = 'attachment; filename="somefilename.csv"'
    return response
```


## 使用模板系统[¶](#using-the-template-system "Permalink to this headline")

或者，你也可以使用[_Django模板系统_](https://docs.djangoproject.com/en/1.9/topics/templates/)来生成CSV。这比使用便利的Python [`csv`](https://docs.python.org/3/library/csv.html#module-csv "(在Python v3.5中)")模块更低级别，但出于完整性，这里提出了这个方法。

这里的想法是，将项列表传递给你的模板，并在一个[`for`](https://docs.djangoproject.com/en/1.9/ref/templates/builtins/#std:templatetag-for)循环中让模板输出逗号。

下面是一个例子，它生成如上相同的CSV文件：

```py
from django.http import HttpResponse
from django.template import loader, Context

def some_view(request):
    # Create the HttpResponse object with the appropriate CSV header.
    response = HttpResponse(content_type='text/csv')
    response['Content-Disposition'] = 'attachment; filename="somefilename.csv"'

    # The data is hard-coded here, but you could load it from a database or
    # some other source.
    csv_data = (
        ('First row', 'Foo', 'Bar', 'Baz'),
        ('Second row', 'A', 'B', 'C', '"Testing"', "Here's a quote"),
    )

    t = loader.get_template('my_template_name.txt')
    c = Context({
        'data': csv_data,
    })
    response.write(t.render(c))
    return response
```

这个例子和前面那个例子唯一的区别在于，它使用了模板加载，而不是CSV模块。代码剩余的部分，例如`content_type='text/csv'`， 是一样的。

然后，创建模板`my_template_name.txt`，如下所示：
```html
{% for row in data %}"{{ row.0|addslashes }}", "{{ row.1|addslashes }}", "{{ row.2|addslashes }}", "{{ row.3|addslashes }}", "{{ row.4|addslashes }}"
{% endfor %}
```

这个模板相当基础。它只是遍历提供的数据，然后一行显示一行CSV。它使用[`addslashes`](https://docs.djangoproject.com/en/1.9/ref/templates/builtins/#std:templatefilter-addslashes)模板过滤器来保证在处理引号的时候不存在任何问题。



## 其他基于文本的格式[¶](#other-text-based-formats "Permalink to this headline")

注意，这里并没有很具体到CSV —— 只是特定的输出格式。你可以食用这些技术来输出你能想到的任何基于文本的格式。你也可以使用相似的技术来生成任意的二进制数据；见[_使用Django输出PDF_](https://docs.djangoproject.com/en/1.9/howto/outputting-pdf/)。


