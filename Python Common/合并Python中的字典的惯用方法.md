原文： [The Idiomatic Way to Merge Dictionaries in Python](https://treyhunner.com/2016/02/how-to-merge-dictionaries-in-python/)

---

Have you ever wanted to combine two or more dictionaries in Python?

There are multiple ways to solve this problem: some are awkward, some are inaccurate, and most require multiple lines of code.

Let’s walk through the different ways of solving this problem and discuss which is the most [Pythonic](https://docs.python.org/3/glossary.html#term-pythonic).

## Our Problem

Before we can discuss solutions, we need to clearly define our problem.

Our code has two dictionaries: `user` and `defaults`.  We want to merge these two dictionaries into a new dictionary called `context`.

We have some requirements:

1.  `user` values should override `defaults` values in cases of duplicate keys
2.  keys in `defaults` and `user` may be any valid keys
3.  the values in `defaults` and `user` can be anything
4.  `defaults` and `user` should not change during the creation of `context`
5.  updates made to `context` should never alter `defaults` or `user`

**Note**: In 5, we’re focused on updates to the dictionary, not contained objects.  For concerns about mutability of nested objects, we should look into [copy.deepcopy](https://docs.python.org/3/library/copy.html#copy.deepcopy).

So we want something like this:
```py
>>> user = {'name': "Trey", 'website': "http://treyhunner.com"}
>>> defaults = {'name': "Anonymous User", 'page_name': "Profile Page"}
>>> context = merge_dicts(defaults, user)  # magical merge function
>>> context
{'website': 'http://treyhunner.com', 'name': 'Trey', 'page_name': 'Profile Page'}
```

    We’ll also consider whether a solution is Pythonic.  This is a very subjective and often illusory measure.  Here are a few of the particular criteria we will use:

*   The solution should be concise but not terse
*   The solution should be readable but not overly verbose
*   The solution should be one line if possible so it can be written inline if needed
*   The solution should not be needlessly inefficient

## Possible Solutions

Now that we’ve defined our problem, let’s discuss some possible solutions.

We’re going to walk through a number of methods for merging dictionaries and discuss which of these methods is the most accurate and which is the most idiomatic.

### Multiple update

Here’s one of the simplest ways to merge our dictionaries:
```py
context = {}
context.update(defaults)
context.update(user)
```

Here we’re making an empty dictionary and using the [update](https://docs.python.org/3.5/library/stdtypes.html#dict.update) method to add items from each of the other dictionaries.  Notice that we’re adding `defaults` first so that any common keys in `user` will override those in `defaults`.

All five of our requirements were met so this is **accurate**.  This solution takes three lines of code and cannot be performed inline, but it’s pretty clear.

Score:

*   Accurate: yes
*   Idiomatic: fairly, but it would be nicer if it could be inlined

### Copy and update

Alternatively, we could copy `defaults` and update the copy with `user`.
```py
context = defaults.copy()
context.update(user)
```

This solution is only slightly different from the previous one.

For this particular problem, I prefer this solution of copying the `defaults` dictionary to make it clear that `defaults` represents default values.

Score:

*   Accurate: yes
*   Idiomatic: yes

### Dictionary constructor

We could also pass our dictionary to the `dict` constructor which will also copy the dictionary for us:
```py
context = dict(defaults)
context.update(user)
```
This solution is very similar to the previous one, but it’s a little bit less explicit.

Score:

*   Accurate: yes
*   Idiomatic: somewhat, though I’d prefer the first two solutions over this

### Keyword arguments hack

You may have seen this clever answer before, [possibly on StackOverflow](http://stackoverflow.com/a/39858/98187):
```py
context = dict(defaults, **user)
```

This is just one line of code.  That’s kind of cool.  However, this solution is a little hard to understand.

Beyond readability, there’s an even bigger problem: **this solution is wrong.**

The keys must be strings.  In Python 2 (with the CPython interpreter) we can get away with non-strings as keys, but don’t be fooled: this is a hack that only works by accident in Python 2 using the standard CPython runtime.

Score:

*   Accurate: no.  Requirement 2 is not met (keys may be any valid key)
*   Idiomatic: no.  This is a hack.

### Dictionary comprehension

Just because we can, let’s try doing this with a dictionary comprehension:
```py
context = {k: v for d in [defaults, user] for k, v in d.items()}
```

This works, but this is a little hard to read.

If we have an unknown number of dictionaries this might be a good idea, but we’d probably want to break our comprehension over multiple lines to make it more readable.  In our case of two dictionaries, this doubly-nested comprehension is a little much.

Score:

*   Accurate: yes
*   Idiomatic: arguably not

### Concatenate items

What if we get a `list` of items from each dictionary, concatenate them, and then create a new dictionary from that?
```py
context = dict(list(defaults.items()) + list(user.items()))
```

This actually works.  We know that the `user` keys will win out over `defaults` because those keys come at the end of our concatenated list.

In Python 2 we actually don’t need the `list` conversions, but we’re working in Python 3 here (you are on Python 3, right?).

Score:

*   Accurate: yes
*   Idiomatic: not particularly, there’s a bit of repetition

### Union items

In Python 3, `items` is a `dict_items` object, which is a quirky object that supports union operations.
```py
context = dict(defaults.items() | user.items())
```

That’s kind of interesting.  But **this is not accurate**.

Requirement 1 (`user` should “win” over `defaults`) fails because the union of two `dict_items` objects is a [set](https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset) of key-value pairs and sets are unordered so duplicate keys may resolve in an _unpredictable_ way.

Requirement 3 (the values can be anything) fails because sets require their items to be [hashable](https://docs.python.org/3/glossary.html#term-hashable) so both the keys _and values_ in our key-value tuples must be hashable.

Side note: I’m not sure why the union operation is even allowed on `dict_items` objects.  What is this good for?

Score:

*   Accurate: no, requirements 1 and 3 fail
*   Idiomatic: no

### Chain items

So far the most idiomatic way we’ve seen to perform this merge in a single line of code involves creating two lists of items, concatenating them, and forming a dictionary.

We can join our items together more succinctly with [itertools.chain](https://docs.python.org/3/library/itertools.html#itertools.chain):
```py
from itertools import chain
context = dict(chain(defaults.items(), user.items()))
```

This works well and may be more efficient than creating two unnecessary lists.

Score:

*   Accurate: yes
*   Idiomatic: fairly, but those `items` calls seem slightly redundant

### ChainMap

A [ChainMap](https://docs.python.org/3/library/collections.html#collections.ChainMap) allows us to create a new dictionary without even looping over our initial dictionaries (well _sort of_, we’ll discuss this):
```py
from collections import ChainMap
context = ChainMap({}, user, defaults)
```

A `ChainMap` groups dictionaries together into a proxy object (a “view”); lookups query each provided dictionary until a match is found.

This code raises a few questions.

#### Why did we put `user` before `defaults`?

We ordered our arguments this way to ensure requirement 1 was met.  The dictionaries are searched in order, so `user` returns matches before `defaults`.

#### Why is there an empty dictionary before `user`?

This is for requirement 5.  Changes to `ChainMap` objects affect the first dictionary provided and we don’t want `user` to change so we provided an empty dictionary first.

#### Does this actually give us a dictionary?

A `ChainMap` object is **not a dictionary** but it is a **dictionary-like** mapping.  We may be okay with this if our code practices [duck typing](https://docs.python.org/3/glossary.html#term-duck-typing), but we’ll need to inspect the features of `ChainMap` to be sure.  Among other features, `ChainMap` objects are coupled to their [underlying dictionaries](https://gist.github.com/treyhunner/2abe2617ea029504ef8e) and they handle [removing items](https://gist.github.com/treyhunner/5260810b4cced03359d9) in an interesting way.

Score:

*   Accurate: possibly, we’ll need to consider our use cases
*   Idiomatic: yes if we decide this suits our use case

### Dictionary from ChainMap

If we really want a dictionary, we could convert our `ChainMap` to a dictionary:
```py
context = dict(ChainMap(user, defaults))
```

It’s a little odd that `user` must come before `defaults` in this code whereas this order was flipped in most of our other solutions.  Outside of that oddity, this code is fairly simple and should be clear enough for our purposes.

Score:

*   Accurate: yes
*   Idiomatic: yes

### Dictionary concatenation

What if we simply concatenate our dictionaries?
```py
context = defaults + user
```

This is cool, but it **isn’t valid**.  This was discussed in a [python-ideas thread](https://mail.python.org/pipermail/python-ideas/2015-February/031748.html) last year.

Some of the concerns brought up in this thread include:

*   Maybe `|` makes more sense than `+` because dictionaries are like sets
*   For duplicate keys, should the left-hand side or right-hand side win?
*   Should there be an `updated` built-in instead (kind of like [sorted](https://docs.python.org/3/library/functions.html#sorted))?

Score:

*   Accurate: no. This doesn’t work.
*   Idiomatic: no. This doesn’t work.

### Dictionary unpacking

If you’re using Python 3.5, thanks to [PEP 448](https://www.python.org/dev/peps/pep-0448/), there’s a new way to merge dictionaries:
```py
context = {**defaults, **user}
```

This is simple and Pythonic.  There are quite a few symbols, but it’s fairly clear that the output is a dictionary at least.

This is functionally equivalent to our very first solution where we made an empty dictionary and populated it with all items from `defaults` and `user` in turn.  All of our requirements are met and this is likely the simplest solution we’ll ever get.

Score:

*   Accurate: yes
*   Idiomatic: yes

## Summary

There are a number of ways to combine multiple dictionaries, but there are few elegant ways to do this with just one line of code.

If you’re using Python 3.5, this is the one obvious way to solve this problem:
```py
context = {**defaults, **user}
```

If you are not yet using Python 3.5, you’ll need to review the solutions above to determine which is the most appropriate for your needs.

**Note**: For those of you particularly concerned with performance, I also measured the [performance of these different dictionary merging methods](https://gist.github.com/treyhunner/f35292e676efa0be1728).

I teach Python for a living.  If you like my teaching style and your team is interested in **[Python training](http://truthful.technology/)**, please [contact me](/cdn-cgi/l/email-protection#f29a979e9e9db2868087869a94879edc8697919a9c9d9e9d958b)!
