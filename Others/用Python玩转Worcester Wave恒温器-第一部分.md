原文：[Hacking the Worcester Wave thermostat in Python – Part 1](http://blog.rtwilson.com/hacking-the-worcester-wave-thermostat-in-python-part-1/)

---

当我们去年买了一台新热水器时，我们决定安装“智能恒温”。目前，有很多可用的，包括Google Nest，Hive（来自英国电气），以及[Worcester Bosch ‘Wave’](https://www.worcester-bosch.co.uk/products/boiler-controls/wave)。由于我们已经有了一个Worcester Bosch热水器，所以我们获得了[Wave](https://www.worcester-bosch.co.uk/products/boiler-controls/wave) – 而它并不比一个标准的程序员/恒温器昂贵得多。

恒温器看起来是这样的：

![Bosch-Wave-small](http://blog.rtwilson.com/wp-content/uploads/2015/12/Bosch-Wave-small-300x270.jpg)

它挂在我们楼梯底部的墙壁上，并通过电缆连接到热水器。正如你所看到的，它有一个简单的触摸感应界面：可以增加或减少恒温器设置，查看当前温度和当前设置，将手动模式修改为编程模式，以及查看热水器是否打开。对于任何更高级的东西（例如设置可编程定时器），你可以使用移动应用，它看起来像这样：

![screen568x568](http://blog.rtwilson.com/wp-content/uploads/2015/12/screen568x568-169x300.jpeg)

看起来很熟悉，不是吗？在应用“主屏幕”上拥有一样简单的界面，但你也可以配置更高级的设置，如定时程序：

![screen568x568 (1)](http://blog.rtwilson.com/wp-content/uploads/2015/12/screen568x568-1-169x300.jpeg)

总体来说，我很满意Wave恒温器：相较于一个微小且繁琐控制器，在一个手机应用上配置定时器设置要容易得多，并有许多你可以使用的高级功能，（例如，“优化”，其中系统逐渐学习需要让你的房子热起来要多长时间，并在你想的时候，在正确的时间加热到你想要的温度） - 同时具有能够在离开家的时候控制加热的能力。

无论如何，当然，一旦安装好了，我查看的第一件事就是我的家庭服务器可以用来监控和控制恒温器的API，最好是通过一个Python脚本。不幸的是，在 Worcester Bosch的网站上，并没有关于API的信息，而当我打电话给客服时，他们告诉我，没有可用的API。所以，我想我会尝试逆向它已使用的协议，看看我是否能用一个Python接口来玩转它。

## 这个系统整体是如何工作的？

当开始调查它时，我对于系统的 先验理解是，该应用必须发送某种消息到远程服务器（可能由Bosch运行或控制），然后转发信息到恒温器本身，反之亦然。这是因为无论你在哪里，你都可以通过应用程序访问恒温器，而不必是在相同的无线网络上。无论使用何种协议或何种端口，它都必须能够可靠地通过家庭防火墙，使得远程服务器可以实际与恒温器进行通信。

## 他们使用了什么技术？

在调查应用时，我注意到有一个产品信息的屏幕显示，它显示了关于软件和硬件版本的信息，并且包含了一个标有 _Worcester Wave uses Open Source software_ 的按钮。它表明了该系统使用的一开源包列表，包括它们=各种许可协议。这个列表组成如下：

*   [AndroidPlot](http://androidplot.com/) – Android的一个绘图库
*   [Guava](https://github.com/google/guava) – 谷歌Java核心类库
*   [XMP Toolkit](http://sourceforge.net/adobe/adobexmp/home/Home/) – 扩展元数据平台
*   [Smack](http://www.igniterealtime.org/projects/smack/) – 一个Java XMPP（也叫Jabber）聊天协议的实现
*   [JSR305 Expert Group](https://code.google.com/p/jsr-305/) – Java中的注解软件缺陷检测
*   朗讯科技 – _不知道，也找不到这一个！_
*   [Chromium](https://en.wikipedia.org/wiki/Chromium_(web_browser)) – _我不知道为什么使用了Chromium网络浏览器_
*   [Takayua OOURA](http://www.kurims.kyoto-u.ac.jp/~ooura/) – Ooura的数学软件
*   [Eigen software](https://github.com/hughperkins/jeigen) – 矩阵操作工具
*   [libresample](https://ccrma.stanford.edu/~jos/resample/Free_Resampling_Software.html) – 数据采样（主要是音频，但也推测其它数据类型）

所以，该名单显示，可扩展消息与存在协议([XMPP](https://en.wikipedia.org/wiki/XMPP))用于通信，可能与嵌入了一些扩展元数据平台的元数据。库的其余部分与我们的工作没有多大关系，但看起来挺有趣的（我怀疑数学部分用于进行一些高级功能，如优化，的数学运算）。

## 发送了什么，发送到那里？

所以，我们觉得使用XMPP进行通信 - 现在我们需要进行确认，并且找出发送了什么以及它被发送到哪里。所以，我打开了[Wireshark](https://www.wireshark.org/)以启动流量嗅探。在玩了一会过滤器后，我得到这个（点击以放大）:

![Screen Shot 2016-01-02 at 22.45.15](http://blog.rtwilson.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-02-at-22.45.15-1024x238.png)

这表明，我的猜测是正确的：XMPP协议被用来在运行Wave Android应用的手机（本地网络上的`192.168.0.42`）和Bosch(`wa2-mz36-qrmzh6.bosch.de`)运行的服务器之间发送信息。所以，我们现在知道要处理的是什么协议了：这是个好消息。

Anyway, in this case the Bosch server responds with `PROCEED` (“Yes, I’m happy to do this encrypted”). From that point onwards, we see the TLS security negotiation (“What sort of encryption do you support?”, “I support X, Y and Z”, “Ok, lets use Z”, “Here are my keys” etc) followed by a lot of ‘Application Data’ messages:然而，坏消息是我们在那里看到的`STARTTLS`消息。你可以从电子邮件配置对话框终意识到这一点，因为它是连接到POP3 / IMAP / SMTP服务器的安全选项之一。它代表着'开始传输层安全性“，并且基本上是跟你说：“我想从即日起对这个会话进行加密，可以吗？”的一种方式（供参考，另一种方法是从会话一开始就对所有进行加密）。无论如何，在这种情况下，Bosch服务器响应`PROCEED`（“是的，我很高兴进行这样的加密”）。从这一点开始，我们看到了伴随着大量的“应用程序数据”消息的TLS安全协商（“你支持什么样的加密？”，“我支持X，Y和Z”，“好吧，让我们用Z”，“这里是我的钥匙”等等）：

![Screen Shot 2016-01-02 at 22.54.06](http://blog.rtwilson.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-02-at-22.54.06-1024x250.png)

我们不能真正看到任何消息的内容，因为它们被加密了，所以我们得到的是原始的十六进制转储：`80414a90ca64968de3a0acc5eb1b50108bbc5b26973626daaa`….(以此类推). 不是很有用哦！

我认为这是完成故事的第一部分的一个好地方。我们已经得到了从应用程序到Bosch服务器的通信，它使用XMPP协议，但使用`STARTTLS`进行TLS加密，所以我们没能得到任何消息实际上包含的内容。接下来第2部分听听我如何设法读取消息...

（译者：我已经迫不及待他的第二部分了~~）
