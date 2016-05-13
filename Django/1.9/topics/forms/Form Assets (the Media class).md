原文：[Form Assets (the `Media` class)](https://docs.djangoproject.com/en/1.9/topics/forms/media/)

---

渲染一个引人注意并且易于使用的Web表单不仅需要HTML，还需要CSS样式表，而如果你想要使用花哨的"Web2.0"窗口小部件，那么你可能还需要在每个页面上包含一些JavaScript。任何给定页面需要的CSS和JavaScript确切组合将取决于在该页面上使用的部件。

这就是资产定义扮演的角色。Django允许你关联不同的文件，例如样式表和脚本，到那些需要这些资产的表单和部件上。例如，如果你想要使用一个日历来渲染DateField，那么你可以定义自定义日历控件。然后，这个部件可以与渲染日历所需的CSS和JavaScript关联起来。当将Calendar部件用于表单时，Django能够识别需要的CSS和JavaScript，并提供文件名列表，以使得你可以轻松地在你的网页上包含它们。

>资产和Django Admin

>Django Admin应用为日历，过滤选择等定义了许多自定义的小部件。这些部件定义了资产要求，并且Django Admin使用这些自定义的布局来代替Django的默认部件。Admin模板将只包含那些在任意给定页面上渲染部件需要的文件。

>如果你喜欢Django Admin应用使用的布局，随时可以在你的应用中使用！它们存储在`django.contrib.admin.widgets`中。

>哪一个JavaScript工具包？

>存在许多JavaScript工具包，而它们中许多都包括那些可以用来增强你的应用部件（例如日历部件）。Django刻意回避了给任何JavaScript工具包打广告的嫌疑。每个工具包都有自己的相对优势和劣势 —— 使用那些满足你的要求的工具包吧，哪个都成。Django能够与任何JavaScript工具包集成。

## 作为静态定义的资产

定义资产最简单的方式是一个静态定义。使用这种方法，声明式一个内部的`Media`类。该内部类的属性定义了要求。

这里有一个简单的例子：
    
    ```python
    from django import forms
    
    class CalendarWidget(forms.TextInput):
        class Media:
            css = {
                'all': ('pretty.css',)
            }
            js = ('animations.js', 'actions.js')
    ```

This code defines a `CalendarWidget`, which will be based on `TextInput`.
Every time the CalendarWidget is used on a form, that form will be directed to
include the CSS file `pretty.css`, and the JavaScript files `animations.js`
and `actions.js`.

This static definition is converted at runtime into a widget property named
`media`. The list of assets for a `CalendarWidget` instance can be retrieved
through this property:

    
    
    >>> w = CalendarWidget()
    >>> print(w.media)
    <link href="http://static.example.com/pretty.css" type="text/css" media="all" rel="stylesheet" />
    <script type="text/javascript" src="http://static.example.com/animations.js"></script>
    <script type="text/javascript" src="http://static.example.com/actions.js"></script>
    

Here's a list of all possible `Media` options. There are no required options.

### `css`

A dictionary describing the CSS files required for various forms of output
media.

The values in the dictionary should be a tuple/list of file names. See the
section on paths for details of how to specify paths to these files.

The keys in the dictionary are the output media types. These are the same
types accepted by CSS files in media declarations: 'all', 'aural', 'braille',
'embossed', 'handheld', 'print', 'projection', 'screen', 'tty' and 'tv'. If
you need to have different stylesheets for different media types, provide a
list of CSS files for each output medium. The following example would provide
two CSS options - one for the screen, and one for print:

    
    
    class Media:
        css = {
            'screen': ('pretty.css',),
            'print': ('newspaper.css',)
        }
    

If a group of CSS files are appropriate for multiple output media types, the
dictionary key can be a comma separated list of output media types. In the
following example, TV's and projectors will have the same media requirements:

    
    
    class Media:
        css = {
            'screen': ('pretty.css',),
            'tv,projector': ('lo_res.css',),
            'print': ('newspaper.css',)
        }
    

如果渲染了这最后一个CSS定义，那么它将变成以下HTML：
    
    
    <link href="http://static.example.com/pretty.css" type="text/css" media="screen" rel="stylesheet" />
    <link href="http://static.example.com/lo_res.css" type="text/css" media="tv,projector" rel="stylesheet" />
    <link href="http://static.example.com/newspaper.css" type="text/css" media="print" rel="stylesheet" />
    

### `js`¶

A tuple describing the required JavaScript files. See the section on paths for details of how to specify paths to these files.

### `extend`¶

A boolean defining inheritance behavior for `Media` declarations.

By default, any object using a static `Media` definition will inherit all the
assets associated with the parent widget. This occurs regardless of how the
parent defines its own requirements. For example, if we were to extend our
basic Calendar widget from the example above:

    
    
    >>> class FancyCalendarWidget(CalendarWidget):
    ...     class Media:
    ...         css = {
    ...             'all': ('fancy.css',)
    ...         }
    ...         js = ('whizbang.js',)
    
    >>> w = FancyCalendarWidget()
    >>> print(w.media)
    <link href="http://static.example.com/pretty.css" type="text/css" media="all" rel="stylesheet" />
    <link href="http://static.example.com/fancy.css" type="text/css" media="all" rel="stylesheet" />
    <script type="text/javascript" src="http://static.example.com/animations.js"></script>
    <script type="text/javascript" src="http://static.example.com/actions.js"></script>
    <script type="text/javascript" src="http://static.example.com/whizbang.js"></script>
    

The FancyCalendar widget inherits all the assets from its parent widget. If
you don't want `Media` to be inherited in this way, add an `extend=False`
declaration to the `Media` declaration:

    
    
    >>> class FancyCalendarWidget(CalendarWidget):
    ...     class Media:
    ...         extend = False
    ...         css = {
    ...             'all': ('fancy.css',)
    ...         }
    ...         js = ('whizbang.js',)
    
    >>> w = FancyCalendarWidget()
    >>> print(w.media)
    <link href="http://static.example.com/fancy.css" type="text/css" media="all" rel="stylesheet" />
    <script type="text/javascript" src="http://static.example.com/whizbang.js"></script>
    

If you require even more control over inheritance, define your assets using a
dynamic property. Dynamic properties give you complete control over which
files are inherited, and which are not.

## `Media`作为动态属性

If you need to perform some more sophisticated manipulation of asset
requirements, you can define the `media` property directly. This is done by
defining a widget property that returns an instance of `forms.Media`. The
constructor for `forms.Media` accepts `css` and `js` keyword arguments in the
same format as that used in a static media definition.

For example, the static definition for our Calendar Widget could also be
defined in a dynamic fashion:

    
    
    class CalendarWidget(forms.TextInput):
        def _media(self):
            return forms.Media(css={'all': ('pretty.css',)},
                               js=('animations.js', 'actions.js'))
        media = property(_media)
    

See the section on Media objects for more details on how to construct return
values for dynamic `media` properties.

## 资产定义中的路径

Paths used to specify assets can be either relative or absolute. If a path
starts with `/`, `http://` or `https://`, it will be interpreted as an
absolute path, and left as-is. All other paths will be prepended with the
value of the appropriate prefix.

As part of the introduction of the [_staticfilesapp_](https://docs.djangoproject.com/en/1.9/ref/contrib/staticfiles/) two new
settings were added to refer to "static files" (images, CSS, JavaScript, etc.)
that are needed to render a complete web page:
[`STATIC_URL`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-STATIC_URL) and
[`STATIC_ROOT`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-STATIC_ROOT).

To find the appropriate prefix to use, Django will check if the
[`STATIC_URL`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-STATIC_URL) setting is not `None` and automatically fall back to
using [`MEDIA_URL`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-MEDIA_URL). For example, if the
[`MEDIA_URL`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-MEDIA_URL) for your site was `'http://uploads.example.com/'` and
[`STATIC_URL`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-STATIC_URL) was `None`:

    
    
    >>> from django import forms
    >>> class CalendarWidget(forms.TextInput):
    ...     class Media:
    ...         css = {
    ...             'all': ('/css/pretty.css',),
    ...         }
    ...         js = ('animations.js', 'http://othersite.com/actions.js')
    
    >>> w = CalendarWidget()
    >>> print(w.media)
    <link href="/css/pretty.css" type="text/css" media="all" rel="stylesheet" />
    <script type="text/javascript" src="http://uploads.example.com/animations.js"></script>
    <script type="text/javascript" src="http://othersite.com/actions.js"></script>
    

But if [`STATIC_URL`](https://docs.djangoproject.com/en/1.9/ref/settings/#std:setting-STATIC_URL) is `'http://static.example.com/'`:

    
    
    >>> w = CalendarWidget()
    >>> print(w.media)
    <link href="/css/pretty.css" type="text/css" media="all" rel="stylesheet" />
    <script type="text/javascript" src="http://static.example.com/animations.js"></script>
    <script type="text/javascript" src="http://othersite.com/actions.js"></script>
    

## `Media`对象

When you interrogate the `media` attribute of a widget or form, the value that
is returned is a `forms.Media` object. As we have already seen, the string
representation of a `Media` object is the HTML required to include the
relevant files in the `<head>` block of your HTML page.

However, `Media` objects have some other interesting properties.

### 资产子集

If you only want files of a particular type, you can use the subscript
operator to filter out a medium of interest. For example:

    
    
    >>> w = CalendarWidget()
    >>> print(w.media)
    <link href="http://static.example.com/pretty.css" type="text/css" media="all" rel="stylesheet" />
    <script type="text/javascript" src="http://static.example.com/animations.js"></script>
    <script type="text/javascript" src="http://static.example.com/actions.js"></script>
    
    >>> print(w.media['css'])
    <link href="http://static.example.com/pretty.css" type="text/css" media="all" rel="stylesheet" />
    

When you use the subscript operator, the value that is returned is a new
`Media` object - but one that only contains the media of interest.

### 组合`Media`对象

`Media` objects can also be added together. When two `Media` objects are
added, the resulting `Media` object contains the union of the assets specified
by both:

    
    
    >>> from django import forms
    >>> class CalendarWidget(forms.TextInput):
    ...     class Media:
    ...         css = {
    ...             'all': ('pretty.css',)
    ...         }
    ...         js = ('animations.js', 'actions.js')
    
    >>> class OtherWidget(forms.TextInput):
    ...     class Media:
    ...         js = ('whizbang.js',)
    
    >>> w1 = CalendarWidget()
    >>> w2 = OtherWidget()
    >>> print(w1.media + w2.media)
    <link href="http://static.example.com/pretty.css" type="text/css" media="all" rel="stylesheet" />
    <script type="text/javascript" src="http://static.example.com/animations.js"></script>
    <script type="text/javascript" src="http://static.example.com/actions.js"></script>
    <script type="text/javascript" src="http://static.example.com/whizbang.js"></script>
    

## 表单中的`Media`

Widget并不是唯一具有`media`定义的对象，表单也可以定义`media`。表单上的`media`定义规则与widget一致：声明可以是静态的，也可以是动态的；对于这些声明，路径和继承规则也是完全一样的。

无论你是否定义了`media`声明，所有的Form对象都有一个`media`属性。该属性的默认值是为表单中所有的widget添加`media`声明的结果：
    
    
    >>> from django import forms
    >>> class ContactForm(forms.Form):
    ...     date = DateField(widget=CalendarWidget)
    ...     name = CharField(max_length=40, widget=OtherWidget)
    
    >>> f = ContactForm()
    >>> f.media
    <link href="http://static.example.com/pretty.css" type="text/css" media="all" rel="stylesheet" />
    <script type="text/javascript" src="http://static.example.com/animations.js"></script>
    <script type="text/javascript" src="http://static.example.com/actions.js"></script>
    <script type="text/javascript" src="http://static.example.com/whizbang.js"></script>
    

如果你想要将额外的资产关联到一个表单上，例如，该表单布局的CSS，简单添加一个`Media`声明到该表单即可：
    
    
    >>> class ContactForm(forms.Form):
    ...     date = DateField(widget=CalendarWidget)
    ...     name = CharField(max_length=40, widget=OtherWidget)
    ...
    ...     class Media:
    ...         css = {
    ...             'all': ('layout.css',)
    ...         }
    
    >>> f = ContactForm()
    >>> f.media
    <link href="http://static.example.com/pretty.css" type="text/css" media="all" rel="stylesheet" />
    <link href="http://static.example.com/layout.css" type="text/css" media="all" rel="stylesheet" />
    <script type="text/javascript" src="http://static.example.com/animations.js"></script>
    <script type="text/javascript" src="http://static.example.com/actions.js"></script>
    <script type="text/javascript" src="http://static.example.com/whizbang.js"></script>
    
