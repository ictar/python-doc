原文：[How to Optimize Images for Page Load Speed in Django](https://worthwhile.com/blog/2016/07/11/django-page-load-speed/)

---

由于随着时间的推移，页面大小一直激增，让网站快速加载成了持久战。回到2011年，我们处理的网站平均大小是700KB，这在当时已经算是有点极端了。

现在，我们处理的网站大小通常是2MB或者更高。[那是Doom的大小](http://www.wired.com/2016/04/average-webpage-now-size-original-doom/)，也是90年代中期一个视频游戏的大小。

大部分这种膨胀的主要驱动力是图像。在2011年6月，平均每个网站有480KB张图，而[现在平均是1.4MB](http://httparchive.org/trends.php?s=All&minlabel=Jun+15+2011&maxlabel=Jun+15+2016)。
  
![](https://s3.amazonaws.com/twc-worthwhile/uploads/zinnia/2016/07/11/graph.png)

显然，图片是主要的性能杀手。但是客户端及其用户通常要求的体验，迫使软件开发者拿出越来越聪明的方法来处理这个问题。

你可以使用3种不同的方法来解决这个问题。让我们来看一看。

#### 使用Sorl在服务器端动态调整图像大小

当你的网站提供了多张图片时，最容易实现的目标是压缩图片大小，特别是当你的应用允许用户上传图片的时候。用户很少会为显示优化图片。诚然，我们也不应该要求他们这样做。这是用户使用我们的软件需要客户的另一个障碍。因此，当我们要显示那个媒体文件的时候，我们很少想要以我们能够得到的最大分辨率来展示。

例如，当用户上传一个6016 x 3376，大小为7MB的图片时，你真的不应该将相同的分辨率提供给任何用户。这是因为，[根据w3 schools](http://www.w3schools.com/browsers/browsers_resolution_higher.asp)，1%的市场中，你可能会获得的最高的普通大小是2560x1440。实际上，允许的最大大小应该是1920x1080，因为[那个浏览拥有最大的市场份额 (18%)](http://www.w3schools.com/browsers/browsers_display.asp)。

对于Django社区来说，用于减少图像大小的一个流行包是[Sorl-thumbnail](https://github.com/mariocesar/sorl-thumbnail)。它生成，然后在服务器端缓存一个合适大小的图像。

##### 安装设置

You can get the code for the latest stable release using 你可以使用'pip'来获取最新稳定。(像往常一样，对于一个真正的项目，一定要使用[requirements文件](https://devcenter.heroku.com/articles/python-pip)和[virtualenv](http://docs.python-guide.org/en/latest/dev/virtualenvs/)，这一般是好的python实践。)

`$ pip install sorl-thumbnail`

然后，找到配置文件，然后在'INSTALLED_APPS'中注册'sorl.thumbnail'。

```python
INSTALLED_APPS = (  
    ...  
   'sorl.thumbnail',  
)
```

为了获得最佳性能，你真的应该使用sorl中的ImageField，因为当你删除原来的主图像时，它会自动删除相关的缓存图像。但是，如果你选择跳过它，仍能使用主要功能

```python
from django.db import models  
from sorl.thumbnail import ImageField  
  
class Thing(models.Model):  
   image = ImageField(upload_to='thing')
```

##### 添加到模板

使用sorl的模板标签是极为方便的。对于实现，你有多种选择，但最简单的方法是慢慢等待，直到有人访问了一个页面。

一旦页面被访问，它就会为该页面生成一次图像，除非你清除缓存，否则它将一直保有。因此，你需要加载合适的模板标签，以获得这种便利。

对于以下实现，让我们使用以下的一组假设：

1\. 在项目目录中，你创建了一个基本的模板，用来处理所有的样板HTML (如Title, Head, Body标签)。按惯例，我们称其为`base.html`。

2\. 有一个名为`recipes`的Django应用。

3\. 在recipes应用目录下的合适子目录中，有一个Detail模板 (例如，`recipes/templates/recipes/detail.html`)。

4\. 也假设该模板通过一个Detail View进行加载，这个视图添加`recipe`变量到它的上下文，该变量有一个`image`属性，这个属性是一个ImageField对象。

5\. 最后，对于所有这些实现，假设你通过使用100%视口来处理英雄般的图像。对于我们而言，基于我们已经看到的浏览统计，将其宽限制在1920px。

##### 基本（反模式）实现

以下基本方法是性能最糟糕的，我会将其当成一个反模式。但它正好类似于Sorl文档中如何使用Sorl库的方法：<http://sorl-
thumbnail.readthedocs.io/en/latest/examples.html#template-examples>

recipes/templates/recipes/detail.html

```html
{% extends 'base.html' %}  
{% load thumbnail %}  
  
{% block content %}  
<img src="{% thumbnail recipe.image '1920' as im %}{{ im.url }}{% endthumbnail
%}" />  
{% endblock content %}
```

再次，虽然这个特定的方法的实现微不足道，但是它有一个巨大的缺点：只处理最大显示尺寸。结果是，对于不需要进行大文件加载的手机极其糟糕。（我看着你，iPhone和你的小屏幕。）

这种方法聊胜于无，但老实说，你不应该用它，除非时间真的很重要。有一种更好的方式，让我们开始吧。

##### 使用Srcset和Sizes响应执行

Srcset和Sizes方法是一种棒棒的方式，它让浏览器自己选择想要的的。它相对容易实现，但如果你开始看它的实现，不知道为什么我不使用Picture元素来对媒体查询进行增量控制，那么我推荐你读一读Eric
Portis写的[Srcset和大小一文](http://ericportis.com/posts/2014/srcset-sizes/)，了解为什么你不应该那样做。

为了你能简单实用，我已经展示了几种流行的CSS框架(例如，Bootstrap 3&4, Foundation 6, 和Material Design Lite)。然而，这种一般的方法可以很容易的修改以适用于你使用的任何框架。只要找到合适的断点兵处理最大尺寸即可。

需要注意的是，无关框架，对于src上的所有图像，我回到1920px。这样做是因为我相信，你的回退将最有可能发生在使用老的浏览器，但是有一个足够的互联网连接的桌面用户上。所以，我回到一个高分辨率图像。如果对于你的用户群，你不同意这种做法，那么你可以选择任意对你有意义的。

**附注：在写这篇文章的时候，所有浏览器的最新版本都支持该CSS属性，[除了IE11及后版本。](http://caniuse.com/#search=srcset)如果你必须考虑哪些不支持这个属性的浏览器，而你真的想要优化它，那么读一读下面“那些不支持Srcset的浏览器怎么办？”这一节。

###### [Bootstrap 4](http://v4-alpha.getbootstrap.com/)默认断点

该框架在544px, 768px, 992px,和1024px使用4个断点。

recipes/templates/recipes/detail.html

```html
{% extends 'base.html' %}  
{% load thumbnail %}  
  
{% block content %}  
  
<img src="{% thumbnail recipe.image '1920' as im %}{{ im.url }}{% endthumbnail
%}"  
    srcset="  
        {% thumbnail recipe.image '544' as im %}  {{ im.url }} {{ im.x }}w{%
endthumbnail %},  
        {% thumbnail recipe.image '768' as im %}  {{ im.url }} {{ im.x }}w{%
endthumbnail %},  
        {% thumbnail recipe.image '992' as im %}  {{ im.url }} {{ im.x }}w{%
endthumbnail %},  
        {% thumbnail recipe.image '1200' as im %}  {{ im.url }} {{ im.x }}w{%
endthumbnail %},  
        {% thumbnail recipe.image '1920' as im %}  {{ im.url }} {{ im.x }}w{%
endthumbnail %}"  
    alt="Some awesome soup"  
    sizes="100vw"  
/>  
  
{% endblock content %}
```

###### [Bootstrap 3](http://getbootstrap.com/)默认断点

该框架在768px, 992px, 和1024px使用3个断点。

recipes/templates/recipes/detail.html

```python
{% extends 'base.html' %}  
{% load thumbnail %}  
  
{% block content %}  
  
<img src="{% thumbnail recipe.image '1920' as im %}{{ im.url }}{% endthumbnail
%}"  
    srcset="  
        {% thumbnail recipe.image '768' as im %}  {{ im.url }} {{ im.x }}w{%
endthumbnail %},  
        {% thumbnail recipe.image '992' as im %}  {{ im.url }} {{ im.x }}w{%
endthumbnail %},  
        {% thumbnail recipe.image '1200' as im %}  {{ im.url }} {{ im.x }}w{%
endthumbnail %},  
        {% thumbnail recipe.image '1920' as im %}  {{ im.url }} {{ im.x }}w{%
endthumbnail %}"  
    alt="Some awesome soup"  
    sizes="100vw"  
/>  
  
{% endblock content %}
```

###### [Foundation 6](http://foundation.zurb.com/sites.html)默认断点

该框架在640px和1024px使用2个断点。

recipes/templates/recipes/detail.html

```html
{% extends 'base.html' %}  
{% load thumbnail %}  
  
{% block content %}  
  
<img src="{% thumbnail recipe.image '1920' as im %}{{ im.url }}{% endthumbnail
%}"  
    srcset="  
        {% thumbnail recipe.image '640' as im %}  {{ im.url }} {{ im.x }}w{%
endthumbnail %},  
        {% thumbnail recipe.image '1024' as im %}  {{ im.url }} {{ im.x }}w{%
endthumbnail %},  
        {% thumbnail recipe.image '1920' as im %}  {{ im.url }} {{ im.x }}w{%
endthumbnail %}"  
    alt="Some awesome soup"  
    sizes="100vw"  
/>  
  
{% endblock content %}
```

###### [Material Design Lite (MDL)](https://getmdl.io/)默认断点

该框架在480px和840px使用2个断点。

recipes/templates/recipes/detail.html

```python
{% extends 'base.html' %}  
{% load thumbnail %}  
  
{% block content %}  
  
<img src="{% thumbnail recipe.image '1920' as im %}{{ im.url }}{% endthumbnail
%}"  
    srcset="  
        {% thumbnail recipe.image '480' as im %}  {{ im.url }} {{ im.x }}w{%
endthumbnail %},  
        {% thumbnail recipe.image '840' as im %}  {{ im.url }} {{ im.x }}w{%
endthumbnail %},  
        {% thumbnail recipe.image '1920' as im %}  {{ im.url }} {{ im.x }}w{%
endthumbnail %}"  
    alt="Some awesome soup"  
    sizes="100vw"  
/>  
  
{% endblock content %}
```

##### 使用Picture元素进行响应式实现

所以我之前说你可能不希望使用这种方法。但是，既然你在阅读这部分，那么我希望你真的想知道如何实现Picture元素来代替。

为了减轻你的好奇心，这里是使用Bootstrap 4断点一些示例代码：

recipes/templates/recipes/detail.html

```html
{% extends 'base.html' %}  
{% load thumbnail %}  
  
{% block content %}  
  
<picture>  
    <source {% thumbnail recipe.image '544' as im %}  
        srcset="{{ im.url }}”  
        media="(max-width:{{ im.x }}px)"  
    {% endthumbnail %}>  
    <source {% thumbnail recipe.image '768' as im %}  
        srcset="{{ im.url }}"  
        media="(min-width:545px)"  
    {% endthumbnail %}>  
    <source {% thumbnail recipe.image '992' as im %}  
        srcset="{{ im.url }}"  
        media="(min-width:769px)"  
    {% endthumbnail %}>  
    <source {% thumbnail recipe.image '1200' as im %}  
        srcset="{{ im.url }}"  
        media="(min-width:993px)"  
    {% endthumbnail %}>  
    <source {% thumbnail recipe.image '1920' as im %}  
        srcset="{{ im.url }}"  
        media="(min-width:1201px)"  
    {% endthumbnail %}>  
    <img srcset="{% thumbnail recipe.image '1920' as im %}{{ im.url }}{%
endthumbnail %}"  
        alt="Some awesome soup">  
</picture>  
  
{% endblock content %}
```

这种方法的缺点是：

1\. 对于相同的效果，它繁琐得多得多。

2\. 你要记住，为各种断点进行1px抵消。

3\. 如果你决定重构断点，这将极其脆弱。

当你实际上自适应你的显示图像时，你会选择这种方式。也就是说，你要在不同的断点肯定第选择不同的图片，并且你要特别挑选断点，而不是将其留给浏览器。


##### 那些不支持Srcset的浏览器怎么办？

如果你读到这里，你可能会生气，因为现在你知道你真的应该使用Srcset，但由于你需要针对IE10或者也许是较旧版本的Andr​​oid浏览器而不能使用。所以，你将要被诱于使用我认为是反模式的基本实现，因为你认为你没有选择。那么，这就是为你准备的。

使用诸如[Picturefill](http://scottjehl.github.io/picturefill/)这样的填充工具JavaScript库。你可以在那里读一读文档，但是这里是直接从他们的文档中找到的实现：

base.html

```html
<head>  
. . .  
 <script>  
   // Picture element HTML5 shiv  
   document.createElement( "picture" );  
 </script>  
 <script src="picturefill.js" async></script>  
</head>
```

如果添加一点，它就会工作。你的超级老的Firefox版本现在可以工作啦。

##### 背景图片呢？

不幸的是，当使用背景图片的时候，对于srcset并没有一个真正对应的选秀。因此，基于目前我已经告诉你的东西，你将不得不使用基本实现，并处理最大尺寸，和使用断点。

##### django-flexible-images呢？

如果你已经到Django社区寻求帮助了，那么你可能会看到[django-flexible-images](https://github.com/lewiscollard/django-flexible-images)。它看起来非常棒，因为它实现起来相对简单。

通过pip安装。

`$ pip install https://github.com/lewiscollard/django-flexible-images.git`

然后，找到你的配置文件，在'INSTALLED_APPS'中注册`storages`。

```python
INSTALLED_APPS = (  
    ...  
   'flexible_images',  
)
```

接着，添加js到基本模板中。

base.html

```html
<head>  
. . .  
<script type="text/javascript" src="{% static 'flexible-images/flexible-
images.js' %}"></script  
</head>
```

然后，在一个合适的模板中使用它。

recipes/templates/recipes/detail.html

```html
{% extends 'base.html' %}  
{% load flexible_images %}  
  
{% block content %}  
  
{% flexible_image recipe.image alt="Some awesome soup" %}  
  
{% endblock content %}
```

如果你仅是将其与sorl进行对比，那么它会短得多，因为它在后台为一堆使用`FLEXIBLE_IMAGE_SIZES`设置的不同大小自动生成，如果你喜欢的话，可以覆盖你的设置。

在这一切之上，它处理背景图片，然后自动生成你所需要的所有大小。这是相当聪明的。

那么，为什么一开始我没用呢？这有些缺点：

* 该项目开始不到一年，并且它只有一个小的用户群 (Github上包括我只有4个star)

* 只在Django 1.8中测试过

* 对于Python或者JavaScript没有单元测试

* 基本上，它是对Sorl的一个非常薄的封装。比之Python，更多的是JavaScript代码。

* 对于如何在HTML上实现超级固执己见。

因此，虽然它超级酷，但是，我不能完全赞同它作为你现在应该做的事情。

##### 关于Sorl的结论

如果你实现了我上面说的任意一种方式，那么至少应该看看，当你的浏览器加载图片的时候，他们的大小将会更合理。对于你的页面速度，这应该有显著改善。

#### 使用AWS S3，以获取更好的响应时间和缓存

在[AWS](https://en.wikipedia.org/wiki/Amazon_Web_Services)上实现任何东西应该有它自身提供。我只会告诉你你应该怎么做，而不是为该方法提供大量的解释和防范。

使用S3，我们的目标是比你从自身服务器更快的传递文件，并在用户机器上尽可能长的缓存图像。

##### 安装AWS

遵循以下几个步骤：

* 在[https://aws.amazon.com](https://aws.amazon.com/)上创建或登录你的aws账户。

* 点击左边的S3。

* 点击“Create Bucket.”

* 对于bucket名字，为你的项目输入合适的名字。对于区域，选择“US Standard.”

* 创建bucket之后，点击左上角的Properties按钮。

* 展开“Permissions”，并点击“Add CORS Configuration."

* 粘贴以下：

```html
<?xml version="1.0" encoding="UTF-8"?>  
<CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">  
    <CORSRule>  
        <AllowedOrigin>*</AllowedOrigin>  
        <AllowedMethod>GET</AllowedMethod>  
    </CORSRule>  
</CORSConfiguration>
```

* 点击Save按钮。

##### Django中的安装配置

我们将使用[django-storages](https://github.com/jschneier/django-storages)来帮助我们处理到刚刚创建的这个S3 bucket的连接。对于连接，有一个不错的[boto](https://github.com/boto/boto)封装。所以，还要安装它。

`$ pip install django-storages boto`

然后，找到你的配置文件，然后在'INSTALLED_APPS'中注册`storages`。

```python
INSTALLED_APPS = (  
    ...  
   'storages',  
)
```

在相同的配置文件中添加一个配置部分，它看起来像这样：

```python
# ######### AMAZON S3 CONFIGURATION  
  
DEFAULT_FILE_STORAGE = 'storages.backends.s3boto.S3BotoStorage'  
AWS_ACCESS_KEY_ID = ''  # TODO: enter aws access key here  
AWS_SECRET_ACCESS_KEY = ''  # TODO: enter aws secret key here  
AWS_STORAGE_BUCKET_NAME = ''  # TODO: enter aws bucket name here (note: it
must be all lowercase)  
AWS_S3_FILE_OVERWRITE = False  # have this set to false if you never have to
worry about updating files with the same name; it just makes it easier  
AWS_QUERYSTRING_AUTH = False  
AWS_HEADERS = {  # see
http://developer.yahoo.com/performance/rules.html#expires  
   'Expires': 'Thu, 31 Dec 2099 20:00:00 GMT',  
   'Cache-Control': 'max-age=94608000',  
}  
  
# ######### END AMAZON S3 CONFIGURATION
```

你在上面看到的配置的每一部分对于让你的S3存储工作都至关重要。(关于使用AWS_QUERYSTRING_AUTH的细节，[阅读AWS文档](http://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-uthenticating-requests.html)。然而，对于AWS_HEADERS配置设置，该常量对于提高性能很重要。如果你的内容大多数是静态的，并且从不更新，那么你会想要设置Expires和Cache-Control值为一个相当遥远的时间，正如我上面写的那样。

##### 为了更好的分配使用AWS Cloudfront

老实说，这是最关键的一步，但如果你正在寻找挤出最后几毫秒的办法，Cloudfront将有助于完成最后一步，而不仅仅是S3从最近的CDN节点传输文件。

现在，让我们配置的Cloudfront。

* 访问<https://console.aws.amazon.com/>。

* 点击左边的“Cloudfront”。(在S3下的右边。)

* 点击“Create Distribution.”

* 在Web选项下，点击“Get Started.”

* 你现在应该在第2步。

* 对于字段Origin Domain Name，输入bucket的名字。它应该为你自动完成正确的名字。

* 为Object Caching, Minimum TTL,  Maximum TTL, 和Default TTL自定义字段，赋值为以秒为单位的长周期。这些将会控制Cloudfront检查S3，看看你的图像是否更新了的频率，对我们来说应该是永远不会。所以如果你愿意，可以设置它们为真正高的值，例如数周。

* 最后，点击右下方的“Create Distribution”。

#### 总结

如果你已经实现了所有这三个建议，那么这里是你应该看看的总结：

1\. 使用Sorl，你会创建多种图像分辨率，缓存它们，然后让浏览器选择它所需的一个，这样的话，对于最终用户，图像就不用必须那么大。

2\. 使用S3，你的应用服务器就不再需要负责渲染页面和查询，而是将文件发送给AWS S3，其中，每个文件都会有相应的缓存设置以及最大TTL。

3\. 使用Cloudfront，你会分布存储在S3上的图像，这样，用户就可以从最近因此最快的AWS CDN节点获取图像。你应该为那些大大下降的资源等待数毫秒连接。

上面所述的这些改进作用是数量级的。因此，如果你需要考虑当务之急是什么，那么顺着列表来即可。

当你完成这些步骤后，你会看到你的页面加载越来越快。这让你的用户和搜索引擎感到高兴，当然，从而也会让你高兴。
