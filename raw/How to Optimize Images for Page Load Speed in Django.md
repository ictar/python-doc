原文：[How to Optimize Images for Page Load Speed in Django](https://worthwhile.com/blog/2016/07/11/django-page-load-speed/)

---

Getting websites to load quickly is a consistent battle, as page sizes have
ballooned over time. Back in 2011, we were dealing with sites that were
averaging 700KB in size, and thought to be a bit extreme.

Now we’re dealing with sites that are routinely 2MB or higher. [这是Doom的大小](http://www.wired.com/2016/04/average-webpage-now-size-original-doom/), a video game from the mid 90s.

The main driver of most of this bloat is images. In June 2011, the average
site had 480KB of images, and [现在平均是1.4MB](http://httparchive.org/trends.php?s=All&minlabel=Jun+15+2011&maxlabel=Jun+15+2016).  
  
![](https://s3.amazonaws.com/twc-worthwhile/uploads/zinnia/2016/07/11/graph.png)

Clearly, images are the main performance killer. But clients and their users
routinely demand experiences that force software developers to come up with
increasingly clever approaches to handle this problem.

You can employ 3 different strategies to approach this problem. Let’s take a
walk through them.

#### Using Sorl to Dynamically Resize Images Server-Side

When your website serves up several images, the lowest hanging fruit is to
crunch down the image size, especially if your application allows users to
upload images. Users rarely optimize the images for display. Admittedly, we
shouldn’t ask them to either. That’s another barrier for them to overcome to
use our software. So when we go to display that media, we really don’t want to
display it at the maximum resolution we could get.

For instance, when a user uploads a 6016 x 3376 image that is 7MB, you really
shouldn’t serve that same resolution back to any user. That’s because the
highest common size you could possibly get is 2560x1440, 1% of market
[根据w3 schools](http://www.w3schools.com/browsers/browsers_resolution_higher.asp).
Realistically, your upper maximum ought to be 1920x1080, because [that browser has the most market share (18%)](http://www.w3schools.com/browsers/browsers_display.asp) of a large
size.

For the Django community, a popular package for reducing image size is [Sorl-thumbnail](https://github.com/mariocesar/sorl-thumbnail). It can help by
generating and then caching server side an appropriately sized image.

##### Installation and Settings Configuration

You can get the code for the latest stable release using 'pip'. (As always,
for a real project, be sure to use a [requirements file](https://devcenter.heroku.com/articles/python-pip) and
[virtualenv](http://docs.python-guide.org/en/latest/dev/virtualenvs/), which
are just good python practices in general.)

`$ pip install sorl-thumbnail`

Then go find your settings file and register 'sorl.thumbnail' in the
'INSTALLED_APPS'.

`INSTALLED_APPS = (  
    ...  
   'sorl.thumbnail',  
)`

For best performance, you really ought to use the ImageField from sorl because
it will auto-delete the related cached images when you delete the original
main image. However, if you choose to skip this, the main functionality will
work otherwise.

`from django.db import models  
from sorl.thumbnail import ImageField  
  
class Thing(models.Model):  
   image = ImageField(upload_to='thing')`

##### Add into Templates

Using sorl’s template tags is extremely convenient. You have several options
for implementation, but the simplest approach works by lazily waiting until
someone visits a page.

Once a page gets hit, it generates images once for that page, and unless you
purge the cache, it’ll keep it for all time. Consequently, you need to load
the appropriate template tag in order to get that convenience.

For the following implementations, let’s use the following set of assumptions:

1\. In the project directory, you created a base template that handles all the
boilerplate HTML (e.g. Title, Head, Body tags). We’ll call that one
`base.html` per the normal Django convention.

2\. You have a Django app called `recipes`.

3\. You have a Detail template in the appropriate subdirectory from inside the
recipes app directory (e.g. `recipes/templates/recipes/detail.html`).

4\. Let’s also say that this template gets loaded by a Detail View that adds
in a `recipe` variable into its context that holds a relationship to an object
with attribute called `image` which itself is an ImageField object.

5\. Finally, let’s assume for all these implementations that you’re dealing
with a hero-like image by using 100% viewport. For our purposes, we’re capping
this import at 1920px wide based on the Browser stats we’ve already looked at.

##### Basic (Anti-Pattern) Implementation

This following basic approach is the worst performer, and I would consider it
an anti-pattern. But  it happens to be similar to how the Sorl docs address
using their library: <http://sorl-thumbnail.readthedocs.io/en/latest/examples.html#template-examples>

recipes/templates/recipes/detail.html

`{% extends 'base.html' %}  
{% load thumbnail %}  
  
{% block content %}  
<img src="{% thumbnail recipe.image '1920' as im %}{{ im.url }}{% endthumbnail
%}" />  
{% endblock content %}`

Again, while this particular approach is trivial to implement, it has the huge
drawback of only handling the maximum display size. The consequence is that
mobile is negatively hit by large file loads that they simply do not need.
(I’m looking at you, iPhone and your tiny screens.)

This approach is better than nothing, but honestly you shouldn’t use this
approach unless time is really that important. There’s a better way, so let’s
get to it.

##### Responsive Implementation Using Srcset and Sizes

The Srcset and Sizes approach is a great way to let the browser pick what it
wants. It’s relatively simple to implement, but if you start reading through
the implementation and wondering why I don’t use the Picture element for
increased control with media queries, I recommend that you read [the Srcset and sizes article](http://ericportis.com/posts/2014/srcset-sizes/) by Eric
Portis on why you shouldn’t go down that path.

For your ease of drop-in use, I’ve demonstrated several popular CSS frameworks
(e.g., Bootstrap 3 &amp; 4, Foundation 6, and Material Design Lite). However,
this general approach can be easily modified for whatever framework you’re
using. Just find the appropriate breakpoints and handle the largest size.

Note that, regardless of framework, I’m falling back to 1920px for all the
images on src. I do this because I believe that most likely your fallback will
occur in a desktop user situation with an old browser but a decent enough
Internet connection. So I fall back to a hi-res image. If you don’t agree with
this being true about your user base, then by all means pick something that
makes sense to you.

**Side note: at time of this writing, this CSS attribute is supported by the most recent version of all browsers, [except IE11 and back. ](http://caniuse.com/#search=srcset) If you’re in a situation where you must target a browser that does not support this attribute, but you really need to optimize for that situation, then read the section below called “What do I do with Browsers that Don’t Support Srcset?”

###### [Bootstrap 4](http://v4-alpha.getbootstrap.com/) Default Breakpoints

This framework uses four breakpoints at 544px, 768px, 992px, and 1024px.

recipes/templates/recipes/detail.html

`{% extends 'base.html' %}  
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
  
{% endblock content %}`

###### [Bootstrap 3](http://getbootstrap.com/) Default Breakpoints

This framework uses three breakpoints at 768px, 992px, and 1024px.

recipes/templates/recipes/detail.html

`{% extends 'base.html' %}  
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
  
{% endblock content %}`

###### [Foundation 6](http://foundation.zurb.com/sites.html) Default
Breakpoints

This framework uses two breakpoints at 640px and 1024px.

recipes/templates/recipes/detail.html

`{% extends 'base.html' %}  
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
  
{% endblock content %}`

###### [Material Design Lite (MDL)](https://getmdl.io/) Default Breakpoints

This framework uses two breakpoints at 480px and 840px.

recipes/templates/recipes/detail.html

`{% extends 'base.html' %}  
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
  
{% endblock content %}`

##### Responsive Implementation Using Picture Element

So I previously said you probably don’t want to use this approach. But since
you’re reading this section, I expect you really want to know how to implement
the Picture element approach instead.

To assuage your curiosity, here’s some sample code using the Bootstrap 4
breakpoints:

recipes/templates/recipes/detail.html

`{% extends 'base.html' %}  
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
  
{% endblock content %}`

The negatives with this approach are:

1\. It’s far more verbose for the same effect.

2\. You have to remember to do the 1px offset for the various breakpoints.

3\. It’s extremely brittle if you decide to refactor your breakpoints.

You would choose this approach when you are actually being adaptive with the
image you’re displaying. That is, you want to definitely pick different
pictures at different breakpoints, and you want to specifically pick the
breakpoints instead of leaving it up to the browser.

##### What about Browsers that Don’t Support Srcset?

If you’ve read this far, you might be annoyed because now you know that you
really ought to use Srcset, but you can’t because you need to target IE10 or
an older version of the Android Browser perhaps. So you’re going to be tempted
to use the Basic Implementation that I consider an anti-pattern because you
think you have no choice. This is for you.

Use a polyfill JavaScript library like
[Picturefill](http://scottjehl.github.io/picturefill/). You should read the
docs over there, but here’s an implementation straight from their docs:

base.html

`<head>  
. . .  
 <script>  
   // Picture element HTML5 shiv  
   document.createElement( "picture" );  
 </script>  
 <script src="picturefill.js" async></script>  
</head>`

If you add that bit, it just works. Your super-old version of Firefox will now
be supported.

##### What about Background Images?

Unfortunately, there’s not really a corresponding option for srcset when using
a background image. So based on what I’ve told you so far, you’re going to
have to use the Basic Implementation and handle the max size, and use
breakpoints

##### What about django-flexible-images?

If you’ve looked around the Django community for solutions, you may have come
across [django-flexible-images](https://github.com/lewiscollard/django-flexible-images). It looks really awesome because it is relatively simple to
implement it:

Install it via pip.

`$ pip install https://github.com/lewiscollard/django-flexible-images.git`

Then find your settings file and register `storages` in the 'INSTALLED_APPS'.

`INSTALLED_APPS = (  
    ...  
   'flexible_images',  
)`

Then add the js to the base template.

base.html

`<head>  
. . .  
<script type="text/javascript" src="{% static 'flexible-images/flexible-
images.js' %}"></script  
</head>`

Then use it in the appropriate template.

recipes/templates/recipes/detail.html

`{% extends 'base.html' %}  
{% load flexible_images %}  
  
{% block content %}  
  
{% flexible_image recipe.image alt="Some awesome soup" %}  
  
{% endblock content %}`

If you compare the template implementation to just sorl, it’s far shorter
because it automatically generates everything in the background for a bunch of
different sizes using the `FLEXIBLE_IMAGE_SIZES` setting that you can override
on your settings if you like.

On top of all of this, it handles background images and auto generates all the
sizes you might need. It’s quite clever.

So why didn’t I lead with this? Here are some negatives:

* The project is less than a year old, and it has a small user base from what I can tell (4 stars on Github including mine)

* It has only been tested on Django 1.8

* There are no unit tests for the Python or JavaScript

* Basically it’s a very thin wrapper around Sorl. There’s more JavaScript than Python code.

* It’s super opinionated on how to implement your HTML

So while it’s super cool, I can’t fully endorse it as the way you ought to do
things right now.

##### Conclusion about Sorl

If you’ve implemented one of the approaches I’ve stated above, you should at
least see that your images will be far more reasonably sized when your browser
loads them. This should have a substantial improvement for your page speed.

#### Using AWS S3 for Better Response Time and Caching

Implementing anything on
[AWS](https://en.wikipedia.org/wiki/Amazon_Web_Services) could be a book in of
itself. Rather than providing a lot of explanation and defense of the
approach, I’m just going to show you what you ought to do.

With S3, our goal is to deliver the files faster than you could from your own
server and to keep the images cached as long as possible on the user’s
machine.

##### Setup on AWS

Follow the next few steps:

* Either create or login into your aws account at [https://aws.amazon.com](https://aws.amazon.com/).

* Click S3 at the left.

* Click “Create Bucket.”

* For the bucket name, enter the appropriate name for your project. For region, choose “US Standard.”

* After creating the bucket, click the Properties button at top left.

* Expand “Permissions” and click “Add CORS Configuration."

* Paste this in:

`<?xml version="1.0" encoding="UTF-8"?>  
<CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">  
    <CORSRule>  
        <AllowedOrigin>*</AllowedOrigin>  
        <AllowedMethod>GET</AllowedMethod>  
    </CORSRule>  
</CORSConfiguration>`

* Click the Save button.

##### Installation and Config in Django

We’re going to use `[django-storages](https://github.com/jschneier/django-storages)` to help us handle connecting to this S3 bucket we’ve just created.
It’s a nice wrapper around `[boto](https://github.com/boto/boto)` for
connecting. So install that too.

`$ pip install django-storages boto`

Then find your settings file and register `storages` in the 'INSTALLED_APPS'.

`INSTALLED_APPS = (  
    ...  
   'storages',  
)`

Add to that same settings file a configuration section that looks like below:

`# ######### AMAZON S3 CONFIGURATION  
  
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
  
# ######### END AMAZON S3 CONFIGURATION`

Everything on this configuration you see above is important in getting your S3
storage to work. (For details on using  AWS_QUERYSTRING_AUTH, [read the AWS docs](http://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-authenticating-requests.html). For the AWS_HEADERS configuration setting, however, that
constant is the important one for improving performance. If you’re in a
situation where your content is mostly static and never updates, you’ll want
to set the Expires and Cache-Control values to the really distant future, as I
have shown above.

##### Using AWS Cloudfront for Better Distribution

Honestly, this is the least critical step, but if you’re looking to squeeze
out the final few milliseconds, Cloudfront will help to travel the last mile
more than just S3 delivering the files from the closest CDN node will.

Now let’s config Cloudfront.

* Go to <https://console.aws.amazon.com/>.

* Click on “Cloudfront” on the left. (It’s right below S3.)

* Click “Create Distribution.”

* Under the Web option, click “Get Started.”

* You should now be on Step 2.

* For the field Origin Domain Name, start typing the name you gave for the bucket. It should autocomplete the correct one for you.

* Customize the fields for Object Caching, Minimum TTL,  Maximum TTL, and Default TTL, to long periods of time in seconds. All this will control is how often Cloudfront checks S3 to see if your images have updated, which for our purposes should be never. So if you want, you can set these to really high numbers like weeks.

* Finally, click “Create Distribution” on the bottom right.

#### Conclusion

If you’ve implemented all three of these recommendations, here are the
improvements you should see as a result:

1\. Using Sorl, you’re creating multiple image resolutions, caching them, and
then letting the browser pick which one it wants so that images are no larger
than they absolutely have to be for the end user.

2\. Using S3, you’re taking load off your application server that was trying
to render pages and do queries, and instead sending files to AWS S3 with cache
settings per file with the max TTL that we can rationalize.

3\. Using Cloudfront, you’re distributing the images you’ve stored on S3 so
that users will fetch the images from the closest and therefore fastest AWS
CDN node. You should see wait ms for connections to those resources drop
tremendously.

These improvements stated above are in order of the magnitude of their effect.
So if you’re needing to prioritize what you do first, just go down the list.

As you complete these steps, you’ll see your page load speeds getting faster
and faster. That makes your users and the search engines happy, which will
make you happy too.
