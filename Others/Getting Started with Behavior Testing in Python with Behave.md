原文：[Getting Started with Behavior Testing in Python with Behave](https://semaphoreci.com/community/tutorials/getting-started-with-behavior-testing-in-python-with-behave)

---


### Introduction

Behavior testing simply means that we should test how an application behaves in
certain situations. Often the behavior is given to us developers by our
customers. They describe the functionality of an application, and we write code
to meet their specifications. Behavioral tests are a tool to formalize their
requirements into tests. This leads naturally to behavior-driven development
[(BDD)](https://semaphoreci.com/community/tutorials/behavior-driven-development).

After completing this tutorial, you should be able to:

*   Explain the benefits of behavior testing
*   Explain the "given", "when", and "then" phases of Behave
*   Write basic behavioral tests using Behave
*   Write parameterized behavioral tests using Behave

## Prerequisites

Before starting, make sure you have the following installed:

*   [Python 3.x](https://www.python.org/downloads/)
*   [Behave](http://pythonhosted.org/behave/install.html)

## Setting Up Your Environment

This tutorial will walk you through writing tests for and coding a feature of a
[Twenty-One](https://en.wikipedia.org/wiki/Blackjack) (or "Blackjack") game.
Specifically, we'll be testing the logic for the dealer. To get started, create
a root directory where your code will go, and then create the following
directories and blank files:

```py
.
├── features
│   ├── dealer.feature
│   └── steps
│       └── steps.py
└── twentyone.py
```

Here's a brief explanation of the files:

*   `dealer.feature`: The written out tests for the dealer feature.
*   `steps.py`: The code that runs the tests in `dealer.feature`.
*   `twentyone.py`: The implementation code for the dealer feature.

## Writing Your First Test

Although behavioral tests do not require test-driven development, the two
methodologies go hand-in-hand. We'll approach this problem from a test-driven
perspective, so instead of jumping to code, we'll start with the tests.

### Writing the Scenario

Open `dealer.feature` and add the following first line:
```
    Feature: The dealer for the game of 21
```

This line describes the feature. In a large application, you would have many
features. Next, we'll add a test. The first test will be simple — when the round
starts, the dealer should deal itself two cards. The word Behave uses to define
a test is "Scenario", so go ahead and add the following line:

`Scenario: Deal initial cards`

Before we write more, we need to understand the three phases of a basic Behave
test: "Given", "When", and "Then". "Given" initializes a state, "When" describes
an action, and "Then" states the expected outcome. For this test, our state is a
new dealer object, the action is the round starting, and the expected outcome is
that the dealer has two cards. Here's how this is translated into a Behave test:

```
Scenario: Deal initial cards
  Given a dealer
  When the round starts
  Then the dealer gives itself two cards
```

Notice that the three phases read like a normal English sentence. You should
strive for this when writing behavioral tests because they are easily readable
by anyone working in the code base.

Now to see how Behave works, simply open a terminal in the root directory of
your code and run the following command:

```py
behave
```

You should see this output:

```
Feature: The dealer for the game of 21 # features/dealer.feature:1

  Scenario: Deal initial cards             # features/dealer.feature:3
    Given a dealer                         # None
    When the round starts                  # None
    Then the dealer gives itself two cards # None


Failing scenarios:
  features/dealer.feature:3  Deal initial cards

0 features passed, 1 failed, 0 skipped
0 scenarios passed, 1 failed, 0 skipped
0 steps passed, 0 failed, 0 skipped, 3 undefined
Took 0m0.000s

You can implement step definitions for undefined steps with these snippets:
[ The rest of output removed for brevity ]
```

The key part here is that we have one failing scenario (and therefore a failing
feature) that we need to fix. Below that, Behave suggests how to implement
steps. You can think of a step as a task for Behave to execute. Each phase
("given", "when", and "then") are all implemented as steps.

### Writing the Steps

The steps that Behave runs are written in Python and they are the link between
the descriptive tests in `.feature` files and the actual application code. Go
ahead and open `steps.py` and add the following imports:
```py
from behave import *
from twentyone import *
```

Behave steps use annotations that match the names of the phases. This is the
first step as described in the scenario:
```py
@given('a dealer')
def step_impl(context):
    context.dealer = Dealer()
```

It's important to notice that the text inside of the annotation matches the
scenario text exactly. If it doesn't match, the test cannot run.

The context object is passed from step to step, and it is where we can store
information to be used by other steps. Since this step is a "given", we need to
initialize our state. We do that by creating a `Dealer` object, and attaching
that object to the `context`. If you run `behave` again, you'll see the test
fails, but now for a different reason: We haven't defined the Dealer class yet!
Again, we have a failing test that is "driving" us to do work.

Now we will open `twentyone.py` and create a `Dealer` class:
```py
class Dealer():
    pass
```

Run `behave` once again to verify that we fixed the last error we saw, but that
the scenario still fails because the "when" and "then" steps are not
implemented. From here on, the tutorial will not explicitly state when you
should run `behave`. But remember, the cycle is to write a test, see that it
fails, and then write code to make the test pass.

Here are the next steps to add to `steps.py`:
```py
@when('the round starts')
def step_impl(context):
    context.dealer.new_round()


@then('the dealer gives itself two cards')
def step_impl(context):
    assert (len(context.dealer.hand) == 2)
```

Again, the annotation text matches the text in the scenario exactly. In the
"when" step, we have access to the `dealer` created in "given" and we can now
call a method on that object. Finally, in the "then" step, we still have access
to the `dealer`, and we assert that the dealer has two cards in its hand.

We defined two new pieces of code that need to be implemented: `new_round()` and
`hand`. Switch back to `twentyone.py` and add the following to the `Dealer`
class:
```py
class Dealer():
    def __init__(self):
        self.hand = []

    def new_round(self):
        self.hand = [_next_card(), _next_card()]
```

The `_next_card()` function will be defined as a top-level function of the
module, along with a definition of the cards. At the top of the file, add the
following:
```py
import random

_cards = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K']


def _next_card():
    return random.choice(_cards)
```

Remember that `random` is not secure and should not be used in a real
implementation of this game, but for this tutorial it will be fine.

If you run `behave` now, you should see that the test passes:

```
Feature: The dealer for the game of 21 # features/dealer.feature:1

  Scenario: Deal initial cards             # features/dealer.feature:3
    Given a dealer                         # features/steps/steps.py:5 0.000s
    When the round starts                  # features/steps/steps.py:9 0.000s
    Then the dealer gives itself two cards # features/steps/steps.py:14 0.000s

1 feature passed, 0 failed, 0 skipped
1 scenario passed, 0 failed, 0 skipped
3 steps passed, 0 failed, 0 skipped, 0 undefined
Took 0m0.000s
```

## Writing Tableized Tests

Often when writing tests we want to test the same behavior against many
different parameters and check the results. Behave makes this easier to do by
providing tools to create a tableized test instead of writing out each test
separately. The next game logic to test is that the dealer knows the point value
of its hand. Here is a test that checks several scenarios:

```
Scenario Outline: Get hand total
  Given a &lt;hand&gt;
  When the dealer sums the cards
  Then the &lt;total&gt; is correct

  Examples: Hands
  | hand          | total |
  | 5,7           | 12    |
  | 5,Q           | 15    |
  | Q,Q,A         | 21    |
  | Q,A           | 21    |
  | A,A,A         | 13    |
```

You should recognize the familiar "given, when, then" pattern, but there's a lot
of differences in this test. First, it is called a "Scenario Outline". Next, it
uses parameters in angle brackets that correspond to the headers of the table.
Finally, there's a table of inputs ("hand") and outputs ("total").

The steps will be similar to what we've seen before, but we'll now get to use
the parameterized steps feature of Behave.

Here's how to implement the new "given" step:
```py
@given('a {hand}')
def step_impl(context, hand):
    context.dealer = Dealer()
    context.dealer.hand = hand.split(',')
```

The angle brackets in the `dealer.feature` file are replaced with braces, and
the `hand` parameter becomes an object that is passed to the step, along with
the context.

Just like before, we create a new `Dealer` object, but this time we manually set
the dealer's cards instead of generating them randomly. Since the `hand`
parameter is a simple string, we split the parameter to get a list.

Next, add the remaining steps:
```py
@when('the dealer sums the cards')
def step_impl(context):
    context.dealer_total = context.dealer.get_hand_total()

@then('the {total:d} is correct')
def step_impl(context, total):
    assert (context.dealer_total == total)
```

The "when" step is nothing new, and the "then" step should look familiar. If
you're wondering about the ":d" after the `total` parameter, that is a shortcut
to tell Behave to treat the parameter as an integer. It saves us from manually
casting with the `int()` function. Here's a complete [list of patterns](https://pythonhosted.org/behave/parse_builtin_types.html) that Behave
accepts and if you need advanced parsing, you can [define your own pattern](https://pythonhosted.org/behave/api.html#step-parameters).

There's many different approaches to summing values of cards, but here's one
solution to find the total of the dealer's hand. Create this as a top-level
function in the `twentyone.py` module:
```py
def _hand_total(hand):
    values = [None, 2, 3, 4, 5, 6, 7, 8, 9, 10, 10, 10, 10, 10]
    value_map = {k: v for k, v in zip(_cards, values)}

    total = sum([value_map[card] for card in hand if card != 'A'])
    ace_count = hand.count('A')

    for i in range(ace_count, -1, -1):
        if i == 0:
            total = total + ace_count
        elif total + (i * 11) + (ace_count - i) <= 21:
            total = total + (i * 11) + ace_count - i
            break

    return total
```

In short, the function maps the card character strings to point values, and sums
the values. However, aces have to be handled separately because they can value
1 or 11 points.

We also need to give the dealer the ability to total its cards. Add this
function to the `Dealer` class:
```py
def get_hand_total(self):
    return _hand_total(self.hand)
```

If you run `behave` now, you'll see that each example in the table runs as its
own scenario. This saves a lot of space in the features file, but still gives us
rigorous tests that pass or fail individually.

We'll add one more tableized test, this time to test that the dealer plays by
the rules. Traditionally, the dealer must play "hit" until he or she has 17 or
more points. Add this scenario outline to test that behavior:
```
Scenario Outline: Dealer plays by the rules
  Given a hand <total>
   when the dealer determines a play
   then the <play> is correct

  Examples: Hands
  | total  | play   |
  | 10     | hit    |
  | 15     | hit    |
  | 16     | hit    |
  | 17     | stand  |
  | 18     | stand  |
  | 19     | stand  |
  | 20     | stand  |
  | 21     | stand  |
  | 22     | stand  |
```

Before we add the next steps, it's important to understand that when using
parameters, the order matters. Parameterized steps should be ordered from most
restrictive to least restrictive. If you do not do this, the correct step may
not be matched by Behave. To make this easier, group your steps by type. Here is
the new given step, ordered properly:
```py
@given('a dealer')
def step_impl(context):
    context.dealer = Dealer()

## NEW STEP
@given('a hand {total:d}')
def step_impl(context, total):
    context.dealer = Dealer()
    context.total = total


@given('a {hand}')
def step_impl(context, hand):
    context.dealer = Dealer()
    context.dealer.hand = hand.split(',')
```

The typed parameter `{total:d}` is more restrictive than the untyped `{hand}`,
so it must come earlier in the file.

The new "when" step is not parameterized and can be placed anywhere, but, for
readability, should be grouped with the other `when` steps:
```py
@when('the dealer determines a play')
def step_impl(context):
    context.dealer_play = context.dealer.determine_play(context.total)
```

Notice that this test expects a `determine_play()` method, which we can add to
the `Dealer` class:
```py
def determine_play(self, total):
    if total < 17:
        return 'hit'
    else:
        return 'stand'
```

Last, the "then" step is parameterized so it needs to also be ordered properly:
```py
@then('the dealer gives itself two cards')
def step_impl(context):
    assert (len(context.dealer.hand) == 2)


@then('the {total:d} is correct')
def step_impl(context, total):
    assert (context.dealer_total == total)

## NEW STEP
@then('the {play} is correct')
def step_impl(context, play):
    assert (context.dealer_play == play)
```

## Putting Everything Together

We're going to add one final test that will tie together all of the code we've
just written. We've proven to ourselves with tests that the dealer can deal
itself cards, determine its hand total, and make a play separately, but there's
no code to tie this together. Since we are emphasizing test-driven development,
let's add a test for this behavior.

```
Scenario: A Dealer can always play
  Given a dealer
  When the round starts
  Then the dealer chooses a play
```
We already wrote steps for the "given" and "when" statements, but we need to add
a step for "the dealer chooses a play." Add this new step, and be sure to order
it properly:
```py
@then('the dealer gives itself two cards')
def step_impl(context):
    assert (len(context.dealer.hand) == 2)

#NEW STEP
@then('the dealer chooses a play')
def step_impl(context):
    assert (context.dealer.make_play() in ['stand', 'hit'])


@then('the {total:d} is correct')
def step_impl(context, total):
    assert (context.dealer_total == total)
```

This test relies on a new method `make_play()` that you should now add to the
`Dealer` class:
```py
def make_play(self):
    return self.determine_play(self.get_hand_total())
```

This method isn't critical, but makes it easier to use the `Dealer` class.

If you've done everything correctly, running `behave` should display all of the
tests and give a summary similar to this:

```
1 feature passed, 0 failed, 0 skipped
16 scenarios passed, 0 failed, 0 skipped
48 steps passed, 0 failed, 0 skipped, 0 undefined
Took 0m0.007s
```

## Conclusion

This tutorial walked you through setting up a new project with the Behave
library and using test-driven development to build the code based off of
behavioral tests.

If you would like to get experience writing more tests with this project, try
implementing a `Player` class and `player.feature` that plays with some [basic
strategy](https://en.wikipedia.org/wiki/Blackjack#Basic_strategy).

To learn more about BDD and why you might want to adopt it, check out our
article on [Behavior-Driven Development](https://semaphoreci.com/community/tutorials/behavior-driven-development).
