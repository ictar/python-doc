原文：[Outputting PDFs with Django](https://docs.djangoproject.com/en/1.9/howto/outputting-pdf/)

---

本文解释了如何使用Django视图动态的生成PDF文件。优秀的开源[ReportLab](http://www.reportlab.com/opensource/)，这个Python PDF库，使其成为了可能。

动态生成PDF文件的好处是，你可以根据不同的目的（比如，为不同的用户或不同的内容）创建定制的PDF。

例如，[kusports.com](http://www.kusports.com/)就用Django来为参加疯狂三月比赛的人生成定制的、打印友好的，PDF格式的NCAA锦标赛表格。


## 安装ReportLab[¶](#install-reportlab "Permalink to this headline")

[在PyPI上](https://pypi.python.org/pypi/reportlab)，可以找到ReportLab库。还可以下载一份[用户指南](http://www.reportlab.com/docs/reportlab-userguide.pdf) (并不是巧合，它就是一份PDF文件)。你可以通过
`pip`安装ReportLab:
```sh
$ pip install reportlab
```

通过将其导入到Python的交互式解释器来测试安装是否成功：
```py
>>> import reportlab
```

如果这个命令不会引发任何错误，那么说明安装好了。


## 编写视图[¶](#write-your-view "Permalink to this headline")

使用Django动态生成PDF文件的关键在于，ReportLab API操作的是类文件对象，而Django的[`HttpResponse`](https://docs.djangoproject.com/en/1.9/ref/request-response/#django.http.HttpResponse "django.http.HttpResponse")对象就是类文件对象。

这里是一个“Hello World”例子：

```py
from reportlab.pdfgen import canvas
from django.http import HttpResponse

def some_view(request):
    # Create the HttpResponse object with the appropriate PDF headers.
    response = HttpResponse(content_type='application/pdf')
    response['Content-Disposition'] = 'attachment; filename="somefilename.pdf"'

    # Create the PDF object, using the response object as its "file."
    p = canvas.Canvas(response)

    # Draw things on the PDF. Here's where the PDF generation happens.
    # See the ReportLab documentation for the full list of functionality.
    p.drawString(100, 100, "Hello world.")

    # Close the PDF object cleanly, and we're done.
    p.showPage()
    p.save()
    return response
```

代码和注释应该是自解释的，但有几件事值得一提：

*   window.response有一个特殊的MIME类型，_application/pdf_。它告诉浏览器，该文档是一个PDF文件，而不是一个HTML文件。如果不使用它，浏览器将有可能将输出解析为HTML，这将导致在浏览器窗口中丑陋可怕的鬼画符。

*   response有一个额外的`Content-Disposition`头，它包含该PDF文件的名字。这个文件名是可选的：任何你想要的时候使用它。它也被浏览器使用于“Save as...”对话框中。

*   在这个例子中，`Content-Disposition`头以`'attachment; '`开头。这会强制web浏览器弹出一个对话框来提示/确认如何处理该文档，即使在该机器上已经设置了默认值。如果你不用`'attachment;'`，那么浏览器将使用为PDF文件配置的程序/插件来处理该PDF。下面是代码可能的样子：
```py
response['Content-Disposition'] = 'filename="somefilename.pdf"'
```

*   钩挂到ReportLab API是容易的：只要把`response`作为第一个参数传递给`canvas.Canvas`即可。`Canvas`类需要一个类文件对象，而[`HttpResponse`](https://docs.djangoproject.com/en/1.9/ref/request-response/#django.http.HttpResponse "django.http.HttpResponse")对象恰好满足要求。

*   注意，所有后继的PDF生成方法都是由该PDF对象（在这个例子中是`p`）调用的，而不是`response`。

*   最后，在该PDF文件上调用`showPage()`和`save()`很重要。

>注意

>ReportLab并不是线程安全的。我们的一些用户已经报告了在构建同时由多人访问的PDF生成的Django视图时出现的奇怪的问题。


## 复杂的PDF文件[¶](#complex-pdfs "Permalink to this headline")

如果你正在用ReportLab创建一个复杂的PDF文档，那么考虑一下使用[`io`](https://docs.python.org/3/library/io.html#module-io "(in Python v3.5)")库作为临时保存你的PDF文件的地方。这个库提供了一个非常有效的类文件对象接口。这里是上面的“Hello World”例子改写为使用[`io`](https://docs.python.org/3/library/io.html#module-io "(in Python v3.5)")的代码：
```py
from io import BytesIO
from reportlab.pdfgen import canvas
from django.http import HttpResponse

def some_view(request):
    # Create the HttpResponse object with the appropriate PDF headers.
    response = HttpResponse(content_type='application/pdf')
    response['Content-Disposition'] = 'attachment; filename="somefilename.pdf"'

    buffer = BytesIO()

    # Create the PDF object, using the BytesIO object as its "file."
    p = canvas.Canvas(buffer)

    # Draw things on the PDF. Here's where the PDF generation happens.
    # See the ReportLab documentation for the full list of functionality.
    p.drawString(100, 100, "Hello world.")

    # Close the PDF object cleanly.
    p.showPage()
    p.save()

    # Get the value of the BytesIO buffer and write it to the response.
    pdf = buffer.getvalue()
    buffer.close()
    response.write(pdf)
    return response
```

## 更多资源[¶](#further-resources "Permalink to this headline")

*   [PDFlib](http://www.pdflib.org/)是另外一个具有Python绑定的PDF生成库。要在Django中使用它，只要使用在这篇文章中解释的相同概念即可。
*   [XHTML2PDF](https://github.com/xhtml2pdf/xhtml2pdf)是另外一个PDF生成库。它自带了一个如何集成到Django的例子。
*   [HTMLdoc](https://www.msweet.org/projects.php?Z1) 是一个命令行脚本，它可以将HTML转换成PDF。它并没有一个Python接口，但是你可以使用`system`或者`popen`来跳出到shell方式，然后在Python中检索输出。

## 其他格式[¶](#other-formats "Permalink to this headline")

请注意，这些例子中并没有很多是PDF特有的 —— 只是使用`reportlab`的一小部分。你可以使用类似的技术来生成任意你可以找到Python库来生成的格式。另见[_使用Django输出CSV_](./使用Django输出CSV.md)，以获得另一个例子，以及一些在生成基于文本的格式时可以使用的技术。

