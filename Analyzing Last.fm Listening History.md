原文：[Analyzing Last.fm Listening History](http://geoffboeing.com/2016/05/analyzing-lastfm-history/)

---


Last.fm is a web site that tracks your music listening history across devices (computer, phone, iPod, etc) and services (Spotify, iTunes, Google Play, etc). I’ve been [using Last.fm](http://www.last.fm/user/gboeing) for nearly 10 years now, and my tracked listening history goes back even further when you consider all my pre-existing iTunes play counts that I _scrobbled_ (ie, submitted to my Last.fm database) when I joined Last.fm.

Using Python, pandas, matplotlib, and leaflet, I downloaded my listening history from Last.fm’s API, analyzed and visualized the data, downloaded full artist details from the [Musicbrainz](https://musicbrainz.org/) API, then geocoded and mapped all the artists I’ve played. All of my code used to do this is available in this [GitHub repo](https://github.com/gboeing/data-visualization/tree/master/lastfm-listening-history), and is easy to re-purpose for exploring your own Last.fm history. All you need is an [API key](http://www.last.fm/api).

[![Last.fm artists played the most](http://i2.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-artists-played-most.png?resize=570%2C439)](http://i2.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-artists-played-most.png)

First I visualized my most-played artists, above. Across the dataset, I have 263,428 scrobbles (aka, song plays). I’ve listened to 15,503 different artists and 53,632 different songs across 25,804 different albums from when I first started using iTunes circa 2005 through the present day. This includes pretty close to every song I’ve played on anything other than vinyl during that time.<span id="more-1799"></span>

I also mapped all the artists I’ve listened to. To do this, I took each artist ID in the Last.fm data set and passed it [to the Musicbrainz API](https://github.com/gboeing/data-visualization/blob/master/lastfm-listening-history/musicbrainz_downloader.ipynb) to get full artist details. Then I recursively queried the place until I got a full place name, like “Brixton, London, England, UK” (this process takes a while, and is perfectly suited to run on a [Raspberry Pi](http://geoffboeing.com/2016/03/scientific-python-raspberry-pi/)!). Next I [geocoded](https://github.com/gboeing/data-visualization/blob/master/lastfm-listening-history/musicbrainz_geocoder.ipynb) these place names to latitude-longitude using the Nominatim and Google APIs. Finally I mapped these points in Python with matplotlib [basemap](http://matplotlib.org/basemap/):

[![Map of Last.fm artist origins](http://i2.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm_artists_origins_map.png?resize=660%2C404)](http://i2.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm_artists_origins_map.png)

I also converted these [points to GeoJSON](https://github.com/gboeing/data-visualization/blob/master/lastfm-listening-history/musicbrainz_lastfm_leaflet.ipynb) to produce an interactive Leaflet web map of the artists I listen to (see this previous post for more on exporting [pandas DataFrames to GeoJSON](http://geoffboeing.com/2015/10/exporting-python-data-geojson/)). Click any point in the map below to see a list of artists from there:

<iframe src="http://geoffboeing.com/share/projects/leaflet-lastfm-artists/lastfm-artists-map.html" width="100%" height="400" scrolling="no"></iframe>

I predominately listen to artists from the populated areas of the U.S. and the [blue banana](https://en.wikipedia.org/wiki/Blue_Banana). But, this map is not fully representative of all the artists I’ve played, because many of them lack a Musicbrainz ID on Last.fm, and many who _do_ have an ID lack place information in the Musicbrainz database. This database also over-represents Western artists and artists listened to by Westerners, and under-represents other artists around the world (that appear in my listening history).

## Last.fm trends over time

I was curious about my most-played artists’ relative performance over time. So, I took the top six artists and charted their cumulative play counts since 2009:

[![Last.fm top artists](http://i2.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-scrobbles-top-artists-years.png?resize=660%2C389)](http://i2.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-scrobbles-top-artists-years.png)

David Bowie is the big winner here, moving from sixth place in 2009 all the way up to first place today as my most-played artist (since Last.fm sign-up). Note that these disaggregate data differ slightly from the aggregate play counts per artist earlier, because Last.fm here discards the lump-sum play counts from iTunes that I first scrobbled upon signing up. The disaggregate scrobbles with _dates_ thus only represent post-sign-up song plays.

I wanted to look more into these time dynamics of my listening history. How have they changed over the years? And, when exactly do I spend time listening to music? First I looked at my songs played per _month_ since January 2010:

[![Last.fm scrobbles per month](http://i1.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-scrobbles-per-month.png?resize=660%2C315)](http://i1.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-scrobbles-per-month.png)

Although there are a couple of big spikes, during most months I listen to somewhere around 1,000 to 2,500 songs. The peak during March 2015 coincides with my doctoral qualifying exams, which saw me sitting in my room about 16 hours day reading, writing… and listening to music. Next I looked at which _days_ of the week I do most of my listening:

[![Last.fm scrobbles per day of the week](http://i1.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-scrobbles-per-weekday.png?resize=450%2C350)](http://i1.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-scrobbles-per-weekday.png)

So, I listen to the most music on Fridays, and the least on Saturdays. The weekdays are all consistently higher as I tend to listen to music all day long while I’m working. The weekends are consistently lower as I tend to be out and about more, away from my computer and stereo. Next I looked at my cumulative listening history by _hour_ of the day:

[![Last.fm scrobbles by hour of the day](http://i0.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-scrobbles-per-hour.png?resize=600%2C324)](http://i0.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-scrobbles-per-hour.png)

This chart essentially follows my sleep, wake, work schedule. Most of my listening occurs during the mid-day while I’m working and tails off into the evening. But this aggregate pattern isn’t exactly same each day. Here I broke out the hourly chart above, by each day of the week:

[![Last.fm scrobbles by hour and day of week](http://i2.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-scrobbles-days-hours.png?resize=660%2C354)](http://i2.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-scrobbles-days-hours.png)

Now it’s easy to see the low days of Saturday and Sunday – but interestingly, Saturday has my highest play count late at night, when I’m up late PARTYING. Fridays at noon and Wednesdays at 3pm have the highest peaks. Also, my Monday mornings get off to slower starts than the rest of the work days, as I recover from the weekend.

## Artist names

For yuks, I looked at a couple traits of artist names. The first is the frequency of artist names beginning with each letter of the alphabet (sans a preceding “the”):

[![Last.fm artist names first letter prevalence](http://i1.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-artists-first-letter-count.png?resize=500%2C323)](http://i1.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-artists-first-letter-count.png)

S’s and M’s lead the pack, and Q’s and X’s bring up the rear. Next I looked at the frequency of artist name lengths:

[![Last.fm artist name character length](http://i2.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-artists-name-length.png?resize=500%2C324)](http://i2.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-artists-name-length.png)

Not everyone can be a name length outlier like [X](https://en.wikipedia.org/wiki/X_(American_band)) and [Orchestral Manoeuvres in the Dark](https://en.wikipedia.org/wiki/Orchestral_Manoeuvres_in_the_Dark).

## Top songs and albums on Last.fm

Finally, I’ll wrap this up similarly to how I started it by visualizing my most-played songs and albums of all-time on Last.fm. First, my most-played tracks:

[![Last.fm most played songs of all time](http://i1.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-tracks-played-most-h.png?resize=450%2C534)](http://i1.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-tracks-played-most-h.png)

And lastly, my most-played albums:

[![Last.fm most played albums](http://i2.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-albums-played-most-h.png?resize=450%2C718)](http://i2.wp.com/geoffboeing.com/wp-content/uploads/2016/05/lastfm-albums-played-most-h.png)

There are some common themes here: similar artists appear in both the most-played songs and most-played albums lists, unsurprisingly. There’s also a pretty clear correlation between the most-played albums and the number of tracks on the album, as these data are not normalized by the latter. It _might_ be possible to normalize album play counts by number of tracks on the album, by querying the MusicBrainz API for more information.

To recap, I [downloaded my listening history](https://github.com/gboeing/data-visualization/blob/master/lastfm-listening-history/lastfm_downloader.ipynb) from Last.fm, [analyzed and visualized](https://github.com/gboeing/data-visualization/blob/master/lastfm-listening-history/lastfm_analysis.ipynb) the data, [downloaded full artist details](https://github.com/gboeing/data-visualization/blob/master/lastfm-listening-history/musicbrainz_downloader.ipynb) from the Musicbrainz API, then [geocoded and mapped](https://github.com/gboeing/data-visualization/blob/master/lastfm-listening-history/musicbrainz_geocoder.ipynb) all the artists I’ve played, and finally dumped these [points to GeoJSON](https://github.com/gboeing/data-visualization/blob/master/lastfm-listening-history/musicbrainz_lastfm_leaflet.ipynb) for leaflet web mapping. All of my Python and leaflet code used to do this is available in this [GitHub repo](https://github.com/gboeing/data-visualization/tree/master/lastfm-listening-history), and is easy to re-purpose for exploring your own Last.fm history.

You might also be interested in:

*   Running scientific [Python on a Raspberry Pi](http://geoffboeing.com/2016/03/scientific-python-raspberry-pi/)
*   Exporting [pandas DataFrames to GeoJSON](http://geoffboeing.com/2015/10/exporting-python-data-geojson/)
*   Visualizing and mapping your [Foursquare check-in history](http://geoffboeing.com/2016/04/visualize-foursquare-history/)
*   Our [course at UC Berkeley](http://geoffboeing.com/2015/08/urban-informatics-visualization-berkeley/) that teaches these skills and tools
<div class="sharedaddy sd-sharing-enabled"><div class="robots-nocontent sd-block sd-social sd-social-icon sd-sharing">

### Share this:
<div class="sd-content">

*   [<span></span><span class="sharing-screen-reader-text">Click to share on Twitter (Opens in new window)</span>](http://geoffboeing.com/2016/05/analyzing-lastfm-history/?share=twitter&amp;nb=1 "Click to share on Twitter")
*   [<span><span class="share-count">9</span></span><span class="sharing-screen-reader-text">Click to share on Facebook (Opens in new window)<span class="share-count">9</span></span>](http://geoffboeing.com/2016/05/analyzing-lastfm-history/?share=facebook&amp;nb=1 "Click to share on Facebook")
*   [<span></span><span class="sharing-screen-reader-text">Click to share on Google+ (Opens in new window)</span>](http://geoffboeing.com/2016/05/analyzing-lastfm-history/?share=google-plus-1&amp;nb=1 "Click to share on Google+")
*   [<span></span><span class="sharing-screen-reader-text">Click to share on LinkedIn (Opens in new window)</span>](http://geoffboeing.com/2016/05/analyzing-lastfm-history/?share=linkedin&amp;nb=1 "Click to share on LinkedIn")
*   [<span>More</span>](#)
*<div class="sharing-hidden"><div class="inner" style="display: none;">

*   [<span></span><span class="sharing-screen-reader-text">Click to share on Pinterest (Opens in new window)</span>](http://geoffboeing.com/2016/05/analyzing-lastfm-history/?share=pinterest&amp;nb=1 "Click to share on Pinterest")
*   [<span></span><span class="sharing-screen-reader-text">Click to share on Tumblr (Opens in new window)</span>](http://geoffboeing.com/2016/05/analyzing-lastfm-history/?share=tumblr&amp;nb=1 "Click to share on Tumblr")
**   [<span></span><span class="sharing-screen-reader-text">Click to share on Reddit (Opens in new window)</span>](http://geoffboeing.com/2016/05/analyzing-lastfm-history/?share=reddit&amp;nb=1 "Click to share on Reddit")
*   [<span></span><span class="sharing-screen-reader-text">Click to share on Pocket (Opens in new window)</span>](http://geoffboeing.com/2016/05/analyzing-lastfm-history/?share=pocket&amp;nb=1 "Click to share on Pocket")
**   [<span></span><span class="sharing-screen-reader-text">Click to email this to a friend (Opens in new window)</span>](http://geoffboeing.com/2016/05/analyzing-lastfm-history/?share=email&amp;nb=1 "Click to email this to a friend")
*</div></div></div></div></div> </div>