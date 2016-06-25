原文：[Facebook Messenger Bot Tutorial: Step-by-Step Instructions for Building a Basic Facebook Chat Bot](https://blog.hartleybrody.com/fb-messenger-bot/)

---

首先是桌面软件产品，接着，一切都被搬到了网上。然后，基于电子邮件，甚至基于短信的产品出现了。软件界面的最新热潮是通讯机器人，而可以肯定的是，Facebook拥有最大的聊天平台。

![facebook-chatbot](https://blog.hartleybrody.com/wp-content/uploads/2016/06/facebook-chatbot.png)

在本教程中，我将向你展示如何**在Python中构建你自己的Facebook Messenger聊天机器人**。我们会使用Flask进行一些基本的web请求处理，然后我们将部署该应用到Heroku。

让我们开始吧。

### 第一步，创建一个可用的网络挂接端点(Working Webhook Endpoint)

很快，我们将开始进行发送和接收消息，但首先，你需要一个可用的端点，它返回一个200响应码，然后回显一些信息，以与Facebook验证你的机器人。

[![fb-chat-bot-github](https://blog.hartleybrody.com/wp-content/uploads/2016/06/fb-chat-bot-github-1024x533.png)](https://github.com/hartleybrody/fb-messenger-bot)

首先，`git clone`[我为这个项目建立的Github仓库](https://github.com/hartleybrody/fb-messenger-bot):

`git clone git@github.com:hartleybrody/fb-messenger-bot.git`

然后，`cd`进去，安装python依赖：

```

mkvirtualenv test-bot

pip install -r requirements.txt

```

为简单起见，我们将部署它到Heroku，但你也可以部署这个Flask web应用程序到任何你可以访问的服务器上。

假设你已经安装了[Heroku CLI Toolbelt](https://toolbelt.heroku.com/)，那么你可以运行

`heroku create`

从而安装你的新应用。

我们还使用Heroku对Procfile的协定，以告诉它如何运行这个应用，但是你可以在自己的服务器上，在一个或多个gunicorn进程前面使用一些诸如nginx的应用，来设置它。

要验证Heroku能够在你的机器上本地运行，使用下面命令启动你的本地服务器：

`heroku local`

然后，在你的浏览器中访问[http://localhost:5000/](http://localhost:5000/)，接着，你应该会看到“Hello world”。

![localhost](https://blog.hartleybrody.com/wp-content/uploads/2016/06/localhost-1024x628.png)

用`Ctrl+C`命令杀掉本地服务器。要部署这个端点到Heroku

`git push heroku master`

然后在你的浏览器中打开它

`heroku open`

现在，你就有了一个“可用的”网络挂接网址，你可以用来设置你的机器人。确保你从浏览器捕获到完整的`https://*.herokuapp.com` URL，因为我们稍后将会需要它。

### 第二步：创建一个Facebook Page

如果你还没有，那么需要[创建一个Facebook Page](https://www.facebook.com/pages/create/)。Facebook Page是你的机器人的“身份证”，包括当有人在Facebook Messenger和它聊天时出现的名字和头像。

如果你只是为你的机器人创建了一个虚设的，那么它叫啥名字或者你如何对其进行分类都没有啥关系。你可以跳过大多数的设置步骤。

为了与机器人交流，人么将需要通过你的Page，这个我们稍后将会看到。

### 第三步：创建一个Facebook App

转至[Facebook开发者快速入门页面](https://developers.facebook.com/quickstarts/?platform=web)，然后点击右上角的“Skip and Create App ID”。接着为你的机器人创建一个新的Facebook App，并为你的机器人填写名字、目录和联系电子邮箱信息。

![create-fb-app](https://blog.hartleybrody.com/wp-content/uploads/2016/06/create-fb-app-1024x604.png)

下一页，你将在右上角看到你新的App ID。向下滚动，然后点击Messenger旁边的“Get Started”。

![setup-fb-messenger-app](https://blog.hartleybrody.com/wp-content/uploads/2016/06/setup-fb-messenger-app-1024x613.png)

### 第四步：设置你的Messaging App

现在，你位于你的Facebook App的Messenger设置页面。这里，你需要填些东西，以便让你的聊天机器人连接到我们之前设置的Heroku端点。

**生成一个Page Access Token**

使用之前创建的Page（或现有的Page），点击通过验证流程，接着你会收到一个应用的Page Access Token。

![page-access-token-generation](https://blog.hartleybrody.com/wp-content/uploads/2016/06/page-access-token-generation-1024x346.png)

点击Page Access Token，将其复制到剪贴板。你需要将其设为Heroku应用的环境变量。在命令行中，在你克隆应用的同个文件夹下，运行：

`heroku config:add PAGE_ACCESS_TOKEN={{your_page_token_here}}`

每当你尝试发送一个信息或回复别人时，这个token将被用来验证你的请求。

**设置网络挂接**

当你设置你的网络挂接时，你将需要一点信息：

![setup-webhook-annotated](https://blog.hartleybrody.com/wp-content/uploads/2016/06/setup-webhook-annotated-1024x491.png)

*   **Callback URL** – 我们之前设置的Heroku (或其他的) URL。
*   **Verification Token** – 将会发送给你的机器人的一个秘密值，以验证请求是来自于Facebook的。不管你在这里设置了什么值，确保你使用了`heroku config:add VERIFY_TOKEN={{your_verification_token_here}}`将其添加到了Heroku环境 using 
*   **Subscription Fields** – 这个告诉Facebook，你关心并且想要通知到你的网络挂接的消息事件是啥。如果你不确定，只需要以“messages,”开头，因为稍后你可以修改它

当你配置好网络挂接后，需要订阅你想要接收邮件通知的特定页面。

![subscribe-to-page](https://blog.hartleybrody.com/wp-content/uploads/2016/06/subscribe-to-page-1024x352.png)

一旦你已经获取了Page Access Token，并设置好了网络挂接，确保你在Heroku应用中设置了`PAGE_ACCESS_TOKEN`和`VERIFY_TOKEN`配置的值，这样你就可以很好地完成工作了！

### 第五步：开始与你的机器人聊天

转到你创建的Facebook Page，然后点击“Message”按钮，这个按钮挨着“Like”按钮，在页面顶部附近。这应该会在你的页面上打开一个消息面板。

![fb-page-chat-bot](https://blog.hartleybrody.com/wp-content/uploads/2016/06/fb-page-chat-bot-1024x668.png)

开始发送你的Page消息，然后这个机器人应该会回复！

要看看发生了啥，检查应用的日志：

`heroku logs -t`

你应该会看到每当一个新消息发送到你的Page机器人时，Facebook发送给你的端点的POST数据。

这里是当我发送“does this work?”给我的机器人时，我获得的一个样例JSON POST体

```
       {
            "object":"page",
            "entry":[
                {
                    "messaging":[
                        {
                            "message":{
                                "text":"does this work?",
                                "seq":20,
                                "mid":"mid.1466015596912:7348aba4de4cfddf91"
                            },
                            "timestamp":1466015596919,
                            "sender":{
                                "id":"885721401551027"
                            },
                            "recipient":{
                                "id":"260317677677806"
                            }
                        }
                    ],
                    "time":1466015596947,
                    "id":"260317677677806"
                }
            ]
        }
```

默认情况下，机器人应该回复“got it, thanks!”

### 第六步：定制机器人的行为

这里就是我们最后开始深入代码之处。

其实消息机器人只有两个关键部分：接收和发送消息

**接收信息*

[app.py的第24行起, 在我们的`webhook()`视图函数中](https://github.com/hartleybrody/fb-messenger-bot/blob/master/app.py#L24)，我们开始处理传入的消息。

首先，每当触发了一个新的消息事件，通常是当有人发送一条消息到我们的Page上，我们就加载从Facebook发送给网络挂接的JSON POST数据

然后我们遍历每一项 - 在我的测试经验中，一次只有发送一项给网络挂接。

然后，我们循环每个消息事件。这里，可能有几种消息事件。

在第4步，我们告诉Facebook我们想要通知我们的网络挂接的消息类型。如果你遵循了我的建议，那么我们的终端将只能收到“message”事件，但我们也可以接收到传送确认，选择项(Ele注：原文是optins，私以为笔误)和回传（参见后述）。我适当地留下了一些用于检测其他类型的消息事件的代码，但我实际上并不处理它们。

对大多数应用程序最有用的的消息事件将是“message”事件，这意味着有人给你的Page发送新的消息。我写了一些基本的代码来​​处理这种事件，解析出发送者的ID，并简单地回应他们。

**发送信息**

为了发送一个简单的短信，你只需要两件事情：

*   收件人的Facebook ID
*   你想要发送的消息文本

我创建了一个简单的`send_message()`函数，它自动匹配Facebook API，然后发送那些消息。

记得吗，请求是使用我们在第四步获取的`PAGE_ACCESS_TOKEN`环境变量来进行验证的。

你还可以发送许多更为复杂的消息类型，包括内容包含图片和按钮的消息。[关于那些消息的更多信息，点击这里](https://developers.facebook.com/docs/messenger-platform/implementation#send_message)。

需要注意的是，在消息中发送一个“postback”按钮的能力。这些基本上是这样的按钮，当用户按下时，它会发送一个回发消息事件到你的网络挂接。

这基本上允许用户在你的应用程序中，同时在Facebook Messenger里“按下按钮”。你可以将其用于下订单，确认请求或很多其他的事情。

每当用户点击一个回发按钮，就会通知你的网络挂接，并可以执行任何种类的必要的后续操作。

### 第七步：提交应用进行审查

当你测试你的机器人时，只有你和其他Page管理员可以直接与机器人发消息。在你的机器人对世界开放，准备好与任何人聊天之前，必须要经过一个审查程序。

Facebook的似乎会在他们的审查过程中非常透彻地检查你的应用，而且有充分理由。消息机器人的代码运行在自己服务器上，并可能在Facebook不知道的情况下随时更改。

他们似乎在努力确保你是一个好人，而不是提交一个简单的虚拟应用程序以获得批准，然后在一段时间后将其修改成某些垃圾邮件机器人。

显然，如果你这样做，他们仍然可以撤销你的API访问令牌，但他们宁愿让Messenger平台上不存在任何滥用。

回到我们在第4步使用的Messenger App Settings页面。向下滚动到“App Review for Messenger”，并点击“Request Permissions.”。

![chat-bot-approval](https://blog.hartleybrody.com/wp-content/uploads/2016/06/chat-bot-approval-1024x556.png)

请求你需要的全新，然后你就会被带到“Review Status”页面。此页面需要大量的信息，以确保开发人员不会滥用平台。

它需要你

* 选中几个复选框，确认你已经阅读他们的政策和指导方针
* 保证你不会参与到来路不明的出站消息
* 描述你将如何通过你的机器人与用户交互
* 提供一个审查小组可以用来与你的机器人进行互动的测试用户
* 上传你通过Messenger与你的机器人交互的截屏
* 有隐私权保护政策
* 验证你对机器人的解释和设置的对用户的期望

在这个页面上，你也可以要求被授予获取用户额外信息的权限，例如他们的电子邮件或个人资料信息。

然后它将走到Facebook审查小组核准，然后授予你对Messenger platform的完全访问。[这里是关于审批流程的更多信息。](https://developers.facebook.com/docs/messenger-platform/app-review)

* * *

即使你不打算一路经历审查程序，希望你已经学到了一两件有关如何为Facebook Messenger构建一个简单的聊天机器人。

[在这里，检出我的代码](https://github.com/hartleybrody/fb-messenger-bot/)。
