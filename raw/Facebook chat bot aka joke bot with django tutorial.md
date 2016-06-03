原文：[Facebook chat bot aka joke bot with django tutorial](https://codeexperiments.quora.com/Facebook-chat-bot-aka-joke-bot-with-django-tutorial)

---


![](https://qph.is.quoracdn.net/main-qimg-d3f48b427c16a968c79301b80a2b7aa9?convert_to_webp=true)

The age of bots has begun. Chat bots are a sensation now a days and are being
hailed as the next generation replacement to websites and apps. Many platforms
are getting opened up with chat bots as interaction tools such as in Facebook,
Telegram etc. Chat bots are already quite famous in China’s messaging giant We
Chat where people book cabs and make financial transactions via chat.

I have decided to try to develop a chat bot which does only one thing. Send a
random joke like the below one without an image irrespective of what the user
types

![](https://qph.is.quoracdn.net/main-qimg-0a5a4526932b6ee315cc64b6106d72a4?convert_to_webp=true)

Getting started we are going to develop it in django. Why django? , I am good
in python so I felt to go forward in Django, you can choose whatever you like.
The official docs are for node.js .

1. Create a facebook page from here [Create a Page |
Facebook](https://www.facebook.com/pages/create/) . Choose some page type and
go forward with the settings.

2.Create a facebook app from here [Log into Facebook |
Facebook](https://developers.facebook.com/quickstarts/) and fill in the
required details to create an app.

3.Once both the page and app are created go to the app settings and under
Product Settings click Add Product and then Select **Messenger **.

4.Now you need to setup webhooks. We need to deploy the django project online
and make it available for facebook to make requests and facebook allows only
https urls. [Your development environment, in the cloud](http://c9.io) is the
best option I found online and it’s free for a basic app. Create a workspace
in c9 and choose **Django **as platform. Now you will be directed to your
online workspace.

5. From the workspace use the below command to create a django app.

```python

    django-admin.py startapp jokebot
    
```

This should create an app jokebot.

Go to [settings.py](http://settings.py) file in the workspace, find
INSTALLED_APPS and add jokebot.

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

Modify [urls.py](http://urls.py) to include an endpoint for webhook. The
webhook will hit the endpoint url/james and we have to respind with a token
for facebook to validate.

```python

    from django.conf.urls import url, include
    from django.contrib import admin
    from jokebot.views import jokebot
    
    urlpatterns = [
        url(r'^admin/', admin.site.urls),
        url(r'^james/?$', jokebot.as_view()) 
    ]
    
```

Now go to [views.py](http://views.py) in the app jokebot and modify it to the
following.

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

We create a class jokebot which responds with

```python

    self.request.GET['hub.challenge']
    
```

on a webhook request.

Under the "PRODUCT SETTINGS" section, click on the "Messenger" product you
just added, find the Webhooks section and click Setup Webhooks. Enter a URL
for a webhook, enter a Verify Token and select message_deliveries, messages,
messaging_optins, and messaging_postbacks under Subscription Fields.

![](https://qph.is.quoracdn.net/main-qimg-70f9ecaffdc3ae1100c3402099af9364?convert_to_webp=true)

Choose any verify_token and place it in facebook and in the code. To get the
url click on **Run** in c9 and place the url/james in callback url option. Now
click on Verify and Save and you should get the webhook verified.

Now we handle the message and send a joke using this code

```python

    def post(self, request, *args, **kwargs):
            incoming_message = json.loads(self.request.body.decode('utf-8'))
            for entry in incoming_message['entry']:
                for message in entry['messaging']: 
                    if 'message' in message: 
                        get_joke(message['sender']['id'], message['message']['text'])    
            return HttpResponse()
    
```

This basically reads the post request of a message and responds with a joke.

```python

    def get_joke(fbid, recevied_message):
        joke_text = requests.get("http://api.icndb.com/jokes/random/").json()['value']['joke']
        post_message_url = 'https://graph.facebook.com/v2.6/me/messages?access_token=%s'%'Place your access token here'
        response_msg = json.dumps({"recipient":{"id":fbid}, "message":{"text":joke_text}})
        status = requests.post(post_message_url, headers={"Content-Type": "application/json"},data=response_msg)
        pprint(status.json())
    
```

This function gets a random joke using an online api [The Internet ChuckNorris Database](http://www.icndb.com/) and sends it back to the user.

To get page access token in the Token Generation section, select your Page. A
Page Access Token will be generated for you. Copy this Page Access Token.
Note: The generated token will NOT be saved in this UI. Each time you select
that Page a new token will be generated. However, any previous tokens created
will continue to function.

![](https://qph.is.quoracdn.net/main-qimg-0d58fc1759de26a3fadfafeda03e7e8e?convert_to_webp=true)

So now run the server again and send a message and you should get a Chuck
Norris joke. Remember that the app now works only for you. To make it publicly
available you have to request fb to approve your app and publish it.

You can find the entire source code here [Anil1331/Facebook-chat-bot](https://github.com/Anil1331/Facebook-chat-bot) .

Sources:-

  1. [How To Build Bots for Messenger - Facebook for Developers](https://developers.facebook.com/blog/post/2016/04/12/bots-for-messenger/)


