原文：[Hacking the Worcester Wave thermostat in Python – Part 2](http://blog.rtwilson.com/hacking-the-worcester-wave-thermostat-in-python-part-2/)

---

In the [previous part](http://blog.rtwilson.com/hacking-the-worcester-wave-thermostat-in-python-part-1/) we had established that the Worcester Wave thermostat app communicates with a remote server (run by Worcester Bosch) using the XMPP protocol, with TLS encryption. However, because of the encryption we haven’t yet managed to see the actual content of any of these messages!

To decrypt the messages we need to do a _man in the middle attack_. In this sort of attack we put insert ourselves between the two ends of the conversation and intercept messages, decrypt them and then forward them on to the original destination. Although it is called an ‘attack’, here I am basically attacking myself – because I’m basically monitoring communications going from a phone I own through a network I own. Beware that doing this to networks that you do not own or have permission to use in this way is almost certainly illegal.

There is a [good guide](https://blog.heckel.xyz/2013/08/04/use-sslsplit-to-transparently-sniff-tls-ssl-connections/) to setting all of this up using a tool called [sslsplit](https://www.roe.ch/SSLsplit), although I had to do things slightly differently as I couldn’t get sslsplit to work with the STARTTLS method used by the Worcester Wave (as you may remember from the previous part, STARTTLS is a way of starting the communication in an unencrypted manner, and then ‘turning on’ the encryption part-way through the communication).

The summary of the approach that I used is:

1.  I configured a Linux server on my network to use Network Address Translation (NAT) – so it works almost like a router. This means that I can then set up another device on the network to use that server as a ‘gateway’, which means it will send _all_ traffic to that server, which will then forward it on to the correct place.
2.  I created a self-signed [root certificate](https://en.wikipedia.org/wiki/Root_certificate) on the server. A root certificate is a ‘fully trusted’ certificate that can be used to trust any other certificates or keys derived from it (that explanation is probably technically wrong, but it’s conceptually right).
3.  I installed this root certificate on a spare Android phone, connected the phone to my home wifi and configured the Linux server as the gateway. I then tested, and could access the internet fine from the phone, with all of the communications going through the server.

Now, if I use the Worcester Wave app on the phone, all of the communications will go through the server – and the phone will believe the server when it says that it is the Bosch server at the other end, because of the root certificate we installed.

Now we’ve got all of the certificates and networking stuff configured, we just need to actually decrypt the messages. As I said above, I tried using [SSLSplit](https://www.roe.ch/SSLsplit), but it couldn’t seem to cope with STARTTLS. I found the same with Wireshark itself, so looked for another option.

Luckily, I found a great tool called [starttls-mitm](https://github.com/ipopov/starttls-mitm) which _does_ work with STARTTLS. Even more impressively it’s barely 80 lines of Python, and so it’s very easy to understand the code. This does mean that it is less configurable than a more complex tool like SSLSplit – but that’s not a problem for me, as the tool does exactly what I want . It is even configured for XMPP by default too! (Of course, as it is written in Python I could always modify the code myself if I needed to).

So, running starttls-mitm with the appropriate command-line parameters (basically your keys, certificates etc) will print out all communications: both the unencrypted ones before the STARTTLS call, and the decrypted version of the encrypted ones after the STARTTLS call. If we then start doing something with the app while this is running, what do we get?

Well, first we get the opening logging information starttls-mitm telling us what it is doing:
```sh
LISTENER ready on port 8443
CLIENT CONNECT from: ('192.168.0.42', 57913)
RELAYING
```

We then start getting the beginnings of the communication:
```xml
C->S 129 '<stream:stream to="wa2-mz36-qrmzh6.bosch.de" xmlns="jabber:client" xmlns:stream="http://etherx.jabber.org/streams" version="1.0">'
S->C 442 '<?xml version=\'1.0\' encoding=\'UTF-8\'?><stream:stream xmlns:stream="http://etherx.jabber.org/streams" xmlns="jabber:client" from="wa2-mz36-qrmzh6.bosch.de" id="260d2859" xml:lang="en" version="1.0"><stream:features><starttls xmlns="urn:ietf:params:xml:ns:xmpp-tls"></starttls><mechanisms xmlns="urn:ietf:params:xml:ns:xmpp-sasl"><mechanism>DIGEST-MD5</mechanism></mechanisms><auth xmlns="http://jabber.org/features/iq-auth"/></stream:features>'
```

Fairly obviously, `C-&gt;S` are messages from the client to the server, and `S-&gt;C` are messages back from the server (the numbers directly afterwards are just the length of the message). These are just the initial handshaking communications for the start of XMPP communication, and aren’t particularly exciting as we saw this in Wireshark too.

However, now we get to the interesting bit:
```xml
C->S 51 '<starttls xmlns="urn:ietf:params:xml:ns:xmpp-tls"/>'
S->C 50 '<proceed xmlns="urn:ietf:params:xml:ns:xmpp-tls"/>'
Wrapping sockets.
```

The STARTTLS message is sent, and the server says PROCEED, and – crucially – starttls-mitm notices this and announces that it is ‘wrapping sockets’ (basically enabling the decryption of the communication from this point onwards).

I’ll skip the boring TLS handshaking messages now, and skip to the initialisation of the XMPP protocol itself. I’m no huge XMPP expert, but basically the `iq` messages are ‘info/query’ messages which are part of the handshaking process with each side saying who they are, what they support etc. This part of the communication finishes with each side announcing its ‘presence’ (remember, XMPP is originally a chat protocol, so this is the equivalent of saying you are ‘Online’ or ‘Active’ on Skype, Facebook Messenger or whatever).
```xml
C->S 110 '<iq id="lj8Vq-1" type="set"><bind xmlns="urn:ietf:params:xml:ns:xmpp-bind"><resource>70</resource></bind></iq>'
S->C 188 '<iq type="result" id="lj8Vq-1" to="wa2-mz36-qrmzh6.bosch.de/260d2859"><bind xmlns="urn:ietf:params:xml:ns:xmpp-bind"><jid>rrccontact_458921440@wa2-mz36-qrmzh6.bosch.de/70</jid></bind></iq>'
C->S 87 '<iq id="lj8Vq-2" type="set"><session xmlns="urn:ietf:params:xml:ns:xmpp-session"/></iq>'
S->C 86 '<iq type="result" id="lj8Vq-2" to="rrccontact_458921440@wa2-mz36-qrmzh6.bosch.de/70"/>'
C->S 74 '<iq id="lj8Vq-3" type="get"><query xmlns="jabber:iq:roster" ></query></iq>'
S->C 123 '<iq type="result" id="lj8Vq-3" to="rrccontact_458921440@wa2-mz36-qrmzh6.bosch.de/70"><query xmlns="jabber:iq:roster"/></iq>'
C->S 34 '<presence id="lj8Vq-4"></presence>'
C->S 34 '<presence id="lj8Vq-5"></presence>'
```

Now all of the preliminary messages are dealt with, we get to the good bit. The message below is sent from the client (the phone app) to the server:
```xml
C->S 162 '<message id="lj8Vq-6" to="rrcgateway_458921440@wa2-mz36-qrmzh6.bosch.de" type="chat"><body>GET /ecus/rrc/uiStatus HTTP /1.0\nUser-Agent: NefitEasy</body></message>'
```

It basically seems to be a HTTP GET request embedded within an XMPP message. That seems a bit strange to me – why not just use HTTP directly? – but at least it is easy to understand. The URL that is being requested also makes sense – I was on the ‘home screen’ of the app at that point, so it was grabbing the status for displaying in the user-interface (things like current temperature, set-point temperature, whether the boiler is on or not, etc).

Now we can see the response from the server:
```xml
S->C 904 '<message to="rrccontact_458921440@wa2-mz36-qrmzh6.bosch.de/70" type="chat" xml:lang="en" from="rrcgateway_458921440@wa2-mz36-qrmzh6.bosch.de/RRC-RestApi"><body>HTTP/1.0 200 OK\nContent-Length: 640\nContent-Type: application/json\nconnection: close\n\n5EBW5RuFo7QojD4F1Uv0kOde1MbeVA46P3RDX6ZEYKaKkbLxanqVR2I8ceuQNbxkgkfzeLgg6D5ypF9jo7yGVRbR/ydf4L4MMTHxvdxBubG5HhiVqJgSc2+7iPvhcWvRZrRKBEMiz8vAsd5JleS4CoTmbN0vV7kHgO2uVeuxtN5ZDsk3/cZpxiTvvaXWlCQGOavCLe55yQqmm3zpGoNFolGPTNC1MVuk00wpf6nbS7sFaRXSmpGQeGAfGNxSxfVPhWZtWRP3/ETi1Z+ozspBO8JZRAzeP8j0fJrBe9u+kDQJNXiMkgzyWb6Il6roSBWWgwYuepGYf/dSR9YygF6lrV+iQdZdyF08ZIgcNY5g5XWtm4LdH8SO+TZpP9aocLUVR1pmFM6m19MKP+spMg8gwPm6L9YuWSvd62KA8ASIQMtWbzFB6XjanGBQpVeMLI1Uzx4wWRaRaAG5qLTda9PpGk8K6LWOxHwtsuW/CDST/hE5jXvWqfVmrceUVqHz5Qcb0sjKRU5TOYA+JNigSf0Z4CIh7xD1t7bjJf9m6Wcyys/NkwZYryoQm99J2yH2khWXyd2DRETbsynr1AWrSRlStZ5H9ghPoYTqvKvgWsyMVTxbMOht86CzoufceI2W+Rr9</body></message>'
```

Oh. This looks a bit more complicated, and not very easily interpretable. Lets format the main body of the message formatted a bit more nicely:
```
HTTP/1.0 200 OK
Content-Length: 640
Content-Type: application/json
connection: close
 
5EBW5RuFo7QojD4F1Uv0kOde1MbeVA46P3RDX6ZEYKaKkbLxanqVR2I8ceuQNbxkgkfzeLgg6D5ypF9jo7yGVRbR/ydf4L4MMTHxvdxBubG5HhiVqJgSc2+7iPvhcWvRZrRKBEMiz8vAsd5JleS4CoTmbN0vV7kHgO2uVeuxtN5ZDsk3/cZpxiTvvaXWlCQGOavCLe55yQqmm3zpGoNFolGPTNC1MVuk00wpf6nbS7sFaRXSmpGQeGAfGNxSxfVPhWZtWRP3/ETi1Z+ozspBO8JZRAzeP8j0fJrBe9u+kDQJNXiMkgzyWb6Il6roSBWWgwYuepGYf/dSR9YygF6lrV+iQdZdyF08ZIgcNY5g5XWtm4LdH8SO+TZpP9aocLUVR1pmFM6m19MKP+spMg8gwPm6L9YuWSvd62KA8ASIQMtWbzFB6XjanGBQpVeMLI1Uzx4wWRaRaAG5qLTda9PpGk8K6LWOxHwtsuW/CDST/hE5jXvWqfVmrceUVqHz5Qcb0sjKRU5TOYA+JNigSf0Z4CIh7xD1t7bjJf9m6Wcyys/NkwZYryoQm99J2yH2khWXyd2DRETbsynr1AWrSRlStZ5H9ghPoYTqvKvgWsyMVTxbMOht86CzoufceI2W+Rr9
```

So, it seems to be a standard HTTP response (200 OK), but the body looks like it is encoded somehow. I assume that the decoded body would be something like JSON or XML or something containing the various status values – but how do we decode it to get that?

I tried all sorts of things like Base64, MD5 and so on but nothing seemed to work. I gave up on this for a few days, while gently pondering it in the back of my mind. When I came back to it, I realised that the data here was probably actually _encrypted_, using the Access Code that comes with the Wave and the password that you set up when you first connect the Wave. Of course, to decrypt it we need to know how it was encrypted…so time to break out the next tool: a decompiler.

Yes, that’s right: to fully understand exactly what the Wave app is doing, I needed to decompile the Android app’s APK file and look at the code. I did this using the aptly named [Android APK Decompiler](http://www.decompileandroid.com/), and got surprisingly readable Java code out of it! (I mean, it had a lot of goto statements, but at least the variables had sensible names!)

It’s difficult to explain the full details of the encryption/decryption algorithm in prose – so I’ve included the Python code I implemented to do this below. However, a brief summary is that: the main encryption is AES using ECB, with keys generated from the MD5 sums of combinations of the Access Code, the password and a ‘secret’ (a value hard-coded into the app).
```py
def encode(s):
    abyte1 = get_md5(access + secret)
    abyte2 = get_md5(secret + password)
 
    key = abyte1 + abyte2
 
    a = AES.new(key)
    a = AES.new(key, AES.MODE_ECB)
    res = a.encrypt(s)
 
    encoded = base64.b64encode(res)
 
    return encoded
 
def decode(data):
    decoded = base64.b64decode(data)
 
    abyte1 = get_md5(access + secret)
    abyte2 = get_md5(secret + password)
 
    key = abyte1 + abyte2
 
    a = AES.new(key)
    a = AES.new(key, AES.MODE_ECB)
    res = a.decrypt(decoded)
 
    return res
```

Using these functions we can decrypt the response to the `GET /ecus/rrc/uiStatus` message that we saw earlier, and we get this:
```json
{'id': '/ecus/rrc/uiStatus',
 'recordable': 0,
 'type': 'uiUpdate',
 'value': {'ARS': 'init',
  'BAI': 'CH',
  'BBE': 'false',
  'BLE': 'false',
  'BMR': 'false',
  'CPM': 'auto',
  'CSP': '31',
  'CTD': '2014-12-26T12:34:27+00:00 Fr',
  'CTR': 'room',
  'DAS': 'off',
  'DHW': 'on',
  'ESI': 'off',
  'FPA': 'off',
  'HED_DB': '',
  'HED_DEV': 'false',
  'HED_EN': 'false',
  'HMD': 'off',
  'IHS': 'ok',
  'IHT': '16.70',
  'MMT': '15.5',
  'PMR': 'false',
  'RS': 'off',
  'TAS': 'off',
  'TOD': '0',
  'TOR': 'on',
  'TOT': '17.0',
  'TSP': '17.0',
  'UMD': 'clock'},
 'writeable': 0}
``` 

This makes far more sense!

It may not be immediately apparent what each field is (three character variable names – great!), but some of them are fairly obvious (CTD presumably stands for something like Current Time/Date), or can be established by decoding a number of messages with the boiler in different states (showing that DHW stands for Domestic Hot Water and BAI for Burner Active Indicator).

We’ve made a lot of progress in the second part of this guide: we’ve now decrypted the communications, and worked out how to get all of the status information that is shown on the app home screen. At this point I set up a simple temperature monitoring system to produce nice graphs of temperature over time – but I’ll leave the description of that to later in the series. In the next part we’re going to look at sending messages to actually _change_ the state of the thermostat (such as setting a new temperature, or switching to manual mode), and then have a look at the Python library I’ve written to control the thermostat.
