原文：[Reverse Engineering A Mysterious UDP Stream in My Hotel](http://wiki.gkbrk.com/Hotel_Music.html)

---

Hey everyone, I have been staying at a hotel for a while. It's one of those
modern ones with smart TVs and other connected goodies. I got curious and
opened Wireshark, as any tinkerer would do.

I was very surprised to see a huge amount of UDP traffic on port 2046. I
looked it up but the results were far from useful. This wasn't a standard
port, so I would have to figure it out manually.

At first, I suspected that the data might be a television stream for the TVs,
but the packet length seemed too small, even for a single video frame.

### Grabbing the data

The UDP packets weren't sent to my IP and I wasn't doing ARP spoofing, so
these packets were sent to everyone. Upon closer inspection, I found out that
these were **Multicast** packets. This basically means that the packets are
sent once and received by multiple devices simultaneously. Another thing I
noticed was the fact that all of those packets were the same length (634
bytes).

I decided to write a Python script to save and analyze this data. First of
all, here's the code I used to receive Multicast packets. In the following
code, _234.0.0.2_ is the IP I got from Wireshark.

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

On top of this, I also used
[binascii](https://docs.python.org/3.5/library/binascii.html) to convert this
to hex in order make reading the bytes easier. After watching thousands of
these packets scroll through the console, I noticed that the first ~15 bytes
were the same. These bytes probably indicate the protocol and the
packet/command ID but I only received the same one so I couldn't investigate
those.

### Audio is so LAME

It also took me an embarrassingly long time to see the string
`LAME3.91UUUUUUU` at the end of the packets. I suspected this was MPEG
compressed audio data, but saving one packet as test.mp3 failed to played with
mplayer and the _file_ utility only identified this as `test.mp3: data`. There
was obviously data in this packet and _file_ should know when it sees MPEG
Audio data, so I decided to write another Python script to save the packet
data with offsets. This way it would save the file `test1` skipping 1 byte
from the packet, `test2` skipping 2 bytes and so on. Here's the code I used
and the result.

```python

    data = s.recv(2048)
    for i in range(25):
        open("test{}".format(i), "wb+").write(data[i:])
    
```

After this, I ran `file test*` and voilà! Now we know we have to skip 8 bytes
to get to the MPEG Audio data.

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

Now all we need to do is continuously read packets, skip the first 8 bytes,
write them to a file and it should play perfectly.

But what was this audio? Was this a sneakily placed bug that listened to me?
Was it something related to the smart TVs in my room? Something related to the
hotel systems? Only one way to find out.

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

### The Revelation/Disappointment

What the hell? I can't believe I spent time for this. It's just elevator
music. It is played in the hotel corridors around the elevators. Oh well, at
least I can listen to it from my room now.


