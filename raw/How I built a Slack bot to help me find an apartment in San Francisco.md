<<<<<<< HEAD
原文：[How I built a Slack bot to help me find an apartment in San Francisco](https://www.dataquest.io/blog/apartment-finding-slackbot/)

---

几个月前，我从波士顿搬到湾区。Priya（我的女朋友）和我听到了租赁市场的各种恐怖故事。事实上，在谷歌上搜索“如何在旧金山找到一个公寓”得到[数十](https://www.google.com/search?q=how+to+find+an+apartment+in+san+francisco)页建议是对找房子是一个痛苦的过程的最好的暗示。

![](https://www.dataquest.io/blog/images/slack/boston.jpg)

Boston很冷，但在旧金山找房子是很可怕的


We read that landlords hold open houses, and that you have to bring all of your paperwork to the open house and be willing to put down a deposit immediately to even be considered.  We started exhaustively researching the process, and figured out that a lot of finding an apartment comes down to timing.  Some landlords want to hold an open house no matter what, but for others, being one of the first people to see the apartment usually means that you can get it. You eneed to find the listing, quickly figure out if it meets your criteria, then call the landlord to arrange a showing to have a shot.

We looked around at some of the apartment rental sites recommended by internet posters, like [Padmapper](https://www.padmapper.com/) and [LiveLovely](https://livelovely.com), but none of them gave us a feed of real-time listings that we could look at and rate together.  None of them gave us the ability to specify additional criteria, like very specific neighborhoods only, or proximity to transportation.  As most apartment listings in the Bay Area are originally on [Craigslist](https://www.craigslist.com), then scraped by the other sites, there’s also a fear that maybe not all the listings are scraped, or that they’re not scraped quickly enough to make the alerts real-time.

We wanted a way to:

*   Get notified in near real-time when a posting was made to Craigslist.
*   Filter out listings that didn’t fall into our desired neighborhoods.
*   Filter out listings that didn’t match additional criteria, like proximity to public transit.
*   Collaborate on listings and rate them together.
*   Easily get in touch with the landlord for listings we liked.

After thinking about the problem, I realized that we could solve the problem with a four step process:

*   Scrape listings from Craigslist.
*   Filter out listings that don’t match our criteria.
*   Post the listings to [Slack](https://slack.com/), a team chat tool, so we can discuss and rate them.
*   Wrap the whole process into a persistent loop and deploy it to a server (so it would run continuously).

In the rest of this post, we’ll walk through how each piece was built, and how the final Slack bot was used to help us find an apartment.  Using this bot, Priya and I found a reasonably priced (for SF!) one bedroom that we love in about a week, far less time than we thought it would take.

**If you want to look at the code as you go through this post, [here’s](https://github.com/VikParuchuri/apartment-finder) a link to the finished project, and [here’s](https://github.com/VikParuchuri/apartment-finder/blob/master/README.md) a link to the README.md.**

## 第一步 —— 从Craigslist抓取列表

The first step in building our bot is to get listings from Craiglist.  Craiglist unfortunately doesn’t have an API, but we can get posts using the [python-craiglist](https://github.com/juliomalegria/python-craigslist) package.  `python-craigslist` scrapes the content of the page, then uses [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/) to extract relevant pieces from the page, and convert it to structured data.  The code for the package is fairly short, and worth a read-through.

Craigslist apartment listings for San Francisco are located at `https://sfbay.craigslist.org/search/sfc/apa`.  In the below code, we:

*   Import `CraigslistHousing`, a class in `python-craigslist`.
*   Initialize the class with the following arguments:

    *   `site` – the Craigslist site that we want to scrape.  The `site` is the first part of the URL, like `https://sfbay.craigslist.org`.
    *   `area` – the subarea within the site that we want to scrape.  The `area` is the last part of a URL, like `https://sfbay.craigslist.org/sfc/`, which will only look in San Francisco.
    *   `category` – the type of listing we want to look for.  The `category` is the last part of a search URL, like `https://sfbay.craigslist.org/search/sfc/apa`, which lists all the apartments.
    *   `filters` – any filters we want to apply to the results.

        *   `max_price` – the maximum price we’re willing to pay.
        *   `min_price` – the minimum price we want to look for.
*   Get the results from Craigslist using the `get_results` method, which is a [generator](https://wiki.python.org/moin/Generators).

    *   Pass the `geotagged` argument to attempt to add coordinates to each result.
    *   Pass the `limit` argument to only get `20` results.
    *   Pass the `newest` argument to only get the newest listings.
*   Get each `result` from the `results` generator and print it.

```python
from craigslist import CraigslistHousing

cl = CraigslistHousing(site='sfbay', area='sfc', category='apa',
                         filters={'max_price': 2000, 'min_price': 1000})

results = cl.get_results(sort_by='newest', geotagged=True, limit=20)
for result in results:
    print result
```

We’ve finished the first step of the bot pretty quickly!  We’re now able to scrape Craigslist and get listings.  Each `result` is a dictionary with several fields:

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

*   `datetime` – when the listing was posted.
*   `geotag` – the coordinate location of the listing.
*   `has_image` – whether there’s an image in the Craigslist posting.
*   `has_map` – whether there’s a map associated with the listing.
*   `id` – the unique Craigslist id for the listing.
*   `name` – the name of the listing that shows up on Craigslist.
*   `price` – the monthly rent.
*   `url` – the URL to view the full listing.
*   `where` – what the person who created the listing put in for where it is.

## 第二步 —— 过滤结果

    Now that we have a way to get listings from Craigslist, we just need a way to filter them and only see the ones we like.

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

In order to filter by neighborhood, we’ll first need to define bounding boxes boxes around the areas:


![](https://www.dataquest.io/blog/images/slack/bounding_box.png)

Drawing a box around Lower Pacific Heights


The bounding box above was created using [BoundingBox](http://boundingbox.klokantech.com/).  Be sure to specify the `CSV` option in the bottom left to get the coordinates of the box.

You can also define a bounding box yourself by finding the coordinates for the bottom left and the top right using a tool like Google Maps.  After finding the boxes, we’ll create a dictionary of neighborhoods and coordinates:

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

Each dictionary key is a neighborhood name, and each key contains a list of lists.  The first inner list is the coordinates of the bottom left of the box, and the second is the coordinates of the top right of the box.  We can then perform the filtering by checking to see if the coordinates for a listing are inside any of the boxes.

下面的代码将：

*   Loop through each of the keys in `BOXES`.
*   Check to see if the result is inside the box.
*   Set the appropriate variables if so.

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

Unfortunately, not all results from Craigslist will have coordinates associated with them.  It’s up to the person who posts the listing to specify a location, which coordinates can be calculated from.  The more familiar the person posting the listing is with Craigslist, the more likely they are to include a location.  

Usually the listings that are posted by agents who are more likely to charge high rent have associated locations.  The postings by owners are more likely to not have coordinates, but are also usually better deals.  Thus, it makes sense to have a failsafe to figure out if listings without coordinates associated with them are in the neighborhoods we want.  We’ll create a list of neighborhoods, then do string matching to see if the listing falls into one of them.  This is less accurate than using coordinates, because many listings misreport their neighborhood, but it’s better than nothing:

```python
NEIGHBORHOODS = ["berkeley north", "berkeley", "rockridge", "adams point", ... ]
```

To do name-based matching, we can loop through each of the `NEIGHBORHOODS`:

```python
location = result["where"]
for hood in NEIGHBORHOODS:
    if hood in location.lower():
        area = hood
```

Once a result has been processed by the two parts of code we’ve written so far, we’ll have removed any listings that aren’t in the neighborhoods we want to live in.  We’ll have a few false positives, and we may miss some listings that don’t have a neighborhood or location specified, but this system catches the vast majority of listings.

### 通过靠近交通过滤结果

Priya and I knew we’d both be traveling to San Francisco a lot, so we wanted to live near public transit if we weren’t going to be SF.  In the Bay Area, the main form of public transit is called [BART](https://en.wikipedia.org/wiki/Bay_Area_Rapid_Transit).  BART is a partially underground regional transit system that connects Oakland, Berkeley, San Francisco, and the surrounding areas.

In order to build this functionality into our bot, we’ll first need to define a list of transit stations.  We can get the coordinates of transit stops from [Google Maps](https://maps.google.com), then create a dictionary of them:

```python
TRANSIT_STATIONS = {
    "oakland_19th_bart": [37.8118051,-122.2720873],
    "macarthur_bart": [37.8265657,-122.2686705],
    "rockridge_bart": [37.841286,-122.2566329],
    ...
}
```

Every key is the name of a transit station, and has an associated list.  The list contains the latitude and longitude of the transit station.  Once we have the dictionary, we find the closest transit station to each result.

下面的代码将：

*   Loop through each key and item in `TRANSIT_STATIONS`.
*   Use the `coord_distance` function to find the distance in kilometers between two pairs of coordinates.  You can find an explanation of this function [here](http://www.codecodex.com/wiki/Calculate_distance_between_two_points_on_a_globe#Python).
*   Check to see if the station is the closest to the listing.

    *   If the station is too far (farther than `2` kilometers, or about `1.2` miles), it is ignored.
    *   If the station is closer than the previous closest station, it’s used.

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

After this, we know the closest transit station to each listing.

## 第三步 —— 创建我们的Slack机器人

### 安装

After we filter down our results, we’re ready to post what we have to Slack.  If you’re unfamiliar with Slack, it’s a team chat application.  You create a team in Slack, and can then invite members.  Each Slack team can have multiple channels where members exchange messages.  Each message can be annotated by other people in the channel, such as adding a thumbs up or other emoticons.  [Here’s](https://slack.com/is) more information on Slack.  If you want to get a feel for Slack, we run a [data science Slack community](https://www.dataquest.io/chat) that you might like to join.

By posting our results to Slack, we’ll be able to collaborate with others and figure out which listings are the best.  To do this, we’ll need to:

*   Create a Slack team, which we can do [here](https://slack.com/create#email).*   Create a channel for the listings to be posted into.  [Here’s](https://get.slack.help/hc/en-us/articles/201402297-Creating-a-channel) help on this.  It’s suggested to use `#housing` as the name of the channel.
*   Get a Slack API token, which we can do [here](https://api.slack.com/docs/oauth-test-tokens).  [Here’s](https://get.slack.help/hc/en-us/articles/215770388-Creating-and-regenerating-API-tokens) more information on the process.

After these steps, we’re ready to create the code that posts the listings to Slack.

### 开始写代码

After getting the right channel name and token, we can post our results to Slack.  To do this, we’ll use [python-slackclient](https://github.com/slackhq/python-slackclient), a Python package that makes it easy to use the [Slack API](https://api.slack.com/).  `python-slackclient` is initialized using a Slack token, then gives us access to many API endpoints that manage the team and messages.

The below code will:

* Initialize a `SlackClient` using the `SLACK_TOKEN`.

* Create a message string from the `result` containing all the information we need to see, such as price, the neighborhood the listing is in, and the URL.

* Post the message to Slack with the username `pybot`, and a robot as an avatar.

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

Once everything is hooked up, the Slack bot will post listings into Slack that look like this:

![](https://www.dataquest.io/blog/images/slack/slack.png)

How listings will look when the bot is running.  Note how you can annotate listings with emoticons, like the thumbs up.


## 第四步 —— 开始运行

Now that we have the basics nailed out, we’ll need to run the code persistently.  After all, we want our results to be posted to Slack in real-time, or close to it.  In order to operationalize everything, we’ll need to go through a few steps:

*   Store the listings in the database, so we don’t post duplicates into Slack.
*   Separate the settings, like `SLACK_TOKEN`, from the rest of the code to make them easy to adjust.
*   Create a loop that will run continuously, so we’re scraping results 24/7.

### 保存清单

The first step is to use a Python package called [SQLAlchemy](http://www.sqlalchemy.org/) to store our listings.  SQLAlchemy is an [Object Relational Mapper](https://en.wikipedia.org/wiki/Object-relational_mapping), or ORM, that makes it easier to work with databases from Python.  Using SQLAlchemy, we can create a database table that will store listings, and a database connection to make it easy to add data to the table.

We’ll use SQLAlchemy in conjunction with the [SQLite](https://www.sqlite.org/) database engine, which will store all of our data into a single file called `listings.db`.

下面的代码将：

*   Import SQLAlchemy.
*   Create a connection to the SQLite database `listings.db` that will be created in our current directory.
*   Define a table called `Listing` that contains all the relevant fields from a Craigslist listing.

    *   The `unique` fields `cl_id` and `link` will prevent us from posting duplicate listings to Slack.
*   Create a database session from the connection, which will allow us to store listings.

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

Now that we have our database model, we’ll just need to store each listing into the database, and we’ll be able to avoid duplicates.

### 从代码中分离配置

The next step is to separate the configuration from the code.  We’ll create a file called `settings.py` that stores our configuration.  Configuration includes `SLACK_TOKEN`, which is a secret, and we don’t want to commit to git accidentally and push to Github, as well as other settings like `BOXES` that aren’t secret, but we want to be able to edit easily.

We’ll move the following settings to `settings.py`:

*   `MIN_PRICE` – the minimum listing price we want to search for.
*   `MAX_PRICE` – the minimum listing price we want to search for.
*   `CRAIGSLIST_SITE` – the regional Craigslist site we want to search in.
*   `AREAS` – a list of areas of the regional Craiglist site that we want to search in.
*   `BOXES` – coordinate boxes of the neighborhoods we want to look in.
*   `NEIGHBORHOODS` – if the listing doesn’t have coordinates, a list of neighborhoods to match on.
*   `MAX_TRANSIT_DIST` – the farthest we want to be from a transit station.
*   `TRANSIT_STATIONS` – the coordinates of transit stations.
*   `CRAIGSLIST_HOUSING_SECTION` – the subsection of Craigslist housing that we want to look in.
*   `SLACK_CHANNEL` – the Slack channel we want the bot to post in.

We’ll also want to create a file called `private.py`, that is ignored by git, and contains the following key:

*   `SLACK_TOKEN` – the token to post to our Slack team.

You can see the finished `settings.py` file [here](https://github.com/VikParuchuri/apartment-finder/blob/master/settings.py).

### 创建循环

Finally, we’ll need to create a loop that runs our scraping code continuously.  The below code will:

*   When called from the command line:

    *   Print a status message containing the current time.
    *   Run the craigslist scraping code by calling the `do_scrape` function.
    *   Quit if the user types `Ctrl + C`.
    *   Handle other exceptions by printing the traceback and continuing.
    *   If no exceptions, print a success message (corresponds to the `else` clause below).
    *   Sleeping for a defined interval before scraping again.  By default, this is set to `20` minutes.

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

We’ll also need to add `SLEEP_INTERVAL` to `settings.py` in order to control how often the scraping happens.  By default, this is set to `20` minutes.

## 自己运行

Now that the code is wrapped up, let’s look into how you can run the Slack bot yourself.

### 在本地计算机上运行

You can find the project on Github [here](https://github.com/vikparuchuri/apartment-finder).  In the [README.md](https://github.com/vikparuchuri/apartment-finder), you’ll find detailed installation instructions.  Unless you’re experienced installing programs, and are running Linux, it’s suggested to follow the [Docker](https://www.docker.com/) instructions.  Docker is a tool that makes it easy to create and deploy applications, and makes it very fast to get started with this Slack bot on your local machine.

Here are basic instructions for installing and running the Slack bot with Docker:

*   Create a folder called `config`, then put a file called `private.py` inside.

    *   Any settings you specify in `private.py` will override the defaults that are in `settings.py`.
    *   By adding settings in `private.py`, you can customize the behavior of the bot.
*   Specify new values for any of the settings above in `private.py`.

    *   For example, you could put `AREAS = ['sfc']` in `private.py` to only look in San Francisco.
    *   If you want to post into a Slack channel not called `housing`, add an entry for `SLACK_CHANNEL`.
    *   If you don’t want to look in the Bay Area, you’ll need to update the following settings at the minimum:

        *   `CRAIGSLIST_SITE`
        *   `AREAS`
        *   `BOXES`
        *   `NEIGHBORHOODS`
        *   `TRANSIT_STATIONS`
        *   `CRAIGSLIST_HOUSING_SECTION`
        *   `MIN_PRICE`
        *   `MAX_PRICE`
*   Install Docker by following [these instructions](https://docs.docker.com/engine/installation/).
*   To run the bot with the default configuration:

    *   `docker run -d -e SLACK_TOKEN={YOUR_SLACK_TOKEN} dataquestio/apartment-finder`
*   To run the bot with your own configuration:

    *   `docker run -d -e SLACK_TOKEN={YOUR_SLACK_TOKEN} -v {ABSOLUTE_PATH_TO_YOUR_CONFIG_FOLDER}:/opt/wwc/apartment-finder/config dataquestio/apartment-finder`

### 部署机器人

Unless you want to leave your computer on 24/7, it makes sense to deploy the bot to a server, so it can run continuously.  We can create a server on a hosting provider called [DigitalOcean](https://www.digitalocean.com).  Digital Ocean can automatically create a server with [Docker installed](https://www.digitalocean.com/features/one-click-apps/docker/).  

[Here’s](https://www.digitalocean.com/community/tutorials/how-to-use-the-digitalocean-docker-application) a guide on how to get started with Docker on DigitalOcean.  If you don’t know what the author means by “shell”, [here’s](https://www.digitalocean.com/community/tutorials/how-to-connect-to-your-droplet-with-ssh) a tutorial on how to SSH into a DigitalOcean droplet.  If you don’t want to follow a guide, you can also get started [here](https://www.digitalocean.com/features/one-click-apps/docker/).

After creating a server on DigitalOcean, you can ssh into the server, then follow the Docker installation and usage instructions above.

## 下一步

After following the steps above, you should have a Slack bot that finds apartments for you automatically.  Using this bot, Priya and I found a great apartment in San Francisco for more than we hoped to pay, but less than we thought a one bedroom in SF would end up costing.  It also took us a lot less time than we’d expected it to.  Even though it worked for us, there are quite a few extensions that could be made to improve the bot:

*   Taking thumbs up and thumbs down from Slack, and training a machine learning model.
*   Automatically pulling the locations of transit stops from an API.
*   Adding in points of interest like parks and other items.
*   Adding in the walkscore or other neighborhood quality scores, like crime.
*   Automatically extracting landlord phone numbers and emails.
*   Automatically calling landlords and scheduling showings (if someone does this, you’re awesome).

Feel free to submit pull requests to the project on [Github](https://github.com/vikparuchuri/apartment-finder), and please leave a comment here if this tool is helpful for you.  Looking forward to seeing how you use it!

