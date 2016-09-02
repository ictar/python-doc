原文：[Python, Machine Learning, and Language Wars - A Highly Subjective Point of View](http://sebastianraschka.com/blog/2015/why-python.html)

---

噢，天呀，那些主观有针对性的，自以为标题党的文章的另一个？是哒！为什么我还要不厌其烦的写下来呢？嗯，这里是来自于我的前教授的最琐碎但又改变生活的洞察和世俗的智慧之一，它已经成为了我的口头禅了：“如果你必须做这个任务超过三次以上，那么只要写一个脚本，然后对其自动化。”

现在，你或许已经开始琢磨这个博客了。我已经超过半年没写什么东西了！好吧，[沉迷在社交网络平台](https://twitter.com/rasbt)除外，那不是真的：我写了[一些东西](https://github.com/rasbt/python-machine-learning-book) —— 准确来说，约400页。最近，对我来说，这真的已经是一次旅程了。而对于经常被问道的问题“为什么你选择Python来进行机器学习？”，我猜，是时候来写_我的脚本_了。

在下面的段落中，我真的不打算告诉你为什么_你_或者其他人应该使用Python。老实说，我真心讨厌那类问题：“哪个*最好？”（这里，用“编程语言、文本编辑器、IDE、操作系统、计算机制造商”替换掉*）。这实在是扯淡。虽然有时它挺有意思的，但是我建议你节省下关于这个问题的时间，用来在下班后跟朋友或者同事偶尔喝喝啤酒或咖啡。

#### 目录

  * 对于一个复杂问题的简短回答
  * What are my favorite Python tools?
  * What do I think about MATLAB?
  * Julia is awesome … on paper!
  * There is really nothing wrong with R
  * What happened to Perl?
  * Other options
  * Is Python a dying language?
  * Conclusion
  * Feedback-and-opinions

## 对于一个复杂问题的简短回答

或许我应该从一个简短的回答开始。欢迎你停止阅读这段后面的文章，因为它真的解决掉这个问题了。我是一个科学家，我喜欢完成我的工作。我喜欢有一个环境，在那里我可以快速原型，并记下我的模型和想法。我需要解决非常特殊的问题。我分析给定的数据集以得出结论。这对我来说是最重要的：我怎样才能最多产的完成我的工作呢？“多产”这里意味着什么？好吧，我通常只进行一次分析 (不同的想法测试和调试除外); 我不需要重复地24/7地运行一段特定的代码，我并不是在为最终用户开发软件应用或web应用。当我_量化y_ “多产”时，我从字面上评估(1) 把想法以代码的形式写下来所花费的时间，(2) 调试的时间和 (3) 执行的时间之和。对我来说，“最多产”意味着“获得结果需要花费多少时间？” 现在，这么多年来，我发现，Python就是为我而生的。并非总是如此，但很多时候是这样。正如生活中的其他东西一样，Python并不是“银弹”，它并非总是每一个问题的“最佳”解决方案。然而，如果你跨常见和不那么常见的问题任务来比较编程语言的话，它已经非常接近（最佳解决方案）了；Python可能是最通用，最有能力的全才。

![](http://sebastianraschka.com/images/blog/2015/why-python/the_general_problem.png)

(来源: <https://xkcd.com/974/>)

请记住：“过早的优化是一切罪恶的根源” (Donald Knuth)。如果你是那种想要从机器学习和数据科学划分中中优化下一个颠覆性高频交易模型的软件工程团队中的一员，那么Python可能不适合你 (但或许它是数据科学团队的语言选择，所以学习如何读懂它仍然有用)。因此，我的一个小小的忠告是，当你选择一门语言时，评估你每天的问题任务和需求。“如果你只有一把锤子，那么一切开始看起来都像一个钉子” – 你聪明得不会掉入这个陷阱！然而，记住，有一个平衡点。在有些场景下，即使螺丝刀可能是“更漂亮的”解决方法，锤子可能还是最好的选择。再次，这归结为生产力。

**让我从个人经历中挑个例子来说说。** I needed to develop a bunch of novel algorithms to “screen” 15 million small, chemical compounds with regard to a very problem specific hypothesis. I am an entirely computational person, but I am collaborating with biologists who do non-computational experiments (we call them “wet lab” experiments). The goal was to narrow it down to a list of 100 potential compounds that they could test in their lab. The caveat was that they needed the results quickly, because they only had limited time to conduct the experiments. Trust me, time was really “limited:” We just got our grant application accepted and research funded a few weeks before the results had to be collected (our collaborators were doing experiments on larvae of a certain fish species that only spawns in Spring). Therefore, I started thinking “How could I get those results to them as quickly as possible?” Well, I know C++ and FORTRAN, and if I implement those algorithms in the respective languages executing the “screening” run may be faster compared to a Python implementation. This was more of an educated guess, I don’t really know if it would have been substantially faster. But there was one thing I knew for sure: If I started developing the code in Python, I could be able to get it to run in a few days – maybe it would take a week to get the respective C++ versions coded up. I would worry about a more efficient implementation later. At that moment, it was just important to get those results to my collaborators – “Premature optimization is the root of all evil.” On a side node: The same train of thought applies to data storage solutions. Here, I just went with SQLite. CSV didn’t make quite sense since I had to annotate and retrieve certain molecules repeatedly. I surely didn’t want to scan or rewrite a CSV from start to end every time I wanted to look up a molecule or manipulate its entry – issues in dealing with memory capacities aside. Maybe MySQL would have been even better but for the reasons mentioned above, I wanted to get the job done quickly, and setting up an additional SQL server … there was no time for that, SQLite was just fine to get the job done.

![](http://sebastianraschka.com/images/blog/2015/why-python/automation.png)

(来源: <https://xkcd.com/1319/>)

The verdict: **Choose the language that satisfies _your_ needs!** However,
there is once little caveat here! How can a beginning programmer possibly know
about the advantages and disadvantages of a language before learning it, and
how should the programmer know if this language will be useful to her at all?
This is what I would do: Just search for particular applications and solutions
related to your most common problem tasks on Google and
[GitHub](https://github.com). You don’t need to read and understand the code.
Just look at the end product. Also, don’t hesitate to ask people. Don’t just
ask about the “best” programming language in general but be specific, describe
your goals and why you want to learn how to program. If you want to develop
applications for MacOS X you will probably want to check out Objective C and
Swift, if you want to develop on Android, you are probably more interested in
learning Java and so on. ⇧

## 我最喜欢的Python工具是什么？

如果你感兴趣，那些事我最喜欢并且最常使用的Python“工具”，每天，我都会使用它们中的大部分。

  * [NumPy](http://www.numpy.org): 我处理线性代数阵列结构和量化公式最喜欢的库；由[SciPy](http://scipy2015.scipy.org/ehome/index.php?eventid=115969&)增强。
  * [Theano](https://theano.readthedocs.org/en/latest/): 为机器学习算法减负，并将计算分布到我的GPU内核中。
  * [scikit-learn](http://scikit-learn.org/stable/): 用于每日、更基本的机器学习任务的最方便的API。
  * [matplotlib](http://matplotlib.org): 当涉及到画图时，这是我所选择的库。有时，我还使用[seaborn](http://stanford.edu/~mwaskom/software/seaborn/index.html)来绘制特殊的图，例如，热图超级棒！

![](http://sebastianraschka.com/images/blog/2015/why-python/heatmap.png)

(来源: <http://stanford.edu/~mwaskom/software/seaborn/examples/structured_heatmap.html>)

  * [Flask (Django)](http://flask.pocoo.org): 难得，我想要将一个想法转换成一个web应用。这里，Flask非常方便！
  * [SymPy](http://www.sympy.org/en/index.html): 对于符号数学，对我来说，它取代了WolframAlpha。
  * [pandas](http://pandas.pydata.org): 处理相当小的数据集，大多数来自于CSV文件。
  * [sqlite3](https://docs.python.org/2/library/sqlite3.html):注释和查询“中型”数据集。
  * [IPython notebooks](http://ipython.org): 我还能说什么呢？我90%的研究发生在IPython notebooks。它只是一个让所有的东西都放在一个地方的好环境：想法、代码、注释、LaTeX方程、插图、图表、输出……

![](http://sebastianraschka.com/images/blog/2015/why-python/ipython_notebook.png)

注意，IPython项目最近演变成了[Jupyter项目](https://jupyter.org)。现在，你不止可以使用Jupyter notebook环境到Python上，还可以是R, Julia, 等等。

## 我对MATLAB是怎么看的？

I used MATLAB (/Octave) quite extensively some years ago; most of the computer
science-data science classes were taught in MATLAB. I really think that it’s
not a bad environment for prototyping after all! Since it was built with
linear algebra in mind (MATLAB for MATrix LABoratory), MATLAB feels a tad more
“natural” when it comes to implementing machine learning algorithms compared
to Python/NumPy – okay, to be fair, [1-indexed](https://www.cs.utexas.edu/user
s/EWD/transcriptions/EWD08xx/EWD831.html) programming languages may seem a
little bit weird to us programmers. However, keep in mind that MATLAB comes
with a big price tag, and I think it is slowly fading from academia as well as
industry. Plus, I am a big fan open-source enthusiast after all ;). In
addition, its performance is also not that compelling compared to other
“productive” languages looking at the benchmarks below:

![](http://sebastianraschka.com/images/blog/2015/why-python/julia_benchmark.png)

(Benchmark times relative to C – smaller is better, C performance = 1.0;
来源: <http://julialang.org/benchmarks/>)

However, we should not forget that there is also this neat Theano library for
Python. In 2010, the developers of Theano reported an 1.8x faster performance
than NumPy when the code was run on the CPU, and if Theano targeted the GPU,
it was even 11x faster than NumPy (J. Bergstra, O. Breuleux, F. Bastien, P.
Lamblin, R. Pascanu, G. Desjardins, J. Turian, D. Warde-Farley, and Y. Bengio.
Theano: A CPU and GPU math compiler in Python. In Proc. 9th Python in Science
Conf, pages 1–7, 2010.). Now, keep in mind that this Theano benchmark is from
2010, and Theano has improved significantly over the years and so did the
capabilities of modern graphics cards.

> I have learned that many of the Greeks believe Pythagoras said all things
are generated from number. The very assertion poses a difficulty: How can
things which do not exist even be conceived to generate? - Theano of Croton
(Philosopher, 6th-century BC)

PS: If you don’t like NumPy’s `dot` method, stay tuned for the upcoming
[Python 3.5](https://docs.python.org/3.6/whatsnew/3.5.html) – we will get an
infix [operator](http://legacy.python.org/dev/peps/pep-0465/) for matrix
multiplication, yay!

Matrix-matrix multiplication “by hand” (I mean without the help of NumPy and
BLAS or LAPACK looks tedious and pretty inefficient).

```python

    [[1, 2],     [[5, 6],     [[1 * 5 + 2 * 7, 1 * 6 + 2 * 8],
    [3, 4]]  x   [7, 8]]  =   [3 * 5 + 4 * 7, 3 * 6 + 4 * 8]]
    
```

Who wants to implement this expression using nested for-loops if we have
linear algebra and libraries that are optimized to take care of it!?

```python

    >>> X = numpy.array()
    >>> W = numpy.array()
    >>> X.dot(W)
    [[19, 22],
    [43, 50]]
    
```

Now, if this `dot` product does not appeal to you, this is how it will look
like in Python 3.5:

```python

    >>> X @ W
    [[19, 22],
    [43, 50]]
    
```

To be honest, I have to admit that I am not necessarily a big fan of the “@”
symbol as matrix operator. However, I really thought long and hard about this
and couldn’t find any better “unused” symbol for this purpose. If you have a
better idea, please let me know, I am really curious! ⇧

## Julia真棒……理论上！

我认为Julia是一个伟大的语言，并且我会将其推荐给那些开始编程和机器学习的人。虽然，我不确定是否真的应该这样做。为什么呢？要把自己交给这个编程语言，是有点悲伤矛盾的。使用Julia，我们无法肯定在接下来的几年内，它是否会变得够“流行”。等等，“流行性”跟一门编程语言有多棒多有用有啥关系？让我告诉你。窘境是，最有用的语言不一定是设计良好的，但一定是流行的。为什么？

  1. 已经有大量的（大多数是免费的）库以供你使用，这样一来，你可以对你的时间尽其所用，而无需重新发明轮子。
  2. 在线查找帮助、教程和样例容易得多。
  3. 更频繁的语言改善、更新和补丁，会让它“甚至更好”。
  4. 对协作更友好，并且在团队中工作更简单。
  5. 更多的人会从你的代码中受益 (例如，如果你决定将其分享到GitHub上)。

就个人而言，我爱Julia本身。它完美的匹配了我的个人兴趣。虽然，我使用Python；主要是因为已经有了那么多超级棒的东西在那里了，这使得它格外得心应手。Python社区一切安好，而我相信在（至少）下一个5到10年内，它还会存在并蓬勃发展。但是对于Julia，我并没那么肯定。我喜欢设计，我觉得这很棒。尽管如此，如果它并不流行，那么我无法分辨它是“面向未来的”。如果在几年内发展停止了呢？我会对那些在这点上将“死”的东西进行投资。然而，如果每个人都这样想，那么新语言就永远没戏了。

## R实在没啥错

Well, I guess it’s no big secret that I was an R person once. I even wrote a
book about it (okay, it was actually about [Heat maps in
R](http://www.amazon.com/Instant-Heat-Maps-R-How-/dp/1782165649/ref=sr_1_1?ie=
UTF8&qid=1372160113&sr=8-1&keywords=instant+heat+maps+in+r+how-to) to be
precise. Note that this was years ago, before `ggplot2` was a thing. There’s
no real compelling reason to check it out – I mean the book. However, if you
can’t resist, here’s is the free, 5-minute-read [short
version](http://sebastianraschka.com/Articles/heatmaps_in_r.html)). I agree,
this is a bit of a digression. So, back to the discussion: What’s wrong with
R? I think there is nothing wrong with it at all. I mean, it’s pretty powerful
and a capable and “popular” language for “data science” after all! Not too
long ago even Microsoft became really, really interested: [Microsoft acquires
Revolution Analytics, a commercial provider of services for the open source R
programming language for statistical computing and predictive
analytics](http://www.cio.com/article/2906456/data-analytics/microsoft-closes-
acquisition-of-r-software-and-services-provider.html).

So, how can I summarize my feelings about R? I am not exactly sure where this
quote is comes from – I picked it up from someone somewhere some time ago –
but it is great for explaining the difference between R and Python: “R is a
programming language developed by statisticians for statisticians; Python was
developed by a computer scientist, and it can be used by programmers to apply
statistical techniques.” Part of the message is that both R and Python are
similarly capable for “data science” tasks, however, the Python syntax simply
feels more natural to me – it’s a personal taste.

I just wanted to bring up Theano and computing on GPUs as a big plus for
Python, but I saw that R is also pretty capable: [Parallel Programming with
GPUs and R](http://blog.revolutionanalytics.com/2015/01/parallel-programming-
with-gpus-and-r.html). I know what you want to ask next: “Okay, what about
turning my model into a nice and _shiny_ web application? I bet this is
something that you can’t do in R!” Sorry, but you lose this bet; have a look
at [Shiny by RStudio A web application framework for
R](http://www.rstudio.com/shiny/). You see what I am getting at? There is no
winner here. There will probably never be,

To take one of my favorite Python quotes out of its original context: “We are
all adults here” – let’s not waste our time with language wars. Choose the
tool that “clicks” for you. When it comes to perspectives on the job market:
There is no right or wrong here either. I don’t think a company that wants to
hire you as a “data scientist” really bothers about your favorite toolbox –
programming languages are just “tools” after all. The most important skill is
to think like a “data scientist,” to ask the right questions, to be able to
solve problems. The hard part is the math and machine learning theory, a new
programming language can easily be learned. Just think about, you learned how
to swing a hammer to drive the nail in, how hard can it possibly be to pick up
a hammer from a different manufacturer? But if you are still interested, look
at the Tiobe Index for example, _one_ measure of popularity of programming
languages:

![](http://sebastianraschka.com/images/blog/2015/why-python/tiobe.png)

(来源: <http://www.tiobe.com/index.php/content/paperinfo/tpci/index.html>)

However, if we look at the [The 2015 Top Ten Programming
Languages](http://spectrum.ieee.org/computing/software/the-2015-top-ten-
programming-languages) by Spectrum IEEE, the R language is climbing fast (left
column: 2015, right column: 2014).

![](http://sebastianraschka.com/images/blog/2015/why-python/spectrum.jpg)

(来源: [http://spectrum.ieee.org/computing/software/the-2015-top-ten-programming-languages](http://spectrum.ieee.org/computing/software/the–2015-top-ten-programming-languages))

I think you get the idea. Python and R, there’s really no big difference
anymore. Moreover, you shouldn’t worry about job opportunities when you are
choosing one language over the other. ⇧

## Perl发生了什么？

Perl是在我早起职业生涯中选取的第一门语言(当然，除了高中时使用的Basic, Pascal, 和Delphi)。在我还是德国的一个大学生的时候，我上了一门Perl编程课。那时，我真心喜欢它，但是，嘿，在这点上，我确实没有什么好对其进行比较的。就个人而言，我只知道少数几个积极使用Perl为每天写脚本。虽然，我认为在生物信息学领域这仍然相当普遍!? 不管怎么说，让我们保持这部分简短，让它安息：“[“Perl死了。Perl万岁。”](http://archive.oreilly.com/pub/post/perl_is_dead_long_live_perl.html)

## 其他观点

还有许多其他语言可以用于机器学习，例如，[Ruby](https://www.ruby-lang.org) ([Thoughtful Machine Learning: A Test-Driven Approach](http://www.amazon.com/Thoughtful-Machine-Learning-Test-Driven-Approach/dp/1449374069/ref=sr_1_1?ie=UTF8&qid=1440407371&sr=8-1&keywords=machine+learning+ruby)), [Java](https://www.java.com/en/) ([Java-ML](http://java-ml.sourceforge.net)), [Scala](http://www.scala-lang.org)([Breeze](https://github.com/scalanlp/breeze)), [Lua](http://www.lua.org)([Torch](http://torch.ch)), 等等。然而，除了我多年前参与的一个Java类，或者[PySpark](https://spark.apache.org/docs/0.9.0/python-programming-guide.html), 这一用于Spark的Python API，它是用Scala写的，我真的没有使用那些语言的丰富经验，因此不知道该说些什么好。

## Python是一个正在死掉的语言吗？

这是一个合法的问题，最近它在Quora出现了，如果你想听听关于这个的一些其他不错的观点，那么看看这个[问题支线](http://www.quora.com/Is-it-true-that-Python-is-a-dying-language)。不过，如果你想听听我的观点，我会说，不，它不是。为什么？好吧，Python是一门“相当”古老的语言 —— 它的第一个版本是在90年代初的某个时候 (我们可以从1991年开始算起)，它像每一个编程语言一样，不得不做出某些选择和妥协。每一个编程语言都有它自己的怪癖，而更现代的语言趋向于从过去的错误中学习，这是件好事 (顺便说一下，R是在Python之后不久发布的：1995)。 Python远不“完美”，而像其他每个语言一样，它有自己的缺点。作为一个核心Python用户，我必须提一下，GIL (Global Interpreter Lock，全局解释锁)是让我最苦恼的东西 —— 但是请注意，有一个多进程和多线程模块，因此它实际上并非是一种限制，而是某些上下文中的小小“不便”。

对于一个编程语言“多棒”并无可以量化的度量，它真正取决于你在找寻什么。你想要问的问题是：“我想要实现什么，哪一个是实现它的最好的工具” —— “如果你只有一把锤子，那么一切开始看起来都像一个钉子。”再次说到锤子和钉子，Python是非常灵活的，我每天大部分的研究都是通过Python，使用强大的scikit-learn机器学习库 —— 用于数据改写的pandas，用户可视化的matplotlib/seaborn，以及用以跟踪所有这些东西的IPython notebooks —— 来完成的。

## 总结

好啦，这是对于一个看似很简单的问题的一个相当长的答案。相信我，我可以花几小时或几天继续写下去。但为嘛要把事情搞复杂呢？让我们总结一下：

![](http://sebastianraschka.com/images/blog/2015/why-python/python.png)

(来源: <https://xkcd.com/353/>)

## 反馈和观点

我想要跟你分享关于这篇文章的很多很好的意见。记住，“一句忠告”有所偏颇是自然的；你或许发现了额，我的偏见是非常喜欢Python —— 抱歉啦，但这就是我！我相信，听取其他人的想法也是非常有用的！特别是当你是“数据科学”、机器学习和编程领域的新手的时候。话虽如此，请继续，看看下面的那些带干货的评论！

### Python

  * [hackernews](https://news.ycombinator.com/item?id=10113413)上的rm999:

> I switched from mostly using R to Python about a year ago for gluing
together my data pipeline (from data source all the way to production models
and frontends/visualizations). It hasn’t really impacted what I’m capable of
doing or my productivity, except the standard extra googling that comes in the
first couple years I use any language. The main reason I went for Python is
purely practical: it’s a language people outside my team will respect and deal
with. It makes it easier for me to collaborate in many different ways: share
tools with other teams, transfer ownership of my code, get help when I need
it, etc. Data science at some companies has the reputation of “hack something
together and throw it over the wall for someone else to deal with”. In my
experience R only furthers this reputation. Which is too bad, it’s really
great at what it does.

  * [hackernews](https://news.ycombinator.com/item?id=10113413)上的DrNuke:

> I love the hacking approach in the post: a tool is only a tool to do
something valuable and not the goal itself. The Python ecosystem is the right
tool at the right time, nowadays, because of the data science explosion and
the need to interact very quickly with non-specialists.

  * [hackernews](https://news.ycombinator.com/item?id=10113413)上的zzleeper:

> Quite interesting post. I feel that a lot of the numerical Pythonistas are
in the same spot: They tolerate most languages, but find R’s syntax a bit
unnatural, Matlab lacking when trying to go beyond pure matrix stuff, and are
waiting to see if Julia picks up (which it seems to be from what I can tell)

  * [reddit](https://www.reddit.com/r/MachineLearning/comments/3ibx9j/python_machine_learning_and_language_wars_a/)上的JanneJM:

> The key is enough good-quality libraries. Many people I know - myself
included - aren’t really interested in Python as such. We’re using Numpy,
Scipy, Matplotlib, Pandas and so on and so on. Python just comes along for the
ride. Had these libraries appeared for Ruby or Perl or Lua, then that’s what
we would be using today.

### Perl

  * [hackernews](https://news.ycombinator.com/item?id=10113413)上的leni536：

> “I think it Perl is still quite common in the bioinformatics field though!?”
That’s true - many day-to-day tasks in bioinformatics are more or less plain-
text parsing [1], and Perl excels in parsing text and quickly using regular
expressions. “My” generation of bioinformaticians doing data cleanup and
analysis (20–30) uses Python, sometimes because plotting is nicer, the
language is easier to get into, it’s more commonly taught in universities, or
other reasons - people older than that normally use P

### R

  * [hackernews](https://news.ycombinator.com/item?id=10113413)上的geomark：

> I just completed the Coursera data science track which took me from a
complete R newbie to being at least somewhat proficient. Having previously
used Python for a quite a bit of web programming, I disliked R at first except
for its power in statistical programming. But I’ve since discovered a number
of great R packages that make it a pleasure to use for things I would normally
turn to Python for. Like I recently discovered the rvest package for
webscraping. Data visualizations with R seem vastly superior, unless I am
missing something with Python (highly likely). And putting up a slick
statistics app is easy with shiny or RStudio Presenter. But R can’t really
scale to a large production app, isn’t that right? So I feel I need to keep
working with both Python and R. Added: That’s a nice list Lofkin. Thanks.
Also, in the article he says that Python syntax feels more natural, which I
also felt. But then I started to use things like the magrittr and dplyr
packages in R which gives you nice things like pipes and that feeling starts
to ebb.

  * [hackernews](https://news.ycombinator.com/item?id=10113413)上的Adam_O

> From the perspective of a student, most of the good online analytics/data
analysis/stats courses use R, so it is hard to get away from it while learning
the material. Once you get the base concepts down, switching to python
shouldn’t be hard. I think most people still prefer ggplot2 for visualization
though. Whenever I use R I feel like a statistician, I can feel that ‘cold
rigor’ emanating from the language. But in the end I think it is advantageous
to wield both languages.

### MATLAB/Octave

  * [hackernews](https://news.ycombinator.com/item?id=10113413)上的sampo

> Andrew Ng said in the Coursera Machine learning class that according to his
experience, students implement the course homework faster in Octave/Matlab
than in Python. But yes, the point of that course is to implement and play
around with small numerical algorithms, whereas the linked blog is about
someone who mainly calls existing machine learning libraries from Python.

  * [hackernews](https://news.ycombinator.com/item?id=10113413)上的misiti3780

> Octave/Matlab are “great” but good luck trying to integrate them into a
production web application. Since you cant really do that - avoid using them
unless you are fine with implementing the same algorithm twice. Matlab
licenses cost money also, and the toolboxes cost additional money. R is useful
because there are a lot of resources as it has been along for so long and is
used by a large portion of the stats community. It also has a lot of useful
libraries that have not been ported over to other languages yet (ggmap!!!).
But you still still run into the same problem that you cannot integrate R into
a production web application. I am pretty sure Hadoop streaming does not
support R,Octave, or Matlab either

  * [hackernews](https://news.ycombinator.com/item?id=10113413)上的thanatropism

> One thing missing here: Matlab syntax is actually very close to modern
Fortran. At least twice I’ve written Fortran code (for Monte Carlo
simulations; different contexts) by overwriting Matlab code adding types /
general verbosity / fixing the syntax of do-loops / etc.

### Julia

  * [hackernews](https://news.ycombinator.com/item?id=10113413)上的Lofkin:

> Personally I’m tempted to make the switch to Julia, but slow higher order
functions, high churn in the core data infrastructure and no Pymc 3 are
keeping me on pydata for a bit longer. I have numba to hold me over.

  * [hackernews](https://news.ycombinator.com/item?id=10113413)上的Buttons 840:

> I’ve used Python professionally for 8 years, and it’s my favorite language.
I have used numpy and a scikit-learn a little bit. That said, I’ve really
enjoying learning Julia recently. It’s been easy to learn and it really does
perform well (read: it’s fast). In fact, I think learning Julia has been about
as much work as learning something like numba would be, and gives similar
(some say slightly better?) performance.

  * [hackernews](https://news.ycombinator.com/item?id=10113413)上的idunning:

> As someone who almost exclusively uses Julia for their day-to-day work (and
side projects), I think most of the author’s thoughts about Julia are correct.
I think the language is great, and using it makes my life better. There are
some packages that are actually better than any of their equivalents in other
languages, in my opinion. On the other hand, I’ve also got a higher tolerance
for things not being perfect, I can figure things out for myself (and luckily
have the time do so), and I’m willing to code it up if it doesn’t already
exist (to a point). Naturally, that is not true for most people, and thats
fine. The author isn’t willing to take the risk that Julia won’t “survive”,
which is fair. Its definitely not complete yet, but its getting there. I am
confident that it will survive (and thrive) though, and continue growing the
not-insubstantial community. I have a feeling the author will find their way
to Julia-land eventually, in a couple of years or so.

  * [reddit](https://www.reddit.com/r/Python/comments/3i8crj/python_machine_learning_and_language_wars_a/)上的niksko:

> I agree on the subject of Julia. It has really great potential and it’s
basically tailor-made for these sorts of applications, but the community and
support just isn’t there yet. I spent a semester doing a small research
project in the area of computation evolutionary dynamics, and the most tedious
and difficult part was getting Julia to plot what I wanted to. Also it didn’t
have docstring support at the time :/. It’s fast, it’s fancy, but it’s not
mature enough.

  * [reddit](https://www.reddit.com/r/MachineLearning/comments/3ibx9j/python_machine_learning_and_language_wars_a/)上的niksko:

> After I took the Ng ML course on Coursera I looked around and it seemed like
the thing to do was to use Python… but there were several large libraries
including the ones you mention that had to be learned. Then I looked at Julia
and figured that I might as well learn it as it already has all of the linear
algebra &amp; SIMD stuff built in and is more performant. It really does seem
like the “sweet-spot” language for ML.

### 其他语言 (我忘记提的那些)

  * [hackernews](https://news.ycombinator.com/item?id=10113413)上的leni536:

> And there is nothing wrong with C++. For linear algebra I use the armadillo
library and it’s really a nice wrapper around LAPACK and BLAS (and fast!). For
some reason scientists are somewhat afraid of C++. For some reason you “have
to” prototype in an “easier” language. Sure, you can’t use C++ as a calculator
as opposed to interpreted languages, but I see people being stuck with their
computations at the prototyping language and eventually not bringing it to a
faster platform. Point being: C++ is not hard for scientific calculations.

  * [reddit](https://www.reddit.com/r/Python/comments/3i8crj/python_machine_learning_and_language_wars_a/)上的leni536:

> If anything is going to replace Python for me, it looks like Scala is the
likeliest candidate. I think functional languages are a nice fit for mathy
work, and it’s on the JVM so prototypes can become production code without
much overhead that can run ‘anywhere.’ Spark is a killer app for Scala. Now I
can go from prototype to running on an arbitrarily large dataset without too
much barrier in between.

  * [reddit](https://www.reddit.com/r/Python/comments/3i8crj/python_machine_learning_and_language_wars_a/)上的rpcope1:

> [Scala] may be slow on the compilation, but it’s both safer and far faster
than CPython (barring using code that’s not bytecode and calls to C/Fortran
libraries), and has a number of concepts that I am really missing in Python
now, like Option[T], the implicit modifier, non-crap map/reduce/filter, non-
crap lambdas, etc.

