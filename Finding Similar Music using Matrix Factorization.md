原文：[Finding Similar Music using Matrix Factorization](http://www.benfrederickson.com/matrix-factorization/)

---

In a previous post I wrote about [how to build a 'People Who
Like This Also Like ...' feature](/distance-metrics/) for displaying lists of similar musicians.
My goal was to show how simple Information Retrieval techniques can do a good job calculating lists
of related artists. For instance, using BM25 distance on The Beatles shows the most
similar artists being John Lennon and Paul McCartney.

One interesting technique I didn't cover was using Matrix Factorization methods
to reduce the dimensionality of the data before calculating the related artists. This kind of analysis
can generate matches that are impossible to find with the techniques in my original post.

This post is a step by step guide on how to calculate related artists using a couple of different
matrix factorization algorithms. The code is written in Python using
[Pandas](http://pandas.pydata.org/)
and [SciPy](https://www.scipy.org/) to do the calculations and [D3.js](https://d3js.org/) to interactively visualize the results.

As part of writing this post, I also open sourced a [high performance python version of the Implicit Alternating Least
Squares](http://github.com/benfred/implicit) matrix factorization algorithm.
Most of the code here can be found in the examples directory of that project.


### Loading the Data

For the post here I'm using
the same [Last.fm dataset](http://www.dtic.upf.edu/~ocelma/MusicRecommendationDataset/lastfm-360K.html)
as in my first post. This can be loaded into a sparse matrix with only a couple lines of code
using Pandas:

```python
# read in triples of user/artist/playcount from the input dataset
data = pandas.read_table("usersha1-artmbid-artname-plays.tsv", 
                         usecols=[0, 2, 3], 
                         names=['user', 'artist', 'plays'])

# map each artist and user to a unique numeric value
data['user'] = data['user'].astype("category")
data['artist'] = data['artist'].astype("category")

# create a sparse matrix of all the artist/user/play triples
plays = coo_matrix((data['plays'].astype(float), 
                   (data['artist'].cat.codes, 
                    data['user'].cat.codes)))

```

The matrix returned here has 300,000 artists and 360,000 users with around 17 million entries
total. Each entry is the number of times the user played the artist, with the data collected
from the Last.fm API way back in 2008.

### Matrix Factorization

One technique thats commonly used for this problem is to project the matrix of
user-artist-plays in to a low rank approximation, and then compute distances
in that space.

The idea is to take the original play count matrix, and then reduce that down to two
much smaller matrices that approximate the original when multiplied together:

<div id="mf"><svg width="750" height="312.5"><rect x="25" y="25" width="262.5" height="262.5" fill="#1f77b4" fill-opacity=".25" stroke="#1f77b4" stroke-opacity=".8"></rect><text text-anchor="middle" dy=".3em" x="156.25" y="156.25" style="font-size: 12px;">Artist/User/Play Counts</text><rect x="362.5" y="25" width="25" height="262.5" fill="#ff7f0e" fill-opacity=".25" stroke="#ff7f0e" stroke-opacity=".8"></rect><text text-anchor="middle" dy=".3em" x="375" transform="rotate(90, 375,156.25)" y="156.25" style="font-size: 12px;">Artist Factors</text><rect x="462.5" y="143.75" width="262.5" height="25" fill="#ff7f0e" fill-opacity=".25" stroke="#ff7f0e" stroke-opacity=".8"></rect><text text-anchor="middle" dy=".3em" x="593.75" y="156.25" style="font-size: 12px;">User Factors</text><text text-anchor="middle" dy=".3em" x="325" y="156.25" style="font-size: 24px;">=</text><text text-anchor="middle" dy=".3em" x="425" y="156.25" style="font-size: 24px;">×</text></svg></div>

Instead of representing each artist as a sparse vector of the play counts of all 360,000
possible users, after factorizing the matrix each artist will be represented by say
a 50 dimensional dense vector.

By reducing the dimensionality of the data like this, we're in effect compressing the input matrix
down to two much smaller matrices. This compression forces generalization of the input data, and
that generalization leads to a better understanding of the data.

### Latent Semantic Analysis

One simple way to factorize the input matrix is to compute the [Singular Value
Decomposition](https://en.wikipedia.org/wiki/Singular_value_decomposition) on an appropriately
weighted matrix [(1)](#footnote1):

```python
artist_factors, _, user_factors = scipy.sparse.linalg.svds(bm25_weight(plays), 50)
```

The SVD is one of those amazingly useful techniques, and can also be used for things like
[Principal Component Analysis](https://en.wikipedia.org/wiki/Principal_component_analysis) or
[Multidimensional Scaling](http://localhost:4000/multidimensional-scaling/). I was going include a
summary of how it works, but Jeremy Kun recently wrote an [excellent overview
 of the SVD](https://jeremykun.com/2016/04/18/singular-value-decomposition-part-1-perspectives-on-linear-algebra/)
, and I'm not going to even try beating that. For the purposes of this post, we
just need to know that the SVD generates a low rank approximation of the input matrix, and we
can use that low rank representation to generate insights.

Using the SVD like this is called [Latent Semantic
Analysis](https://en.wikipedia.org/wiki/Latent_semantic_analysis) (LSA). All thats really involved
is getting the top most related artists by cosine distance in this factorized space, which can be done
by:

```python
class TopRelated(object):
    def __init__(self, artist_factors):
        # fully normalize artist_factors, so can compare with only the dot product
        norms = numpy.linalg.norm(artist_factors, axis=-1)
        self.factors = artist_factors / norms[:, numpy.newaxis]

    def get_related(self, artistid, N=10):
        scores = self.factors.dot(self.factors[artistid])
        best = numpy.argpartition(scores, -N)[-N:]
        return sorted((best, scores[best]), key=lambda x: -x[1])
```

Latent Semantic Analysis got its name because after factorizing the matrix,
latent hidden structure in the input data can be exposed - which can be thought of as revealing the semantic meaning of the input data.

For instance, 'Arcade Fire' and 'The Arcade Fire' have no common
listeners in the dataset I'm using: it was generated by Last.fm histories
and people either used one label or the other in their music libraries.

This means that all of the direct methods of comparing artists think that
these two bands are entirely different. However,
both of these labels refer to the same band - a fact that LSA manages to pick up
on since they get ranked as being the most similar to each other:

<div class="cosinelist well" style="background:#FFFFFF;">
<div class="row">
  <div class="col-lg-6">
    <div class="input-group">
      <div class="input-group-btn">
        <button type="button" class="btn btn-default dropdown-toggle" data-toggle="dropdown">
          <span class="cosine-method">LSA</span> <span class="caret"></span>
        </button>

*   <a class="lsi">LSA</a>
*   <a class="implicitals">Implicit ALS</a>
      </div>
    <span class="twitter-typeahead" style="position: relative"><input class="form-control typeahead tt-hint" style="position: absolute; top: 0px; left: 0px; border-color: transparent; box-shadow: none; opacity: 1; background: none 0% 0% / auto repeat scroll padding-box border-box rgb(255, 255, 255);" readonly="" autocomplete="off" spellcheck="false" tabindex="-1"><input class="form-control typeahead tt-input" id="set-artist-name" style="position: relative; vertical-align: top; background-color: transparent;" placeholder="Enter an Artist Name" autocomplete="off" spellcheck="false" dir="auto"><pre aria-hidden="true" style="position: absolute; visibility: hidden; white-space: pre; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-size: 14px; font-style: normal; font-variant: normal; font-weight: 400; word-spacing: 0px; letter-spacing: 0px; text-indent: 0px; text-rendering: auto; text-transform: none;"></pre><span class="tt-dropdown-menu" style="position: absolute; top: 100%; z-index: 100; display: none; left: 0px; right: auto;"><div class="tt-dataset-artists"></div></span></span>
    </div>
  </div>
</div>
<div>

* * *

<div class="resultstitle">

#### Similar to 'Arcade Fire' by LSA:
</div>

<div class="row">
<div class="artistlist col-sm-6"><table class="table table-striped table-hover" style="table-layout:fixed"><thead><tr><th>Artist</th><th>LSA</th></tr></thead><tbody><tr><td><a style="color:black">The Arcade Fire</a></td><td>0.988</td></tr><tr><td><a style="color:black">The Dears</a></td><td>0.985</td></tr><tr><td><a style="color:black">Ambulance Ltd</a></td><td>0.984</td></tr><tr><td><a style="color:black">The National</a></td><td>0.981</td></tr><tr><td><a style="color:black">Clap Your Hands Say Yeah</a></td><td>0.977</td></tr></tbody><tfoot><tr><td><a>more ...</a></td><td></td></tr></tfoot></table></div>
<div class="venn col-sm-6" style="padding:0px"><svg width="370" height="277.5"><g class="venn-area venn-circle venn-sets-Arcade Fire"><path style="fill-opacity: 0.25; fill: rgb(31, 119, 180); stroke-opacity: 0; stroke: rgb(255, 255, 255); stroke-width: 0;" d="
M 231.61034132865382 138.75 
m -123.38965867134617 0 
a 123.38965867134617 123.38965867134617 0 1 0 246.77931734269234 0 
a 123.38965867134617 123.38965867134617 0 1 0 -246.77931734269234 0"></path><text text-anchor="middle" dy=".35em" x="241" y="138" style="fill: rgb(31, 119, 180); font-size: 12px;"><tspan x="241" y="138" dy="0.35em">Arcade Fire</tspan></text></g><g class="venn-area venn-intersection venn-sets-Arcade Fire_The Arcade Fire"><path style="fill-opacity: 0; stroke-opacity: 0; stroke: rgb(255, 255, 255); stroke-width: 0;" d="M 0 0"></path><text text-anchor="middle" dy=".35em" x="0" y="-1000" style="fill: rgb(68, 68, 68); font-size: 12px;"><tspan x="0" y="-1000" dy="0.35em"></tspan></text></g><g class="venn-area venn-intersection venn-sets-Arcade Fire_The Arcade Fire_The Dears"><path style="fill-opacity: 0; stroke-opacity: 0; stroke: rgb(255, 255, 255); stroke-width: 0;" d="M 0 0"></path><text text-anchor="middle" dy=".35em" x="0" y="-1000" style="fill: rgb(68, 68, 68); font-size: 12px;"><tspan x="0" y="-1000" dy="0.35em"></tspan></text></g><g class="venn-area venn-intersection venn-sets-Arcade Fire_The Dears"><path style="fill-opacity: 0; stroke-opacity: 0; stroke: rgb(255, 255, 255); stroke-width: 0;" d="
M 111.067237616283 165.10086363707885 
A 123.38965867134617 123.38965867134617 0 0 1 111.067237616283 112.39913636292115 
A 28.640549150787812 28.640549150787812 0 0 1 111.067237616283 165.10086363707885"></path><text text-anchor="middle" dy=".35em" x="118" y="138" style="fill: rgb(68, 68, 68); font-size: 12px;"><tspan x="118" y="138" dy="0.35em"></tspan></text></g><g class="venn-area venn-circle venn-sets-The Arcade Fire"><path style="fill-opacity: 0.25; fill: rgb(255, 127, 14); stroke-opacity: 0; stroke: rgb(255, 255, 255); stroke-width: 0;" d="
M 68.2464454432251 205.9255223121374 
m -53.24644544322509 0 
a 53.24644544322509 53.24644544322509 0 1 0 106.49289088645018 0 
a 53.24644544322509 53.24644544322509 0 1 0 -106.49289088645018 0"></path><text text-anchor="middle" dy=".35em" x="66" y="209" style="fill: rgb(255, 127, 14); font-size: 12px;"><tspan x="66" y="209" dy="-0.7500000000000001em">The</tspan><tspan x="66" y="209" dy="0.35em">Arcade</tspan><tspan x="66" y="209" dy="1.4500000000000002em">Fire</tspan></text></g><g class="venn-area venn-intersection venn-sets-The Arcade Fire_The Dears"><path style="fill-opacity: 0; stroke-opacity: 0; stroke: rgb(255, 255, 255); stroke-width: 0;" d="
M 75.0704046966308 153.11815982880364 
A 53.24644544322509 53.24644544322509 0 0 1 104.5753028970562 166.99740819858744 
A 28.640549150787812 28.640549150787812 0 0 1 75.0704046966308 153.11815982880364"></path><text text-anchor="middle" dy=".35em" x="89" y="161" style="fill: rgb(68, 68, 68); font-size: 12px;"><tspan x="89" y="161" dy="0.35em"></tspan></text></g><g class="venn-area venn-circle venn-sets-The Dears"><path style="fill-opacity: 0.25; fill: rgb(44, 160, 44); stroke-opacity: 0; stroke: rgb(255, 255, 255); stroke-width: 0;" d="
M 99.84613957559345 138.75 
m -28.640549150787812 0 
a 28.640549150787812 28.640549150787812 0 1 0 57.281098301575625 0 
a 28.640549150787812 28.640549150787812 0 1 0 -57.281098301575625 0"></path><text text-anchor="middle" dy=".35em" x="89" y="137" style="fill: rgb(44, 160, 44); font-size: 12px;"><tspan x="89" y="137" dy="-0.20000000000000007em">The</tspan><tspan x="89" y="137" dy="0.9em">Dears</tspan></text></g></svg></div>
</div>
</div>
</div>

You can also see the same effect with
<a id="gnr"> "Guns N Roses" vs "Guns N' Roses"</a> and
<a id="nickcave">"Nick Cave and
the Bad Seeds" vs "Nick Cave &amp; the Bad Seeds"</a>. Feel free to enter other artists, but keep
in mind this dataset is from 2008 so more modern artists aren't represented here.

While LSA successfully generalizes some aspects of our data, the results here aren't
all that good. Take a look at the results for <a id="bobdylan">Bob Dylan</a> for an example.

To do a better job while still maintaining this ability to generalize, we need to come up with a
better model.

### Implicit Alternating Least Squares

Recommender systems frequently use matrix factorization models to
[generate personalized  recommendations for users.](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf)
These models have been found to work well on recommending items, and can be easily reused
for calculating related artists.

Many of the MF models used in recommender systems assume explicit data, where the user
has rated both things they like and dislike using something like a 5 star rating scale.
They typically work by treating the missing data as an unknown, and then minimizing the
reconstruction error using SGD.

The data here is implicit though - we can assume that a user listening to an artist
means they like it, but we don't have the corresponding signal that a user
doesn't like an artist. Implicit data is usually
more plentiful and easier to collect than explicit data - and even when you have
the user give 5 star ratings [the vast majority of those ratings are going to be positive only](/rating-set-distributions) so you need to account for implicit behaviour anyways.

This means we can't just treat the missing data as unknowns, instead we have to treat a a user not
listening to an artist as being a signal that the user might not like that artist.

This presents a couple of challenges in learning a factorized representation.

The first challenge is in doing this factorization efficiently: by treating the unknowns as negatives,
the naive implementation would look at every single entry in our input matrix. Since the dimensionality
here is roughly 360K by 300K - there are over 100 billion total entries to consider, compared to
only 17 million non zero entries.

The second problem is that we can't be certain that a user not listening to an artist actually
means that they don't like it. There could be other reasons for the artist not being listened to,
especially considering that we only have the top 50 most played artists for each user in the
dataset.

The [Collaborative Filtering for Implicit Feedback Datasets](http://yifanhu.net/PUB/cf.pdf) paper accounts for
both of these challenges in an elegant way.

To handle the case where we're not confident about our negative data, this approach learns a factorized matrix
representation using different confidence levels on binary preferences: unseen items are treated as negative
with a low confidence, where present items are treated as positive with a much higher confidence.

The goal then is to learn user factors X<sub>u</sub> and artist factors Y<sub>i</sub> by minimizing
a confidence weighted sum of squared errors loss function:

> <span class="MathJax_Preview" style="color: inherit;"></span><div class="MathJax_Display" style="text-align: center;"><span class="MathJax" id="MathJax-Element-1-Frame" tabindex="0" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot; display=&quot;block&quot;><mi>l</mi><mi>o</mi><mi>s</mi><mi>s</mi><mo>=</mo><munder><mo>&amp;#x2211;</mo><mi>u</mi></munder><munder><mo>&amp;#x2211;</mo><mi>i</mi></munder><mrow class=&quot;MJX-TeXAtom-ORD&quot;><msub><mi>C</mi><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mi>u</mi><mi>i</mi></mrow></msub><mo stretchy=&quot;false&quot;>(</mo><msub><mi>P</mi><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mi>u</mi><mi>i</mi></mrow></msub><mo>&amp;#x2212;</mo><msub><mi>X</mi><mi>u</mi></msub><msub><mi>Y</mi><mi>i</mi></msub><msup><mo stretchy=&quot;false&quot;>)</mo><mn>2</mn></msup></mrow><mo>+</mo><mi>&amp;#x03BB;</mi><mo stretchy=&quot;false&quot;>(</mo><mo>&amp;#x2225;</mo><msub><mi>X</mi><mi>u</mi></msub><msup><mo>&amp;#x2225;</mo><mn>2</mn></msup><mo>+</mo><mo>&amp;#x2225;</mo><msub><mi>Y</mi><mi>i</mi></msub><msup><mo>&amp;#x2225;</mo><mn>2</mn></msup><mo stretchy=&quot;false&quot;>)</mo></math>" role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-1" role="math" style="width: 27.384em; display: inline-block;"><span style="display: inline-block; position: relative; width: 22.801em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.074em 1022.68em 3.574em -999.997em); top: -2.199em; left: 0.003em;"><span class="mrow" id="MathJax-Span-2"><span class="mi" id="MathJax-Span-3" style="font-family: MathJax_Math-italic;">l</span><span class="mi" id="MathJax-Span-4" style="font-family: MathJax_Math-italic;">o</span><span class="mi" id="MathJax-Span-5" style="font-family: MathJax_Math-italic;">s</span><span class="mi" id="MathJax-Span-6" style="font-family: MathJax_Math-italic;">s</span><span class="mo" id="MathJax-Span-7" style="font-family: MathJax_Main; padding-left: 0.301em;">=</span><span class="munderover" id="MathJax-Span-8" style="padding-left: 0.301em;"><span style="display: inline-block; position: relative; width: 1.432em; height: 0px;"><span style="position: absolute; clip: rect(2.86em 1001.37em 4.646em -999.997em); top: -3.985em; left: 0.003em;"><span class="mo" id="MathJax-Span-9" style="font-family: MathJax_Size2; vertical-align: 0.003em;">∑</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; clip: rect(3.515em 1000.42em 4.289em -999.997em); top: -2.914em; left: 0.539em;"><span class="mi" id="MathJax-Span-10" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="munderover" id="MathJax-Span-11" style="padding-left: 0.182em;"><span style="display: inline-block; position: relative; width: 1.432em; height: 0px;"><span style="position: absolute; clip: rect(2.86em 1001.37em 4.646em -999.997em); top: -3.985em; left: 0.003em;"><span class="mo" id="MathJax-Span-12" style="font-family: MathJax_Size2; vertical-align: 0.003em;">∑</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; clip: rect(3.336em 1000.24em 4.289em -999.997em); top: -2.914em; left: 0.598em;"><span class="mi" id="MathJax-Span-13" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="texatom" id="MathJax-Span-14" style="padding-left: 0.182em;"><span class="mrow" id="MathJax-Span-15"><span class="msubsup" id="MathJax-Span-16"><span style="display: inline-block; position: relative; width: 1.432em; height: 0px;"><span style="position: absolute; clip: rect(3.098em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-17" style="font-family: MathJax_Math-italic;">C<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.717em;"><span class="texatom" id="MathJax-Span-18"><span class="mrow" id="MathJax-Span-19"><span class="mi" id="MathJax-Span-20" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span class="mi" id="MathJax-Span-21" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="mo" id="MathJax-Span-22" style="font-family: MathJax_Main;">(</span><span class="msubsup" id="MathJax-Span-23"><span style="display: inline-block; position: relative; width: 1.372em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-24" style="font-family: MathJax_Math-italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.122em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.658em;"><span class="texatom" id="MathJax-Span-25"><span class="mrow" id="MathJax-Span-26"><span class="mi" id="MathJax-Span-27" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span class="mi" id="MathJax-Span-28" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="mo" id="MathJax-Span-29" style="font-family: MathJax_Main; padding-left: 0.241em;">−</span><span class="msubsup" id="MathJax-Span-30" style="padding-left: 0.241em;"><span style="display: inline-block; position: relative; width: 1.313em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.84em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-31" style="font-family: MathJax_Math-italic;">X<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.836em;"><span class="mi" id="MathJax-Span-32" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-33"><span style="display: inline-block; position: relative; width: 0.896em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-34" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.182em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.598em;"><span class="mi" id="MathJax-Span-35" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-36"><span style="display: inline-block; position: relative; width: 0.836em; height: 0px;"><span style="position: absolute; clip: rect(3.039em 1000.3em 4.408em -999.997em); top: -3.985em; left: 0.003em;"><span class="mo" id="MathJax-Span-37" style="font-family: MathJax_Main;">)</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -4.402em; left: 0.42em;"><span class="mn" id="MathJax-Span-38" style="font-size: 70.7%; font-family: MathJax_Main;">2</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span></span></span><span class="mo" id="MathJax-Span-39" style="font-family: MathJax_Main; padding-left: 0.241em;">+</span><span class="mi" id="MathJax-Span-40" style="font-family: MathJax_Math-italic; padding-left: 0.241em;">λ</span><span class="mo" id="MathJax-Span-41" style="font-family: MathJax_Main;">(</span><span class="mo" id="MathJax-Span-42" style="font-family: MathJax_Main;">∥</span><span class="msubsup" id="MathJax-Span-43"><span style="display: inline-block; position: relative; width: 1.313em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.84em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-44" style="font-family: MathJax_Math-italic;">X<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.836em;"><span class="mi" id="MathJax-Span-45" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-46"><span style="display: inline-block; position: relative; width: 0.955em; height: 0px;"><span style="position: absolute; clip: rect(3.039em 1000.36em 4.408em -999.997em); top: -3.985em; left: 0.003em;"><span class="mo" id="MathJax-Span-47" style="font-family: MathJax_Main;">∥</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -4.402em; left: 0.479em;"><span class="mn" id="MathJax-Span-48" style="font-size: 70.7%; font-family: MathJax_Main;">2</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="mo" id="MathJax-Span-49" style="font-family: MathJax_Main; padding-left: 0.241em;">+</span><span class="mo" id="MathJax-Span-50" style="font-family: MathJax_Main; padding-left: 0.241em;">∥</span><span class="msubsup" id="MathJax-Span-51"><span style="display: inline-block; position: relative; width: 0.896em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-52" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.182em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.598em;"><span class="mi" id="MathJax-Span-53" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-54"><span style="display: inline-block; position: relative; width: 0.955em; height: 0px;"><span style="position: absolute; clip: rect(3.039em 1000.36em 4.408em -999.997em); top: -3.985em; left: 0.003em;"><span class="mo" id="MathJax-Span-55" style="font-family: MathJax_Main;">∥</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -4.402em; left: 0.479em;"><span class="mn" id="MathJax-Span-56" style="font-size: 70.7%; font-family: MathJax_Main;">2</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="mo" id="MathJax-Span-57" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.205em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -1.496em; border-left-width: 0px; border-left-style: solid; width: 0px; height: 2.718em;"></span></span></nobr><span class="MJX_Assistive_MathML MJX_Assistive_MathML_Block" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML" display="block"><mi>l</mi><mi>o</mi><mi>s</mi><mi>s</mi><mo>=</mo><munder><mo>∑</mo><mi>u</mi></munder><munder><mo>∑</mo><mi>i</mi></munder><mrow class="MJX-TeXAtom-ORD"><msub><mi>C</mi><mrow class="MJX-TeXAtom-ORD"><mi>u</mi><mi>i</mi></mrow></msub><mo stretchy="false">(</mo><msub><mi>P</mi><mrow class="MJX-TeXAtom-ORD"><mi>u</mi><mi>i</mi></mrow></msub><mo>−</mo><msub><mi>X</mi><mi>u</mi></msub><msub><mi>Y</mi><mi>i</mi></msub><msup><mo stretchy="false">)</mo><mn>2</mn></msup></mrow><mo>+</mo><mi>λ</mi><mo stretchy="false">(</mo><mo>∥</mo><msub><mi>X</mi><mi>u</mi></msub><msup><mo>∥</mo><mn>2</mn></msup><mo>+</mo><mo>∥</mo><msub><mi>Y</mi><mi>i</mi></msub><msup><mo>∥</mo><mn>2</mn></msup><mo stretchy="false">)</mo></math></span></span></div><script type="math/tex; mode=display" id="MathJax-Element-1">loss = \sum_u \sum_i { C_{ui}(P_{ui} - X_uY_i)^2} + \lambda (\|X_u\|^2 + \|Y_i\|^2)
> </script>

<!--_-->

<span class="MathJax_Preview" style="color: inherit;"></span><span class="MathJax" id="MathJax-Element-2-Frame" tabindex="0" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><msub><mi>C</mi><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mi>u</mi><mi>i</mi></mrow></msub></math>" role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-58" role="math" style="width: 1.789em; display: inline-block;"><span style="display: inline-block; position: relative; width: 1.491em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.313em 1001.49em 2.562em -999.997em); top: -2.199em; left: 0.003em;"><span class="mrow" id="MathJax-Span-59"><span class="msubsup" id="MathJax-Span-60"><span style="display: inline-block; position: relative; width: 1.432em; height: 0px;"><span style="position: absolute; clip: rect(3.098em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-61" style="font-family: MathJax_Math-italic;">C<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.717em;"><span class="texatom" id="MathJax-Span-62"><span class="mrow" id="MathJax-Span-63"><span class="mi" id="MathJax-Span-64" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span class="mi" id="MathJax-Span-65" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.205em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.282em; border-left-width: 0px; border-left-style: solid; width: 0px; height: 1.146em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><msub><mi>C</mi><mrow class="MJX-TeXAtom-ORD"><mi>u</mi><mi>i</mi></mrow></msub></math></span></span><script type="math/tex" id="MathJax-Element-2">C_{ui}</script> is the confidence that we have that the user likes the artist, <span class="MathJax_Preview" style="color: inherit;"></span><span class="MathJax" id="MathJax-Element-3-Frame" tabindex="0" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><msub><mi>P</mi><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mi>u</mi><mi>i</mi></mrow></msub></math>" role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-66" role="math" style="width: 1.729em; display: inline-block;"><span style="display: inline-block; position: relative; width: 1.432em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.372em 1001.43em 2.562em -999.997em); top: -2.199em; left: 0.003em;"><span class="mrow" id="MathJax-Span-67"><span class="msubsup" id="MathJax-Span-68"><span style="display: inline-block; position: relative; width: 1.372em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-69" style="font-family: MathJax_Math-italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.122em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.658em;"><span class="texatom" id="MathJax-Span-70"><span class="mrow" id="MathJax-Span-71"><span class="mi" id="MathJax-Span-72" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span class="mi" id="MathJax-Span-73" style="font-size: 70.7%; font-family: MathJax_Math-italic;">i</span></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.205em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.282em; border-left-width: 0px; border-left-style: solid; width: 0px; height: 1.146em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><msub><mi>P</mi><mrow class="MJX-TeXAtom-ORD"><mi>u</mi><mi>i</mi></mrow></msub></math></span></span><script type="math/tex" id="MathJax-Element-3">P_{ui}</script> is a binary value
indicating if the user listened to the artist or not, and the <span class="MathJax_Preview" style="color: inherit;"></span><span class="MathJax" id="MathJax-Element-4-Frame" tabindex="0" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mi>&amp;#x03BB;</mi></math>" role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-74" role="math" style="width: 0.717em; display: inline-block;"><span style="display: inline-block; position: relative; width: 0.598em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.313em 1000.6em 2.384em -999.997em); top: -2.199em; left: 0.003em;"><span class="mrow" id="MathJax-Span-75"><span class="mi" id="MathJax-Span-76" style="font-family: MathJax_Math-italic;">λ</span></span><span style="display: inline-block; width: 0px; height: 2.205em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.068em; border-left-width: 0px; border-left-style: solid; width: 0px; height: 1.004em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><mi>λ</mi></math></span></span><script type="math/tex" id="MathJax-Element-4">\lambda</script> is a basic [L2
Regularizer](https://en.wikipedia.org/wiki/Regularization_(mathematics)) to reduce overfitting.

To minimize the user factors, we fix the item factors constant and then take the derivative
of the loss function to calculate X<sub>u</sub> directly:

> <span class="MathJax_Preview" style="color: inherit;"></span><div class="MathJax_Display" style="text-align: center;"><span class="MathJax" id="MathJax-Element-5-Frame" tabindex="0" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot; display=&quot;block&quot;><msub><mi>X</mi><mi>u</mi></msub><mo>=</mo><mo stretchy=&quot;false&quot;>(</mo><msup><mi>Y</mi><mi>T</mi></msup><msub><mi>C</mi><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mi>u</mi></mrow></msub><mi>Y</mi><mo>+</mo><mi>&amp;#x03BB;</mi><mi>I</mi><msup><mo stretchy=&quot;false&quot;>)</mo><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mo>&amp;#x2212;</mo><mn>1</mn></mrow></msup><mo stretchy=&quot;false&quot;>(</mo><msup><mi>Y</mi><mi>T</mi></msup><msub><mi>C</mi><mi>U</mi></msub><msub><mi>P</mi><mi>u</mi></msub><mo stretchy=&quot;false&quot;>)</mo></math>" role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-77" role="math" style="width: 17.86em; display: inline-block;"><span style="display: inline-block; position: relative; width: 14.884em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.134em 1014.77em 2.622em -999.997em); top: -2.199em; left: 0.003em;"><span class="mrow" id="MathJax-Span-78"><span class="msubsup" id="MathJax-Span-79"><span style="display: inline-block; position: relative; width: 1.313em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.84em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-80" style="font-family: MathJax_Math-italic;">X<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.836em;"><span class="mi" id="MathJax-Span-81" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="mo" id="MathJax-Span-82" style="font-family: MathJax_Main; padding-left: 0.301em;">=</span><span class="mo" id="MathJax-Span-83" style="font-family: MathJax_Main; padding-left: 0.301em;">(</span><span class="msubsup" id="MathJax-Span-84"><span style="display: inline-block; position: relative; width: 1.432em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-85" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.182em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -4.402em; left: 0.896em;"><span class="mi" id="MathJax-Span-86" style="font-size: 70.7%; font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-87"><span style="display: inline-block; position: relative; width: 1.193em; height: 0px;"><span style="position: absolute; clip: rect(3.098em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-88" style="font-family: MathJax_Math-italic;">C<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.717em;"><span class="texatom" id="MathJax-Span-89"><span class="mrow" id="MathJax-Span-90"><span class="mi" id="MathJax-Span-91" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="mi" id="MathJax-Span-92" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.182em;"></span></span><span class="mo" id="MathJax-Span-93" style="font-family: MathJax_Main; padding-left: 0.241em;">+</span><span class="mi" id="MathJax-Span-94" style="font-family: MathJax_Math-italic; padding-left: 0.241em;">λ</span><span class="mi" id="MathJax-Span-95" style="font-family: MathJax_Math-italic;">I<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span class="msubsup" id="MathJax-Span-96"><span style="display: inline-block; position: relative; width: 1.372em; height: 0px;"><span style="position: absolute; clip: rect(3.039em 1000.3em 4.408em -999.997em); top: -3.985em; left: 0.003em;"><span class="mo" id="MathJax-Span-97" style="font-family: MathJax_Main;">)</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -4.402em; left: 0.42em;"><span class="texatom" id="MathJax-Span-98"><span class="mrow" id="MathJax-Span-99"><span class="mo" id="MathJax-Span-100" style="font-size: 70.7%; font-family: MathJax_Main;">−</span><span class="mn" id="MathJax-Span-101" style="font-size: 70.7%; font-family: MathJax_Main;">1</span></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="mo" id="MathJax-Span-102" style="font-family: MathJax_Main;">(</span><span class="msubsup" id="MathJax-Span-103"><span style="display: inline-block; position: relative; width: 1.432em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-104" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.182em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -4.402em; left: 0.896em;"><span class="mi" id="MathJax-Span-105" style="font-size: 70.7%; font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-106"><span style="display: inline-block; position: relative; width: 1.313em; height: 0px;"><span style="position: absolute; clip: rect(3.098em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-107" style="font-family: MathJax_Math-italic;">C<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.717em;"><span class="mi" id="MathJax-Span-108" style="font-size: 70.7%; font-family: MathJax_Math-italic;">U<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-109"><span style="display: inline-block; position: relative; width: 1.134em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-110" style="font-family: MathJax_Math-italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.122em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.658em;"><span class="mi" id="MathJax-Span-111" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="mo" id="MathJax-Span-112" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.205em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.354em; border-left-width: 0px; border-left-style: solid; width: 0px; height: 1.504em;"></span></span></nobr><span class="MJX_Assistive_MathML MJX_Assistive_MathML_Block" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML" display="block"><msub><mi>X</mi><mi>u</mi></msub><mo>=</mo><mo stretchy="false">(</mo><msup><mi>Y</mi><mi>T</mi></msup><msub><mi>C</mi><mrow class="MJX-TeXAtom-ORD"><mi>u</mi></mrow></msub><mi>Y</mi><mo>+</mo><mi>λ</mi><mi>I</mi><msup><mo stretchy="false">)</mo><mrow class="MJX-TeXAtom-ORD"><mo>−</mo><mn>1</mn></mrow></msup><mo stretchy="false">(</mo><msup><mi>Y</mi><mi>T</mi></msup><msub><mi>C</mi><mi>U</mi></msub><msub><mi>P</mi><mi>u</mi></msub><mo stretchy="false">)</mo></math></span></span></div><script type="math/tex; mode=display" id="MathJax-Element-5">X_u =  (Y^TC_{u}Y + \lambda I)^{-1}(Y^TC_UP_u)
> 
> </script>

<!--_-->

<div>

The item factors are calculated in a similar way, and the entire thing is minimized by alternating back and forth
until it converges (hence the 'Alternative Least Squares' name).

The clever part of this paper is in how it learns over all data, but only has to do work on the non-zero items. 
Since <span class="MathJax_Preview" style="color: inherit;"></span><span class="MathJax" id="MathJax-Element-6-Frame" tabindex="0" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><msub><mi>P</mi><mi>u</mi></msub></math>" role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-113" role="math" style="width: 1.432em; display: inline-block;"><span style="display: inline-block; position: relative; width: 1.193em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.372em 1001.19em 2.562em -999.997em); top: -2.199em; left: 0.003em;"><span class="mrow" id="MathJax-Span-114"><span class="msubsup" id="MathJax-Span-115"><span style="display: inline-block; position: relative; width: 1.134em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-116" style="font-family: MathJax_Math-italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.122em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.658em;"><span class="mi" id="MathJax-Span-117" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.205em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.282em; border-left-width: 0px; border-left-style: solid; width: 0px; height: 1.146em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><msub><mi>P</mi><mi>u</mi></msub></math></span></span><script type="math/tex" id="MathJax-Element-6">P_u</script> is sparse (the negative preferences have a 0 value), <span class="MathJax_Preview" style="color: inherit;"></span><span class="MathJax" id="MathJax-Element-7-Frame" tabindex="0" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><msup><mi>Y</mi><mi>T</mi></msup><msub><mi>C</mi><mi>u</mi></msub><msub><mi>P</mi><mi>u</mi></msub></math>" role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-118" role="math" style="width: 4.586em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.812em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.193em 1003.81em 2.562em -999.997em); top: -2.199em; left: 0.003em;"><span class="mrow" id="MathJax-Span-119"><span class="msubsup" id="MathJax-Span-120"><span style="display: inline-block; position: relative; width: 1.432em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-121" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.182em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -4.342em; left: 0.896em;"><span class="mi" id="MathJax-Span-122" style="font-size: 70.7%; font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-123"><span style="display: inline-block; position: relative; width: 1.193em; height: 0px;"><span style="position: absolute; clip: rect(3.098em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-124" style="font-family: MathJax_Math-italic;">C<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.717em;"><span class="mi" id="MathJax-Span-125" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-126"><span style="display: inline-block; position: relative; width: 1.134em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-127" style="font-family: MathJax_Math-italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.122em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.658em;"><span class="mi" id="MathJax-Span-128" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.205em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.282em; border-left-width: 0px; border-left-style: solid; width: 0px; height: 1.361em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><msup><mi>Y</mi><mi>T</mi></msup><msub><mi>C</mi><mi>u</mi></msub><msub><mi>P</mi><mi>u</mi></msub></math></span></span><script type="math/tex" id="MathJax-Element-7">Y^TC_uP_u</script> can be easily calculated.
 To calculate <span class="MathJax_Preview" style="color: inherit;"></span><span class="MathJax" id="MathJax-Element-8-Frame" tabindex="0" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><msup><mi>Y</mi><mi>T</mi></msup><msub><mi>C</mi><mrow class=&quot;MJX-TeXAtom-ORD&quot;><mi>u</mi></mrow></msub><mi>Y</mi></math>" role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-129" role="math" style="width: 4.11em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.396em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.193em 1003.4em 2.562em -999.997em); top: -2.199em; left: 0.003em;"><span class="mrow" id="MathJax-Span-130"><span class="msubsup" id="MathJax-Span-131"><span style="display: inline-block; position: relative; width: 1.432em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-132" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.182em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -4.342em; left: 0.896em;"><span class="mi" id="MathJax-Span-133" style="font-size: 70.7%; font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="msubsup" id="MathJax-Span-134"><span style="display: inline-block; position: relative; width: 1.193em; height: 0px;"><span style="position: absolute; clip: rect(3.098em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-135" style="font-family: MathJax_Math-italic;">C<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.717em;"><span class="texatom" id="MathJax-Span-136"><span class="mrow" id="MathJax-Span-137"><span class="mi" id="MathJax-Span-138" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="mi" id="MathJax-Span-139" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.182em;"></span></span></span><span style="display: inline-block; width: 0px; height: 2.205em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.282em; border-left-width: 0px; border-left-style: solid; width: 0px; height: 1.361em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><msup><mi>Y</mi><mi>T</mi></msup><msub><mi>C</mi><mrow class="MJX-TeXAtom-ORD"><mi>u</mi></mrow></msub><mi>Y</mi></math></span></span><script type="math/tex" id="MathJax-Element-8">Y^TC_{u}Y</script> they note that it's equal to <span class="MathJax_Preview" style="color: inherit;"></span><span class="MathJax" id="MathJax-Element-9-Frame" tabindex="0" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><msup><mi>Y</mi><mi>T</mi></msup><mi>Y</mi><mo>+</mo><msup><mi>Y</mi><mi>T</mi></msup><mo stretchy=&quot;false&quot;>(</mo><msub><mi>C</mi><mi>u</mi></msub><mo>&amp;#x2212;</mo><mi>I</mi><mo stretchy=&quot;false&quot;>)</mo><mi>Y</mi></math>" role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-140" role="math" style="width: 11.313em; display: inline-block;"><span style="display: inline-block; position: relative; width: 9.408em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.193em 1009.41em 2.622em -999.997em); top: -2.199em; left: 0.003em;"><span class="mrow" id="MathJax-Span-141"><span class="msubsup" id="MathJax-Span-142"><span style="display: inline-block; position: relative; width: 1.432em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-143" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.182em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -4.342em; left: 0.896em;"><span class="mi" id="MathJax-Span-144" style="font-size: 70.7%; font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="mi" id="MathJax-Span-145" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.182em;"></span></span><span class="mo" id="MathJax-Span-146" style="font-family: MathJax_Main; padding-left: 0.241em;">+</span><span class="msubsup" id="MathJax-Span-147" style="padding-left: 0.241em;"><span style="display: inline-block; position: relative; width: 1.432em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-148" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.182em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -4.342em; left: 0.896em;"><span class="mi" id="MathJax-Span-149" style="font-size: 70.7%; font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="mo" id="MathJax-Span-150" style="font-family: MathJax_Main;">(</span><span class="msubsup" id="MathJax-Span-151"><span style="display: inline-block; position: relative; width: 1.193em; height: 0px;"><span style="position: absolute; clip: rect(3.098em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-152" style="font-family: MathJax_Math-italic;">C<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.717em;"><span class="mi" id="MathJax-Span-153" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="mo" id="MathJax-Span-154" style="font-family: MathJax_Main; padding-left: 0.241em;">−</span><span class="mi" id="MathJax-Span-155" style="font-family: MathJax_Math-italic; padding-left: 0.241em;">I<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span class="mo" id="MathJax-Span-156" style="font-family: MathJax_Main;">)</span><span class="mi" id="MathJax-Span-157" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.182em;"></span></span></span><span style="display: inline-block; width: 0px; height: 2.205em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.354em; border-left-width: 0px; border-left-style: solid; width: 0px; height: 1.432em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><msup><mi>Y</mi><mi>T</mi></msup><mi>Y</mi><mo>+</mo><msup><mi>Y</mi><mi>T</mi></msup><mo stretchy="false">(</mo><msub><mi>C</mi><mi>u</mi></msub><mo>−</mo><mi>I</mi><mo stretchy="false">)</mo><mi>Y</mi></math></span></span><script type="math/tex" id="MathJax-Element-9">Y^TY + Y^T(C_u-I)Y</script>. By setting the confidences for
negative items to 1, <span class="MathJax_Preview" style="color: inherit;"></span><span class="MathJax" id="MathJax-Element-10-Frame" tabindex="0" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><mo stretchy=&quot;false&quot;>(</mo><msub><mi>C</mi><mi>u</mi></msub><mo>&amp;#x2212;</mo><mi>I</mi><mo stretchy=&quot;false&quot;>)</mo></math>" role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-158" role="math" style="width: 4.527em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.753em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.253em 1003.63em 2.622em -999.997em); top: -2.199em; left: 0.003em;"><span class="mrow" id="MathJax-Span-159"><span class="mo" id="MathJax-Span-160" style="font-family: MathJax_Main;">(</span><span class="msubsup" id="MathJax-Span-161"><span style="display: inline-block; position: relative; width: 1.193em; height: 0px;"><span style="position: absolute; clip: rect(3.098em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-162" style="font-family: MathJax_Math-italic;">C<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -3.807em; left: 0.717em;"><span class="mi" id="MathJax-Span-163" style="font-size: 70.7%; font-family: MathJax_Math-italic;">u</span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="mo" id="MathJax-Span-164" style="font-family: MathJax_Main; padding-left: 0.241em;">−</span><span class="mi" id="MathJax-Span-165" style="font-family: MathJax_Math-italic; padding-left: 0.241em;">I<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span class="mo" id="MathJax-Span-166" style="font-family: MathJax_Main;">)</span></span><span style="display: inline-block; width: 0px; height: 2.205em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.354em; border-left-width: 0px; border-left-style: solid; width: 0px; height: 1.361em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><mo stretchy="false">(</mo><msub><mi>C</mi><mi>u</mi></msub><mo>−</mo><mi>I</mi><mo stretchy="false">)</mo></math></span></span><script type="math/tex" id="MathJax-Element-10">(C_u - I)</script> is sparse, and <span class="MathJax_Preview" style="color: inherit;"></span><span class="MathJax" id="MathJax-Element-11-Frame" tabindex="0" data-mathml="<math xmlns=&quot;http://www.w3.org/1998/Math/MathML&quot;><msup><mi>Y</mi><mi>T</mi></msup><mi>Y</mi></math>" role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-167" role="math" style="width: 2.682em; display: inline-block;"><span style="display: inline-block; position: relative; width: 2.205em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.193em 1002.21em 2.384em -999.997em); top: -2.199em; left: 0.003em;"><span class="mrow" id="MathJax-Span-168"><span class="msubsup" id="MathJax-Span-169"><span style="display: inline-block; position: relative; width: 1.432em; height: 0px;"><span style="position: absolute; clip: rect(3.158em 1000.78em 4.17em -999.997em); top: -3.985em; left: 0.003em;"><span class="mi" id="MathJax-Span-170" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.182em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span><span style="position: absolute; top: -4.342em; left: 0.896em;"><span class="mi" id="MathJax-Span-171" style="font-size: 70.7%; font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.063em;"></span></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span></span><span class="mi" id="MathJax-Span-172" style="font-family: MathJax_Math-italic;">Y<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.182em;"></span></span></span><span style="display: inline-block; width: 0px; height: 2.205em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.068em; border-left-width: 0px; border-left-style: solid; width: 0px; height: 1.146em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><msup><mi>Y</mi><mi>T</mi></msup><mi>Y</mi></math></span></span><script type="math/tex" id="MathJax-Element-11">Y^TY</script> can be precalculated for all users. 

</div>

Putting the entire algorithm together in python, and you get code like:

```python
def alternating_least_squares(Cui, factors, regularization, iterations=20):
    users, items = Cui.shape

    X = np.random.rand(users, factors) * 0.01
    Y = np.random.rand(items, factors) * 0.01

    Ciu = Cui.T.tocsr()
    for iteration in range(iterations):
        least_squares(Cui, X, Y, regularization)
        least_squares(Ciu, Y, X, regularization)

    return X, Y

def least_squares(Cui, X, Y, regularization):
    users, factors = X.shape
    YtY = Y.T.dot(Y)

    for u in range(users):
        # accumulate YtCuY + regularization * I in A
        A = YtY + regularization * np.eye(factors)

        # accumulate YtCuPu in b
        b = np.zeros(factors)

        for i, confidence in nonzeros(Cui, u):
            factor = Y[i]
            A += (confidence - 1) * np.outer(factor, factor)
            b += confidence * factor

        # Xu = (YtCuY + regularization * I)^-1 (YtCuPu)
        X[u] = np.linalg.solve(A, b)

```python

To call this, I'm using the same weighting for the confidence matrix as we used in LSA,
and then calculating related artists in the same way:

```python
artist_factors, user_factors = alternating_least_squares(bm25_weight(plays), 50)
```

This method leads to significantly better results than just using LSA. Take a look at a
[slopegraph](http://charliepark.org/slopegraphs/) comparing the results for Bob Dylan as an
example:

<div class="slopegraph well" style="background:#FFFFFF;">
    <div class="input-group">
        <div class="input-group-btn left" style="text-align:right">
            <button type="button" class="btn btn-default dropdown-toggle lleft" data-toggle="dropdown" style="width: 200px;">
              <span class="method">LSA</span> <span class="caret"></span>
            </button>

*   <a>LSA</a>
*   <a>Implicit ALS</a>
**   <a>Overlap</a>
*   <a>Jaccard</a>
*   <a>Ochiai</a>
*   <a>Cosine</a>
*   <a>Smoothed Cosine</a>
*   <a>TFIDF</a>
*   <a>BM25</a>
        </div>
        <span class="twitter-typeahead" style="position: relative"><input class="form-control typeahead middle tt-hint" readonly="" autocomplete="off" spellcheck="false" tabindex="-1" style="position: absolute; top: 0px; left: 0px; border-color: transparent; box-shadow: none; opacity: 1; background: none 0% 0% / auto repeat scroll padding-box border-box rgb(255, 255, 255);"><input class="form-control typeahead middle tt-input" id="set-artist-name" placeholder="Enter an Artist Name" autocomplete="off" spellcheck="false" dir="auto" style="position: relative; vertical-align: top; background-color: transparent;"><pre aria-hidden="true" style="position: absolute; visibility: hidden; white-space: pre; font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif; font-size: 14px; font-style: normal; font-variant: normal; font-weight: 400; word-spacing: 0px; letter-spacing: 0px; text-indent: 0px; text-rendering: auto; text-transform: none;"></pre><span class="tt-dropdown-menu" style="position: absolute; top: 100%; z-index: 100; display: none; left: 0px; right: auto;"><div class="tt-dataset-artists"></div></span></span>
        <div class="input-group-btn right">
            <button type="button" class="btn btn-default dropdown-toggle rright" data-toggle="dropdown" style="width: 200px;">
              <span class="caret"></span>
              <span class="method">Implicit ALS</span>
            </button>

*   <a>LSA</a>
*   <a>Implicit ALS</a>
**   <a>Overlap</a>
*   <a>Jaccard</a>
*   <a>Ochiai</a>
*   <a>Cosine</a>
*   <a>Smoothed Cosine</a>
*   <a>TFIDF</a>
*   <a>BM25</a>
        </div>
    </div>

* * *

#### 
        <span class="title">Bob Dylan</span>

        <small class="subtitle">LSA versus Implicit ALS</small>

    <div class="graph"><svg height="897.975376119831" width="710"><g><text class="lefttext" x="200" y="15" dy=".35em" text-anchor="end" style="font-size: 12px; fill: rgb(31, 119, 180);">Neil Young - #1</text><text class="righttext" x="510" y="15" dy=".35em" style="font-size: 12px; fill: rgb(255, 127, 14);">#1 - Neil Young</text><line x1="207.5" x2="502.5" y1="15" y2="15" stroke="black" stroke-width="1" stroke-opacity="0.4"></line></g><g><text class="lefttext" x="200" y="103.722839111673" dy=".35em" text-anchor="end" style="font-size: 12px; fill: rgb(31, 119, 180);">Loudon Wainwright Iii - #2</text><text class="righttext" x="510" y="499.3762731415374" dy=".35em" style="font-size: 12px; fill: rgb(255, 127, 14);">#44 - Loudon Wainwright Iii</text><line x1="207.5" x2="502.5" y1="103.722839111673" y2="499.3762731415374" stroke="black" stroke-width="1" stroke-opacity="0.4"></line></g><g><text class="lefttext" x="200" y="155.62237294951805" dy=".35em" text-anchor="end" style="font-size: 12px; fill: rgb(31, 119, 180);">Elvis Costello &amp; The Imposters - #3</text><text class="righttext" x="510" y="882.975376119831" dy=".35em" style="font-size: 12px; fill: rgb(255, 127, 14);">#881 - Elvis Costello &amp; The Imposters</text><line x1="207.5" x2="502.5" y1="155.62237294951805" y2="882.975376119831" stroke="black" stroke-width="1" stroke-opacity="0.4"></line></g><g><text class="lefttext" x="200" y="192.445678223346" dy=".35em" text-anchor="end" style="font-size: 12px; fill: rgb(31, 119, 180);">Tim Keller - #4</text><text class="righttext" x="510" y="711.074151542169" dy=".35em" style="font-size: 12px; fill: rgb(255, 127, 14);">#230 - Tim Keller</text><line x1="207.5" x2="502.5" y1="192.445678223346" y2="711.074151542169" stroke="black" stroke-width="1" stroke-opacity="0.4"></line></g><g><text class="lefttext" x="200" y="221.00805279156484" dy=".35em" text-anchor="end" style="font-size: 12px; fill: rgb(31, 119, 180);">The Umbrellas Of Cherbourg - #5</text><text class="righttext" x="510" y="711.6294669467895" dy=".35em" style="font-size: 12px; fill: rgb(255, 127, 14);">#231 - The Umbrellas Of Cherbourg</text><line x1="207.5" x2="502.5" y1="221.00805279156484" y2="711.6294669467895" stroke="black" stroke-width="1" stroke-opacity="0.4"></line></g><g><text class="lefttext" x="200" y="244.34521206119103" dy=".35em" text-anchor="end" style="font-size: 12px; fill: rgb(31, 119, 180);">Muy Cansado - #6</text><text class="righttext" x="510" y="641.9234943937166" dy=".35em" style="font-size: 12px; fill: rgb(255, 127, 14);">#134 - Muy Cansado</text><line x1="207.5" x2="502.5" y1="244.34521206119103" y2="641.9234943937166" stroke="black" stroke-width="1" stroke-opacity="0.4"></line></g><g><text class="lefttext" x="200" y="264.0764990790801" dy=".35em" text-anchor="end" style="font-size: 12px; fill: rgb(31, 119, 180);">Elvis Costello - #7</text><text class="righttext" x="510" y="427.0161055831297" dy=".35em" style="font-size: 12px; fill: rgb(255, 127, 14);">#25 - Elvis Costello</text><line x1="207.5" x2="502.5" y1="264.0764990790801" y2="427.0161055831297" stroke="black" stroke-width="1" stroke-opacity="0.4"></line></g><g><text class="lefttext" x="200" y="391.88818933330435" dy=".35em" text-anchor="end" style="font-size: 12px; fill: rgb(31, 119, 180);">Bob Dylan And The Band - #19</text><text class="righttext" x="510" y="103.722839111673" dy=".35em" style="font-size: 12px; fill: rgb(255, 127, 14);">#2 - Bob Dylan And The Band</text><line x1="207.5" x2="502.5" y1="391.88818933330435" y2="103.722839111673" stroke="black" stroke-width="1" stroke-opacity="0.4"></line></g><g><text class="lefttext" x="200" y="515.7389446948027" dy=".35em" text-anchor="end" style="font-size: 12px; fill: rgb(31, 119, 180);">Donovan - #50</text><text class="righttext" x="510" y="192.445678223346" dy=".35em" style="font-size: 12px; fill: rgb(255, 127, 14);">#4 - Donovan</text><line x1="207.5" x2="502.5" y1="515.7389446948027" y2="192.445678223346" stroke="black" stroke-width="1" stroke-opacity="0.4"></line></g><g><text class="lefttext" x="200" y="590.9756378022739" dy=".35em" text-anchor="end" style="font-size: 12px; fill: rgb(31, 119, 180);">The Band - #90</text><text class="righttext" x="510" y="155.62237294951805" dy=".35em" style="font-size: 12px; fill: rgb(255, 127, 14);">#3 - The Band</text><line x1="207.5" x2="502.5" y1="590.9756378022739" y2="155.62237294951805" stroke="black" stroke-width="1" stroke-opacity="0.4"></line></g><g><text class="lefttext" x="200" y="621.2334013944954" dy=".35em" text-anchor="end" style="font-size: 12px; fill: rgb(31, 119, 180);">Van Morrison - #114</text><text class="righttext" x="510" y="244.34521206119103" dy=".35em" style="font-size: 12px; fill: rgb(255, 127, 14);">#6 - Van Morrison</text><line x1="207.5" x2="502.5" y1="621.2334013944954" y2="244.34521206119103" stroke="black" stroke-width="1" stroke-opacity="0.4"></line></g><g><text class="lefttext" x="200" y="657.2118191123103" dy=".35em" text-anchor="end" style="font-size: 12px; fill: rgb(31, 119, 180);">The Kinks - #151</text><text class="righttext" x="510" y="221.00805279156484" dy=".35em" style="font-size: 12px; fill: rgb(255, 127, 14);">#5 - The Kinks</text><line x1="207.5" x2="502.5" y1="657.2118191123103" y2="221.00805279156484" stroke="black" stroke-width="1" stroke-opacity="0.4"></line></g><g><text class="lefttext" x="200" y="704.8011814165121" dy=".35em" text-anchor="end" style="font-size: 12px; fill: rgb(31, 119, 180);">Buffalo Springfield - #219</text><text class="righttext" x="510" y="264.0764990790801" dy=".35em" style="font-size: 12px; fill: rgb(255, 127, 14);">#7 - Buffalo Springfield</text><line x1="207.5" x2="502.5" y1="704.8011814165121" y2="264.0764990790801" stroke="black" stroke-width="1" stroke-opacity="0.4"></line></g></svg></div>
</div>

The irrelevant results returned by LSA here are pushed out of the head of the list and are
replaced by relevant results.

The nice thing about Implicit ALS is that it still generalizes the input successfully. As an example,
both <a id="gnr_slope">Guns N' Roses</a> and <a id="nickcave_slope">Nick Cave And
The Bad Seeds</a> each still bring up their synonyms, just without some of the marginal results being returned by LSA.

### Performance

The are a couple performance challenges in computing related artists like this.

The Implicit ALS code I posted above here isn't terribly fast. Since each user factor
can be calculated independently of each other, this algorithm is embarrassingly parallel and well
suited to multithreading - which pure Python code doesn't do well at.

To overcome this problem, I [published a fast python
version](https://github.com/benfred/implicit) that uses Cython and OpenMP to parallelize
the computation. On this task,
it is [currently 1.8 times faster](https://github.com/benfred/implicit/blob/master/examples/benchmark.py) than the multithreaded C++ version that Quora just published in their [QMF
library](https://github.com/quora/qmf).

By parallelizing computation, this library can factorize the Last.fm dataset in about 40 seconds
on a
[c4.8xlarge](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/c4-instances.html#c4-instances-cpu-support) EC2 instance (50 factors, 15 iterations).

The problem is that using these factors to calculate all the most related artists takes about
an hour once the factorization is done. To do the exact nearest neighbour calculation is a
O(N<sup>2</sup>) calculation since we need to compare each artist to each other artist.

It's possible to do a much faster job using an approximate nearest neighbours search.
The [annoy](http://github.com/spotify/annoy) package uses a random hyperplane splitting approach
to build up a forest of search trees to efficiently calculate the nearest neighbours.

The annoy package comes with Python bindings. To generate the approximate nearest neighbours
using cosine distance using these bindings is pretty simple:

```python
class ApproximateTopRelated(object):
    def __init__(self, artist_factors, treecount=20):
        index = annoy.AnnoyIndex(artist_factors.shape[1], 'angular')
        for i, row in enumerate(artist_factors):
            index.add_item(i, row)
        index.build(treecount)
        self.index = index

    def get_related(self, artistid, N=10):
        neighbours = self.index.get_nns_by_item(artistid, N)
        return sorted(((other, 1 - self.index.get_distance(artistid, other))
                      for other in neighbours), key=lambda x: -x[1])
```

The results using this method are largely identical, and reduce the time to compute over all
artists from an hour to about 30 seconds - which includes the time to build up the index in the
first place.

Erik Bernhardsson has a [series](http://erikbern.com/2015/09/24/nearest-neighbor-methods-vector-models-part-1/)
of
[excellent posts](http://erikbern.com/2015/10/20/nearest-neighbors-and-vector-models-epilogue-curse-of-dimensionality/)
on [how annoy works](http://erikbern.com/2015/10/01/nearest-neighbors-and-vector-models-part-2-how-to-search-in-high-dimensional-spaces/)
if you're interested in reading more on this.

### Conclusion

Matrix Factorization methods like Implicit ALS are typically used to generate personalized results - but there are
some upsides to using these models for the much simpler task of generating lists of related artists.

In fact, Spotify uses a matrix factorization technique called
[Logistic Matrix Factorization](http://stanford.edu/~rezab/nips2014workshop/submits/logmat.pdf) to
generate their lists of related artists.
This method has a similar idea to Implicit ALS: it's a confidence weighted
factorization on binary preference data - but uses a logistic loss instead of a least squares
loss. The paper has some examples where Logistic Matrix Factorization does a better job calculating similar artists
than Implicit ALS [(2)](#footnote2).

There are many other matrix factorization methods that can be used instead of the couple of talked
about here though. For instance, [Bayesian Personalized
Ranking](http://www.algo.uni-konstanz.de/members/rendle/pdf/Rendle_et_al2009-Bayesian_Personalized_Ranking.pdf),
and [Collaborative Less-is-More
Filtering](http://www.ci.tuwien.ac.at/~alexis/Publications_files/climf-recsys12.pdf) both attempt
to learn a factorized representation that optimizes the ranking of artists for each user.

The real power with these models are in generating personalized recommendations for each
user - which I'm hoping to talk more about in a future post.

##### Footnote 1

In techniques like LSA, it's common to use TFIDF to weight the input matrix before doing the matrix
factorization.
However from the previous post, we know that BM25 weighting produces good results in calculating
similarity between artists - so I'm using that weighting instead here. See the [example app in my Implicit
library](https://github.com/benfred/implicit/blob/master/examples/lastfm.py) for code on how
to do this efficiently.


##### Footnote 2

The results here for Implicit ALS are much better than those reported in the Logistic Matrix
Factorization paper (aside from Mumford &amp; Sons, who got popular after the Last.fm dataset was released). This is mainly
because of how I am weighting the confidence matrix -  by using a simple linear weighting I also get terrible results.
