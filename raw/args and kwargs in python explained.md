# Follow Blog via Email

Enter your email address to follow this blog and receive notifications of new
posts by email.

Join 13,189 other followers

# Social

  * [View freepythontips's profile on Facebook](https://www.facebook.com/freepythontips/ "View freepythontips's profile on Facebook" )
  * [View yasoobkhalid's profile on Twitter](https://twitter.com/yasoobkhalid/ "View yasoobkhalid's profile on Twitter" )
  * [View yasoob's profile on GitHub](https://github.com/yasoob/ "View yasoob's profile on GitHub" )

# Top Posts &amp; Pages

  * [ *args and **kwargs in python explained ](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-explained/)
  * [ 20 Python libraries you can't live without ](https://pythontips.com/2013/07/30/20-python-libraries-you-cant-live-without/)
  * [ The self variable in python explained ](https://pythontips.com/2013/08/07/the-self-variable-in-python-explained/)
  * [ Storing and Loading Data with JSON ](https://pythontips.com/2013/08/08/storing-and-loading-data-with-json/)
  * [ The Python yield keyword explained ](https://pythontips.com/2013/09/29/the-python-yield-keyword-explained/)
  * [ What is Pickle in python ? ](https://pythontips.com/2013/08/02/what-is-pickle-in-python/)
  * [ Python socket network programming ](https://pythontips.com/2013/08/06/python-socket-network-programming/)
  * [ 10 python blogs worth following ](https://pythontips.com/2013/07/31/10-python-blogs-worth-following/)
  * [ Interesting Python Tutorials ](https://pythontips.com/2016/08/19/interesting-python-tutorials/)
  * [ OCR on PDF files using Python ](https://pythontips.com/2016/02/25/ocr-on-pdf-files-using-python/)

  * [ Email ](mailto:yasoob.khld@gmail.com "Email" )
  * [ Twitter ](https://twitter.com/yasoobkhalid "Twitter" )
  * [ Facebook ](https://www.facebook.com/m.yasoob "Facebook" )
  * [ GitHub ](https://github.com/yasoob "GitHub" )

Search

  * Widgets
  * Connect
  * Search

[](https://wordpress.com/about-these-ads/)

[
![](https://freepythontips.files.wordpress.com/2013/07/python_logo_notext.png)
](https://pythontips.com/ "Python Tips" )

# [Python Tips](https://pythontips.com/ "Python Tips" )

## Your daily dose of bite sized python tips

# Menu

Skip to content

  * [Home](https://freepythontips.wordpress.com/)
  * [Resources](https://pythontips.com/python-resources/)
  * [Intermediate Python - Book](http://book.pythontips.com)
  * [Newsletter](http://newsletter.pythontips.com/)

[python](https://pythontips.com/category/python/),
[tutorial](https://pythontips.com/category/tutorial/)

# *args and **kwargs in python explained

[August 4, 2013](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/ "6:14 pm" )[Yasoob](https://pythontips.com/author/yasoob008/ "View
all posts by Yasoob" )[args](https://pythontips.com/tag/args/), [args and
kwargs](https://pythontips.com/tag/args-and-kwargs/),
[kwargs](https://pythontips.com/tag/kwargs/),
[python](https://pythontips.com/tag/python/), [python decorator args
kwargs](https://pythontips.com/tag/python-decorator-args-kwargs/), [python
function args kwargs](https://pythontips.com/tag/python-function-args-
kwargs/), [python super args kwargs](https://pythontips.com/tag/python-super-
args-kwargs/) [51 Comments](https://pythontips.com/2013/08/04/args-and-kwargs-
in-python-explained/#comments)

Hi there folks. I have come to see that most new python programmers have a
hard time figuring out the *args and **kwargs magic variables. So what are
they ? First of all let me tell you that it is not necessary to write *args or
**kwargs. Only the * (aesteric) is necessary. You could have also written *var
and **vars. Writing *args and **kwargs is just a convention. So now lets take
a look at *args first.

** Usage of *args **  
*args and **kwargs are mostly used in function definitions. *args and **kwargs allow you to pass a variable number of arguments to a function. What does variable mean here is that you do not know before hand that how many arguments can be passed to your function by the user so in this case you use these two keywords. *args is used to send a **non-keyworded** variable length argument list to the function. Here's an example to help you get a clear idea:
[code]

    
    def test_var_args(f_arg, *argv):
        print "first normal arg:", f_arg
        for arg in argv:
            print "another arg through *argv :", arg
    
    test_var_args('yasoob','python','eggs','test')
    
[/code]

This produces the following result:

[code]

    
    first normal arg: yasoob
    another arg through *argv : python
    another arg through *argv : eggs
    another arg through *argv : test
    
[/code]

I hope this cleared away any confusion that you had. So now lets talk about
**kwargs

**Usage of **kwargs**  
**kwargs allows you to pass **keyworded** variable length of arguments to a function. You should use **kwargs if you want to handle **named arguments** in a function. Here is an example to get you going with it:
[code]

    
    def greet_me(**kwargs):
        if kwargs is not None:
            for key, value in kwargs.iteritems():
                print "%s == %s" %(key,value)
     
    >>> greet_me(name="yasoob")
    name == yasoob
    
[/code]

So can you see how we handled a keyworded argument list in our function. This
is just the basics of **kwargs and you can see how useful it is. Now lets talk
about how you can use *args and **kwargs to call a function with a list or
dictionary of arguments.

**Using *args and **kwargs to call a function**  
So here we will see how to call a function using *args and **kwargs. Just
consider that you have this little function:

[code]

    
    def test_args_kwargs(arg1, arg2, arg3):
        print "arg1:", arg1
        print "arg2:", arg2
        print "arg3:", arg3
    
[/code]

Now you can use *args or **kwargs to pass arguments to this little function.
Here's how to do it:

[code]

    
    # first with *args
    >>> args = ("two", 3,5)
    >>> test_args_kwargs(*args)
    arg1: two
    arg2: 3
    arg3: 5
    
    # now with **kwargs:
    >>> kwargs = {"arg3": 3, "arg2": "two","arg1":5}
    >>> test_args_kwargs(**kwargs)
    arg1: 5
    arg2: two
    arg3: 3
    
[/code]

**Order of using *args **kwargs and formal args**  
So if you want to use all three of these in functions then the order is

[code]

    
    some_func(fargs,*args,**kwargs)
    
[/code]

I hope you have understood the usage of *args and **kwargs. If you have got
any problems or confusions with this then feel free to comment below. For
further study i suggest the official [python docs on defining
functions](http://docs.python.org/tutorial/controlflow.html#more-on-defining-
functions) and [*args and **kwargs on
stackoverflow](http://stackoverflow.com/questions/3394835/args-and-kwargs).

**You might also like :**  
*) [Making a url shortener in python](https://freepythontips.wordpress.com/2013/08/03/a-url-shortener-in-python/)  
*) [20 Python libraries you can’t live without](https://freepythontips.wordpress.com/2013/07/30/20-python-libraries-you-cant-live-without/)  
*) [Targeting python 2 and 3 at the same time.](https://freepythontips.wordpress.com/2013/07/30/make-your-programs-compatible-with-python-2-and-3-at-the-same-time/)

[](https://wordpress.com/about-these-ads/)

### Show your love by sharing this:

  * [Click to share on Twitter (Opens in new window)](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-explained/?share=twitter "Click to share on Twitter" )
  * [Share on Facebook (Opens in new window)](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-explained/?share=facebook "Share on Facebook" )
  * [Click to share on Google+ (Opens in new window)](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-explained/?share=google-plus-1 "Click to share on Google+" )
  * More
  * 

  * [Click to share on Pocket (Opens in new window)](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-explained/?share=pocket "Click to share on Pocket" )
  * [Click to email (Opens in new window)](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-explained/?share=email "Click to email" )
  *   * 

### Like this:

Like Loading...

### _Related_

Standard

# Post navigation

[-&gt; Python socket network programming](https://pythontips.com/2013/08/06
/python-socket-network-programming/)

[&lt;- Making a url shortener in python](https://pythontips.com/2013/08/03/a
-url-shortener-in-python/)

##  51 thoughts on "*args and **kwargs in python explained"

  1. ![](https://1.gravatar.com/avatar/1b08c7f6a54c7b7a5bcdf09ded9f107e?s=48&d=identicon&r=G) [R-M-R](http://lifeaccordingtoateenager.wordpress.com) says:

Saw your links to this in the python programmers group on fb. I have been
coding for a while now, but didn't know anything about this. your blog is
really really helpful! Thank's for posting this, and keep the fb group updated
with your posts!

[ August 5, 2013 at 1:19 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-66)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=66#respond)

    * ![](https://1.gravatar.com/avatar/741c7bf9a1bd02d108f7e5681229925f?s=48&d=identicon&r=G) [Yasoob](https://freepythontips.wordpress.com/) says:

Thanx i am glad that you found this helpful. If you want regular updates then
consider following this blog. The link for following is available in the menu.

[ August 5, 2013 at 1:22 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-68)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=68#respond)

  2. ![](https://0.gravatar.com/avatar/69a428d4b869b34e9f84fb83e2c4cdea?s=48&d=identicon&r=G) Nit says:

Ahhh thanks🙂

[ August 5, 2013 at 2:44 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-71)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=71#respond)

    * ![](https://1.gravatar.com/avatar/741c7bf9a1bd02d108f7e5681229925f?s=48&d=identicon&r=G) [Yasoob](https://freepythontips.wordpress.com/) says:

No problem , cheers

[ August 5, 2013 at 2:52 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-72)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=72#respond)

  3. ![](https://i0.wp.com/graph.facebook.com/1505460658/picture?q=type%3Dlarge&resize=48%2C48) [Terrence Andrew Davis](https://www.facebook.com/terrence.a.davis1) says:

U0 PrintF(U8 *fmt,…)  
{  
//like "this" in C++, in HolyC argv[] and argc  
}

[ August 5, 2013 at 2:55 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-73)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=73#respond)

  4. ![](https://0.gravatar.com/avatar/38f5a8d7acd5ece0d15704d27ccdd433?s=48&d=identicon&r=G) [Amino Prime](http://aminoprimereview.com) says:

Great article, exactly what I needed.

[ August 5, 2013 at 10:09 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-77)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=77#respond)

  5. ![](https://1.gravatar.com/avatar/4b33da66faf7e19559b5397579b99c40?s=48&d=identicon&r=G) [raghvendra](http://gravatar.com/raghvendradharmapurikar) says:

Thanks for nice explanation.

[ August 5, 2013 at 10:28 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-78)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=78#respond)

    * ![](https://1.gravatar.com/avatar/741c7bf9a1bd02d108f7e5681229925f?s=48&d=identicon&r=G) [Yasoob](https://freepythontips.wordpress.com/) says:

No problem……I am glad that i could help you guys.

[ August 5, 2013 at 2:17 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-85)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=85#respond)

  6. Pingback: [Python socket network programming | Bite Sized Python Tips](https://freepythontips.wordpress.com/2013/08/06/python-socket-network-programming/)

  7. Pingback: [The self keyword in python explained | Bite Sized Python Tips](https://freepythontips.wordpress.com/2013/08/07/the-self-keyword-in-python-explained/)

  8. Pingback: [The self variable in python explained | Bite Sized Python Tips](https://freepythontips.wordpress.com/2013/08/07/the-self-variable-in-python-explained/)

  9. Pingback: [Storing and Loading Data with JSON | Bite Sized Python Tips](https://freepythontips.wordpress.com/2013/08/08/storing-and-loading-data-with-json/)

  10. ![](https://2.gravatar.com/avatar/ef4c0aad737bdb4e7ae6e786c296ca88?s=48&d=identicon&r=G) Xavi says:

I am definly going to follow this blog

[ August 9, 2013 at 2:36 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-230)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=230#respond)

  11. Pingback: [Best Python Resources for Beginners and Professionals | Bite Sized Python Tips](https://freepythontips.wordpress.com/2013/09/01/best-python-resources/)

  12. Pingback: [Yasoob Khalid: Best Python Resources for Beginners and Professionals | The Black Velvet Room](http://todd328.wordpress.com/2013/09/23/yasoob-khalid-best-python-resources-for-beginners-and-professionals/)

  13. ![](https://0.gravatar.com/avatar/62fb4307b13a3aa9a12d9e19a4399cd2?s=48&d=identicon&r=G) Per says:

Hi im trying to use a fill_between command but i dont know what to put in the
**kwargs "section".

This is my coding:

(…)

fill_between(x, y1, y2=0, where=None, **kwargs)

What should i write in the kwargs** section?

Greetings from Norway.

[ October 24, 2013 at 11:47 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-1025)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=1025#respond)

    * ![](https://1.gravatar.com/avatar/741c7bf9a1bd02d108f7e5681229925f?s=48&d=identicon&r=G) [Yasoob](https://freepythontips.wordpress.com/) says:

Hi Per. Welcome to my blog. Now regarding your problem I am sorry that I am
not able to clearly understand you. What you can do is that you can write a
detailed email to me or use stackoverflow🙂 In both cases you will get help.

[ October 25, 2013 at 9:39 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-1028)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=1028#respond)

    * ![](https://1.gravatar.com/avatar/47861438c06d2dceaf71d5fe600526e6?s=48&d=identicon&r=G) Mahan Marwat says:

The thingy **kwargs by itself and then refer to them in your function as
kwargs.

[ October 15, 2014 at 9:34 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-3466)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=3466#respond)

  14. Pingback: [Naudingos python nuorodos](http://itdev.lt/programavimas/naudingos-python-nuorodos/)

  15. ![](https://0.gravatar.com/avatar/022cc09bd19fc27ec12b44ef4780ad6d?s=48&d=identicon&r=G) Juan Sierra Pons says:

Quick and easy.  
Still learning python from a book and I didn't find anything on it reletated
to to *args  
and *kwargs  
Thanks for sharing your knowlegde and time.

[ January 24, 2014 at 11:11 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-1262)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=1262#respond)

    * ![](https://1.gravatar.com/avatar/741c7bf9a1bd02d108f7e5681229925f?s=48&d=identicon&r=G) [Yasoob](https://freepythontips.wordpress.com/) says:

I am glad you liked it🙂

[ January 25, 2014 at 3:22 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-1265)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=1265#respond)

  16. ![](https://i2.wp.com/graph.facebook.com/100001574956848/picture?q=type%3Dlarge%26_md5%3D367e735178518820c1c20decfe440799&resize=48%2C48) [Mahesh Puttanna](https://www.facebook.com/mahesh.puttanna.7) says:

hi really it’s I needed, I searched a lot finally I found and I understand
*args and **kwargs. can you please in the same way explain django views
modesls program and urls patterens mapping.thank you very much.

[ February 5, 2014 at 2:54 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-1309)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=1309#respond)

    * ![](https://1.gravatar.com/avatar/741c7bf9a1bd02d108f7e5681229925f?s=48&d=identicon&r=G) [Yasoob](https://freepythontips.wordpress.com/) says:

Hi Mahesh I am glad that you liked the post. I will consider writing on django
views in the future.

[ February 5, 2014 at 2:56 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-1310)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=1310#respond)

  17. ![](https://1.gravatar.com/avatar/41678df2a4211cd1a0c61408887dcc04?s=48&d=identicon&r=G) Deepak Devanand says:

Very helpful. Thank you Yasoob.

[ March 7, 2014 at 11:31 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-1532)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=1532#respond)

  18. ![](https://0.gravatar.com/avatar/65c051d2e09de02641ff2f7d1d8d111d?s=48&d=identicon&r=G) Scooper says:

"bite sized" is written "bite-sized", btw…

[ April 4, 2014 at 6:57 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-1781)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=1781#respond)

  19. ![](https://0.gravatar.com/avatar/f5672a14e1b37ffb4b1e975e0b3cb844?s=48&d=identicon&r=G) Rohit shukla says:

thnx dear yasoob a great article

[ April 25, 2014 at 2:42 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-1947)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=1947#respond)

  20. ![](https://0.gravatar.com/avatar/f5672a14e1b37ffb4b1e975e0b3cb844?s=48&d=identicon&r=G) Rohit shukla says:

will u also explain about a library storm

[ April 25, 2014 at 2:45 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-1948)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=1948#respond)

  21. Pingback: [args and kwargs in python explained | Bite Sized Python Tips | timcoleman3d](http://timcoleman3d.com/2014/06/18/args-and-kwargs-in-python-explained-bite-sized-python-tips/)

  22. ![](https://2.gravatar.com/avatar/bf33f53fb2ba8c5393803faa7078ca58?s=48&d=identicon&r=G) parkerandhobbes says:

Thank you! It's the best explanation I've read but I don't really see the
point in them.

In PHP we just pass variables onto functions. A variable can be well…
anything. A number, a string, an array or even a multidimensional array - can
all be handled by just saying:

function hello($some_variable) {  
//do some stuff  
}

In PHP the function doesn't really care what gets passed to it. Whether you
through into the function, PHP will deal with it. So, if PHP is able to live
quite happily without args and kwargs, how come Python seems to have a problem
here?

This is not a loaded question. It's a sincere question from a dude who is
trying to learn. Perhaps I'm missing something.

Many thanks,

-DC

[ June 27, 2014 at 3:23 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-2528)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=2528#respond)

    * ![](https://1.gravatar.com/avatar/47861438c06d2dceaf71d5fe600526e6?s=48&d=identicon&r=G) Mahan Marwat says:

Python is dynamically typed language.  
Python functions really doesn't care about what you pass to them.  
But you got it the wrong way… please re-read the post.  
If you want to pass one thousand arguments to your function, then you can't
explicitly define every parameter in your function definition. For the reason
you will use *args and your function will be automagically able to handle all
the arguments you pass to them for you.

[ October 15, 2014 at 9:52 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-3467)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=3467#respond)

  23. ![](https://2.gravatar.com/avatar/52fd1a5022ab76e4bbe5d6f5b2ca0b74?s=48&d=identicon&r=G) Sumer Sadawarti says:

Awesome…. doubt cleared… thanks a lot..🙂

Notice one thing: **kwargs arrange keys in alphabetical order  
e.g:

def print_it(f_arg, **kwargs):  
print f_arg  
for key,value in kwargs.iteritems():  
print "%s=%s"%(key,value)

print_it('sumer',name='panda',age=12)

output:

sumer  
age=12  
name=panda

'age' gets printed before 'name'

once again….this post helped me a lot…. thanks dude.

[ October 16, 2014 at 12:31 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-3472)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=3472#respond)

  24. Pingback: [Programming for Everybody - Week 7 | The Lit Tech](http://thelittech.wordpress.com/2014/11/17/programming-for-everybody-week-7/)

  25. Pingback: [NotesIndex: Index of Notes | Never Ending Security](https://neverendingsecurity.wordpress.com/2015/02/26/notesindex-index-of-notes/)

  26. ![](https://1.gravatar.com/avatar/4a1f282e67c16f84f8c09e675fb988ef?s=48&d=identicon&r=G) [ko_dong_sung](http://qkqhxla1.tistory.com) says:

thank you so much  
easy explanations.

[ March 1, 2015 at 8:34 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-4184)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=4184#respond)

  27. ![](https://1.gravatar.com/avatar/1d6f629cdbb5f900ff482d453f6cc9ab?s=48&d=identicon&r=G) Ivo says:

The best explanation about *args and **kwargs I have ever seen. Thank you a
lot.

[ April 10, 2015 at 5:02 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-4313)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=4313#respond)

  28. ![](https://0.gravatar.com/avatar/044d489f0c765d88688a60e297273651?s=48&d=identicon&r=G) Falon says:

For Python3 now please note a difference:  
in the first example for usage of **kwargs the line that has this:  
for key, value in kwargs.iteritems():

gave me an error because it needs to be:  
for key, value in kwargs.items():

since iteritems() no longer is defined in Python3 because items() can do the
same thing.

[ April 14, 2015 at 4:09 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-4328)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=4328#respond)

  29. ![](https://1.gravatar.com/avatar/d9c8d25d44ec7dadd5a11909616f694b?s=48&d=identicon&r=G) glayn says:

OMG you r like the best teacher EVER! thnx a bunch

[ April 21, 2015 at 7:44 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-4354)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=4354#respond)

  30. ![](https://0.gravatar.com/avatar/c3e4ddc33b7ad2218d903acff0dd4648?s=48&d=identicon&r=G) [niteDrain](http://netheregal.wordpress.com) says:

In the second paragraph, you've said:

"*args is used to send a non-keyworded variable length argument list to the
function".

As far as I understand, what you're passing is any number of non-keyworded
arguments in the function call. But NOT in a list. Am I wrong?

Thanks for the article : )

[ May 28, 2015 at 11:51 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-4449)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=4449#respond)

  31. Pingback: [*args and **kwargs in python explained | Zack's Ad hoc Page](https://zhihuicao.wordpress.com/2015/06/23/args-and-kwargs-in-python-explained/)

  32. ![](https://1.gravatar.com/avatar/d15157a027bd5d7df21452c179cc1c72?s=48&d=identicon&r=G) shengy90 says:

Reblogged this on [codaborate](https://codaborate.wordpress.com/2015/08/18
/args-and-kwargs-in-python-explained/).

[ August 18, 2015 at 2:13 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-5636)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=5636#respond)

  33. ![](https://2.gravatar.com/avatar/5512bda013101248a53acb42c1edda41?s=48&d=identicon&r=G) alnauto says:

That was a short but insightful explanation. Thank you very much, I'm sure I
can make good use of *args and *kwargs in my future coding!

[ August 18, 2015 at 11:39 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-5641)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=5641#respond)

  34. ![](https://2.gravatar.com/avatar/5512bda013101248a53acb42c1edda41?s=48&d=identicon&r=G) [alnauto](http://alnauto.wordpress.com) says:

That was a short but insightful explanation. Thank you very much, I'm sure I
can make good use of *args and **kwargs in my future coding!

[ August 18, 2015 at 11:39 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-5642)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=5642#respond)

    * ![](https://1.gravatar.com/avatar/741c7bf9a1bd02d108f7e5681229925f?s=48&d=identicon&r=G) [Yasoob](https://freepythontips.wordpress.com/) says:

I am glad that it helped!

[ August 21, 2015 at 12:11 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-5670)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=5670#respond)

  35. ![](https://0.gravatar.com/avatar/0664f51f1137f666f3d53a1a0900205b?s=48&d=identicon&r=G) Edd says:

Well, this was quite nice. now I know how to use *args and **kwargs correctly.
If's easier thant what I thought before. Thank you for the tips.

[ October 28, 2015 at 2:05 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-6779)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=6779#respond)

  36. ![](https://1.gravatar.com/avatar/a37322c098e2de658e4fe3dadce8cf19?s=48&d=identicon&r=G) [vu2aeo](http://bayesianadventures.wordpress.com) says:

Excellent tutorial. Thanks a lot!

[ November 30, 2015 at 1:28 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-7302)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=7302#respond)

  37. ![](https://1.gravatar.com/avatar/413dcd1dcbb81301c82772b5c6163c38?s=48&d=identicon&r=G) Jay says:

When using **kwargs, we call those 'keyword' arguments.  
But in Python, I thought 'keyword' referred to Python keywords like 'if',
'else', 'while', 'for', etc.  
I'm guessing the term 'keyword' means two completely different things
depending on the context (Python language keywords vs. keyword arguments) but
I wasn't sure, can someone clarify?

[ March 27, 2016 at 4:11 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-8372)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=8372#respond)

  38. ![](https://0.gravatar.com/avatar/0b53597cd590387898cf8ebc339924e7?s=48&d=identicon&r=G) durexyl says:

just a minor (and late) correction: * is not "aesteric", but "asterisk"🙂

[ April 26, 2016 at 1:08 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-8521)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=8521#respond)

  39. ![](https://1.gravatar.com/avatar/1f8fcadb0652c4e98ea572740868fbdb?s=48&d=identicon&r=G) Muneer says:

Nice. Very usefull.

[ May 3, 2016 at 5:59 pm ](https://pythontips.com/2013/08/04/args-and-kwargs-
in-python-explained/#comment-8554) [Reply](https://pythontips.com/2013/08/04
/args-and-kwargs-in-python-explained/?replytocom=8554#respond)

  40. ![](https://i1.wp.com/graph.facebook.com/v2.2/158346237901642/picture?q=type%3Dlarge%26_md5%3Dcd2e0ab98e5ba22e38d059e8cd772748&resize=48%2C48) [Mohammed Reda Berrohou](https://www.facebook.com/app_scoped_user_id/158346237901642/) says:

Wonderful explaining Thank You Very very much

[ May 17, 2016 at 1:35 pm ](https://pythontips.com/2013/08/04/args-and-kwargs-
in-python-explained/#comment-8617) [Reply](https://pythontips.com/2013/08/04
/args-and-kwargs-in-python-explained/?replytocom=8617#respond)

  41. ![](https://2.gravatar.com/avatar/88cb34f0efb6965b3124d438528848a7?s=48&d=identicon&r=G) Aditya says:

I have one doubt on kwarg.pop(). What is the use of this method? I am bit
confused on this. Can you please explain it to me?

[ May 29, 2016 at 11:33 am ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-8687)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=8687#respond)

  42. ![](https://1.gravatar.com/avatar/de768b2378672e63de965d0606b8c8e4?s=48&d=identicon&r=G) Luiz Rodrigo says:

Nice explanation😀 , thanks

[ August 9, 2016 at 6:52 pm ](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#comment-8981)
[Reply](https://pythontips.com/2013/08/04/args-and-kwargs-in-python-
explained/?replytocom=8981#respond)

### Leave a Reply [Cancel reply](https://pythontips.com/2013/08/04/args-and-
kwargs-in-python-explained/#respond)

Enter your comment here...

Fill in your details below or click an icon to log in:

  *   *   *   *   * 

[ ![Gravatar](https://1.gravatar.com/avatar/ad516503a11cd5ca435acc9bb6523536?s
=25&d=identicon&forcedefault=y&r=G) ](https://gravatar.com/site/signup/)

Email (required) (Address never made public)

Name (required)

Website

![WordPress.com Logo](https://1.gravatar.com/avatar/ad516503a11cd5ca435acc9bb6
523536?s=25&d=identicon&forcedefault=y&r=G)

**** You are commenting using your WordPress.com account. ( [Log Out](javascript:HighlanderComments.doExternalLogout\( 'wordpress' \);) / Change )

![Twitter picture](https://1.gravatar.com/avatar/ad516503a11cd5ca435acc9bb6523
536?s=25&d=identicon&forcedefault=y&r=G)

**** You are commenting using your Twitter account. ( [Log Out](javascript:HighlanderComments.doExternalLogout\( 'twitter' \);) / Change )

![Facebook photo](https://1.gravatar.com/avatar/ad516503a11cd5ca435acc9bb65235
36?s=25&d=identicon&forcedefault=y&r=G)

**** You are commenting using your Facebook account. ( [Log Out](javascript:HighlanderComments.doExternalLogout\( 'facebook' \);) / Change )

![Google+ photo](https://1.gravatar.com/avatar/ad516503a11cd5ca435acc9bb652353
6?s=25&d=identicon&forcedefault=y&r=G)

**** You are commenting using your Google+ account. ( [Log Out](javascript:HighlanderComments.doExternalLogout\( 'googleplus' \);) / Change )

[Cancel](javascript:HighlanderComments.cancelExternalWindow\(\);)

Connecting to %s

Notify me of new comments via email.

Notify me of new posts via email.

[Blog at WordPress.com.](https://https://wordpress.com//?ref=footer_blog)

[Follow](javascript:void\(0\))

### Follow "Python Tips"

Get every new post delivered to your Inbox.

Join 13,189 other followers

[Build a website with WordPress.com](https://wordpress.com/?ref=lof)

Send to Email Address Your Name Your Email Address

![loading](https://s2.wp.com/wp-content/mu-plugins/post-
flair/sharing/images/loading.gif) Cancel

Post was not sent - check your email addresses!

Email check failed, please try again

Sorry, your blog cannot share posts by email.

%d bloggers like this:

![](https://sb.scorecardresearch.com/p?c1=2&c2=7518284&c3=&c4=&c5=&c6=&c15=&cv
=2.0&cj=1)

![](https://pixel.wp.com/b.gif?v=noscript)

