原文：[Getting started with Raspberry Pi - Building a Digital Photo Frame](https://paulstamatiou.com/getting-started-raspberry-pi/)

---

** 用一个小小的$35的电脑，你可以做什么，以及我是如何构建一个电子相框的**

2012年年初，一个名字奇怪的有趣的单板计算机投入了市场。仅需低廉的$35，你就可以获得一个可以运行真正操作系统的全功能计算机。

这就是所谓的树莓派（Raspberry Pi），它是一个名为树莓派基金会的英国慈善机构的心血结晶。在看到申请学习计算机科学的学生人数持续下降后，他们看到了对负担得起的计算机的需求。

而事实证明，这个微小而廉价的全功能计算机有一个比预期大得多的受众。自创建以来，已经有多个型号了，包括$5的Pi Zero，另外，**已经售出了超过9百万的树莓派**。

这是一篇很长的博文，因此，一路上我更有可能犯一些错。随时[在Twitter上知会我](http://twitter.com/Stammy "Paul Stamatiou on Twitter")，谢谢！

<!-- MarkdownTOC -->

- [它是什么？](#它是什么？)
      - [这就像一块Arduino吗？并不是。](#这就像一块arduino吗？并不是。)
      - [电量消耗](#电量消耗)
      - [用于教育的Pi](#用于教育的pi)
      - [Pi社区和资源](#pi社区和资源)
- [有了Pi，你能做什么？](#有了pi，你能做什么？)
    - [灰常多。](#灰常多。)
  - [为什么我会有一个Pi？](#为什么我会有一个pi？)
  - [所以，你可以拿它做什么？](#所以，你可以拿它做什么？)
  - [Pi不大适合做什么？](#pi不大适合做什么？)
- [开始](#开始)
    - [操作系统安装](#操作系统安装)
  - [你需要什么](#你需要什么)
      - [零部件清单](#零部件清单)
      - [Optional](#optional)
  - [什么操作系统？](#什么操作系统？)
  - [Imaging the microSD card](#imaging-the-microsd-card)
  - [Benchmarking, overclocking and cooling](#benchmarking-overclocking-and-cooling)
      - [Heatsinks](#heatsinks)
- [使用I/O引脚](#使用io引脚)
    - [Pi electronics 101](#pi-electronics-101)
  - [Parts](#parts)
  - [Setup & powering an LED](#setup--powering-an-led)
  - [Control the LED](#control-the-led)
  - [Using a transistor](#using-a-transistor)
  - [Use a relay](#use-a-relay)
  - [What's next?](#whats-next)
- [做一个数码相框](#做一个数码相框)
    - [with a 10" 1920x1200 display](#with-a-10-1920x1200-display)
  - [We're going to need a display](#were-going-to-need-a-display)
  - [Mounting the display](#mounting-the-display)
  - [Mounting the Pi](#mounting-the-pi)
  - [Displaying the photos](#displaying-the-photos)
  - [Turning the display off](#turning-the-display-off)
  - [Wiring up a button and fan](#wiring-up-a-button-and-fan)
  - [What's next with the Pi Frame?](#whats-next-with-the-pi-frame)

<!-- /MarkdownTOC -->


[![](https://turbo.paulstamatiou.com/assets/gray-squircles-loader-3.gif)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09364-1500.jpg)

[![](https://turbo.paulstamatiou.com/assets/gray-squircles-loader-3.gif)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09366-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09460-750.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09460-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09465-750.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09465-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09147-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09147-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00108-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00108-1500.jpg)

## 它是什么？
###### 这就像一块Arduino吗？并不是。

虽然我听说Pi多年了，但是我从没好好看看它，而精神上只是把它看成给爱好者玩的某种类型的Arduino开发板。我真是大错特错了。

Arduino是一个开源的微控制器，有I/O引脚来控制其他电子产品。另一方面，Pi 仍然有那些I/O引脚 (GPIO)，但还有一个闭源的ARM **S**ystem **o**n a **C**hip (SoC)。然而，Arduino更适合连接到立即可用的模拟传感器，而Pi最适合那些使用I2C或者SPI通信的串行口传感器。

最快的Arduino运行在84MHz，而Pi 3运行在**1.2GHz**。

所以，当我说Pi是一个全功能的计算机时，我想表达什么呢？当然，那么便宜的东西实际上并不可用 …… 它是。装备**64位 1.2GHz四核CPU**，Model 3 B拥有**1GB内存，802.11n标准的Wi-Fi和Bluetooth**，以及大量的端口：4个USB，HDMI，microSD等等。这允许它运行为ARM芯片优化的操作系统，而且目前有相当多可用的操作系统：

*   [Ubuntu MATE](https://ubuntu-mate.org/raspberry-pi/ "Ubuntu MATE for the Raspberry Pi 2 and Raspberry Pi 3")
*   [Raspbian](https://www.raspbian.org/ "Raspbian is a free operating system based on Debian optimized for the Raspberry Pi hardware.")和Raspbian Lite (用于Pi的优化过的Debian)
*   [OSMC](https://osmc.tv/2016/02/raspberry-pi-3-announced-with-osmc-support/ "Raspberry Pi 3 announced with OSMC support")和[OpenELEC](http://openelec.tv/) (媒体中心操作系统)
*   [Pidora](http://pidora.ca/ "Pidora is a Fedora Remix optimized for the Raspberry Pi computer.") (用于Pi的Fedora)
*   [Arch Linux ARM](https://archlinuxarm.org/platforms/armv8/broadcom/raspberry-pi-3 "Arch Linux, a lightweight and flexible Linux? distribution that tries to Keep It Simple.") (为ARM电脑优化的Arch)
*   [用于单板计算机的Chromium OS](http://www.chromiumosforsbc.org/) (你可能知道它，它作为Chrome OS，用于Chromebooks)
*   [Windows 10 IoT Core](https://developer.microsoft.com/en-us/windows/iot/win10/noobs) (一个为物联网提出的[Windows 10变体](https://developer.microsoft.com/en-us/windows/iot))
*   即将推出：[官方Android支持](http://arstechnica.com/gadgets/2016/05/google-to-bring-official-android-support-to-the-raspberry-pi-3/) from Google

作为对比，更小的$5的树莓派Zero (如下所示的v1.3和照相机模块)拥有单核**1GHz CPU和512MB内存** —— 仍然足以运行以上操作系统 —— 带有microSD插槽，一个迷你HDMI和两个微型USB端口。然而，它缺少板载网络，因此如果你想上网，那么仍需要一个带有USB Wi-Fi的USB上网卡。
  
[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09417-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09417-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09429-750.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09429-1500.jpg)
    
有许多树莓派的竞争者。有些功能更强大，更贵。[PINE64](https://www.pine64.com/ "A powerful 64-Bit expandable single board computer ¨C Starting at just $15")带有2GB内存和千兆以太网，2GHz的四核Odroid XU4带USB 3.0和千兆以太网，以及明目张胆克隆树莓派的香蕉派<sup>[1](#footnote-1)</sup>带有SATA连接。然而，它们都无法打败**Pi大大规模开发者和爱好者社区**。这使得它容易找到你正构建的项目的支持，或者使用Pi特有的软件项目。

###### 电量消耗

噢，这个玩意不怎么吃电！不像更大的计算机，对于24*7运行它，你也许要三思而行，**树莓派运行成本基本没有**。这使得将Pi作为永远在线的Linux服务器或者连接硬件的项目的基础极具吸引力。

消耗取决于幸好，并且有很多方法可以更进一步的减少消耗<sup>[2](#footnote-2)</sup>，但一块Pi 3 Model B闲时大概消耗1.4W，并在负载的适合上升到3.7W

在加利福尼亚州，电费大概是每千瓦15美分，一整年负载运行一块Pi 3只需花费$5 (而闲时则只需每年$2)。一块Pi Zero将明显花费更少，因为它在负载时运行接近0.7W。为了便于比较，我的[4个磁盘的Synology NAS](https://paulstamatiou.com/storage-for-photographers-part-2/ "Storage for photographers Part 2")的运行大概花费_每月_$3.5。

微小的Pi电力需求为好玩的移动和嵌入式应用打开了大门，因为它可以通过一个正常的USB电池组供好一会电。

###### 用于教育的Pi

在它在开发者和爱好者之中疯狂流行之前，Pi意欲成为教育负担得起的电脑。这种努力仍在继续，树莓派基金会试图把Pi带进学校，现在，有了新的努力，像[Kano电脑包](http://us.kano.me/)和[Pi-Top](http://pi-top.com/ "Pi-Top")，它们都是成功众筹的结果。  
    
[![](https://turbo.paulstamatiou.com/assets/gray-squircles-loader-3.gif)](https://turbo.paulstamatiou.com/uploads/2016/05/pstam_pitop_kits.jpg)

[![](https://turbo.paulstamatiou.com/assets/gray-squircles-loader-3.gif)](https://turbo.paulstamatiou.com/uploads/2016/05/pstam_kano_pc_kit_photo.jpg)

[![](https://turbo.paulstamatiou.com/assets/gray-squircles-loader-3.gif)](https://turbo.paulstamatiou.com/uploads/2016/05/pstam_kano_pc_photo.jpg)

鉴于这些原因，对于Pi，有相当对面向教育的软件：用于音乐的[Sonic Pi](https://www.raspberrypi.org/learning/getting-started-with-sonic-pi/worksheet/)，[Scratch](https://www.raspberrypi.org/learning/physical-computing-with-scratch/worksheet/ "Physical computing with Scratch")，[Processing](https://www.raspberrypi.org/learning/introduction-to-processing/worksheet/)和[Python](https://www.raspberrypi.org/learning/python-intro/)，[Minecraft Pi](https://www.raspberrypi.org/learning/getting-started-with-minecraft-pi/worksheet/)，[还有更多](https://www.raspberrypi.org/resources/)。当然，很多这些都可以用任何一台计算机来完成，但是Pi的价位使得它更易获得。

###### Pi社区和资源

一旦你决定好了开始玩树莓派，那么你会很高兴的知道你并不孤单。有大量的Pi hacker社区来帮助你设想、构建和调试你的项目，并且有丰富的Pi特定的硬件来助力你的项目。

*   [树莓派subreddit](http://reddit.com/r/raspberry_pi/)
*   [树莓派论坛](https://www.raspberrypi.org/forums/)
*   [MagPi杂志](https://www.raspberrypi.org/magpi/)
*   [树莓派eLinux维基](http://elinux.org/RPi_Hub)
*   [adafruit](https://blog.adafruit.com/category/raspberry-pi/) - 一个在线电子商店，它有一个博客，上面有大量关于Pi的新闻和教程
*   [Pi故障排除指南](http://elinux.org/R-Pi_Troubleshooting)


## 有了Pi，你能做什么？
##### 灰常多。

#### 为什么我会有一个Pi？

在我看到了我一个朋友使用树莓派Zero来用他的Amazon Echo控制Sonos设置，我变得对Pi好奇起来。鉴于我有一个[Sonos系统](https://paulstamatiou.com/stuff-i-use/ "Stuff I use")，我想要用相同的设置启动。虽然我可以很容易的只把必要的节点服务器放在我的NAS上，但是我更想让那个盒子隐藏在我的路由器背后以获得安全性。我无所谓为一个简单的基于Pi的服务器转发端口。

那周，我订了一个Pi 3 Model B，并[设置了我自己的Sonos/Echo集成](https://github.com/rgraciano/echo-sonos "Amazon Echo integration with Sonos") —— 虽然我原本想要那个小小的Pi Zero，但它在那时已经售罄了。

<twitterwidget class="twitter-tweet twitter-tweet-rendered" id="twitter-widget-2" style="position: static; visibility: visible; display: block; transform: rotate(0deg); max-width: 100%; width: 500px; min-width: 220px; margin-top: 10px; margin-bottom: 10px;" data-tweet-id="732099150884392960"></twitterwidget>

<script async="" src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


然后，我开始思考，用Pi还能做什么……

#### 所以，你可以拿它做什么？

第一个以及最明显的路：**把它当成一台电脑使用**。它绝对不是你房子里最快的机器，但它可以很好的完成基本任务。虽然你可以将它挂到一个全尺寸鼠标、键盘和桌面显示上，但是对于更小的展示，还有大量的选择，包括触摸屏，这使得Pi适合各种项目。

但是请记住，它有那些I/O引脚。以Pi为中心，无数的硬件改装和物联网创意可以很容易地成为现实：

*   **数码相框**

    数码相框并非新玩意；你可能还记得多年前的那个蹩脚的版本，那时，你将SD卡插进去来播放你的照片。嗯，时代已经变了，现在，市场上有大量的连接数码相框。有$299的[Electric Objects EO1](https://www.electricobjects.com/)，$999+的[Klio](http://www.klioart.com/)和$445+的[Meural数字画布](https://meural.com/)。因此现在，大量的人都转向Pi来构建他们自己的版本，包括我（在这篇文章的最底部）。

    ![digital picture frame](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00138-750.jpg)

*   **魔镜**

    魔镜可能是当下现存最受欢迎的树莓派项目。它们带来了你对未来电影界面的回忆，并且很简单就可以建立起来，特别是有一个大社区在开发它们，并且[发布软件](https://github.com/MichMich/MagicMirror "Magic Mirror 2 the open source modular smart mirror platform")来驱动它们。简而言之：将一个运行着使用黑色UI，并展示新闻、天气或者任何对你来说重要的东西的全页面浏览器的显示器放在双面镜后，然后把它挂在家里的某个地方。网上有许多关于它的指南：[1](https://www.reddit.com/r/raspberry_pi/comments/3oktfu/magic_mirror_how_to/ "Magic Mirror how to")，[2](http://michaelteeuw.nl/post/83188136918/magic-mirror-part-v-installing-the-raspberry-pi&#10; "Magic Mirror: Part V - Installing the Raspberry Pi")，[3](http://innate.cc/ "Smart Mirror Mini Form Factor Update")，[4](http://blog.dylanjpierce.com/raspberrypi/magicmirror/tutorial/2015/12/27/build-a-magic-mirror.html)，[5](https://medium.com/@maxbraun/my-bathroom-mirror-is-smarter-than-yours-94b21c6671ba)。

    ![smart/magic mirror](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-pi-smart-mirror.jpg)
    ![smart/magic mirror](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-max-smart-mirror.jpg)

    也许是[最流行的魔镜构建](https://medium.com/@maxbraun/my-bathroom-mirror-is-smarter-than-yours-94b21c6671ba)。这是用一个Fire电视Android棒完成的，因为创造者在当时买不到Pi Zero。但是是相同的概念。
    
*   [使用motionEyeOS](https://github.com/ccrisan/motioneyeos/wiki "motionEyeOS is a Linux distribution that turns a single-board computer into a video surveillance system.")，运转你自己的运动检测Dropcam
*   [创建一个闹钟](https://georgecushen.com/spotify-alarm-clock-raspberry-pi-ubuntu-linux/ "Wake Up with Spotify Alarm Clock for Raspberry Pi")，让它播放来自Spotify的音乐
*   使用[PiVPN (OpenVPN)](http://www.pivpn.io)设置你自己的VPN服务器，当你不在家，并且连接到不安全的咖啡厅的Wi-Fi网络时，你可以用它。
*   构建你自己的[便携式Pi台式电脑](https://learn.adafruit.com/10-raspberry-pi-desktop)或者[平板](http://francescopochetti.com/pipad-build-tablet-raspberry-pi/)：

    ![Portable Pi Desktop computer](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-adafruit-pidesktop.jpg)
    ![Portable Pi tablet computer](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-pi-tablet-project.jpg)

*   [构建一个文档扫描仪](http://www.instructables.com/id/Raspberry-Pi-Based-Document-Scanner-With-Automatic/ "Raspberry Pi Document Scanner With Automatic Upload to Dropbox")，它自动上传文档到Dropbox。
*   连接OpenElec, OSMC, Kodi, [RasPlex](https://github.com/RasPlex/RasPlex/releases "Rasplex is a community driven port of Plex Home Theater for the Raspberry Pi")或者甚至是[Android TV](https://github.com/peyo-hd/device_brcm_rpi3 "Android TV for Raspberry Pi 3")的家庭影院电脑 ([见视频](https://www.youtube.com/watch?v=NMDf5thqoMk "Raspberry Pi 3 Running Android Tv OS and N64 , SNES emulator test"))。
*   创建一个["洋葱Pi" Tor代理](https://learn.adafruit.com/onion-pi/overview "Make a Raspberry Pi into a Anonymizing Tor Proxy")
*   使用[Pi Hole](https://pi-hole.net/)为你的整个网络设置广告拦截
*   使用[GoPiGo机器人套件](http://www.dexterindustries.com/GoPiGo/)编程你自己的基于Pi的机器人：

    ![gopigo robot kit](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-pi-gopigo-robot.jpg)

*   DIY植物自动浇水系统：[1](http://www.instructables.com/id/Automatic-Plant-Watering-and-Soil-Moisture-Sensing/ "Automatic Plant Watering and Soil Moisture Sensing"), [2](http://www.instructables.com/id/Raspberry-Pi-Irrigation-Controller/ "Raspberry Pi Irrigation Controller"), [3](https://hackaday.io/project/2711-autonomous-watering-system "Autonomous watering system"), [4](https://blog.serverdensity.com/automatically-watering-your-plants-with-sensors-a-pi-and-webhooks/)
*   弄个[个人语音助理](http://www.instructables.com/id/Raspberri-Personal-Assistant/?ALLSTEPS)
*   弄个[便携式游戏机](http://www.instructables.com/id/Raspberry-Pi-Portable-Games-Console/step6/Assembling-the-Console/), [便携式Pi街机系统](http://www.instructables.com/id/Build-your-own-Mini-Arcade-Cabinet-with-Raspberry-/)或者使用 [RetroPie](https://retropie.org.uk/)的[Game Boy Zero](https://www.raspberrypi.org/blog/game-boy-zero/ "Game Boy Zero")。

      ![Portable Pi gaming console](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-pi-portable-games-console.jpg)
      ![Porta Pi Arcade](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-pi-arcade.jpg)
      ![Game Boy Zero](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-pi-gameboy-zero.jpg)

*  使用[Lakka](http://www.lakka.tv/ "The open source game console") Linux发行版，把你的树莓派编程一个游戏控制台
*   让你的Pi运行一个[发照片的Twitter机器人](http://blog.bandwidth.com/actually-using-your-raspberry-pi-part-4-twitter-bot/)，照片来自于Pi照相机
*   使用[iSPRESSO](http://ispresso.net/ "iSPRESSO is an appliance modification comprised of Raspberry Pi computer, solid state relays, temp sensor, buttons and a display, a custom Printed Circuit Board, and custom linux shell scripts and a good bit of python code")，DIY Pi控制的咖啡机
*   使用Alexa语音服务，[DIY Amazon Echo](https://github.com/amzn/alexa-avs-raspberry-pi)：

    ![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-diy-alexa.jpg)

*   为Pi定制[通过RF模块设置无线电源插座wireless electrical outlets via RF modules](https://timleland.com/wireless-power-outlets/ "Wireless power outlets") 或hack你自己的[声控电源插座](http://www.instructables.com/id/Wireless-Multi-Channel-Voice-Controlled-Electrical/)：

    ![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-pi-wireless-voice-outlets.jpg)

*   创建你自己的[BitTorrent下载盒](http://www.howtogeek.com/142044/how-to-turn-a-raspberry-pi-into-an-always-on-bittorrent-box/ "How to Turn a Raspberry Pi into an Always-On BitTorrent Box")
*   做一个[树莓派服务器集群](http://makezine.com/projects/build-a-compact-4-node-raspberry-pi-cluster/):

    ![pi cluster](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-picluster.jpg)

*   让树莓派[根据Slack聊天命令开门](http://blog.tryolabs.com/2016/06/01/raspberrypi-slack-our-humble-contribution-to-the-offices-laziness/ "RASPBERRY PI + SLACK: OUR HUMBLE CONTRIBUTION TO THE OFFICE¡¯S LAZINESS")
*   对于你家里所有连接的设备和应用，把它当成[Home Assistant](https://home-assistant.io/)或者[pimatic](https://pimatic.org/ "pimatic is a home automation framework that runs on node.js. It provides a common extensible platform for home control and automation tasks")的服务器，或者[让它运行HomeBridge](https://github.com/nfarina/homebridge/wiki/Running-HomeBridge-on-a-Raspberry-Pi "Homebridge is a lightweight NodeJS server you can run on your home network that emulates the iOS HomeKit API.")，从而允许Siri控制更多的家庭自动化设备。
*   使用你的Pi来自己托管任意[免费的web应用](https://github.com/Kickball/awesome-selfhosted/blob/master/README.md)
**   DIY [Seenote](https://www.getseenote.com/) 数字便条 / 待办事项列表
*   [写一个Python web服务器](http://mattrichardson.com/Raspberry-Pi-Flask/)来从任意浏览器控制连接到Pi的GPIO引脚的设备
*   [通过一个特斯拉线圈来播放MIDI文件](https://www.youtube.com/watch?v=KhvExaTCXHA)
*   创建一个[高分辨率网络户外摄像头](http://blog.wq.lc/16-megapixel-outdoor-network-camera-on-the-cheap/ "16 Megapixel Outdoor Network Camera on the Cheap")：

    ![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-networked-outdoor-picam.jpg)

*   以及[许多](https://hackaday.io/list/3424-raspberry-pi-projects)更多的[项目](http://www.instructables.com/id/Raspberry-Pi-Projects/)...

#### Pi不大适合做什么？

For one, the Pi is a full computer and while it does not consume much power for a computer, it can still be overkill compared to an Arduino for simple hardware projects that don't require running an OS, GUI or networking.

The Pi is not without a few tradeoffs. Ethernet networking (which is a 100Mbps link) and disk access (if you attach any storage device via USB) all go through the USB bus. So any simultaneous Ethernet network traffic and storage device usage will be bottlenecked by the same bus. Wi-Fi does not route through the USB bus, but you will still typically only see around 20-40Mbps over Wi-Fi instead of the theoretical 150Mbps for 802.11n.

In short, the Pi is not quite the best for intense I/O and networking uses but can get the job done when speed is not mission critical.



## 开始

##### 操作系统安装



#### 你需要什么

Okay, let's get started! I'm going to assume that at the very least you'd like to just install some operating system on a Pi. First you'll need to pick what Raspberry Pi and accessories to buy ¡ª you didn't think you could get away with just buying the Pi itself did you?

But what Pi do you want? There's the larger and more powerful Pi 3 Model B and the tiny Pi Zero. I ended up getting both, but if the cost difference isn't a big issue for you I'd suggest starting with the Pi 3B. With integrated Wi-Fi as well as full-size HDMI and USB ports, it's almost a turn-key solution.

###### 零部件清单
* **Raspberry Pi 3 Model B** ($35)

买自：
[The Pi Hut](https://thepihut.com/collections/raspberry-pi/products/raspberry-pi-3-model-b), 
[MCM](http://www.mcmelectronics.com/product/83-17300),
[Adafruit](https://www.adafruit.com/product/3055), [Pimoroni](https://shop.pimoroni.com/collections/raspberry-pi/products/raspberry-pi-3),
[Amazon](http://www.amazon.com/Raspberry-Pi-RASP-PI-3-Model-Motherboard/dp/B01CD5VC92/ref=as_li_ss_tl?ie=UTF8&keywords=raspberry%20pi&qid=1464412585&ref_=sr_1_4&s=pc&sr=1-4&linkCode=ll1&tag=paulstamatiou-20&linkId=a9639c24281e760bc09a6a807c37bcd6)
        

你的Pi项目的核心。Unfortunately, Amazon themselves do not sell it (just third parties, often at a higher price) so it's probably best to purchase from one of the official partners above.

* [2.5A micro-USB power adapter](http://www.amazon.com/CanaKit-Raspberry-Supply-Adapter-Charger/dp/B00MARDJZ4/ref=as_li_ss_tl?ie=UTF8&refRID=1N7W6CHN64Q9EYQBEN21&linkCode=ll1&tag=paulstamatiou-20&linkId=34d578791be1daf5282c214b30895f06 "CanaKit 5V 2.5A Raspberry Pi 3 Power Supply / Adapter / Charger (UL Listed)") ($10)

Just about any micro-USB power adapter should work, but the more power hungry devices you connect to your Pi the more critical a good power supply becomes. Lots of Raspberry Pi issues that crop up end up being caused by a bad power source, so it's best not to mess around and get a good one. While I have a ton of USB chargers already, I opted not to get a powerful one used by many Pi folks without issue. Another option is this [Anker Dual USB charger](http://www.amazon.com/Anker-Charger-PowerPort-Foldable-iPhone/dp/B012WMWPJW/ref=as_li_ss_tl?ie=UTF8&qid=1464558272&sr=8-1&keywords=wall+charger+anker&refinements=p_89%3AAnker&linkCode=ll1&tag=paulstamatiou-20&linkId=5064fe7ae75d29f829a0f59701c87a32 "Anker 24W Dual USB Wall Charger") with 2 2.4A USB ports if you have other devices to run simultaneously, which I also ended up purchasing later on for my Pi Frame project below.

* [16GB microSD memory card](http://www.amazon.com/SanDisk-Extreme-MicroSDXC-Adapter-SDSQXNE-064G-GN6MA/dp/B013CP5F90/ref=as_li_ss_tl?ie=UTF8&keywords=64GB%20sandisk%20extreme%20microsd&qid=1464419155&ref_=sr_1_1&sr=8-1&linkCode=ll1&tag=paulstamatiou-20&linkId=0df538f214b6b8fcba4d469830c692a0 "SanDisk Extreme 16GB microSDHC UHS-1") ($10)

Anything larger than 8GB should be fine, but it's very important to get a fast card (Class 10 at least) from a reputable brand. Faulty or slow microSD cards are often a source of Raspberry Pi woes, so it's best not to skimp around in this department either.

I already had a bunch of large and fast microSD cards from my GoPro, so I just used one: a 64GB SanDisk Extreme. If you want something even faster you can opt for the SanDisk Extreme Plus or [SanDisk Extreme Pro version](http://www.amazon.com/SanDisk-Extreme-Memory-Speeds-Ready-SDSDQXP-064G-G46A/dp/B008HK1YAA/ref=as_li_ss_tl?ie=UTF8&qid=1464420526&sr=8-3&keywords=64GB+sandisk+extreme+plus+microsd&linkCode=ll1&tag=paulstamatiou-20&linkId=6b513c25ae825406b86d59cf941371f3 "SanDisk Extreme Pro 16GB MicroSDHC memory card").

* **You'll also need a keyboard, mouse, HDMI cable and a monitor or TV<sup>[3](#footnote-3)</sup>.** I'm assuming you probably already have these lying around. A USB keyboard and mouse are ideal so there's no issues getting it to connect during initial setup. As for the display, you really only need it for the setup process. After that you can install a VNC server and access it from any computer (if your intended Pi use doesn't require a display all the time).

###### Optional
* [Case for the Raspberry Pi 3](http://www.amazon.com/Raspberry-Pi-Case-Black-fits/dp/B00UW2G1BS/ref=as_li_ss_tl?s=pc&ie=UTF8&qid=1464423290&sr=1-3&keywords=raspberry+pi+3+case+canakit&linkCode=ll1&tag=paulstamatiou-20&linkId=f6330a1e3c8876702f340774b21c3170 "Raspberry Pi Case") ($9)

There are a million to chose from so I encourage you to search around though to see what's out there.

* [Tiny wireless keyboard/trackpad](http://www.amazon.com/FAVI-FE02RF-BL-Wireless-Keyboard-SmartStick/dp/B0090BTY8Y/ref=as_li_ss_tl?ie=UTF8&psc=1&redirect=true&ref_=oh_aui_detailpage_o02_s00&linkCode=ll1&tag=paulstamatiou-20&linkId=2035b5790be962b2b8dd82dac8bfccc5 "FAVI FE02RF-BL Mini 2.4GHz Wireless PC / Tablet Keyboard") ($32)

While I have a full-size USB keyboard and mouse, I ended up getting this tiny keyboard mouse combo for casual usage. Easy to hide away when not in use.

#### 什么操作系统？

There are lots of operating systems to choose from when it comes time to image your microSD card and start the installation process. The typical Raspberry Pi setup advice involves  [installing NOOBS](https://www.raspberrypi.org/documentation/installation/noobs.md) which makes it easy to select between Raspbian, Pidora, OpenELEC, OSMC, RISC OS and Arch Linux. Most newcomers select the Debian-based Raspbian Jessie, the official operating system for the Raspberry Pi.

I used Raspbian for a bit but then Ubuntu MATE 16.04 optimized for the Pi 3 came out. I went with Ubuntu MATE largely for a superficial reason ¡ª I like how it looked out of the box. ¡¥\_(¥Ä)_/¡¥

There are a few downsides compared to Raspbian ¡ª hardware acceleration seems to be a bit experimental, so I would not suggest this OS if you plan on using it for any Home Theater PC needs. Also not every Ubuntu package is ARM processor friendly so you may find less applications you can use at the moment on Ubuntu MATE. Also Firefox is pretty slow on Ubuntu MATE so you'll want to install another browser like Midori (or Chromium once they solve their current crash issue).

#### Imaging the microSD card

This part is usually harder than it needs to be. Typically you would download the image and use a command line tool like `dd` to manually image the card. You can't just drag the file to the microSD card.

I ended up going the hard route with the command line to image my card with Ubuntu MATE, but I'll be listing an easier option below.

*   Download the [Ubuntu MATE 16.04 LTS image made for the Raspberry Pi](https://ubuntu-mate.org/download/). You should get a file named `ubuntu-mate-16.04-desktop-armhf-raspberry-pi.img.xz`.
*   Uncompress the image. You can install the command line tool [unxz](http://tukaani.org/xz/) or use a GUI app like [The Unarchiver](http://unarchiver.c3.cx/unarchiver). I chose the latter: <pre class="terminal hide-label">unxz ubuntu-mate-16.04-desktop-armhf-raspberry-pi.img.xz
```
*   Plug in your microSD card (you'll probably get an SD card adapter with any card you purchased) and run <pre class="terminal hide-label">diskutil list
```
*   Identify the disk for your microSD card. This should be something like `/dev/disk4`, **not** `/dev/disk4s1` (The "s" part denotes the partition and we want the whole disk). Triple-check that this is the correct device and size. You may want to eject it and put it back in and verify that the item is removed from `diskutil list` when you do this. You don't want to overwrite the wrong disk!
*   If this disk is not listed as being FAT32, you will need to format it as DOS FAT32. You can do this in OS X by opening up Disk Utility, selecting the microSD card, clicking Erase and then selecting MS-DOS (FAT).
*   Unmount the disk, with the "X" being the number you just identified: <pre class="terminal hide-label">diskutil unmountDisk /dev/diskX
```
*   Now we get to start the actual imaging process. Verify the name and location of the downloaded .img file you extracted, and enter in the correct disk location (the /dev/diskX part) and run this: <pre class="terminal hide-label">sudo dd bs=1M if=~/Desktop/ubuntu-mate-16.04-desktop-armhf-raspberry-pi.img of=/dev/diskX
```
*   Alternatively you can try an even faster method by using the raw disk location instead of the buffered disk identifier. Just add an "r" before the disk like so: /dev/rdisk4. This may not work for everyone:
    <pre class="terminal hide-label">sudo dd bs=1M if=~/Desktop/ubuntu-mate-16.04-desktop-armhf-raspberry-pi.img of=/dev/rdiskX
```
*   **This will take a long time.** For my 64GB card it took 48 minutes using the first approach (not the rdisk method). You get no status from the dd command while it's working but you can press CTRL+T to get an update.
*   When completed you can pull out the card and put it in your Raspberry Pi! If you have any questions about this process or are not using a Mac, there are lots of more detailed guides online like [this one](http://elinux.org/RPi_Easy_SD_Card_Setup "RPi Easy SD Card Setup") and [this one](http://www.tweaking4all.com/hardware/raspberry-pi/install-img-to-sd-card/ "Raspberry Pi ¨C How to get an Operating System on a SD-Card").

If you're dying to just get started immediately you can [buy a microSD card with NOOBS preinstalled](https://www.adafruit.com/products/1583) or  [create your own](https://www.raspberrypi.org/documentation/installation/noobs.md) and install Raspbian Jessie or another OS instead of Ubuntu MATE.

**But there's an easier way for OS X users**: a new tool called [ApplePi-Baker](http://www.tweaking4all.com/software/macosx-software/macosx-apple-pi-baker/ "MacOS X - ApplePi Baker - Prep SD-Cards for IMG or NOOBS"). It's ridiculously easy to use. It automatically detected my microSD card and all I had to do was select the extracted img file. It did the rest and my card was ready to use after a few minutes.

![ApplePi Baker](https://turbo.paulstamatiou.com/uploads/2016/05/pstam_applepibaker.jpg)
  

#### 开机！

With your new SD card ready to go, slide it in your Raspberry Pi 3 and power it up. It should boot into the setup wizard for whichever OS you chose. This part should be a breeze. After a short while you'll be greeted with your new OS! Take some time to browse around and get it setup to your liking. But you'll first want to resize the file system. You'll find it in the Ubuntu MATE welcome dialog here:

[![Ubuntu MATE first boot](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-ubuntu-mate-first-boot.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-ubuntu-mate-first-boot.jpg)


#### Set a DHCP reservation

The majority of how I work with my Pi is actually over SSH and VNC rather than directly using it on a display. As such it's important that I can always find the Pi on my network at the same IP address. To do this I added a DHCP reservation with the AirPort Utility (I have an AirPort Extreme).

![AirPort Utility - DHCP reservation](https://turbo.paulstamatiou.com/uploads/2016/05/pstam_raspi_dhcp_reservation.jpg)
  
  

To do this you'll need to know the MAC address of your Raspberry Pi. You can do this by running `ifconfig eth0` if you're connected via Ethernet, or `ifconfig wlan` if connected via Wi-Fi.

Now you can always ssh into your Pi from any computer on your network (or from any computer if you also setup port forwarding) with the same local IP. The default username for Raspbian is `pi` but you will have set your own username for Ubuntu MATE.

```sh
ssh pi@10.0.1.46
```

#### Setting up VNC

Unless you have a dedicated display for your Pi, it will probably be annoying to constantly have it plugged into your TV. Installing a VNC server on the Pi and a VNC client on another computer will let you see and control the Pi with the window manager GUI instead of via SSH command line.

While either SSH'd in or directly on your Pi, install the Tight VNC server:

```sh
sudo apt-get update
sudo apt-get install tightvncserver
tightvncserver
```

Now you need to enable the VNC server on the Pi. While it's sufficient to just type `tightvncserver` to run it, you'll want to customize a few things to get a higher resolution display, especially if you're accessing it on your LAN. Running the following command will setup a virtual screen with resolution of 1920x1080. You can use any screen resolution you like here within reason:

```sh
stammy@rpi:~$ vncserver :1 -geometry 1920x1200 -depth 24

New 'X' desktop is rpi:1

Starting applications specified in /home/stammy/.vnc/xstartup
Log file is /home/stammy/.vnc/rpi:1.log
```

If you need to kill the server and change settings you can run `vncserver -kill :1`

The VNC server is now running on display **:1**. Download the [VNC Viewer client from realVNC](http://www.realvnc.com/download/) for your Mac and open it up. Type in the IP of your Pi on the network and append the :1 screen, like this:

![realVNC Viewer Mac](https://turbo.paulstamatiou.com/uploads/2016/05/pstam_vncclient_mac.jpg)
  

Type in the password for your Pi and you're set! You'll quickly notice it's not quite as snappy as if you were using your Pi with a physical display but it can get the job done.

![Ubuntu MATE via realVNC Viewer Mac](https://turbo.paulstamatiou.com/uploads/2016/05/pstam_ubuntumate_vnc_2000.jpg)

Ubuntu MATE 16.04 running on a Raspberry Pi 3 over VNC


Now you can fully control your Pi without the need for a hardware display. You will need to manually start the VNC server on the Pi whenever the Pi is rebooted. I'm usually logged in via SSH and rarely reboot so it's not a huge deal for me to run that line every now and then. But you can configure it to [automatically run at boot](http://elinux.org/RPi_VNC_Server).

But you might be thinking.. **doesn't OS X have it's own screen sharing utility?** Why do I need to install another app for this?

You're right! OS X is native VNC capable. To get this working we need to make the Pi discoverable via Bonjour and have it to broadcast it's new VNC support in a way that OS X can understand.

```sh
sudo apt-get install netatalk avahi-daemon
```

We're going to install `netatalk`<sup>[4](#footnote-4)</sup> which sets up the Apple Filing Protocol so we can also manipulate files on your Pi directly from the OS X Finder. If you're following this guide with Ubuntu MATE, you can leave off the `avahi-daemon` part as Ubuntu seems to come preinstalled with Avahi, the networking service discovery daemon.

At this point your Raspberry Pi should be visible and accessible on the local network with your Mac! However, to be able to see the screen sharing capability advertised here you'll need to modify a file:

```sh
sudo nano /etc/avahi/services/rfb.service
```

Paste this configuration below and save. We're telling the avahi daemon about RFB (remote framebuffer.. VNC basically) and what port it works on.

```xml
<?xml version="1.0" standalone='no'?>
  <!DOCTYPE service-group SYSTEM "avahi-service.dtd">
  <service-group>
    <name replace-wildcards="yes">%h</name>
    <service>
      <type>_rfb._tcp</type>
      <port>5901</port>
    </service>
  </service-group>
```


And then restart the daemon:

```sh
sudo /etc/init.d/avahi-daemon restart
```

You should now be see a new **Share Screen...** button. Click on that, type in your Pi password and you can now easily VNC into your Pi natively.


![Raspberry Pi visible in the OS X Finder](https://turbo.paulstamatiou.com/uploads/2016/05/pstam_finder_pi_ss.jpg)

Raspberry Pi visible in the OS X Finder

This approach uses netatalk/AFP for sharing your Pi on the network so it will only work for Macs. If you'd like to share files for Windows machines, you'd want to setup Samba sharing. Also, AFP is technically deprecated, so a future-proof solution would be to setup SMB2... but I've always had a heck of a time getting it to work flawlessly and AFP works great for now.



## 看看别人都用它做什么

##### 一起做些什么


#### Turning it into a NAS

Now that you have your Pi and its files completely accessible via the OS X Finder, wouldn't it be neat to add more storage to your Pi, share that volume and backup to it? While I personally don't use my Pi for this ¡ª I [setup a larger Synology 4-disk NAS system](https://paulstamatiou.com/storage-for-photographers-part-2/ "Storage for Photographers (Part 2) - How a 12TB Synology NAS changed my digital life") for my terabytes of photos ¡ª it [can be done](http://www.howtogeek.com/139433/how-to-turn-a-raspberry-pi-into-a-low-power-network-storage-device/ "How to Turn a Raspberry Pi into a Low-Power Network Storage Device") with [a Raspberry Pi](http://www.techradar.com/how-to/computing/how-to-make-a-mac-time-capsule-with-the-raspberry-pi-1319989 "How to make a Mac Time Capsule with the Raspberry Pi"). Just don't expect it to be fast.

![](https://turbo.paulstamatiou.com/uploads/2015/05/DSC01751-500.jpg)

#### Storage for Photographers (Part 2)

How a 12TB Synology NAS changed my digital life

  
](https://paulstamatiou.com/storage-for-photographers-part-2/ "Storage for Photographers (Part 2)")

There are many small and energy efficient storage options from USB sticks to external laptop and desktop hard drives and SSDs. Keep in mind that **the Raspberry Pi 3 only has USB 2.0** so you won't get the entire speed benefit of an SSD. And for the smaller drives that don't require their own power source, you will still actually want to plug it into a powered USB hub before plugging into the Pi to make sure you don't cause stability problems by stealing too much juice from the Pi itself.

The Raspberry Pi ended up getting so popular that Western Digital actually created a more efficient drive _just_ for the Pi. Called the [WD PiDrive](http://wdlabs.wd.com/products/wd-pidrive-314gb/) it's a 314GB hard drive (314GB as in ¦Ð, get it?) with a native 7mm USB connection. Unfortunately, it's pricey for how many gigabytes you get.

As you might expect with a real NAS, **you can connect the Raspberry Pi to a UPS battery backup**: either a [real desktop-class UPS](https://melgrubb.com/2014/09/05/raspberry-pi-home-server-part-15power-failures/ "Raspberry Pi Home Server: Part 15¨CPower Failures") or a [tiny add-on board like this](http://www.modmypi.com/raspberry-pi/breakout-boards/pi-modules/ups-pico "UPS PIco - Uninterruptible Power Supply & I2C Control HAT") or [this](https://www.pi-supply.com/product/pi-ups-uninterrupted-power-supply-raspberry-pi/ "Pi UPS ¨C Uninterrupted Power Supply for Raspberry Pi") that sits on top of the Pi and has a cell-phone battery with enough battery to let your Pi run for a few hours and safely shut down.

If you don't need the data portion of a real UPS system (being able to tell your Pi it's now running on battery and should shut off soon), you can just get a [good USB battery pack](https://www.amazon.com/Powerful-10000mAh-Anker-PowerCore-Technology/dp/B013HSQXZC/ref=as_li_ss_tl?srs=2528932011&ie=UTF8&qid=1464849921&sr=8-4&keywords=anker+powercore%2B&linkCode=ll1&tag=paulstamatiou-20&linkId=e2f04b6418a0bbb1e5b7890210d64b70 "Anker PowerCore+ 10050 Premium Aluminum Portable Battery Charger ") that you always keep plugged in. Make sure you get a reputable one with the appropriate circuitry to support pass-through charging or build your own with [this PowerBoost circuit](https://www.adafruit.com/products/2465 "PowerBoost 1000 Charger - Rechargeable 5V Lipo USB Boost @ 1A - 1000C") and a 3.7V LiPo battery.

You can also setup your Pi to be a [Time Machine backup destination](https://pwntr.com/2012/03/03/easy-mac-os-x-lion-10-7-time-machine-backup-using-an-ubuntu-linux-server-11-10-12-04-lts-and-up/ "
Easy Mac OS X (Mountain) Lion and Mavericks 10.7, 10.8 and 10.9 Time Machine backup using an Ubuntu Linux server [11.10, 12.04 LTS and up]") on the network and you can even [install CrashPlan](https://gist.github.com/n8henrie/37d96807e31d94ca0464 "Set up CrashPlan on Raspberry Pi (Raspbian Jessie)") to have all your Pi's files backed up to the cloud as well. But be warned it won't be particularly fast.

###### Mounting and sharing a USB drive

Regardless of what drive you get, you'll want to mount it and have netatalk share it so your Mac can access it. While Ubuntu MATE has some automounting stuff, I prefer to disable it and proceed the old-fashioned way. On Ubuntu MATE (not via SSH, you technically can with gsettings but it didn't work for me), type `dconf-editor` in the terminal to open the GUI dconf editor. Browse to `org.gnome.desktop.media-handling` in the left pane and uncheck `automount` and `automount-open`. Reboot.

*   Prepare your USB device by formatting it to ExFAT if it's not already. If you're not using Ubuntu MATE on your Pi, you will want to install this package to add support for ExFAT mounting: `sudo apt-get install exfat-fuse`
*   Plug in your USB device and type in `sudo blkid`:

    <pre class="terminal hide-label">stammy@rpi:~$ sudo blkid
[sudo] password for stammy:
/dev/mmcblk0: PTUUID="580a66ff" PTTYPE="dos"
/dev/mmcblk0p1: SEC_TYPE="msdos" LABEL="PI_BOOT" UUID="4442-965D" TYPE="vfat" PARTUUID="580a66ff-01"
/dev/mmcblk0p2: LABEL="PI_ROOT" UUID="e440adac-fcf9-4b68-9f94-6bfd030f60b3" TYPE="ext4" PARTUUID="580a66ff-02"
/dev/sda1: UUID="9C33-6BBD" TYPE="exfat"
```*   We're looking for the UUID of the USB device so we can mount the drive based on it's unique id instead of it's location, so it will always mount flawlessly. In this case my drive is the last line with type `exfat`, since it is a large 128GB SDXC card that I plugged in (not the micro-SD card but a USB card reader and SD card just to test this out). You can verify that this is the correct line item by ejecting and running the `sudo blkid` command again to see that the line vanishes.
*   Create a new directory where we will mount the drive and then have your user account own it. If you are using the default pi username it will just be the following:

    <pre class="terminal hide-label">sudo mkdir /usb-drive
sudo chown -R pi:pi /usb-drive
```*   Now for the real work, we need to add a line to our file systems table file. It is _very important_ that this is typed correctly with the correct UUID and filesystem type for your drive. If this is incorrect your Raspberry Pi will get stuck at boot and you won't even be able to SSH in, you'll have to enter emergency mode to fix the file.

    <pre class="terminal hide-label">sudo vim /etc/fstab
```
          ![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-edit-fstab-for-usb-device.jpg)
    *   Now add this line to the bottom of your fstab file, making sure to replace XXXX-XXXX with the UUID from the blkid command earlier and using the correct file system type (vfat, exfat, etc):

    <pre class="terminal hide-label">UUID=XXXX-XXXX  /usb-drive     exfat   auto,nofail,uid=1000,gid=100,umask=0002,rw 0 0
```*   Now we'll add this new drive as an item for netatalk to share. You'll need to edit this file:

    <pre class="terminal hide-label">sudo vim /etc/netatalk/AppleVolumes.default
```
          ![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-edit-netatalk-volumes.jpg)
    *   Scroll to the very bottom and add this line representing the new persistent mount point for your USB storage device:

    <pre class="terminal hide-label">/usb-drive      "USB Drive"
```*   Reboot. Your drive should now be shared and accessible on the network!

#### Benchmarking, overclocking and cooling

While I won't dwell on this too much, it's possible to overclock your Raspberry Pi to achieve  higher CPU and RAM speeds. Why would you want to overclock your Pi? You might want to squeeze some extra performance from a CPU-limited process like video transcoding or the like. Or you might just want to see if you can overclock it for fun.

There's a [simple Pi benchmark script](https://github.com/aikoncwd/rpi-benchmark) that includes overclocking documentation so you can run before and after your overclock to measure benefits. There are also quite a few guides on the topic if you're really curious: [Raspberry Pi 3 Overclocking](http://www.jackenhack.com/raspberry-pi-3-overclocking/ "Raspberry Pi 3 Overclocking") and [Pi 3 Overclocking, Stability Testing & Cooling](https://www.raspberrypi.org/forums/viewtopic.php?f=63&t=144391&p=960651 "Pi3 Configuration, Overclocking, Stability Testing & Cooling"). However, **do not attempt overclocking** without adding additional cooling to the Raspberry Pi CPU and RAM.

###### Heatsinks

Actually, even if you don't overclock your Pi but you put it through its paces or place it in a closed case you will still benefit from adding some heatsinks to your Pi. They're not absolutely required, but I sleep better at night knowing my Pi is nice and cool. Also, I used to overclock and watercool all my computers over a decade ago so I can't help but enhance my Pi's cooling situation.

While you could go a bit extreme with a [massive heatsink and fan](https://www.youtube.com/watch?v=WfQMLInuwws "Raspberry Pi 3: More Extreme Cooling"), peltier setup or even a custom watercooling contraption, it's all bound to be overkill unless you are doing some crazy hardware voltage modifications to your Pi. I'm assuming that's not you and just a simple heatsink will do.

There are 2 main chips to consider for cooling: The primary SoC that's on the top of the board and the RAM chip underneath the board. There is also a smaller chip that gets a bit warm near the USB ports and that's the USB and Ethernet controller. I did a bit of research and eventually ended up getting some high-quality (albeit pricey) copper [RAM heatsinks](https://www.amazon.com/gp/product/B002BWXW6E/ref=as_li_ss_tl?ie=UTF8&psc=1&linkCode=ll1&tag=paulstamatiou-20&linkId=2a12faeae15ac183f4fafcda6e879b2d "ENZOTECH Memory Ramsink BMR-C1") and [MOSFET heatsinks](https://www.amazon.com/gp/product/B004CLDIHK/ref=as_li_ss_tl?ie=UTF8&psc=1&linkCode=ll1&tag=paulstamatiou-20&linkId=9eec0bca23273108f9bfc933cc94d134 "Enzotech MOS-C1 MOSFET Heatsinks - 10 Pack") from ENZOTECH. The RAM heatsinks fit well over the SoC and the RAM, but you can also do the same by just placing 4 of the tiny MOSFET heatsinks on each chip.

  
[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09376-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09376-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09390-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09390-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09392-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09392-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09397-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09397-1500.jpg)
    
  



Unfortunately, they are a bit tall so having this on the underside of your Pi can limit your case mounting options. I also got a set of [low profile aluminum heatsinks](https://www.amazon.com/gp/product/B00A88DVTG/ref=as_li_ss_tl?ie=UTF8&psc=1&linkCode=ll1&tag=paulstamatiou-20&linkId=28ccdcc3c6502e804397c1590f214fc5 "LinuxFreak brand Aluminum Heatsink set for Raspberry Pi - Set of 2 Heat Sinks") that would work as well.

With my Pi now heatsink'd up I can let it handle just about any task and not worry about it overheating. You can also get a 5V fan to blow over the heatsinks and [use a script to only spin it up when it gets hot](https://medium.com/@edoardo849/how-to-control-a-fan-to-cool-the-cpu-of-your-raspberrypi-3313b6e7f92c "How to control a fan to cool the CPU of your RaspBerryPi").



## 使用I/O引脚

##### Pi electronics 101



Time for the fun part ¡ª tinkering with some electronics and the Pi's General Purpose I/O (GPIO) pins!

The Pi 3 ¡ª and Pi Zero, just without the connector pins ¡ª has 40 pins, but 26 of them are GPIO pins accessible to program. The other pins are ground (8), 5V (2), 3.3V (2) and reserved EEPROM pins (2).

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09453-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09453-1500.jpg)
    
  


There's a lot you can do with these pins. You can program any GPIO pin on or off (high or low) which provides 3.3V at a max current of around 50mA<sup>[5](#footnote-5)</sup>. You can also have your program accept input on these pins, such as when connecting a sensor, switch or other device.

However, 50mA off a 3.3V pin is really not a lot to power anything. If you need a bit more juice, there's the two 5V pins which come straight from the same power source powering the Pi, minus however much current the Pi consumes. So with a 2.5A power supply, subtract about 1A for general Pi use with some small devices plugged in and you can probably consume a max of 1.5A off those pins.

Instead, the GPIO pin output should just be used as a signal to switch something on but not power it. If you're going to need to power anything more than an LED, you're going to want to use an external power source and something like a transistor, [FET](http://www.robertcudmore.org/blog/?p=181 "Switching 12vdc on and off with a Raspberry Pi") or relay. There are tons of [electronics and Raspberry Pi guides](http://elinux.org/RPi_GPIO_Interface_Circuits "Raspberry Pi GPIO interface circuits") that cover all of this in detail, but now that you have a primer let's build something simple!

#### Parts

To make things easier for tinkering I purchased a [breadboard](https://www.adafruit.com/products/239 "solderless breadboard") so I could prototype little circuits without soldering. I also purchased a [T-Cobbler](https://www.adafruit.com/products/2028 "Pi T-Cobbler Plus - GPIO Breakout") that directly connects the Raspberry Pi pins to the breadboard. And of course various LEDs, wires, transistors, resistors and buttons to play with.

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09490-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09490-1500.jpg)
    
[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09486-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09486-1500.jpg)
    
  


#### Setup & powering an LED

First, attach the T-Cobbler board to the breadboard and then to the Pi with the ribbon cable. Make sure the ribbon cable's white wire connects to the corner side of the Pi. Aside from making it easy to physically connect things with the breadboard, the T-Cobbler board also provides you with the GPIO pin labels.

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09497-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09497-1500.jpg)
    
  


It's important to note how the breadboard works so you don't short anything out by accident. On the breadboard, the outer rails (marked by blue and red lines) run the length of the board. These are the power rails where you can connect GND to the **-** blue line with a jumper cable and 5V to the **+** red line (or even an external power source instead of from the Pi) and be able to access those lines throughout the length of the board.

The lines between those outer rails ¡ª the terminal strips ¡ª run perpendicular. So if you want to plug into GPIO pin #17 for example, you would put a jumper cable anywhere to the left of that pin, but not the blue or red outer rails. You can read more about [breadboards in this article](https://learn.sparkfun.com/tutorials/how-to-use-a-breadboard "How to Use a Breadboard").

Now let's just try to power an LED. Nothing special for now, we'll just connect the LED to the 5V source that comes directly from the power adapter connected to the Pi.

But first we should be cautious about how much voltage and current we supply to the LED; we don't want to burn it out. In my case I got a [25mA 3.4V UV purple LED](https://www.adafruit.com/product/1793 "UV LED"). I used [this LED resistor calculator](http://ledcalc.com/) to find out exactly what I needed. It said 68?, but I could only find a larger 220? resistor at the time. No biggie, it just won't be as bright.

Resistors don't have polarity so it doesn't matter which way they are used. LEDs do however. There's the cathode (**-**) that is easy to identify as it's the shorter wire from the LED and there is a flat side of the LED identifying the cathode. The longer wire coming from the LED is the anode (**+**). This can be done various ways with the breadboard, but I started by placing a jumper from GND to the blue power rail and then plugging the LED into the power rail, making sure that the short end of the LED (cathode) stayed on the negative blue rail. Then I connected the resistor from the 5V terminal strip on the breadboard that comes from the Pi to the positive red power rail. Now our LED will light up!

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09516-750.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09516-1500.jpg)
      
    
  

#### Control the LED

Okay now that we've got the basics out of the way, let's actually control the LED instead of having it on continuously. To do this we need to power it from a GPIO pin instead of the 5V line. Since the GPIO pin is 3.3V and our LED has about the same forward voltage, we can ditch the resistor now. As a word of caution, we can get away with powering the LED from the GPIO pin since it won't draw much current. Don't try to power anything other than an LED off a GPIO pin or it might damage your Pi.

This time we just need to connect the LED anode (the long wire) to GPIO pin 17 and the cathode to GND. You can see how I used jumpers to get this done in the photo below. As for why I used GPIO pin 17: no reason, you can pick any GPIO pin and program it below.

Then we SSH into our Pi ¡ª or directly on it if you've got a display hooked up or are using VNC ¡ª and open up the python interpreter so we can use the python GPIO library. First we need import that library. It should be included in your OS automatically if you're using Ubuntu MATE or Raspbian Jessie.

```
stammy@rpi:~$ python
Python 2.7.11+ (default, Apr 17 2016, 14:00:29)
[GCC 5.3.1 20160413] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import RPi.GPIO as GPIO
>>> GPIO.setmode(GPIO.BCM)
>>> GPIO.setup(17, GPIO.OUT)
>>> GPIO.output(17, True)
>>> GPIO.output(17, False)
```

Then we need to set the pin mode. This determines what we mean when we provide a GPIO pin number: either by the physical pin position (`GPIO.BOARD`), or by the Broadcom pin number (`GPIO.BCM`). The latter is listed on the T-Cobbler for us so I provided the `GPIO.BCM` mode. Then we setup() each GPIO pin to be used and tell the Pi if it will be used for input or output (`GPIO.IN` or `GPIO.OUT`). We'll go with output since we just want to power the LED instead of listen for an input signal.

And now we can finally **we can turn the LED on and off** with these commands: `GPIO.output(17, True)` and `GPIO.output(17, False)`. Go ahead and try it a few times! and think about how we're talking to a tiny computer over a network and having it control our electronics for us. Pretty neat. Despite everything being an app or connected device these days it's still fun to be able to control something simple like this.

  
[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09519-750.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09519-1500.jpg)
      
    
  

This [python GPIO library](https://sourceforge.net/p/raspberry-gpio-python/wiki/BasicUsage/) provides the essential foundation for interfacing with the i/o pins on your Pi. There is also a popular C library called [WiringPi](http://wiringpi.com/pins/) and one called [gpiozero](https://github.com/RPi-Distro/python-gpiozero), which provides higher-level functionality for quickly setting up common output and input components like LEDs, buttons, sensors and more. Definitely worth checking out for more advanced items.

And while I just wanted to show the basics using the GPIO library in the python interpreter, you can of course write them as a .python file and run it as you please. There's a whole lot more you can do when actually writing your python programs.

#### Using a transistor

Remember how I mentioned that we don't really want to drive more than a simple LED directly off the GPIO pins? Since the GPIO pins can't provide much voltage or current <sup>[6](#footnote-6)</sup> you should find other means to trigger and power your connected components.

One way to control your components is with a transistor. I'm using a basic BJT (bipolar junction transistor) NPN (negative-positive-negative) transistor, the PN2222. It can handle up to a peak of 40 Volts(!) at 1A, more than enough to drive something like a small motor, various lights, and so on. **They only need a tiny amount of current to flip on, thus saving your GPIO pins from doing the heavy lifting.**

Typically you should also use a resistor between the transistor and the GPIO pin to reduce the current it draws. There is a bit more to it ¡ª like how the amount of current applied to the base can vary the collector current as the transistor acts as a simple amplifier, up to a "saturation" point ¡ª but that's best left for [some extra reading if you're curious](https://learn.sparkfun.com/tutorials/transistors/all "Transistors").

![NPN PN2222 Transistor pinout](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-npn-transistor-schematic2.png)
  

Okay so we have 3 pins on our transistor: emitter, base, collector (EBC). It's important to note the exact pin layout as it varies by transistor. If you're looking at the flat side of the PN2222 NPN transistor, we have EBC from left to right. The middle base pin is what actually causes the transistor to trigger, making the normally open emitter and collector closed. This is the opposite behavior from a PNP transistor, with [some extra nuances](http://www.learningaboutelectronics.com/Articles/Difference-between-a-NPN-and-a-PNP-transistor "Difference Between an NPN and a PNP Transistor").

![transistor schematic](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi-3-transistor-led-diagram.jpg)

While this circuit works for this very simple use, technically you would want to add a pull-down resistor from the base to ground to get it to switch off faster [and for other reasons](http://electronics.stackexchange.com/questions/56010/why-pull-base-of-bjt-switch).
  

We want to connect the base pin to our GPIO pin 17 along with a resistor. I wanted to use larger resistor but only had my same 220? resistors, so I put a few in series. This introduces enough resistance to lower the current used by the transistor on the GPIO pin but have enough juice to saturate the transistor into a fully on state.

Current flows from the collector to the emitter, so I connected a jumper cable from GND to the emitter and then placed the LED<sup>[7](#footnote-7)</sup> between the Pi's 5V pin and the collector. Then connect the LED anode to the 5V line and the cathode (the flat side of the LED) to the transistor's collector with that same 220? resistor in between.

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09530-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09530-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09525-750.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09525-1500.jpg)
      
I forgot to add a resistor from in front of the LED, so it was powered by 5V directly here. No issues but you don't want to be running it like this for more than a bit.

Now you can run the same `GPIO.output()` commands from earlier to switch the LED on and off, via the NPN transistor! This is a safer way to control small devices off your GPIO pins.

But what should you do if you need something even heavier duty for controlling a larger motor, high-wattage LED or anything demanding more than an amp of continuous current? You can use a beefier transistor like a heavy duty BJT, darlington or a MOSFET<sup>[8](#footnote-8)</sup>. There are various differences between them that I'm not experienced enough to comment on. But I do know the about the next option: relays!

#### Use a relay

Lets get a relay working for our last experiment. There are several kinds from electromechanical to solid state and more, but the concept is the same: it will close or open any circuit you connect it to when triggered. The difference between our earlier BJT NPN transistor is that it's only on or off (no amplification characteristics) and the circuit you switch on is entirely separate to the switching logic (at least with electromechanical relays) so most relays can support much larger voltages while not interacting with your low voltage circuit. Okay I'm rambling a bit but basically this is what you want if you want to run an even larger device, like a 120V lamp for example (assuming your relay is rated for that).

I ended up getting an electromechanical relay that was [preassembled to just need a control signal](https://www.amazon.com/gp/product/B00VRUAHLE/ref=as_li_ss_tl?ie=UTF8&psc=1&linkCode=ll1&tag=paulstamatiou-20&linkId=097665a19e82d17d0bcccfd7229da356) to make it easy to get started ¡ª it wires up just like our previous transistor. It bakes in the necessary transistor and some safeguards like a flyback spike protection diode for the relay coil. Again, there's [more to read about on the EE side](http://electronics.stackexchange.com/questions/100134/why-is-there-a-diode-connected-in-parallel-to-a-relay-coil "Why is there a diode connected in parallel to a relay coil?") here if you're interested.  
    
[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09540-750.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09540-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09544-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09544-1500.jpg)
      
    
  

#### What's next?

My intent with this section was to fall somewhere between informative overview and step-by-step. There's obviously a ton of detail I flew past and I only showed the most basic examples. But now you get to have fun trying out new circuits on your own! I love this stuff as you can probably tell.. I took a few EE classes in college long ago.

While we only touched on using a single GPIO pin as output, there's lots to learn about using them for input as well. Here's some more reading about Raspberry Pi GPIO electronics to keep you busy:

*   Learn more about [circuits at Khan Academy](https://www.khanacademy.org/science/electrical-engineering/ee-circuit-analysis-topic)
**   [Connect a button](http://razzpisampler.oreilly.com/ch07.html "Connecting a Push Switch with Raspberry Pi") and use it as an input
*   [Control a servo motor](http://razzpisampler.oreilly.com/ch05.html#SEC7.12)
*   Hook up a [motion sensor as an input](https://www.raspberrypi.org/learning/parent-detector/worksheet/) and record a video when motion is detected.
*   Use an iOS app and Pi server like [Cayenne](http://www.cayenne-mydevices.com/ "Easy IoT for Raspberry Pi") or [MyPi](https://itunes.apple.com/us/app/mypi-control-your-raspberry/id1098156642?mt=8 "MyPi - Control your Raspberry Pi GPIO") to control your connected relays, sensors and other GPIO devices on your phone or the web. Or do it yourself with the self-hosted [Pi GPIO web interface](https://github.com/stuart-thackray/pi_gpio_web/ "Raspberry Pi GPIO Web Interface")



## 做一个数码相框

##### with a 10" 1920x1200 display

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00194-1280.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00194-1500.jpg)
    
  


I wanted to do a real project with this Raspberry Pi. Something I would actually use around the house. I had narrowed it down to either a smart mirror or a digital photo frame.

While there is definitely a strong cool factor with smart mirrors, I soon realized just would not get real value out of it. There's not much information I care about enough to be on an ambient display that I can't interact with.

Weather, news headlines, email subject lines? The majority of those require me to pull out my phone to actually learn more. And I wouldn't put anything too personal on there that guests might see (like work emails). There might be something there in the future when it's easy to setup eye tracking and have the device detect when my eye is dwelling on and open a browser to learn more about that news headline, or read it out loud to me. But for now I already check my phone a million times per day and I already get my news read to me with the Amazon Echo every morning.

A digital photo frame on the other hand has an aesthetic value for me and given that I'm a [photographer](https://paulstamatiou.com/photos) as well, it's right up my alley. It will fit in nicely with the other framed photos and travel books throughout my house.

#### We're going to need a display

Now the obvious question ¡ª where was I going to put this and what kind of display would I need? There's also the question of whether I wanted a touchscreen display. I decided to keep it simple, no touchscreen. Though I did initially think it would be neat to swipe between photos. Going with a regular display also makes mounting the display easier as I could put it behind glass.

As I began searching for displays I quickly realized that it was hard to find a high-resolution display. The majority seemed to be somewhere between [800x480](https://www.adafruit.com/products/2718) (like the official Raspberry Pi 7-inch touchscreen display) and 1280x800. I even found some folks using iPad 2 displays (9.7-inch 1024x768). Not bad but not terribly appealing to me. A low resolution screen would not do my photos justice, especially as I have gotten used to seeing them on high PPI displays.

I also knew I would be mounting this in a frame and put it on my bookshelf. While disassembling a regular desktop computer monitor is a popular route, I wanted something smaller and didn't want anything that required a bulky 120V cable and adapter. I needed a display that could be powered via a USB cable and accept an HDMI input.

The maximum resolution the Pi can support is 1920x1200 so I wanted to get as close as possible in something around the 10" to 12" size. After lots of searching, I eventually stumbled across [this 10-inch beauty from Chalk Elec](http://www.chalk-elec.com/?page_id=1280#!/10-FullHD+-LCD-with-HDMI-interface/p/41737268/category=3094859 "10-inch FullHD+ LCD with HDMI interface"). Yes, it's expensive at $140 USD for just the panel, but it was just what I was looking for. It's 1920x1200 and at 10 inches this makes it a **ridiculous 226ppi panel**. That's a tad better than the 218ppi 5K iMac.

I tested the display and everything was working great! I was just amazed at the resolution. It would have been overkill if I was going to use this for anything other than a photo frame. I would  have had to use a lower resolution setting to make it comfortable to use for longer periods.



  
[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09125-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09125-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09150-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09150-1500.jpg)



[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09147-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09147-1500.jpg)
    
  


The display's controller board can accept power from either a dedicated wall adapter or from USB. I wanted to go with USB so I followed their instructions and soldered a jumper (0¦¸ resistor) where it says R12 to be able to accept USB power. And while I was tinkering with it I also decided to put heatsinks on the two LVDS chips as they got very hot when in use.

I was a bit nervous working with this board. The cable running to the display extremely short and delicate, whereas the huge HDMI cable is rather inflexible and heavy. I was quick to use some electrical tape to prevent the flat cable from getting plucked out and torn.



  
[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09379-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-raspberry-pi3-DSC09379-1500.jpg)

  


#### Mounting the display

Now that I had my functioning display, I needed to figure out how to mount it. I decided I wanted to go with a real photo frame with matting. I planned on putting it on my bookshelf. The original thought of hanging it on my wall was intriguing but hiding the cables would be tricky and I didn't feel like drilling into my wall and installing an up-to-code flush electrical outlet behind the mounting location.

I went to a local frame store and **picked up a 9" x 12" frame** with matting. I carefully measured the viewport of the display and cut the sides of the matting to add room. This worked but the matting had a beveled cut so my cuts don't look natural . Eventually I will have the frame store custom cut the matting in the size I need, but this will do for now.


[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC09815-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC09815-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC09821-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC09821-1500.jpg)


[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC09826-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC09826-1500.jpg)
    
  


I then placed the display behind the matting. Fortunately, the bezel of the display came with a sticky foam so I just peeled that back and placed it on the matting. After the display was in place, I ran a thin line of hot glue around the sides.

I cut peices of cardboard and put them alongside the display to reduce pressure on the display when I put the back of the frame on. I then cut a space for the cables and the controller board to peek through as well. I'm surprised how easily this part all came together. It actually looks pretty decent! Well, everything except for the hacky way I cut the frame backboard.. in hindsight I could have probably just cut a hole for the display's cable only instead of having it be against the display. I was just a bit concerned with moving that cable as it's pretty short and very fragile.



  
[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC09832-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC09832-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC09840-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC09840-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC09841-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC09841-1500.jpg)



[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00010-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00010-1500.jpg)
    
  


#### Mounting the Pi

With the back of the frame in place, I still had a bit of room to tuck the Pi away out of sight. The HDMI cable was pretty thick so that pretty much held the Pi in place on its own. I hot glued the cables in place to be safe. Then I added some small 3M plastic hooks to keep the Pi in place ¡ª one hooks into the Ethernet port and the other I attached with a small wire to the corner mounting hole.




[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00040-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00040-1500.jpg)
    
  


Now it was time to give it a test boot. I was powering the Pi and display off separate USB sources so that I had more than enough juice to spare for the Pi. Instead of having to find two USB power adapters, I opted for [this great Anker dual USB adapter](https://www.amazon.com/gp/product/B012WMWPJW/ref=as_li_ss_tl?ie=UTF8&psc=1&linkCode=ll1&tag=paulstamatiou-20&linkId=cdcfeeb2de9fca9e548e87beea54255e "Anker 24W Dual USB Wall Charger PowerPort 2") which provides 2.4 amps per USB port.




[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00051-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00051-1500.jpg)
    
  


With the Pi Frame now up and running **I had two challenges**:

*   Figure out how to display my photos in fullscreen
*   Figure out how to turn the display and backlight off when needed.

#### Displaying the photos

The traditional route for a digital photo frame is to have a synced photos folder with something like Dropbox or rsync and then use a fullscreen image viewer like `feh` or `fbi`. Both are rather no-frills setups.

I wanted to see if I could bypass the photo syncing portion of this and **just use the Google Photos website**. I've already [professed my love for Google Photos](https://paulstamatiou.com/storage-for-photographers-part-2/) so it would be great to use it here as well.

I would only need to have a browser that could display in fullscreen or kiosk mode. I decided to give Firefox a try, only because there is currently an issue with the latest Chromium crashing on Ubuntu MATE. There are also dedicated kiosk browsers like [kweb](https://www.raspberrypi.org/forums/viewtopic.php?t=40860) and [FullPageOS](https://github.com/guysoft/FullPageOS).

Fortunately, **Firefox's native fullscreen mode did the trick**. I just had to log into Google Photos, select an album and hide the mouse in the corner. To get photos to be the perfect aspect ratio to fill the 1920x1200 display and not have pillar or letterboxing, I created a new album and **uploaded some of my travel photos cropped to a 16:10 aspect ratio**. It worked perfectly!

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00108-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00108-1500.jpg)
    
  


I was able navigate between photos in the selected album using the left and right arrow keys. Google Photos also recently created a slideshow mode that I tested out. However, there were two issues with the slideshow mode to address. The first is that it puts a control box in the bottom left corner that does not hide. I ended up using the Stylish Firefox plugin to inject CSS to hide that box.

The other issue with slideshow mode is that it's a bit too fast for this use case. It seems to progress to the next photo every ~5 seconds. I'd much prefer something every few minutes or hours. I ended up not using slideshow mode. I can just hit the right arrow on the keyboard to get the next photo or video.

I decided to take a stab at automate this by writing and **injecting some JavaScript into the page to change the photos for me every 10 minutes**. I used the Firefox Greasemonkey add-on and wrote this script below. Unfortunately, when you get to the last photo in an album in Google Photos, the next arrow disappears and it does not loop you back to the beginning. So I had to have the script to detect when you got to the end and then go backwards until it hits the first photo and so on.

You can adjust the time by changing the `600000` number (10 minutes in milliseconds) on the last line.

```
// ==UserScript==
// @name        google photos slower slideshow
// @namespace   piframe
// @include     https://photos.google.com/album/*
// @version     1
// @grant       none
// ==/UserScript==

function next_or_prev() {
  window.direction = window.direction || 'forward';
  var next_el = document.getElementsByClassName("oJhm5 gMFQN")[0];
  var prev_el = document.getElementsByClassName("oJhm5 KUdGif")[0];

  if (direction == 'forward') {
    var css_display = window.getComputedStyle(next_el).getPropertyValue('display');
    if (css_display == 'block') {
      next_el.click();
    } else if (css_display == 'none') {
      window.direction = 'backward';
      next_or_prev();
    }
  } else if (direction == 'backward') {
    var css_display = window.getComputedStyle(prev_el).getPropertyValue('display');
    if (css_display == 'block') {
      prev_el.click();
    } else if (css_display == 'none') {
      window.direction = 'forward';
      next_or_prev();
    }
  }
}
window.setInterval(function(){next_or_prev()}, 600000);

```

**Update:** The issue with this approach is that it relies on the class names of the left and right arrows, which are bound to change with future Google Photos web deploys. **I rewrote this script (below)** to trigger a right arrow key event instead. It keeps trying to go to the next photo and if the URL doesn't change it figures it must be at the last photo so it goes to the first photo. This **requires you to provide the URL of the first photo** in the album you are using.

```
// ==UserScript==
// @name        google photos slower slideshow
// @namespace   piframe
// @include     https://photos.google.com/album/*
// @version     1
// @grant       none
// ==/UserScript==

// CHANGE first_photo TO USE THE URL OF THE FIRST PHOTO IN YOUR ALBUM
var first_photo = 'https://photos.google.com/album/XXXX/photo/XXXX';
function pressKey() {
  var key = 39; // right arrow keycode
  var body = document.getElementsByTagName('body')[0];
  if(document.createEventObject) {
    var eventObj = document.createEventObject();
    eventObj.keyCode = key;
    body.fireEvent("onkeydown", eventObj);
  } else if (document.createEvent) {
    var eventObj = document.createEvent("Events");
    eventObj.initEvent("keydown", true, true);
    eventObj.which = key;
    body.dispatchEvent(eventObj);
  }
}
function next_or_prev() {
  var current_url = window.location.href;
  pressKey();
  if (current_url == window.location.href) {
    // page didnt change, must be at last photo
    // load the first photo
    window.location.href = first_photo;
  }
}
window.setInterval(function(){next_or_prev()}, 600000);

```

Now we're in business! With the photo display stuff figured out, I put the Pi Frame in its new home on my bookshelf. I nestled it in between my travel book collection, hooked up an extension cord to the Anker power adapter and hid the cable.



  
[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00129-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00129-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00138-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00138-1500.jpg)



[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00151-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00151-1500.jpg)
    
  


#### Turning the display off

With that out of the way, the next objective was to figure out how to turn off the display. There were two routes to explore. I could cut up the USB power cable going to the display and place a relay on it so the Pi's GPIO pin could programatically turn the power on and off. The other route would be to figure out how to programatically tell the display controller that there was no signal so it would turn off the backlight automatically. I explored the latter route.

I was unable to get the display and backlight to completely shutoff by simply setting the OS setting for display inactivity. It would black out the screen but the display controller would still think there was a video signal and keep the backlight on. What I needed to do was turn off the HDMI port entirely.

I wrote two bash scripts. The first, `display_off.sh`, simply ran this command: `tvservice -o`. I saved that file and made sure to `chmod +x` it to make it executable. I tested it out and it correctly turned off the display and backlight!

The next script to turn the display back on was a bit trickier. This is what I put in my `display_on.sh` script:

```
tvservice -p
chvt 9 && chvt 7
xrefresh -d :0
```

I tried a lot of stuff before I landed on something that worked. The chvt commands require sudo, so this script must always be run with sudo. I added the bash script to the sudoers file so that it at least doesn't ask for a password. I ran `sudo visudo` and added this line to the end (replace stammy with your Pi's username): 

```
stammy ALL=NOPASSWD: /home/stammy/display_on.sh
```

#### Wiring up a button and fan

With these two scripts I could SSH into the Pi and turn the display on and off. But SSHing into the Pi each time I wanted to toggle the display was going to be annoying. I decided to wire up a physical button to run this script for me. I soldered up a push button, hot glued it to the top right corner of the frame for easy access and attached it to GPIO pin 17 and GND.

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00163-750.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00163-1500.jpg)
      
    
  

But... if I was going to be doing a bit of soldering I figured I would also **wire up a relay to control a fan to cool down the display controller board** when running. It's probably not entirely necessary, but even with the copper heatsinks the display controller still gets very hot.

I rebuilt the relay circuit I talked about earlier in this article to control a small 5V 50mm fan powered off the Pi's 5V power source. Except this time I soldered everything (rather hackily at that) instead of using a breadboard. I wanted to use a transistor for this instead of a relay but I didn't have a 1N4001 flyback diode on hand to prevent inductive kickback when the fan shuts off.



  
[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00168-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00168-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00172-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00172-1500.jpg)

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00183-500.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00183-1500.jpg)
    
  


With everything back in place, I put the Pi Frame back on the bookshelf. I just had to **write a python script to detect the button press** then appropriately trigger the display on or off bash script and trigger the fan relay.

[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00201-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00201-1500.jpg)
    
  


With the button hooked up to GPIO pin 17 I setup that pin to listen for input. One thing to note is that it is configured with the Pi's internal pull up resistor so I didn't have to use a resistor along with the button when wiring it up. This resistor configuration makes it easier for the Pi to distinguish if the button is being pushed by making it so that the pin's input voltage doesn't float (as it would with stray capacitance) when not connected to anything.

Since I just have the push button connected to GND and a GPIO pin, I use a pull-up resistor so the GPIO pin will read high normally and read low when the button is pushed. This is a bit opposite of what you may expect, thus why the script below only acts when the input is detected as false. The other way to do this is to wire the push button to a 3.3V line and a GPIO pin with a pull-down resistor. That will get the GPIO input to be pulled down to low by default and go high when the button is pressed and the circuit closes.

```
import RPi.GPIO as GPIO
import time
import subprocess

GPIO.setmode(GPIO.BCM)
GPIO.setup(17, GPIO.IN, pull_up_down=GPIO.PUD_UP)
GPIO.setup(27, GPIO.OUT, initial=GPIO.LOW)
display_on = True

while True:
    btn = GPIO.input(17)
    if btn == False:
        print('Button press registered')
        if display_on == True:
                display_on = False
                GPIO.output(27, False)
                subprocess.call("/home/stammy/display_off.sh", shell=True)
                time.sleep(0.5)
        elif display_on == False:
                display_on = True
                GPIO.output(27, True)
                subprocess.call("/home/stammy/display_on.sh", shell=True)
                time.sleep(0.5)
```

I connected the relay's signal line to GPIO pin 27 so I just needed to set that as output. Now I just listen for the button input and toggle between turning the display on and off. To actually run the aforementioned bash scripts I use the `subprocess.call()` lines. The `time.sleep()` lines are added in there so that holding the button a bit too long won't run the scripts multiple times.

I saved the script as a python file and ran it in a terminal on the Pi. Again, I need sudo here for the chvt commands mentioned above:

  <pre class="terminal hide-label noexpand">sudo python display_button.python
```

To make it easier whenever you reboot the Pi, you can add this as a custom application launcher in the top panel. Set it to Type: "Application in Terminal."

  
    
<video loop="" autoplay="" class="photoset-item" data-ratio="1.333" preload="auto" poster="https://turbo.paulstamatiou.com/uploads/2016/06/pstam-pi-frame-toggle-poster.jpg" style="height: 468px; width: 623.844px;">
<source src="https://turbo.paulstamatiou.com/uploads/2016/06/pstam-pi-frame-toggle.mp4" type="video/mp4">
Your browser does not support HTML5 video.
</video>
    
  

Time to actually enjoy the Pi Frame!

#### What's next with the Pi Frame?

I'm pretty impressed with the result of this little frame. Impressive, crisp image quality with a ridiculously easy Google Photos "integration"... love it. However, there are a few more things I'd like to explore with this project in the future:

*   Get another matting sheet professionally cut to my exact spec since I was unable to replicate the same beveled cut it came with.
*   Use a motion sensor to turn off the display after no movement has been detected in the room for a while.
*   Replace Firefox with Chromium when the issues are addressed and see if I can get hardware accelerated videos working so that my videos on Google Photos can play well (they play now but frames drop). Detect proximity and if someone is actively looking at the display while a video is playing, enable audio to play through a small speaker.
*   Setup an Amazon Echo Alexa skill so that I can tell the Pi to change to various Google Photos albums on command, or turn on/off the display.
*   Connect a Leap Motion to be able to gesture between photos without needing to touch anything.
*   Be able to use the display for various other tasks by hosting simple local webpages and changing the browser tab on command. For example one page could be a digital clock or analog clock with an interesting watch face.



  
[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00203-1000.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00203-1500.jpg)



[![](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00211-1280.jpg)](https://turbo.paulstamatiou.com/uploads/2016/06/pstam-rpi-photo-frame-DSC00211-1500.jpg)
    
  


Thanks for reading! If you've enjoyed this post I only ask that you please share it.


[1](#r1) 只能运行较老的内核，并且软件支持更糟糕。

[2](#r2) 例如，关闭不用的端口，板载LED，减少外设以及过度的软件消耗CPU周期。[这里是一篇文章](http://www.jeffgeerling.com/blogs/jeff-geerling/raspberry-pi-zero-conserve-energy "Raspberry Pi Zero - Conserve power and reduce draw to 80mA")，它谈到了对Pi Zero做这些操作，以减少空转，从而逼近仅仅80mA。

[3](#r3) There is actually [a way to go through the initial setup via USB](http://blog.gbaman.info/?p=791 "Raspberry Pi Zero - Programming over USB") without a display at all, but it's a bit more complicated than I'd like to explain in this post. You can also preconfigure and build an image that has SSH ready to go so you can do the entire setup via SSH on boot, or if you use Raspbian it has SSH enabled by default. You'll need to connect via Ethernet instead of Wi-Fi at first and [then follow these steps](http://raspberrypi.stackexchange.com/a/27352 "How to set up Raspberry Pi without a monitor?").

[4](#r4) This actually installs an older version of netatalk, which still works but if you must always have the latest version of everything, search for how to setup netatalk 3.0+. You'll have to build it yourself, but it lets Spotlight index and search your Pi as well.

[5](#r5) Though I think that is just 50mA across _all_ GPIO pins combined. Not very much.

[6](#r6) Especially if you will be using more than one GPIO pin since they seem to all share the 50mA maximum current available on the 3.3V line; though there is a chance this number is a bit higher for the Pi 3, it was very hard to actual find this listed anywhere official.

What happens if you draw too much current off the GPIO pins when in output mode? Most likely it will just cause the Raspberry Pi to reboot. In more extreme scenarios of drawing lots of power (including various USB devices) you could blow the Raspberry Pi's built-in polyfuse which could take anywhere from a few minutes to hours to get back to normal.

[7](#r7) Yeah, I'm still using the LED here to test it out but you can place a larger device here. Up to about ~500mA safely if you like, you just may want to power it from something other than the Pi's 5V line.

[8](#r8) You'll have to do some searching for one that works at 3.3V, most require a bit more.. or just use a transistor to switch on that MOSFET with a larger power source.