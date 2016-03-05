原文：[The origins of the class Meta idiom in python](http://mapleoin.github.io/perma/python-class-meta)


So I keep finding this `class Meta` idiom in python APIs lately. Found it in [factory-boy](http://factoryboy.readthedocs.org/en/latest/index.html) and [WTForms](https://wtforms.readthedocs.org/en/latest/meta.html) and I suspected they both got it from [Django](https://docs.djangoproject.com/en/1.9/topics/db/models/#meta-options), but I googled and couldn’t find any explanations of the reason for it or where it came from or why they’re all it `class Meta`. So here it is!

#### TL;DR What it is

The inner `Meta` class has absolutely no relation to python’s [metaclasses](https://www.python.org/doc/essays/metaclasses/). The name is just a coincidence of history (as you can read below).

There’s nothing magical about this syntax at all, here’s an example from Django’s documentation:
```py
class Ox(models.Model):
    horn_length = models.IntegerField()
    class Meta:
        ordering = ["horn_length"]
        verbose_name_plural = "oxen"
```

Having an inner `Meta` class makes it easier for both the users and the ORM to tell what is a field on the model and what is just other information (or metadata if you like) about the model. The ORM can simply do `your_model.pop('Meta')` to retrieve the information it needs. You can also do this in any library you implement just as `factory-boy` and `WTForms` have done.

#### Some early Django history

Now for the longer story. I did some software archaeology, which should totally be a thing!, and discovered the first commit which mentions `class Meta` (actually `class META`[1](#fnaf4ee8d80aa04674b5bd7d121b1a5a8e)) in Django: [commit 25264c86](https://github.com/django/django/commit/25264c86048d442a4885dfebae94510e2fa0c1e4). There is a [Release Notes wiki page](https://code.djangoproject.com/wiki/ModelSyntaxChangeInstructions) which includes that change.

From there we can see how Django models were declared before the introduction of the internal `class Meta`. A Django Model class had a few special attributes. The `db_table` attribute held the SQL table name. A `fields` attribute was a tuple (!) of instances of field types (e.g. `CharField`, `IntegerField`, `ForeignKey`). These mapped to SQL table columns. One other interesting attribute was `admin` which was mostly used to describe how that model would behave in django’s admin interface. Now all these classes were defined in the `django.core.meta` package i.e. `meta.Model`, `meta.CharField`, `meta.ForeignKey`, `meta.Admin`. _That’s so meta!_ (and probably where the name came from in the end)

In a `django-developers` mailing list thread from July 2005 titled [Cleaner approach to ORM fields description](https://groups.google.com/forum/#%21msg/django-developers/IZdH8K8IbLA/AIJGlt8d3icJ) user `deelan` suggests bringing some of [SQLObject](https://en.wikipedia.org/wiki/SQLObject) ‘s ideas to Django’s ORM. This seems to be the first seed of the idea of having an inner class in Django to store part of a model’s attributes:

    > it’s desiderable to avoid name clashes between fields, so it would be
> 
> good to have a way to wrap fields into a private namespace.
> 
> — `deelan`, [Cleaner approach to ORM fields description](https://groups.google.com/forum/#%21msg/django-developers/IZdH8K8IbLA/AIJGlt8d3icJ)

At the end of the thread, [django ticket 122](https://code.djangoproject.com/ticket/122) is created which seems to contain the first mention of a separate internal `Meta` class.

What started off as a backwards-compatible change, soon turned backwards-incompatible and was "the first really big community-driven improvement to Django" as Adrian Holovaty will later describe it in the release announcement which included the change.

The first patch on [ticket 122](:https://code.djangoproject.com/ticket/122) by Matthew Marshall started by suggesting that fields should be able to be defined directly on the model class, as class attributes (_Build models using fieldname=FieldClass_) rather than in the `fields` list. So:
```py
class Poll(meta.Model):
    question = meta.CharField(maxlength=200)
    pub_date = meta.DateTimeField('date published')
```

rather than:
```py
class Poll(meta.Model):
    fields = (
        meta.CharField(maxlength=200),
        pub_date = meta.DateTimeField('date published'),
    )
```

But also that there should be two ways of defining a `ForeignKey`:
```py
ForeignKey = Poll, {'edit_inline':True, 'num_in_admin':3}
.
#the attribute name is irrelevant here:
anything = ForeignKey(Poll, edit_inline=True, num_in_admin=3)
```

In his first comment, `mmarshall` introduces the inner `class Meta` to hold anything that’s not a field: the table name (strangely renamed to `module_name`) and the admin options. The fields would be class attributes.

The decision over what goes in an inner class and what goes in the outer class seems to be left to the user. An optional `class Field` inner class would be supported so the fields would live there and the metadata would live as class attributes (this seemed to offer the advantage of being backwards-compatible with the `admin` class attribute while allowing tables to have a column that’s also named `admin`.

There are some other ideas thrown around and the syntax for `ForeignKey` is also discussed. At one point, Adrian Holovaty (`adrian`) intervenes to say (about the original `class Meta/class Field` suggestion):

    > It’s too flexible, to the point of confusion. Making it possible to do either class Meta or class Field or plain class attributes just smacks of “there’s more than one way to do it.” There should be one, clear, obvious way to do it. If we decide to change model syntax, let’s have class Meta for non-field info, and all fields are just attributes of the class.
> 
> — Adrian Holovaty, [django ticket 122, comment 9](https://code.djangoproject.com/ticket/122#comment%3A9)

The thread goes on from there. There are some detractors to the idea (citing performance and conformance to other python APIs), there are discussions about implementation details and talking again about the `ForeignKey` syntax.

Then, in a dramatic turn of events, Adrian Holovaty **closes the ticket as wontfix!**:

    > Jacob [Kaplan-Moss] and I have talked this over at length, and we’ve decided the model syntax shouldn’t change. Using a fieldname=FieldClass syntax would require too much “magic” behind the scenes for minimal benefit.
> 
> — Adrian Holovaty, [django ticket 122, comment 33](https://code.djangoproject.com/ticket/122#comment%3A33)

It’s interesting, because IMHO this was a huge differentiator in terms of making django’s models API more human and was also what other frameworks like Rails and SQLObject were doing at the time.

An [IRC discussion](https://code.djangoproject.com/attachment/ticket/122/modeldiscuss_cleanedup.txt) is then referenced in the ticket.[2](#fn34b8a5e31ddd4cd4b540e944f5f3f4b7) From that discussion, it seems that `adrian`’s reasons for closing were mostly concerns about the `ForeignKey` syntax and making a backwards-incompatible change to the model. `rmunn` does a great job of moderating the discussion, clarifying the situation and everyone’s opinions while strongly pushing for the new syntax.

The trac ticket is reopened as a consequence and it looks like smooth-sailing from the on. Some days later the new syntax is merged and the ticket is once again closed, this time with _Resolution_ set to _fixed_.

Adrian will later announce the change in a `django-developers` mailing list post. Here are some interesting fragments from that mailing list post:

    > I apologize for the backwards-incompatibility, but this is still unofficial software. ;-) Once we reach 1.0 — which is much closer now that the model syntax is changed — we’ll be very dedicated to backwards-compatibility.
> 
> I can’t think of any other backwards-incompatible changes that we’re planning before 1.0 (knock on wood). If this isn’t the last one, though, it’s at least the last **major** one.
> 
> — Adrian Holovaty, [IMPORTANT: Django model syntax is changing](https://groups.google.com/d/msg/django-developers/z_eGMWTJBqk/Fa3xur7J0jwJ)

Things didn’t go as planned. In May 2006, came [commit f69cf70e](https://github.com/django/django/commit/f69cf70ed813a8cd7e1f963a14ae39103e8d5265) which was exactly another _let’s-change-everything-in-one-huge-branch_ commit which was released as part of Django **0.95**. As part of this API change, `class META` was renamed to `class Meta` (because it’s _easier on the eyes_). You can find the details on [RemovingTheMagic wiki page](https://code.djangoproject.com/wiki/RemovingTheMagic). It’s funny how in [ticket 122](https://code.djangoproject.com/ticket/122) all the comments use the `Meta` capitalization, except for the last person (who I guess submitted the patch) who uses `META`. There was some discussion, both in the ticket and on IRC, about it and a few people had concerns that users of Django would actually want to have a field called `Meta` in their models and the inner class name would clash with that.

#### That’s it. Almost…

Anyway, so that’s the end of the story of how Django got its `class Meta`. Now what if I told you that all of this had already happened more than one year before in the SQLObject project? Remember that first post to `django-developers` which said Django models should hold some of its attributes in a separate inner class like SQLObject already does?

In April 2004, Ian Bicking (creator of SQLObject) sent an email to the `sqlobject-discuss` mailing list:

    > There’s a bunch of metadata right now that is being stored in various instance variables, all ad hoc like, and with no introspective interfaces.  I’d like to consolidate these into a single object/class that is separated from the SQLObject class.  This way I don’t have to worry about name clashes, and I don’t feel like every added little interface will be polluting people’s classes.  (Though most of the public methods that are there now will remain methods of the SQLObject subclasses, just like they are now)  So I’m looking for feedback on how that should work.
> 
> — Ian Bicking, [Metadata container](https://sourceforge.net/p/sqlobject/mailman/message/7522562/)

His code example:
```py
class Contact(SQLObject):
     class sqlmeta(SQLObject.sqlmeta):
         table = 'contact_table'
         cacheInstances = False
     name = StringCol()
```

SQLObject’s community did not seem nearly as animated as Django’s. There were a couple of emails on the `sqlobject-discuss` mailing list from Ian Bicking which included the proposal and asked for feedback. I suspect some discussion happened through some other channels, but this community was neither as big nor as good at docummenting its functioning as Django. (And sourceforge’s interface to the mailing list archives and cvs logs does not make this easy to navigate).

A year later, Ian Bicking takes part in the `django-developers` mailinglist discussion where he makes some small syntax suggestions, but it does not seem that he made any other contributions to the design of this part of the Django models API.

#### Conclusion

As far as I could tell, Ian Bicking is the originator of the idea of storing metadata in a **metadata container** inner class. Although it was the Django project which settled on the `class Meta` name and popularised it outside of its own community.

Anyway, that’s the end of the story. To me, it shows just how awesome open source and the open internet can be. The fact that I was able to find all of this 11 years later, complete with the original source code, commit logs and all the discussion around the implementation on the issue tracker, mailing lists and IRC logs is just amazing community work and puts a tear in my eye.

Hope you’ve enjoyed the ride!

1 because in 2005, people were less soft-spoken

2 It’s very fun, you should read it. At some point someone’s cat catches a blue jay. And I think they meant it literally.
