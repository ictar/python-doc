原文：[Defensive programming in Python](http://tutorials.pluralsight.com/python/defensive-programming-in-python)

---

It's Friday afternoon, and your new release has been out for a few days.  Your
week began with a feeling of pride and relief, but your pride has slowly
diminished as the week marched forward.  It took a lot of effort and dedication
to put out such a bug-free release.  In fact, on the release date you were
confident the next few weeks would be quiet as users didn't have anything else
to need or want.

Of course, it was too good to be true and not too long after the release your
first bug report came in.  The first bug report was just something innocuous, a
minor misspelling in a new dialog box.  Then, a few more small bug tickets
trickle in, which you quickly fixed and pushed to the repository.

Then it happened, every developer's worst nightmare, a bug reported in your
most prized portion of the system.  You frantically look through the code even
though you know it by memory.  How is it possible that branch of code was even
executed in this scenario?!  The code **must** be lying to you.

Fast-forward a few days into the bug hunt, and you still have no clue how this
happened.  You cannot even reproduce the scenario in your testing environment.
If only you had more debugging information about the failure...

### The truth will set you free

You'll recognize this scenario if you've been writing software for any
non-trivial amount of time.  It's upsetting that despite your best efforts
you've shipped broken software, again.  Don't worry, it happens.

This is the part of the story where I reveal the magic bullet to solve this for
you once and for all.  Unfortunately, I can't, and I don't think such a thing
exists.

The hidden truth is all software has bugs.  However, that doesn't mean we
should give up and not strive for perfection.  It just means we would be better
served by slightly altering our perception of this reality.  We should be
writing software almost as if we are planning for defects.  We should be
writing software defensively, i.e calmly setting traps for the inevitable
and unsuspecting bugs.

## Defensive programming

The best term to describe this style is
[Defensive Programming ](http://en.wikipedia.org/wiki/Defensive_programming).
Wikipedia description doesn't quite capture what I have in mind, but it's a
good starting point:

    A form of defensive design intended to ensure the continuing function of a
    piece of software in spite of unforeseeable usage of said software. The
    idea can be viewed as reducing or eliminating the prospect of Murphy's Law
    having effect. Defensive programming techniques are used especially when a
    piece of software could be misused mischievously or inadvertently to
    catastrophic effect.

What I'm really talking about is a combination of the following guidelines:

Guidelines

*   Every line of code is a liability
*   Codify your assumptions
*   Executable documentation is preferable [1]

These guidelines are crucial to ensuring that we can protect our code and
sanity from inevitable bugs.  Remember, we're operating from the standpoint
that we aren't going to write bug-free code.

We need to keep the guidelines in mind to help us find bugs quickly.  A lot of
times finding bugs is the hard part.  So, let's optimize for finding, not the
impossible task of preventing them entirely.

### Python tools

[![description](https://raw.githubusercontent.com/pluralsight/guides/master/images/a1516ea4-3261-43a6-a52d-348446b844df.png) ](https://raw.githubusercontent.com/pluralsight/guides/master/images/a1516ea4-3261-43a6-a52d-348446b844df.png)

Let's take a deeper look at some of the tools available to help in
following the guidelines.  We'll use [Python](http://python.org)
as our language for demonstration purposes, but most languages have very
similar tools.

*   Asserts
*   Logging
*   Unit tests

Assume we have the following function which takes values from a user and will
normalize the specified range of data into something between 0 and 1, which can
be used by a new widget later down the road.
```py
def normalize_ranges(colname): 
    """ Normalize given data range to values in [0 - 1] Return dictionary new 'min' and 'max' keys in range [0 - 1] """ 

    # 1-D numpy array of data we loaded application with 
    original_range = get_base_range(colname) 
    colspan = original_range['datamax'] - original_range['datamin'] 

    # User filtered data from GUI 
    live_data = get_column_data(colname) 
    live_min = numpy.min(live_data) 
    live_max = numpy.max(live_data) 

    ratio = {} 
    ratio['min'] = (live_min - original_range['datamin']) / colspan 
    ratio['max'] = (live_max - original_range['datamin']) / colspan 

    return ratio
```

Now, assume we have the following 'columns' that are returned by the
`get_column_data()` function:
```py
age = numpy.array([10.0, 20.0, 30.0, 40.0, 50.0]) 
height = numpy.array([60.0, 66.0, 72.0, 63.0, 66.0])
```

Let's verify it does indeed turn our given range into something between [0 -
1]:
```py
>>> normalize_ranges('age')
    {'max': 1.0, 'min': 0.0}
```

OK, that's a pretty short test, but it seems to work.  We passed in a range of
'real' numbers and normalized it to something in the space of [0 - 1].

Remember the guidelines mentioned above, and let's find the bugs
that exist in this function.  What assumptions are we making implicitly in the
code?

I can see a few assumptions:

1.  `original_range` contains only positive numbers
2.  Ratio returned is between 0.0 and 1.0

After careful examination, there are quite a few assumptions in the above code.
Unfortunately, these aren't immediately obvious when glancing at the code.  If
only we could make these assumptions more clear...

## Asserts

[Asserts](http://docs.python.org/2/reference/simple_stmts.html#the-assert-statement)
are very common in [unit tests](http://docs.python.org/2/library/unittest.html).
In fact, [Python](http://python.org) has a large collection of customized
[asserts for unit tests](http://docs.python.org/2/library/unittest.html#assert-methods).
However, there is no reason this useful tool should be used simply in the
testing world.

[Assert statements](http://docs.python.org/2/reference/simple_stmts.html#the-assert-statement)
within _normal_ code are very useful as well.  These statements take an
expression and raise an
[AssertionError](http://docs.python.org/2/library/exceptions.html#exceptions.AssertionError)
along with an optional message if the expression is `False`.

For example, our above function claimed to always return a value between [0 -
1].  Unfortunately, more stressing of our assumptions shows this isn't true:
```py
>>> age = numpy.array([-10.0, 20.0, 30.0, 40.0, 50.0]) 
>>> normalize_ranges('age') 
    {'max': 1.0, 'min': -0.5}
```

As you can imagine, this scenario could easily go unnoticed for a long time and
this return value could be propagated all over the code base. This is precisely
the type of bug that's impossible to find and leads to the sad story that
started this post.

We could try to think of every possible value our users could pass in and
handle it properly.  In fact, this is the _right thing_ to do, but there's no
guarantee that we won't miss something.  We've already conceded the fact that
programmers are fallible.

Luckily, we can use assert statements to code **against** our future selves now
that we've accepted that we make mistakes.
```py
def normalize_ranges(colname): 
    """ Normalize given data range to values in [0 - 1] 
    Return dictionary new 'min' and 'max' keys in range [0 - 1] 
    """ 

    # 1-D numpy array of data we loaded application with 
    original_range = get_base_range(colname) 
    colspan = original_range['datamax'] - original_range['datamin'] 

    # User filtered data from GUI 
    live_data = get_column_data(colname) 
    live_min = numpy.min(live_data) 
    live_max = numpy.max(live_data) 

    ratio = {} 
    ratio['min'] = (live_min - original_range['datamin']) / colspan 
    ratio['max'] = (live_max - original_range['datamin']) / colspan 

    assert 0.0 <= ratio['min'] <= 1.0, ( 
            '"%s" min (%f) not in [0-1] given (%f) colspan (%f)' % ( 
            colname, ratio['min'], original_range['datamin'], colspan)) 
    assert 0.0 <= ratio['max'] <= 1.0, ( 
            '"%s" max (%f) not in [0-1] given (%f) colspan (%f)' % ( 
            colname, ratio['max'], original_range['datamax'], colspan)) 

    return ratio
```

We added a few assert statements that will alert us if we don't return values
within the expected range. Let's see how these assertions change our small test
case:
```py
>>> age = numpy.array([-10.0, 20.0, 30.0, 40.0, 50.0]) 
>>> normalize_ranges('age') 
    AssertionError: "age" min (-0.500000) not in [0-1] given (10.000000) colspan(40.000000)
```

This small change has several benefits:

*   Serves as a form of _executable_ documentation
*   Places warnings closer to the root problem
*   Includes valuable debugging information about the "invalid" parameters

1.  **Serves as a form of _executable_ documentation**

Typically documentation comes in a few different flavors such as in-line or
block comments, docstrings, and sphinx.  Each of these serves a specific
purpose and are almost essential to software development. Unfortunately,
they all suffer from the same issue. They can quickly get out of sync with
fast changing code and requirements.  This leads to documentation that
developer cannot trust.

Asserts act as documentation with a different purpose. They clearly and
concisely describe the expected state of the application at run time.  In
addition, the application will complain if we change our assumptions
without modifying the asserts to match the new behavior.

Assert statements are much more likely to be updated alongside other
changes. Therefore asserts are more trustworthy than non-executable
documentation.  In addition, asserts still provide many of the benefits of
comments, docstrings, etc.

It's worth pointing out there's another form of executable documentation
quite common in the Python ecosystem known as
[doctests](http://docs.python.org/2/library/doctest.html).  These
tests/documentation can be somewhat ugly to look at, but their key feature
is they are **close to the code**, just like asserts.

2.  **Places warnings closer to the root problem**

We've all been there, you debug a problem for hours and realize the _real_
bug wasn't even close to where you started (see
[5 whys](http://en.wikipedia.org/wiki/5_Whys)).  Maybe the root cause of
the bug was logically far away from where you first saw the symptoms.

For example, you find a byte string deep in your system, but you assumed
everything internally was Unicode strings.  It could take a long time to
find where the conversion was first broken. This is a frustrating situation
to be in.  It would be nice to have found the bug much sooner or at
least had more debug information.

Asserts aren't going to prevent this situation, but they do offer a chance
to improve it.  The above asserts will alert us the moment this function
doesn't obey its'
[contract](http://en.wikipedia.org/wiki/Design_by_contract) to return a
value between [0 - 1].  This could give us a valuable clue later if we find
other code that has an invalid range.  We'll know this function didn't
live up to its end of the
[contract](http://en.wikipedia.org/wiki/Design_by_contract).  This one clue
could literally save hours since it can avoid tracing back from the symptom
all the way to the cause.

3.  **Includes valuable debugging information about the "invalid" parameters**

Notice that our assert statements also included information about the input
parameters.  This information will be invaluable when a user encounters the
bug using data we don't have access to.  Also, the debug information will
be especially useful when the user has trouble explaining the error
scenario.  So, these few assert statements could prevent you being the guy
who disgracefully marks the bug with an "unreproducible" status.

The input parameter information also has a few other subtle benefits:

    *   Displays an invalid assumption about what type of data users are running
    *   Explain oversight in our documentation about what type of data is
expected
    *   Expose potential new use cases that cannot be executed

### Assert downsides

We've established that asserts can provide a ton of benefits, but it's
not all fun and games.  As usual, there are downsides.

1.  **Debug mode**

Typically, for both technical and practical reasons, assert statements
aren't meant for production code.  Asserts are only enabled when the hidden
[debug](http://docs.python.org/2/library/constants.html#__debug__) constant
is `True`.  However, the default value for this constant is `True`, which
means your code is most likely currently shipping in _debug_ mode.

This is something to consider if your application is in an environment
where small amounts of additional logic is noticeable.  The only way to
turn off _debug_ mode is to run the Python interpreter with the `-O`
[option](http://docs.python.org/2/using/cmdline.html#cmdoption-O).

2.  **Increased code noise**

It's very easy to overuse asserts and quickly make your code difficult to
read.  This can make your code very noisy and bury the real functionality
in a series of error checks and conditions.  The following code is an
example of overuse and how it's difficult to see what the code is meant to
do.
```py
def normalize_ranges(colname): 
    """ 
    Normalize given data range to values in [0 - 1] 
    Return dictionary new 'min' and 'max' keys in range [0 - 1] 
    """ 

    assert isinstance(colname, str) 

    original_range = get_base_range(colname) 
    assert original_range['datamin'] >= 0 
    assert original_range['datamax'] >= 0 
    assert original_range['datamin'] <= original_range['datamax'] 

    colspan = original_range['datamax'] - original_range['datamin'] 
    assert colspan >= 0, 'Colspan (%f) is negative' % (colspan) 

    live_data = get_column_data(colname) 
    assert len(live_data), 'Empty live data' 

    live_min = numpy.min(live_data) 
    live_max = numpy.max(live_data) 

    ratio = {} 
    ratio['min'] = (live_min - original_range['datamin']) / colspan 
    ratio['max'] = (live_max - original_range['datamin']) / colspan 

    assert 0.0 <= ratio['min'] <= 1.0 
    assert 0.0 <= ratio['max'] <= 1.0 

    return ratio
```

#### Proper assert usage

Use asserts sparingly and for things you assume are **never** supposed to
occur.  Don't go overboard using assertions to check for invalid input.

There are no hard and fast rules for this and each developer might have a
different tolerance for assert usage.  Try to adopt some standards of your own
and include them in your
[developer style guide](http://google-styleguide.googlecode.com/svn/trunk/pyguide.html).

You do have a style guide right?

Also, remember Python embraces
[duck-typing](http://en.wikipedia.org/wiki/Duck_typing) so don't ruin this
by going overboard and using asserts to verify all your types.

One technique I've found useful is to catch all `AssertionError` exceptions
at the top-level of my application and combine them with another useful
technique.

##  Logging

Logging can be used similarly to assert statements.  It can provide run time
debugging information and potentially improve upon your executable
documentation.  Although, logging is not exactly like asserts.  It has few
extra benefits.

1.  Increased control granularity

Python's [logging](http://docs.python.org/2/library/logging.html) module is
very comprehensive and customizable.  You can send messages to several
different levels and each level can be turned on and off at will.  So, you
might consider some situations to be more severe than others and can codify
this with log levels.

Remember, this isn't the case with asserts because they rely on
[debug mode](http://docs.python.org/2/library/constants.html#__debug__).

2.  More dynamic control

Logging allows you to read your log level from pretty much anywhere.  Some
common places are a config file, environment variable, and database.  This
flexibility allows you to control how much log information you see without
re-running or re-distributing your application.

In contrast, Python doesn't allow you to dynamically assign to the
[debug](http://docs.python.org/2/library/constants.html#__debug__) constant
for assert statements.  So, you can't turn on and off asserts without
re-running the application.

This is worth considering when deciding on your defensive coding strategy.
Dynamic logging control is especially important if you an "alternative"
distribution mechanism like [PyInstaller](http://www.pyinstaller.org/).

3.  Silently save tracebacks

It's useful to have traceback and debugging information at the time of
a failure and smart logging usage can do this for you almost automatically.

This concept is demonstrated in a
[great exception handling post](http://doughellmann.com/2009/06/python-exception-handling-techniques.html#index-1)
by [Doug Hellman](http://doughellmann.com):
```py
def main(): 
    logging.basicConfig(level=logging.WARNING) 
    log = logging.getLogger('example') 
    try: 
        throws() 
        return 0 
    except Exception, err: 
        log.exception('Error from throws():') 
        return 1
```

    The call to
[log.exception](http://docs.python.org/2/library/logging.html#logging.Logger.exception)
automatically adds the exception information for us.  Then, we could
configure the logger to put tracebacks and exceptions into a separate log
file for later inspection without all the normal information and warnings.

This exception file could contain excellent debug information if enabled in
production code. This opens up a lot of exciting possibilities for mining
this logging data:

    *   Discover users trying different permutations of features we've never
tested or even considered, which could lead to adding new features to
make common use-cases easier.
    *   Find common errors due to a misunderstanding that users have about how
the application works, which could lead to writing better user
documentation.
4.  Higher-level combination with assertions

You can also use assertions in tandem with your logging.  For example, you
could run your application in the default debug mode then catch and
log your `AssertionError` to a different file.  This could lead to even more
data mining possibilities such as finding out an environmental assumption
you have about the platform your running in.

These are just a few uses for logging in the context of Defensive
Programming.  In fact, you could use logging to create low-fidelity
solutions for all sorts of problems, which is another post all in its own.
[2]

### Logging downsides

Logging suffers from many of the same downsides as asserts.  However, logging's
additional flexibility comes with additional baggage to consider.

1.  Difficulty managing consistent levels

The most difficult thing with logging is using the available set of levels
consistently throughout a code base. This boils down to the subjective
problem of naming, which is one of only
[two problems in Computer Science](http://martinfowler.com/bliki/TwoHardThings.html).
The best solution is to commit guidelines alongside your code in a
style guide.  Then, there is some project-specific documentation for
newcomers to refer to when adding log messages.

2.  Design of multiple loggers

The [logging module](http://docs.python.org/2/library/logging.html) is
extremely flexible, but that flexibility comes at a cost.  Logging
configurations can get complicated. Consider a strategy like the following:

    *   Debug level messages go to a hidden file called .debug
    *   Info, warning, and error level messages go to stderr
    *   Critical and exception level messages go into GUI pop up boxes

    This is a good starting point. Also, Python's documentation includes
several
[good logging strategies](http://docs.python.org/2/howto/logging-cookbook.html)
that are definitely worth a read before deciding on your setup.

Remember, logging could be your saving grace when chasing a difficult bug.  So,
take the time to learn the logging system and be sure to design your
configuration carefully.  Even simple applications deserve a good, carefully
designed logging strategy.

## Unit tests

One of the last ways to protect ourselves from future bugs is to not forget
past bugs.  Old bugs have a tendency to creep back into long lived code bases
This is usually a result of someone not understanding _why_ something is
written in a particular way and removing it to 'clean it up.'

This presents another scenario where it's useful to have another form of
executable documentation.  So, when an unsuspecting developer changes some code
or removes something there's running code to alert them of their mistake.

Typically people encounter unit tests in the context of
[Test Driven Development](http://en.wikipedia.org/wiki/Test-driven_development).
This is a great concept, but often in practice it's a bit too optimistic
and difficult to follow (read: customer wants code now).  However, that
discussion is for another blog post.

What I want to discuss is how to use unit tests to protect you from future and
past bugs.  Think of this as somewhat reverses the
[TDD](http://en.wikipedia.org/wiki/Test-driven_development) testing concept
into something a bit more pragmatic with less up-front time costs.

I propose you write tests AFTER you fix a bug. [(see discussion)](http://codrspace.com/durden/a-software-developers-hidden-truth/#comment-1096395589)

This serves a few purposes that might not be immediately obvious:

1.  Improves documentation of bug fix

Surely you included a nice commit message indicating how you fixed the bug,
but don't stop there.  It's likely that your commit message and comments
are lacking something like:

    *   How did you test the fix?
    *   What exact scenario caused the bug?

    This is where a good unit test comes into play.  You already know how to
test the bug.  (You did test it before committing, right?)  So, codify the
scenario you tested with and let everyone benefit from your hard work.

Unit tests are an excellent place to
[spell it all out](http://en.wikipedia.org/wiki/Five_Ws) and provide
documentation for a bug fix.  You can not only explain how and why you
fixed it, but how you tested it.  This information can be very valuable if
the bug creeps up again.

Don't forget, a passing test can be used as a clue to where the bug is
**not** located.

2.  Future-proofing code against duplicate bugs

It's not uncommon for a particular bug to sneak back into a code base.
This can happen because of changing requirements, re-factoring errors, or
any number of situations.  You can catch regressions by writing a unit test
for a specific bug fix and remembering to run your tests.  Think of a unit
test as giving yourself a get out of jail free card on a bug you might
inject again later.

### Unit test downsides

The main downside of unit tests is that it's easy to forget to run them.  This
is just a byproduct of the tests not be located directly with the code.  This
downside can be mitigated by practicing something like
[Continuous Integration](http://en.wikipedia.org/wiki/Continuous_integration)
using something like [Travis CI](https://travis-ci.org/) and automating your
testing.

Another downside is unit tests are typically only executed in their own testing
environment, which is a simulated environment with no real users.

### Conclusion

This style of development is tough to categorize, and unfortunately there
aren't any solid rules to say when to use what.  So, I encourage you to keep
the guidelines in mind.

The guidelines will lead to a subtle change in mindset.  The
mindset change is important, not the tools and mechanisms themselves.
Eventually you'll make some mistakes by overusing asserts or logging and start
to form your own style.  Also, the requirements for every project differ so
it's important to learn all the tools and combine them in ways that
make sense for your situation.

#### Footnotes

*   References for this blog have been grouped into a
[Pinboard collection of links](https://pinboard.in/u:durden/t:defensive_coding_talk/)
*   A version of the blog was
[presented](http://www.youtube.com/watch?v=HZUY-lo-Esg&amp;list=PL0MRiRrXAvRhewgIznFV_mSDaWst3kHKE&amp;feature=player_detailpage) at the
[Pytexas 2013 conference](http://pytexas.org).

        *   You can view the slides for that talk
[here](http://durden.github.io/defensive_coding/).
    *   You can also view the references for the related talk
[here](http://bitly.com/defensive_coding).

[1]

    Executable documentation is a term sometimes used to describe
    [doctests](http://docs.python.org/2/library/doctest.html).  "Literate
    testing" is term used to describe this concept.

[2]

    You could even use logging to build your own analytics tool.  Log to a
    network or Dropbox file each time a feature is used.  Then, have a shell
    script come behind you every time and collect these files.  Now you have a
    ton of usage information in a simple text-based format opening up tons of
    possibilities for data mining and helping your users.

 <!-- markdown parsing fails if we don't include a true html break -->
    Keep in mind that most users won't fill out surveys.  So, this would be a
    way to collect information on the features they are trying to use or common
    workflows.  Then, you could make these better in future releases.
