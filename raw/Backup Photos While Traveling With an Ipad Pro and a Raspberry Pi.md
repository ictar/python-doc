#  Moving Electrons

  * [Home](http://www.movingelectrons.net/)
  * [Archive](http://www.movingelectrons.net/archives.html)
  * [About](http://www.movingelectrons.net/pages/about.html)

### __ Tags

  * [Reviews](http://www.movingelectrons.net/tag/reviews.html)
  * [Productivity](http://www.movingelectrons.net/tag/productivity.html)
  * [Photography](http://www.movingelectrons.net/tag/photography.html)
  * [Gadgets](http://www.movingelectrons.net/tag/gadgets.html)
  * [Audio](http://www.movingelectrons.net/tag/audio.html)
  * [Travel](http://www.movingelectrons.net/tag/travel.html)
  * [Scripting](http://www.movingelectrons.net/tag/scripting.html)

* * *

#  [Backup Photos While Traveling With an Ipad Pro and a Raspberry
Pi](http://www.movingelectrons.net/blog/2016/06/26/backup-photos-while-
traveling-with-a-raspberry-pi.html "Permalink to Backup Photos While Traveling
With an Ipad Pro and a Raspberry Pi" )

__ Sun 26 June 2016  

__ Photography __ Travel __ Gadgets __ Scripting

![Travel Photo Backup
Gear](http://www.movingelectrons.net/images/bkup_photos_main.jpg)Backup Photos
While Traveling - Gear.

## Introduction

I’ve been on a quest to finding the ideal travel photo backup solution for a
_long time_. Relying on just tossing your SD cards in your camera bag after
they are full during a trip is a risky move that leaves you too exposed: SD
cards can be lost or stolen, data can get corrupted or cards can get damaged
in transit. Backing up to another medium - even if it’s just another SD card -
and leaving that in a safe(r) place while traveling is the best practice.
Ideally, backing up to a remote location would be the way to go, but that may
not be practical depending on where you are traveling to and Internet
availability in the region.

My requirements for the ideal backup procedure are:

  1. Use an iPad to manage the process instead of a laptop. I like to travel light and since most of my trips are business related (i.e. non-photography related), I’d hate to bring my personal laptop along with my business laptop. My iPad, however; is always with me, so using it as a tool just makes sense.
  2. Use as few hardware devices as practically possible.
  3. Connection between devices should be **secure**. I’ll be using this setup in hotels and airports, so closed and encrypted connection between devices is ideal. 
  4. The whole process should be sturdy and **reliable**. I’ve tried other options using router/combo devices and [it didn’t end up well](http://bit.ly/1MVVtZi). 

## The Setup

I came up with a setup that meets the above criteria and is also flexible
enough to expand on it in the future. It involves the use of the following
gear:

  1. [iPad Pro 9.7](http://www.amazon.com/dp/B01D3NZIMA/?tag=movinelect0e-20) inches. It’s the most powerful, small and lightweight iOS device at the time of writing. **Apple pencil** is not really needed, but it’s part of my gear as I so some editing on the iPad Pro while on the road. All the heavy lifting will be done by the Raspberry Pi, so any other device capable of connecting through SSH would fit the bill.
  2. [Raspberry Pi 3](http://www.amazon.com/dp/B01CD5VC92/?tag=movinelect0e-20) with Raspbian installed.
  3. [Micro SD](http://www.amazon.com/dp/B010Q57T02/?tag=movinelect0e-20) card for Raspberry Pi and a Raspberry Pi [box/case](http://www.amazon.com/dp/B01F1PSFY6/?tag=movinelect0e-20).
  4. [128 GB Pen Drive](http://amzn.to/293kPqX). You can go bigger, but 128 GB is enough for my user case. You can also get a portable external hard drive like [this one](http://amzn.to/290syFY), but the Raspberry Pi may not provide enough power through its USB port, which means you would have to get a [powered USB hub](http://amzn.to/290syFY), along with the needed cables, defeating the purpose of having a lightweight and minimalistic setup.
  5. [SD card reader](http://amzn.to/290syFY)
  6. [SD Cards](http://amzn.to/290syFY). I use several as I don’t wait for one to fill up before using a different one. That allows me to spread photos I take on a single trip amongst several cards.

The following diagram shows how these devices will be interacting with each
other.

![Backup Photos Gear
Diagram](http://www.movingelectrons.net/images/bkup_photos_diag.jpg)Backup
Photos While Traveling - Process Diagram.

The Raspberry Pi will be configured to act as a **secured Hot Spot**. It will
create its own WPA2-encrypted WiFi network to which the iPad Pro will connect.
Although there are many online tutorials to create an Ad Hoc (i.e. computer-
to-computer) connection with the Raspberry Pi, which is easier to setup; that
connection is not encrypted and it’s relatively easy for other devices near
you to connect to it. Therefore, I decided to go with the WiFi option.

The camera’s SD card will be connected to one of the Raspberry Pi’s USB ports
through an SD card reader. Additionally, a high capacity Pen Drive (128 GB in
my case) will be permanently inserted in one of the USB ports on the Raspberry
Pi. I picked the [Sandisk Ultra Fit](http://amzn.to/293kPqX) because of its
tiny size. **The main idea is to have the Raspberry Pi backup the photos from
the SD Card to the Pen Drive with the help of a Python script**. The backup
process will be incremental, meaning that only changes (i.e. new photos taken)
will be added to the backup folder each time the script runs, making the
process really fast. This is a huge advantage if you take a lot of photos or
if you shoot in _RAW_ format. The iPad will be used to trigger the Python
script and to browse the SD Card and Pen Drive as needed.

As an added benefit, if the Raspberry Pi is connected to Internet through a
wired connection (i.e. through the Ethernet port), **it will be able to share
the Internet connection with the devices connected to its WiFi network**.

## 1\. Raspberry Pi Configuration

This is the part where we roll up our sleeves and get busy as we’ll be using
Raspbian’s _command-line interface_ (CLI) . I’ll try to be as descriptive as
possible so it’s easy to go through the process.

### Install and Configure Raspbian

Connect a keyboard, mouse and an LCD monitor to the Raspberry Pi. Insert the
Micro SD in the Raspberry Pi’s slot and proceed to install Raspbian per the
instructions in the [official
site](https://www.raspberrypi.org/downloads/noobs/).

After the installation is done, go to the CLI (Terminal in Raspbian) and type:

[code]

    sudo apt-get update
    sudo apt-get upgrade
    
[/code]

This will upgrade all software on the machine. I configured the Raspberry Pi
to connect to the local network and changed the default password as a safety
measure.

By default SSH is enabled on Raspbian, so all sections below can be done from
a remote machine. I also configured RSA authentication, but that’s optional.
More info about it [here](https://www.raspberrypi.org/documentation/remote-
access/ssh/passwordless.md).

This is a screenshot of the SSH connection to the Raspberry Pi from
[iTerm](https://www.iterm2.com) on Mac:

### Creating Encrypted (WPA2) Access Point

The installation was made based on [this](https://frillip.com/using-your-
raspberry-pi-3-as-a-wifi-access-point-with-hostapd/) article, it was optimized
for my user case.

**1\. Install Packages**

We need to type the following to install the required packages:

[code]

    sudo apt-get install hostapd
    sudo apt-get install dnsmasq
    
[/code]

**hostapd** allows to use the built-in WiFi as an access point. **dnsmasq** is a combined DHCP and DNS server that’s easy to configure.

**2\. Edit dhcpcd.conf**

Connect to the Raspberry Pi through Ethernet. Interface configuration on the
Raspbery Pi is handled by `dhcpcd`, so first we tell it to ignore `wlan0` as
it will be configured with a static IP address.

Open up the dhcpcd configuration file with `sudo nano /etc/dhcpcd.conf` and
add the following line to the bottom of the file:

`denyinterfaces wlan0`

**Note**: This must be **above** any interface lines that may have been added.

**3\. Edit interfaces**

Now we need to configure our static IP. To do this, open up the interface
configuration file with `sudo nano /etc/network/interfaces` and edit the
`wlan0` section so that it looks like this:

[code]

    allow-hotplug wlan0
    iface wlan0 inet static
        address 192.168.1.1
        netmask 255.255.255.0
        network 192.168.1.0
        broadcast 192.168.1.255
    #    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
    
[/code]

Also, the `wlan1` section was edited to be:

[code]

    #allow-hotplug wlan1
    #iface wlan1 inet manual
    #    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
    
[/code]

**Important:** Restart `dhcpcd` with `sudo service dhcpcd restart` and then reload the configuration for `wlan0` with `sudo ifdown eth0; sudo ifup wlan0`.

**4\. Configure Hostapd**

Next, we need to configure `hostapd`. Create a new configuration file with
`sudo nano /etc/hostapd/hostapd.conf with the following contents:

[code]

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
    
[/code]

Now, we also need to tell `hostapd` where to look for the config file when it
starts up on boot. Open up the default configuration file with `sudo nano
/etc/default/hostapd` and find the line `#DAEMON_CONF=""` and replace it with
`DAEMON_CONF="/etc/hostapd/hostapd.conf"`.

**5\. Configure Dnsmasq**

The shipped dnsmasq config file contains tons of information on how to use it,
but we won’t be using all the options. I’d recommend moving it (rather than
deleting it), and creating a new one with

[code]

    sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.orig  
    sudo nano /etc/dnsmasq.conf  
    
[/code]

Paste the following into the new file:

[code]

    interface=wlan0      # Use interface wlan0
    listen-address=192.168.1.1 # Explicitly specify the address to listen on
    bind-interfaces      # Bind to the interface to make sure we aren't sending things elsewhere
    server=8.8.8.8       # Forward DNS requests to Google DNS
    domain-needed        # Don't forward short names
    bogus-priv           # Never forward addresses in the non-routed address spaces.
    dhcp-range=192.168.1.50,192.168.1.100,12h # Assign IP addresses in that range  with a 12 hour lease time
    
[/code]

**6\. Set up IPV4 forwarding**

One of the last things that we need to do is to enable packet forwarding. To
do this, open up the sysctl.conf file with `sudo nano /etc/sysctl.conf`, and
remove the # from the beginning of the line containing
`net.ipv4.ip_forward=1`. This will enable it on the next reboot.

We also need to share our Raspberry Pi’s internet connection to our devices
connected over WiFi by the configuring a NAT between our `wlan0` interface and
our `eth0` interface. We can do this by writing a script with the following
lines.

[code]

    sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE  
    sudo iptables -A FORWARD -i eth0 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT  
    sudo iptables -A FORWARD -i wlan0 -o eth0 -j ACCEPT  
    
[/code]

I named the script `hotspot-boot.sh` and made it executable with:

`sudo chmod 755 hotspot-boot.sh`

The script should be executed when the Raspberry Pi boots. There are many ways
to accomplish this, and this is the way I went with:

  1. Put the file in `/home/pi/scripts`.
  2. Edit the `rc.local` file by typing `sudo nano /etc/rc.local` and place the call to the script **before the line that reads `exit 0`** (more information [here](https://www.raspberrypi.org/documentation/linux/usage/rc-local.md)).

This is how the `rc.local` file looks like after editing it.

[code]

     1
     2
     3
     4
     5
     6
     7
     8
     9
    10
    11
    12
    13
    14
    15
    16
    17
    18
    19
    20
    21
    22
[/code]

|

[code]

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
    
[/code]  
  
---|---  
  
### Installing Samba and NTFS Compatibility.

We also need to install the following packages to enable the Samba protocol
and allow the [File Browser](https://itunes.apple.com/us/app/filebrowser-
access-files-on/id364738545?mt=8&uo=4&at=11lqkH) App to see the connected
devices to the Raspberry Pi as shared folders. Also, `ntfs-3g` provides NTFS
compatibility in case we decide to connect a portable hard drive to the
Raspberry Pi.

[code]

    sudo apt-get install ntfs-3g
    sudo apt-get install samba samba-common-bin
    
[/code]

You can follow [this](http://www.howtogeek.com/139433/how-to-turn-a-raspberry-
pi-into-a-low-power-network-storage-device/) article for details on how to
configure Samba.

**Important Note:** The referenced article also goes through the process of mounting external hard drives on the Raspberry Pi. We won’t be doing that because, at the time of writing, **the current version of Raspbian (Jessie) auto-mounts both the SD Card and the Pendrive to `/media/pi/` when the device is turned on**. The article also goes over some redundancy features that we won’t be using.

## 2\. Python Script

Now that the Raspberry Pi has been configured, we need to work on the script
that will actually backup/copy our photos. Note that **this script just
provides certain degree of automation to the backup process**. If you have a
basic knowledge of using the Linux/Raspbian CLI, you can just SSH into the
Raspberry Pi and copy yourself all photos from one device to the other by
creating the needed folders and using either the `cp` or the `rsync` command.
We’ll be using the `rsync` method on the script as it’s very reliable and
allows for **incremental backups**.

This process relies on two files: the script itself and the configuration file
`backup_photos.conf`. The latter just have a couple of lines indicating where
the destination drive (Pendrive) is mounted and what folder has been mounted
to. This is what it looks like:

[code]

    mount folder=/media/pi/
    destination folder=PDRIVE128GB
    
[/code]

**Important:** Do not add any additional spaces between the `=` symbol and the words to both sides of it as the script will break (definitely an opportunity for improvement).

Below is the Python script, which I named `backup_photos.py` and placed in
`/home/pi/scripts/`. I included comments in between the lines of code to make
it easier to follow.

[code]

     1
     2
     3
     4
     5
     6
     7
     8
     9
    10
    11
    12
    13
    14
    15
    16
    17
    18
    19
    20
    21
    22
    23
    24
    25
    26
    27
    28
    29
    30
    31
    32
    33
    34
    35
    36
    37
    38
    39
    40
    41
    42
    43
    44
    45
    46
    47
    48
    49
    50
    51
    52
    53
    54
    55
    56
    57
    58
    59
    60
    61
    62
    63
    64
    65
    66
    67
    68
[/code]

|

[code]

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
    
[/code]  
  
---|---  
  
## 3\. iPad Pro Configuration

Since all the heavy-lifting will be done on the Raspberry Pi and no files will
be transferred through the iPad Pro, which was a huge disadvantage in [one of
the workflows I tried before](http://bit.ly/1MVVtZi); we just need to install
[Prompt
2](https://itunes.apple.com/us/app/prompt-2/id917437289?mt=8&uo=4&at=11lqkH)
on the iPad to access the Raspeberry Pi through SSH. Once connected, you can
either run the Python script or copy the files manually.

![Prompt Screenshot](http://www.movingelectrons.net/images/bkup_photos_ipad&rp
i_prompt.jpg)SSH Connection to Raspberry Pi From iPad Using Prompt.

Since we installed Samba, we can access USB devices connected to the Raspberry
Pi in a more graphical way. **You can stream videos, copy and move files
between devices.** [File Browser](https://itunes.apple.com/us/app/filebrowser-
access-files-on/id364738545?mt=8&uo=4&at=11lqkH) is perfect for that.

## 4\. Putting it All Together

Let’s suppose that `SD32GB-03`is the label of an SD card connected to one of
the USB ports on the Raspberry Pi. Also, let’s suppose that `PDRIVE128GB` is
the label of the Pendrive, also connected to the device and defined on
the`.conf` file as indicated above. If we wanted to backup the photos on the
SD Card, we would need to go through the following steps:

  1. Turn on Raspberry Pi so that drives are mounted automatically.
  2. Connect to the WiFi network generated by the Raspberry Pi.
  3. Connect to the Raspberry Pi through SSH using the [Prompt](https://itunes.apple.com/us/app/prompt-2/id917437289?mt=8&uo=4&at=11lqkH) App.
  4. Type the following once you are connected:

`python3 backup_photos.py SD32GB-03`

The first backup my take some minutes depending on how much of the card is
used. That means **you need to keep the connection alive to the Raspberry Pi
from the iPad. You can get around this by using the
[nohup](https://en.m.wikipedia.org/wiki/Nohup) command before running the
script**.

`nohup python3 backup_photos.py SD32GB-03 &`

![iterm Screenshot After Running the Python Script.](http://www.movingelectron
s.net/images/bkup_photos_ipad&rpi_finished.png)iTerm Screenshot After Running
Python Script.

## Further Customization

I installed a VNC server to access Raspbian’s graphical interface from another
computer or the iPad through [Remoter App](https://itunes.apple.com/us/app
/remoter-pro-vnc-ssh-rdp/id519768191?mt=8&uo=4&at=11lqkH). I’m looking into
installing [BitTorrent Sync](https://getsync.com) for backing up photos to a
remote location while on the road, which would be the ideal setup. I’ll expand
this post once I have a workable solution.

Feel free to either include your comments/questions below or reach out to me.
My contact info is at the footer of this page.

## Comments

Please enable JavaScript to view the comments.

  * [__](https://twitter.com/LeninRzSz)
  * [__](mailto:lenin@movingelectrons.net?Subject=email%20from%20www.movingelectrons.net)
  * [__](http://www.movingelectrons.net/feeds/all.atom.xml)
  * [__](https://www.flickr.com/photos/lenin-ramirez-sanchez/)
  * [__](https://github.com/Moving-Electrons)

  *[
                __ Sun 26 June 2016
        ]: 2016-06-26T21:00:00-05:00

