原文：[Hacking the Worcester Wave thermostat in Python – Part 2](http://blog.rtwilson.com/hacking-the-worcester-wave-thermostat-in-python-part-2/)

---

在[前面的部分](http://blog.rtwilson.com/hacking-the-worcester-wave-thermostat-in-python-part-1/)，我们已经确定了Worcester Wave恒温器应用使用XMPP协议与远程服务器(由Worcester Bosch运行)进行通信，并使用TLS加密。然而，由于加密，我们还尚未成功看到这些消息的任何实际内容！

要解密消息，我们需要做一个中间人攻击。在这种攻击中，我们把自己插入到对话两端之间，拦截消息，解密它们，然后转发到原目的地。虽然被称为“攻击”，在这里我基本上是攻击自己-因为我基本上是通过我自己的网络监控通过我自己的手机的通信。要注意的是，对于那些你不拥有的网络，或者是没有以这种方式使用的权限的网络，这样做几乎可以肯定是违法的。

有一个[很好的指导](https://blog.heckel.xyz/2013/08/04/use-sslsplit-to-transparently-sniff-tls-ssl-connections/)，这个指导使用一个名为[sslsplit](https://www.roe.ch/SSLsplit)的工具来设置这一切，虽然我不得不做的事情略有不同，因为我无法让sslsplit与Worcester Wave使用的STARTTLS方法一起工作（从先前的部分，你可能还记得，STARTTLS是一种方式，它以未加密的方式开始通信，然后通过通信“打开”的加密）。

我使用的方法总结如下：

1. 我在我的网络上配置了一台Linux服务器，它使用网络地址转换（NAT），所以它几乎像一个路由器一样工作。这意味着我可以在网络上再设置其他设备以作为一个“网关”使用该服务器，这意味着它会发送 所有流量发送到那个服务器上，然后将其转发到正确的位置。
2. 我在服务器上创建了一个自签名的[根证书](https://en.wikipedia.org/wiki/Root_certificate)。根证书是可以用于信任来自它派生的任何其他证书或密钥的“完全信任”证书（这个解释可能技术上错误，但概念上它是对的）。
3.  我在一个备用的Andr​​oid手机上安装了这个根证书，将手机连接到我家wifi，然后配置Linux服务器的网关。接着，进行测试，它可以从手机很好的访问互联网，其中所有的通信通过服务器。

现在，如果我在手机上使用Worcester Wave应用，那么所有的通信将通过服务器 - 当该服务器说它是另一端的Bosch服务器时，手机将信任这个服务器，这是因为我们安装的根证书。

现在，我们已经配置了所有的证书和网络相关的东西，我们只需要实际解密消息。正如我前面所说，我尝试使用[SSLSplit](https://www.roe.ch/SSLsplit)，但它似乎无法应付STARTTLS。我发现使用Wireshark本身也是一样的，所以我找了另一种方法。

幸运的是，我发现了名为[starttls-mitm](https://github.com/ipopov/starttls-mitm)的伟大的工具，它确实对STARTTLS有效。更令人印象深刻的是，它几乎只有80行Python代码，所以很容易理解这个代码。这并不意味着它比更复杂的工具，例如SSLSplit，更不可配置 —— 但是这对我来说不是一个问题，因为工具做的正是我想要的东西。默认情况下，它甚至配置了XMPP呢！（当然，因为它是用Python写的，所以如果我需要的话，我可以随时自己修改代码）

因此，使用相应的命令行参数（基本上是你的密钥，证书等）运行starttls-mitm将打印出所有的通信：都是STARTTLS调用之前的未加密的通信，以及STARTTLS调用后那些加密的解密版本。如果当其运行时，我们再开始使用该应用程序做一些事情，那么我们获得什么呢？

嗯，首先我们得到打开的日志信息，starttls-mitm告诉我们它是做什么的：
```sh
LISTENER ready on port 8443
CLIENT CONNECT from: ('192.168.0.42', 57913)
RELAYING
```

然后，获取该通信的开端：
```xml
C->S 129 '<stream:stream to="wa2-mz36-qrmzh6.bosch.de" xmlns="jabber:client" xmlns:stream="http://etherx.jabber.org/streams" version="1.0">'
S->C 442 '<?xml version=\'1.0\' encoding=\'UTF-8\'?><stream:stream xmlns:stream="http://etherx.jabber.org/streams" xmlns="jabber:client" from="wa2-mz36-qrmzh6.bosch.de" id="260d2859" xml:lang="en" version="1.0"><stream:features><starttls xmlns="urn:ietf:params:xml:ns:xmpp-tls"></starttls><mechanisms xmlns="urn:ietf:params:xml:ns:xmpp-sasl"><mechanism>DIGEST-MD5</mechanism></mechanisms><auth xmlns="http://jabber.org/features/iq-auth"/></stream:features>'
```

相当明显，`C->S`是从客户端到服务器的消息，而`S->C`是从服务器返回的消息（后面紧跟着的数字只是消息的长度）。这些只是XMPP通信开始的初始握手通信，并且不是特别令我们兴奋，因为我们也在Wireshark中看到了这一点。

但是，现在我们得到了有趣的一点：
```xml
C->S 51 '<starttls xmlns="urn:ietf:params:xml:ns:xmpp-tls"/>'
S->C 50 '<proceed xmlns="urn:ietf:params:xml:ns:xmpp-tls"/>'
Wrapping sockets.
```

STARTTLS消息被发送，然后服务器说PROCEED，而关键的是，starttls-mitm注意到了这点并宣布它是“包装套接字”（基本上从这时起，启用通信的解密）。

现在，我将跳过无聊的TLS握手消息，并跳到XMPP协议本身的初始化。我不是XMPP大专家，但基本上，`iq`消息是“信息/查询”消息，这些消息是握手过程的一部分，其中每一方说，它们是谁，它们支持什么等.当每边宣布它的“存在”时，通信的这部分结束（记住，XMPP原本是一个聊天协议，所以等同于在Skype，Facebook Messenger等上说你“在线”或“活跃”）。

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

现在所有的初步消息都被处理了，我们一点点的做好。下面是从客户端（手机应用程序）发送到服务器的消息：
```xml
C->S 162 '<message id="lj8Vq-6" to="rrcgateway_458921440@wa2-mz36-qrmzh6.bosch.de" type="chat"><body>GET /ecus/rrc/uiStatus HTTP /1.0\nUser-Agent: NefitEasy</body></message>'
```

基本上，它似乎是嵌入在XMPP消息中的HTTP GET请求。这对我来说似乎有点奇怪 —— 为什么不直接使用HTTP呢？ - 但至少这是很容易理解的。被请求的URL页是有道理的 —— 在这一点上我位于应用的“主屏幕”上，因此它抓取状态用于在用户界面中显示（像当前温度，设定温度，热水器是否开启，等等信息）。

现在我们可以看到来自服务器的响应：
```xml
S->C 904 '<message to="rrccontact_458921440@wa2-mz36-qrmzh6.bosch.de/70" type="chat" xml:lang="en" from="rrcgateway_458921440@wa2-mz36-qrmzh6.bosch.de/RRC-RestApi"><body>HTTP/1.0 200 OK\nContent-Length: 640\nContent-Type: application/json\nconnection: close\n\n5EBW5RuFo7QojD4F1Uv0kOde1MbeVA46P3RDX6ZEYKaKkbLxanqVR2I8ceuQNbxkgkfzeLgg6D5ypF9jo7yGVRbR/ydf4L4MMTHxvdxBubG5HhiVqJgSc2+7iPvhcWvRZrRKBEMiz8vAsd5JleS4CoTmbN0vV7kHgO2uVeuxtN5ZDsk3/cZpxiTvvaXWlCQGOavCLe55yQqmm3zpGoNFolGPTNC1MVuk00wpf6nbS7sFaRXSmpGQeGAfGNxSxfVPhWZtWRP3/ETi1Z+ozspBO8JZRAzeP8j0fJrBe9u+kDQJNXiMkgzyWb6Il6roSBWWgwYuepGYf/dSR9YygF6lrV+iQdZdyF08ZIgcNY5g5XWtm4LdH8SO+TZpP9aocLUVR1pmFM6m19MKP+spMg8gwPm6L9YuWSvd62KA8ASIQMtWbzFB6XjanGBQpVeMLI1Uzx4wWRaRaAG5qLTda9PpGk8K6LWOxHwtsuW/CDST/hE5jXvWqfVmrceUVqHz5Qcb0sjKRU5TOYA+JNigSf0Z4CIh7xD1t7bjJf9m6Wcyys/NkwZYryoQm99J2yH2khWXyd2DRETbsynr1AWrSRlStZ5H9ghPoYTqvKvgWsyMVTxbMOht86CzoufceI2W+Rr9</body></message>'
```

噢，这看起来有点复杂，而且不是很容易理解。让我们格式化消息主体让它看起来更好些：
```
HTTP/1.0 200 OK
Content-Length: 640
Content-Type: application/json
connection: close
 
5EBW5RuFo7QojD4F1Uv0kOde1MbeVA46P3RDX6ZEYKaKkbLxanqVR2I8ceuQNbxkgkfzeLgg6D5ypF9jo7yGVRbR/ydf4L4MMTHxvdxBubG5HhiVqJgSc2+7iPvhcWvRZrRKBEMiz8vAsd5JleS4CoTmbN0vV7kHgO2uVeuxtN5ZDsk3/cZpxiTvvaXWlCQGOavCLe55yQqmm3zpGoNFolGPTNC1MVuk00wpf6nbS7sFaRXSmpGQeGAfGNxSxfVPhWZtWRP3/ETi1Z+ozspBO8JZRAzeP8j0fJrBe9u+kDQJNXiMkgzyWb6Il6roSBWWgwYuepGYf/dSR9YygF6lrV+iQdZdyF08ZIgcNY5g5XWtm4LdH8SO+TZpP9aocLUVR1pmFM6m19MKP+spMg8gwPm6L9YuWSvd62KA8ASIQMtWbzFB6XjanGBQpVeMLI1Uzx4wWRaRaAG5qLTda9PpGk8K6LWOxHwtsuW/CDST/hE5jXvWqfVmrceUVqHz5Qcb0sjKRU5TOYA+JNigSf0Z4CIh7xD1t7bjJf9m6Wcyys/NkwZYryoQm99J2yH2khWXyd2DRETbsynr1AWrSRlStZ5H9ghPoYTqvKvgWsyMVTxbMOht86CzoufceI2W+Rr9
```

所以，这似乎是一个标准的HTTP响应（200 OK），但响应体看起来是用某种方式进行编码。我假设解码的响应体是像JSON或XML或含有各种状态值的东西 - 但我们如何解码得到这些信息？

我尝试各种东西，例如Base64，MD5等，但似乎没有任何用。在这几天，我放弃了，而是在我的脑海里轻轻地琢磨。当我回过头看它的时候，我意识到，这里的数据可能是实际加密的，使用Wave自带的访问码和当你第一次连接Wave设置的密码。当然，要对其进行解密，我们需要知道它是如何加密的...所以是时候拿出下一个工具了：一个反编译器。

是的，这是正确的：要充分认识Wave应用程序是干什么的，我需要反编译Android应用的APK文件，并查看代码。我使用适当命名的[Android APK反编译器](http://www.decompileandroid.com/)这样做，并从中得到了令人惊讶的可读的Java代码！（我的意思是，它有很多goto语句，但至少变量有有意义的名字！）

很难用散文的形式解释加密/解密算法的全部细节 - 所以我已经在下面包括了我实现它的Python代码。然而，小结是：主加密使用ECB的AES，密钥是由接入码，密码和一个‘secret’（硬编码到应用程序的值）的组合的MD5校验和生成的。
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

使用这些函数，我们可以解密我们之前看到的`GET /ecus/rrc/uiStatus`消息的响应，然后我们得到了这些：
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

这有意义得多！

它可能不会立即显现出来每个字段是什么（三字符变量名 - 太棒了！），但其中一些相当明显（CTD大概代表了像当前时间/日期之类的东西），也可以通过解码一堆在不同的状态下热水器的消息来建立（它表明，DHW代表了家用热水（Domestic Hot Water），而BAI表示燃烧器活动指示灯（Burner Active Indicator））。

在本指南的第二部分，我们已经取得了很大的进步：我们现在已经解密了通信，并制定了如何获得所有在应用主屏幕上显示的状态信息。在这一点上，我建立了一个简单的温度监控系统来产生漂亮的随着时间的推移的温度曲线图 —— 但我会将这部分的说明留在以后的系列。在接下来的部分，我们将看看发送消息以真正 改变温控器的状态（如设置新的温度，或切换至手动模式），然后看看我写的控制温控器的Python库。
