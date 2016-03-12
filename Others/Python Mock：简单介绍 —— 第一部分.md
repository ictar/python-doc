原文：[Python Mocks: a gentle introduction - Part 1](http://blog.thedigitalcatonline.com/blog/2016/03/06/python-mocks-a-gentle-introduction-part-1)

---

As already stressed in the two introductory posts on TDD (you can find them [here](http://blog.thedigitalcatonline.com/categories/tdd/)) testing requires to write some code that uses the functions and objects you are going to develop. This means that you need to isolate a given (external) function that is part of your public API and demonstrate that it works with standard inputs and in edge cases.

For example, if you are going to develop an object that stores percentages (such as for example poll results), you should test the following conditions: the class can store a standard percentage such as 42%, the class shall give an error if you try to store a negative percentage, the class shall give an error if you store a percentage greater than 100%.

Tests shall be _idempotent_ and _isolated_. Idempotent in mathematics and computer science identifies a process that can be run multiple times without changing the status of the system. Isolated means that a test shall not change its behaviour depending on previous executions of itself, nor depend on the previous execution (or missing execution) of other tests.

Such restrictions, which guarantee that your tests are not passing due to a temporary configuration of the system or the order in which they are run, can raise big issues when dealing with external libraries and systems, or with intrinsically mutable concepts such as time. In the testing discipline, such issues are mostly faced using mocks, that is objects that pretend to be other objects.

In this series of posts I am going to review the Python `mock` library and exemplify its use. I will not cover everything you may do with mock, obviously, but hopefully I'll give you the information you need to start using this powerful library.

## Installation

First of all, mock is a Python library which development started around 2008. It was selected to be included in the standard library as of Python 3.3, which however does not prevent you to use other libraries if you prefer.

Python 3 users, thus, are not required to take any step, while for Python 2 projects you are still required to issue a `pip install mock` to install it into the system or the current virtualenv.

You may find the official documentation [here](https://docs.python.org/dev/library/unittest.mock.html). It is very detailed, and as always I strongly recommend taking your time to run through it.

## Basic concepts

A mock, in the testing lingo, is an object that simulates the behaviour of another (more complex) object. When you (unit)test an object of your library you need sometimes to access other systems your object want to connect to, but you do not really want to be forced to run them, for several reasons.

The first one is that connecting with external systems means having a complex testing environment, that is you are dropping the isolation requirement of you tests. If your object wants to connect with a website, for example, you are forced to have a running Internet connection, and if the remote website is down you cannot test your library.

The second reason is that the setup of an external system is usually slow in comparison with the speed of unit tests. We expect to run hundred of tests in seconds, and if we have to fetch information from a remote server for each of them the time easily increases by several orders of magnitude. Remember: having slow tests means that you cannot run them while you develop, which in turn means that you will not really use them for TDD.

The third reason is more subtle, and has to to with the mutable nature of an external system, thus I'll postpone the discussion of this issue for the moment.

Let us try and work with a mock in Python and see what it can do. First of all fire up a Python shell or a [Jupyter Notebook](jupyter.org) and import the library 
```py
from unittest import mock
```
If you are using Python 2 you have to install it and use
```py
import mock
```

The main object that the library provides is `Mock` and you can instantiate it without any argument
```py
m = mock.Mock()
```
This object has the peculiar property of creating methods and attributes on the fly when you require them. Let us first look inside the object to take a glance of what it provides
```py
>>> dir(m)
['assert_any_call', 'assert_called_once_with', 'assert_called_with', 'assert_has_calls', 'attach_mock', 'call_args', 'call_args_list', 'call_count', 'called', 'configure_mock', 'method_calls', 'mock_add_spec', 'mock_calls', 'reset_mock', 'return_value', 'side_effect']
```

As you can see there are some methods which are already defined into the `Mock` object. Let us read a non-existent attribute
```py
>>> m.some_attribute
<Mock name='mock.some_attribute' id='140222043808432'>
>>> dir(m)
['assert_any_call', 'assert_called_once_with', 'assert_called_with', 'assert_has_calls', 'attach_mock', 'call_args', 'call_args_list', 'call_count', 'called', 'configure_mock', 'method_calls', 'mock_add_spec', 'mock_calls', 'reset_mock', 'return_value', 'side_effect', 'some_attribute']
```

Well, as you can see this class is somehow different from what you are accustomed to. First of all its instances do not raise an `AttributeError` when asked for a non-existent attribute, but they happily return another instance of `Mock` itself. Second, the attribute you tried to access has now been created inside the object and accessing it returns the same mock object as before.
```py
>>> m.some_attribute
<Mock name='mock.some_attribute' id='140222043808432'>
```

Mock objects are callables, which means that they may act both as attributes and as methods. If you try to call the mock it just returns you another mock with a name that includes parentheses to remarks its callable nature
```py
>>> m.some_attribute()
<Mock name='mock.some_attribute()' id='140247621475856'>
```
As you can understand, such objects are the perfect tool to mimic other objects or systems, since they may expose any API without raising exceptions. To use them in tests, however, we need them to behave just like the original, which implies returning sensible values or performing operations.

## Return value

The simplest thing a mock can do for you is to return a given value every time you call it. This is configured setting the `return_value` attribute of a mock object
```py
>>> m.some attribute.return_value = 42
>>> m.some attribute()
42
```

Now the object does not return a mock object any more, instead it just returns the static value stored in the `return_value` attribute. Obviously you can also store a callable such as a function or an object, and the method will return it, but it will not run it. Let me give you an example
```py
>>> def print_answer():
...  print("42")
... 
>>> 
>>> m.some_attribute.return_value = print_answer
>>> m.some_attribute()
<function print_answer at 0x7f8df1e3f400>
```

As you can see calling `some_attribute()` just returns the value stored in `return_value`, that is the function itself. To return values that come from a function we have to use a slightly more complex attribute of mock objects called `side_effect`.

## Side effect

The `side_effect` parameter of mock objects is a very powerful tool. It accepts three different flavours of objects, callables, iterables, and exceptions, and changes its behaviour accordingly.

If you pass an exception the mock will raise it
```py
>>> m.some_attribute.side_effect = ValueError('A custom value error')
>>> m.some_attribute()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/lib/python3.4/unittest/mock.py", line 902, in __call__
    return _mock_self._mock_call(*args, **kwargs)
  File "/usr/lib/python3.4/unittest/mock.py", line 958, in _mock_call
    raise effect
ValueError: A custom value error
```

If you pass an iterable, such as for example a generator, or a plain list, tuple, or similar objects, the mock will yield the values of that iterable, i.e. return every value contained in the iterable on subsequent calls of the mock. Let me give you an example
```py
>>> m.some_attribute.side_effect = range(3)
>>> m.some_attribute()
0
>>> m.some_attribute()
1
>>> m.some_attribute()
2
>>> m.some_attribute()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/lib/python3.4/unittest/mock.py", line 902, in __call__
    return _mock_self._mock_call(*args, **kwargs)
  File "/usr/lib/python3.4/unittest/mock.py", line 961, in _mock_call
    result = next(effect)
StopIteration
```

As promised, the mock just returns every object found in the iterable (in this case a `range` object) once at a time until the generator is exhausted. According to the iterator protocol (see [this post](blog/2013/03/25/python-generators-from-iterators-to-cooperative-multitasking/)) once every item has been returned the object raises the `StopIteration` exception, which means that you can correctly use it in a loop.

The last and perhaps most used case is that of passing a callable to `side_effect`, which shamelessly executes it with its own same parameters. This is very powerful, especially if you stop thinking about "functions" and start considering "callables". Indeed, `side_effect` also accepts a class and calls it, that is it can instantiate objects. Let us consider a simple example with a function without arguments
```py
>>> def print_answer():
...     print("42")       
>>> m.some_attribute.side_effect = print_answer
>>> m.some_attribute.side_effect()
42
```

A slightly more complex example: a function with arguments
```py
>>> def print_number(num):
...     print("Number:", num)
... 
>>> m.some_attribute.side_effect = print_number
>>> m.some_attribute.side_effect(5)
Number: 5
```

And finally an example with a class
```py
>>> class Number(object):
...     def __init__(self, value):
...         self._value = value
...     def print_value(self):
...         print("Value:", self._value)
... 
>>> m.some_attribute.side_effect = Number
>>> n = m.some_attribute.side_effect(26)
>>> n
<__main__.Number object at 0x7f8df1aa4470>
>>> n.print_value()
Value: 26
```

## Testing with mocks

Now we know how to build a mock and how to give it a static return value or make it call a callable object. It is time to see how to use a mock in a test and what facilities do mocks provide. I'm going to use [pytest](http://pytest.org) as a testing framework. You can find a quick introduction to pytest and TDD [here](/categories/tdd/)).

### Setup

If you want to quickly setup a pytest playground you may execute this code in a terminal (you need to have Python 3 and virtualenv installed in your system) 
```sh
mkdir mockplayground
cd mockplayground
virtualenv venv3 -p python3
source venv3/bin/activate
pip install --upgrade pip
pip install pytest
echo "[pytest]" >> pytest.ini
echo "norecursedirs=venv*" >> pytest.ini
mkdir tests
touch myobj.py
touch tests/test_mock.py
PYTHONPATH="." py.test
```

The `PYTHONPATH` environment variable is an easy way to avoid having to setup a whole Python project to just test some simple code.

### The three test types

According to Sandy Metz we need to test only three types of messages (calls) between objects:

*   Incoming queries (assertion on result)
*   Incoming commands (assertion on direct public side effects)
*   Outgoing commands (expectation on call and arguments)

You can see the original talk [here](https://www.youtube.com/watch?v=URSWYvyc42M) or read the slides [here](https://speakerdeck.com/skmetz/magic-tricks-of-testing-railsconf). The final table is shown in slide number 176.

As you can see when dealing with external objects we are only interested in knowing IF a method was called and WHAT PARAMETERS the caller passed to the object. We are not testing if the remote object returns the correct result, this is faked by the mock, which indeed returns exactly the result we need.

So the purpose of the methods provided by mock objects is to allow us to check what methods we called on the mock itself and what parameters we used in the call.

### Asserting calls

To show how to use Python mocks in testing I will follow the TDD methodology, writing tests first and then writing the code that makes the tests pass. In this post I want to give you a simple overview of the mock objects, so I will not implement a real world use case, and the code will be very trivial. In the second part of this series I will test and implement a real class, in order to show some more interesting use cases.

The first thing we are usually interested in when dealing with an external object is to know that a given method has been called on it. Python mocks provide the `assert_called_with()` method to check this condition.

The use case we are going to test is the following. _We instantiate the `myobj.MyObj` class, which requires an external object. The class shall call the `connect()` method of the external object without any parameter._
```py
from unittest import mock
import myobj

def test_instantiation():
    external_obj = mock.Mock()
    myobj.MyObj(external_obj)
    external_obj.connect.assert_called_with()
```

The `myobj.MyObj` class, in this simple example, needs to connect to an external object, for example a remote repository or a database. The only thing we need to know for testing purposes is if the class called the `connect()` method of the external object without any parameter.

So the first thing we do in this test is to instantiate the mock object. This is a fake version of the external object, and its only purpose is to accept calls from the `MyObj` object under test and return sensible values. Then we instantiate the `MyObj` class passing the external object. We expect the class to call the `connect()` method so we express this expectation calling `external_obj.connect.assert_called_with()`.

What happens behind the scenes? The `MyObj` class receives the external object and somewhere is its initialization process calls the `connect()` method of the mock object and this creates the method itself as a mock object. This new mock records the parameters used to call it and the subsequent call to `assert_called_with()` checks that the method was called and that no parameters were passed.

Running pytest the test obviously fails.
```sh
$ PYTHONPATH="." py.test
========================================== test session starts ==========================================
platform linux -- Python 3.4.3+, pytest-2.9.0, py-1.4.31, pluggy-0.3.1
rootdir: /home/leo/devel/mockplayground, inifile: pytest.ini
collected 1 items 

tests/test_mock.py F

=============================================== FAILURES ================================================
___________________________________________ test_instantiation __________________________________________

    def test_instantiation():
        external_obj = mock.Mock()
>       myobj.MyObj(external_obj)
E       AttributeError: 'module' object has no attribute 'MyObj'

tests/test_mock.py:6: AttributeError
======================================= 1 failed in 0.03 seconds ========================================
$
```

Putting this code in `myobj.py` is enough to make the test pass
```py
class MyObj():
    def __init__(self, repo):
        repo.connect()
```

As you can see, the `__init__()` method actually calls `repo.connect()`, where `repo` is expected to be a full-featured external object that provides a given API. In this case (for the moment) the API is just its `connect()` method. Calling `repo.connect()` when `repo` is a mock object silently creates the method as a mock object, as shown before.

The `assert_called_with()` method also allows us to check the parameters we passed when calling. To show this let us pretend that we expect the `MyObj.setup()` method to call `setup(cache=True, max_connections=256)` on the external object. As you can see we pass a couple of arguments (namely `cache` and `max_connections`) to the called method, and we want to be sure that the call was exactly in this form. The new test is thus
```py
def test_setup():
    external_obj = mock.Mock()
    obj = myobj.MyObj(external_obj)
    obj.setup()
    external_obj.setup.assert_called_with(cache=True, max_connections=256)
```

As usual the first run fails. Be sure to check this, it is part of the TDD methodology. You must have a test that DOES NOT PASS, then write some code that make it pass.
```sh
$ PYTHONPATH="." py.test
========================================== test session starts ==========================================
platform linux -- Python 3.4.3+, pytest-2.9.0, py-1.4.31, pluggy-0.3.1
rootdir: /home/leo/devel/mockplayground, inifile: pytest.ini
collected 2 items 

tests/test_mock.py .F

=============================================== FAILURES ================================================
______________________________________________ test_setup _______________________________________________

    def test_setup():
        external_obj = mock.Mock()
        obj = myobj.MyObj(external_obj)
>       obj.setup()
E       AttributeError: 'MyObj' object has no attribute 'setup'

tests/test_mock.py:14: AttributeError
================================== 1 failed, 1 passed in 0.03 seconds ===================================
$
```

To show you what type of check the mock object provides let me implement a partially correct solution

```py
class MyObj():
    def __init__(self, repo):
        self._repo = repo
        repo.connect()

    def setup(self):
        self._repo.setup(cache=True)
```

As you can see the external object has been stored in `self._repo` and the call to `self._repo.setup()` is not exactly what the test expects, lacking the `max_connections` parameter. Running pytest we obtain the following result (I removed most of the pytest output)
```
E           AssertionError: Expected call: setup(cache=True, max_connections=256)
E           Actual call: setup(cache=True)
```

and you see that the error message is very clear about what we expected and what happened in our code.

As you can read in the official documentation, the `Mock` object also provides the following methods and attributes: `assert_called_once_with`, `assert_any_call`, `assert_has_calls`, `assert_not_called`, `called`, `call_count`. Each of them explores a different aspect of the mock behaviour concerning calls, make sure to check their description and the examples provided along.

## Final words

In this first part of the series I described the behaviour of mock objects and the methods they provide to simulate return values and to test calls. They are a very powerful tool that allows you to avoid creating complex and slow tests that depend on external facilities to run, thus missing the main purpose of tests, which is that of _continuously_ helping you to check your code.

In the next issue of the series I will explore the automatic creation of mock methods from a given object and the very important patching mechanism provided by the `patch` decorator and context manager.

## Feedback

Feel free to use [the blog Google+ page](https://plus.google.com/u/0/111444750762335924049) to comment the post. The [GitHub issues](http://github.com/TheDigitalCatOnline/thedigitalcatonline.github.com/issues) page is the best place to submit corrections.
