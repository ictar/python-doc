[Home](https://blog.modeanalytics.com/)
[Product](https://about.modeanalytics.com/product/) [Data
Sources](https://about.modeanalytics.com/data-sources/)
[Customers](https://about.modeanalytics.com/customers/)
[Company](https://about.modeanalytics.com/company/)
[Jobs](https://about.modeanalytics.com/jobs/)
[Resources](https://about.modeanalytics.com/resources/) [SQL
School](http://sqlschool.modeanalytics.com)
[Playbook](https://about.modeanalytics.com/playbook/) [Sign
In](https://modeanalytics.com/signin)

[

](javascript://)

[ ![](https://about.modeanalytics.com/about/img/mode-logo.png)
](https://modeanalytics.com)

[Product](https://about.modeanalytics.com/product/)
[Pricing](https://about.modeanalytics.com/pricing/)
[Community](https://community.modeanalytics.com/)

[ More ![](https://blog.modeanalytics.com/images/triangle.png)
](javascript://)

[Data Sources](https://about.modeanalytics.com/data-sources/)
[Customers](https://about.modeanalytics.com/customers/)
[Company](https://about.modeanalytics.com/company/)
[Jobs](https://about.modeanalytics.com/jobs/)
[Blog](https://blog.modeanalytics.com) [Help](http://help.modeanalytics.com)

[Sign Up](http://modeanalytics.com/signup) [Sign
In](http://modeanalytics.com/signin)

[Mode Blog](https://blog.modeanalytics.com/)

#  [Handy Python Libraries for Formatting and Cleaning
Data](https://blog.modeanalytics.com/python-data-cleaning-libraries/)

August 23, 2016 | [Melissa Bierly](http://www.twitter.com/melissa_bierly) --
Content Marketing at Mode

The real world is messy, and so too is its data. So messy, that a [recent
survey](http://visit.crowdflower.com/data-science-report.html) reported data
scientists spend 60% of their time cleaning data. Unfortunately, 57% of them
also find it to be the least enjoyable aspect of their job.

Cleaning data may be time-consuming, but lots of tools have cropped up to make
this crucial duty a little more bearable. The Python community offers a host
of libraries for making data orderly and legible—from styling DataFrames to
anonymizing datasets.

Let us know which libraries you find useful—we're always looking to prioritize
which libraries to add to [Mode Python
Notebooks](https://about.modeanalytics.com/python/).

![Scrub that Data](https://blog.modeanalytics.com/images/post-images/python-
data-cleaning-libraries.png) _Too bad cleaning isn't as fun for data
scientists as it is for this little guy._

## Dora

Dora is designed for exploratory analysis; specifically, automating the most
painful parts of it, like feature selection and extraction, visualization,
and—you guessed it—data cleaning. Cleansing functions include:

  * Reading data with missing and poorly scaled values
  * Imputing missing values
  * Scaling values of input variables

**Created by:** [Nathan Epstein](https://twitter.com/epstein_n)  
**Where to learn more:** <https://github.com/NathanEpstein/Dora>

## datacleaner

Surprise, surprise, datacleaner cleans your data—but only once it's in a
[pandas DataFrame](https://community.modeanalytics.com/python/tutorial/pandas-
dataframe/). From creator Randy Olson: “datacleaner is not magic, and it won't
take an unorganized blob of text and automagically parse it out for you.”

It will, however, drop rows with missing values, replace missing values with
the mode or median on a column-by-column basis, and encode non-numeric
variables with numerical equivalents. This library is fairly new, but since
DataFrames are fundamental to analysis in Python, it's worth checking out.

**Created by:** [Randy Olson](https://twitter.com/randal_olson)  
**Where to learn more:** <https://github.com/rhiever/datacleaner>

## PrettyPandas

DataFrames are powerful, but they don't produce the kind of tables you'd want
to show your boss. PrettyPandas makes use of the [pandas Style
API](http://pandas.pydata.org/pandas-docs/stable/style.html) to transform
DataFrames into presentation-worthy tables. Create summaries, add styling, and
format numbers, columns, and rows. Added bonus: robust, easy-to-read
[documentation](http://prettypandas.readthedocs.io/en/latest/).

**Created by:** [Henry Hammond](https://twitter.com/henryhammond92)  
**Where to learn more:** <https://github.com/HHammond/PrettyPandas>

## tabulate

tabulate lets you print small, nice-looking tables with just one function
call. It's handy for making tables more readable with column alignment by
decimal, number formatting, headers, and more.

One of the coolest features is the ability to output data in a variety of
formats like HTML, PHP, or Markdown Extra, so you can continue working with
your tabular data in another tool or language.

**Created by:** Sergey Astanin  
**Where to learn more:** <https://pypi.python.org/pypi/tabulate>

## scrubadub

Data scientists in fields like healthcare and finance regularly have to
anonymize datasets. scrubadub removes [personally identifiable information
(PII)](https://en.wikipedia.org/wiki/Personally_identifiable_information) from
free text, such as:

  * Names (proper nouns)
  * Email addresses
  * URLs
  * Phone numbers
  * username/password combinations
  * Skype usernames
  * Social security numbers

The documentation does a good job of showing ways in which you might want to
customize scrubadub's behavior, like defining new PII types or excluding
certain kinds of PII from being scrubbed.

**Created by:** [Datascope Analytics](http://datascopeanalytics.com/)  
**Where to learn more:** <http://scrubadub.readthedocs.io/en/stable/index.html>

## Arrow

Let's be honest: working with dates and times in Python is a pain. Local
timezones aren't automatically recognized. It takes several lines of
unpleasant code to convert timezones and timestamps.

Arrow aims to fix these problems and plug functionality gaps to help you
handle dates and times with less code and fewer imports. Unlike Python's
standard library, Arrow is time-zone aware and UTC by default. You can convert
timezones or parse strings using one line of code.

**Created by:** [Chris Smith](https://twitter.com/crsmithdev)  
**Where to learn more:** <http://arrow.readthedocs.io/en/latest/>

## Beautifier

Beautifier's mission is simple: clean and prettify URLs and email addresses.
You can parse emails by domain and username; URLs by domain and parameters
(e.g. UTMs or tokens).

**Created by:** [Sachin Philip Mathew](https://twitter.com/sachin_philip)  
**Where to learn more:** <https://github.com/sachinvettithanam/beautifier>

## ftfy

ftfy (fixes text for you) takes in bad Unicode outputs good Unicode.
Basically, it fixes all the junk characters. `â€œquotesâ€\x9d` becomes
`"quotes"`; `uÌˆ` becomes `ü`; `&lt;3` becomes `<3`. If you work with text on
a daily basis, this library is, as one user says, “a handy piece of magic.”

**Created by:** [Luminoso](http://www.luminoso.com/)  
**Where to learn more:** <https://github.com/LuminosoInsight/python-ftfy>

## Further resources for wrangling data

Here are a couple of our favorite reads on munging/wrangling/cleansing data.

  * [What every data scientist should know about data anonymization](https://github.com/krasch/presentations/blob/master/pydata_Berlin_2016.pdf) (Katharina Rasch)
  * [Cleaning data in Python](https://data.library.utoronto.ca/cleaning-data-python) (University of Toronto Map &amp; Data Library)
  * [Data Cleaning with Python - MoMA's Artwork Collection](https://www.dataquest.io/blog/data-cleaning-with-python/) (Dataquest)

### Recommended articles

  * [Cohort Analysis That Helps You Look Ahead](https://blog.modeanalytics.com/cohort-analysis-helps-look-ahead/?utm_medium=recommended&utm_source=blog&utm_content=data_cleaning)
  * [10 Useful Python Data Visualization Libraries for Any Discipline](https://blog.modeanalytics.com/python-data-visualization-libraries/?utm_medium=recommended&utm_source=blog&utm_content=data_cleaning)
  * [Thinking in SQL vs Thinking in Python](https://blog.modeanalytics.com/learning-python-sql/?utm_medium=recommended&utm_source=blog&utm_content=data_cleaning)

Category: [Community](https://blog.modeanalytics.com/archive/community)

## Keep your finger on the pulse of analytics.

Each week we publish a roundup of the best analytics and data science content
we can find. Sign up here:

Thanks! Keep an eye on your email for the next issue of the Analytics
Dispatch!

Please enable JavaScript to view the [comments powered by
Disqus.](https://disqus.com/?ref_noscript)

### Next Article

## [Analytics Dispatch 037: End the language
war](https://blog.modeanalytics.com/analytics-dispatch-037/)

![](https://about.modeanalytics.com/about/img/mode-logo.png)

Product

[Overview](https://about.modeanalytics.com/product/)
[SQL](https://about.modeanalytics.com/online-sql-editor/)
[Python](https://about.modeanalytics.com/python/)
[Reporting](https://about.modeanalytics.com/reporting/)
[Pricing](https://about.modeanalytics.com/pricing/)
[Customers](https://about.modeanalytics.com/customers/) [Data
Sources](https://about.modeanalytics.com/data-sources/)
[Security](https://about.modeanalytics.com/security/)

Resources

[Community](https://community.modeanalytics.com) [Learn
SQL](https://community.modeanalytics.com/sql) [Learn
Python](https://community.modeanalytics.com/python) [Open Source
SQL](https://about.modeanalytics.com/playbook/) [Retention
Analytics](https://about.modeanalytics.com/improving-retention-rates/) [CRM
Analytics](https://about.modeanalytics.com/sales-analytics/) [Help +
Support](http://help.modeanalytics.com)

Company

[About](https://about.modeanalytics.com/company/)
[Careers](https://about.modeanalytics.com/jobs/)
[Press](https://about.modeanalytics.com/press/)
[Blog](http://blog.modeanalytics.com)

Contact Us

415-689-7436

208 Utah St. Suite 300

San Francisco CA 94103

[ ![Facebook](https://blog.modeanalytics.com/images/social-logos/facebook.png)
](https://www.facebook.com/ModeAnalytics) [
![Twitter](https://blog.modeanalytics.com/images/social-logos/twitter.png)
](https://twitter.com/modeanalytics) [
![LinkedIn](https://blog.modeanalytics.com/images/social-logos/linkedin.png)
](https://www.linkedin.com/company/mode-analytics) [
![GitHub](https://blog.modeanalytics.com/images/social-logos/github.png)
](https://github.com/mode)

(C) Mode Analytics, Inc. 2015 [terms of
service](https://about.modeanalytics.com/tos/) [privacy
policy](https://about.modeanalytics.com/privacy/)

