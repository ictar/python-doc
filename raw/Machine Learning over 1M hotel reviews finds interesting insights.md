原文：[Machine Learning over 1M hotel reviews finds interesting insights](https://blog.monkeylearn.com/machine-learning-1m-hotel-reviews-finds-interesting-insights/)

---

On a [previous post](https://blog.monkeylearn.com/creating-aspect-classifier-
from-reviews-using-machine-learning/) we learned how to train a machine
learning classifier that is able to detect the different aspects mentioned on
hotel reviews. With this [aspect
classifier](https://app.monkeylearn.com/main/classifiers/cl_TKb7XmdG/), we
were able to automatically know if a particular review was talking about
cleanliness, comfort &amp; facilities, food, Internet, location, staff and/or
value for money.

We also learned how to combine this classifier with the [sentiment analysis
classifier](https://blog.monkeylearn.com/creating-sentiment-analysis-model-
with-scrapy-and-monkeylearn/) to get interesting insights and answer questions
like are guests loving the location of a particular hotel but complaining
about its cleanliness?

On this post we will cover how we can use these machine learning models to
analyze millions of reviews from TripAdvisor and then compare how people feel
about hotels in different cities to understand things like:

  * Do people who stay in hotels in Bangkok complain more about cleanliness than those that stay in, say, hotels in Paris?
  * What is the city with the worst facilities?
  * Does the number of [stars of a hotel](https://en.wikipedia.org/wiki/Hotel_rating) impact its reviews?
  * Do people have different standards when it comes to hotels of different classes?

These are the kind of questions we aim to answer with this tutorial and that
will lead us to some interesting insights. The source code used for this
process is available in this [repository](https://github.com/monkeylearn
/hotel-review-analysis).

## Scraping the hotel reviews

We created a new version of the [TripAdvisor
Spider](https://github.com/monkeylearn/hotel-review-analysis) that we had
built on a [previous post](https://blog.monkeylearn.com/creating-sentiment-
analysis-model-with-scrapy-and-monkeylearn/), one that collects more data from
a review:

  * The name of the hotel.
  * The city where the hotel is located.
  * The stars the hotel has (given by the reviewers).

## Creating a Pipeline to combine the models

After scraping more than 1 million of reviews from TripAdvisor with the new
spider, we split the content into opinion units and classified them in a
similar way we had done [last time around](https://blog.monkeylearn.com
/creating-aspect-classifier-from-reviews-using-machine-learning/) . The big
difference is that now we created a
[pipeline](http://docs.monkeylearn.com/article/what-are-pipelines/) that
combines both classifiers. Pipelines are very powerful and versatile tools
that allows you to combine different modules within MonkeyLearn and thanks to
them we can classify the reviews for both aspect and sentiment with a single
request.

This is what it looks like to classify opinion units using the pipeline:

Python

from monkeylearn import MonkeyLearn ml = MonkeyLearn("&lt;your api key
here&gt;") data = { "texts": [{"text": "The room was very clean"}, {"text":
"very rude staff"}] } res = ml.pipelines.run('pi_YKStimMw', data,
sandbox=False)

1

2

3

4

5

6

|

from monkeylearn import MonkeyLearn

ml = MonkeyLearn("&lt;your api key here&gt;")

data = {

"texts": [{"text": "The room was very clean"}, {"text": "very rude staff"}]

}

res = ml.pipelines.run('pi_YKStimMw', data, sandbox=False)  
  
---|---  
  
Simple huh? Then, res.result is a JSON that looks like this:

JavaScript

{ 'tags': [{ 'sentiment': [{ 'category_id': 102881, 'label': 'Good',
'probability': 1.0 }], 'topic': [ [{ 'category_id': 1495678, 'label':
'Cleanliness', 'probability': 1.0 }] ] }, { 'sentiment': [{ 'category_id':
102882, 'label': 'Bad', 'probability': 1.0 }], 'topic': [ [{ 'category_id':
1495676, 'label': 'Staff', 'probability': 1.0 }] ] }] }

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

|

{

    'tags': [{

   'sentiment': [{

   'category_id': 102881,

   'label': 'Good',

   'probability': 1.0

   }],

   'topic': [

   [{

   'category_id': 1495678,

   'label': 'Cleanliness',

   'probability': 1.0

   }]

   ]

    }, {

   'sentiment': [{

   'category_id': 102882,

   'label': 'Bad',

   'probability': 1.0

   }],

   'topic': [

   [{

   'category_id': 1495676,

   'label': 'Staff',

   'probability': 1.0

   }]

   ]

    }]

}  
  
---|---  
  
There’s an item for each review sent to the pipeline, and each one has a
sentiment and a list of topics (aspects).

When saving these results to a CSV file, we also added a link to its parent
review with a key generated from a hash of the review text. So, we now had two
files: one with the reviews, which included the metadata for each review that
we scraped (city, hotel location, stars, etc), and one with the classified
opinion units, the sentiment and topic of each unit, the probability of the
sentiment, and a link to its parent review.

## Indexing the results with Elasticsearch + Kibana visualizations

But we didn’t stop there. We then indexed the results with
[Elasticsearch](https://www.elastic.co/products/elasticsearch) and loaded them
into [Kibana](https://www.elastic.co/products/kibana) in order to generate
beautiful visualizations.

This was pretty straightforward. First, we installed Elasticsearch and got it
running as a local instance. Some trial and error followed for getting the
best model for the data, we used
[sense](https://chrome.google.com/webstore/detail/sense-
beta/lhjgkmllcaadmopgmanpapmpjgmfcfig?hl=en) for doing that. It’s a great
Chrome extension that lets you interact with elastic’s API on a nice interface
instead of using cURL. We created an index with two types: _review_ and
_opinion_unit_. The fields of each type were the fields of the csv, and using
the __parent_ property we added a link from each opinion unit to its parent
review. Finally, we indexed the data from the csv files using the [python
SDK](https://www.elastic.co/guide/en/elasticsearch/client/python-
api/current/index.html).

Using the SDK to index an opinion unit is very simple. You just need to create
a Python dictionary with all the fields of the item, and send it to
elasticsearch using the SDK. Doing it by bulk is faster than doing it an item
at a time:

Python

es = Elasticsearch(['http://localhost:9200']) actions = [] parent_key =
'bd1ed398a8529d5ad010d927d5af7240' opinion_unit = "The room was very clean"
sentiment = "Good" topic = "Cleanliness" item = [parent_key, opinion_unit,
sentiment, topic] action = { "_index": "index_hotels", "_type":
"opinion_unit", "_id": cont_id, "_parent": parent_key, "_source": item }
actions.append(action) helpers.bulk(es, actions)

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

|

es = Elasticsearch(['http://localhost:9200'])

actions = []



parent_key = 'bd1ed398a8529d5ad010d927d5af7240'

opinion_unit = "The room was very clean"

sentiment = "Good"

topic = "Cleanliness"

item = [parent_key, opinion_unit, sentiment, topic]

action = {

         "_index": "index_hotels",

         "_type": "opinion_unit",

         "_id": cont_id,

         "_parent": parent_key,

         "_source": item

         }

actions.append(action)



helpers.bulk(es, actions)  
  
---|---  
  
Afterwards, we installed Kibana and configured it to point to our local
Elasticsearch instance. In order to create the graphs shown in the following
section, we used JSON based queries instead of Lucene because we needed to use
the _has_parent_ and _has_child_ clauses.

The following is a query that selects the opinion units from New York. It uses
a filter with a _has_parent_ clause, which means that it will only match
elements (opinion units) that have a parent element (a review) that satisfy
the requirement (being from New York). It also requires the opinion unit’s
sentiment to be classified with a probability above a certain threshold, which
will improve the quality of the result:

JavaScript

{ "query": { "filtered": { "query": { "match_all": { "range": {
"sent_probability": { "gt": "0.501" } } } }, "filter": { "has_parent": {
"type": "review", "query": { "match": { "city": "New York City" } } } } } } }

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

|

{

    "query": {

   "filtered": {

   "query": {

   "match_all": {

   "range": {

   "sent_probability": {

   "gt": "0.501"

   }

   }

   }

   },

   "filter": {

   "has_parent": {

   "type": "review",

   "query": {

   "match": {

   "city": "New York City"

   }

   }

   }

   }

   }

    }

}  
  
---|---  
  
Most of the queries used for creating the graphs are similar to this one, and
they are available in [Github](https://github.com/monkeylearn/hotel-review-
analysis/tree/master/classify_elastic/queries).

## Insights from the reviews

### Most reviews are positive

Looking at the overall picture, most reviews tend to be mostly positive. Out
of all the opinion units analysed, 82% had a ‘positive’ sentiment. This means
that, on average, 82% of the things people write in hotel reviews are
positive:

![Overall sentiment of the 4,000,000 opinion
units](https://blog.monkeylearn.com/wp-content/uploads/2016/07/1-1.png)

Overall sentiment of the 4,000,000 opinion units

### London hotels have the worst reviews

However, things start to get interesting once you segment the sentiment of the
reviews by city: London hotel reviews tend to be harsher than those of other
cities. We were surprised by this result, as we were expecting London to
perform at about the same level as New York or Paris. With millions of
travelers visiting the city every year, it is remarkable that it scores
differently to other similarly sized cities:

![Sentiment of the hotel reviews across the different
cities](https://blog.monkeylearn.com/wp-content/uploads/2016/07/2.png)

Sentiment of the hotel reviews across the different cities

This model doesn’t give us any reasons for this behavior, only the facts.
Could this be a symptom of the famously bad British customer service? Or maybe
hotels in London are just worse than in other cities? Or is it because people
are more demanding with the hotels in London? (Share your opinion on this
result in the comments below)

### London is dirtier than New York and has the worst food overall

Another interesting insight comes from comparing the overall sentiment of the
particular aspects of the hotels across the different cities.

Some aspects keep mostly the same level as the overall sentiment for the city:
'Comfort and Facilities' in London is lower than in New York, and so forth.

Interestingly, not all aspects follow this pattern. For example ‘Location’
tends to be overwhelmingly positive across all cities, meaning that when
reviewers mention the hotel location, it’s usually because they liked it and
rarely to complain. ‘Food’ is a similar case, except for the fact that London
is again remarkably lower than the other cities. This could be the English
cuisine getting a bad rep:

![Sentiment of the different aspects across the different
cities](https://blog.monkeylearn.com/wp-content/uploads/2016/07/3.png)

Sentiment of the different aspects across the different cities

### Sentiment on hotels with different number of stars

We did a final comparison to find out how people felt about the aspects in
hotels of different class (stars). It seems that travelers keep the same
standards no matter what hotel they stay in, since the positivity of the
reviews goes up with the number of stars:

![Overall sentiment in hotels with different
class](https://blog.monkeylearn.com/wp-content/uploads/2016/07/4.png)

Overall sentiment in hotels with different class

### Internet is always an issue

Interestingly, ‘Internet’ doesn’t really rise above 70% in any hotel class,
indicating that internet access is as bad in 3 star hotels as in 5 star
hotels. One would expect that more expensive hotels would provide a better
internet service, but If you’ve ever stayed at a 5 star hotel you know that’s
not usually the case:

![Internet sentiment across hotels with different number of
stars](https://blog.monkeylearn.com/wp-content/uploads/2016/07/5.png)

Internet sentiment across hotels with different number of stars

### Hotels with 3 stars provide the best Value for Money

The other aspect of hotel reviews that behaves differently is ‘Value for
Money’, which peaks at 3 stars and then goes down. This implies that hotels
with three or even two stars have a better value for money overall even though
they have more negative aspects than the hotels with a higher class:

![Value across hotel stars](https://blog.monkeylearn.com/wp-
content/uploads/2016/07/6.png)

Value across hotel stars

## Context analysis with keyword extraction

Numbers are alright, but what about actual _content_? Out of the reviews about
cleanliness, what is praised in each city? What are common complaints in the
food of each hotel? In order to find out, we used the [keyword extraction
module](https://app.monkeylearn.com/main/extractors/ex_y7BPYzNG/). This is a
public module that for given a text, extracts its keywords ordered by
relevance.

In order to get keywords representative from each segment, we combined several
hundred opinion units with the same city, aspect and sentiment into a single
text, and extracted keywords from that text. This is how you run the keyword
extractor:

Python

ml = MonkeyLearn("&lt;your api key here&gt;") text = ["The carpets are a
disgrace in the dining room and need replaced immediately. The room was
frankly grim....old, saggy beds (all 3 of them), scuffed walls and decor, the
hotel is SO old, smells old, and TINY!"] module_id = 'ex_y7BPYzNG' res =
ml.extractors.extract(module_id, text)

1

2

3

4

|

ml = MonkeyLearn("&lt;your api key here&gt;")

text = ["The carpets are a disgrace in the dining room and need replaced
immediately. The room was frankly grim....old, saggy beds (all 3 of them),
scuffed walls and decor, the hotel is SO old, smells old, and TINY!"]

module_id = 'ex_y7BPYzNG'

res = ml.extractors.extract(module_id, text)  
  
---|---  
  
The result is a JSON that contains the extracted keywords and information
about them, such as the relevance and the number of appearances.

For example, these are the top ten keywords for New York hotels reviews with a
'Bad' sentiment in the 'Cleanliness' aspect and their relevance as returned by
MonkeyLearn:

Python

for d in res.result[0]: print d["keyword"], d["relevance"] room 0.999 bathroom
0.790 carpet 0.407 towels 0.311 bed bugs 0.246 bed 0.232 hotel 0.196 shower
0.155 shared bathroom 0.150 walls 0.138

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

|

for d in res.result[0]:

print d["keyword"], d["relevance"]



room 0.999

bathroom 0.790

carpet 0.407

towels 0.311

bed bugs 0.246

bed 0.232

hotel 0.196

shower 0.155

shared bathroom 0.150

walls 0.138  
  
---|---  
  
After the extraction was done for each segment, we compared the keywords
obtained from the different cities: which ones were unique, and which ones
were common to several destinations.

### Bangkok has a cockroach problem

This analysis gave us some very interesting insights about the differences and
the similarities between the hotels in each city.

For instance, cleanliness complaints common to each city were things like
carpet, bed, hair, bed bugs, stains. However, cockroaches appeared only in
Bangkok, which implies that the roach situation in Bangkok’s hotels may be
much worse than in other places.

Shared bathroom appeared only in New York, which could mean that in NYC shared
bathrooms are more common, and they are dirty to boot!

The content of opinions about location changes widely from city to city.
Keywords with the name of different landmarks come up in each city: in Rio de
Janeiro comments mention things like Copacabana, Ipanema; in Beijing things
like the Forbidden Palace and Tiananmen Square; in Madrid Puerta del Sol, and
so forth. These are all places that are important tourist attractions in each
city, so reviewers consider them important when it comes to how well located a
hotel is. There are also mentions to other elements that are characteristic
from a city without being a landmark, such as Tube Station for London, and
Metro for Paris.

### Croissants are a huge disappointment

There are many more insights hidden in this dataset, but we’d like to mention
one more. Namely, the opinions about food in different cities. Breakfast is of
course a common keyword, and so are coffee and tea. However, things get more
interesting when we consider the keywords of a single city.

For example, the keyword croissant appears only in reviews from Paris. In
addition, it appears mostly on a Negative context, which was surprising. Why
would such a staple French food be mentioned in a negative light? Looking at
the content from these reviews, the answer became clear: croissant is
mentioned in the context of a very basic breakfast. So if you go to Paris, a
lackluster breakfast will surely include some croissants (but little else!)

## Final words

On this tutorial we learned how to scrape millions of reviews, analyze them
with pre-trained classifiers within MonkeyLearn, indexed the results with
Elasticsearch and visualize them using Kibana.

Machine learning makes sense when you want to analyze big volumes of data in a
cost effective way.

We discovered some really interesting insights; some were expected (like
Internet being always an issue) and some were a total surprise for us (London
hotels seem to be some of the worst).

If you have the opportunity, check out the
[code](https://github.com/monkeylearn/hotel-review-analysis) and perform your
own analysis, you will find that playing with data and Machine Learning can be
a lot of fun. And if you do, please share your opinions and results in the
comments below, it would be awesome to hear your insights.

Happy coding!
