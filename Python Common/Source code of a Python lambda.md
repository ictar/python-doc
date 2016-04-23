原文：[Source code of a Python lambda](http://xion.io/post/code/python-get-lambda-code.html)

---

_…or: The Most Hideous Hack I’ve (Almost) Done_

In [callee](http://github.com/Xion/callee), the [argument matcher library for Python](http://callee.readthedocs.org) that
[I released recently](http://xion.io/post/news/callee-intro.html), there is this lovely
[`TODO` note](https://github.com/Xion/callee/blob/f695ff4e1c45bfd45445ebb8014a202029a93dce/callee/general.py#L55)
for a seemingly simple feature. When using the
[`Matching` construct](http://callee.readthedocs.org/en/stable/reference/general.html#callee.general.Matching)
with a simple `lambda` predicate:
```python
mock_foo.assert_called_with(Matching(lambda x: x % 2 == 0))
```

it would be great to see its _code_ in the error message if the assertion fails. Right now it’s just going to say
something like `<Matching <function <lambda> at 0x7f5d8a06eb18>>`. Provided you don’t possess a supernatural ability
of dereferencing pointers in your head, this won’t give you any immediate hint as to what went wrong. Wouldn’t it be nice
if it read as, say, `<Matching \x: x % 2>` instead?[1](#fn:1)

So I thought: why not try and implement such a mechanism? This is Python, after all — a language where you can spawn
[completely new classes](https://blog.ionelmc.ro/2015/02/09/understanding-python-metaclasses/#simple-metaclass-use)
at runtime, walk the stack [backwards](https://docs.python.org/2/library/inspect.html#the-interpreter-stack)
(or even [forward](https://docs.python.org/2/library/sys.html#sys.settrace)) and read the local variables,
or change the behavior of the [import system itself](http://xion.org.pl/2012/05/06/hacking-python-imports/).
Surely it would be possible — nay, easy — to get the source code of a short lambda function, right?

Boy, was I _wrong_.

Make no mistake, though: the task turned out to be absolutely doable, at least in the scope I wanted it done.
But what would you think of a solution that involves not just the usual Python hackery, but also AST inspection,
transformations of the source code as text, _and_ bytecode shenanigans?…

#### The code, all the code, and… much more than the code

Let’s start from the beginning, though. Here’s a short lambda function, the kind of which we’d like to obtain
the source code of:
```python
is_even = lambda x: x % 2 = 0
```

If the documentation for Python standard library is to be believed, this should be pretty easy.
In the [`inspect` module](https://docs.python.org/2/library/inspect.html),
there is a function called no different than
[`getsource`](https://docs.python.org/2/library/inspect.html#inspect.getsource). For our purposes, however,
[`getsourcelines`](https://docs.python.org/2/library/inspect.html#inspect.getsourcelines) is a little more
convienient, because we can easily tell when the lambda is too long:
```python
def get_short_lambda_source(lambda_func):
    try:
        source_lines, _ = inspect.getsourcelines(lambda_func)
    except IOError:
        return None
    if len(source_lines) > 1:
        return None
    return source_lines[0].strip()
```

Of course if you programmed in Python for any longer period of time, you know very well that the standard docs
are _not_ to be trusted. And it’s not just that the `except` clause should also include `TypeError`, because it
will be thrown when you try to pass any of the Python builtins to `getsourcelines`.

More important is the ambiguity of what does “source lines for an object” actually mean. “Source lines _containing_
the object definition” would be much more accurate, and this seemingly small distinction is rather crucial here.
Passing a lambda function to either `getsourcelines` or `getsource`, we’ll get its source _and everything else_
that the returned lines included.

That’s right. Say hello to the complete `is_even =` assignment, and the entire `assert_called_with` invocation!
And in case you are wondering: yes, the result will also include any end-of-line comments. No token left behind!

#### Trim left

Clearly this is more than we’ve bargained for. Maybe there is a way to strip away the unnecessary cruft? Python does
know how to parse itself, after all: the standard [`ast` module](https://docs.python.org/2/library/ast.html)
is a manifestation of this knowledge. Perhaps we can use it to retrieve the `lambda` AST node in order to turn it —
and just it — back into Python code?…
```python
def get_short_lambda_ast_node(lambda_func):
    source_text = get_short_lambda_source(lambda_func)
    if source_text:
        source_ast = ast.parse(source_text)
        return next((node for node in ast.walk(source_ast)
                     if isinstance(node, ast.Lambda)), None)
```

But as it turns out, getting the source text back this way is only _mostly_ possible.

See, every substantial AST node — which is either an expression (`ast.expr`) or a statement (`ast.stmt`) —
has two common attributes: `lineno` and `col_offset`. When combined, they point to a place in the original source code
where the node was parsed from. This is how we can find out where to look for the definition of our lambda function.

Looks promising, right? The only problem is we don’t know when to _stop_ looking.
That’s right: nodes created by `ast.parse` are annotated with their start offset, but not with length nor the end offset.
As a result, the best we can do when it comes to carving out the lambda source from the very first example is this:
```python
lambda x: x % 2 == 0))
```

So close! Those hanging parentheses are evidently just taunting us, but how can we remove them? `lambda` is basically
just a Python expression, so in principle it can be followed by almost anything. This is doubly true for lambdas inside
the `Matching` construct, as they may be a part of some larger mock assertion:
```python
mock_foo.assert_called_with(Matching(lambda x: x % 2 == 0), Integer() & GreaterThan(42))
```

Here, the extraneous suffix is the entirety of `), Integer() &amp; GreaterThan(42))`, quite a lot of more than just `))`.
And that’s of course nowhere near the limit of possiblities: for one, there may be more `lambda`s in there, too!

#### Back off, _slowly_

It seems, however, that there is one thing those troublesome tails have in common: _they aren’t syntactically valid_.

Intuitively, a `lambda` node nested within some other syntactical constructs will have their closing fragments (e.g. `)`)
appear somewhere after its end. Without the corresponding openings (e.g. `Matching(`), those fragments won’t parse.

So here’s the crazy idea. What we have is invalid Python, but only because of some unspecified number of extra characters.
How about we just try and remove them, one by one, until we get something that _is_ syntactically correct?
If we are not mistaken, this will finally be our lambda and nothing else.

The fortune favors the brave, so let’s go ahead and try it:
```python
# ... continuing get_short_lambda_source() ...

source_text = source_lines[0].strip()
lambda_node = get_short_lambda_ast_node(lambda_func)

lambda_text = source_text[lambda_node.col_offset:]
min_length = len('lambda:_')  # shortest possible lambda expression
while len(lambda_text) > min_length:
    try:
        ast.parse(lambda_text)
        return lambda_text
    except SyntaxError:
        lambda_text = lambda_text[:-1]
return None
```

Considering that we’re basically taking lessons from the dusty old tomes in the Restricted Section of Hogwarts library,
the magic here looks quite simple. As long as there is something that can pass for a lambda definition,
we try to parse it and see if it succeeds. The line that says `except SyntaxError:` is obviously not something for
the faint of heart, but at least we are specifying
[_what_ exception](https://docs.python.org/2/howto/doanddont.html#except) we anticipate catching.

And the kicker? It _works_. By that I mean it doesn’t return garbage results for a few obvious and not so obvious
test cases, which is already more than you would normally expect from hacks of this magnitude.
All the lambdas defined until this paragraph, for example, can have their source code extracted without issue.

#### Just one more thing

So… victory? Not quite. Astute readers may recall my promise of some bytecode arcana, and now’s the time for it.

Despite the initial success of our gradual, character dropping approach, there are cases where it doesn’t produce
the correct result. Consider, for example, a lambda definition that’s nestled within a tuple[2](#fn:2):
```python
>>> x = lambda _: True, 0
>>> get_short_lambda_source(x[0])
lambda _: True, 0
```

We would of course expect the result to be `lambda _: True`, without a comma or zero.

Unfortunately, here’s where our earlier assumption fails rather spectacularly. The line of code extracted from AST
is syntactically valid even _with_ the extra characters. As a result, `ast.parse` succeeds too early and returns an
incorrect definition. It should have been of a lambda contained within a tuple, but tuple is apparently what the lambda
_returns_.

You may say that this is the sharp end of a narrow edge case, and anyone who defines functions like that deserves all
the trouble they get. And sure, I wouldn’t mind if we just threw hands in the air and tell them we’re simply unable
to retrieve the source here. But my opinion is that it doesn’t justify serving them obviously _wrong_ results!

#### A halting problem

Not if we can help it, anyway. Have a look at the expected source code and the one we’ve extracted, side by side:
```python
lambda _: True
lambda _: True, 0
```

The second line isn’t just longer: it is also _doing more_. It isn’t just defining a lambda; it defines it,
conjures up a constant `0`, and then packs them both into a tuple. That’s at least two additional steps compared to
the original.

Those steps have a more precise name, too: they are the _bytecode instructions_. Every piece of Python source is compiled
to a binary bytecode before it’s executed, because the interpreter can only work with this representation.
Compilation typically happens when a Python module is first imported, producing a _.pyc_ file corresponding to its
_.py_ file. Subsequent imports will simply reuse the cached bytecode.

Moreover, any function or class object has its bytecode accessible (read-only) at runtime. There is even a
[dedicated data type](http://late.am/post/2012/03/26/exploring-python-code-objects.html) to hold it — called simply
`code` — with a buffer of raw bytes under one of its attributes.

Finally, the bytecode compiler itself is also available to Python programs as a built-in
[`compile` function](https://docs.python.org/2/library/functions.html#compile). You don’t see it used as often as its
counterparts [`eval`](https://docs.python.org/2/library/functions.html#eval)
and [`exec`](https://docs.python.org/2/reference/simple_stmts.html#exec) (which hopefully are a rare sight themselves!),
but it taps into the same internal machinery of Python.

So how does it all add up? The idea is, basically, to cross-check the alleged source code of the lambda with its own
_byte_code. Any junk that’s still left to trim — even if syntactically valid — will surface as a divergence after
compilation. Thus we can simply continue dropping characters until the bytecodes match:
```python
lambda_text = source_text[lambda_node.col_offset:]
lambda_body_text = source_text[lambda_node.body.col_offset:]
min_length = len('lambda:_')  # shortest possible lambda expression
while len(lambda_text) > min_length:
    try:
        code = compile(lambda_body_text, '<unused filename>', 'eval')
        if len(code.co_code) == len(lambda_func.__code__.co_code):
            return lambda_text
    except SyntaxError:
        pass
    lambda_text = lambda_text[:-1]
    lambda_body_text = lambda_body_text[:-1]
return None
```

Okay, maybe not the exact bytes[3](#fn:3), but stopping at the identical bytecode _length_ is good enough a strategy.
As an obvious bonus, `compile` will also take care of detecting syntax errors in the candidate source code,
so we don’t need the `ast` parsing anymore.

#### That escalated quickly!

Believe it or not, but there aren’t any more objections to this solution, You can view it in its glorious entirety
by looking at [this gist](https://gist.github.com/Xion/617c1496ff45f3673a5692c3b0e3f75a).

Does it mean it is also making its cameo in the [_callee_ library](https://github.com/Xion/callee)?…

No, I’m afraid not.

Normally, I’m not the one to shy away from, ahem, _bold_ solutions to tough problems. But in this case, the magnitude
of hackery required is just too great, the result not satisfactory enough, the feature’s priority isn’t really
all that high, and the maintenance burden it’d introduce is most likely too large.

In the end, it was great fun figuring it out: yet another example of how you can fiddle with Python to do basically
anything. Still, we must not get too preoccupied with whether or not we can as to forget if we _should_.


* * *

1.  Backslash (`\`) is how lambda functions are denoted in Haskell. We want to be short and sweet, so it feels
like a natural choice. [↩](#fnref:1 "Jump back to footnote 1 in the text")

2.  This isn’t an actual snippet from a Python REPL, because `inspect.getsourcelines` requires the object to be
defined in a _.py_ file. [↩](#fnref:2 "Jump back to footnote 2 in the text")

3.  Why we won’t always get an identical bytecode? The short answer is that some instructions may be swapped
for their approximate equivalents.

    The long answer is that with `compile`, we aren’t able to replicate the exact closure environment of the original lambda.
When a function refers to an _free variable_ (like `foo` in `lambda x: x + foo`), it is its closure where the value
for that variable comes from. For ad-hoc lambdas, this is typically the local scope of its _outer function_.

    Code produced by `compile`, however, isn’t associated with any such local scope. All free names are thus assumed
to refer to _global_ variables. Because Python uses different bytecode instructions for referencing local and global
names (`LOAD_FAST` vs `LOAD_GLOBAL`), the result of `compile` may differ from a piece of bytecode produced in the regular
manner. [↩](#fnref:3 "Jump back to footnote 3 in the text")