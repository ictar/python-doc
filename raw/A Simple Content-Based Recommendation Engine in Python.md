原文：[A Simple Content-Based Recommendation Engine in Python](http://blog.untrod.com/2016/06/simple-similar-products-recommendation-engine-in-python.html)

---

Let's pretend we need to build a recommendation engine for an
eCommerce web site.

There are basically two approaches you can take:
content-based and collaborative-filtering. We'll look at some pros and
cons of each approach, and then we'll dig into a
[simple implementation](https://github.com/groveco/content-engine/blob/master/engines.py)
(ready for deployment on Heroku!) of a content-based engine.

For a sneak peak at the results of this approach, take a look at how
we use a nearly-identical recommendation engine
[in production at Grove](https://www.grove.co/catalog/product/cellulose-sponge/?v=802).

## How Content-Based Recommenders Works

Content-based systems are the ones that your friends and colleagues
all assume you are building; using actual item properties like
description, title, price, etc. If you had never thought about
recommendation systems before, and someone put a gun to your head,
Swordfish-style, and forced you to describe one out loud in 30
seconds, you would probably describe a content-based system. "Uhh,
uhh, I'd like, show a bunch of products from the same manufacturer
that have a similar description."

You're using the actual attributes of the item itself to recommend
similar products. This makes a ton of sense, as it's how we actually
shop in the real world. We go into the Toaster-Oven aisle and look at
all the toaster ovens, which are probably physically arranged on the
shelf according to brand, or price, or ability to also cook a full
turkey in under 30 minutes.

## Where Content-Based Falls Short

On most eCommerce sites it's already easy enough for folks to
[browse the toaster oven category](http://www.target.com/c/toaster-ovens-kitchen-appliances/-/N-5xtri). What
we really want is a recommendation system that drives incremental
sales (e.g. sales that would not have happened otherwise). If a
customer is looking at the product details page for Harry Potter and
the Chamber of Secrets, and your recommender shows Prisoner of
Azkaban, and the customer buys it, the data scientists back at Random
House HQ should _not_ be high-fiving. It's a safe bet that that
customer already knew there were more than two books in the series and
would have bought Prisoner of Azkaban anyway. It was _not_ an
incremental sale.

## How Collaborative Filtering Recommenders Work

We need another approach. Enter Collaborative Filtering, or CF. The
big idea behind CF is also pretty intuitive; the product someone is
most likely to buy, is the product that a bunch of people like you
also bought. Sure, this can lead to the Harry Potter situation, but
it's much better at making recommendations from further afield, from
deeper in the product catalog. It's more robust against problems like
typos ("Harry Pooter" still gets recommended), and when measured in
the real world in terms of generating incremental sales, generally
beats the pants off pure content-based systems.

While the big idea behind CF is intuitive, there is one aspect that
you will definitely have to explain to colleagues many, many
times. Pure CF systems have _no knowledge whatsoever_ about the
products they are recommending! To the system, it's just a giant grid
of product IDs and user IDs, representing who bought what. It's deeply
counterintuitive that CF algorithms often see no measurable
performance improvements when they are hybridized with content-based
systems. Surely knowing _something_ about the items you are
recommending must help a _little_, right?

Nope.

In most cases, essentially 100% of the 'signal' is retrievable from a
simple matrix of who bought what.

So why on earth would you use content-based approaches?

## When Content-Based Approaches Make Sense

But sometimes CF isn't a viable option; let's say we want to make
recommendations to a customer viewing a product details page, who just
got there from a Google SERP link. We don't know anything about this
customer, so we can't build a matrix of purchases. But we _can_ use a
content-based system to recommend similar products. In that sense,
content-based recommenders can solve the "cold-start" problem that CF
systems have.

They can also provide a measure of automated curation when you have a
strong indication of buying intent for a particular product (like when
a lead comes from a Google search for a term related to that
product). If you're interested in the
[Nike Pro Hypercool Fitted Men's Compression Shirt](http://store.nike.com/us/en_us/pd/pro-hypercool-fitted-shirt/pid-10862654/pgid-11296413),
you might also love the
[Nike Pro Hypercool Printed Men's Tights](http://store.nike.com/us/en_us/pd/pro-hypercool-print-3-4-tights/pid-10862709/pgid-11296417). A
content-based engine rocks at picking up related products like this,
without a bunch of manual curation (those products don't appear
together in the 'pants' category, nor in 'shirts').

## Let's Build it with TF-IDF.

Like many algorithms, we can use a bunch of off-the-shelf libraries to
make life pretty easy. As I walk through the approach, bear in mind
that the entire implementation is going to ultimately be
[fewer than 10 lines of Python](https://github.com/groveco/content-engine/blob/master/engines.py#L50). But
before we get to the big reveal and look at the code, let's talk
through the approach.

I've provided a sample dataset of outdoor clothing and products
from Patagonia. The data looks like this, and you can view the whole
thing (~550kb)
[on Github](https://github.com/groveco/content-engine/blob/master/sample-data.csv).

| id | description                                                                 |
|----|-----------------------------------------------------------------------------|
|  1 | Active classic boxers - There's a reason why our boxers are a cult favori...|
|  2 | Active sport boxer briefs - Skinning up Glory requires enough movement wi...|
|  3 | Active sport briefs - These superbreathable no-fly briefs are the minimal...|
|  4 | Alpine guide pants - Skin in, climb ice, switch to rock, traverse a knife...|
|  5 | Alpine wind jkt - On high ridges, steep ice and anything alpine, this jac...|
|  6 | Ascensionist jkt - Our most technical soft shell for full-on mountain pur...|
|  7 | Atom - A multitasker's cloud nine, the Atom plays the part of courier bag...|
|  8 | Print banded betina btm - Our fullest coverage bottoms, the Betina fits h...|
|  9 | Baby micro d-luxe cardigan - Micro D-Luxe is a heavenly soft fabric with ...|
| 10 | Baby sun bucket hat - This hat goes on when the sun rises above the horiz...|

That's it; just IDs and text about the product in the form `Title -
Description`. We're going to use a simple Natural Language Processing
technique called TF-IDF (Term Frequency - Inverse Document Frequency)
to parse through the descriptions, identify distinct phrases in each
item's description, and then find 'similar' products based on those
phrases.

TF-IDF works by looking at all (in our case) one, two, and three-word
phrases (uni-, bi-, and tri-grams to NLP folks) that appear multiple
times in a description (the "term frequency") and divides them by the
number of times those same phrases appear in _all_ product
descriptions. So terms that are 'more distinct' to a particular
product ("Micro D-luxe" in item 9, above) get a higher score, and
terms that appear often, but also appear often in other products
("soft fabric", also in item 9) get a lower score.

Once we have the TF-IDF terms and scores for each product, we'll use a
measurement called
[cosine similarity](http://blog.christianperone.com/2013/09/machine-learning-cosine-similarity-for-vector-space-models-part-iii/)
to identify which products are 'closest' to each other.

Luckily, like most algorithms, we don't have to reinvent the wheel;
there are ready-made libraries that will do the heavy lifting for
us. In this case, Python's SciKit Learn has both a
[TF-IDF](http://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html)
and
[cosine similarity](http://scikit-learn.org/stable/modules/metrics.html#cosine-similarity)
implementation. I've put the whole thing together in a
[Flask app](https://github.com/groveco/content-engine) that will
actually serve recommendations over a REST API, as you might do in
production (in fact, the code is not very different from what we
actually do run in production at [Grove](https://www.grove.co)).

The engine has a `.train()` method that runs TF-IDF across the input
products file, computes similar items for every item in the set, and
stores those items along with their cosine similarity, in Redis. The
`.predict` method just takes an item ID and returns the precomputed
similarities from Redis. Dead simple!

The engine code in its entirety is below. The comments explain how the
code works, and you can explore the complete Flask app
[on Github](https://github.com/groveco/content-engine).

```python
import pandas as pd
import time
import redis
from flask import current_app
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import linear_kernel


def info(msg):
    current_app.logger.info(msg)


class ContentEngine(object):

    SIMKEY = 'p:smlr:%s'

    def __init__(self):
        self._r = redis.StrictRedis.from_url(current_app.config['REDIS_URL'])

    def train(self, data_source):
        start = time.time()
        ds = pd.read_csv(data_source)
        info("Training data ingested in %s seconds." % (time.time() - start))

        # Flush the stale training data from redis
        self._r.flushdb()

        start = time.time()
        self._train(ds)
        info("Engine trained in %s seconds." % (time.time() - start))

    def _train(self, ds):
        """
        Train the engine.

        Create a TF-IDF matrix of unigrams, bigrams, and trigrams
        for each product. The 'stop_words' param tells the TF-IDF
        module to ignore common english words like 'the', etc.

        Then we compute similarity between all products using
        SciKit Leanr's linear_kernel (which in this case is
        equivalent to cosine similarity).

        Iterate through each item's similar items and store the
        100 most-similar. Stops at 100 because well...  how many
        similar products do you really need to show?

        Similarities and their scores are stored in redis as a
        Sorted Set, with one set for each item.

        :param ds: A pandas dataset containing two fields: description & id
        :return: Nothin!
        """

        tf = TfidfVectorizer(analyzer='word',
                             ngram_range=(1, 3),
                             min_df=0,
                             stop_words='english')
        tfidf_matrix = tf.fit_transform(ds['description'])

        cosine_similarities = linear_kernel(tfidf_matrix, tfidf_matrix)

        for idx, row in ds.iterrows():
            similar_indices = cosine_similarities[idx].argsort()[:-100:-1]
            similar_items = [(cosine_similarities[idx][i], ds['id'][i])
                             for i in similar_indices]

            # First item is the item itself, so remove it.
            # This 'sum' is turns a list of tuples into a single tuple:
            # [(1,2), (3,4)] -> (1,2,3,4)
            flattened = sum(similar_items[1:], ())
            self._r.zadd(self.SIMKEY % row['id'], *flattened)

    def predict(self, item_id, num):
        """
        Couldn't be simpler! Just retrieves the similar items and
        their 'score' from redis.

        :param item_id: string
        :param num: number of similar items to return
        :return: A list of lists like: [["19", 0.2203],
        ["494", 0.1693], ...]. The first item in each sub-list is
        the item ID and the second is the similarity score. Sorted
        by similarity score, descending.
        """

        return self._r.zrange(self.SIMKEY % item_id,
                              0,
                              num-1,
                              withscores=True,
                              desc=True)

content_engine = ContentEngine()
```

## Run It Yourself!

If you actually want to try this out, it's very easy. Follow the
instructions in the
[readme](https://github.com/groveco/content-engine/blob/master/readme.md)
and you'll have it running locally in no time, using the sample
Patagonia data. The engine is ready to be deployed to Heroku as well.

Take that, collaborative filtering! #contentbased
