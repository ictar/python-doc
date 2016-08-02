原文：[How I built a Slack bot to help me find an apartment in San Francisco](https://www.dataquest.io/blog/apartment-finding-slackbot/)

---

几个月前，我从波士顿搬到湾区。Priya（我的女朋友）和我听到了租赁市场的各种恐怖故事。事实上，在谷歌上搜索“如何在旧金山找到一个公寓”得到[数十](https://www.google.com/search?q=how+to+find+an+apartment+in+san+francisco)页建议是对找房子是一个痛苦的过程的最好的暗示。

![](https://www.dataquest.io/blog/images/slack/boston.jpg)

Boston很冷，但在旧金山找房子是很可怕的

我们读到房东举行开放日，而你必须把所有的文书工作带到开放日，并且愿意立即付首款，才能被考虑参与开放日。我们开始研究详尽的过程，并推断出很多时候找到一个公寓是时机的问题。有些业主不管是什么都想举行开放日，但对于其他业主，作为第一个看到了公寓的人，通常意味着你可以租它。你必须找到房源，快速地弄清楚它是否符合你的标准，然后打电话给房东，让他安排你去看一看。

我们看了一些网络贴主推荐的公寓租赁网站，例如[Padmapper](https://www.padmapper.com/)和[LiveLovely](https://livelovely.com)，但他们都没有给我们一个实时房源提要，以供我们查看和比较。他们也没有能让我们指定附加​​条件，像非常具体的社区，或靠近交通。由于在海湾地区，大多数的房源基本上是在[Craigslist](https://www.craigslist.com)网站上，而其他网站是通过爬取该网站来获得房源的，因此也有一个担心，也许不是所有房源都爬取到了，或者说它们爬取的速度不够快得保证实时。

我们想要：

*   当Craigslist上有了新发布的时候，可以得到近乎实时的通知。
*   筛选掉不符合我们所期望社区的房源。
*   筛选掉不符合附加条件的房源，例如那些不靠近公共交通的。
*   房源协作，并且一起评价。
*   对于那些我们喜欢的房源的房东，可以很容易的和他们联系。

在思考了这个问题后，我发现，可以用4个步骤来解决这个问题：

*   从Craigslist爬取房源。
*   过滤掉那些不符合我们标准的房源。
*   把房源发布到[Slack](https://slack.com/)，它是一个团队聊天工具，这样，我们就可以讨论和评价它们。
*   将整个过程封装到一个持续的循环，然后将其部署到一个服务器（这样，它就会连续运行）。

在这篇文章的其余部分，我们将逐步看看每一块是如何构建的，以及如何使用这个最终的Slack机器人来帮助我们找到一间公寓。使用这个机器人，Priya和我在一个星期左右，发现了一间我们喜欢的，并且价位合理的（对于旧金山！）卧室，这比我们想象的要花费少得多的时间。

**当你在看这篇文章的时候，如果你想要看看代码，那么[这里是](https://github.com/VikParuchuri/apartment-finder)到最终项目的链接，而[这里是](https://github.com/VikParuchuri/apartment-finder/blob/master/README.md)README.md的链接。**

## 第一步 —— 从Craigslist抓取房源

构建我们的机器人的第一步是从Craiglist获取房源。不幸的是，Craiglist并未提供API，但是，我们可以使用[python-craiglist](https://github.com/juliomalegria/python-craigslist)包来获取。`python-craigslist`抓取页面的内容，然后使用[BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/)来从页面提取相关部分，接着将其转换成结构化数据。这个包的代码非常短，值得一读。

Craigslist上的旧金山房源位于`https://sfbay.craigslist.org/search/sfc/apa`。在下面的代码中，我们：

*   导入`CraigslistHousing`，这是`python-craigslist`中的一个类。
*   使用以下参数初始化该类：

    *   `site` – 我们想要爬的Craigslist网站。`site`是URL的第一部分，像`https://sfbay.craigslist.org`。
    *   `area` – 我们想要的爬取的网站分区。`area`是URL的最后一部分，像`https://sfbay.craigslist.org/sfc/`，我们只会看旧金山的。
    *   `category` – 我们想要找的房源类型。`category`是搜索URL的最后一部分，像`https://sfbay.craigslist.org/search/sfc/apa`，它会列出所有公寓。
    *   `filters` – 我们想应用到结果的任意过滤器。

        *   `max_price` – 我们愿意支付的最高价。
        *   `min_price` – 我们想要找的最低价。
*   使用`get_results`方法获取Craigslist的结果，这是一个[生成器](https://wiki.python.org/moin/Generators).

    *   传递`geotagged`参数，试图添加坐标到每个结果中。
    *   传递`limit`参数，只获取`20`个结果。
    *   传递`newest`参数，只获取最新的房源。
*   从`results`生成器中获取每个`result`，然后打印出来。

```python
from craigslist import CraigslistHousing

cl = CraigslistHousing(site='sfbay', area='sfc', category='apa',
                         filters={'max_price': 2000, 'min_price': 1000})

results = cl.get_results(sort_by='newest', geotagged=True, limit=20)
for result in results:
    print result
```

我们已经非常快速的完成了这个机器人的第一步！现在，我们可以抓取Craigslist，获得房源了。每个`result`都是一个带有几个字段的字典：

```python
{'datetime': '2016-07-20 16:39',
 'geotag': (37.783166, -122.418671),
 'has_image': True,
 'has_map': True,
 'id': '5692904929',
 'name': 'Be the first in line at Brendas restaurant!SQuiet studio available',
 'price': '$1995',
 'url': 'http://sfbay.craigslist.org/sfc/apa/5692904929.html',
 'where': 'tenderloin'}
```

下面是字段描述：

*   `datetime` – 该房源的发布时间。
*   `geotag` – 该房源的坐标位置。
*   `has_image` – Craigslist的发布中是否有图片。
*   `has_map` – 该房源是否带有地图。
*   `id` – 该房源的唯一Craigslist id。
*   `name` – 该房源在Craigslist上显示的名字。
*   `price` – 每月租金。
*   `url` – 查看完整房源的URL。
*   `where` – 创建了该房源的人所说明的地点。

## 第二步 —— 过滤结果

现在，我们有办法从Craigslist网站获取房源了，只需找到一个方法过滤它们，并且只看到我们喜欢的。

### 通过范围过滤结果

在Priya和我找房子的时候，我们想要在几个范围内找，包括：

*   San Francisco

    *   [Sunset](https://en.wikipedia.org/wiki/Sunset_District,_San_Francisco)
    *   [Pacific Heights](https://en.wikipedia.org/wiki/Pacific_Heights,_San_Francisco)
    *   [Lower Pacific Heights](https://en.wikipedia.org/wiki/Lower_Pacific_Heights,_San_Francisco)
    *   [Bernal Heights](https://en.wikipedia.org/wiki/Bernal_Heights,_San_Francisco)
    *   [Richmond](https://en.wikipedia.org/wiki/Richmond_District,_San_Francisco)
*   Berkeley
*   Oakland

    *   [Adams Point](https://en.wikipedia.org/wiki/Adams_Point,_Oakland,_California)
    *   [Lake Merritt](https://en.wikipedia.org/wiki/Lake_Merritt)
    *   [Rockridge](https://en.wikipedia.org/wiki/Rockridge,_Oakland,_California)
*   Alameda

为了根据社区过滤，我们将首先需要定义区域周围的边界盒：

![](https://www.dataquest.io/blog/images/slack/bounding_box.png)

在Lower Pacific Heights周围画一个盒子


上面的边界盒是用[BoundingBox](http://boundingbox.klokantech.com/)创建的。确保在左下方指定`CSV`选秀，以获取盒的坐标。

你还可以通过使用诸如Google Maps这样的工具，查找左下角和右上角的坐标，自己定义一个边界盒。在找到边界盒后，我们会创建社区和坐标的字典：

```python
BOXES = {
    "adams_point": [
        [37.80789, -122.25000],
        [37.81589,	-122.26081],
    ],
    "piedmont": [
        [37.82240, -122.24768],
        [37.83237, -122.25386],
    ],
    ...
}
```

每个字典键是一个社区名，每个键包括一个列表的列表。第一个内部列表是盒的左下角的坐标，第二个是盒的右上角的坐标。然后，我们可以通过检查房源的坐标是否位于盒子中间来进行过滤。

下面的代码将：

*   通过`BOXES`中的每个键进行循环。
*   检查结果是否位于盒中。
*   如果是的话，设置合适的变量。

```python
def in_box(coords, box):
    if box[0][0] < coords[0] < box[1][0] and box[1][1] < coords[1] < box[0][1]:
        return True
    return False
    
geotag = result["geotag"]
area_found = False
area = ""
for a, coords in BOXES.items():
    if in_box(geotag, coords):
        area = a
        area_found = True
```

不幸的是，并不是所有Craigslist上的结果都带有坐标的。这取决于那个发布房源的人是否指定了位置，这样才能计算坐标。发布房源的人越熟悉Craigslist，他们就越有可能包括位置。

通常，由中介（更有可能收取高额租金）发布的房源会有相关位置信息。有业主发布的房源更有可能没有坐标，但是通常也有更好的交易。因此，弄清楚没有坐标的房源是否位于我们所想要的社区这种失效保护是有意义的。我们将创建一个社区列表，然后进行字符串匹配，看看是否该房源落入其中一个社区中。这比使用坐标不精确，但是聊胜于无：

```python
NEIGHBORHOODS = ["berkeley north", "berkeley", "rockridge", "adams point", ... ]
```

进行基于名字的匹配，可以通过每个`NEIGHBORHOODS`进行循环：

```python
location = result["where"]
for hood in NEIGHBORHOODS:
    if hood in location.lower():
        area = hood
```

一旦通过我们目前写的这两部分代码处理了结果，我们已经移除了任何我们不想要住的社区的房源。会有一些误报，并且可能错过一些没有指定社区或位置的房源，但是这个系统拥有大多数的房源。

### 通过靠近交通过滤结果

Priya和我知道，我们都会频繁去旧金山，因此，如果我们不在旧金山的话，那么我们想要住得靠近公共交通。在湾区，公共交通的主要形式是[BART](https://en.wikipedia.org/wiki/Bay_Area_Rapid_Transit)。BART是连接Oakland, Berkeley, San Francisco及其周边地区的部分地下交通系统。

为了将这个功能集成到我们的机器人中，首先，我们将需要定义一个中转站列表。我们可以从[Google Maps](https://maps.google.com)获得公交车站的坐标，然后用它们创建一个字典：

```python
TRANSIT_STATIONS = {
    "oakland_19th_bart": [37.8118051,-122.2720873],
    "macarthur_bart": [37.8265657,-122.2686705],
    "rockridge_bart": [37.841286,-122.2566329],
    ...
}
```

每个键是一个中转站的名字，并且具有相关联列表。列表包含中转站的经度和纬度。一旦我们有了字典，我们就可以找到到每个结果的最近的中转站。

下面的代码将：

*   对`TRANSIT_STATIONS`中的每个键和项进行循环。
*   使用`coord_distance`函数来查找两对坐标之间的以公里为单位的距离。你可以[在这里](http://www.codecodex.com/wiki/Calculate_distance_between_two_points_on_a_globe#Python)找到这个函数的解释。
*   检查该站是否最靠近房源。

    *   如果站太远 (远于`2`公里，或者约`1.2`英里)，那将其忽略。
    *   如果站比前一个最靠近的站近，那么就用它。

```python
min_dist = None
near_bart = False
bart_dist = "N/A"
bart = ""
MAX_TRANSIT_DIST = 2 # kilometers

for station, coords in TRANSIT_STATIONS.items():
    dist = coord_distance(coords[0], coords[1], geotag[0], geotag[1])
    if (min_dist is None or dist < min_dist) and dist < MAX_TRANSIT_DIST:
        bart = station
        near_bart = True

    if (min_dist is None or dist < min_dist):
        bart_dist = dist
```

在此之后，我们知道了到每个房源的最近的中转站。

## 第三步 —— 创建我们的Slack机器人

### 安装

在我们过滤了结果后，已经准备好发布我们所有的到Slack了。如果你不熟悉Slack，那么有一个团队聊天应用。在Slack上创建一个团队，然后就可以邀请成员。每个Slack团队可以有多个频道，成员可以通过频道交换消息。每个消息可以被频道中的其他成员注释，例如添加一个赞或者其他表情。[这里是](https://slack.com/is)在Slack上的更多信息。如果你想要感受一下Slack，我们运行了一个[数据科学Slack社区](https://www.dataquest.io/chat)，你也许想要加入。

通过将我们的结果发布到Slack，我们将能够与其他人合作，并找出哪个房源是最好的。要做到这一点，我们需要：

*   创建一个Slack团队，可以[在这里](https://slack.com/create#email)做到。
*   为要发布的房源创建一个频道。[这里是](https://get.slack.help/hc/en-us/articles/201402297-Creating-a-channel)关于这个的帮助。建议使用`#housing`作为频道的名字。
*   获取一个Slack API token，我们可以[在这里](https://api.slack.com/docs/oauth-test-tokens)做到。[这里是](https://get.slack.help/hc/en-us/articles/215770388-Creating-and-regenerating-API-tokens)此过程的更多信息。

在这些步骤后，我们准备好创建将房源发布到Slack的代码了。

### 开始写代码

在获取正确的频道名和token后，我们可以将结果发布到Slack了。要做到这点，我们将使用[python-slackclient](https://github.com/slackhq/python-slackclient)，它是一个让使用[Slack API](https://api.slack.com/)变得容易的Python包。使用让我们访问管理团队和消息的许多API端点。

下面的代码将：

* 使用`SLACK_TOKEN`初始化一个`SlackClient`。

* 从`result`创建一个消息字符串，包含所有我们需要看到的信息，例如价格，房源所在社区和URL。

* 使用username为`pybot`，以及一个机器人作为头像，将消息发布到Slack。

```python
from slackclient import SlackClient

SLACK_TOKEN = "ENTER_TOKEN_HERE"
SLACK_CHANNEL = "#housing"

sc = SlackClient(SLACK_TOKEN)
desc = "{0} | {1} | {2} | {3} | <{4}>".format(result["area"], result["price"], result["bart_dist"], result["name"], result["url"])
sc.api_call(
    "chat.postMessage", channel=SLACK_CHANNEL, text=desc,
    username='pybot', icon_emoji=':robot_face:'
)
```

一旦所有的东西都连在一起了，Slack机器人将会把房源发布到Slack，看起来是这样的：

![](https://www.dataquest.io/blog/images/slack/slack.png)

机器人运行时房源看起来的样子。注意，你可以如何用表情注释房源，例如，点赞。


## 第四步 —— 开始运行

现在，基本的都有了，我们需要持续的运行代码。毕竟，我们想要结果实时，或近乎实时的发布到Slack。为了操作一切，我们将需要经过以下几个步骤：

*   将房源存储到数据库中，这样，就不会重复发布到Slack。
*   将配置，例如`SLACK_TOKEN`，从代码的其余部分分离，使其易于调整。
*   创建一个将会连续运行的循环，这样我们就可以24/7爬取结果。

### 保存房源

第一步是使用名为[SQLAlchemy](http://www.sqlalchemy.org/)的Python包来存储我们的房源。SQLAlchemy是一个[对象关系映射(Object Relational Mapper)](https://en.wikipedia.org/wiki/Object-relational_mapping)，或者ORM，使得从Python与数据库交互更容易。使用SQLAlchemy，我们可以创建一个数据库表，用来存储房源，以及创建一个数据库连接，使得添加数据到表中变得简单。

我们将结合[SQLite](https://www.sqlite.org/)数据库引擎使用SQLAlchemy，这将把我们所有的数据存储到名为`listings.db`的单个文件中。

下面的代码将：

*   导入SQLAlchemy。
*   创建一个到SQLite数据库`listings.db`的连接，这个数据库将会在我们当前目录下创建。
*   定义一个名为`Listing`的表单，保存Craigslist房源的所有的相关字段。

    *   `unique`字段`cl_id`和`link`将会防止我们发布重复的房源到Slack。
*   从连接中创建一个数据库session，这将允许我们存储房源。

```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String, DateTime, Float, Boolean
from sqlalchemy.orm import sessionmaker

engine = create_engine('sqlite:///listings.db', echo=False)

Base = declarative_base()

class Listing(Base):
    """
    A table to store data on craigslist listings.
    """

    __tablename__ = 'listings'

    id = Column(Integer, primary_key=True)
    link = Column(String, unique=True)
    created = Column(DateTime)
    geotag = Column(String)
    lat = Column(Float)
    lon = Column(Float)
    name = Column(String)
    price = Column(Float)
    location = Column(String)
    cl_id = Column(Integer, unique=True)
    area = Column(String)
    bart_stop = Column(String)

Base.metadata.create_all(engine)

Session = sessionmaker(bind=engine)
session = Session()
```

现在，有了数据库模型，我们仅需存储每个房源到数据库，并且我们能够避免重复。

### 从代码中分离配置

下一步骤是将配置从代码分开。我们将创建一个名为`settings.py`的文件，用来存储我们的配置。配置包括`SLACK_TOKEN`，它是一个机密，我们不想意外地提交到Git，并且推到Github上，还有其他设置，如`BOXES`，它不属于机密，但我们希望能够容易地编辑。

我们将把以下配置移到`settings.py`：

*   `MIN_PRICE` – 我们要搜索​​的最低房源价格。
*   `MAX_PRICE` – 我们要搜索​​的最高房源价格。
*   `CRAIGSLIST_SITE` – 我们想要搜索的区域Craigslist网站。
*   `AREAS` – 我们想要搜索的区域Craigslist网站的区域列表。
*   `BOXES` – 我们想要看到的社区坐标盒。
*   `NEIGHBORHOODS` – 如果房源没有坐标，那么这是匹配的社区列表。
*   `MAX_TRANSIT_DIST` – 我们想要离中转站的最远距离。
*   `TRANSIT_STATIONS` – 中转站坐标。
*   `CRAIGSLIST_HOUSING_SECTION` – 我们想要看到的Craigslist房屋的分段。
*   `SLACK_CHANNEL` – 我们想要机器人发布的Slack渠道。

我们还想要创建一个名为`private.py`的文件，git会忽略这个文件，这个文件包含以下键：

*   `SLACK_TOKEN` – 发布到我们的Slack团队的token。

你可以[在这里](https://github.com/VikParuchuri/apartment-finder/blob/master/settings.py)看到完整的`settings.py`文件。

### 创建循环

最后，我们需要创建一个连续运行我们的爬取代码的循环。下面的代码将：

*   当从命令行调用的时候：

    *   打印一条包含当前时间的状态信息。
    *   通过调用`do_scrape`函数来运行craigslist抓取代码。
    *   如果用户输入`Ctrl + C`，那么退出。
    *   通过打印回溯并继续来处理异常。
    *   如果没有异常，打印一条成功信息 (对应于下面的`else`语句)。
    *   在再次抓取之前，休眠一段定义的时间间隔。默认情况下，它被设置成`20`分钟。

```python
from scraper import do_scrape
import settings
import time
import sys
import traceback

if __name__ == "__main__":
    while True:
        print("{}: Starting scrape cycle".format(time.ctime()))
        try:
            do_scrape()
        except KeyboardInterrupt:
            print("Exiting....")
            sys.exit(1)
        except Exception as exc:
            print("Error with the scraping:", sys.exc_info()[0])
            traceback.print_exc()
        else:
            print("{}: Successfully finished scraping".format(time.ctime()))
        time.sleep(settings.SLEEP_INTERVAL)
```

为了控制抓取的频率，我们还需要添加`SLEEP_INTERVAL`到`settings.py`。默认情况下，它被设置成`20`分钟。

## 自己运行

现在，封装好代码了，让我们来看看如何自己运行Slack机器人。

### 在本地计算机上运行

你可以在[这里](https://github.com/vikparuchuri/apartment-finder)的Github路径上找到该项目。在[README.md](https://github.com/vikparuchuri/apartment-finder)中，你会找到详细的安装说明。除非你具有丰富的安装程序的经验，并且运行的是Linux，否则建议你遵循[Docker](https://www.docker.com/)的指示。Docker是一个使得创建和部署应用变得容易的工具，这让你在你的本地计算机上上手这个Slack机器人变得非常快速。

这里是使用Docker安装和运行Slack机器人的基本指导：

*   创建一个名为`config`的文件夹，然后讲一个名为`private.py`的文件放进去。

    *   你在`private.py`中指定的任何设置将会覆盖在`settings.py`中的默认设置。
    *   通过在`private.py`添加设置，你可以自定义机器人的行为。
*   在`private.py`上为任意配置指定新值。

    *   例如，你可以将`AREAS = ['sfc']`放到`private.py`中，设置只查找旧金山的房源。
    *   如果你想发布到一个不是叫`housing`的Slack频道中，那么添加一条`SLACK_CHANNEL`。
    *   如果你不想在湾区查找，那么至少你会需要更新以下设置：

        *   `CRAIGSLIST_SITE`
        *   `AREAS`
        *   `BOXES`
        *   `NEIGHBORHOODS`
        *   `TRANSIT_STATIONS`
        *   `CRAIGSLIST_HOUSING_SECTION`
        *   `MIN_PRICE`
        *   `MAX_PRICE`
*   遵循[这些指示](https://docs.docker.com/engine/installation/)来安装Docker
*   使用默认配置运行机器人：

    *   `docker run -d -e SLACK_TOKEN={YOUR_SLACK_TOKEN} dataquestio/apartment-finder`
*   使用自己的配置运行机器人：

    *   `docker run -d -e SLACK_TOKEN={YOUR_SLACK_TOKEN} -v {ABSOLUTE_PATH_TO_YOUR_CONFIG_FOLDER}:/opt/wwc/apartment-finder/config dataquestio/apartment-finder`

### 部署机器人

除非你想24/7开着你的电脑，否则将机器人部署到服务器会有意义的多，这样它就可以连续运行了。我们可以在托管服务提供商[DigitalOcean](https://www.digitalocean.com)上创建一个服务器。Digital Ocean可以自动创建一个[安装了Docker](https://www.digitalocean.com/features/one-click-apps/docker/)的服务器。

[这里是](https://www.digitalocean.com/community/tutorials/how-to-use-the-digitalocean-docker-application)关于如何在DigitalOcean开始使用Docker的指导。如果你不知道通过“shell”作者的意思是什么，那么[这里是](https://www.digitalocean.com/community/tutorials/how-to-connect-to-your-droplet-with-ssh)是一个关于如何SSH到DigitalOcean的教程。如果你不想遵循指南，那么你还可以[从这里](https://www.digitalocean.com/features/one-click-apps/docker/)开始。

在DigitalOcean创建好了一个服务器后，你可以ssh到该服务器上，然后按照上面的Docker安装和使用说明。

## 下一步

完成上述步骤后，你应该有一个Slack机器人，它可以自动为你找到公寓。使用这个机器人，Priya和我在旧金山发现了一个棒棒哒的公寓，它比我们希望的租金高，但也比我们认为在旧金山一间卧室的最终花费低。这也比我们预期花费的时间少了很多。尽管它对我们来说有用，但是对于提高机器人还有相当多可做的扩展：

*   从Slack点赞或差评，训练一个机器学习模型。
*   从API自动的拉取中转站的位置。
*   添加兴趣点，例如公园等等。
*   添加步行分数或者其他社区质量分数，例如犯罪。
*   自动提取房东电话号码和邮箱。
*   自动给房东打电话并定下看房时间 (如果有人这样做，那么你真棒)。

请随意在[Github](https://github.com/vikparuchuri/apartment-finder)上提交你的改动，如果这个工具对你有用的话，请在下面（Ele注：去原post哈~）留言。期待见到你是如何使用它的！

