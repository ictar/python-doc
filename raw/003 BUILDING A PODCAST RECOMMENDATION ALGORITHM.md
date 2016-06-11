原文: [003: Building a podcast recommendation algorithm](http://lindsayvass.com/2016/05/27/003-building-a-podcast-recommendation-algorithm/)

---

I've been listening to podcasts for a few years now -- the usual suspects,
[This American Life](http://www.thisamericanlife.org/) and
[Serial](https://serialpodcast.org/season-one) got me started -- but [The
Political Gabfest](http://www.slate.com/articles/podcasts/gabfest.html) was my
gateway drug. I soon discovered that one hour a week wasn't nearly enough. I
needed to find more podcasts to fill out my playlist. But then I ran into a
problem: how do you find podcasts similar to one you like? iTunes is the
ultimate podcast repository, but their recommendations and search are pretty
terrible.

Take [Meowster](http://meowsterpodcast.com/), for example, a podcast about
cats. If you look up related podcasts on iTunes, you'd probably expect to get
back other podcasts for pet enthusiasts. But what you actually get is this:

[![Meowster_Related](http://lindsayvass.com/wp-content/uploads/2016/05
/Meowster_Related-500x215.png)](http://lindsayvass.com/wp-
content/uploads/2016/05/Meowster_Related.png)

It's just the list of the top podcasts in the _Society &amp; Culture
_category, which sure, you might like, but are hardly what I would call
"related" in any meaningful way.

If you try searching for "cats" podcasts on iTunes, you won't do much better.
Half of the results are actually about cars, some just happen to have the word
"cat" in the title, and two of them are inactive; you only get back 1 good
result.

[![Cats_Search](http://lindsayvass.com/wp-content/uploads/2016/05/Cats_Search-
500x501.png)](http://lindsayvass.com/wp-
content/uploads/2016/05/Cats_Search.png)

So what's a podcast aficionado to do? Well, when I started my fellowship at
[Insight Data Science](http://insightdatascience.com/), I realized that this
was the perfect problem to tackle for my project. I figured that I could build
a much better podcast recommendation algorithm by leveraging the text data
(i.e., titles and descriptions) associated with each podcast. And so I did!
The result was [thesauropod.us](http://www.thesauropod.us/), a thesaurus for
podcasts.

### Getting the Data

In order for my podcast recommendation algorithm to work, I needed to obtain
the titles and descriptions associated with a large number of podcasts.
Fortunately, there is an [iTunes
API](https://affiliate.itunes.apple.com/resources/documentation/itunes-store-
web-service-search-api/), but much to my dismay, it didn't allow me to query
the information I needed.

The first problem I needed to solve was to figure out which podcasts to query.
The iTunes API doesn't allow you to obtain a list of some or all of the
podcasts in the iTunes collection.1  You have to either search for a
particular podcast (e.g., by title) or lookup a particular podcast using its
iTunes ID. I solved this problem by obtaining the titles of thousands of
podcasts from a dataset of ~135,000 podcasts I found on
[github](https://github.com/ageitgey/all-podcasts-dataset).

The second problem I needed to solve was to obtain the titles and descriptions
for each podcast. Frustratingly, this information is not available through the
API, but it can be scraped from the iTunes website if you know the podcast's
ID. This led me to a two-step process. First, use the iTunes API to do a title
search for each podcast in my dataset. Then use the podcast ID obtained from
that API query to find the iTunes webpage associated with that podcast and
scrape the titles and descriptions from there using
[BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/).

Because API queries and web scraping are both time intensive, I tried to
reduce my dataset where I could. Before querying the API, I eliminated any
non-English podcasts using the [guess_language](https://pypi.python.org/pypi
/guess-language) package. Before web scraping, I eliminated any podcasts that
hadn't released an episode in the last 45 days (since I only wanted active
podcasts). Still, I encountered some issues trying to download the data.
First, the iTunes API has a rate limit, but [they won't tell you what it
is](http://stackoverflow.com/questions/12596300/itunes-search-api-rate-limit).
As a result, my queries would start timing out after a while, bringing my
script to a halt. I solved this using the function below, which allowed me to
test whether the API query URL was currently working, and wait and retry if it
wasn't. Four retries with a 60 second wait ended up working pretty well for
me.

  
Second, the title search didn't always work. Sometimes I would get back 0
results and sometimes I would get back a dozen (this was more likely to happen
with short podcast titles like "This American Life" or "Serial"). Because I
didn't want to constantly manually intervene, I just threw out any podcast
whose title search didn't give me exactly 1 result. And because this ended up
throwing out a lot of the most popular podcasts, I added those back in using
the data from
[iTunesCharts.net](http://www.itunescharts.net/us/charts/podcasts/current/).2
In the end, I was left with a dataset of ~6,000 podcasts.

### Natural Language Processing

Now that I had the titles and descriptions for each podcast, it was time to
convert the text data into a representation that could be used for similarity
analyses. First up: pre-processing.

#### Pre-Processing the Text

Because I wanted to build an overall representation of what a particular
podcast is about, I concatenated all the text data from a single podcast into
one long document.3 Next I needed to strip away any text data that I didn't
want to contribute to my algorithm. There's no one-size-fits-all solution to
this. You need to think carefully about what's present in your dataset (which
means looking at lots of raw data), and whether there's anything in that data
that can hinder the success of your algorithm. In my case, I chose to remove:

  1. _Mixed alphanumeric words - _Some podcasts include a season/episode number in the title or description (e.g., "S4E11"), but most don't. If I left this data in, then podcasts that share this numbering scheme would be marked as more similar to each other. But because I'm interested in content similarity, this isn't meaningful for my algorithm.
  2. _Days and months_ - Similarly, some podcasts include the date in their description (e.g., "Thursday May 26"), but most don't. I removed the days of the week and months of the year so this wouldn't contribute to my similarity metric.
  3. _Sponsorship statements_ - The primary way podcasts make money is by playing ads during the episode. In some cases, these corporate sponsors are also included in the episode description (e.g., "This podcast is sponsored by Squarespace…"). Although it could be true that podcasts with the same corporate sponsors are more similar in content, I didn't want to assume that, so I did my best to remove these statements from the data.

After cleaning up the data, the next step was to tokenize the long string of
text into a list of individual words. I removed any non-alphanumeric
characters from the text using regular expressions and then used the
RegexpTokenizer from [NLTK](http://www.nltk.org/) to split the text on white
space. At this point, I also removed high-frequency words (e.g., a, the, or,
but) using the [stop_words](https://pypi.python.org/pypi/stop-words) package.
Finally, each individual word was stemmed to remove suffixes using NLTK's
PorterStemmer. The benefit of this step is that related words (e.g., walk,
walks, walking, walked) are all converted to the same stem. Here's what the
process looks like:

We start with the original text:

`'Sure people who arent fascinated by cats and all of their uniquely feline
awesomeness might be unable to tell a calico from a tortoiseshell, let alone a
Burmese from a Balinese'`

After tokenizing:

`['Sure', 'people', 'who', 'arent', 'fascinated', 'by', 'cats', 'and', 'all',
'of', 'their', 'uniquely', 'feline', 'awesomeness', 'might', 'be', 'unable',
'to', 'tell', 'a', 'calico', 'from', 'a', 'tortoiseshell', 'let', 'alone',
'a', 'Burmese', 'from', 'a', 'Balinese']`

After removing stop words:

`['Sure', 'people', 'fascinated', 'cats', 'uniquely', 'feline', 'awesomeness',
'might', 'unable', 'tell', 'calico', 'tortoiseshell', 'let', 'alone',
'Burmese', 'Balinese']`

After stemming:  
`[u'sure', u'peopl', u'fascin', u'cat', u'uniqu', u'felin', u'awesom',
u'might', u'unabl', u'tell', u'calico', u'tortoiseshel', u'let', u'alon',
u'burmes', u'balines']`

#### Creating the Corpus

At this point, we have a list of stemmed words for each of our podcasts. Now,
we need to turn those lists of strings into vectors of numbers that we can
ultimately use to calculate similarity between podcasts. For all of the
remaining processing, I used the fantastic
[gensim](https://radimrehurek.com/gensim/index.html) package.  It has
excellent documentation and tutorials and critically, for my purpose, also
included a document similarity server called
[simserver](http://radimrehurek.com/gensim/simserver.html). Simserver provides
a memory-independent and efficient way to store all of the text data and runs
in the background on my website, serving up the similarity results for each
podcast query. It can also be safely updated online, which means that I can
add new podcasts to my dataset without disrupting current users.

The first thing gensim does is to create a dictionary for the corpus of
documents (in my case, each podcast's list of stemmed words is a "document").
This dictionary contains entries for each unique "token" (i.e., stemmed word)
and the frequency of its appearance across all podcasts. This frequency
information will be important to us later on.

Once we've defined all the unique tokens in the dictionary, we can transform
the list of stemmed words for each podcast into a much more efficient vector
representation referred to as a _bag of words_. For example, let's say that
our Meowster document includes the token 'cat' 27 times, the token 'paw' 15
times, and the token 'felin' 5 times. We could take that long list of strings
and re-represent it as a list of tuples, in which the first value indicates
the integer associated with that token and the second value indicates the
frequency of that token: `[(0, 27), (1, 15), (2, 5)]`

#### Transforming the Corpus

At this point, there's nothing stopping you from just using those vector
representations to calculate the similarity between podcasts. But there's at
least two reasons why you might not want to do that.

First off, the current representation of each podcast treats all words as
equally informative, even though we know that's not true. For example,
"wrestling" is likely a very low-frequency word in the corpus, but it's a
_highly diagnostic _word for podcasts about wrestling: if we find two podcasts
that both contain the word "wrestling" many times, it's a good indication that
those podcasts are similar in content. In contrast, a word like "today"
probably occurs at a high-frequency across the corpus; even if two podcasts
both contain the word "today," this doesn't tell us much about how similar
their content is (e.g., one might be about today's news and another might be
about movies released today).

To solve this problem, I applied a [TF-IDF](http://www.tfidf.com/) (term
frequency-inverse document frequency) transformation to my original bag of
words representation. In the output, words that are rare in the corpus are
given more weight than words that are frequent in the corpus. This is exactly
what we're looking for (e.g., wrestling &gt; today).

The second problem with the original model is that it contains a massive
number of features -- in my corpus, I had ~174,000 unique tokens. It's
generally a good idea to [reduce the number of
features](http://www.analyticsvidhya.com/blog/2015/07/dimension-reduction-
methods/) in your model. For practical reasons, it's much more efficient to
store 100 values for each podcast than 174,000 values for each podcast.
Perhaps counterintuitively, fewer features also tends to improve model
performance. This is because many of the features in the model are redundant
and don't provide additional unique information. For example, if we know that
a podcast contains the word "cat" 100 times, then knowing that it also
contains the word "feline" 50 times, doesn't tell us anything new -- we
already had a pretty good idea that it was about cats. We can then collapse
"cat" and "feline" (and perhaps other words like "fur" and "paw") into one
"cat-ness" feature that better represents the _topic_ rather than the
individual words.

In NLP, there are many different ways to solve this problem. Gensim's
simserver includes three options: LSI (latent semantic indexing), LDA (latent
Dirichlet allocation), and log entropy. I tried all three and found that LSI
worked best for my dataset (for an easy-to-understand overview of how LSI
works, [see here](http://www.seobook.com/lsi/lsa_explanation.htm)). How did I
choose which one was best? Because I didn't have any ground truth for how
similar any two podcasts are (if I did, I wouldn't need to build an
algorithm!), this was all done the old-fashioned way: eyeballometrically. I
ran each model on the TF-IDF-transformed data, which output a much shorter
vector for each podcast. Then, I simply computed the [cosine
similarity](http://blog.christianperone.com/2013/09/machine-learning-cosine-
similarity-for-vector-space-models-part-iii/) between each pair of vectors,
and looked at what each model said were the most similar podcasts to a
particular test podcast. Below are the results for Meowster. You can see that
even before tweaking the parameters of the model (i.e., how many "topics" to
reduce the word features down to), LSI is doing a pretty good job. I used the
same eyeballometric tactics to settle on the number of topics for my LSI model
(I found that 100 topics worked best for me).

[![model_comparison](http://lindsayvass.com/wp-content/uploads/2016/05
/model_comparison-500x378.png)](http://lindsayvass.com/wp-
content/uploads/2016/05/model_comparison.png)

### Validating the Model

I just got done saying that I don't have any ground truth for podcast
similarity, so how can I validate my model? Ideally, I would have access to
data about how individual users rate podcasts, or which podcasts individual
users subscribe to. I wasn't able to get that from iTunes, but I _was_ able to
get some information about podcast subscriptions. For popular podcasts, iTunes
will give you suggestions that "Listeners also subscribed to."4 I hypothesized
that two podcasts that are commonly co-subscribed should be more similar than
two random podcasts. Therefore, if I compare the similarity scores from my
model, the co-subscribed podcasts should have higher similarity than the
random podcast pairings. That's exactly what I found.

[![validation](http://lindsayvass.com/wp-content/uploads/2016/05/validation-
500x370.png)](http://lindsayvass.com/wp-
content/uploads/2016/05/validation.png)

So that's the inner workings of [thesauropod.us](http://www.thesauropod.us/)!
It was a LOT of work for 3 weeks, but I'm thrilled with the outcome. There's
nothing more satisfying than building something you'll actually use.





  1. You can download all the metadata if you have access to the [Enterprise Partner Feed](https://affiliate.itunes.apple.com/resources/documentation/itunes-enterprise-partner-feed/), but the rapid pace of the Insight fellowship meant that I couldn't get access in time. ↩
  2. I was able to directly identify the podcast's ID by parsing the iTunes URL they provided. ↩
  3. If I had wanted to build an episode recommendation algorithm, I would have separated each episode's text into individual documents. ↩
  4. Because you get more suggestions (up to 15) in the iTunes application than on the website (up to 5), I used [PycURL](http://pycurl.io/) to imitate the iTunes user agent when I was doing my web scraping. ↩


