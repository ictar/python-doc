原文：[Building a (semi) Autonomous Drone with Python](http://blog.yhat.com/posts/autonomous-droning-with-python.html)

---

它们或许还不能邮递我们的信件 ([或者我们的卷饼](http://tacocopter.com/))，但是现在，无人机是简单、体积小、并且能够买得起的，因此可以把它们当成一个玩具。甚至你可以通过方便的应用程序编程接口 ([API](https://www.quora.com/What-is-an-API-4))对它们其中一些进行定制和编程！Parrot AR Drone拥有一个API，能让你不仅控制无人机的移动，还能从它的摄像头传输视频和图像。在这篇文章中，我会告诉你如何使用Python和node.js来构建一个完全靠自己移动的无人机。

![](http://blog.yhat.com/static/img/greg-flying-drone.png)

守住你的屁屁。

### 项目

因此，假定我不是一个无人家，或者一个机器视觉的专家，我将不得不让事情变得简单。对于这个项目，我将**教会我的无人机如何跟着一个红色的物体**。

我懂，我懂，这与T-800 Model 101 (或者[其他类似的东东](https://www.youtube.com/watch?v=YQIMGV5vtd4))相去甚远，但在我的时间和预算限制的情况下，这是一个不错的开始！在此期间，不要客气，尽情的发送你最好的自主终端或者无人机群吧。

![](http://blog.yhat.com/static/img/terminator.jpg)

这里没有神经网络处理器，只有Node.js和Python。

### 无人机

当我在圣诞节的早晨打开了我的无人机，我完全不能确定我要拿它做啥，但有一件事是肯定的：这个东西很酷。[AR Drone 2.0](http://ardrone2.parrot.com/) (我知道这个名字超级蹩脚) 是一个四轴飞行器。如果你在想像那些刚好和你的手一样大，单旋翼，RC小发明，那么你就错了。我首先注意到的（最感到吃惊的）是这个AR Drone有真大。在它的“室内壳”打开的情况下，它大概约2英尺宽，2英尺长，以及6英寸高。它还有点吵 —— 好的方式（就像威吓你的狗那样的方式，不像[这种用无人机遛狗](https://vimeo.com/95078536)）。再加上两个摄像头 —— 一个在前面，一个在底部，于是，你终于获得了你自己的终极极客玩具。

![](http://blog.yhat.com/static/img/ar-drone.png)

### 对你的无人机进行编程

让AR Drone与众不同之处在于，（在无人机的年代里）它是老牌的 —— 它在2012年第一次发布。这似乎是一件坏事，但是，由于我们正努力对这个小发明进行编程，因此实际上它是一个好事。

鉴于它有4年来“成熟”，它有一些真的很棒的控制/编程该无人机的API，辅助库和项目/代码样例（见下面的资源列表）。所以在本质上，别人已经搞清楚了如何用字节码与无人机进行沟通这一最困难的部分，因此，我所需要做的事就是导入_node_module_，然后我要出发去具体的[无人机比赛](http://thedroneracingleague.com/)了。

对无人机进行编程实际上是非常简单的。我使用[`ar-drone`](https://github.com/felixge/node-ar-drone) node.js模块。我发现，尽管它没有获得_超级_活跃的发展，但是它真的工作良好。要开始，让我告诉你如何进行一个预编程的飞行计划。下面的程序将：

*   通过WIFI连接到无人机
*   告诉无人机起飞
*   1秒后，全速顺时针旋转
*   1秒后，停止，然后以50%的推力向前移动
*   1秒后，停止并着陆

非常简单的小程序。现在，即使非常直接，但是我仍会**强烈建议准备好一个_[紧急降落](https://gist.github.com/glamp/0b6f0ef87525e3cefcfb4f5bd146712c)_脚本**。因为只有在你真的需要的时候，你才会知道你需要 ;)

```python
var arDrone = require('ar-drone');
var drone = arDrone.createClient();
drone.takeoff();

drone
  .after(1000, function() {
    drone.clockwise(1.0);
  })
  .after(1000, function() {
    drone.stop();
    drone.front(0.5);
  })
  .after(1000, function() {
    drone.stop();
    drone.land();
  })
```    
<iframe width="420" height="315" src="https://www.youtube.com/embed/Edh98lNtFfo" frameborder="0" allowfullscreen=""></iframe>


你也可以完成一些特技 —— 你知道的，来打动你的朋友。我个人最喜欢的是一个后空翻。


<iframe width="420" height="315" src="https://www.youtube.com/embed/aF8V8p1n3P0" frameborder="0" allowfullscreen=""></iframe>


### Le Machine Vision

好了，现在是第二块拼图了：教会你的无人机如何看。要做到这一点，我们将使用[OpenCV](http://opencv.org/)和Python模块[cv2](http://opencv-python-tutroals.readthedocs.org/)。OpenCV有点难用，但它可以做一些让人印象深刻的事情，它甚至还有一些机器学习库。

我们将用OpenCV来进行一些基本的物体跟踪。我们将使用**摄像头跟踪任何在它的视野中出现的红色的东西**。有点像在斗牛中的牛。

![](http://blog.yhat.com/static/img/bullfight.jpg)

就像这样，除了用无人机代替公牛，用Greg代替红色斗篷(_斗牛红布_)☹！另外，我的裤子也没那么紧。

好消息是，`cv2`使得这一切很容易做到。

```python
import numpy as np
import cv2
from skimage.color import rgb2gray
from PIL import Image
from StringIO import StringIO
from scipy import ndimage
import base64
import time

def get_coords(img64):
    "Reads in a base64 encoded image, filters for red, and then calculates the center of the red"
    # convert the base64 encoded image a numpy array
    binaryimg = base64.decodestring(img64)
    pilImage = Image.open(StringIO(binaryimg))
    image = np.array(pilImage)

    # create lower and upper bounds for red
    red_lower = np.array([17, 15, 100], dtype="uint8")
    red_upper = np.array([50, 56, 200], dtype="uint8")

    # perform the filtering. mask is another word for filter
    mask = cv2.inRange(image, red_lower, red_upper)
    output = cv2.bitwise_and(image, image, mask=mask)
    # convert the image to grayscale, then calculate the center of the red (only remaining color)
    output_gray = rgb2gray(output)
    y, x = ndimage.center_of_mass(output_gray)

    data = {
        "x": x,
        "y": y,
        "xmax": output_gray.shape[1],
        "ymax": output_gray.shape[0],
        "time": time.time()
    }
    return data
```

正如你在上面看到的。我使用了一个色彩掩码来过滤一个图像中的像素。这是一个简单而直观的方法。更重要的是，**它能用**。看一看：

<div class="row">
  <div class="col-sm-6">
    ![](http://blog.yhat.com/static/img/towel-pic.png)
    使用原始相机
  </div>
  <div class="col-sm-6">
    ![](http://blog.yhat.com/static/img/towel-pic-filtered.png)
    使用红色过滤器后
  </div>
</div>

它在学习！好吧，或许并不辣么像一个T-800 Model 101，但这至少是一个开始。

![](http://blog.yhat.com/static/img/terminator-dot.jpg)

红点是巧合吗？再想想……

<div class="row">
  <div class="col-sm-6">
    ![](http://blog.yhat.com/static/img/drone/raw/raw-view.gif)
    使用原始相机
  </div>
  <div class="col-sm-6">
    ![](http://blog.yhat.com/static/img/drone/processed/drone-view.gif)
    使用红色过滤器后
  </div>
</div>

### 把一切拼接在一起

好了，棘手的部分来了。我们有了一个小小的node.js脚本，它可以控制无人机导航，还有一个小的python脚本可以检测一张图中红色的物体在哪里，但是问题赫然出现：**我们要怎样把它们黏在一起呢？**

![](http://blog.yhat.com/static/img/duct-tape.jpg)

好吧，亲，要做到这点，我将使用Yhat自己的模型部署软件，[ScienceOps](https://www.yhat.com/products/scienceops)。我将把我的Python代码部署到ScienceOps，在上面，我可以通过一个API来访问到它，然后从node.js，我可以在ScienceOps上面调用我的模型。这意味着，我将把我当OpenCV红色过滤模型放到一个非常简单的HTTP端。我使用ScienceOps来使得我的孩童期的无人机斗牛梦想成真，但是你可以用它来将任何R或者Python模型嵌入到任何可以进行API进行的应用中，无论是无人机还是其他的。

![](http://blog.yhat.com/static/img/braveheart-freedom.jpg)

不用更多的记录来让模型投入生产。自由万岁！

我不需要浪费时间到任何跨平台的事情上，而如果我需要上传我的模型的马力（例如说，如果我控制一个以上的无人机），那么我可以让ScienceOps自动地向外扩展我的模型。如果你想要跟多关于使用ScienceOps部署模型（或无人机）到生产的信息，看看我们的[网站](www.yhat.com)吧，或者安排一个演示，看看直播。

```python
from yhat import Yhat, YhatModel

class DroneModel(YhatModel):
    REQUIREMENTS = [
        "opencv"
    ]
    def execute(self, data):
        return get_coords(data['image64'])

yh = Yhat(USERNAME, APIKEY, "https://sandbox.yhathq.com/")
yh.deploy("DroneModel", DroneModel, globals(), True)
```

这都意味着什么呢？好吧，举例来说，它意味着我的node.js代码变得简单许多。我甚至可以使用Yhat node.js库来执行我的模型：

```python
var fs = require('fs');
var img = fs.readFileSync('./example-image.png').toString('base64');

var yhat = require('yhat');
var cli = yhat.init('greg', 'my-apikey-goes-here', 'https://sandbox.yhathq.com/');

cli.predict('DroneModel', base64edImage, function(err, data) {
  console.log(JSON.stringify(data, null, 2));
  // {
  //   "result": {
  //     "time": 1460067540.30213,
  //     "total_red": 5.810973333333334,
  //     "x": 425.0256166460453,
  //     "xmax": 640,
  //     "y": 220.03434178824077,
  //     "ymax": 360
  //   },
  //   "version": 1,
  //   "yhat_id": "529b84c9c4957008446a56faadc152a6",
  //   "yhat_model": "DroneModel"
  // }
  var x = data.x / data.xmax - 0.5
    , y = data.y / data.ymax - 0.5;

  if (x > 0) {
    drone.right(Math.abs(x));
  } else {
    drone.left(Math.abs(x));
  }
  if (y > 0) {
    drone.up(Math.abs(y));
  } else {
    drone.down(Math.abs(y));
  }
});
```

真好！现在，我可以非常简单地只是将其放到我的导航脚本中。我所需要做的是告诉我的脚本我要怎么对响应做出反应。在这种情况下，它将是一对步骤：

*   调用保存在ScienceOps上的`DroneModel`模型
*   如果没有任何错误，那么看看结果。结果将给我图像中红色的`x`和`y`坐标。
*   对无人机进行调整，尝试将红色移动到无人机视野的中心。

超简单！怎么可能会出问题！


  <iframe width="560" height="315" src="https://www.youtube.com/embed/ZVDfMPHqHKc?t=7" frameborder="0" allowfullscreen=""></iframe>


### 我的修修补补

正如谚语所说，如果开始没成功，那就努力再干。我花了几个迭代来让自动化这块可以工作。原来，结合各个组件有加重错误的倾向！

但不要担心！我的无人机跌跌撞撞，但它是一个坚强的小孩 —— **专业提示：**你可以使用胶带来修补你的无人机。只要确保无人机的每一面都是相同的重量，这样它才是平衡的！

![](http://blog.yhat.com/static/img/drone-duct-tape.jpg)

胶带：不仅仅是比喻。

通过一番艰难我才了解到的一些事：

*   **_构建一个辅助应用：_** 经过几次试飞，我构建了一个辅助应用 (见下面的视频)，以确定正在发生啥事，以及为嘛发生这样的事。让我告诉你，**这本应是步骤#1**。能够看到我的程序执行什么代码，以及处理的图像看起来是什么样子的，这是无价的。
*   **_不要过度正确：_** 对于像这样简单的事情，如果你告诉无人机同时做太多的事，那么它就会变得怪怪的，要么(a) 只是坐在那里，要么(b)开始在房间里乱飞 (见上面的视频)。
*   **_手上总是有一个紧急降落脚本：_** 前面我就提到了这点，但它一点都不为过。原因是，如果你的程序奔溃，而你还没让你的无人机着陆，那么，你就会陷入深深的……麻烦。你的无人机将会一直飞行（可能错误滴），知道你告诉它着陆。拥有[`emergency-landing.js`](https://gist.github.com/glamp/0b6f0ef87525e3cefcfb4f5bd146712c)在手，将会节省你的一些维护（以及可能会的一起诉讼）。
*   **_如果有风，不要放飞你的无人机：_** _真的真的_很艰难才学到这点……

最后，带着一点点持久性和一点点人品，我可以让几个自动化运行良好！


  <iframe width="560" height="315" src="https://www.youtube.com/embed/VdgQajDuA5E" frameborder="0" allowfullscreen=""></iframe>


### 总结

我兴奋地在[PAPIs Valencia](http://www.papis.io/connect-valencia-2016/program)上展示了它，超好玩的 (顺便说一下，PAPIs棒棒哒！我强烈推荐给那些对预测分析有兴趣的人)。不幸的是，[我的PAPIs并没有顺利地进行](http://www.papis.io/connect-valencia-2016/talks/2016/3/using-ml-to-build-an-autonomous-drone-greg-lamp)。报告厅的照明与我们的办公室不同，结果，红色并没有以同样的方式完全得到过滤。结果逊色得多，但是它仍然是很好玩的！

### 资源

想了解更多关于无人机编程吗？这里是用于开始的一些重要资源：

*   [NodeCopter](http://www.nodecopter.com/) - 无人机的JS社区。不再活跃了，但是有一些很棒的入门指南。
*   [node-ar-drone](https://github.com/felixge/node-ar-drone) - 编程无人机的Node库。
*   [Parrot AR Drone 2.0](http://www.amazon.com/Parrot-Drone-Quadricopter-Edition-Orange/dp/B007HZLLOK) - 在这里买。
*   [OpenCV](http://opencv.org/) - 使用OpenCV的信息。
*   [scikit-image](http://scikit-image.org/) - 更高层次，更关注于ML的计算版本库。
*   [dronestream](https://github.com/bkw/node-dronestream) - Parrot AR 2.0的实时视频订阅。

还有，如果你想要我的代码，这是到仓库的[链接](https://github.com/yhat/semi-autonomous-drone)。


  <iframe width="420" height="315" src="https://www.youtube.com/embed/2fWr6CBARMw" frameborder="0" allowfullscreen=""></iframe>

醉酒驾车
