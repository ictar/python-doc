原文：[Static types in Python, oh my(py)!](http://blog.zulip.org/2016/10/13/static-types-in-python-oh-mypy/)

---

Over the last few years, static type checkers have become available for popular dynamic languages like PHP ([Hack](http://hacklang.org/)) and JavaScript ([Flow](https://flowtype.org/) and [TypeScript](https://www.typescriptlang.org/)), and have seen wide adoption. Two years ago, a [provisional syntax for static type annotations](https://www.python.org/dev/peps/pep-0484/) was added to Python 3\. However, static types in Python have yet to be widely adopted, because the tool for checking the type annotations, [mypy](http://mypy-lang.org/), was not ready for production use… until now!

The exciting news is that over the last year, a team at Dropbox (including Python creator Guido van Rossum!) has led the development of mypy into a mature type checker that can enforce static type consistency in Python programs. For the many programmers who work in large Python 2 codebases, the even more exciting news is that mypy has full support for type-checking Python 2 programs, scales to large Python codebases, and can substantially simplify the upgrade to Python 3\.

The Zulip development community has seen this in action during 2016\. [Zulip](https://zulip.org/) is a popular open source group chat application, complete with apps for all major platforms, a REST API, dozens of integrations, etc. To give you a sense of scale, Zulip is about 50,000 lines of Python 2, with dozens of developers contributing hundreds of commits every month. During 2016, we have annotated 100% (!) of our backend with static types using mypy, and thanks to mypy, we are on the verge of switching to Python 3\. Zulip is now the largest open source Python project that has fully adopted static types, though I doubt we’ll hold that title for long :).

In this post, I’ll explain how mypy works, the benefits and pain points we’ve seen in using mypy, and share a detailed guide for adopting mypy in a large production codebase (including how to find and fix dozens of issues in a large project in the first few days of using mypy!).

# A brief introduction to mypy

Here is an example of the clean mypy/PEP-484 annotation syntax in Python 3:

```
def sum_and_stringify(nums: List[int]) -> str:  
    """Adds up the numbers in a list and returns the result as a string."""
    return str(sum(nums))

```

And here’s how that same code looks using the comment syntax for both Python 2 and 3:

```
def sum_and_stringify(nums):  
    # type: (List[int]) -> str
    """Adds up the numbers in a list and returns the result as a string."""
    return str(sum(nums))

```

With this comment syntax, mypy supports type-checking normal Python 2 programs, and programs annotated using mypy will run normally with any Python runtime environment (mypy shares this excellent property with the Flow JavaScript type checker). This is awesome because it means projects can adopt mypy without changing anything about how they run Python.

You run mypy on your codebase like a linter, and it reports errors in a nice compiler-style format. For example, here’s what the mypy output would be if I’d incorrectly annotated `sum_and_stringify` as returning a float:

```
$ mypy /tmp/test.py 
/tmp/test.py: note: In function "sum_and_stringify":
/tmp/test.py:6: error: Incompatible return value type: expected builtins.float, got builtins.str

```

If you’re curious how to annotate something, check out the [mypy syntax cheat sheet](http://mypy.readthedocs.io/en/latest/cheat_sheet.html) (for simple things) and [PEP-484](https://www.python.org/dev/peps/pep-0484/) (for complex things); they’re great resources. If you want to play with mypy now, you can install it with `pip3 install mypy-lang`.

When mypy has complete type annotations for a module as well as its dependencies, then it can provide a very strong consistency check, similar to what the compiler provides in statically typed languages. Mypy uses the [typeshed](https://github.com/python/typeshed) repository of type “stubs” (type definitions for a module in the style of a header file) to provide type data for both the Python standard library and dozens of popular libraries like requests, six, and sqlalchemy. Importantly, mypy is designed for gradually adding types; if type data for an import isn’t available, it just treats that import as being consistent with anything.

# Benefits of using mypy

Here are the benefits we’ve seen from adopting mypy, starting with the most important:

*   **Static type annotations improve readability**. Improved readability is probably the most important benefit of statically typed Python. With static types, every function clearly documents the types of its arguments and return value, which saves a ton of time reading the codebase to figure out the precise types a function expects so that you can call it. In many large codebases, developers write docstrings that contain little information other than the types of the arguments and often end up out of date. Type annotations are far better since they are automatically verified for correctness and consistency. It turned out that the code that we found most difficult to annotate was precisely the code where the type annotations improved readability the most: the cases where it was totally unclear from the code alone what all the objects being passed around are.
*   **We can refactor with confidence**. Refactoring a Python codebase without breaking things involves a lot of careful manual checking to confirm that you’ve updated all the code that used the old interface. With a statically typed Python codebase, the type checker can often verify this for you automatically. We’ve already found this to be [super useful when refactoring Zulip](https://github.com/zulip/zulip/commit/eb09dd217dbd5d9c214b14a5b28b3a12cf8b7854).
*   **We upgraded to Python 3**. Thanks to mypy, Zulip now supports Python 3\. For years, the 2to3 library has been able to do automatically most of the changes required to support Python 3\. The hard part of the migration is the unicode issue: Python 3 is much more strict about the distinction between arrays of bytes and strings than Python 2\. Mypy annotations made this project much easier, since we could explicitly declare which values were which across the entire codebase, and check those declarations for consistency. Even in Zulip, which has an unusually comprehensive automated test suite, mypy caught many Python 3 issues that the tests did not find.
*   **mypy frequently catches bugs**. When we started using mypy, Zulip was a mature web application with unusually high test coverage, so we didn’t find any super embarrassing bugs as part of annotating the codebase. However, mypy [flagged](https://github.com/zulip/zulip/commit/620411c0ea757c9f3f0ae08597e77de84f20ee97) [dozens](https://github.com/zulip/zulip/commit/d77c70220c91a47b06695409e178bb006fe996e8) [of](https://github.com/zulip/zulip/commit/fc02ea9f674e44d99eb12528fceaed64a950bbed) [latent](https://github.com/zulip/zulip/commit/e9f39922a09d2aad90de63a349abe408e93edd4d) [bugs](https://github.com/zulip/zulip/commit/7595e4b05fe69a786cf83cad9c8a77f6e47b2f0f), and dozens more [pieces](https://github.com/zulip/zulip/commit/eee36618fe297bf306c19a7deb11941b53680272) of [confusing](https://github.com/zulip/zulip/commit/c55ac01ae69ef1ca8ce8315ec4b179ec3ebb46a6) code. I’m glad that mypy helped us purge those categories of issues from the codebase. More important for us is that running mypy on code that hasn’t yet been reviewed or tested regularly saves time by catching bugs.
*   **Static types highlight bad interfaces**. Annotated functions with messy interfaces (e.g sometimes returning a `List` and sometimes a `Tuple` ) really stand out.

# Pain points

To provide a complete picture of the experience of adopting mypy, I think it’s important to also talk about the pain points with mypy today:

*   **Interactions with “unused import” linters**: Currently, linters like `pyflakes` don’t read the Python 2 type annotations style (since the annotations are comments, after all!), and thus will report that imports needed for mypy are unused. Since cleaning up unused imports is only marginally useful anyway, we just filtered out those warnings. This problem will go away when we move to the new Python 3 type syntax, since then type annotations are visible “users” of the imports to tools like pyflakes; I also expect that popular Python linters will add an option to check imports in type comments before long.
*   **Import cycles**: We needed to create a few import cycles in order to import types that were used as arguments (but not explicitly referenced) in a file. One can work around this (in the Python 2 comment syntax) using e.g. `if False: from x import y` (soon to be the cleaner `if typing.TYPE_CHECKING` ), but it won’t work when we move to the Python 3 syntax. I’m hopeful that someone will come up with a nice solution to the import cycle creation issue, which may just take the form of recommendations on how to organize one’s code to avoid this.

## Non-pain points

This section discusses the things that (before trying mypy) I was worried might be problems, but that after adopting mypy I don’t think are significant issues:

*   **Training**. Zulip has had well over 100 contributors at this point, so the thing I was most worried about when adopting mypy was that it could hurt the project by adding a new obscure technology that contributors need to learn. This has not turned out to be a material problem: new contributors usually annotate their code correctly on the first try. Python programmers know the Python type system, and the PEP-484 type syntax is a pretty intuitive way of writing it down. The [mypy cheat sheet](http://mypy.readthedocs.io/en/latest/cheat_sheet.html) helps a lot by providing a single place to look up all the common stuff.
*   **False positives**. Mypy is well-engineered and designed around the idea of only reporting things that are actual inconsistencies in a program’s types. It has a far lower false positive rate than the commercial static analyzers I’ve used! And, it has a really nice `type: ignore` system for silencing false positives to get clean output. Finally, we’ve had good luck with typing even some highly dynamic parts of our system (e.g. our [framework for parsing REST API requests](http://zulip.readthedocs.io/en/latest/writing-views.html#request-variables)).
*   **Mypy and typeshed bugs**. Zulip started using mypy in January 2016, when mypy itself was essentially the only open source project using mypy. Because we were the first webapp and the second large project using mypy seriously, we encountered a lot of small bugs at the beginning (in total, Zulip developers have reported ~50 issues in mypy and submitted 16 PRs for typeshed). Essentially all of those issues were fixed upstream (with tests!) months ago, and we rarely encounter regressions. So, I wouldn’t expect similar projects to encounter a similar scale of issues; exactly how many issues an individual project encounters will depend a lot on whether that project uses the same corners of Python as other projects that already use mypy and typeshed. Even as a super early adopter, I feel like we spent far more time annotating our code than investigating, working around with `type: ignore` , and reporting bugs.
*   **Performance.** Mypy today is really fast compared to other static analyzers I’ve used. With the mypy module cache, it takes about 3s to check the entire Zulip codebase and thus is about as fast as pyflakes (one of the faster Python linters). However, because mypy detects cross-file issues (unlike most linters), it doesn’t have a really fast (e.g. <100ms) way to fully check if a given diff/commit introduced a new issue.
*   **Community**. The mypy and typeshed communities are amazing and are impressively responsive to good bug reports (aka ones with a clean, simple reproducer, which I can almost always generate in under 5 minutes).

# Finding bugs in your first few days with mypy

This section details what you need to do in order to start benefiting from mypy in a large codebase. To give you a sense of the scale of work involved, I did everything described in this section in 4 days at a hackathon in January (and back then, mypy wasn’t mature, so I spent half the time filing good bug reports for all the issues I found). If you’re thinking about using mypy but want more data to help make a decision, I’d recommend completing the steps discussed in this section. It’s a pretty good effort/value tradeoff.

**Read the mypy cheat sheet.** The [mypy cheat sheet](http://mypy.readthedocs.io/en/latest/cheat_sheet.html) provides a great overview of the PEP-484 syntax, and you’ll be referring to it often as you start writing annotations.

**Standardize how you’ll run mypy**. Write tooling to [install](https://github.com/zulip/zulip/blob/master/tools/install-mypy) and [run](https://github.com/zulip/zulip/blob/master/tools/run-mypy) `mypy` against your codebase, so that everyone using the project can run the type checker the same way. Two features are important in how you run mypy:

*   Support for determining which files should be checked (a whitelist/exclude list is useful!).
*   Specifying the correct flags for your project at this time. For a Python 2 project, I recommend starting with `mypy --py2 --silent-imports --fast-parser -i <paths>`. You should be able to do this using a [mypy.ini file](http://mypy.readthedocs.io/en/latest/config_file.html).

**Get mypy running** on your codebase with clean output. This usually requires just adding type annotations for empty global data structures. Back in January, this took a few hours of work (including reporting bugs with reproducers). It’s probably even less work now. By default, mypy will only check the interior of functions that are annotated, so with an unannotated codebase, this is just making sure mypy can analyze your entire codebase.

**Check for basic consistency**. Add the `--check-untyped-defs` option to your mypy arguments, and get that running with no errors on the codebase. This option causes mypy to check every `def` in the codebase for internal consistency; mypy can detect many classes of bugs and mistakes in your codebase, without your having written a single type annotation!

In most cases, you’ll want to fix bugs and bad code as you go, but you can also use `#type: ignore` annotations or exclude files to defer issues. For example, we excluded all of Zulip’s tests at first, since they’re both lower value to type-check and had most of the monkey-patching and other questionable Python in the project. For Zulip, getting clean `--check-untyped-defs` output took me about 2 days of hard work, including merging fixes for about 40 issues in the Zulip codebase.

I spent another day or two generating nice reproducers for the mypy bugs I’d encountered and improving typeshed. Now that mypy is no longer in its infancy, mypy bugs are increasingly rare. But a large project should expect to encounter and fix typeshed issues (just submit a PR!).

**Run mypy in continuous integration**. Once `mypy --check-untyped-defs` passes on your codebase, you should lock down your progress by running the `mypy` checker in your CI environment.

Because mypy type annotations are optional, once you’ve done the setup above, you can start annotating your codebase at whatever pace you like. Over time, you’ll get the benefits of static types in the parts of the codebase that you’ve annotated, without needing to do anything special to the rest of the codebase (the wonders of gradual typing!). In the next section, I’ll discuss strategy for how to move the codebase towards being fully annotated.

# Fully annotating a large codebase

This section discusses what’s involved in going from having mypy set up to having a fully annotated codebase.

The great thing about mypy is that you can do everything gradually. After the initial setup, we actually didn’t do anything with mypy for a couple months. That changed when we posted mypy annotations as one of our project ideas for Google Summer of Code (GSoC). We found an incredible student, Eklavya Sharma, for the project. Eklavya did the vast majority of the hard work of annotating Zulip, including upgrading our tooling, annotating the core library, contributing bug reports and PRs to mypy and typeshed upstream, and fixing all the mistakes we made early on. Amazingly, he also found the time during the summer to migrate Zulip to use virtualenvs and then upgrade Zulip to Python 3!

One can divide the work of annotating a large project into a few phases.

**Phase 1: Annotate the core library**. Strategically, you want to annotate the core library code that is used in lots of other files first. The annotations for these functions impose constraints on the types used in the rest of the codebase, so you’ll spend less time fixing incorrect annotations and catch more actual bugs faster if you do these files first. This is also a good time to [document how your project is using mypy](http://zulip.readthedocs.io/en/latest/mypy.html) (and link that doc from mypy failures in the CI system).

**Phase 2: Annotate the bulk of the codebase**. Many projects will likely do this slowly over many months as developers touch the different parts of the codebase, which is a pretty reasonable strategy.

It also works well to annotate a codebase as a focused effort; the story of how we did this for Zulip is instructive. About halfway through Eklavya’s summer of code, we went to the [PyCon sprints](https://us.pycon.org/2016/community/sprints/) with the goal of annotating as much of Zulip as possible. The PyCon sprints are my favorite part of PyCon. It’s an awesome 4-day party after the main PyCon conference where hundreds of developers work on open source projects together. It’s completely free to attend, and is a great opportunity to get involved in contributing to open source projects.

We grabbed a few tables next to the mypy developers, and managed to attract a rotating cast of 5–10 developers to the Zulip mypy annotation project each day. During the PyCon sprints, Zulip went from 17% annotated to 85% (with 25–30 engineer-days of work, mostly by folks new to both Zulip and mypy). We used mypy’s coverage support and coveralls.io to track progress, but the more fun progress bar was our giant sheet of paper, pictured here at the start of the last day:

![Zulip mypy Coverage Goal](https://d2mxuefqeaa7sj.cloudfront.net/s_75D610ACC7DE41FD41A47CCE7C098AB12F65B4E72F59B74EBE6F6100D75C117F_1475462832801_thermometer-pycon-sprint.jpg)

Our PyCon experience is I think the best proof that mypy is accessible to new developers: aside from me, all of the contributors adding annotations were new to both Zulip and mypy. We found that with a good 5-minute demo and good documentation, new contributors were effective within their first hour of working with mypy. I would definitely recommend the mypy hackathon approach to other open source projects — it’s a great way for contributors to have meaningful impact on an unfamiliar project.

**Phase 3: Get to 100%.** Annotating the last several files is relatively difficult work, because this is where you end up debugging all the mistakes you made in Phase 2\. While you do this, it’s important to lock down files/directories that reach 100% by adding the `--disallow-untyped-defs` option (which will report any functions missing type annotations) to the `mypy` flags to prevent regressions.

Eklavya brought us from 85% to 96% before his college started up again, and then a few weeks ago we spent the couple hours needed to get to 100%. Now, all new Python code going into Zulip comes with mypy annotations (with the exception of a shrinking list of scripts, settings, and test files).

**Phase 4: Celebrate and write a blog post**! At least, that was the next step for Zulip :)

Overall, the focused time that went into fully annotating Zulip was a 1-week hackathon, a GSOC project, and a party at the PyCon sprints. In the scheme of things, this is a pretty modest level of effort.

I should mention that even though Zulip is 100% annotated, Zulip’s mypy journey is not complete. We will eventually want to add stubs to typeshed for the most important libraries used by Zulip (e.g. Django).

# Recommendations for annotating code

We have a few recommendations for annotating that will likely save you a lot of time:

*   Make sure to handle `str` vs. `Text` correctly. Bytes vs. str and str vs. unicode errors are the majority of the problem for moving a codebase from Python 2 to Python 2+3\. If you do this right as you annotate your codebase, you’ll save yourself a lot of time when you upgrade to Python 3; we found the Python 3 upgrade went very quickly once we had the codebase mostly annotated. We ended up adding [a couple helper functions](https://github.com/zulip/zulip/blob/master/zerver/lib/str_utils.py) to do casts between `str` , `Text` , and `bytes` correctly (and readably!) in our codebase on both Python 2 and Python 3.
*   Remember to annotate class variables! We neglected to do this when we first annotated our Django models file, and ended up having to fix a number of incorrect annotations (primarily around Text vs. bytes) that got into the codebase because they weren’t being cross-checked against anything.
*   Avoid using a guess-and-check approach when adding annotations. In a partially annotated codebase, mypy will still detect many classes of errors in annotations, but it can’t detect every error (since the inconsistency might be with code you haven’t annotated yet). So you should make sure people writing annotations are actually understanding/tracing the code, and that you code review the annotations just like actual code. We found writing one commit per large file (or collection of related smaller files) to be a good commit discipline.
*   Use precise types where possible (e.g. avoid using `Any` ).
*   When using `type: ignore` to work around a potential mypy or typeshed bug, I recommend using the following style to record the original GitHub issue:

    `bad_code # type: ignore # https://github.com/python/typeshed/issues/372`

    That way, it’s easy for future you to verify whether the issue that required that use of `type: ignore` has since been fixed upstream. If a file would require a lot of `type: ignore` annotations, you can always add it to the exclude list (another feature of our `run-mypy` wrapper) and plan to come back to it later.

# Conclusion

Overall, the experience of using mypy (and the PEP-484 type system) has been awesome, and we feel that adopting mypy has been a big advance for the Zulip project. It improves readability, catches bugs in code without running it, has very few false positives, and hasn’t come with significant downsides. Deploying mypy in a large codebase required relatively little investment on our part, and annotating the codebase had the side benefit of making our Python 3 migration feel easy.

If you have a large Python codebase and would like to make working in your codebase better, you should find a week to get started with mypy!

Finally, if you’re excited to see what static types in Python look like in a large codebase, check out the [Zulip server project on GitHub](https://github.com/zulip/zulip/). We welcome new contributors!

Huge thanks to Guido van Rossum, Alya Abbott, Steve Howell, Jason Chen, Eklavya Sharma, Anurag Goel, and Joshua Simmons for their feedback on this post.
