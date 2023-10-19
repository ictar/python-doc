原文：[Building a Japanese Kanji Flashcard App](https://adilmoujahid.com/posts/2023/10/kanji-gpt4/)

---

# 使用 GPT-4、Python 和 Langchain，构建日语汉字抽认卡应用程序

近几个月来，GPT-4 吸引了主流的关注，ChatGPT 等应用程序展示了其广泛的功能，并为技术的代际转变奠定了基础。可以通过 API 以编程方式访问 GPT-4 模型，从而能够创建具有丰富的上下文相关数据的多样化应用程序。这篇博文旨在揭开利用 GPT-4 构建应用程序的过程的神秘面纱。我们将通过逐步开发日语汉字抽认卡应用程序来探索这一点，使用 GPT-4 来构建该应用程序并为其提供有价值的数据。

随着我们的步步推进，我们将探索学习日语汉字的挑战，利用 ChatGPT 构建前端，利用 GPT-4 的数据生成逻辑动态获取和格式化汉字数据，最终将其整合为一个使用 Python 和 Flask 的统一应用程序。

您可以在下面的动画 GIF 中查看最终成果，并在[此 GitHub 存储库](https://github.com/adilmoujahid/kanji-flashcard-app-gpt4)中找到该项目的源代码。

![](https://adilmoujahid.com/images/kanji-gpt4/kanji-gpt4.gif)


# 1. 定义用例

当学生开始踏上日语学习的旅程时，他们会遇到一个迷人而复杂的书写系统，由三种文字组成：平假名、片假名和汉字。例如，短语“東京タワーは高いです。”（Tōkyō Tawā wa takai desu），其中“東京”（东京）是汉字，“タワー”（塔）是片假名，连接语法是平假名。这句话翻译过来就是“东京塔很高”。各种文字组合在一起，它们具有不同的功能——汉字用于大多数名词、动词和形容词，片假名用于外来词和借用词，平假名主要用于语法功能 —— 这是日语书面语言的一个显着特征。

![](https://adilmoujahid.com/images/kanji-gpt4/hiragana-katakana-kanji.png)

平假名和片假名各有 46 个基本字符，属于注音文字，而汉字是由中国汉字改编而成的，具有含义，并且通常有多种读法。虽然汉字字符有数以万计个，但日本的一般识字能力围绕着 2,136 个字符进行，如[Joyo Kanji List](https://en.wikipedia.org/wiki/List_of_j%C5%8Dy%C5%8D_kanji) 所定义。

在下表中，您可以看到平假名和片假名字符及其相应读法。在每个框中，平假名字符显示在左侧，片假名字符显示在右侧。


![](https://adilmoujahid.com/images/kanji-gpt4/hiragana-katakana-list.png)

下面您可以看到汉字字符的一小部分示例。

![](https://adilmoujahid.com/images/kanji-gpt4/kanji-sample.png)

为了解决日语汉字学生面临的挑战，我们的目标是构建一个由 GPT-4 功能支持的汉字抽认卡应用程序，以促进学习之旅。该应用程序充当动态学习伴侣，使用户能够用自然语言指定他们希望探索的特定汉字。利用 GPT-4 的强大功能，该应用程序会自动整理符合用户明确需求的汉字列表，并通过提供单词的多项选择阅读来进一步评估他们的能力，确保提供积极有效的学习体验。

# 2. 构建前端

我们的下一步是为我们的汉字抽认卡应用程序构建前端，我们将在第 4 节中将其与 GPT-4 API 集成在一起。为了实现这一目标，我们将采用 ChatGPT，利用 GPT-4 模型，并应用以下提示生成我们前端的 HTML/CSS/JS 代码。

```
Develop a flashcard app to facilitate the study of Japanese Kanji, utilizing HTML, JavaScript, and CSS for implementation. The app should have the following functionalities:
1- Upon launching, the app presents a Japanese word in Kanji, accompanied by four buttons containing Hiragana readings: one correct and three incorrect options.
2- When the user selects an answer, the corresponding button should be highlighted in green if it's correct, and in red if it's wrong, while also highlighting the correct button in green.
3- Once an answer is selected, the app should display the English translation of the word, present the word within the context of a Japanese sentence, and also provide its English translation.
4- Include a button to transition to the subsequent word.
5- Populate the app with 10 different words in Kanji to test the app. The incorrect options should also be realistic and relevant to the correct answer.
6- Make sure the app is centered on the screen and use simple styling.

```

下面的 gif 显示了完全使用 ChatGPT 制作的前端。这一令人印象深刻的结果证明了 GPT-4 能够简化开发流程并使其更易于使用，即使对于那些前端经验有限的人来说也是如此。 

![](https://adilmoujahid.com/images/kanji-gpt4/kanji_UI_1.gif)


# 3. 构建数据生成逻辑

在本节中，我们将使用 GPT-4 构建日语汉字学习抽认卡应用程序的后端逻辑。该后端将负责获取和格式化汉字数据。为了实现这一目标，我们将结合使用 Python 和 LangChain。LangChain 是一个专门的框架，旨在创建由大型语言模型（包括来自 OpenAI 的模型）驱动的应用程序。它提供了与 API 接口交互、制作提示和构建返回输出的各种抽象。

我们将从导入必要的库开始。在 Langchain 中，我们特别需要 `ChatOpenAI` 来与 GPT-4 进行通信，以及 `ChatPromptTemplate` 为我们的用例创建提示模板。

```py
import os
from langchain.chat_models import ChatOpenAI
from langchain.prompts import ChatPromptTemplate

```
接下来，我们指定要部署的模型（“gpt-4-0613”）。确保正确配置我们的 API 密钥至关重要。完成此操作后，我们与 GPT-4 建立连接。有关可用 OpenAI API 的完整列表，请访问[此处](https://platform.openai.com/account/rate-limits)。有关 API 的详细信息以及获取API 密钥的说明，您可以参考 [此链接](https://openai.com/product)。

```py
llm_model = "gpt-4-0613"
OPENAI_API_KEY = openai_API_key
os.environ["OPENAI_API_KEY"] = OPENAI_API_KEY

chat = ChatOpenAI(temperature=1, model=llm_model)
```
接下来，我们构建一个专门针对我们的要求设计的 Langchain 模板。将 Langchain 模板视为预定义的表单。这些表单允许我们构建 GPT-4 的提示，其中包含我们希望在发送请求之前设置的特定变量。出于此目的，该模板将包含旨在检索和格式化汉字单词以及附加数据的提示。在本例中，我们的变量用 `{description}` 表示，它将表示我们感兴趣的汉字的具体描述。



```py
string_template = """Give 2 words written in Kanji that are: ```{description}```, \
accompanied with its correct Hiragana reading and three incorrect Hiragana readings \
that are realistic and relevant to the correct answer. \
Also give me the English translation of the word, and present the word within the context \
of a Japanese sentence, and also provide its English translation.

Format the output as JSON with the data represented as an array of dictionaries with the following keys:
"word": str  // Japanese word written in Kanji
"correct": str  // Correct reading of the Kanji word in Hiragana
"incorrect": List[str] //Incorrect readings of the Kanji phrase
"english": str  // English translation of the Kanji word
"sentenceJP": str  // Example sentence in Japanese using the Kanji word
"sentenceEN": str  // English translation of the example sentence
"""

prompt_template = ChatPromptTemplate.from_template(string_template)
```

有了我们手头的模板，我们就可以从 GPT-4 中检索汉字单词，例如，询问与经济学相关的汉字。

```py
description_example = "related to Economics"
kanji_request = prompt_template.format_messages(description=description_example)

kanji_response = chat(kanji_request)
print(kanji_response.content)

```

在本例中，我们收到了一个结构良好的 JSON。但是，如果响应与我们所需的格式不匹配，Langchain 会提供各种[输出解析器](https://python.langchain.com/docs/modules/model_io/output_parsers/)来帮助我们相应地调整输出。


```py
[
  {
    "word": "経済",
    "correct": "けいざい",
    "incorrect": ["けいせい", "えいざい", "けんざい"],
    "english": "economics",
    "sentenceJP": "経済の状況を理解するためのデータが必要です。",
    "sentenceEN": "We need data to understand the economic situation."
  },
  {
    "word": "財政",
    "correct": "ざいせい",
    "incorrect": ["さいせい", "ざいぜい", "ざいしょう"],
    "english": "finance",
    "sentenceJP": "政府は財政問題に対応するための新たな策を立てます。",
    "sentenceEN": "The government will devise new measures to deal with financial problems."
  }
]

```

# 4. 整一块

数据生成准备就绪后，我们现在需要将其连接到我们的前端。我们将为此使用 Flask。Flask 会将我们的数据生成逻辑转变为 API，并管理我们的前端。代码很短，不到 50 行，有两条主要路由：服务前端的根路由 `(/)` 和根据前端输入调用数据生成逻辑并以 JSON 格式返回汉字数据的路由 `/get_words`。

```py
import os
import json
from config import *
from langchain.chat_models import ChatOpenAI
from langchain.prompts import ChatPromptTemplate
from flask import Flask, render_template, request, jsonify

app = Flask(__name__)

llm_model = "gpt-4-0613"
OPENAI_API_KEY = openai_API_key
os.environ["OPENAI_API_KEY"] = OPENAI_API_KEY

chat = ChatOpenAI(temperature=1, model=llm_model)


string_template = """Give 2 words written in Kanji that are: ```{description}```, \
accompanied with its correct Hiragana reading and three incorrect Hiragana readings \
that are realistic and relevant to the correct answer. \
Also give me the English translation of the word, and present the word within the context \
of a Japanese sentence, and also provide its English translation.

Format the output as JSON with the data represented as an array of dictionaries with the following keys:
"word": str  // Japanese word written in Kanji
"correct": str  // Correct reading of the Kanji word in Hiragana
"incorrect": List[str] //Incorrect readings of the Kanji phrase
"english": str  // English translation of the Kanji word
"sentenceJP": str  // Example sentence in Japanese using the Kanji word
"sentenceEN": str  // English translation of the example sentence
"""

prompt_template = ChatPromptTemplate.from_template(string_template)

@app.route('/')
def home():
    return render_template('index.html')

@app.route('/get_words', methods=['POST'])
def get_word():
    description = request.json.get('description', '')
    words_request = prompt_template.format_messages(description=description)
    words_response = chat(words_request)
    return jsonify(json.loads(words_response.content))

if __name__ == "__main__":
    app.run(port=5000)
```

在前端方面，我们引入了一些细微的变化：一个输入字段和一个按钮，让用户能够指定他们希望探索的汉字类型，并附有一个指示数据检索过程的加载旋转器。

要启动应用程序，请从终端运行命令 `python app.py`，然后在您首选的浏览器中访问 http://127.0.0.1:5000。


![](https://adilmoujahid.com/images/kanji-gpt4/kanji-gpt4.gif)

# 5. 优化和扩展我们应用的一些想法

我们构建的应用程序功能齐全，非常适合学习汉字。尽管如此，我们仍然需要注意某些成本和性能方面的考虑。

### 成本

OpenAI 的定价模型根据 API 调用期间消耗的代币进行收费。截至撰写本文时：

- GPT-4（8K 上下文）的输入价格为**每 1K 代币 0.03 美元**，输出**每 1K 代币价格为 0.06 美元**。
- GPT-3.5 Turbo（4K 上下文）的输入价格为**每 1K 代币 0.0015 美元**，输出**每 1K 代币价格为 0.002 美元**。
- 您可以在[此处](https://openai.com/pricing)找到 OpenAI 定价详细信息。

对于我们的特定场景，通过 GPT-4 获取和格式化 5 个汉字单词的提示使用大约 188 个输入标记和 176 个输出标记，这意味着总费用为 0.0162 美元。

要获取消耗的代币数量和美元成本，您可以执行以下代码：

```py
from langchain.callbacks import get_openai_callback

with get_openai_callback() as cb:
    description_example = "related to Economics"
    kanji_request = prompt_template.format_messages(description=description_example)
    kanji_response = chat(kanji_request)
    print(cb)

```

虽然这种成本结构对于一些 API 调用来说似乎是可以接受的，但扩展应用程序以满足更大的用户群会增加这些费用。

### 执行时间

使用 GPT-4 获取并格式化 5 个汉字单词大约需要 17.2 秒。这种延迟会对用户体验产生负面影响。

为了有效地优化和扩展我们的应用程序，我们可以考虑一种将数据源与 GPT-4 API 调用相结合并简化提示和输出格式的方法。例如，我们可以从常用汉字列表中获取所有字符，并使用 GPT-4 进行一次性翻译和列举例句。然后，可以通过要求 GPT-4 获取与特定主题相关的汉字单词来简化提示，而无需翻译或句子示例。之后，我们可以将这些汉字与我们预先生成的句子进行匹配。此方法可能会加快执行时间并减少令牌使用。


# 总结

总之，GPT-4 正在改变应用程序开发的游戏规则，尤其是在处理数据方面。我们的日语抽认卡应用程序展示了 GPT-4 有多么方便。开发人员可以使用 GPT-4 快速获取所需的信息，而不是手动收集数据。这不仅加快了构建过程，还确保应用程序充满有用的内容。借助 GPT-4 等工具，创建数据丰富的应用程序从未如此简单和高效。