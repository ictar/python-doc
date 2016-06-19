原文：[A Simple Content-Based Recommendation Engine in Python](http://blog.untrod.com/2016/06/simple-similar-products-recommendation-engine-in-python.html)

---

假设，我们需要为一个电子商务网站建立一个推荐引擎。

基本上，你有两种方法：基于内容和协同过滤。我们将看看每种方法的优缺点，然后深入一个基于内容的引擎的一个[简单的实现](https://github.com/groveco/content-engine/blob/master/engines.py)（准备在Heroku之上部署！）。

要对此方法的结果先睹为快，你可以看看[在Grove的生产环境上](https://www.grove.co/catalog/product/cellulose-sponge/?v=802)，我们是如何使用一个几乎相同的推荐引擎的。

## 基于内容的推荐如何工作

基于内容的系统是那种，你的朋友和同事都假设你正在建立的; 它使用实际的项目属性，如描述，名称，价格等，如果你以前从来没有想过推荐系统，那么假设有人用枪指着你的头，剑鱼式，并迫使你在30秒内大声描述，此时，你大概描述的就是一个基于内容的系统。“呃，呃，我想，显示来自具有类似描述的，并且来自同一制造商的一堆产品。”

你使用的是项目本身的实际属性来推荐同类产品。这意义重大，因为它就是我们在现实世界中如何真正购物的。我们走进烤箱过道，看看所有的烤箱，它们可能是按照品牌，或价格，或依据在30分钟内煮一只完整火鸡的能力，是物理排列在货架上。

## 基于内容做不到的事

在大多数的电子商务网站上，对于人们来说，已经很容易[浏览电烤箱类别](http://www.target.com/c/toaster-ovens-kitchen-appliances/-/N-5xtri)了。而我们真正需要的是一个推荐系统，它受增量销售驱动（例如，尚未发生的销售）。如果一个客户正在看哈利波特与密室(Harry Potter and the Chamber of Secrets)的产品详细信息页面，而你的推荐器显示了阿兹卡班的囚徒(Prisoner of Azkaban)，于是客户买了这本书，那么回到Random House HQ的数据科学家不应该击掌相庆。因为几乎可以百分百的确认，那个客户已经知道了该系列不仅有两本，总之他买了Prisoner of Azkaban。这并不是一个增量销售。

## 协同过滤推荐如何工作

我们需要另一种方法。看看协同过滤，或CF。CF背后的大思路也是非常直观的; 人们最有可能购买的产品就是，一堆像你这样的人还会买的产品。当然，这可能会导致上面提到的哈利波特的情况，但从更远处，从产品目录中更深层次提出建议会好得多。这对像错别字问题会更健壮（“哈利·普特”仍然得到了推荐），而当用现实世界中产生的销售增量来衡量时，通常会打败纯粹的基于内容的系统。

虽然CF背后的大思路是直观的，但是还有一个你一定要向同事解释无数次的方面。纯CF系统没有任何关于它们正在推荐的产品的知识！对于系统来说，只是一个由产品ID和用户ID组成的巨大的网格，这个网格表示谁买了什么。当它们与基于内容的系统混合使用时，CF算法经常看不到可测量的性能改善，这是违反直觉的。当然，知道一些关于你正推荐的产品的知识的建议必须有点小用，对不对？

不。

在大多数情况下，“信号”基本上100％是从谁买了什么的一个简单矩阵中检索的。

那么，到底为什么你要使用基于内容的方法？

## 什么时候基于内容的方法会有意义

但有时CF不是一个可行的选择; 比方说，我们希望给正在查看产品详细信息页面的客户（他们刚刚从谷歌搜索结果页面链接过来）推荐。我们不知道这个客户任何信息，所以不能够建立一个购买矩阵。但是，我们可以使用一个基于内容的系统来推荐同类产品。在这个意义上，基于内容的推荐器可以解决CF系统有的“冷启动”问题。

当你有购买某一个特定产品的强烈意向时（例如，当来自谷歌搜索的某个词与那个产品相关时），它们还可以提供自动化的策展衡量。如果你对[Nike Pro Hypercool Fitted Men's Compression Shirt](http://store.nike.com/us/en_us/pd/pro-hypercool-fitted-shirt/pid-10862654/pgid-11296413)感兴趣，那么你可能也喜欢[Nike Pro Hypercool Printed Men's Tights](http://store.nike.com/us/en_us/pd/pro-hypercool-print-3-4-tights/pid-10862709/pgid-11296417)。基于内容的引擎热衷于挑选像这样的相关产品，无需一堆的手动策展 (那些产品并不一起出现在“裤子”类别，也不在“衬衫”类别)。

## 让我们用TF-IDF构建它

像许多算法一样，我们可以使用一堆现成的库来让生活更加美好。当我检验这些方法时，记住整个实现将最终[少于10行的Python代码](https://github.com/groveco/content-engine/blob/master/engines.py#L50)。但在我们开始大揭秘以及看代码之前，让我们聊聊这个方法。

我提供了来自Patagonia的户外服装和产品的样本数据集。数据看起来是这样的，你可以[在Github上](https://github.com/groveco/content-engine/blob/master/sample-data.csv)看到完整数据(~550kb)。

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

就是这样；只是`Title - Description`这种形式的产品的ID和文本。我们将使用一个简单的自然语言处理技术，TF-IDF (词频-逆文档频率，Term Frequency - Inverse Document Frequency)，来解析描述，确定每个项目描述中的不同的短语，然后基于这些短语找到“类似的”产品。

TF-IDF的工作原理是看看所有在描述中出现多次的（在本例中）一字词，二字词和三字词短语（对于NLP人来说，即uni-, bi-, 和tri-grams）（下称“词频” ），并用一词语出现的次数除以该产品描述的总词语数。因此，那些对于一个特定产品“更特别”的词语（上面，在产品9中的“Micro D-luxe”），会得到更高的分数，而那些出现频率较高，并且在其他产品中也出现频率较高的词语（同时，在产品9中，“soft fabric”），会得到较低的分数。

一旦对于每个产品，我们都有了TF-IDF词语和分数，那么我们将使用一个称为[余弦相似性](http://blog.christianperone.com/2013/09/machine-learning-cosine-similarity-for-vector-space-models-part-iii/)的方法来识别每个产品“最类似”的产品是什么。

幸运的是，如大多数的算法，我们无需再造轮子；有现成的库可以为我们做这些繁重的工作。在这种情况下，Python的SciKit Learn既有[TF-IDF](http://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html)实现，也有[余弦相似性](http://scikit-learn.org/stable/modules/metrics.html#cosine-similarity)实现。我已经将所有的东西都放到一个[Flask应用](https://github.com/groveco/content-engine)去了，它将实际通过一个REST API，来提供推荐服务，就如你会在生产上做的那样（事实上，代码与我们实际上在[Grove](https://www.grove.co)的生产环境上跑的没多大区别）。

该引擎有一个`.train()`方法，它在输入的产品文件中运行TF-IDF，为集合中的每一项计算相似项，然后将这些项及其余弦相似性一同保存在Redis中。`.predict`方法只需要一个产品ID，并从Redis中返回预先计算好的余弦相似性。简单的要死！

全部引擎代码如下。注释解释了代码如何工作，而你可以[在Github上](https://github.com/groveco/content-engine)探索整个Flask应用。

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

## 自己运行！

如果你真的想试一试，也很容易。按照[readme](https://github.com/groveco/content-engine/blob/master/readme.md)中的说明，你将可以让它在任何时间，使用示例Patagonia数据，运行于本地。该引擎也准备好部署到Heroku了。

接招，协同过滤！#基于内容
