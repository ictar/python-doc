原文：[Scientific Python for Raspberry Pi](http://geoffboeing.com/2016/03/scientific-python-raspberry-pi/)

---

_![Raspberry Pi 3 Model B](http://i1.wp.com/geoffboeing.com/wp-content/uploads/2016/03/raspberry-pi-3.jpg?resize=200%2C150)A guide to setting up the Python scientific stack, well-suited for geospatial analysis, on a Raspberry Pi 3. The whole process takes just a few minutes._

The Raspberry Pi 3 was announced two weeks ago and presents a substantial step up in computational power over its predecessors. It can serve as a functional Wi-Fi connected Linux desktop computer – albeit underpowered. However it’s perfectly capable of running the Python scientific computing stack including pandas, Jupyter, matplotlib, scipy, scikit-learn, and numpy.

Despite (or because of?) its low power, it’s ideal for low-overhead and repetitive tasks that researchers and engineers often face, including geocoding, web scraping, scheduled API calls, or recurring statistical or spatial analyses (with small-ish data sets). It’s also a great way to set up a simple server or experiment with Linux. This guide is aimed at newcomers to the world of Raspberry Pi and Linux, but who have an interest in setting up a Python environment on these $35 credit card sized computers. We’ll run through everything you need to do to get started (if your Pi is already up and running, skip steps 1 and 2).

### Step 1: Get the hardware

Assuming you have an available phone charger, HDMI cable, mouse, and keyboard, you can buy everything else you need to get up and running for under ![raspberry-pi-3-b](http://i2.wp.com/geoffboeing.com/wp-content/uploads/2016/03/raspberry-pi-3-b.jpg?resize=300%2C300)$45. Here’s what you’ll need:

1.  A [Raspberry Pi](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/) ($35)
2.  A 5-volt 1-amp [power supply](https://www.raspberrypi.org/documentation/hardware/raspberrypi/power/README.md) (I just used an old Android charger with a micro-USB cable, otherwise about $5)
3.  A micro SD card with a full-size SD adapter ([about $9](http://www.amazon.com/Samsung-Class-Adapter-MB-MP32DA-AM/dp/B00IVPU786/))
4.  An HDMI cable to connect your monitor (I already had one, otherwise [about $5](http://www.amazon.com/AmazonBasics-High-Speed-HDMI-Cable-Supports/dp/B00870ZHCQ/))
5.  USB mouse/keyboard (I already had them, otherwise [about $15](http://www.amazon.com/AmazonBasics-Wired-Keyboard-Mouse-Bundle/dp/B00B7GV802/) for a basic set)
6.  _Optional_: if you’re running your Raspberry Pi side-by-side with a desktop computer, you can get a cheap [USB switch](http://www.amazon.com/gp/product/B006Z0Q2SI) to switch your mouse/keyboard from the computer to the Raspberry Pi and back

### Step 2: Install Raspbian OS on the Raspberry Pi

Now we’ll install _Raspbian_ (the Debian Linux OS tailored for Raspberry Pi) the easy way, then boot the OS and connect to Wi-Fi.

1.  Pop your micro SD card into its full-size SD adapter sleeve and stick it in your computer
2.  Download the [NOOBS](https://www.raspberrypi.org/downloads/noobs/) installer for Raspbian and un-zip it to your desktop
3.  Download [SDFormatter](https://www.sdcard.org/downloads/formatter_4/eula_windows/SDFormatterv4.zip) and install it (this tool is particularly useful if you eventually want to refresh your Raspberry Pi system, as the Linux partitions otherwise might be tricky to work with on your desktop computer)
4.  Open SDFormatter, choose the SD card drive, click format
5.  When the formatting is done, copy the NOOBS files from your desktop to the SD card
6.  Pop the SD card out of the adapter sleeve and into the Raspberry Pi
7.  Connect your mouse, keyboard, HDMI, and power supply to the Raspberry Pi
8.  Once NOOBS boots up, choose your language, select Raspbian, then click install

When the installation is finished, click ok and the new OS will boot up. The Raspberry Pi 3 has Wi-Fi: in the top-right of the screen, click the Wi-Fi networks panel item and choose your network to connect.

### Step 3: Update packages

Next we update the existing software. Open a terminal window and run the following commands, one at a time. The first line fetches updated package lists from the repositories, and the second then fetches new versions of installed packages. The last two lines list the installed system packages and installed Python packages and dump them to files, just for reference.
```sh
sudo apt-get update
sudo apt-get upgrade
dpkg -l > ~/Desktop/packages.list
pip freeze > ~/Desktop/pip-freeze-initial.list
```
[apt-get](https://wiki.debian.org/apt-get) is a Debian tool to install and update software packages. We’ll use it instead of pip wherever we can because the packages come pre-compiled, meaning they take seconds rather than minutes to install. When a Python package isn’t available via apt-get, we’ll fall back on using pip to install (and compile) it.

### Step 4: Install the Python basics

As we saw in the previous step’s file output, the Raspberry Pi comes with several Python packages already installed. We need to supplement it with a few more prerequisites. In the terminal window, run this command:
```sh
sudo apt-get install build-essential python-dev python-distlib python-setuptools python-pip python-wheel libzmq-dev libgdal-dev
```

The [build-essential](https://packages.debian.org/jessie/build-essential) package is required for building Debian packages; [python-dev](https://packages.debian.org/jessie/python-dev), [python-distlib](https://packages.debian.org/jessie/python-distlib), and [python-setuptools](https://packages.debian.org/jessie/python-setuptools) provide several Python development and packaging tools; [python-pip](https://packages.debian.org/jessie/python-pip) and [python-wheel](https://packages.debian.org/jessie/python-wheel) are useful for installing Python packages; [libzmq-dev](https://packages.debian.org/jessie/libzmq-dev) is needed for Jupyter notebooks; [libgdal-dev](https://packages.debian.org/jessie/libgdal-dev) is needed for geospatial analysis with geopandas.

### Step 5: Install pandas dependencies

Pandas has several recommended and optional [dependencies](http://pandas.pydata.org/pandas-docs/stable/install.html#dependencies) that unlock functionality or provide significant performance enhancements. To install them all, run the following two commands:
```py
sudo apt-get install xsel xclip libxml2-dev libxslt-dev python-lxml python-h5py python-numexpr python-dateutil python-six python-tz python-bs4 python-html5lib python-openpyxl python-tables python-xlrd python-xlwt cython python-sqlalchemy python-xlsxwriter python-jinja2 python-boto python-gflags python-googleapi python-httplib2 python-zmq libspatialindex-dev
sudo pip install bottleneck rtree
```

The first command uses apt-get to install the available recommended dependencies, and the second command uses pip to install the two that are not available in the apt repositories.

### Step 6: Install the scientific Python stack

Fortunately we can use apt-get to install all the massive, complex packages that make up the Python scientific stack without having to compile everything. This makes the process much, much faster.
```sh
sudo apt-get install python-numpy python-matplotlib python-mpltoolkits.basemap python-scipy python-sklearn python-statsmodels python-pandas
```
If you need a specific version of these packages or want a more up-to-date version than exists in the Debian repositories, you can use pip to install it, but be prepared for a slow compilation process.

### Step 7 (optional): Install other useful packages

We’re all done! But if you optionally would like to install a few more useful packages, run the following two commands:
```sh
sudo apt-get install python-requests python-pil python-scrapy python-geopy python-shapely python-pyproj
sudo pip install jupyter geopandas
```

The [requests](https://packages.debian.org/jessie/python-requests) Python package provides a graceful interface for making HTTP requests, [pil](https://packages.debian.org/jessie/python-pil) provides Python imaging capabilities, [scrapy](https://packages.debian.org/jessie/python-scrapy) is a web scraping framework, [geopy](https://packages.debian.org/jessie/python-geopy) provides geocoding and geodesic distance functions, [shapely](https://packages.debian.org/jessie/python-shapely) provides 2D geometry manipulation, and [pyproj](https://packages.debian.org/jessie/python-pyproj) provides cartographic transformations. In the second command, [jupyter](http://jupyter.org/) provides interactive coding notebooks and [geopandas](http://geopandas.org/) spatializes pandas.

### Wrapping up

Our Python scientific stack is now all ready to use on the Raspberry Pi. Launch a [Jupyter notebook](http://geoffboeing.com/2015/08/urban-informatics-visualization-berkeley/), load up some [data with pandas](http://geoffboeing.com/2014/08/visualizing-summer-travels-part-5-python-matplotlib/), or [plot a map](http://geoffboeing.com/2014/09/visualizing-summer-travels-part-6-projecting-spatial-data-python/) with basemap. Due to the Raspberry Pi’s memory constraints, you cannot load huge data sets, bur everything else works great. It’s particularly good for repetitive, scheduled, or low-overhead tasks such as geocoding and web scraping.

