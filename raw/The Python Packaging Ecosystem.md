原文：[The Python Packaging Ecosystem](http://www.curiousefficiency.org/posts/2016/09/python-packaging-ecosystem.html)

---

从开发到部署

[TOC]

最近已经有一些文章从最终用户的角度反映Python包生态圈的现状，因此，作为该生态圈的主架构师之一，对我来说，值得从我的角度写写我如何描述软件出版发行的整体问题空间，此刻我认为我们所处的境地，以及我所希望看到的未来的发展。

作为背景，我回复的具体文章是：

  * [Python Packaging is Good Now](https://glyph.twistedmatrix.com/2016/08/python-packaging.html) (Glyph Lefkowitz)
  * [Conda：神话和误解](https://jakevdp.github.io/blog/2016/08/25/conda-myths-and-misconceptions/) (Jake VanderPlas)
  * [PayPal的Python Packaging](https://www.paypal-engineering.com/2016/09/07/python-packaging-at-paypal/) (Mahmoud Hashemi)

These are all excellent pieces considering the problem space from different
perspectives, so if you'd like to learn more about the topics I cover here, I
highly recommend reading them.

## [我的核心软件生态设计理念](http://www.curiousefficiency.org/posts/2016/09/python-packaging-ecosystem.html#id1)

Since it heavily influences the way I think about packaging system design in
general, it's worth stating my core design philosophy explicitly:

  * As a software consumer, I should be able to consume libraries, frameworks, and applications in the binary format of my choice, regardless of whether or not the relevant software publishers directly publish in that format
  * As a software publisher working in the Python ecosystem, I should be able to publish my software once, in a single source-based format, and have it be automatically consumable in any binary format my users care to use

This is emphatically _not_ the way many software packaging systems work - for
a great many systems, the publication format and the consumption format are
tightly coupled, and the folks managing the publication format or the
consumption format actively seek to use it as a lever of control over a
commercial market (think operating system vendor controlled application
stores, especially for mobile devices).

While we're unlikely to ever pursue the specific design documented in the rest
of the PEP (hence the "Deferred" status), the "[Development, Distribution, and Deployment of Python Software](https://www.python.org/dev/peps/pep-0426/#development-distribution-and-deployment-of-python-software)" section of PEP
426 provides additional details on how this philosophy applies in practice.

I'll also note that while I now work on software supply chain management
tooling at Red Hat, that _wasn't_ the case when I first started actively
participating in the upstream Python packaging ecosystem [design process](https://lwn.net/Articles/580399/). Back then I was working on Red
Hat's main [hardware integration testing system](https://beaker-project.org/),
and growing increasingly frustrated with the level of effort involved in
integrating new Python level dependencies into Beaker's RPM based development
and deployment model. Getting actively involved in tackling these problems on
the Python upstream side of things then led to also getting more actively
involved in addressing them on the [Red Hat downstream side](http://www.slideshare.net/ncoghlan_dev/developing-in-python-on-red-hat-platforms-devnation-2016).

## [关键难题](http://www.curiousefficiency.org/posts/2016/09/python-packaging-ecosystem.html#id2)

When talking about the design of software packaging ecosystems, it's very easy
to fall into the trap of only considering the "direct to peer developers" use
case, where the software consumer we're attempting to reach is another
developer working in the same problem domain that we are, using a similar set
of development tools. Common examples of this include:

  * Linux distro developers publishing software for use by other contributors to the same Linux distro ecosystem
  * Web service developers publishing software for use by other web service developers
  * Data scientists publishing software for use by other data scientists

In these more constrained contexts, you can frequently get away with using a
single toolchain for both publication and consumption:

  * Linux: just use the system package manager for the relevant distro
  * Web services: just use the Python Packaging Authority's twine for publication and pip for consumption
  * Data science: just use conda for everything

For newer languages that start in one particular domain with a preferred
package manager and expand outwards from there, the apparent simplicity
arising from this homogeneity of use cases may frequently be attributed as an
essential property of the design of the package manager, but that perception
of inherent simplicity will typically fade if the language is able to
successfully expand beyond the original niche its default package manager was
designed to handle.

In the case of Python, for example, distutils was designed as a consistent
build interface for Linux distro package management, setuptools for plugin
management in the Open Source Application Foundation's Chandler project, pip
for dependency management in web service development, and conda for local
language-independent environment management in data science. distutils and
setuptools haven't fared especially well from a usability perspective when
pushed beyond their original design parameters (hence the current efforts to
make it easier to use full-fledged build systems like Scons and Meson as an
alternative when publishing Python packages), while pip and conda both seem to
be doing a better job of accommodating increases in their scope of
application.

This history helps illustrate that where things really have the potential to
get complicated (even beyond the inherent challenges of domain-specific
software distribution) is when you start needing to _cross domain boundaries_.
For example, as the lead maintainer of `contextlib` in the Python standard
library, I'm also the maintainer of the `contextlib2` backport project on
PyPI. That's not a domain specific utility - folks may need it regardless of
whether they're using a self-built Python runtime, a pre-built Windows or Mac
OS X binary they downloaded from python.org, a pre-built binary from a Linux
distribution, a CPython runtime from some other redistributor (homebrew,
pyenv, Enthought Canopy, ActiveState, Continuum Analytics, AWS Lambda, Azure
Machine Learning, etc), or perhaps even a different Python runtime entirely
(PyPy, PyPy.js, Jython, IronPython, MicroPython, VOC, Batavia, etc).

Fortunately for me, I _don't_ need to worry about all that complexity in the
wider ecosystem when I'm specifically wearing my `contextlib2` maintainer hat
- I just publish an sdist and a universal wheel file to PyPI, and the rest of
the ecosystem has everything it needs to take care of redistribution and end
user consumption without any further input from me.

However, `contextlib2` is a pure Python project that only depends on the
standard library, so it's pretty much the simplest possible case from a
tooling perspective (the only reason I needed to upgrade from distutils to
setuptools was so I could publish my own wheel files, and the only reason I
haven't switched to using the _much_ simpler pure-Python-only flit instead of
either of them is that that doesn't yet easily support publishing backwards
compatible setup.py based sdists).

This means that things get significantly more complex once we start wanting to
use and depend on components written in languages other than Python, so that's
the broader context I'll consider next.

## [平台管理或者插件管理？](http://www.curiousefficiency.org/posts/2016/09/python-packaging-ecosystem.html#id3)

When it comes to handling the software distribution problem in general, there
are two main ways of approaching it:

  * design a plugin management system that doesn't concern itself with the management of the application framework that _runs_ the plugins
  * design a platform component manager that not only manages the plugins themselves, but _also_ the application frameworks that run them

This "plugin manager or platform component manager?" question shows up over
and over again in software distribution architecture designs, but the case of
most relevance to Python developers is in the contrasting approaches that pip
and conda have adopted to handling the problem of external dependencies for
Python projects:

  * pip is a _plugin manager_ for Python runtimes. Once you have a Python runtime (any Python runtime), pip can help you add pieces to it. However, by design, it won't help you manage the underlying Python runtime (just as it wouldn't make any sense to try to install Mozilla Firefox as a Firefox Add-On, or Google Chrome as a Chrome Extension)
  * conda, by contrast, is a _component manager_ for a cross-platform platform that provides its own Python runtimes (as well as runtimes for other languages). This means that you can get _pre-integrated_ components, rather than having to do your own integration between plugins obtained via pip and language runtimes obtained via other means

What this means is that pip, _on its own_, is not in any way a direct
alternative to conda. To get comparable capabilities to those offered by
conda, you have to add in a mechanism for obtaining the underlying language
runtimes, which means the alternatives are combinations like:

  * apt-get + pip
  * dnf + pip
  * yum + pip
  * pyenv + pip
  * homebrew (Mac OS X) + pip
  * python.org Windows installer + pip
  * Enthought Canopy
  * ActiveState's Python runtime + PyPM

This is the main reason why "just use conda" is excellent advice to any
prospective Pythonista that isn't already using one of the platform component
managers mentioned above: giving that answer replaces an otherwise operating
system dependent or Python specific answer to the runtime management problem
with a cross-platform and (at least somewhat) language neutral one.

It's an especially good answer for Windows users, as chocalatey/OneGet/Windows
Package Management isn't remotely comparable to pyenv or homebrew at this
point in time, other runtime managers don't work on Windows, and getting folks
bootstrapped with MinGW, Cygwin or the new (still experimental) Windows
Subsystem for Linux is just another hurdle to place between them and whatever
goal they're learning Python for in the first place.

However, conda's pre-integration based approach to tackling the external
dependency problem is also why "just use conda for everything" isn't a
sufficient answer for the Python software ecosystem as a whole.

If you're working on an operating system component for Fedora, Debian, or any
other distro, you actually _want_ to be using the system provided Python
runtime, and hence need to be able to readily convert your upstream Python
dependencies into policy compliant system dependencies.

Similarly, if you're wanting to support folks that deploy to a preconfigured
Python environment in services like AWS Lambda, Azure Cloud Functions, Heroku,
OpenShift or Cloud Foundry, or that use alternative Python runtimes like PyPy
or MicroPython, then you need a publication technology that doesn't tightly
couple your releases to a specific version of the underlying language runtime.

As a result, pip and conda end up existing at slightly different points in the
system integration pipeline:

  * Publishing and consuming Python software with pip is a matter of "bring your own Python runtime". This has the benefit that you _can_ readily bring your own runtime (and manage it using whichever tools make sense for your use case), but also has the downside that you _must_ supply your own runtime (which can sometimes prove to be a significant barrier to entry for new Python users, as well as being a pain for cross-platform environment management).
  * Like Linux system package managers before it, conda takes away the requirement to supply your own Python runtime by providing one for you. This is great if you don't have any particular preference as to which runtime you want to use, but if you _do_ need to use a different runtime for some reason, you're likely to end up fighting against the tooling, rather than having it help you. (If you're tempted to answer "Just add another interpreter to the pre-integrated set!" here, keep in mind that doing so without the aid of a runtime independent plugin manager like pip acts as a _multiplier_ on the platform level integration testing needed, which can be a significant cost even when it's automated)

## [接下来，要做什么？](http://www.curiousefficiency.org/posts/2016/09/python-packaging-ecosystem.html#id4)

In case it isn't already clear from the above, I'm largely happy with the
respective niches that pip and conda are carving out for themselves as a
plugin manager for Python runtimes and as a cross-platform platform focused on
(but not limited to) data analysis use cases.

However, there's still plenty of scope to improve the effectiveness of the
collaboration between the upstream Python Packaging Authority and downstream
Python redistributors, as well as to reduce barriers to entry for
participation in the ecosystem in general, so I'll go over some of the key
areas I see for potential improvement.

### [可持续发展与旁观者效应](http://www.curiousefficiency.org/posts/2016/09/python-packaging-ecosystem.html#id5)

It's not a secret that the core PyPA infrastructure (PyPI, pip, twine,
setuptools) is [nowhere near as well-funded](https://caremad.io/posts/2016/05
/powering-pypi/) as you might expect given its criticality to the operations
of some truly enormous organisations.

The biggest impact of this is that even when volunteers show up ready and
willing to work, there may not be anybody in a position to effectively
_wrangle_ those volunteers, and help keep them collaborating effectively and
moving in a productive direction.

To secure long term sustainability for the core Python packaging
infrastructure, we're only talking amounts on the order of a few hundred
thousand dollars a year - enough to cover some dedicated operations and
publisher support staff for PyPI (freeing up the volunteers currently handling
those tasks to help work on ecosystem improvements), as well as to fund
targeted development directed at some of the other problems described below.

However, rather than being a true "[tragedy of the
commons](https://en.wikipedia.org/wiki/Tragedy_of_the_commons)", I personally
chalk this situation up to a different human cognitive bias: the [bystander
effect](https://en.wikipedia.org/wiki/Bystander_effect).

The reason I think that is that we have _so many_ potential sources of the
necessary funding that even folks that agree there's a problem that needs to
be solved are assuming that someone else will take care of it, without
actually checking whether or not that assumption is entirely valid.

The primary responsibility for correcting that oversight falls squarely on the
Python Software Foundation, which is why the Packaging Working Group was
formed in order to investigate possible sources of additional funding, as well
as to determine how any such funding can be spent most effectively.

However, a secondary responsibility also falls on customers and staff of
commercial Python redistributors, as this is _exactly_ the kind of ecosystem
level risk that commercial redistributors are being paid to manage on behalf
of their customers, and they're currently not handling this particular
situation very well. Accordingly, anyone that's actually _paying_ for CPython,
pip, and related tools (either directly or as a component of a larger
offering), and expecting them to be supported properly as a result, really
needs to be asking some very pointed question of their suppliers right about
now. (Here's a sample question: "We pay you X dollars a year, and the upstream
Python ecosystem is one of the things we expect you to support with that
revenue. How much of what we pay you goes towards maintenance of the upstream
Python packaging infrastructure that we rely on every day?").

One key point to note about the current situation is that as a 501(c)(3)
public interest charity, any work the PSF funds will be directed towards
better fulfilling that public interest mission, and that means focusing
primarily on the needs of educators and non-profit organisations, rather than
those of private for-profit entities.

Commercial redistributors are thus _far_ better positioned to properly
represent their customers interests in areas where their priorities may
diverge from those of the wider community (closing the "insider threat"
loophole in PyPI's current security model is a particular case that comes to
mind - see [Making PyPI security independent of
SSL/TLS](http://www.curiousefficiency.org/posts/2016/09/python-packaging-
ecosystem.html#making-pypi-security-independent-of-ssl-tls)).

### [将PyPI迁移到pypi.org](http://www.curiousefficiency.org/posts/2016/09/python-packaging-ecosystem.html#id6)

An instance of the new PyPI implementation (Warehouse) is up and running at
<https://pypi.org/> and connected directly to the production PyPI database, so
folks can already explicitly opt-in to using it over the legacy implementation
if they prefer to do so.

However, there's still a non-trivial amount of design, development and QA work
needed on the new version before all existing traffic can be transparently
switched over to using it.

Getting at least this step appropriately funded and a clear project management
plan in place is the main current focus of the PSF's Packaging Working Group.

### [使得编译器的存在在终端用户系统上可选](http://www.curiousefficiency.org/posts/2016/09/python-packaging-ecosystem.html#id7)

Between the `wheel` format and the `manylinux1` usefully-distro-independent
ABI definition, this is largely handled now, with `conda` available as an
option to handle the relatively small number of cases that are still a problem
for `pip`.

The main unsolved problem is to allow projects to properly express the
constraints they place on target environments so that issues can be detected
at install time or repackaging time, rather than only being detected as
runtime failures. Such a feature will also greatly expand the ability to
correctly generate platform level dependencies when converting Python projects
to downstream package formats like those used by conda and Linux system
package managers.

### [在终端用户的系统上引导依赖管理工具](http://www.curiousefficiency.org/posts/2016/09/python-packaging-ecosystem.html#id8)

With pip being bundled with recent versions of CPython (including CPython 2.7
maintenance releases), and pip (or a variant like upip) also being bundled
with most other Python runtimes, the ecosystem bootstrapping problem has
largely been addressed for new Python users.

There are still a few usability challenges to be addressed (like defaulting to
per-user installations when outside a virtual environment, interoperating more
effectively with platform component managers like conda, and providing an
officially supported installation interface that works at the Python prompt
rather than via the operating system command line), but those don't require
the same level of political coordination across multiple groups that was
needed to establish pip as the lowest common denominator approach to
dependency management for Python applications.

### [让distutils和setuptools的使用可选optional](http://www.curiousefficiency.org/posts/2016/09/python-packaging-ecosystem.html#id9)

As mentioned above, distutils was designed ~18 years ago as a common interface
for Linux distributions to build Python projects, while setuptools was
designed ~12 years ago as a plugin management system for an open source
Microsoft Exchange replacement. While both projects have given admirable
service in their original target niches, and quite a few more besides, their
age and original purpose means they're significantly more complex than what a
user needs if all they want to do is to publish their pure Python library or
framework to the Python Package index.

Their underlying complexity also makes it incredibly difficult to improve the
problematic state of their documentation, which is split between the legacy
distutils documentation in the CPython standard library and the additional
setuptools specific documentation in the setuptools project.

Accordingly, what we want to do is to change the way build toolchains for
Python projects are organised to have 3 clearly distinct tiers:

  * toolchains for pure Python projects
  * toolchains for Python projects with simple C extensions
  * toolchains for C/C++/other projects with Python bindings

This allows folks to be introduced to simpler tools like flit first, better
enables the development of potential alternatives to setuptools at the second
tier, and supports the use of full-fledged pip-installable build systems like
Scons and Meson at the third tier.

The first step in this project, defining the `pyproject.toml` format to allow
declarative specification of the dependencies needed to launch `setup.py`, has
been implemented, and Daniel Holth's `enscons` project demonstrates that that
is already sufficient to bootstrap an external build system even without the
later stages of the project.

Future steps include providing native support for `pyproject.toml` in `pip`
and `easy_install`, as well as defining a declarative approach to invoking the
build system rather than having to run `setup.py` with the relevant distutils
&amp; setuptools flags.

### [使得PyPI的安全独立于SSL/TLS](http://www.curiousefficiency.org/posts/2016/09/python-packaging-ecosystem.html#id10)

PyPI currently relies entirely on SSL/TLS to protect the integrity of the link
between software publishers and PyPI, and between PyPI and software consumers.
The only protections against insider threats from within the PyPI
administration team are ad hoc usage of GPG artifact signing by some projects,
personal vetting of new team members by existing team members and 3rd party
checks against previously published artifact hashes unexpectedly changing.

A credible design for end-to-end package signing that adequately accounts for
the significant usability issues that can arise around publisher and consumer
key management has been available for almost 3 years at this point (see
[Surviving a Compromise of PyPI](https://www.python.org/dev/peps/pep-0458/)
and [Surviving a Compromise of PyPI: the Maximum Security
Edition](https://www.python.org/dev/peps/pep-0480/)).

However, implementing that solution has been gated not only on being able to
first retire the legacy infrastructure, but also the PyPI administators being
able to credibly commit to the key management obligations of operating the
signing system, as well as to ensuring that the system-as-implemented actually
provides the security guarantees of the system-as-designed.

Accordingly, this isn't a project that can realistically be pursued until the
underlying sustainability problems have been suitably addressed.

### [自动化wheel创建](http://www.curiousefficiency.org/posts/2016/09/python-packaging-ecosystem.html#id11)

While redistributors will generally take care of converting upstream Python
packages into their own preferred formats, the Python-specific wheel format is
currently a case where it is left up to publishers to decide whether or not to
create them, and if they do decide to create them, how to automate that
process.

Having PyPI take care of this process automatically is an obviously desirable
feature, but it's also an incredibly expensive one to build and operate.

Thus, it currently makes sense to defer this cost to individual projects, as
there are quite a few commercial continuous integration and continuous
deployment service providers willing to offer free accounts to open source
projects, and these can also be used for the task of producing release
artifacts. Projects also remain free to only publish source artifacts, relying
on pip's implicit wheel creation and caching and the appropriate use of
private PyPI mirrors and caches to meet the needs of end users.

For downstream platform communities already offering shared build
infrastructure to their members (such as Linux distributions and conda-forge),
it may make sense to offer Python wheel generation as a supported output
option for cross-platform development use cases, in addition to the platform's
native binary packaging format.
