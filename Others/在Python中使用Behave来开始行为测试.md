原文：[Getting Started with Behavior Testing in Python with Behave](https://semaphoreci.com/community/tutorials/getting-started-with-behavior-testing-in-python-with-behave)

---


### 介绍

行为测试只是意味着我们应该测试在某些情况下，一个应用会如何表现。通常，行为是由我们的客户提供给我们开发者的。他们描述一个应用的功能，然后我们编写代码来满足他们的要求。行为测试是一个将他们的需求正式化为测试的工具。这自然导致了行为驱动开发[(BDD)](https://semaphoreci.com/community/tutorials/behavior-driven-development)。

在完成本教程后，你应该能够：

*   解释行为测试的好处
*   解释Bebave的“给定(given)”，“当(when)”和“然后(then)”阶段
*   使用Behave编写基本的行为测试
*   使用Behave编写参数化的行为测试

## 先决条件

在开始之前，确保你安装了下面的这些：

*   [Python 3.x](https://www.python.org/downloads/)
*   [Behave](http://pythonhosted.org/behave/install.html)

## 设置环境

本教程将带你为[21点](https://en.wikipedia.org/wiki/Blackjack) (或者"黑杰克(Blackjack)")游戏编写测试以及编码该游戏特性。具体而言，我们将测试庄家(dealer)的逻辑。要开始，我们创建一个将放置你的代码的根目录，然后创建下面的目录和空文件：

```py
.
├── features
│   ├── dealer.feature
│   └── steps
│       └── steps.py
└── twentyone.py
```

这里是文件的简要说明：

*   `dealer.feature`: 为庄家(dealer)特性编写的测试。
*   `steps.py`: 在`dealer.feature`中运行测试的代码。
*   `twentyone.py`: 庄家(dealer)特性的实现代码。

## 编写第一个测试

虽然行为测试不需要测试驱动的开发，但是这两种方法相辅相成。我们将从测试驱动的角度来处理这个问题，所以取代直接跳跃到代码的做法，我们将从测试开始。

### 编写场景

打开`dealer.feature`，然后添加以下第一行：
```
    Feature: The dealer for the game of 21
```

这一行描述特性。在一个大型应用中，你将有许多特性。接下来，我们将添加一个测试。第一个测试将是简单的 —— 当一轮开始时，庄家(dealer)应该自行处理两张牌。Behave用来定义一个测试的词是“场景(Scenario)”，所以我们继续添加下面这一行：

`Scenario: Deal initial cards`

在我们写更多之前，需要了解一个基本的Behave测试的三个阶段：“给定(Given)”，“当(When)”和“然后(Then)”。“给定(Given)”初始化状态，“当(When)”描述动作，而“然后(Then)”规定预期结果。对于这个测试，我们的状态是一个新的dealer对象，动作是该轮开始，而预期输出是庄家(dealer)有两张卡片。下面是这个怎么翻译成一个Behave测试：

```
Scenario: Deal initial cards
  Given a dealer
  When the round starts
  Then the dealer gives itself two cards
```

注意，这三个阶段读起来像一个正常的英语句子。当编写行为测试时，你应该努力做到这点，因为它们是易于可被任何工作在代码库上的人读的。

现在，看看Behave如何工作，只是在你的代码的根目录中打开一个终端，运行以下命令：

```py
behave
```

你应该看到这个输出：

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

这里的关键部分是，我们有一个需要修复的失败场景（因此有一个失败的特性）。在它之下，Behave提出如何实现的步骤。你能想到一个步骤作为Behave执行的一个任务。每个阶段（“给定(Given)”，“当(When)”和“然后(Then)”）都实现为步骤。

### 编写步骤

Behave运行的每个步骤都是用Python编写的，它们是在`.feature`文件里的描述性测试和实际的应用代码之间的链接。继续，打开`steps.py`然后添加下述导入：
```py
from behave import *
from twentyone import *
```

Behave步骤使用与阶段名相匹配的注释。这是作为在场景中描述的第一个步骤：
```py
@given('a dealer')
def step_impl(context):
    context.dealer = Dealer()
```

重要的是要注意到，注释内的文本完全匹配场景文本。如果不匹配，那么测试无法运行。

上下文对象从一步传递到下一步，而它是我们可以存储用于其他步骤的信息的地方。由于这一步是一个“给定”，因此我们需要初始化我们的状态。我们通过创建一个`Dealer`对象，并把该对象附加到`context`来做到这点。如果你再次运行`behave`，那么你会看到测试失败，但是是出于一个不同的原因：我们还没有定义Dealer类呢！再次，我们有一个失败的测试，它“驱动”我们工作。

现在，我们将打开`twentyone.py`，并创建一个`Dealer`类：
```py
class Dealer():
    pass
```

再次`behave`来验证我们修复了最后看到的错误，但是，该场景仍然失败，因为“当”和“然后”步骤尚未实现。从这里开始，当你应该运行`behave`时，本教程将不会明确说明。但是记住，周期是编写一个测试，看它运行失败后，编写代码让测试通过。

以下是要添加到`steps.py`的下一步:
```py
@when('the round starts')
def step_impl(context):
    context.dealer.new_round()


@then('the dealer gives itself two cards')
def step_impl(context):
    assert (len(context.dealer.hand) == 2)
```

同样地，注释文本完全匹配场景中的文本。在“当”这一步，我们访问了在“给定”中创建的`dealer`，我们现在可以调用对象的方法。最后，在“然后”这一步，我们仍然访问该`dealer`，然后我们断言该庄家(dealer)手中有两张牌。

我们定义了需要实现的两段新代码：`new_round()`和`hand`。切换到`twentyone.py` ，然后添加下面代码到`Dealer`类：
```py
class Dealer():
    def __init__(self):
        self.hand = []

    def new_round(self):
        self.hand = [_next_card(), _next_card()]
```

`_next_card()`函数将与牌的定义一起，被定义为模块的顶层函数。在该文件的顶部，添加以下内容：
```py
import random

_cards = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K']


def _next_card():
    return random.choice(_cards)
```

请记住，`random`是不安全的，并且不应该用于这个游戏的真实实现，但是对于该教材，用它没有问题。

如果你现在运行`behave`，那么你应该会看到，测试通过了：

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

## 编写表格化测试

通常，编写测试时，我们想要针对许多不懂的参数测试相同的行为并检查结果。Behave通过提供工具来创建一个表格化测试，而不是分别编写每个测试来使我们可以容易做到这点。下一个要测试的游戏逻辑是，庄家(dealer)知道其手中的点值。这里是一个坚持多个场景的测试：

```
Scenario Outline: Get hand total
  Given a <hand>
  When the dealer sums the cards
  Then the <total> is correct

  Examples: Hands
  | hand          | total |
  | 5,7           | 12    |
  | 5,Q           | 15    |
  | Q,Q,A         | 21    |
  | Q,A           | 21    |
  | A,A,A         | 13    |
```

你应该认出了熟悉的“给定，当，然后”模式，但在这个测试中，有许多不同。首先，它称为“场景纲要”。接着，它使用与表头对应的尖括号中的参数。最后，有一个输入("hand")和输出("total")的表格。

步骤跟我们之前看到的相似，但是我们现在将开始使用Behave的参数化步骤特性。

这里是如何实现新的“给定”步骤：
```py
@given('a {hand}')
def step_impl(context, hand):
    context.dealer = Dealer()
    context.dealer.hand = hand.split(',')
```

`dealer.feature`文件中的尖括号被括号取代，而`hand`参数则变成一个传递给该步骤的参数，并带有上下文。

和从前一样，我们创建了一个新的`Dealer`对象，但这次，我们手动设置庄家的牌，而不是随机的生成它们。由于`hand`参数是一个简单的字符串，因此我们分隔该参数来获得一个列表。

接下来，添加剩余步骤：
```py
@when('the dealer sums the cards')
def step_impl(context):
    context.dealer_total = context.dealer.get_hand_total()

@then('the {total:d} is correct')
def step_impl(context, total):
    assert (context.dealer_total == total)
```

“当”步骤并没啥新的东东，而“然后”步骤应该看起来是相似的。如果你想知道有关`total`参数后面的":d"，那么这是一个捷径，它告诉Behave将该参数当成一个整数。它让我们省去了手工使用`int()`函数进行类型转换。这里是一个完整的Behave接受的[模式列表](https://pythonhosted.org/behave/parse_builtin_types.html)，而如果你需要高级解析，那么可以[定义你自己的模式](https://pythonhosted.org/behave/api.html#step-parameters)。

有许多不同的方法来计算牌的总值，但是有一个方法来查找庄家手中的牌的点数。将这个作为`twentyone.py`模块中的顶级函数来创建：
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

总之，该函数映射卡字符串到点值，然后计算值的总和。然而，A必须区别处理，因为它们可以当做1点或11点。

我们还需要为庄家提供计算它的牌的总和的能力。添加这个函数到`Dealer`类：
```py
def get_hand_total(self):
    return _hand_total(self.hand)
```

如果你现在运行`behave`，那么你将会看到在表中的每个例子会作为它自己的场景运行。这节省了特性文件中的许多空间，但还是为我们严格提供了单独测试成功或失败的结果。

我们将再添加一个表格化测试，这一次是测试庄家按照规则玩游戏。传统上，庄家必须“拿牌”，直到他或她有了17点或17点以上。添加这个场景来测试该行为：
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

在我们添加下一步之前，理解当使用参数时，顺序有关系很重要。参数化的步骤应该从最严格到最不严格进行排序。如果你不这样做，那么整齐的步骤可能不被Behave所匹配。要使这更容易，那么按类型对步骤进行分组。这是新的“给定”步骤，正确的排序：
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

该类型参数`{total:d}`比非类型的`{hand}`更严格，所以在文件中，它必须先出现。

新的“当”步骤不是参数化的，因此可以放在任何地方，但是，为了提高可读性，应与其他`when`步骤分组：
```py
@when('the dealer determines a play')
def step_impl(context):
    context.dealer_play = context.dealer.determine_play(context.total)
```

注意，这个测试需要一个`determine_play()`方法，我们可以添加它到`Dealer`类中：
```py
def determine_play(self, total):
    if total < 17:
        return 'hit'
    else:
        return 'stand'
```

最后，“那么”步骤是参数化的，所以它也需要正确的排序：
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

## 把所有东西放在一起

我们将添加一个最终测试，它将我们刚刚写的所有代码绑在一起。我们已经用测试自我证明了庄家可以处理他自己的牌，确定他手中的牌的总数，并且分别play，但是并没有代码来将这些绑在一起。既然我们强调测试驱动开发，那么让我们为这个行为添加一个测试。

```
Scenario: A Dealer can always play
  Given a dealer
  When the round starts
  Then the dealer chooses a play
```

我们已经为“给定”和“当”语句编写了步骤，但是我们需要为“庄家选择play”添加一个步骤。添加这个新步骤，并确保正确的排序：
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

这个测试依赖一个新的方法`make_play()`，现在，你应该把这个方法添加到`Dealer`类中：
```py
def make_play(self):
    return self.determine_play(self.get_hand_total())
```

此方法不是关键的，但它会使得使用`Dealer`类变得更容易。

如果你已经正确的做好了一切，那么运行`behave`应显示所有的测试，并给出一个类似于此的总结：

```
1 feature passed, 0 failed, 0 skipped
16 scenarios passed, 0 failed, 0 skipped
48 steps passed, 0 failed, 0 skipped, 0 undefined
Took 0m0.007s
```

## 总结

本教程带你创建一个使用Behave库的新工程，并使用测试驱动开发来构建基于行为测试的代码。

如果你想要体验使用这个工程编写更多的测试，那么尝试实现一个`Player`类和`player.feature`来玩一些[基本策略](https://en.wikipedia.org/wiki/Blackjack#Basic_strategy)。

要学习更多关于行为驱动开发以及为嘛你会想要接受它的东东，在[Behavior-Driven Development](https://semaphoreci.com/community/tutorials/behavior-driven-development)上看看我们的文章。
