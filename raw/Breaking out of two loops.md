原文：[Breaking out of two loops](http://nedbatchelder.com/blog/201608/breaking_out_of_two_loops.html "Link to this post" )

---

A common question is, how do I break out of two nested loops at once? For
example, how can I examine pairs of characters in a string, stopping when I
find an equal pair? The classic way to do this is to write two nested loops
that iterate over the indexes of the string:

> `s = "a string to examine"  
for i in range(len(s)):  
    for j in range(i+1, len(s)):  
        if s[i] == s[j]:  
            answer = (i, j)  
            break   # How to break twice???  
`

Here we are using two loops to generate the two indexes that we want to
examine. When we find the condition we're looking for, we want to end both
loops.

There are a few common answers to this. But I don't like them much:

  * Put the loops into a function, and return from the function to break the loops. This is unsatisfying because the loops might not be a natural place to refactor into a new function, and maybe you need access to other locals during the loops.
  * Raise an exception and catch it outside the double loop. This is using exceptions as a form of goto. There's no exceptional condition here, you're just taking advantage of exceptions' action at a distance.
  * Use boolean variables to note that the loop is done, and check the variable in the outer loop to execute a second break. This is a low-tech solution, and may be right for some cases, but is mostly just extra noise and bookkeeping.

My preferred answer, and one that I covered in my PyCon 2013 talk, [Loop Like
A Native](http://nedbatchelder.com/text/iter.html), is to make the double loop
into a single loop, and then just use a simple break.

This requires putting a little more work into the loops, but is a good
exercise in abstracting your iteration. This is something Python is very good
at, but it is easy to use Python as if it were a less capable language, and
not take advantage of the loop abstractions available.

Let's consider the problem again. Is this really two loops? Before you write
any code, listen to the English description again:

> How can I examine pairs of characters in a string, stopping when I find an
equal pair?

I don't hear two loops in that description. There's a single loop, over pairs.
So let's write it that way:

> `def unique_pairs(n):  
    """Produce pairs of indexes in range(n)"""  
    for i in range(n):  
        for j in range(i+1, n):  
            yield i, j  
  
s = "a string to examine"  
for i, j in unique_pairs(len(s)):  
    if s[i] == s[j]:  
        answer = (i, j)  
        break  
`

Here we've written a generator to produce the pairs of indexes we need. Now
our loop is a single loop over pairs, rather than a double loop over indexes.
The double loop is still there, but abstraced away inside the unique_pairs
generator.

This makes our code nicely match our English. And notice we no longer have to
write len(s) twice, another sign that the original code wanted refactoring.
The unique_pairs generator can be reused if we find other places we want to
iterate like this, though remember that multiple uses is not a requirement for
writing a function.

I know this technique seems exotic. But it really is the best solution. If you
still feel tied to the double loops, think more about how you imagine the
structure of your program. The very fact that you are trying to break out of
both loops at once means that in some sense they are one thing, not two. Hide
the two-ness inside one generator, and you can structure your code the way you
really think about it.

Python has powerful tools for abstraction, including generators and other
techniques for abstracting iteration. My [Loop Like A
Native](http://nedbatchelder.com/text/iter.html) talk has more detail (and one
egregious joke) if you want to hear more about it.

tagged: [python](http://nedbatchelder.com/blog/tag/python.html)» 12 reactions

# Comments

![\[gravatar\]](http://www.gravatar.com/avatar/4e190b0a42ef9aea59a7a1ebfc426d6
e.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa147.jpg&size=40
)

**[ionelmc](http://blog.ionelmc.ro)** 2:46 PM on 4 Aug 2016

How about this

[code]

    

    for i in range(len(s)):

        for j in range(i+1, len(s)):

            if s[i] == s[j]:

                answer = (i, j)

                break

        else:

            continue

        break

    
[/code]

![\[gravatar\]](http://www.gravatar.com/avatar/093d6dc5c8295d32bcb8e76c7c66455
a.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa90.jpg&size=40)

**[Gareth Rees](http://garethrees.org)** 4:24 PM on 4 Aug 2016

When you have multiple nested loops you can often reduce them to a single loop
using the tools in the
[itertools](https://docs.python.org/3/library/itertools.html) module. When you
are looping over distinct pairs (or triples, quadruples, etc.), use [itertools
.combinations](https://docs.python.org/3/library/itertools.html#itertools.comb
inations). In this case the code becomes:

[code]

    

    from itertools import combinations

    for (i, a), (j, b) in combinations(enumerate(s), 2):

        if a == b:

            answer = i, j

            break

    else:

        # Failed to find a matching pair — what now?

    
[/code]

Alternatively, the function unique_pairs in the post could be rewritten like
this:

[code]

    

    def unique_pairs(n):

        """Produce pairs of indexes in range(n)"""

        return combinations(range(n), 2)

    
[/code]

Similarly, when you are looping with repetition, use [itertools.product](https
://docs.python.org/3/library/itertools.html#itertools.product).

![\[gravatar\]](http://www.gravatar.com/avatar/75e9a11371cbe1566607180863efdf4
c.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa107.jpg&size=40
)

**[Ned Batchelder](http://nedbatchelder.com)** 5:38 PM on 4 Aug 2016

@ionelmc: break/else/continue/break seems very confusing to me.

![\[gravatar\]](http://www.gravatar.com/avatar/75e9a11371cbe1566607180863efdf4
c.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa107.jpg&size=40
)

**[Ned Batchelder](http://nedbatchelder.com)** 5:39 PM on 4 Aug 2016

@Gareth: Thanks! itertools does have lots of goodies. In this case, I was
trying to focus on the refactoring from two loops to one, but yes, often you
can use itertools to avoid implementing your own logic.

![\[gravatar\]](http://www.gravatar.com/avatar/810ce3e75b43698e8feab4ed7998ebd
a.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa42.jpg&size=40)

**Corrado** 6:55 PM on 4 Aug 2016

Does someone could calculate the computational complexity of all the different
refactoring? What about profiling with millions of elements, which performs
better?

![\[gravatar\]](http://www.gravatar.com/avatar/2e4ef487a7f23239ec152e78a313f7a
c.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa10.jpg&size=40)

**Balazs Tothfalussy** 8:29 PM on 4 Aug 2016

How about avoiding the for loop and breaking altogether?

[code]

    

    s = "a string to examine"

    i,j = 0, 1

    while i < len(s) - 1 and s[i] != s[j]:

        while j < len(s) and s[i] != s[j]:

            j = j + 1

        if j >= len(s):

            i,j = i + 1, i + 2

    

    if i < len(s) - 1 and j < len(s):

        answer = (i, j)

    
[/code]

![\[gravatar\]](http://www.gravatar.com/avatar/d109a5bea9ca3023685e70469a1ee46
8.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa215.jpg&size=40
)

**Tom** 8:36 PM on 4 Aug 2016

Cool solution using a generator!  
  
However I'm confused as to how a generator function is less confusing than
simply putting the nested loops in a function and returning it.  
  
@gareth love this itertoolss example!

![\[gravatar\]](http://www.gravatar.com/avatar/75e9a11371cbe1566607180863efdf4
c.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa107.jpg&size=40
)

**[Ned Batchelder](http://nedbatchelder.com)** 8:46 PM on 4 Aug 2016

@Balazs: I admire the work you put into a while-loop solution, but this is
definitely not clearer than the for-loop.

![\[gravatar\]](http://www.gravatar.com/avatar/75e9a11371cbe1566607180863efdf4
c.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa107.jpg&size=40
)

**[Ned Batchelder](http://nedbatchelder.com)** 8:48 PM on 4 Aug 2016

@Tom: my problem with putting the loops in a function is that you don't get
the power of abstracting the iteration from the condition checking. And in a
real program, it could be awkward to move the double loop into a separate
function, depending on what happened in the loop with the other local
variables.

![\[gravatar\]](http://www.gravatar.com/avatar/8914170c4f8c51ce89de8f3b1b36e15
7.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa124.jpg&size=40
)

**Tobi Wulff** 9:59 PM on 4 Aug 2016

Something like itertools.combinations would be nice but it doesn't do what the
author wants when the two lists contain arbitrary objects. unique_pairs is
really useful and IMO should be part of itertools. Am I missing something or
are there any good reasons why it isn't (yet)?

![\[gravatar\]](http://www.gravatar.com/avatar/b97bf3c9ef3c1b92be966c36306b423
4.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa107.jpg&size=40
)

**Andrew Grigorev** 10:08 PM on 4 Aug 2016

I'd prefer to transform the nested loop to a single generator without defining
a generator function:

[code]

    

    it = ((i, j) for i in range(len(s)) for j in range(i + 1, len(s)))

    

    for i, j in it:

        if s[i] == s[j]:

            answer = (i, j)

            break

    
[/code]

Also, there is a specific solution to get first matching element from such
generator:

[code]

    

    answer = next(

        (i, j)

        for i in range(len(s))

        for j in range(i + 1, len(s))

        if s[i] == s[j]

    )

    
[/code]

![\[gravatar\]](http://www.gravatar.com/avatar/b97bf3c9ef3c1b92be966c36306b423
4.jpg?default=http%3A%2F%2Fnedbatchelder.com%2Fpix%2Favatar%2Fa107.jpg&size=40
)

**Andrew Grigorev** 10:17 PM on 4 Aug 2016

Btw, thank you for your 2003 year talk, found a good point in "Abstracting
your iterators" slide! Other stuff also looks pretty useful and complete :-).

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
    * [how-to](http://nedbatchelder.com/blog/tag/howto.html) *
    * [08](http://nedbatchelder.com/blog/archive2008.html) *
    * [friends & family](http://nedbatchelder.com/blog/tag/friendfam.html) *
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

