原文：[Backup Photos While Traveling With an Ipad Pro and a Raspberry Pi](http://www.movingelectrons.net/blog/2016/06/26/backup-photos-while-traveling-with-a-raspberry-pi.html "Permalink to Backup Photos While Traveling
With an Ipad Pro and a Raspberry Pi" )

---

![旅行照片备份装置](http://www.movingelectrons.net/images/bkup_photos_main.jpg)

旅行时备份照片 —— 装置。

## 介绍

很久以来，我都一直在寻求找到一个理想的旅行照片备份解决方法。旅行中，当SD卡满了的时候，仅仅只是将它们扔到你的相机包里，是非常危险的，这让你太暴露了：SD卡可能会丢失或被偷，传输中数据可能会损坏，或者SD卡也可能坏掉。备份到另一个介质上 —— 即使只是另一张SD卡 —— 然后在旅行中将其放在一个(更)安全的地方，是一种最佳实践。理想情况下，备份到远程位置是上上之策，但取决于你旅行的地方以及该地的网络可用性，这可能是不实际的。

对于这个理想的备份过程，我的需求是：

  1. 使用一个iPad而不是一台笔记本电脑来管理该过程。我喜欢轻旅行，由于我大多数的旅程都是商旅（即不涉及到照相），我讨厌带着我的商务笔记本的同时还带着个人笔记本。然而，我总是带着iPad，因此将它用作工具理所因当。
  2. 实际使用越少硬件设备越好。
  3. 设备间的连接应该是**安全的**。我将在酒店或机场使用这个装置，因此，设备间不公开且加密的连接是理想的。
  4. 整个过程应该是坚固且**可靠**。我尝试过其他选项，使用路由器/组合设备，但是[不尽人意](http://bit.ly/1MVVtZi)。

## 安装程序

我想到了一个装置，它满足上面的要求，并且未来也有足够的灵活性可供扩展。它涉及一下装置的使用：

  1. [iPad Pro 9.7](http://www.amazon.com/dp/B01D3NZIMA/?tag=movinelect0e-20)英寸。这是截止到写这篇文章的时候最强大、体积最小并且最轻量的iOS设备。**Apple pencil**可有可无，但它是我的装置的一部分，因为路上，我还会在iPad Pro上写点东西。所有繁重的工作将由Raspberry Pi完成，因此任何其他能够通过SSH连接的设备都符合需求。
  2. [Raspberry Pi 3](http://www.amazon.com/dp/B01CD5VC92/?tag=movinelect0e-20)，上面装了Raspbian系统。
  3. 用于Raspberry Pi[Micro SD](http://www.amazon.com/dp/B010Q57T02/?tag=movinelect0e-20)卡，以及一个Raspberry Pi [盒子](http://www.amazon.com/dp/B01F1PSFY6/?tag=movinelect0e-20)。
  4. [128 GB Pen Drive](http://amzn.to/293kPqX)。你可以使用更大容量的，但是128 GB对我来说够用了。你还可以使用一个便携式外置硬盘，例如[这个](http://amzn.to/290syFY)，但是Raspberry Pi通过其USB端口可能没法提供足够的功率，这意味着你不得不再用一个[供电的USB集线器](http://amzn.to/290syFY)，并带上所需的电缆，这与使用一个轻便简约的设置的初衷相悖。
  5. [SD卡读卡器](http://amzn.to/290syFY)
  6. [SD卡](http://amzn.to/290syFY)。我用好几个，因为我不会在一个满了之后才用另一个。这让我在多个卡之间都放上单个旅程中的照片。

下图显示了这些设备将如何相互交互。

![旅行照片备份装置过程图](http://www.movingelectrons.net/images/bkup_photos_diag.jpg)

旅行时备份照片 - 过程图

Raspberry Pi将会配置为一个**安全热点**。它将创建自己的WPA2-encrypted WiFi网络，以供iPad Pro连接。虽然网上有很多教程教你用Raspberry Pi创建一个Ad Hoc (即，计算机到计算机)连接，这会更容易建立；但是那种连接是未加密的，你旁边的其他设备很容易就可以连接到它。因此，我决定使用WiFi选项。

通过SD卡读卡器，相机的SD卡将会连到Raspberry Pi的一个USB口上。此外，高容量的Pen Drive (我用了128 GB)将会永久插到Raspberry Pi的一个USB口上。我选择了[Sandisk Ultra Fit](http://amzn.to/293kPqX)，因为它小。**主要想法是让Raspberry Pi在一个Python脚本的帮助下，备份SD卡的照片到Pen Drive**。备份过程是增量的，意味着每次该脚本运行的时候，只有改动 (即，拍了新的照片)才会被添加到备份文件夹，这让整个过程相当快。如果你拍了很多照片，或者以_RAW_格式拍摄，这将是一个巨大的优势。iPad将会用来触发该Python脚本，并且在需要的时候用来浏览SD卡和Pen Drive。

有个福利，如果Raspberry Pi通过有线连接（即通过以太网端口）连接到因特网，那么**它将能够与连接到它自己的WiFi网络上的设备共享因特网连接**。

## 1\. Raspberry Pi配置

这是该我们卷起袖子开始忙起来的部分了，我们将使用Raspbian的_命令行接口_ (CLI)。我会试着尽可能清晰，以使得整个过程变得容易。


### 安装和配置Raspbian系统

连接键盘、鼠标和一个LCD显示器到Raspberry Pi。将微型SD插入到Raspberry Pi的插槽，并继续按照[官网](https://www.raspberrypi.org/downloads/noobs/)上的说明安装Raspbian。

安装完成后，进入CLI（Raspbian中的终端），并输入：

```python

    sudo apt-get update
    sudo apt-get upgrade
    
```

这将升级树莓派上的所有软件。我配置了Raspberry Pi，使其连接到本地网络，并为了安全起见，修改了默认密码。

默认情况下，Raspbian上启用了SSH，所以下面所有部分都是在远程计算机上完成的。我还配置了RSA身份验证，但那是可选的。[这里](https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md)你可以看到更多有关信息。

这里是Mac上的[iTerm](https://www.iterm2.com)到Raspberry Pi的SSH连接的截图：

### 创建加密(WPA2)访问点

基于[这篇](https://frillip.com/using-your-raspberry-pi-3-as-a-wifi-access-point-with-hostapd/)文章，我进行了安装，并根据实际情况对其进行了优化。

**1\. 安装包**

我们需要输入以下命令以安装所需的包：

```python

    sudo apt-get install hostapd
    sudo apt-get install dnsmasq
    
```

**hostapd**允许使用内置的WiFi作为接入点。**dnsmasq**是一个易于配置的DHCP和DNS服务器的组合。

**2\. 编辑dhcpcd.conf**

通过以太网连接到Raspberry Pi。Raspbery Pi上的接口配置由`dhcpcd`来处理，因此首先，我们让它忽略`wlan0`，因为将为它配置一个静态IP地址。

使用`sudo nano /etc/dhcpcd.conf`打开一个dhcpcd配置文件，然后添加以下行到文件底部：

`denyinterfaces wlan0`

**注意**：这必须在已经添加的任何接口行**之上**。

**3\. 编辑接口**

现在，我们需要配置静态IP。使用`sudo nano /etc/network/interfaces`打开接口配置文件，编辑`wlan0`部分，如下所示：

```python

    allow-hotplug wlan0
    iface wlan0 inet static
        address 192.168.1.1
        netmask 255.255.255.0
        network 192.168.1.0
        broadcast 192.168.1.255
    #    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
    
```

此外，编辑`wlan1`部分：

```python

    #allow-hotplug wlan1
    #iface wlan1 inet manual
    #    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
    
```

**重要提示：** 使用`sudo service dhcpcd restart`重启`dhcpcd`，然后使用`sudo ifdown eth0; sudo ifup wlan0`重新加载`wlan0`的配置。

**4\. 配置hostapd**

接下来，我们需要配置`hostapd`。使用`sudo nano /etc/hostapd/hostapd.conf`创建一个新的配置文件，并添加以下内容：

```python

    interface=wlan0
    
    # Use the nl80211 driver with the brcmfmac driver
    driver=nl80211
    
    # This is the name of the network
    ssid=YOUR_NETWORK_NAME_HERE
    
    # Use the 2.4GHz band
    hw_mode=g
    
    # Use channel 6
    channel=6
    
    # Enable 802.11n
    ieee80211n=1
    
    # Enable QoS Support
    wmm_enabled=1
    
    # Enable 40MHz channels with 20ns guard interval
    ht_capab=[HT40][SHORT-GI-20][DSSS_CCK-40]
    
    # Accept all MAC addresses
    macaddr_acl=0
    
    # Use WPA authentication
    auth_algs=1
    
    # Require clients to know the network name
    ignore_broadcast_ssid=0
    
    # Use WPA2
    wpa=2
    
    # Use a pre-shared key
    wpa_key_mgmt=WPA-PSK
    
    # The network passphrase
    wpa_passphrase=YOUR_NEW_WIFI_PASSWORD_HERE
    
    # Use AES, instead of TKIP
    rsn_pairwise=CCMP
    
```

现在，我们还需要告诉`hostapd`，开机启动时应该到哪里寻找配置文件。通过`sudo nano /etc/default/hostapd`打开默认的配置文件，找到`#DAEMON_CONF=""`一行，然后将其改为`DAEMON_CONF="/etc/hostapd/hostapd.conf"`。

**5\. 配置dnsmasq**

自带的dnsmasq配置文件涵盖大量的关于如果使用它的信息，但是我们无需使用所有的选秀。我推荐将其重命名（而不是删掉它），然后创建一个新的配置文件：

```python

    sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.orig  
    sudo nano /etc/dnsmasq.conf  
    
```

将以下内容粘贴到新的文件中：

```python

    interface=wlan0      # Use interface wlan0
    listen-address=192.168.1.1 # Explicitly specify the address to listen on
    bind-interfaces      # Bind to the interface to make sure we aren't sending things elsewhere
    server=8.8.8.8       # Forward DNS requests to Google DNS
    domain-needed        # Don't forward short names
    bogus-priv           # Never forward addresses in the non-routed address spaces.
    dhcp-range=192.168.1.50,192.168.1.100,12h # Assign IP addresses in that range  with a 12 hour lease time
    
```

**6\. 设置IPV4转发**

我们需要做的最后一件事是启用包转发。通过`sudo nano /etc/sysctl.conf`打开sysctl.conf文件，然后移除包含`net.ipv4.ip_forward=1`的行的行首的#。这在下次重启时将会启用它。

我们还需要通过在`wlan0`接口和`eth0`接口之间配置NAT，来共享Raspberry Pi的互联网连接，以使得我们的设备可以通过WiFi进行连接。我们可以编写以下脚本来做到这点。

```python

    sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE  
    sudo iptables -A FORWARD -i eth0 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT  
    sudo iptables -A FORWARD -i wlan0 -o eth0 -j ACCEPT  
    
```

我将该脚本命名为`hotspot-boot.sh`，并让它可执行：

`sudo chmod 755 hotspot-boot.sh`

该脚本应该在Raspberry Pi启动的时候执行。有很多种方式可以来做到，而下面是我选择的方式：

  1. 将该文件放到`/home/pi/scripts`。
  2. 输入`sudo nano /etc/rc.local`以编辑`rc.local`文件，然后**在`exit 0`行之前**放置脚本调用 (详情点击[这里](https://www.raspberrypi.org/documentation/linux/usage/rc-local.md))。

这是编辑后，`rc.local`文件的样子。

```python

    #!/bin/sh -e
    #
    # rc.local
    #
    # This script is executed at the end of each multiuser runlevel.
    # Make sure that the script will "exit 0" on success or any other
    # value on error.
    #
    # In order to enable or disable this script just change the execution
    # bits.
    #
    # By default this script does nothing.
    
    # Print the IP address
    _IP=$(hostname -I) || true
    if [ "$_IP" ]; then
      printf "My IP address is %s\n" "$_IP"
    fi
    
    sudo /home/pi/scripts/hotspot-boot.sh &
    
    exit 0
    
```   
  
### 安装Samba和NTFS兼容性

我们还需要安装下面的包以启用Samba协议，并允许[文件浏览器](https://itunes.apple.com/us/app/filebrowser-access-files-on/id364738545?mt=8&uo=4&at=11lqkH)应用可以看到连接到Raspberry Pi的设备成为共享文件夹。另外，万一我们决定连接一个便携式硬盘驱动器到Raspberry Pi，`ntfs-3g`提供了NTFS兼容。

```python

    sudo apt-get install ntfs-3g
    sudo apt-get install samba samba-common-bin
    
```

你可以看看[这篇](http://www.howtogeek.com/139433/how-to-turn-a-raspberry-pi-into-a-low-power-network-storage-device/)文章，了解如何配置Samba。

**重要提示：** 该引用文章还包含了在Raspberry Pi上挂载外置硬盘的内容。截止到写这篇文章的时候，不用那么做，因为**Raspbian (Jessie)的当前版本在启动的时候，自动挂载SD卡和Pendrive到`/media/pi/`**。该文章还包含了我们不会使用的一些冗余特性。

## 2\. Python脚本

现在，已经配置好了Raspberry Pi，我们需要开始写实际备份/拷贝我们照片的脚本了。注意到**这个脚本只是提供某些程度的自动化给备份过程**。如果你具备使用Linux/Raspbian CLI的基本知识，那么可以SSH到Raspberry Pi，然后通过创建所需文件夹并使用`cp`或者`rsync`命令拷贝设备中的所有照片到另一个地方即可。我们将在脚本种使用`rsync`方法，因为它非常可靠，并允许**增量备份**。

此过程依赖两个文件：脚本自身，以及配置文件`backup_photos.conf`。后者仅需包含几行表示目的驱动器(Pendrive)挂载处以及文件夹挂载处的信息。如下所示：

```python

    mount folder=/media/pi/
    destination folder=PDRIVE128GB
    
```

**重要提示：** 不要在`=`符号以及单词之间添加任何空格，这样会搞挂脚本 (这绝对是个改进的机会)。

下面是这个Python脚本，我将其命名为`backup_photos.py`，并把它放在`/home/pi/scripts/`下面。我在代码间加了注释，使其更容易读懂。

```python

    #!/usr/bin/python3
    
    import os
    import sys
    from sh import rsync
    
    '''
    The script copies an SD Card mounted on /media/pi/ to a folder with the same name 
    created in the destination drive. The destination drive's name is defined in 
    the .conf file.
    
    
    Argument:  label/name of the mounted SD Card.
    '''
    
    CONFIG_FILE = '/home/pi/scripts/backup_photos.conf'
    ORIGIN_DEV = sys.argv[1]
    
    def create_folder(path):
    
        print ('attempting to create destination folder: ',path)
        if not os.path.exists(path):
            try: 
                os.mkdir(path)
                print ('Folder created.')
            except:
                print ('Folder could not be created. Stopping.')
                return
        else:
            print ('Folder already in path. Using that instead.')
    
    
    
    confFile = open(CONFIG_FILE,'rU') 
    #IMPORTANT: rU Opens the file with Universal Newline Support, 
    #so \n and/or \r is recognized as a new line.
    
    confList = confFile.readlines()
    confFile.close()
    
    
    for line in confList:
        line = line.strip('\n')
    
        try:
            name , value = line.split('=')
    
            if name == 'mount folder':
                mountFolder = value
            elif name == 'destination folder':
                destDevice = value
    
    
        except ValueError:
            print ('Incorrect line format. Passing.')
            pass
    
    
    destFolder = mountFolder+destDevice+'/'+ORIGIN_DEV
    create_folder(destFolder)
    
    print ('Copying files...')
    
    # Comment out to delete files that are not in the origin:
    # rsync("-av", "--delete", mountFolder+ORIGIN_DEV, destFolder)
    rsync("-av", mountFolder+ORIGIN_DEV+'/', destFolder)
    
    print ('Done.')
    
```   
  
## 3\. iPad Pro配置

由于所有繁重的工作将会在Raspberry Pi上完成，并且不会通过iPad Pro传输任何文件，这是[我之前尝试的工作流程之一](http://bit.ly/1MVVtZi)的一个巨大的劣势；我们仅需在iPad之上安装[Prompt 2](https://itunes.apple.com/us/app/prompt-2/id917437289?mt=8&uo=4&at=11lqkH)，以通过SSH访问Raspeberry Pi。一旦连接了，你可以运行手动Python脚本，或者拷贝文件。

![Prompt截图](http://www.movingelectrons.net/images/bkup_photos_ipad&rpi_prompt.jpg)

iPad使用Prompt通过SSH连接到Raspberry Pi。

由于我们安装了Samba，因此可以以一种更图形化的方式访问连接到Raspberry Pi的USB设备。**你可以播放视频，在设备之间拷贝和移动文件。** [文件浏览器](https://itunes.apple.com/us/app/filebrowser-access-files-on/id364738545?mt=8&uo=4&at=11lqkH)非常适合做这件事。

## 4\. 把它们都放在一起

假设`SD32GB-03`是连接到Raspberry Pi的USB口之一的SD卡的标签。另外，假设`PDRIVE128GB`是Pendrive的标签，它也连接到设备，并在上面提到的`.conf`文件中定义。如果我们想在SD卡中备份照片，那么我们将需要经过以下步骤：

  1. 打开Raspberry Pi，让设备自动挂载。
  2. 连接到Raspberry Pi生成的WiFi网络。
  3. 使用[Prompt](https://itunes.apple.com/us/app/prompt-2/id917437289?mt=8&uo=4&at=11lqkH)应用，通过SSH连接到Raspberry Pi。
  4. 一旦连接上了，输入以下内容：

`python3 backup_photos.py SD32GB-03`

第一次备份照片花了几分钟，这取决于使用的卡数量。这意味着，**你需要保持iPad到Raspberry Pi的连接。你可以在运行脚本前使用[nohup](https://en.m.wikipedia.org/wiki/Nohup)命令来解决这个问题**。

`nohup python3 backup_photos.py SD32GB-03 &`

![运行Python脚本后，iterm的截图](http://www.movingelectrons.net/images/bkup_photos_ipad&rpi_finished.png)

运行Python脚本后，iTerm的截图。

## 进一步定制

我安装了一个VNC服务器，以便从另一台电脑，或者使用iPad的[Remoter应用](https://itunes.apple.com/us/app/remoter-pro-vnc-ssh-rdp/id519768191?mt=8&uo=4&at=11lqkH)来访问Raspbian的图形界面。我期待安装[BitTorrent Sync](https://getsync.com)，以用来在路上的时候备份照片到一个远程位置，这将会是比较理想的配置。一旦我有了一个可行的解决方案，我将扩展本文。

随意在下面发表评论/提出问题，或者与我联系。我的联系方式在本页的下方（Ele注：请到原文找）。
