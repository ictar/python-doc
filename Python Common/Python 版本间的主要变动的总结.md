原文：[Python 版本间的主要变动的总结](https://www.nicholashairs.com/posts/major-changes-between-python-versions/)

---


# Python 版本间的主要变动的总结

_Feb 2, 2024_





![Summary of Major Changes Between Python Versions](https://images.unsplash.com/photo-1538439907460-1596cafd4eff?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3wxMTc3M3wwfDF8c2VhcmNofDR8fHB5dGhvbnxlbnwwfHx8fDE3MDY4NjA1MDl8MA&ixlib=rb-4.0.3&q=80&w=2000)
摄影：[David Clode](https://unsplash.com/@davidclode?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit)



This post is designed to be a quick reference for the major changes introduced with each new version of Python. This can help with taking advantages of using new features as you upgrade your code base, or ensuring that you have the correct guards for compatibility with older versions. 

There are two sections to this post: the first covers the actual changes, the second useful tools, links, and utilities that can aid with upgrading code bases.

# 版本

In this section I've documented the major changes to the Python syntax and standard library. Except for the `typing` module I've mostly excluded changes to modules. I have **not** included any changes to the C-API, byte-code, or other low level parts.

For each section the end-of-life date (EOL) refers to the date at which the Python Software Foundation will not longer provide security patches for a particular version.

## [Python 3.7 及更早版本](https://docs.python.org/3/whatsnew/index.html)

This section has been combined as all these versions are already EOL at the time of writing, but if you've been programming in Python for a while you may have forgotten about when these features were introduced.

* async and await (3.5+)
* matrix operator: `a @ b` (3.5+)
* type hints (3.5+)
* [Formatted String Literals](https://docs.python.org/3/tutorial/inputoutput.html#formatted-string-literals) (aka f-strings) `f"{something}"` (3.6+)
* underscore in numeric literals `1_000_000` (3.6+)
* dictionaries are guaranteed insertion ordered (3.7+)
* `contextvars` (3.7+)
* `dataclasses` (3.7+)
* `importlib.resources` (3.7+)

## [Python 3.8](https://docs.python.org/3/whatsnew/3.8.html) (EOL Oct 2024)

### 赋值表达式

也称为海象运算符（Walrus operator）

```py
if (thing := get_thing()) is not None:
  do_something(thing)
else:
  raise Exception(f"Something is wrong with {thing}")
```

### Positional only parameters


```py
def foo(a, b, /, c, d, *, e, f):
  # a, b: positional only
  # c, d: positional or keyword
  # e, f: keyword only
```

### Self documenting f-strings


```py
# Before
f"user={user}"

# Now
f"{user=}"
```
### Importlib Metadata


```py
import importlib.metadata
importlib.metadata.version("some-library")
# "2.3.4"
importlib.metadata.requires("some-library")
# ["thing==1.2.4", "other>=5"]
importlib.metadata.files("some-library")
# [...]
```
### Typing: `TypedDict`, `Literal`, `Final`, `Protocol`

* `TypedDict` - [PEP 589](https://peps.python.org/pep-0589/)
* `Literal` - [PEP 586](https://peps.python.org/pep-0586/)
* `Final` - [PEP 591](https://peps.python.org/pep-0591/)
* `Protocol` - [PEP 544](https://peps.python.org/pep-0544/)

## [Python 3.9](https://docs.python.org/3/whatsnew/3.9.html) (EOL Oct 2025)

### Typing: Builtin Generics

Can now use `dict[...]`, `list[...]`, `set[...]` etc instead of using `typing.Dict, List, Set`.

### Remove Prefix/Suffix

Strings and similar types can now use `removeprefix` and `removesuffix` to more safely remove things from the start or end. This is safer than string slicing methods which rely on correctly counting the length of the prefix (and remembering to change the slice if the prefix changes). 


```
if header.startswith("X-Forwarded-"):
  section = header.removeprefix("X-Forwarded-")
```
### Dict Union Operator ([PEP 584](https://peps.python.org/pep-0584/))


```
combined_dict = dict_one | dict_two
updated_dict |= dict_three
```
### Annotations ([PEP 593](https://peps.python.org/pep-0593/))


```
my_int: Annotated[int, SomeRange(0, 255)] = 0
```
### Zoneinfo ([PEP 615](https://peps.python.org/pep-0615/))

IANA Time Zone Database is now part of standard library


```
import zoneinfo
some_zone = zoneinfo.ZoneInfo("Europe/Berlin")
```
## [Python 3.10](https://docs.python.org/3/whatsnew/3.10.html) (EOL Oct 2026)

### Structural Pattern Matching ([PEP 634](https://peps.python.org/pep-0634/), [PEP 635](https://peps.python.org/pep-0635/), [PEP 636](https://peps.python.org/pep-0636/))

[See change log for more examples.](https://docs.python.org/3/whatsnew/3.10.html#pep-634-structural-pattern-matching)


```
match command.split():
  case ["quit"]:
    print("Goodbye!")
    quit_game()
  case ["look"]:
    current_room.describe()
  case ["get", obj]:
    character.get(obj, current_room)
  case ["go", direction]:
    current_room = current_room.neighbor(direction)
  case [action]:
    ... # interpret single-verb action
  case [action, obj]:
    ... # interpret action, obj
  case _:
    ... # anything that didn't match
```
### Typing: Union using pipe


```
# Before
from typing import Optional, Union
thing: Optional[Union[str, list[str]]] = None

# Now
thing: str | list[str] | None = None
```
### Typing: `ParamSpec` ([PEP 612](https://peps.python.org/pep-0612/))

Allows for much better passing of typing information when working with `Callable` and other similar types.


```py
from typing import Awaitable, Callable, ParamSpec, TypeVar

P = ParamSpec("P")
R = TypeVar("R")

def add_logging(f: Callable[P, R]) -> Callable[P, Awaitable[R]]:
  async def inner(*args: P.args, **kwargs: P.kwargs) -> R:
    await log_to_database()
    return f(*args, **kwargs)
  return inner

@add_logging
def takes_int_str(x: int, y: str) -> int:
  return x + 7

await takes_int_str(1, "A") # Accepted
await takes_int_str("B", 2) # Correctly rejected by the type checker
```
### Typing: `TypeAlias` ([PEP 613](https://peps.python.org/pep-0613/))


```
StrCache: TypeAlias = 'Cache[str]'  # a type alias
LOG_PREFIX = 'LOG[DEBUG]'  # a module constant
```
### Typing: `TypeGuard` ([PEP 647](https://peps.python.org/pep-0647/))


```py
_T = TypeVar("_T")

def is_two_element_tuple(val: Tuple[_T, ...]) -> TypeGuard[Tuple[_T, _T]]:
  return len(val) == 2

def func(names: Tuple[str, ...]):
  if is_two_element_tuple(names):
    reveal_type(names)  # Tuple[str, str]
  else:
    reveal_type(names)  # Tuple[str, ...]
```
### Parenthesized Context Managers ([PEP 617](https://peps.python.org/pep-0617/))


```py
with (CtxManager() as example):
  ...

with (
  CtxManager1(), CtxManager2()
):
  ...

with (CtxManager1() as example, CtxManager2()):
  ...

with (CtxManager1(), CtxManager2() as example):
  ...

with (
  CtxManager1() as example1,
  CtxManager2() as example2,
):
  ...
```

### Dataclasses: `slots`, `kw_only`

[Dataclass decorator](https://docs.python.org/3/library/dataclasses.html#dataclasses.dataclass) now supports following:

* `kw_only=True` all parameters in `__init__` will be marked keyword only.
* `slots=True` the generatred dataclass will use `__slots__` for storing data.

## [Python 3.11](https://docs.python.org/3/whatsnew/3.11.html) (EOL Oct 2027)

### Tomllib

`[tomllib](https://docs.python.org/3/library/tomllib.html#module-tomllib)` - Standard library TOML parser

### Exception Groups ([PEP 654](https://peps.python.org/pep-0654/))


> [**PEP 654**](https://peps.python.org/pep-0654/) introduces language features that enable a program to raise and handle multiple unrelated exceptions simultaneously. The builtin types [`ExceptionGroup`](https://docs.python.org/3/library/exceptions.html#ExceptionGroup) and [`BaseExceptionGroup`](https://docs.python.org/3/library/exceptions.html#BaseExceptionGroup) make it possible to group exceptions and raise them together, and the new [`except*`](https://docs.python.org/3/reference/compound_stmts.html#except-star) syntax generalizes [`except`](https://docs.python.org/3/reference/compound_stmts.html#except) to match subgroups of exception groups.

### Enriching Exceptions with notes ([PEP 678](https://peps.python.org/pep-0678/))


> The [`add_note()`](https://docs.python.org/3/library/exceptions.html#BaseException.add_note) method is added to [`BaseException`](https://docs.python.org/3/library/exceptions.html#BaseException). It can be used to enrich exceptions with context information that is not available at the time when the exception is raised. The added notes appear in the default traceback.


```
try:
  do_something()
except BaseException as e:
  e.add_note("this happened during do_something")
  raise
```
### Typing: `Self` ([PEP 673](https://peps.python.org/pep-0673/))


```
class MyClass:
  @classmethod
  def from_hex(cls, s: str) -> Self:  # Self means instance of cls
    return cls(int(s, 16))
        
  def frobble(self, x: int) -> Self: # Self means this instance
    self.y >> x
    return self
```
### Typing: LiteralString ([PEP 675](https://peps.python.org/pep-0675/))


> The new [`LiteralString`](https://docs.python.org/3/library/typing.html#typing.LiteralString) annotation may be used to indicate that a function parameter can be of any literal string type. This allows a function to accept arbitrary literal string types, as well as strings created from other literal strings. Type checkers can then enforce that sensitive functions, such as those that execute SQL statements or shell commands, are called only with static arguments, providing protection against injection attacks.

### Typing: Marking `TypedDict` entries as [not] required ([PEP 655](https://peps.python.org/pep-0655/))


```
# default is required
class Movie(TypedDict):
  title: str
  year: NotRequired[int]

# default is not-required
class Movie(TypedDict, total=False):
  title: Required[str]
  year: int
```
### Typing: Variadic Generics via `TypeVarTuple` ([PEP 646](https://peps.python.org/pep-0646/))


> [**PEP 484**](https://peps.python.org/pep-0484/) previously introduced [`TypeVar`](https://docs.python.org/3/library/typing.html#typing.TypeVar), enabling creation of generics parameterised with a single type. [**PEP 646**](https://peps.python.org/pep-0646/) adds [`TypeVarTuple`](https://docs.python.org/3/library/typing.html#typing.TypeVarTuple), enabling parameterisation with an *arbitrary* number of types. In other words, a [`TypeVarTuple`](https://docs.python.org/3/library/typing.html#typing.TypeVarTuple) is a *variadic* type variable, enabling *variadic* generics.


> This enables a wide variety of use cases. In particular, it allows the type of array-like structures in numerical computing libraries such as NumPy and TensorFlow to be parameterised with the array *shape*. Static type checkers will now be able to catch shape-related bugs in code that uses these libraries.

### Typing: `@dataclass_transform` ([PEP 681](https://peps.python.org/pep-0681/))


> [`dataclass_transform`](https://docs.python.org/3/library/typing.html#typing.dataclass_transform) may be used to decorate a class, metaclass, or a function that is itself a decorator. The presence of `@dataclass_transform()` tells a static type checker that the decorated object performs runtime “magic” that transforms a class, giving it [`dataclass`](https://docs.python.org/3/library/dataclasses.html#dataclasses.dataclass)-like behaviors.


```
# The create_model decorator is defined by a library.
@typing.dataclass_transform()
def create_model(cls: Type[T]) -> Type[T]:
  cls.__init__ = ...
  cls.__eq__ = ...
  cls.__ne__ = ...
  return cls

# The create_model decorator can now be used to create new model classes:
@create_model
class CustomerModel:
  id: int
  name: str
```
### Star unpacking expressions allowed in `for` statements:

This is officially supported syntax


```
for x in *a, *b:
  print(x)
```
## [Python 3.12](https://docs.python.org/3/whatsnew/3.12.html) (EOL Oct 2028)

### Typing: Type Parameter Syntax ([PEP 695](https://peps.python.org/pep-0695/))

Compact annotion of generic classes and functions


```
def max[T](args: Iterable[T]) -> T:
  ...

class list[T]:
  def __getitem__(self, index: int, /) -> T:
    ...

  def append(self, element: T) -> None:
    ...
```
Ability to declare type aliases using `type` statement (generates `TypeAliasType`)


```
type Point = tuple[float, float]

# Type aliases can also be generic
type Point[T] = tuple[T, T]
```
### F-string changes ([PEP 701](https://peps.python.org/pep-0701/))


> Expression components inside f-strings can now be any valid Python expression, including strings reusing the same quote as the containing f-string, multi-line expressions, comments, backslashes, and unicode escape sequences.

Can re-use quotes (including nesting f-string statements


```
## Can re-use quotes
f"This is the playlist: {", ".join(songs)}"

f"{f"{f"{f"{f"{f"{1+1}"}"}"}"}"}" # '2'

## Multiline f-string with comments
f"This is the playlist: {", ".join([
  'Take me back to Eden',  # My, my, those eyes like fire
  'Alkaline',              # Not acid nor alkaline
  'Ascensionism'           # Take to the broken skies at last
])}"

## Backslashes / Unicode
f"This is the playlist: {"\n".join(songs)}"

f"This is the playlist: {"\N{BLACK HEART SUIT}".join(songs)}"
```
### Buffer protocol ([PEP 688](https://peps.python.org/pep-0688/))


> [**PEP 688**](https://peps.python.org/pep-0688/) introduces a way to use the [buffer protocol](https://docs.python.org/3/c-api/buffer.html#bufferobjects) from Python code. Classes that implement the [`__buffer__()`](https://docs.python.org/3/reference/datamodel.html#object.__buffer__) method are now usable as buffer types.


> The new [`collections.abc.Buffer`](https://docs.python.org/3/library/collections.abc.html#collections.abc.Buffer) ABC provides a standard way to represent buffer objects, for example in type annotations. The new [`inspect.BufferFlags`](https://docs.python.org/3/library/inspect.html#inspect.BufferFlags) enum represents the flags that can be used to customize buffer creation.

### Typing: `Unpack` for `**kwargs` typing ([PEP 692](https://peps.python.org/pep-0692/))


```
from typing import TypedDict, Unpack

class Movie(TypedDict):
  name: str
  year: int

def foo(**kwargs: Unpack[Movie]):
  ...
```
### Typing: `override` decorator ([PEP 698](https://peps.python.org/pep-0698/))

Ensure's that the method being overridden by a child class actually exists in a parent class.


```
from typing import override

class Base:
  def get_color(self) -> str:
    return "blue"

class GoodChild(Base):
  @override  # ok: overrides Base.get_color
  def get_color(self) -> str:
    return "yellow"

class BadChild(Base):
  @override  # type checker error: does not override Base.get_color
  def get_colour(self) -> str:
    return "red"
```
# Useful Things

## Postponed Annotations ([PEP 563](https://peps.python.org/pep-0563/))

In newer versions of Python, typing annotations are stored as strings when they are initially parsed. This helps with preventing circular imports, needing to quote references before they are defined, and many other issues. All versions of Python from 3.7 support  
`from __future__ import annotations`  
which allows the interpreter to parse using this new format.

Note: PEP 563 has been superseded by [PEP 649](https://peps.python.org/pep-0649/) which will be implemented in Python 3.13.

## Typing Extensions

This library back-ports typing features so that they are available to type checkers inspecting older code bases.


```
import sys

if sys.version_info < (3, 10):
  from typing_extensions import TypeAlias
else:
  from typing import TypeAlias
```
[GitHub - python/typing\_extensions: Backported and experimental type hints for PythonBackported and experimental type hints for Python. Contribute to python/typing\_extensions development by creating an account on GitHub.![](https://github.com/fluidicon.png)GitHubpython![](https://opengraph.githubassets.com/0f8d9da934deba8a9b780f965eb7a8a75fadb7efb3355a652ddc987e5a24aafe/python/typing_extensions)](https://github.com/python/typing_extensions)## Python Support Schedule

To keep track of which versions of Python are support I use the following website:

[PythonCheck end-of-life, release policy and support schedule for Python.![](https://endoflife.date/assets/apple-touch-icon.png)endoflife.date![](https://endoflife.date/assets/logo-512x512.png)](https://endoflife.date/python)## Ruff

This is a linter and code formatter written in Rust. It's becoming very popular as it can replace a number of existing tools and is very fast. It also includes the ability to auto-fix errors.

Thus you can combine ruff with it's pyupgrade compatible linter (`UP`) and then use  
`ruff check --fix` to auto upgrade the code base.

When using `pyproject.toml` ruff will respect the versions specified by  
`project.requires-python`.

[GitHub - astral-sh/ruff: An extremely fast Python linter and code formatter, written in Rust.An extremely fast Python linter and code formatter, written in Rust. - GitHub - astral-sh/ruff: An extremely fast Python linter and code formatter, written in Rust.![](https://github.com/fluidicon.png)GitHubastral-sh![](https://opengraph.githubassets.com/92aab8b067f8bd03006eff26db63239960e7e38687c2d3d8a5b28ab8b98131a1/astral-sh/ruff)](https://github.com/astral-sh/ruff)## Pyupgrade

This tool can be used to automatically upgrade your code base.

[GitHub - asottile/pyupgrade: A tool (and pre-commit hook) to automatically upgrade syntax for newer versions of the language.A tool (and pre-commit hook) to automatically upgrade syntax for newer versions of the language. - GitHub - asottile/pyupgrade: A tool (and pre-commit hook) to automatically upgrade syntax for newe...![](https://github.com/fluidicon.png)GitHubasottile![](https://opengraph.githubassets.com/b6729be5dce8f0af4aa7a1f5b5505c8735e2506a927d10c8a55d4f7bd6ba4ceb/asottile/pyupgrade)](https://github.com/asottile/pyupgrade)## Black

Black is a popular code formatter.

When using `pyproject.toml` black will respect the versions specified by  
`project.requires-python`.

[GitHub - psf/black: The uncompromising Python code formatterThe uncompromising Python code formatter. Contribute to psf/black development by creating an account on GitHub.![](https://github.com/fluidicon.png)GitHubpsf![](https://repository-images.githubusercontent.com/125266328/48aef880-6cce-11e9-9e3c-3ca0dd3ac138)](https://github.com/psf/black)



