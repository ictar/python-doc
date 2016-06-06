原文：[Reverse Engineering A Mysterious UDP Stream in My Hotel](http://wiki.gkbrk.com/Hotel_Music.html)

---

大家好，我暂时会一直住在酒店里。那是那些现代的酒店之一，带有智能电视和其他连接的东东。我很好奇，于是像任何喜欢捣鼓东西的人会做的那样，打开Wireshark。

我非常惊讶的看到在2046端口有巨大的UDP流量。我查了下这个端口，但是结果没啥用。这不是一个标准端口，所以我必须手动看看它是啥。

起初，我怀疑数据可能是电视的电视流，但是包长度似乎太小了，即使是对于一个视频帧。

### 抓取数据

UDP包并没有发送到我的IP，而我并没有做ARP欺骗，所以这些报文被送到每一个人那里。经过仔细检查，我发现，这些是**多播**包。这基本上意味着数据包被多个设备同时发送和接收一次。另一个要注意的事实是，所有这些包都具有相同的长度（634字节）。

我决定写一个Python脚本来保存和分析这些数据。首先，这里是我用来接收多播数据包的代码。在下面的代码中，_234.0.0.2_是我从Wireshark拿到的IP。

```python

    import socket
    import struct
    
    s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM, socket.IPPROTO_UDP)
    s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    s.bind(('', 2046))
    
    mreq = struct.pack("4sl", socket.inet_aton("234.0.0.2"), socket.INADDR_ANY)
    s.setsockopt(socket.IPPROTO_IP, socket.IP_ADD_MEMBERSHIP, mreq)
    
    while True:
        data = s.recv(2048)
        print(data)
    
```

在此之上，我也用[binascii](https://docs.python.org/3.5/library/binascii.html)将其转换成十六进制，以便更容易读这些字节。在看着这些数以千计的包在控制台滚动后，我注意到，前15字节是相同的。这些字节可能表示协议和包/命令ID，但我只接收到相同的，所以无法验证。

### 音频是如此的LAME

它还花了我相当长的时间去看在包的尾部的`LAME3.91UUUUUUU`字符串。我怀疑这是MPEG压缩音频数据，但是将一个这样的包保存为test.mp3，并无法在mplayer之上播放，而_file_工具只将其当成`test.mp3: data`。在这个包中有明显的数据，而当_file_看到MPEG音频数据时，它应该会知道，所以我决定写另一个Python脚本来带偏移地保存这些包数据。这种方式下，它会跳过1字节保存包到文件`test1`，跳过2字节保存包到`test2`，等等。下面是我用的代码和结果。

```python

    data = s.recv(2048)
    for i in range(25):
        open("test{}".format(i), "wb+").write(data[i:])
    
```

在此之后，我运行`file test*`，然后接着看！现在，我们知道必须跳过8个字节来获得MPEG音频数据。

```python

    $ file test*
    test0:    data
    test1:    UNIF v-16624417 format NES ROM image
    test10:   UNIF v-763093498 format NES ROM image
    test11:   UNIF v-1093499874 format NES ROM image
    test12:   data
    test13:   TTComp archive, binary, 4K dictionary
    test14:   data
    test15:   data
    test16:   UNIF v-1939734368 format NES ROM image
    test17:   UNIF v-1198759424 format NES ROM image
    test18:   UNIF v-256340894 format NES ROM image
    test19:   UNIF v-839862132 format NES ROM image
    test2:    UNIF v-67173804 format NES ROM image
    test20:   data
    test21:   data
    test22:   data
    test23:   DOS executable (COM, 0x8C-variant)
    test24:   COM executable for DOS
    test3:    UNIF v-1325662462 format NES ROM image
    test4:    data
    test5:    data
    test6:    data
    test7:    data
    test8:    MPEG ADTS, layer III, v1, 192 kbps, 44.1 kHz, JntStereo
    test9:    UNIF v-2078407168 format NES ROM image
    
```

```python

    while True:
        data = s.recv(2048)
        sys.stdout.buffer.write(data[8:])
    
```

现在，我们所需要做的事只是继续读包，跳过前8个字节，将它们写入到一个文件中，然后它应该可以完美播放。

但这个音频是啥呢？这是一个听了我的话悄悄放置的错误吗？它是关于我的房间的智能电视的一些东东吗？一些关于整个酒店系统的？只有一个办法可以找出原因。

```python

    $ python3 listen_2046.py > test.mp3
    * wait a little to get a recording *
    ^C
    
    $ mplayer test.mp3
    MPlayer (C) 2000-2016 MPlayer Team
    224 audio & 451 video codecs
    
    Playing test.mp3.
    libavformat version 57.25.100 (external)
    Audio only file format detected.
    =====
    Starting playback...
    A:   3.9 (03.8) of 13.0 (13.0)  0.7%
    
```

### 启示/失望

搞神马嘛？简直不敢相信我花时间在这上面。这只是电梯音乐。它在酒店走廊的电梯周围播放。哦，好吧，至少现在我可以从我的房间听到它了。


