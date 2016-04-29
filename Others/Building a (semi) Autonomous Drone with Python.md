原文：[Building a (semi) Autonomous Drone with Python](http://blog.yhat.com/posts/autonomous-droning-with-python.html)

---

They might not be delivering our mail ([or our burritos](http://tacocopter.com/)) yet, but drones
are now simple, small, and affordable enough that they can be considered a toy. You can
even customize and program some of them via handy dandy Application Programming Interfaces ([API](https://www.quora.com/What-is-an-API-4))!
The Parrot AR Drone has an API that lets you control not only the drone's movement but also stream video and images from its camera.
In this post, I'll show you how you can use Python and node.js to build a drone that
moves all by itself.

![](http://blog.yhat.com/static/img/greg-flying-drone.png)

Hold onto your butts.

### 项目

So given that I'm not a drone, or a machine vision professional, I'm going to have
to keep things simple. For this project, I'm going to **teach my drone how to follow
a red object**.

I know, I know, it's a far cry from a T-800 Model 101 (or [even something like this](https://www.youtube.com/watch?v=YQIMGV5vtd4)), but
given my time and budget constraints it's a good place to start! In the meantime, feel free to send your best autonomous terminators or drone swarms my way.

![](http://blog.yhat.com/static/img/terminator.jpg)

这里没有神经网络处理器，只有Node.js和Python。

### 无人机

When I opened my drone on Christmas morning I wasn't entirely sure what I was going
to do with it, but one thing was for certain: This thing was cool. The [AR Drone 2.0](http://ardrone2.parrot.com/) (I know
super lame name) is a quadcopter. If you're imagining those fit in the palm of your hand, single-rotor,
RC gizmos, you're in the wrong ballpark. The first thing I noticed (and was most surprised by)
was how big the AR Drone is. With its "indoor shell" on, it's about 2 feet wide, 2 feet long,
and 6 inches high. It's also kind of loud--in a good way (like a terrify your dog kind of way, unlike [this down to drone pup](https://vimeo.com/95078536)).
Combine that with 2 cameras--one front and one bottom, and you've got yourself the ultimate grown up geek toy.

![](http://blog.yhat.com/static/img/ar-drone.png)

### 对你的无人机进行编程

What sets the AR Drone apart is that it's old (in drone years)--it was first released in 2012. This
might seem like a bad thing BUT since we're trying to program this gizmo, it's actually
a good thing.

Given that it's had 4 years to "mature", there are some really great APIs, helper
libraries, and project/code samples for controlling/programming the drone (see list of resources below). So in essence, someone
else has already done the hard part of figuring out how to communicate with the drone in bytecode, so all I have
to do is import the _node_module_ and I'm off to the figurative [drone races](http://thedroneracingleague.com/).

Programming the drone is actually quite easy. I'm using the [`ar-drone`](https://github.com/felixge/node-ar-drone) node.js module. I've
found that it works really well despite not being under _super_ active development. To start, let me
show you how to do a pre-programmed flightplan. The following program is going to:

*   connect to the drone over wifi
*   tell the drone to takeoff
*   after 1 second, spin clockwise at full throttle
*   1 second after that, stop and then move forwards at 50% thrust
*   another 1 second after that, stop and land

Pretty simple little program. Now even though it's pretty straightforward, I will
still **highly recommend having an _[emergency landing](https://gist.github.com/glamp/0b6f0ef87525e3cefcfb4f5bd146712c)_ script readily available**. Cause
you never know you need one till you really need one ;)
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


You can also pull off some fancier moves--you know, to impress your friends. My personal
favorite is a backflip.


<iframe width="420" height="315" src="https://www.youtube.com/embed/aF8V8p1n3P0" frameborder="0" allowfullscreen=""></iframe>


### Le Machine Vision

Ok now for the second piece of the puzzle: teaching our drone how to see. To do this, we're going
to be using [OpenCV](http://opencv.org/) and the Python module [cv2](http://opencv-python-tutroals.readthedocs.org/). OpenCV can
be a little prickly to work with, but it can do some really impressive stuff and even has
some machine learning libraries baked right into it.

We're going to be using OpenCV to do some basic object tracking. We're going to have the
**camera track anything red** that appears
in its field of vision. Sort of like a bull at a bullfight.

![](http://blog.yhat.com/static/img/bullfight.jpg)

Just like this, except substitute the bull for a drone, and the red cape (_muleta_) for a Greg ☹! Also, my pants aren't quite that tight.

Good news for us is that `cv2` makes this really easy to do.
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

As you can see above, I'm using a color mask to filter the pixels in an image. It's
a simple but intuitive approach. And more importantly **it works**. Take a look:

<div class="row">
  <div class="col-sm-6">
    ![](http://blog.yhat.com/static/img/towel-pic.png)
    Raw camera feed
  </div>
  <div class="col-sm-6">
    ![](http://blog.yhat.com/static/img/towel-pic-filtered.png)
    Processed with red filter
  </div>
</div>

It's learning! Ok well maybe not quite like a T-800 Model 101, but it's at least a start.

![](http://blog.yhat.com/static/img/terminator-dot.jpg)

Is the red dot a coincidence? Think again...
<div class="row">
  <div class="col-sm-6">
    ![](http://blog.yhat.com/static/img/drone/raw/raw-view.gif)
    Raw camera feed
  </div>
  <div class="col-sm-6">
    ![](http://blog.yhat.com/static/img/drone/processed/drone-view.gif)
    Processed with red filter
  </div>
</div>

### 把一切拼接在一起

Ok here comes the tricky part. We've got our little node.js script that can control
the drone's navigation, and we've got the python bit that can detect where red things
are in an image, but the question looms: **How do we glue them together?**

![](http://blog.yhat.com/static/img/duct-tape.jpg)

Well my friends, to do this I'm going to use Yhat's own model deployment software, [ScienceOps](https://www.yhat.com/products/scienceops).
I'm going to deploy my Python code onto ScienceOps, where it'll be accessible via an API, and then from node.js
I can call my model on ScienceOps. What this means is that I've boiled my OpenCV red-filtering
model into a really simple HTTP endpoint. I'm using ScienceOps to make my childhood drone bull fighting dreams come true, but
you could use it to embed any R or Python model into any application capable of making API requests, be it drone or otherwise.

![](http://blog.yhat.com/static/img/braveheart-freedom.jpg)

No more recoding to get models into production. FREEDOM!

I don't need to mess around with any cross-platform
baloney, and if I need to up the horsepower of my model (say for instance if I'm controlling more than
one drone), I can let ScienceOps scale out my model automatically. If you want more info about
deploying models (or drones) into production using ScienceOps, head over to our [site](www.yhat.com) or
schedule a demo to see it live.
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

What does all this mean? Well for one, it means my node.js code just got a lot simpler. I can even
use the Yhat node.js library to execute my model:
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

Sweet! Now I can pretty much just drop this into my navigation script. All I need to do is tell
my script how I want to react to the response. In this case it's going to be a couple steps:

*   Call the `DroneModel` model hosted on ScienceOps
*   If there weren't any errors, look at the result. The result will give me the `x` and `y` coordinates
of the red in the image.
*   Make course adjustments to the drone that attempt to move the red to the center of the drone's
field of vision.

So simple! What could possibly go wrong?


  <iframe width="560" height="315" src="https://www.youtube.com/embed/ZVDfMPHqHKc?t=7" frameborder="0" allowfullscreen=""></iframe>


### Mending my metaphorical stitching

As the adage goes, If at first you don't succeed try, try again. It took me a few
iterations to get the autonomous piece to actually work. Turns out, combining individual
components has the propensity to compound your error!

But not to worry! My drone took its fair share of bumps and bruises but it's a tough
little guy--**Pro Tip: **You can patch up your drone with duct tape. Just be sure to apply
equal amounts to each side of the drone so it's balanced!

![](http://blog.yhat.com/static/img/drone-duct-tape.jpg)

Duct Tape: More than a metaphor.

A couple of things I learned the hard way:

*   **_Build a helper app: _** After a few trial runs I built a helper app (see video below) to
determine what/why things were happening. Let me tell you, **this should've been step #1**. It
was invaluable being able to see what code my program was executing and what the processed
images looked like.
*   **_Don't over-correct: _** For simple things like this, if you tell the drone to do
too much at the same time, it freaks out and either (a) just sits there or (b) starts
errantly flying all over the room (see video above).
*   **_Always have an emergency landing script handy: _** I mentioned this earlier but it can't
be overemphasized. The reason is that if your program crashes and you haven't landed your
drone, you're in deep ... trouble. Your drone is going to keep flying (possibly errantly) until
you tell it to land. Having [`emergency-landing.js`](https://gist.github.com/glamp/0b6f0ef87525e3cefcfb4f5bd146712c) handy
will save you some maintenance (and possibly from a lawsuit).
*   **_If it's windy, don't fly your drone: _** Learned this one the _really_ hard way...

In the end with some persistence and a little luck, I was able to get a couple of
good autonomous runs in!


  <iframe width="560" height="315" src="https://www.youtube.com/embed/VdgQajDuA5E" frameborder="0" allowfullscreen=""></iframe>


### In the wild

I wound up presenting this at [PAPIs Valencia](http://www.papis.io/connect-valencia-2016/program) which
was a lot of fun (BTW PAPIs is awesome! I highly recommend it for anyone interested
in predictive analytics). Unfortunately [my PAPIs demo didn't go quite as smoothly](http://www.papis.io/connect-valencia-2016/talks/2016/3/using-ml-to-build-an-autonomous-drone-greg-lamp). The lighting
in the lecture hall was different than in our office and as a result, the red didn't
quite get filtered the same way. Despite the less than stellar performance, it was
still a lot of fun!

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
