原文：[Analyzing iPhone Step Data](http://blog.yhat.com/posts/phone-steps-timeseries.html)

---

### Confession

My name is Ross, and I'm addicted to counting steps. The walking kind. This obsession entails frequently opening the counting app on my iPhone to watch the step count climb and ensure I'm getting over 10,000 (my mom says that's the magic number). Luckily, living in NYC makes the goal easily achievable on most days.

In this post I'll show you how I analyzed my iPhone step data using [pandas](http://pandas.pydata.org/) timeseries and [ggplot](http://github.com/yhat/ggplot).
I did all of my data-sciencing in Python using [Rodeo](https://www.yhat.com/products/rodeo), [Yhat's](https://www.yhat.com/) IDE for data science.

### Collecting Data

Like any legitimate data nerd, I wanted to be able to export my data for analysis
outside my phone. The smart people over at Quantified Self Labs put out an app
called [QS Access](http://quantifiedself.com/access-app/app) that makes retrieving
this data a cinch!

Here are a couple of screenshots of exporting the data.

<center>
  ![](/static/img/steps-app-screenshot.png)
</center>

The QS Access app exports a CSV containing three columns: a `start` timestamp, a `finish` timestamp, and the `steps (count)`
during that period. There's an option to produce rows of hourly or daily data. Why not start with hours and see how it goes - bigger data is always better, right?

### TO THE DATAS!

Our analysis will draw on the time series tools built into [pandas](http://pandas.pydata.org/). When [Wes McKinney](https://github.com/wesm)
started the pandas project, he was working for an investment management company and this industry relies extensively on time series analysis. As a result, pandas ships with comprehensive functionality in this area.

A couple other notes about importing this data while we're here.

First, we already know that we have time series data, so we can let pandas know by using the `parse_dates` parameter.

The end time data in the CSV isn't particularly interesting because we have the start time
 and know that we have hourly frequency so we can omit it with `usecols`.

Last, setting the start time (col 0) to be the index column gives a `DateTimeIndex` and will make our job easier later.

    <span class="pln">df_hour </span><span class="pun">=</span><span class="pln"> pd</span><span class="pun">.</span><span class="pln">read_csv</span><span class="pun">(</span><span class="str">'health_data_hour.csv'</span><span class="pun">,</span><span class="pln"> parse_dates</span><span class="pun">=[</span><span class="lit">0</span><span class="pun">,</span><span class="lit">1</span><span class="pun">],</span><span class="pln"> names</span><span class="pun">=[</span><span class="str">'start_time'</span><span class="pun">,</span><span class="pln"> </span><span class="str">'steps'</span><span class="pun">],</span><span class="pln"> usecols</span><span class="pun">=[</span><span class="lit">0</span><span class="pun">,</span><span class="pln"> </span><span class="lit">2</span><span class="pun">],</span><span class="pln"> skiprows</span><span class="pun">=</span><span class="lit">1</span><span class="pun">,</span><span class="pln"> index_col</span><span class="pun">=</span><span class="lit">0</span><span class="pun">)</span><span class="pln">
    </span><span class="com"># ensure the steps col are ints - weirdness going on with this one</span><span class="pln">
    df_hour</span><span class="pun">.</span><span class="pln">steps </span><span class="pun">=</span><span class="pln"> df_hour</span><span class="pun">.</span><span class="pln">steps</span><span class="pun">.</span><span class="pln">apply</span><span class="pun">(</span><span class="kwd">lambda</span><span class="pln"> x</span><span class="pun">:</span><span class="pln"> </span><span class="kwd">int</span><span class="pun">(</span><span class="kwd">float</span><span class="pun">(</span><span class="pln">x</span><span class="pun">)))</span><span class="pln">
    df_hour</span><span class="pun">.</span><span class="pln">head</span><span class="pun">()</span><span class="pln">
    type</span><span class="pun">(</span><span class="pln">df_hour</span><span class="pun">.</span><span class="pln">index</span><span class="pun">)</span><span class="pln">
    type</span><span class="pun">(</span><span class="pln">df_hour</span><span class="pun">.</span><span class="pln">steps</span><span class="pun">[</span><span class="lit">1</span><span class="pun">])</span>`</pre>
    <table border="1" class="dataframe" style="width:auto;text-align:center">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>steps</th>
        </tr>
        <tr>
          <th>start_time</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>2014-10-24 18:00:00</th>
          <td>459</td>
        </tr>
        <tr>
          <th>2014-10-24 19:00:00</th>
          <td>93</td>
        </tr>
        <tr>
          <th>2014-10-24 20:00:00</th>
          <td>421</td>
        </tr>
        <tr>
          <th>2014-10-24 21:00:00</th>
          <td>1306</td>
        </tr>
        <tr>
          <th>2014-10-24 22:00:00</th>
          <td>39</td>
        </tr>
      </tbody>
    </table>

    Notice that the type of the start_time column: `pandas.tseries.index.DatetimeIndex`. This is due to setting the index column during the data ingest, and it gives us access to all sorts of goodies - resampling for one, as we'll see later.

    ### Hourly Step Count

    How about a quick [(gg)plot](http://github.com/yhat/ggplot) to explore the data we have here.

    ![](../static/img/hourly_step_plot.png)

    Yuck! That's a little too busy. How can we improve our visualization? I've got an idea - pandas has a function called `resample` that will allow us to aggregate the data over a longer duration.

    More precisely, this is called **downsampling** when you reduce the sampling rate of a given signal.
    For this example, we will take the hourly data, and resample it on a daily, weekly,
    and monthly basis using the mean and sum aggregations.

    ### Downsampling to Daily Step Count

    Let's start with the daily totals (notice that you can pass the dataframe `__index__` into the ggplot function):

    <pre class="prettyprint prettyprinted">`<span class="pln">df_daily </span><span class="pun">=</span><span class="pln"> pd</span><span class="pun">.</span><span class="typ">DataFrame</span><span class="pun">()</span><span class="pln">
    df_daily</span><span class="pun">[</span><span class="str">'step_count'</span><span class="pun">]</span><span class="pln"> </span><span class="pun">=</span><span class="pln"> df_hour</span><span class="pun">.</span><span class="pln">steps</span><span class="pun">.</span><span class="pln">resample</span><span class="pun">(</span><span class="str">'D'</span><span class="pun">).</span><span class="pln">sum</span><span class="pun">()</span><span class="pln">
    df_daily</span><span class="pun">.</span><span class="pln">head</span><span class="pun">()</span><span class="pln">
    p </span><span class="pun">=</span><span class="pln"> ggplot</span><span class="pun">(</span><span class="pln">df_daily</span><span class="pun">,</span><span class="pln"> aes</span><span class="pun">(</span><span class="pln">x</span><span class="pun">=</span><span class="str">'__index__'</span><span class="pun">,</span><span class="pln"> y</span><span class="pun">=</span><span class="str">'step_count'</span><span class="pun">))</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> \
        geom_step</span><span class="pun">()</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> \
        stat_smooth</span><span class="pun">()</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> \
        scale_x_date</span><span class="pun">(</span><span class="pln">labels</span><span class="pun">=</span><span class="str">"%m/%Y"</span><span class="pun">)</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> \
        ggtitle</span><span class="pun">(</span><span class="str">"Daily Step Count"</span><span class="pun">)</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> \
        xlab</span><span class="pun">(</span><span class="str">"Date"</span><span class="pun">)</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> \
        ylab</span><span class="pun">(</span><span class="str">"Steps"</span><span class="pun">)</span><span class="pln">
    </span><span class="kwd">print</span><span class="pln"> p</span>`</pre>

    ![](../static/img/daily_step_plot.png)

    Ah-ha! We're getting somewhere now.  That's a much more readable plot.
    **And** it looks like there's a nice upward trend (we'll get to that later).

    ### Downsampling to Weekly and Monthly Step Count

    Armed with this, we're able to do weekly and monthly resampling easily as well.
    Just pass `'W'` or `'M'` into the resample function.

    Because I'm most interested in the daily step total metric,
    we can start using an average aggregation function to get a daily
    average during the weekly and monthly samples (got to get those 10,000 a day!).
    That just takes changing the `sum()` function after the `resample` to a `mean()`.

    It looks like this:

    <pre class="prettyprint prettyprinted">`<span class="pln">df_weekly</span><span class="pun">[</span><span class="str">'step_mean'</span><span class="pun">]</span><span class="pln"> </span><span class="pun">=</span><span class="pln"> df_daily</span><span class="pun">.</span><span class="pln">step_count</span><span class="pun">.</span><span class="pln">resample</span><span class="pun">(</span><span class="str">'W'</span><span class="pun">).</span><span class="pln">mean</span><span class="pun">()</span><span class="pln">
    df_monthly</span><span class="pun">[</span><span class="str">'step_mean'</span><span class="pun">]</span><span class="pln"> </span><span class="pun">=</span><span class="pln"> df_daily</span><span class="pun">.</span><span class="pln">step_count</span><span class="pun">.</span><span class="pln">resample</span><span class="pun">(</span><span class="str">'M'</span><span class="pun">).</span><span class="pln">mean</span><span class="pun">()</span>`</pre>

    ![](../static/img/weekly_step_mean_plot.png)
    ![](../static/img/monthly_step_mean_plot.png)

    Brief aside: Pandas can also do the opposite of what we just did; called upsampling.
    Take a look at [the docs](http://pandas.pydata.org/pandas-docs/version/0.17.0/generated/pandas.Panel.resample.html) if you need that for your project.

    ### Going (slightly) deeper

    ![](../static/img/go-deeper.jpg)

    I'm curious if I'm getting more steps during the weekend than during the week.
    We can use the tab suggestions in [Rodeo](https://www.yhat.com/products/rodeo) to take a look at the methods we have available on the DateTimeIndex.

    There happen to be `weekday` and `weekday_name` methods, which sound useful.
    The former will give an integer corresponding to a day of the week,
    while the latter will give the string name of that day. After we make a new column with that
    info, applying a helper function to it can return a boolean value for if that is a weekend or not.

    <pre class="prettyprint prettyprinted">`<span class="kwd">def</span><span class="pln"> weekendBool</span><span class="pun">(</span><span class="pln">day</span><span class="pun">):</span><span class="pln">
        </span><span class="kwd">if</span><span class="pln"> day </span><span class="kwd">not</span><span class="pln"> </span><span class="kwd">in</span><span class="pln"> </span><span class="pun">[</span><span class="str">'Saturday'</span><span class="pun">,</span><span class="pln"> </span><span class="str">'Sunday'</span><span class="pun">]:</span><span class="pln">
            </span><span class="kwd">return</span><span class="pln"> </span><span class="kwd">False</span><span class="pln">
        </span><span class="kwd">else</span><span class="pun">:</span><span class="pln">
            </span><span class="kwd">return</span><span class="pln"> </span><span class="kwd">True</span><span class="pln">

    df_daily</span><span class="pun">[</span><span class="str">'weekday'</span><span class="pun">]</span><span class="pln"> </span><span class="pun">=</span><span class="pln"> df_daily</span><span class="pun">.</span><span class="pln">index</span><span class="pun">.</span><span class="pln">weekday
    df_daily</span><span class="pun">[</span><span class="str">'weekday_name'</span><span class="pun">]</span><span class="pln"> </span><span class="pun">=</span><span class="pln"> df_daily</span><span class="pun">.</span><span class="pln">index</span><span class="pun">.</span><span class="pln">weekday_name
    df_daily</span><span class="pun">[</span><span class="str">'weekend'</span><span class="pun">]</span><span class="pln"> </span><span class="pun">=</span><span class="pln"> df_daily</span><span class="pun">.</span><span class="pln">weekday_name</span><span class="pun">.</span><span class="pln">apply</span><span class="pun">(</span><span class="pln">weekendBool</span><span class="pun">)</span><span class="pln">
    df_daily</span><span class="pun">.</span><span class="pln">head</span><span class="pun">()</span>`</pre>
    <table border="1" class="dataframe" style="width:auto;text-align:center">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>step_count</th>
          <th>weekday</th>
          <th>weekday_name</th>
          <th>weekend</th>
        </tr>
        <tr>
          <th>start_time</th>
          <th></th>
          <th></th>
          <th></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>2014-10-24</th>
          <td>2333</td>
          <td>4</td>
          <td>Friday</td>
          <td>False</td>
        </tr>
        <tr>
          <th>2014-10-25</th>
          <td>3085</td>
          <td>5</td>
          <td>Saturday</td>
          <td>True</td>
        </tr>
        <tr>
          <th>2014-10-26</th>
          <td>21636</td>
          <td>6</td>
          <td>Sunday</td>
          <td>True</td>
        </tr>
        <tr>
          <th>2014-10-27</th>
          <td>13776</td>
          <td>0</td>
          <td>Monday</td>
          <td>False</td>
        </tr>
        <tr>
          <th>2014-10-28</th>
          <td>5732</td>
          <td>1</td>
          <td>Tuesday</td>
          <td>False</td>
        </tr>
      </tbody>
    </table>

    ggplot has a stat_density plot available that's perfect for comparing the weekend vs. weekday populations.  Check it out:

    <pre class="prettyprint prettyprinted">`<span class="pln">ggplot</span><span class="pun">(</span><span class="pln">aes</span><span class="pun">(</span><span class="pln">x</span><span class="pun">=</span><span class="str">'step_count'</span><span class="pun">,</span><span class="pln"> color</span><span class="pun">=</span><span class="str">'weekend'</span><span class="pun">),</span><span class="pln"> data</span><span class="pun">=</span><span class="pln">df_daily</span><span class="pun">)</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> \
        stat_density</span><span class="pun">()</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> \
        ggtitle</span><span class="pun">(</span><span class="str">"Comparing Weekend vs. Weekday Daily Step Count"</span><span class="pun">)</span><span class="pln"> </span><span class="pun">+</span><span class="pln"> \
        xlab</span><span class="pun">(</span><span class="str">"Step Count"</span><span class="pun">)</span>`</pre>

    ![](../static/img/weekend_density_plot.png)

    We can also group the data on this weekend_bool and run some aggregation methods to see the differences in the data.  Have a look at my previous post on [grouping in padas](http://blog.yhat.com/posts/grouping-pandas.html) for an explanation of this functionality.

    <pre class="prettyprint prettyprinted">`<span class="pln">weekend_grouped </span><span class="pun">=</span><span class="pln"> df_daily</span><span class="pun">.</span><span class="pln">groupby</span><span class="pun">(</span><span class="str">'weekend'</span><span class="pun">)</span><span class="pln">
    weekend_grouped</span><span class="pun">.</span><span class="pln">describe</span><span class="pun">()</span><span class="pln">

                     step_count     weekday
    weekend
    </span><span class="kwd">False</span><span class="pln">   count    </span><span class="lit">479.000000</span><span class="pln">  </span><span class="lit">479.000000</span><span class="pln">
            mean   </span><span class="lit">10145.832985</span><span class="pln">    </span><span class="lit">1.997912</span><span class="pln">
            std     </span><span class="lit">4962.913936</span><span class="pln">    </span><span class="lit">1.416429</span><span class="pln">
            min      </span><span class="lit">847.000000</span><span class="pln">    </span><span class="lit">0.000000</span><span class="pln">
            </span><span class="lit">25</span><span class="pun">%</span><span class="pln">     </span><span class="lit">6345.000000</span><span class="pln">    </span><span class="lit">1.000000</span><span class="pln">
            </span><span class="lit">50</span><span class="pun">%</span><span class="pln">     </span><span class="lit">9742.000000</span><span class="pln">    </span><span class="lit">2.000000</span><span class="pln">
            </span><span class="lit">75</span><span class="pun">%</span><span class="pln">    </span><span class="lit">13195.000000</span><span class="pln">    </span><span class="lit">3.000000</span><span class="pln">
            max    </span><span class="lit">37360.000000</span><span class="pln">    </span><span class="lit">4.000000</span><span class="pln">
    </span><span class="kwd">True</span><span class="pln">    count    </span><span class="lit">192.000000</span><span class="pln">  </span><span class="lit">192.000000</span><span class="pln">
            mean   </span><span class="lit">11621.166667</span><span class="pln">    </span><span class="lit">5.500000</span><span class="pln">
            std     </span><span class="lit">7152.197426</span><span class="pln">    </span><span class="lit">0.501307</span><span class="pln">
            min      </span><span class="lit">641.000000</span><span class="pln">    </span><span class="lit">5.000000</span><span class="pln">
            </span><span class="lit">25</span><span class="pun">%</span><span class="pln">     </span><span class="lit">6321.000000</span><span class="pln">    </span><span class="lit">5.000000</span><span class="pln">
            </span><span class="lit">50</span><span class="pun">%</span><span class="pln">    </span><span class="lit">10228.000000</span><span class="pln">    </span><span class="lit">5.500000</span><span class="pln">
            </span><span class="lit">75</span><span class="pun">%</span><span class="pln">    </span><span class="lit">15562.500000</span><span class="pln">    </span><span class="lit">6.000000</span><span class="pln">
            max    </span><span class="lit">35032.000000</span><span class="pln">    </span><span class="lit">6.000000</span><span class="pln">

    weekend_grouped</span><span class="pun">.</span><span class="pln">median</span><span class="pun">()</span><span class="pln">
                step_count  weekday
    weekend
    </span><span class="kwd">False</span><span class="pln">          </span><span class="lit">9742</span><span class="pln">      </span><span class="lit">2.0</span><span class="pln">
    </span><span class="kwd">True</span><span class="pln">          </span><span class="lit">10228</span><span class="pln">      </span><span class="lit">5.5</span>

With an average of 11,621 steps on the weekend (and a median of 10,228) versus
10,146 (median = 9,742) on the weekdays, it looks like a slight edge goes to the weekends!

### Now Trending

Let's get back to the upward trend.

I moved from Charlotte, NC, to New York City at the beginning of April
to work as a software engineer at [Yhat](https://www.yhat.com/).

I'm curious what effect that this location change had on my daily step count.
We can apply the same methodology as the weekend data to figure this out.

I'll just give you the good stuff:

![](../static/img/monthly_step_mean_plot_with_NYC_line.png)

![](../static/img/nyc_step_compare_plot.png)

That plot makes it look like there's a significant change after moving to NYC.
There are more variables than just the location change, like the fact that I just
started running more seriously, which would add some skew, but controlling for that effect would
require more data.  Maybe for another post!

### Final thoughts

Hopefully, this analysis was enough to get you started with checking out your step count data,
using [Rodeo](https://www.yhat.com/products/rodeo) to do some data-sciencing,
and exploring the time series functionality of [pandas](http://pandas.pydata.org/)! Have a look at [my repo](https://github.com/rkipp1210/data-projects) for this project if you want to check out the source.

Now, back to counting those steps.

        </div>