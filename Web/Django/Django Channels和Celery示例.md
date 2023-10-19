原文：[Django Channels and Celery Example](http://vincenttide.com/blog/1/django-channels-and-celery-example/)

---

在本教程中，我将探讨如何建立一个Django Channels项目来与Celery协同工作，以及在任务开始和结束的时候，即时通知。Django Channels使用WebSockets来启用服务器和浏览器客户端之间的双向通信。假设读者已经熟悉如何建立一个普通的Django项目，我们将只覆盖Channels和Celery相关的部分。

你可以在[这里找到Github repo](https://github.com/VincentTide/django-channels-celery-example)以及位于<http://tasker.vincenttide.com>的一个相同部署。注意，该部署包含了在此教程不包括的一些额外的内容，例如取消功能。示例部署的前端还运行React库，而我们在这个演示将只使用JavaScript。

首先，让我们安装一些需要的依赖。我们将需要一个Channels层后端，Channels用来传递和存储数据。我们还将需要一个Celery broker传输后端。事实证明，我们可以为这些任务同时使用Redis，因此Redis就是我们所用的。

```python

    # Add Chris Lea’s redis ppa - he maintains the ppa for many open source projects
    $ sudo add-apt-repository ppa:chris-lea/redis-server
    $ sudo apt-get update
    $ sudo apt-get install redis-server
    
    # Now check that redis-server is up and running
    $ redis-cli ping
    # PONG
    
```

在一个virtualenv内设置一个新的Django项目，然后安装以下库：

```python

    $ pip install django
    $ pip install channels  # the channels library
    $ pip install asgi_redis  # the redis channel layer we are using
    $ pip install celery  # Celery task queue
    
```

让我们先看看settings.py文件。

```python

    # Add our new app to installed apps
    INSTALLED_APPS = [
    #…
      ‘jobs’,
    ]
    
    # Channels settings
    CHANNEL_LAYERS = {
       "default": {
           "BACKEND": "asgi_redis.RedisChannelLayer",  # use redis backend
           "CONFIG": {
               "hosts": [os.environ.get('REDIS_URL', 'redis://localhost:6379')],  # set redis address
           },
           "ROUTING": "django_channels_celery_tutorial.routing.channel_routing",  # load routing from our routing.py file
       },
    }
    
    # Celery settings
    BROKER_URL = 'redis://localhost:6379/0'  # our redis address
    # use json format for everything
    CELERY_ACCEPT_CONTENT = ['json']
    CELERY_TASK_SERIALIZER = 'json'
    CELERY_RESULT_SERIALIZER = 'json'
    
```

首先，添加我们的新app到INSTALLED_APPS列表中。Channels设置只是告诉Channels我们所使用的后端，在这里，是Redis。ROUTING选项告诉Channels到哪里找我们的WebSockets路径，这里是routing.py文件。Celery设置告诉Celery到哪里找我们的broker，以及对所有东西，我们想要使用json格式。

现在，看看routing.py文件：

```python

    from channels import route
    from jobs import consumers
    
    channel_routing = [
       # Wire up websocket channels to our consumers:
       route("websocket.connect", consumers.ws_connect),
       route("websocket.receive", consumers.ws_receive),
    ]
    
```

这里，我们只是为连接和接收消息设置了处理函数。我们还可以添加一个函数来处理断开连接消息，但对我们来说，并不需要。我们告诉Channels在我们的jobs/consumers.py文件中查找函数。

这里是consumers.py文件的主要部分：

```python

    @channel_session
    def ws_connect(message):
       message.reply_channel.send({
           "text": json.dumps({
               "action": "reply_channel",
               "reply_channel": message.reply_channel.name,
           })
       })
    
    
    @channel_session
    def ws_receive(message):
       try:
           data = json.loads(message['text'])
       except ValueError:
           log.debug("ws message isn't json text=%s", message['text'])
           return
    
       if data:
           reply_channel = message.reply_channel.name
    
           if data['action'] == "start_sec3":
               start_sec3(data, reply_channel)
    
```

在我们的ws_connect函数中，我们将只是把客户端的回复通道（reply channel）地址回传。回复通道是一个分配给每一个连接到我们的websockets服务器的浏览器客户端的唯一地址。可以从message.reply_channel.name中检索到该值，并且可以保存或传递该值到另一个函数，例如Celery 的task，这样的话，就可以将消息发回去。事实上，这就是我们将要做的事。message.reply_channel.send是Channels为我们提供的，用来回复到同一个客户端的一个方便的快捷方式。如果你仅有reply_channel名，那么你将必须使用以下方法来发送消息：

```python

    Channel(reply_channel_name).send({
       "text": json.dumps({
           "action": "started",
           "job_id": job.id,
           "job_name": job.name,
           "job_status": job.status,
       })
    })
    
```

在我们的ws_receive函数中，我们根据action参数来看看客户端想要我们做什么。如果你想要做不同的事，那么可以有多个action指令。在我们的例子中，我们只有一个指令，它运行一个名为start_sec3的函数。start_sec3只是休眠3秒，然后回复它已经完成的消息给客户端。注意，我们传递了reply_channel地址，因此它知道发送响应到哪。

最后一个重要的块是客户端侧的javascript处理函数。

```python

    $(function() {
       // When we're using HTTPS, use WSS too.
       var ws_scheme = window.location.protocol == "https:" ? "wss" : "ws";
       var ws_path = ws_scheme + '://' + window.location.host + '/dashboard/';
       console.log("Connecting to " + ws_path)
       var socket = new ReconnectingWebSocket(ws_path);
    
       socket.onmessage = function(message) {
           console.log("Got message: " + message.data);
           var data = JSON.parse(message.data);
    
           // if action is started, add new item to table
           if (data.action == "started") {
               var task_status = $("#task_status");
               var ele = $('<tr></tr>');
               ele.attr("id", data.job_id);
               var item_id = $("<td></td>").text(data.job_id);
               ele.append(item_id);
               var item_name = $("<td></td>").text(data.job_name);
               ele.append(item_name);
               var item_status = $("<td></td>");
               item_status.attr("id", "item-status-"+data.job_id);
               var span = $('<span class="label label-primary"></span>').text(data.job_status);
               item_status.append(span);
               ele.append(item_status);
               task_status.append(ele);
           }
           // if action is completed, just update the status
           else if (data.action == "completed"){
               var item = $('#item-status-' + data.job_id + ' span');
               item.attr("class", "label label-success");
               item.text(data.job_status);
           }
       };
    
       $("#taskform").on("submit", function(event) {
           var message = {
               action: "start_sec3",
               job_name: $('#task_name').val()
           };
           socket.send(JSON.stringify(message));
           $("#task_name").val('').focus();
           return false;
       });
    });
    
```

这里，我们首先创建websockets对象，然后用socket.onmessage函数来处理为每个websockets消息我们应该做的事。如果action参数的值是“started”，那么我们将添加一个新的条目到表格中。如果action是completed，我们只会修改对应的列状态为已完成。

而表单则是发送一个websockets消息到服务器，告诉它运行action “start_sec3”。


要看完整的项目文件，请访问[Github repo](https://github.com/VincentTide/django-channels-celery-example)。要运行Github repo代码，先确保你安装了Redis，然后运行以下命令：

```python

    pip install -r requirements.txt
    python manage.py makemigrations
    python manage.py migrate
    python manage.py runserver  # Start daphne and workers
    celery worker -A example -l info  # Start celery workers
    
```

这会启动部署服务器，地址为`http://localhost:8000`。再一次说明，你可以在http://tasker.vincenttide.com上找到一个类似的部署。


