原文：[Facebook chat bot aka joke bot with django tutorial](https://codeexperiments.quora.com/Facebook-chat-bot-aka-joke-bot-with-django-tutorial)

---


![](https://qph.is.quoracdn.net/main-qimg-d3f48b427c16a968c79301b80a2b7aa9?convert_to_webp=true)

机器人时代已经开始了。如今，聊天机器人引起轰动，并被誉为下一代的网站和应用的替代品。许多平台都开发了聊天机器人作为交互式工具，例如Facebook, Telegram等等。聊天机器人在中国的通讯巨头WeChat中颇负盛名，其中，人们通过聊天预定出租车和进行金融交易。

我决定尝试开发一个聊天机器人，它只做一件事。不管用户输入什么，它都发送一个像下面这样的不含图片的随机笑话。

![](https://qph.is.quoracdn.net/main-qimg-0a5a4526932b6ee315cc64b6106d72a4?convert_to_webp=true)

作为开始，我们将要在django中开发它。为什么是django呢？我擅长于python，因此使用Django，你可以使用任何你喜欢的。官方文档是用于node.js的。

1. 从这里[创建一个页面 | Facebook](https://www.facebook.com/pages/create/)创建一个facebook页面。选择一些页面类型并进行设置。

2. 从这里[登录到Facebook | Facebook](https://developers.facebook.com/quickstarts/)创建一个facebook应用，并填写所需的详细信息来创建一个应用。

3. 一旦页面和应用都创建好了，那么转到应用设置，并在Product Settings下点击Add Product，然后选择**Messenger **。

4. 现在，你需要设置网络钩子。我们需要将该django项目部署在线，并且使其可为facebook创建请求，而facebook只允许https url。[Your development environment, in the cloud](http://c9.io)是我发现在线的最佳选择，而它对于一个基本的应用是免费的。在c9中创建工作空间，选择**Django**作为平台。现在，你将被定向到你的在线工作空间。

5. 在工作区中，使用下面的命令来创建一个django应用。

```python

    django-admin.py startapp jokebot
    
```

这应该创建一个应用jokebot。

转到工作空间中的[settings.py](http://settings.py)文件，找到INSTALLED_APPS，并添加jokebot。

```python

    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'jokebot',
    ]
    
```

修改[urls.py](http://urls.py)以包含网络钩子的端点。该网络钩子将拦截端点url/james，而我们必须返回一个token以供facebook验证。

```python

    from django.conf.urls import url, include
    from django.contrib import admin
    from jokebot.views import jokebot
    
    urlpatterns = [
        url(r'^admin/', admin.site.urls),
        url(r'^james/?$', jokebot.as_view()) 
    ]
    
```

现在，打开应用jokebot中的[views.py](http://views.py)，并如下修改它。

```python

    import json, requests, random, re
    from pprint import pprint
    
    from django.views import generic
    from django.http.response import HttpResponse
    
    from django.views.decorators.csrf import csrf_exempt
    from django.utils.decorators import method_decorator
    
    
    def get_joke(fbid, recevied_message):
        joke_text = requests.get("http://api.icndb.com/jokes/random/").json()['value']['joke']
        post_message_url = 'https://graph.facebook.com/v2.6/me/messages?access_token=%s'%'Place your access token here'
        response_msg = json.dumps({"recipient":{"id":fbid}, "message":{"text":joke_text}})
        status = requests.post(post_message_url, headers={"Content-Type": "application/json"},data=response_msg)
        pprint(status.json())
    
    class jokebot(generic.View):
        def get(self, request, *args, **kwargs):
            if self.request.GET['hub.verify_token'] == verify_token:
                return HttpResponse(self.request.GET['hub.challenge'])
            else:
                return HttpResponse('Error, invalid token')
            
        @method_decorator(csrf_exempt)
        def dispatch(self, request, *args, **kwargs):
            return generic.View.dispatch(self, request, *args, **kwargs)
    
    
        def post(self, request, *args, **kwargs):
            incoming_message = json.loads(self.request.body.decode('utf-8'))
            for entry in incoming_message['entry']:
                for message in entry['messaging']: 
                    if 'message' in message: 
                        get_joke(message['sender']['id'], message['message']['text'])    
            return HttpResponse()
    
```

我们创建了一个类jokebot，它响应一个网络钩子请求中的

```python

    self.request.GET['hub.challenge']
    
```

在"PRODUCT SETTINGS"部分，点击你刚刚添加的"Messenger"产品，找到Webhooks部分，点击Setup Webhooks。为网络钩子输入一个URL，输入一个验证令牌，并在Subscription字段下选择message_deliveries, messages, messaging_optins, 和messaging_postbacks。

![](https://qph.is.quoracdn.net/main-qimg-70f9ecaffdc3ae1100c3402099af9364?convert_to_webp=true)

选择任意verify_token，并将其放到facebook和代码中。要获得url，点击c9中的**Run**，并将url/james放到回调的url选项中。现在，点击Verify and Save，然后应该就可以让该网络钩子被验证。

现在，我们使用这个代码处理消息并发送一个笑话

```python

    def post(self, request, *args, **kwargs):
            incoming_message = json.loads(self.request.body.decode('utf-8'))
            for entry in incoming_message['entry']:
                for message in entry['messaging']: 
                    if 'message' in message: 
                        get_joke(message['sender']['id'], message['message']['text'])    
            return HttpResponse()
    
```

这基本上读取一个消息的Post请求，并用一个笑话进行响应。

```python

    def get_joke(fbid, recevied_message):
        joke_text = requests.get("http://api.icndb.com/jokes/random/").json()['value']['joke']
        post_message_url = 'https://graph.facebook.com/v2.6/me/messages?access_token=%s'%'Place your access token here'
        response_msg = json.dumps({"recipient":{"id":fbid}, "message":{"text":joke_text}})
        status = requests.post(post_message_url, headers={"Content-Type": "application/json"},data=response_msg)
        pprint(status.json())
    
```

这个函数使用一个在线API [The Internet ChuckNorris Database](http://www.icndb.com/)获取一个随机笑话，然后将其发送回给用户。

要获得Token Generation部分的页面访问令牌，选择你的Page。一个Page Access Token将会为你生成。复制此Page Access Token。注意：生成的令牌将不在此UI进行保存。每次选择那个Page，都将会生成新的令牌。然而，任何之前创建令牌将继续发挥作用。

![](https://qph.is.quoracdn.net/main-qimg-0d58fc1759de26a3fadfafeda03e7e8e?convert_to_webp=true)

所以现在再次运行服务器并发送一条消息，你应该会得到一个Chuck
Norris笑话。请记住，应用程序现在仅适用于你。为了使其公开，你必须请求fb批准您的应用程序并进行发布。

你可以在这里发现完整的代码[Anil1331/Facebook-chat-bot](https://github.com/Anil1331/Facebook-chat-bot)。

来源:-

  1. [如何为Messenger建立通讯机器人 - 对于Facebook的开发者](https://developers.facebook.com/blog/post/2016/04/12/bots-for-messenger/)


