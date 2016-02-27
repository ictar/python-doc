原文：[Hacking the Worcester Wave thermostat in Python – Part 3](http://blog.rtwilson.com/hacking-the-worcester-wave-thermostat-in-python-part-3/)

---

So, [last time](http://blog.rtwilson.com/hacking-the-worcester-wave-thermostat-in-python-part-2/) we worked out how communications were encrypted and managed to read the current status of the heating system (whether the boiler is on or not, the current temperature, and so on). That’s great – but it’d be even better if we could actually control the thermostat from Python: set the temperature, change from timer mode to manual mode etc. That’s what we’re going to focus on today.

So, using the same ‘man-in-the-middle’ approach I monitored the communications from the app as I changed various settings. When changing the temperature I got a message like this:

`<message id="lj8Vq-31" to="rrcgateway_458921440@wa2-mz36-qrmzh6.bosch.de" type="chat"><subject>/heatingCircuits/hc1/manualTempOverride/temperature</subject><body>PUT /heatingCircuits/hc1/manualTempOverride/temperature HTTP:/1.0\nContent-Type: application/json\nContent-Length: 25\nUser-Agent: NefitEasy\n\n\n\nXmuIR7wCfDZpPrPkrb/CqQ==\n</body></message>`

Again, tidying it up a bit, and removing the XMPP headers we see the body is:
```py
PUT /heatingCircuits/hc1/manualTempOverride/temperature

HTTP:/1.0

 Content-Type: application/json

 Content-Length: 25

 User-Agent: NefitEasy

XmuIR7wCfDZpPrPkrb/CqQ==
```
If we decode the text at the bottom we find that it decodes as:

`<span class="s1">{"value":16}\x00\x00\x00\x00</span>`

This looks like JSON, with a bit of null-padding at the end (presumably so that the encryption routine is given data with the length divisible by a certain number) – and it makes sense, as I set the thermostat to 16 degrees.

So, if we send this message (but with a different number) then presumably the temperature setting will change?&nbsp;Well…sort of!

You see, changing the temperature depends on what mode you are in. There are two modes, and the UMD part of the status message tells you which one you are in: _manual_ or _clock._ If you’re in manual mode then you can change the temperature by simply sending a PUT message (like the one above) to&nbsp;`/heatingCircuits/hc1/temperatureRoomManual`, with JSON of&nbsp;`{"value":21}` (or whatever temperature you want).

However, if you’re in clock mode then you have to set the ‘override temperature’ (a PUT message to&nbsp;`/heatingCircuits/hc1/manualTempOverride/temperature`) with the same JSON as above,&nbsp;_and_ you then have to turn the ‘temperature override function’ on (a PUT message to&nbsp;`/heatingCircuits/hc1/manualTempOverride/status` with the JSON `{"value": "on"}`).

Oh, and if you want to change the mode then you can just send a PUT message to&nbsp;`/heatingCircuits/hc1/usermode` with the JSON `{"value": "clock"}` or `{"value":"manual"}`.

You may be wondering whether you get a response from these messages or not: you do, but they’re not very interesting. Unless there has been an error, all you get is:
```py
No Content

Content-Type: application/json

connection: close
```

There are loads of other messages you can send to do all sorts of complicated things (such as changing the timer programme), but I haven’t bothered to investigate&nbsp;any of those yet. I know they will use the same format as these messages, they’ll just have slightly more complicated JSON payloads, and may require the sending of multiple messages. I’m just happy that I can read the status of my thermostat, and control basic settings (mode and temperature)!

So, I haven’t actually mentioned Python that much in this series so far (sorry!) – although, in fact, most of my ‘trial and error’ work was done through Python using the great [sleekxmpp library](http://sleekxmpp.com/). I have to confess here that I haven’t written the code as well as I should have done: I should really have designed it to implement a proper Finite State Machine, and send and receive the appropriate messages at the appropriate times, all while updating internal information in the Python classes…but I didn’t! Sorry – dealing with all of that, working asynchronously, was too much like hard work.

So, I wrote a&nbsp;`BaseWaveMessageBot` class that implemented connecting, sending messages, encoding and decoding message payloads and a bit of simple error handling. That class has all of the complicated stuff in it, so I then wrote a couple of very simple classes (`StatusBot` and `SetBot`) that send the appropriate messages and process the responses. I then combined these in a nice class called `WaveThermo`. Currently there aren’t many methods in WaveThermo, but I will gradually add more functionality as I need it.

The code is available [on Github](https://github.com/robintw/pywavethermo)&nbsp;and is fairly easy to use:


Of course, I’ve only tested it with my thermostat – so if it doesn’t work for you then please let me know!

So, that’s it for this time – next time I’ll talk about a bit of the work I’ve done with automated monitoring of the temperature and the thermostat state, and some of the interesting patterns I’ve found.
