原文：[Hacking the Worcester Wave thermostat in Python – Part 3](http://blog.rtwilson.com/hacking-the-worcester-wave-thermostat-in-python-part-3/)

---

所以，[前面](http://blog.rtwilson.com/hacking-the-worcester-wave-thermostat-in-python-part-2/)我们找出了通信是如何被加密的，并且设法读取到了加热系统的当前状态（热水器是否开启，当前温度，等等）。这很棒 —— 但是如何我们能实际地用Python控制恒温器，这将会更棒，例如，设置温度，由定时器模式修改到手动模式，等等。这就是我们今天要关注的。

所以，使用相同的‘中间人’方法，在我修改多项设置的同时，我监控来来自应用的通信。当修改温度的时候，我得到了一个像这样的消息：

`<message id="lj8Vq-31" to="rrcgateway_458921440@wa2-mz36-qrmzh6.bosch.de" type="chat"><subject>/heatingCircuits/hc1/manualTempOverride/temperature</subject><body>PUT /heatingCircuits/hc1/manualTempOverride/temperature HTTP:/1.0\nContent-Type: application/json\nContent-Length: 25\nUser-Agent: NefitEasy\n\n\n\nXmuIR7wCfDZpPrPkrb/CqQ==\n</body></message>`

再次，整理一下它，并且移除XMPP头部，我们看到消息体如下：
```py
PUT /heatingCircuits/hc1/manualTempOverride/temperature

HTTP:/1.0

 Content-Type: application/json

 Content-Length: 25

 User-Agent: NefitEasy

XmuIR7wCfDZpPrPkrb/CqQ==
```

如果我们解码底部的文本，那么可以发现它解码为：
`<{"value":16}\x00\x00\x00\x00`

这看起来像JSON，并在最后有一个位的空填充（大概是这样的，长度按一定数可整除的数据被提供给加密例程） - 它是有意义的，因为我设置了恒温器为16度。

所以，如果我们发送这条短信（但使用不同的号码），那么假设温度会改变？嗯...有可能！

你看，改变温度取决于你在什么模式。有两种模式，而状态消息的UMD部分告诉你，你是其中哪一个：手动(manual)或时钟(clock)。如果你在手动模式下，则可以简单发送一个PUT消息（像上面那个）给`/heatingCircuits/hc1/temperatureRoomManual`，并带上JSON数据`{"value":21}`（或任何你想要的温度）来更改温度。

但是，如果在时钟模式下，那么你必须设置“覆盖温度（override temperature）”（一个到`/heatingCircuits/hc1/manualTempOverride/temperature`的PUT消息），并且带上与上面相同的JSON，然后你必须打开”温度覆盖功能(temperature override function)'（一个到`/heatingCircuits/hc1/manualTempOverride/status`的带有JSON数据`{"value": "on"}`的PUT消息）。

哦，如果你想改变模式，那么你可以只发送一个到`/heatingCircuits/hc1/usermode`的PUT消息，并携带JSON数据`{"value": "clock"}`或`{"value":"manual"}`。

你可能想知道是否从这些消息获得了响应：你可以这样做，但它们不是很有趣的。除非出现了一个错误，你得到的会是：
```py
No Content

Content-Type: application/json

connection: close
```

你还可以发送许多其他的消息来做各种复杂的事情（如改变定时程序），但我还没有尝试去调查这些呢。我知道它们会使用与这些消息相同的格式，它们只是由稍微复杂一点的JSON有效载荷，并可能需要发送多个消息。我很高兴，因为我可以读取我的恒温器状态，并控制基本设置（模式和温度）！

所以，迄今为止，在这个系列中我并没有真正那么多提到Python（对不起！）—— 尽管，事实上，我的大部分'试错'工作是通过使用Python中棒棒哒的[sleekxmpp库](http://sleekxmpp.com/)来完成的。在这里，我必须承认，我没有如我应该做的那样编写代码：我真的本应该设计它来实现一个适当的有限状态机，并且在适当的时间里发送和接收相应的消息，同时在Python类中更新内部信息......但我没有！对不起 —— 应对所有这一切，异步工作，这都太像繁重的工作了。

所以，我写了一个`BaseWaveMessageBot`类，它实现连接，发送消息，编码和解码消息负载和一点简单的错误处理。这个类拥有所有复杂的东西，所以后来我写了几个非常简单的类（`StatusBot`和`SetBot`），它们发送适当的消息并处理响应。然后，我在一个漂亮的名为`WaveThermo`的类中将它们组合在一起。目前，WaveThermo还没有很多方法，但因为我需要它，所以我会逐渐添加更多的功能。

该代码可[在Github](https://github.com/robintw/pywavethermo)上找到，并且它是相当容易使用：

当然，我只是用我的恒温器测试了一下它 —— 所以，如果它对你无效，那么请让我知道！

所以，目前就是这样了 - 下一次，我将谈论我完成的温度和恒温状态自动监控所做的一点工作，以及我发现的一些有趣的模式。
