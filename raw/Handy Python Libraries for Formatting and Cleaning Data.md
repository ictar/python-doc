原文：[Handy Python Libraries for Formatting and Cleaning Data](https://blog.modeanalytics.com/python-data-cleaning-libraries/)

---

真实世界是杂乱的，它的数据也是。那么凌乱，[最近的一项调查](http://visit.crowdflower.com/data-science-report.html)显示，数据科学家花费60%的时间在清理数据。不幸的是，他们中57%还觉得这是他们工作中最不愉快的方面。

清理数据可能是耗时的，但是很多工具已经出现，让这个重要的任务惬意一点。Python社区提供了众多库，用来让数据有序清晰，从具有风格的DataFrame到匿名数据集。

Let us know which libraries you find useful—we're always looking to prioritize
which libraries to add to [Mode Python Notebooks](https://about.modeanalytics.com/python/).

![Scrub that Data](https://blog.modeanalytics.com/images/post-images/python-data-cleaning-libraries.png) _Too bad cleaning isn't as fun for data
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
[pandas DataFrame](https://community.modeanalytics.com/python/tutorial/pandas-dataframe/). From creator Randy Olson: “datacleaner is not magic, and it won't
take an unorganized blob of text and automagically parse it out for you.”

It will, however, drop rows with missing values, replace missing values with
the mode or median on a column-by-column basis, and encode non-numeric
variables with numerical equivalents. This library is fairly new, but since
DataFrames are fundamental to analysis in Python, it's worth checking out.

**Created by:** [Randy Olson](https://twitter.com/randal_olson)  
**Where to learn more:** <https://github.com/rhiever/datacleaner>

## PrettyPandas

DataFrames are powerful, but they don't produce the kind of tables you'd want
to show your boss. PrettyPandas makes use of the [pandas Style API](http://pandas.pydata.org/pandas-docs/stable/style.html) to transform
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
