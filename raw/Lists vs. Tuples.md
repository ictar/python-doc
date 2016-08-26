[![\[*\]](http://nedbatchelder.com/dodeca3_100.gif)](http://nedbatchelder.com/
)| [Ned Batchelder](http://nedbatchelder.com/) :
[Blog](http://nedbatchelder.com/blog) | [Code](http://nedbatchelder.com/code)
| [Text](http://nedbatchelder.com/text) |
[Site](http://nedbatchelder.com/site)  
Lists vs. Tuples  
» [Home](http://nedbatchelder.com/) : [Blog](http://nedbatchelder.com/blog) :
[August 2016](http://nedbatchelder.com/blog/201608.html)  
---|---  
  
### [Lists vs.
Tuples](http://nedbatchelder.com/blog/201608/lists_vs_tuples.html "Link to
this post" )

Thursday 18 [August 2016](http://nedbatchelder.com/blog/201608.html)

A common beginner Python question: what's the difference between a list and a
tuple?

The answer is that there are two different differences, with complex interplay
between the two. There is the Technical Difference, and the Cultural
Difference.

First, the things that are the same: both lists and tuples are containers, a
sequence of objects:

> `>>> my_list = [1, 2, 3]  
>>> type(my_list)  
<class 'list'>  
>>> my_tuple = (1, 2, 3)  
>>> type(my_tuple)  
<class 'tuple'>  
`

Either can have elements of any type, even within a single sequence. Both
maintain the order of the elements (unlike sets and dicts).

Now for the differences. The Technical Difference between lists and tuples is
that lists are mutable (can be changed) and tuples are immutable (cannot be
changed). This is the only distinction that the Python language makes between
them:

> `>>> my_list[1] = "two"  
>>> my_list  
[1, 'two', 3]  
>>> my_tuple[1] = "two"  
Traceback (most recent call last):  
  File "<stdin>", line 1, in <module>  
TypeError: 'tuple' object does not support item assignment  
`

That's the only technical difference between lists and tuples, though it
manifests in a few ways. For example, lists have a .append() method to add
more elements to the list, while tuples do not:

> `>>> my_list.append("four")  
>>> my_list  
[1, 'two', 3, 'four']  
>>> my_tuple.append("four")  
Traceback (most recent call last):  
  File "<stdin>", line 1, in <module>  
AttributeError: 'tuple' object has no attribute 'append'  
`

Tuples have no need for an .append() method, because you can't modify tuples.

The Cultural Difference is about how lists and tuples are actually used: lists
are used where you have a homogenous sequence of unknown length; tuples are
used where you know the number of elements in advance because the position of
the element is semantically significant.

For example, suppose you have a function that looks in a directory for files
ending with *.py. It should return a list, because you don't know how many you
will find, and all of them are the same semantically: just another file that
you found.

> `>>> find_files("*.py")  
["control.py", "config.py", "cmdline.py", "backward.py"]  
`

On the other hand, let's say you need to store five values to represent the
location of weather observation stations: id, city, state, latitude, and
longitude. A tuple is right for this, rather than a list:

> `>>> denver = (44, "Denver", "CO", 40, 105)  
>>> denver[1]  
'Denver'  
`

(For the moment, let's not talk about using a class for this.) Here the first
element is the id, the second element is the city, and so on. The position
determines the meaning.

To put the Cultural Difference in terms of the C language, lists are like
arrays, tuples are like structs.

Python has a namedtuple facility that can make the meaning more explicit:

> `>>> from collections import namedtuple  
>>> Station = namedtuple("Station", "id, city, state, lat, long")  
>>> denver = Station(44, "Denver", "CO", 40, 105)  
>>> denver  
Station(id=44, city='Denver', state='CO', lat=40, long=105)  
>>> denver.city  
'Denver'  
>>> denver[1]  
'Denver'  
`

One clever summary of the Cultural Difference between tuples and lists is:
tuples are namedtuples without the names.

The Technical Difference and the Cultural Difference are an uneasy alliance,
because they are sometimes at odds. Why should homogenous sequences be
mutable, but hetergenous sequences not be? For example, I can't modify my
weather station because a namedtuple is a tuple, which is immutable:

> `>>> denver.lat = 39.7392  
Traceback (most recent call last):  
  File "<stdin>", line 1, in <module>  
AttributeError: can't set attribute  
`

And sometimes the Technical considerations override the Cultural
considerations. You cannot use a list as a dictionary key, because only
immutable values can be hashed, so only immutable values can be keys. To use a
list as a key, you can turn it into a tuple:

> `>>> d = {}  
>>> nums = [1, 2, 3]  
>>> d[nums] = "hello"  
Traceback (most recent call last):  
  File "<stdin>", line 1, in <module>  
TypeError: unhashable type: 'list'  
>>> d[tuple(nums)] = "hello"  
>>> d  
{(1, 2, 3): 'hello'}  
`

Another conflict between the Technical and the Cultural: there are places in
Python itself where a tuple is used when a list makes more sense. When you
define a function with *args, args is passed to you as a tuple, even though
the position of the values isn't significant, at least as far as Python knows.
You might say it's a tuple because you cannot change what you were passed, but
that's just valuing the Technical Difference over the Cultural.

I know, I know: in *args, the position could be significant because they are
positional parameters. But in a function that's accepting *args and passing it
along to another function, it's just a sequence of arguments, none different
from another, and the number of them can vary between invocations.

Python uses tuples here because they are a little more space-efficient than
lists. Lists are over-allocated to make appending faster. This shows Python's
pragmatic side: rather than quibble over the list/tuple semantics of *args,
just use the data structure that works best in this case.

For the most part, you should choose whether to use a list or a tuple based on
the Cultural Difference. Think about what your data means. If it can have
different lengths based on what your program encounters in the real world,
then it is probably a list. If you know when you write the code what the third
element means, then it is probably a tuple.

On the other hand, functional programming emphasizes immutable data structures
as a way to avoid side-effects that can make it difficult to reason about
code. If you are a functional programming fan, you will probably prefer tuples
for their immutability.

So: should you use a tuple or a list? The answer is: it's not always a simple
answer.

tagged: [python](http://nedbatchelder.com/blog/tag/python.html)» 21 reactions

# Comments

![\[gravatar\]](http://www.gravatar.com/avatar/523088431fcf9e52306a598c085c5ab
8.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa172.jpg&size=40
)

**SylvainDe** 12:01 PM on 18 Aug 2016

Once again, excellent article. I perfectly puts words on things I knew but
couldn't explain. I also quite like Raymond Hettinger's wording "Loopy lists
and structy tuples" ( <https://twitter.com/raymondh/status/324920924103122944>
).

![\[gravatar\]](http://www.gravatar.com/avatar/54a3f1ba4fe36ca8f3148c0c8a041d3
6.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa235.jpg&size=40
)

**Tim Arnold** 1:15 PM on 18 Aug 2016

Hi, I enjoy your articles and learn. I hardly ever use tuples, just out of
habit. I wonder what price I'm paying for always (nearly) using lists. The
only time I can remember using tuples is when I need it as a dictionary key.
So I understand what you're saying about the differences, but what is the
real-life penalty for not using tuples? Is it performance? thanks again.

![\[gravatar\]](http://www.gravatar.com/avatar/d0ea972cf6651315c11bff7dc0a5273
3.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa184.jpg&size=40
)

**intellimath** 2:07 PM on 18 Aug 2016

There is one difficulty in current python implementation of lists: if one
create list from another object with known size then the created list instance
allocates more memory than one need. This sometimes prevent using of lists as
mutable sequence with fixed size.

![\[gravatar\]](http://www.gravatar.com/avatar/75e9a11371cbe1566607180863efdf4
c.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa107.jpg&size=40
)

**[Ned Batchelder](http://nedbatchelder.com)** 2:14 PM on 18 Aug 2016

@SylvainDe: thanks for that, it's a good point that lists are for iterating
over, and tuples generally are not, although you can.

![\[gravatar\]](http://www.gravatar.com/avatar/75e9a11371cbe1566607180863efdf4
c.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa107.jpg&size=40
)

**[Ned Batchelder](http://nedbatchelder.com)** 2:14 PM on 18 Aug 2016

@intellimath: although lists are over-allocated, I'm not sure what prevents
you from using them?

![\[gravatar\]](http://www.gravatar.com/avatar/f59fb62df3f86e8a9c16d3d5b159570
5.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa275.jpg&size=40
)

**Christoph** 2:33 PM on 18 Aug 2016

In my undersanding, regarding the "cultural difference", whether the position
of the element is semantically significant or not makes the real difference,
while the homogeneity is only a weak indicator. For instance, you usually want
to implement 2D or 3D points in space as tuples, not as lists, even though
their components have all the same type.

![\[gravatar\]](http://www.gravatar.com/avatar/f59fb62df3f86e8a9c16d3d5b159570
5.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa275.jpg&size=40
)

**Christoph** 2:44 PM on 18 Aug 2016

Tim: Yes, tuples are a bit smaller and faster. Also, various optimizers might
use the information that something is immutable to make it even faster. Plus
the immutability protects you from accidentally changing something you don't
want to change.

![\[gravatar\]](http://www.gravatar.com/avatar/d0ea972cf6651315c11bff7dc0a5273
3.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa184.jpg&size=40
)

**intellimath** 5:29 PM on 18 Aug 2016

@Ned Batchelder: You right that nothing prevent to use lists. But it would be
nice if list instance created at first time (before any mutation) used memory
without over-allocation.

![\[gravatar\]](http://www.gravatar.com/avatar/f710fcfe1f162e7792a2f46d0cef72e
1.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa76.jpg&size=40)

**[Veky](http://vedgar.googlepages.com)** 10:02 AM on 19 Aug 2016

It's a common misconception that mutable objects cannot be keys to a
dictionary. The truth is, they are hashed, just not by value, but by identity.
I don't usually use them as keys directly, but often I have sets of instances
of my custom classes. It works perfectly fine. Only if you define __eq__,
Python turns off that behavior. (Also, not all tuples are hashable. But you
never claimed that.:)  
  
Also, lists and tuples are not the only containers. In your glob example, a
set would probably be a better option. Or, in Python you don't even have to
choose a container: write a generator and let your user decide how to store
the yielded items (and whether to store them at all).  
  
An additional point of difference: lists have a nice shortcut for
comprehension, tuples don't. In quick&amp;dirty; scripts, it's often a
deciding factor. :-)  
  
You mentioned starargs as an example of a tuple that should be a list. An
opposite example is probably the result of str.split: in most cases, you split
a string into fields, and you know which is which by index. Often I've wished
.split returned a tuple.  
  
And a technical nitpick: lists are naturally processed with for statement,
(same name referring to their elements at different times), while tuples are
naturally processed with unpacking (different names referring to their
elements at the same time). Both of them are "iteration" from the perspective
of Python (they use the iteration protocol).

![\[gravatar\]](http://www.gravatar.com/avatar/f59fb62df3f86e8a9c16d3d5b159570
5.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa275.jpg&size=40
)

**Christoph** 4:29 PM on 19 Aug 2016

Good point, Veky. Processing of lists with "for" and tuples by unpacking is
indeed characteristic for these types.  
  
Tuples don't have a nice shortcut for comprehension, but writing something
like tuple(i for i in range(3)) is good enough (it's actually a generator
compression, but since it is wrapped in a function, the extra parantheses can
be ommited). As you say, that kind of usage is untypical for tuples anyway so
they don't need their own syntax.

![\[gravatar\]](http://www.gravatar.com/avatar/8e21f12da8548e6528004e0e7e2f575
1.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa7.jpg&size=40)

**Barnaby Robson** 4:14 AM on 20 Aug 2016

Wait, why are you saying lists are homogenous ?  
  
>>> xs = [None, 1, set()]  
  
is perfectly valid of course ! This makes them different to C arrays.

![\[gravatar\]](http://www.gravatar.com/avatar/75e9a11371cbe1566607180863efdf4
c.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa107.jpg&size=40
)

**[Ned Batchelder](http://nedbatchelder.com)** 10:46 AM on 20 Aug 2016

@Veky: these are all excellent points. I don't quite agree that str.split()
"in most cases" is unpacked, I'd put it in a middle ground where there are
common cases for both looping and unpacking, a good demonstration of why
Python's pragmatism wins out over any strict separation of loopy from structy.

![\[gravatar\]](http://www.gravatar.com/avatar/75e9a11371cbe1566607180863efdf4
c.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa107.jpg&size=40
)

**[Ned Batchelder](http://nedbatchelder.com)** 10:48 AM on 20 Aug 2016

@Barnaby: keep in mind I put the homogeneity consideration under the Cultural
Difference. Of course lists can have different types, but it's unusual, and
indicates that you might want a tuple instead. I say "might" because your
different-typed values might still all be treated homogeneously. There are
other considerations than just type.

![\[gravatar\]](http://www.gravatar.com/avatar/e2985067153167abef1978f7d172ea4
0.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa62.jpg&size=40)

**[Chris Mullins](http://crmullins.com)** 6:26 PM on 20 Aug 2016

Great article. I love the way your writing is accessible to beginners, and
builds from fundamentals of not just Python but software engineering in
general. I'm trying to build my own talks this way. Thanks!

![\[gravatar\]](http://www.gravatar.com/avatar/77f31d3213baeb4922001e016e7af16
3.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa227.jpg&size=40
)

**[stuart](https://twitter.com/pyhxr)** 1:07 AM on 21 Aug 2016

Thank you for the beginner accessible article, I appreciated the part that
describes when and what you should use lists and tuples for, I found that most
valuable. The fact that tuples are more space efficient was interesting as
well.

![\[gravatar\]](http://www.gravatar.com/avatar/ab5768a962212c3b6249e50ecefe958
c.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa153.jpg&size=40
)

**rbistolfi** 11:44 AM on 22 Aug 2016

An additional point of difference: lists have a nice shortcut for
comprehension, tuples don't. In quick&amp;dirty; scripts, it's often a
deciding factor. :-)  
  
Generator expressions can be used with function calls nicely:

[code]

    
    >>> tuple(2**i for i in range(10))
    
[/code]

![\[gravatar\]](http://www.gravatar.com/avatar/f710fcfe1f162e7792a2f46d0cef72e
1.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa76.jpg&size=40)

**[Veky](http://vedgar.googlepages.com)** 8:46 PM on 23 Aug 2016

Trust me, I know all about generator expressions. I said "in quick&amp;dirty;
scripts", those 5 characters are a great weight. :-)

![\[gravatar\]](http://www.gravatar.com/avatar/b59b374097daf7b7e5e24fc6f7f4570
4.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa180.jpg&size=40
)

**[Neil Khristian Manuel](http://lasertekservices.com/)** 11:24 AM on 24 Aug 2016

Nice article, Perfect for python programming aspirants.

![\[gravatar\]](http://www.gravatar.com/avatar/fc9fd50bbca3479810ce0cd46dec252
3.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa93.jpg&size=40)

**Ajurna** 3:55 PM on 25 Aug 2016

one major caveat here is that if you use a list in default function parameter
you are going to have lots of problems with unpredictability.  
eg  
def process_list(data=[]):  
every time you run that function the list will be the same list from the start
of the program. also passing lists into functions doesnt copy it so can cause
more issues.  
  
more detail here <http://docs.python-guide.org/en/latest/writing/gotchas/>

![\[gravatar\]](http://www.gravatar.com/avatar/04841b44018d580c770862416e41dbb
b.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa28.jpg&size=40)

**Mihail Temelkov** 4:02 PM on 25 Aug 2016

I prefer using tuples over lists, unless I know I need a mutable collection.
They are faster, in fact they are the fastest collection type in terms of
creation time, and "safer" (smaller chance of having a side effect).

![\[gravatar\]](http://www.gravatar.com/avatar/3b1cca363ad60a8a837c9137d89c083
b.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa99.jpg&size=40)

**[Curtis Miller](http://ntguardian.wordpress.com)** 5:17 PM on 25 Aug 2016

Great post! I really like the distinction between "Technical" and "Cultural"
difference. I also never knew what a named tuple was; thanks for the exposure.

## Add a comment:

|

name

|  
---|---  
  
email

|

Ignore this:

not displayed and no spam.

Leave this empty:  
  
www

|  not searched.  
  |

Name and either email or www are required.  
  
Don't put anything here:

Leave this empty:  
  
URLs auto-link and some tags are allowed:
&lt;a&gt;&lt;b&gt;&lt;i&gt;&lt;p&gt;&lt;br&gt;&lt;pre&gt;.  
  
Email me future comments  
  
  * Search this site: | |   
---|---  
  * [About me](http://nedbatchelder.com/site/aboutned.html)
  * Also me: 
    * [twitter](http://twitter.com/nedbat) *
    * [email](mailto:ned@nedbatchelder.com)
  * Tip me: 
    * [bitcoin](https://coinbase.com/nedbat) *
    * [paypal](https://paypal.me/nedbat)
  * Commerce: 
    * » [Amazon](http://www.amazon.com/exec/obidos/redirect-home/nedbatchelder-20)
    * » [Susan's books](http://susansenator.com/survivalguide.html)  
[![Making Peace With Autism](http://nedbatchelder.com/pix/makingpeacetiny.png)
](http://susansenator.com/makingpeace.html) [![Dirt, a novel](http://nedbatche
lder.com/pix/dirttiny.png)](http://susansenator.com/dirt.html)

  * More blog: 
    * [python](http://nedbatchelder.com/blog/tag/python.html) *
    * [art](http://nedbatchelder.com/blog/tag/art.html) *
    * [16](http://nedbatchelder.com/blog/archive2016.html) *
    * [funny](http://nedbatchelder.com/blog/tag/funny.html) *
    * [web](http://nedbatchelder.com/blog/tag/webpage.html) *
    * [15](http://nedbatchelder.com/blog/archive2015.html) *
    * [tools](http://nedbatchelder.com/blog/tag/tools.html) *
    * [parenting](http://nedbatchelder.com/blog/tag/parenting.html) *
    * [14](http://nedbatchelder.com/blog/archive2014.html) *
    * [math](http://nedbatchelder.com/blog/tag/math.html) *
    * [politics](http://nedbatchelder.com/blog/tag/politics.html) *
    * [13](http://nedbatchelder.com/blog/archive2013.html) *
    * [coverage](http://nedbatchelder.com/blog/tag/coverage.html) *
    * [movies](http://nedbatchelder.com/blog/tag/movies.html) *
    * [12](http://nedbatchelder.com/blog/archive2012.html) *
    * [language](http://nedbatchelder.com/blog/tag/ling.html) *
    * [my code](http://nedbatchelder.com/blog/tag/mycode.html) *
    * [11](http://nedbatchelder.com/blog/archive2011.html) *
    * [history](http://nedbatchelder.com/blog/tag/history.html) *
    * [books](http://nedbatchelder.com/blog/tag/books.html) *
    * [10](http://nedbatchelder.com/blog/archive2010.html) *
    * [typography](http://nedbatchelder.com/blog/tag/type.html) *
    * [business](http://nedbatchelder.com/blog/tag/business.html) *
    * [09](http://nedbatchelder.com/blog/archive2009.html) *
    * [autism](http://nedbatchelder.com/blog/tag/autism.html) *
    * [friends & family](http://nedbatchelder.com/blog/tag/friendfam.html) *
    * [08](http://nedbatchelder.com/blog/archive2008.html) *
    * [how-to](http://nedbatchelder.com/blog/tag/howto.html) *
    * [science](http://nedbatchelder.com/blog/tag/science.html) *
    * [07](http://nedbatchelder.com/blog/archive2007.html) *
    * [games](http://nedbatchelder.com/blog/tag/games.html) *
    * [quick links](http://nedbatchelder.com/blog/tag/quick.html) *
    * [06](http://nedbatchelder.com/blog/archive2006.html) *
    * [windows](http://nedbatchelder.com/blog/tag/windows.html) *
    * [online](http://nedbatchelder.com/blog/tag/online.html) *
    * [05](http://nedbatchelder.com/blog/archive2005.html) *
    * [work](http://nedbatchelder.com/blog/tag/work.html) *
    * [photos](http://nedbatchelder.com/blog/tag/photos.html) *
    * [04](http://nedbatchelder.com/blog/archive2004.html) *
    * [crafts](http://nedbatchelder.com/blog/tag/crafts.html) *
    * [music](http://nedbatchelder.com/blog/tag/music.html) *
    * [03](http://nedbatchelder.com/blog/archive2003.html) *
    * [cakes](http://nedbatchelder.com/blog/tag/cakes.html) *
    * [google](http://nedbatchelder.com/blog/tag/google.html) *
    * [02](http://nedbatchelder.com/blog/archive2002.html) *
    * [development](http://nedbatchelder.com/blog/tag/development.html) *
    * [animation](http://nedbatchelder.com/blog/tag/animation.html) *
    * [_all tags_](http://nedbatchelder.com/blog/tags.html) *
    * [**_everything!_**](http://nedbatchelder.com/blog/archiveall.html)
  * [RSS](http://nedbatchelder.com/blog/rss.xml)

  
  
[ (C) Copyright 2016, Ned Batchelder
](http://nedbatchelder.com/site/legal.html)

