原文：[Visualizing relationships between python packages](https://kozikow.com/2016/07/10/visualizing-relationships-between-python-packages-2/)

---

## Introduction

I extracted co-occurence of top 3500 python packages in github repos using the
the [github data on BigQuery](https://github.com/blog/2201-making-open-source-
data-more-available%20). I implemented the visualization [force layout in d3
via the velocity verlet integration](https://github.com/d3/d3-force). I also
clustered the graph using algorithms from [python-
igraph](https://pypi.python.org/pypi/python-igraph) and updated it to
<http://graphistry.com/>.

See the screenshot of the numpy cluster in the d3 visualization (click image
for live version):

[![screenshot.png](https://kozikow.files.wordpress.com/2016/07/screenshot1.png
?w=1140)](http://clustering.kozikow.com?center=numpy)

See just the numpy cluster extracted from the graphistry (click image for live
version): [![graphistry.png](https://kozikow.files.wordpress.com/2016/07/graph
istry1.png?w=1140)](https://labs.graphistry.com/graph/graph.html?dataset=PyGra
phistry/5R2115KURX&type=vgraph&splashAfter=1468271796&info=true&static=true&co
ntentKey=numpycluster&play=0&center=true&menu=false&goLive=false&left=-1.44e+3
&right=973&top=-478&bottom=657&poi=true)

Graph properties:

  * Each node is each python package found on github. Radius is calculated in DataFrame with nodes section.
  * For two packages A and B, weight of an edge is ![\(\\frac{ |A \\cap B|}{min\(|A|, |B|\)}\)^2](https://s0.wp.com/latex.php?latex=%28%5Cfrac%7B+%7CA+%5Ccap+B%7C%7D%7Bmin%28%7CA%7C%2C+%7CB%7C%29%7D%29%5E2&bg=%23ffffff&fg=%23000000&s=0), where ![|A \\cap B|](https://s0.wp.com/latex.php?latex=%7CA+%5Ccap+B%7C&bg=%23ffffff&fg=%23000000&s=0) is number of occurrences of packages A and B within the same file. I will migrate it to the [normalized pointwise mutual information](https://en.wikipedia.org/wiki/Pointwise_mutual_information#Normalized_pointwise_mutual_information_.28npmi.29) soon, since it is a bit hard to calculate it using the BigQuery.
  * Edges with weight smaller than 0.1 are removed.
  * The d3 algorithm searches for minimal energy state by the velocity verlet Integration according to simulation parameters.

You can my app at <http://clustering.kozikow.com?center=numpy>. You can:

  * Pass different package names as a query argument in the URL.
  * Scroll the page horizontally and vertically.
  * Click a node to open the pypi. Note that not all packages are on pypi.

Interesting graphistry views are in the next section, Analysis of specific
clusters.

Graph visualizations often lack actionable insights except looking cool. Types
of insights you can use this for:

  * Find packages you have been not aware of in the close proximity of other packages that you use.
  * Evaluate different web development frameworks based on size, adoption and library availability (e.g. [Flask](https://labs.graphistry.com/graph/graph.html?dataset=PyGraphistry/5R2115KURX&type=vgraph&splashAfter=1468271796&info=true&static=true&contentKey=flashcluster&play=0&center=true&menu=false&goLive=false&left=-1.44e+3&right=973&top=-478&bottom=657&poi=true) vs [django](https://labs.graphistry.com/graph/graph.html?dataset=PyGraphistry/5R2115KURX&type=vgraph&splashAfter=1468271796&info=true&static=true&contentKey=djangocluster&play=0&center=true&menu=false&goLive=false&left=-1.44e+3&right=973&top=-478&bottom=657&poi=true)).
  * Find some interesting python use cases, like [robotics cluster](http://clustering.kozikow.com/?center=rospy).

[Revision history of this post is on github](https://github.com/kozikow
/kozikow-blog/blob/master/clustering/clustering.org) in the [org
mode](https://kozikow.com/2016/05/21/very-powerful-data-analysis-environment-
org-mode-with-ob-ipython/).

## Analysis of specific clusters

In addition to d3 visualization I also clustered the data using the [python-
igraph](https://pypi.python.org/pypi/python-igraph)
`community_infomap().membership` and uploaded it to graphistry. Ability to
exclude and filter by clusters was very useful.

### Scientific computing cluster

Unsurprisingly, it is centered on numpy. It is interesting that it is possible
to see the divide between statistics and machine learning.

  * [d3 link](http://clustering.kozikow.com/?center=numpy)
  * [graphistry link](https://labs.graphistry.com/graph/graph.html?dataset=PyGraphistry/5R2115KURX&type=vgraph&splashAfter=1468271796&info=true&static=true&contentKey=numpycluster&play=0&center=true&menu=false&goLive=false&left=-1.44e+3&right=973&top=-478&bottom=657&poi=true)

### Web frameworks clusters

Web frameworks are interesting:

#### d3 links

  * It could be said that [sqlalchemy](http://clustering.kozikow.com/?center=sqlalchemy) is a center of web frameworks land.
  * Found nearby, there's a massive and monolithic cluster for [django](http://clustering.kozikow.com/?center=django).
  * Smaller nearby clusters for [flask](http://clustering.kozikow.com/?center=flask) and [pyramid](http://clustering.kozikow.com/?center=pyramid).
  * [pylons](http://clustering.kozikow.com/?center=pylons), lacking a cluster of its own, in between django and sqlalchemy.
  * Small cluster for [zope](http://www.zope.org/), also nearby sqlalchemy
  * [tornado](http://clustering.kozikow.com/?center=tornado) got swallowed by the big cluster of standard library in the middle, but is still close to other web frameworks.
  * Some smaller web frameworks like [gluon (web2py)](http://clustering.kozikow.com/?center=gluon) or [turbo gears](http://clustering.kozikow.com/?center=tg) ended up close to django, but barely visible and without clusters of their own.

#### Interesting graphistry clusters

  * [Django cluster](https://labs.graphistry.com/graph/graph.html?dataset=PyGraphistry/5R2115KURX&type=vgraph&splashAfter=1468271796&info=true&static=true&contentKey=djangocluster&play=0&center=true&menu=false&goLive=false&left=-1.44e+3&right=973&top=-478&bottom=657&poi=true)
  * [Flask cluster](https://labs.graphistry.com/graph/graph.html?dataset=PyGraphistry/5R2115KURX&type=vgraph&splashAfter=1468271796&info=true&static=true&contentKey=flashcluster&play=0&center=true&menu=false&goLive=false&left=-1.44e+3&right=973&top=-478&bottom=657&poi=true)
  * [Twisted cluster](https://labs.graphistry.com/graph/graph.html?dataset=PyGraphistry/5R2115KURX&type=vgraph&splashAfter=1468271796&info=true&static=true&contentKey=twistedcluster&play=0&center=true&menu=false&goLive=false&left=-1.44e+3&right=973&top=-478&bottom=657&poi=true)

### Other interesting clusters

Looking at results of clustering algorithm, only "medium sized" clusters are
interesting. A few first are obvious like clusters dominated by packages like
os and sys. Very small clusters are not interesting either. [Here you can see
clusters between positions 5 and 30 according to size](https://labs.graphistry
.com/graph/graph.html?dataset=PyGraphistry/5R2115KURX&type=vgraph&splashAfter=
1468271796&info=true&static=true&contentKey=topclusters&play=0&center=true&men
u=false&goLive=false&left=-1.44e+3&right=973&top=-478&bottom=657&poi=true).

Some of the other clusters:

  * Testing cluster, [d3 link](http://clustering.kozikow.com/?center=unittest), [graphistry cluster](https://labs.graphistry.com/graph/graph.html?dataset=PyGraphistry/5R2115KURX&type=vgraph&splashAfter=1468271796&info=true&static=true&contentKey=testclusters&play=0&center=true&menu=false&goLive=false&left=-1.44e+3&right=973&top=-478&bottom=657&poi=true)
  * Openstack cluster, [d3 link](http://clustering.kozikow.com/?center=nova), [graphistry link](https://labs.graphistry.com/graph/graph.html?dataset=PyGraphistry/5R2115KURX&type=vgraph&splashAfter=1468271796&info=true&static=true&contentKey=stackcluster&play=0&center=true&menu=false&goLive=false&left=-1.44e+3&right=973&top=-478&bottom=657&poi=true)
  * [String parsing and formatting cluster](https://labs.graphistry.com/graph/graph.html?dataset=PyGraphistry/5R2115KURX&type=vgraph&splashAfter=1468271796&info=true&static=true&contentKey=stringcluster&play=0&center=true&menu=false&goLive=false&left=-1.44e+3&right=973&top=-478&bottom=657&poi=true)
  * [Robotics land](http://clustering.kozikow.com/?center=rospy)
  * [gaming cluster](http://clustering.kozikow.com/?center=pygame)
  * [deep learning cluster](https://labs.graphistry.com/graph/graph.html?dataset=PyGraphistry/5R2115KURX&type=vgraph&splashAfter=1468271796&info=true&static=true&contentKey=deepcluster&play=0&center=true&menu=false&goLive=false&left=-1.44e+3&right=973&top=-478&bottom=657&poi=true)

## Potentials for further analysis

### Other programming languages

Majority of the code is not specific to python. Only the first step, create a
table with packages, is specific to python.

I had to do a lot of work on fitting the parameters in Simulation parameters
to make the graph look good enough. I suspect that I would have to do similar
fitting to each language, as each language graph would have different
properties.

I will be working on analyzing Java and Scala next.

### Search for "Alternatives to package X", e.g. seaborn vs bokeh

For example, it would be interesting to cluster together all python data
visualization packages.

Intuitively, such packages would be used in similar context, but would be
rarely used together. Assuming that our graph is represented as npmi
coincidence matrix M, for packages x and y, correlation of vectors x and y
would be high, but M[x][y] would be low.

Alternatively, `M^2 /. M` could have some potential. M^2 would roughly
represent "two hops" in the graph, while `/.` is a pointwise division.

e high correlation of their neighbor weights, but low direct edge.

This would work in many situations, but there are some others it wouldn't
handle well. Example case it wouldn't handle well:

  * sqlalchemy is an alternative to django built-in ORM.
  * django ORM is only used in django.
  * django ORM is not well usable in other web frameworks like flask.
  * other web frameworks make heavy use of flask ORM, but not django built-in ORM.

Therefore, django ORM and sqlalchemy wouldn't have their neighbor weights
correlated. I might got some ORM details wrong, as I don't do much web dev.

I also plan to experiment with [node2vec](http://arxiv.org/abs/1607.00653) or
squaring the adjacency matrix.

### Within repository relationship

Currently, I am only looking at imports within the same file. It could be
interesting to look at the same graph built using "within same repository"
relationship, or systematically compare the "within same repository" and
"within same file" relationships.

### Join with pypi

It could be interesting to compare usages on github with pypi downloads. [Pypi
is also accessible on BigQuery.](https://mail.python.org/pipermail/distutils-
sig/2016-May/028986.html)

## Data

  * [Post-processed JSON data used by d3](http://clustering.kozikow.com/graph.js)
  * [Publicly available BigQuery tables with all the data](https://bigquery.cloud.google.com/dataset/wide-silo-135723:github_clustering). See Reproduce section to see how each table was generated.

## Steps to reproduce

### Extract data from BigQuery

#### Create a table with packages

Save to wide-silo-135723:github_clustering.packages_in_file_py:

[code]

    SELECT
      id,
      NEST(UNIQUE(COALESCE(
          REGEXP_EXTRACT(line, r"^from ([a-zA-Z0-9_-]+).*import"),
          REGEXP_EXTRACT(line, r"^import ([a-zA-Z0-9_-]+)")))) AS package
    FROM (
      SELECT
        id AS id,
        LTRIM(SPLIT(content, "\n")) AS line,
      FROM
        [fh-bigquery:github_extracts.contents_py]
      HAVING
        line CONTAINS "import")
    GROUP BY id
    HAVING LENGTH(package) > 0;
    
[/code]

Table will have two fields - id representing the file and repeated field with
packages in the single file. Repeated fields are like arrays - [the best
description of repeated fields I
found.](http://stackoverflow.com/questions/32020714/what-does-repeated-field-
in-google-bigquery-mean)

This is the only step that is specific for python.

#### Verify the packages_in_file_py table

Check that imports have been correctly parsed out from some [random
file](https://github.com/sunzhxjs/JobGIS/blob/master/lib/python2.7/site-
packages/pandas/core/format.py).

[code]

    SELECT
        GROUP_CONCAT(package, ", ") AS packages,
        COUNT(package) AS count
    FROM [wide-silo-135723:github_clustering.packages_in_file_py]
    WHERE id == "009e3877f01393ae7a4e495015c0e73b5aa48ea7"
    
[/code]

packages | count  
---|---  
distutils, itertools, numpy, decimal, pandas, csv, warnings, future, IPython,
math, locale, sys | 12  
  
#### Filter out not popular packages

[code]

    SELECT
      COUNT(DISTINCT(package))
    FROM (SELECT
      package,
      count(id) AS count
    FROM [wide-silo-135723:github_clustering.packages_in_file_py]
    GROUP BY 1)
    WHERE count > 200;
    
[/code]

There are 3501 packages with at least 200 occurrences and it seems like a fine
cut off point. Create a filtered table, wide-
silo-135723:github_clustering.packages_in_file_top_py:

[code]

    SELECT
        id,
        NEST(package) AS package
    FROM (SELECT
            package,
            count(id) AS count,
            NEST(id) AS id
        FROM [wide-silo-135723:github_clustering.packages_in_file_py]
        GROUP BY 1)
    WHERE count > 200
    GROUP BY id;
    
[/code]

Results are in [wide-silo-135723:github_clustering.packages_in_file_top_py].

[code]

    SELECT
        COUNT(DISTINCT(package))
    FROM [wide-silo-135723:github_clustering.packages_in_file_top_py];
    
[/code]

[code]

    3501
    
[/code]

#### Generate graph edges

I will generate edges and save it to table wide-
silo-135723:github_clustering.packages_in_file_edges_py.

[code]

    SELECT
      p1.package AS package1,
      p2.package AS package2,
      COUNT(*) AS count
    FROM (SELECT
      id,
      package
    FROM FLATTEN([wide-silo-135723:github_clustering.packages_in_file_top_py], package)) AS p1
    JOIN 
    (SELECT
      id,
      package
    FROM [wide-silo-135723:github_clustering.packages_in_file_top_py]) AS p2
    ON (p1.id == p2.id)
    GROUP BY 1,2
    ORDER BY count DESC;
    
[/code]

Top 10 edges:

[code]

    SELECT
        package1,
        package2,
        count AS count
    FROM [wide-silo-135723:github_clustering.packages_in_file_edges_py]
    WHERE package1 < package2
    ORDER BY count DESC
    LIMIT 10;
    
[/code]

package1 | package2 | count  
---|---|---  
os | sys | 393311  
os | re | 156765  
os | time | 156320  
logging | os | 134478  
sys | time | 133396  
re | sys | 122375  
__future__ | django | 119335  
__future__ | os | 109319  
os | subprocess | 106862  
datetime | django | 94111  
  
#### Filter out irrelevant edges

Quantiles of the edge weight:

[code]

    SELECT
        GROUP_CONCAT(STRING(QUANTILES(count, 11)), ", ")
    FROM [wide-silo-135723:github_clustering.packages_in_file_edges_py];
    
[/code]

[code]

    1, 1, 1, 2, 3, 4, 7, 12, 24, 70, 1005020
    
[/code]

In my first implementation I filtered edges out based on the total count. It
was not a good approach, as a small relationship between two big packages was
more likely to stay than strong relationship between too small packages.

Create wide-silo-135723:github_clustering.packages_in_file_nodes_py:

[code]

    SELECT
      package AS package,
      COUNT(id) AS count
    FROM [github_clustering.packages_in_file_top_py]
    GROUP BY 1;
    
[/code]

package | count  
---|---  
os | 1005020  
sys | 784379  
django | 618941  
__future__ | 445335  
time | 359073  
re | 349309  
  
Create the table packages_in_file_edges_top_py:

[code]

    SELECT
        edges.package1 AS package1,
        edges.package2 AS package2,
        # WordPress gets confused by less than sign after nodes1.count
        edges.count / IF(nodes1.count nodes2.count,
            nodes1.count,
            nodes2.count) AS strength,
        edges.count AS count
    FROM [wide-silo-135723:github_clustering.packages_in_file_edges_py] AS edges
    JOIN [wide-silo-135723:github_clustering.packages_in_file_nodes_py] AS nodes1
        ON edges.package1 == nodes1.package
    JOIN [wide-silo-135723:github_clustering.packages_in_file_nodes_py] AS nodes2
        ON edges.package2 == nodes2.package
    HAVING strength > 0.33
    AND package1 <= package2;
    
[/code]

[Full results in google docs.](https://docs.google.com/spreadsheets/d/1hbQAIyD
UigIsEajcpNOXbmldgfLmEqsOE729SPTVpmA/edit?usp=sharing)

### Process data with Pandas to json

#### Load csv and verify edges with pandas

import pandas as pd  
import math

df = pd.read_csv("edges.csv")  
pd_df = df[( df.package1 == "pandas" ) | ( df.package2 == "pandas" )]  
pd_df.loc[pd_df.package1 == "pandas","other_package"] = pd_df[pd_df.package1
== "pandas"].package2  
pd_df.loc[pd_df.package2 == "pandas","other_package"] = pd_df[pd_df.package2
== "pandas"].package1

df_to_org(pd_df.loc[:,["other_package", "count"]])

print "\n", len(pd_df), "total edges with pandas"

other_package | count  
---|---  
pandas | 33846  
numpy | 21813  
statsmodels | 1355  
seaborn | 1164  
zipline | 684  
11 more rows |  
  
16 total edges with pandas

#### DataFrame with nodes

nodes_df = df[df.package1 == df.package2].reset_index().loc[:, ["package1",
"count"]].copy()  
nodes_df["label"] = nodes_df.package1  
nodes_df["id"] = nodes_df.index  
nodes_df["r"] = (nodes_df["count"] / nodes_df["count"].min()).apply(math.sqrt)
+ 5  
nodes_df["count"].apply(lambda s: str(s) + " total usages\n")  
df_to_org(nodes_df)

package1 | count | label | id | r  
---|---|---|---|---  
os | 1005020 | os | 0 | 75.711381704  
sys | 784379 | sys | 1 | 67.4690570169  
django | 618941 | django | 2 | 60.4915169887  
__future__ | 445335 | __future__ | 3 | 52.0701286903  
time | 359073 | time | 4 | 47.2662138808  
3460 more rows |   |   |   |  
  
#### Create map of node name -&gt; id

id_map = nodes_df.reset_index().set_index("package1").to_dict()["index"]

print pd.Series(id_map).sort_values()[:5]

[code]

    os            0
    sys           1
    django        2
    __future__    3
    time          4
    dtype: int64
    
[/code]

#### Create edges data frame

edges_df = df.copy()  
edges_df["source"] = edges_df.package1.apply(lambda p: id_map[p])  
edges_df["target"] = edges_df.package2.apply(lambda p: id_map[p])  
edges_df = edges_df.merge(nodes_df[["id", "count"]], left_on="source",
right_on="id", how="left")  
edges_df = edges_df.merge(nodes_df[["id", "count"]], left_on="target",
right_on="id", how="left")  
df_to_org(edges_df)

print "\ndf and edges_df should be the same length: ", len(df), len(edges_df)

package1 | package2 | strength | count_x | source | target | id_x | count_y |
id_y | count  
---|---|---|---|---|---|---|---|---|---  
os | os | 1.0 | 1005020 | 0 | 0 | 0 | 1005020 | 0 | 1005020  
sys | sys | 1.0 | 784379 | 1 | 1 | 1 | 784379 | 1 | 784379  
django | django | 1.0 | 618941 | 2 | 2 | 2 | 618941 | 2 | 618941  
__future__ | __future__ | 1.0 | 445335 | 3 | 3 | 3 | 445335 | 3 | 445335  
os | sys | 0.501429793505 | 393311 | 0 | 1 | 0 | 1005020 | 1 | 784379  
11117 more rows |   |   |   |   |   |   |   |   |  
  
df and edges_df should be the same length: 11122 11122

#### Add reversed edge

edges_rev_df = edges_df.copy()  
edges_rev_df.loc[:,["source", "target"]] = edges_rev_df.loc[:,["target",
"source"]].values  
edges_df = edges_df.append(edges_rev_df)  
df_to_org(edges_df)

package1 | package2 | strength | count_x | source | target | id_x | count_y |
id_y | count  
---|---|---|---|---|---|---|---|---|---  
os | os | 1.0 | 1005020 | 0 | 0 | 0 | 1005020 | 0 | 1005020  
sys | sys | 1.0 | 784379 | 1 | 1 | 1 | 784379 | 1 | 784379  
django | django | 1.0 | 618941 | 2 | 2 | 2 | 618941 | 2 | 618941  
__future__ | __future__ | 1.0 | 445335 | 3 | 3 | 3 | 445335 | 3 | 445335  
os | sys | 0.501429793505 | 393311 | 0 | 1 | 0 | 1005020 | 1 | 784379  
22239 more rows |   |   |   |   |   |   |   |   |  
  
#### Truncate edges DataFrame

edges_df = edges_df[["source", "target", "strength"]]  
df_to_org(edges_df)

source | target | strength  
---|---|---  
0.0 | 0.0 | 1.0  
1.0 | 1.0 | 1.0  
2.0 | 2.0 | 1.0  
3.0 | 3.0 | 1.0  
0.0 | 1.0 | 0.501429793505  
22239 more rows |   |  
  
#### After running simulation in the browser, get saved positions

The whole simulation takes a minute to stabilize. I could just download an
image, but there are extra features like pressing the node opens pypi.

Download all positions after the simulation from the javascript console:

[code]

    var positions = nodes.map(function bar (n) { return [n.id, n.x, n.y]; })
    JSON.stringify()
    
[/code]

Join the positions x and y with edges dataframe, so they will get picked up by
the d3.

pos_df = pd.read_json("fixed-positions.json")  
pos_df.columns = ["id", "x", "y"]  
nodes_df = nodes_df.merge(pos_df, on="id")

#### Truncate nodes DataFrame

# c will be collision strength. Prevent labels from overlaping.  
nodes_df["c"] = pd.DataFrame([nodes_df.label.str.len() * 1.8,
nodes_df.r]).max() + 5  
nodes_df = nodes_df[["id", "r", "label", "c", "x", "y"]]  
df_to_org(nodes_df)

id | r | label | c | x | y  
---|---|---|---|---|---  
0 | 75.711381704 | os | 80.711381704 | 158.70817237 | 396.074393369  
1 | 67.4690570169 | sys | 72.4690570169 | 362.371142521 | -292.138913114  
2 | 60.4915169887 | django | 65.4915169887 | 526.471326062 | 1607.83507287  
3 | 52.0701286903 | __future__ | 57.0701286903 | 1354.91212894 | 680.325432179  
4 | 47.2662138808 | time | 52.2662138808 | 419.407448663 | 439.872927665  
3460 more rows |   |   |   |   |  
  
#### Save files to json

# Truncate columns  
with open("graph.js", "w") as f:  
f.write("var nodes = {}\n\n".format(nodes_df.to_dict(orient="records")))  
f.write("var nodeIds = {}\n".format(id_map))  
f.write("var links = {}\n\n".format(edges_df.to_dict(orient="records")))

### Draw a graph using the new d3 velocity verlet integration algorithm

The physical simulation Simulation uses the new [velocity verlet integration
force graph in d3 v
4.0.](https://github.com/d3/d3/blob/master/API.md#forces-d3-force) Simulation
takes about one minute to stabilize, so for viewing purposes I hard-coded the
position of node after running simulation on my machine.

The core component of the simulation is:

[code]

    var simulation = d3.forceSimulation(nodes)
        .force("charge", d3.forceManyBody().strength(-400))
        .force("link", d3.forceLink(links).distance(30).strength(function (d) {
            return d.strength * d.strength;
        }))
        .force("collide", d3.forceCollide().radius(function(d) {
            return d.c;
        }).strength(5))
        .force("x", d3.forceX().strength(0.1))
        .force("y", d3.forceY().strength(0.1))
        .on("tick", ticked);
    
[/code]

To re-run the simulation you can:

  * Remove fixed positions added in one of pandas processing steps.
  * Uncomment the "forces" in the [javascript file.](https://github.com/kozikow/kozikow-blog/blob/master/clustering/index2.js#L2)

#### Simulation parameters

I have been tweaking simulation parameters for a while. Very dense "center" of
the graph is in conflict with clusters on the edge of the graph.

As you may see in the current graph, nodes in the center sometimes overlap,
while distance between nodes on the edge of a graph is big.

I got as much as I could from the collision parameter and increasing it
further wasn't helpful. Potentially I could increase gravity towards the
center, but then some of the valuable "clusters" from edges of the graph got
lumped into the big "kernel" in the center.

Plotting some big clusters separately worked well to solve this problem.

  * Attraction forces 

    * Weight of edge between packages A and B: ![\(\\frac{ |A \\cap B|}{min\(|A|, |B|\)}\)^2](https://s0.wp.com/latex.php?latex=%28%5Cfrac%7B+%7CA+%5Ccap+B%7C%7D%7Bmin%28%7CA%7C%2C+%7CB%7C%29%7D%29%5E2&bg=%23ffffff&fg=%23000000&s=0), with distance 30
    * Gravity towards center: 0.1

  * Repulsion forces 

    * Repulsion between nodes: -400
    * Strength of nodes collision: 5

## Other posts

You may be interested in my other posts analyzing github data:

  * [Top pandas, numpy and scipy functions used in github repos](https://kozikow.com/2016/07/01/top-pandas-functions-used-in-github-repos/)
  * [More advanced github code search](https://kozikow.com/2016/06/05/more-advanced-github-code-search/)
  * [Top angular directives on github, including custom directives](https://kozikow.com/2016/07/01/top-angular-directives-on-github/)
  * [Top emacs packages used in github repos](https://kozikow.wordpress.com/2016/06/29/top-emacs-packages-used-in-github-repos/)

[](https://wordpress.com/about-these-ads/)

### Share this:

  * [Twitter](https://kozikow.com/2016/07/10/visualizing-relationships-between-python-packages-2/?share=twitter "Click to share on Twitter" )
  * [Facebook](https://kozikow.com/2016/07/10/visualizing-relationships-between-python-packages-2/?share=facebook "Share on Facebook" )
  * [Google](https://kozikow.com/2016/07/10/visualizing-relationships-between-python-packages-2/?share=google-plus-1 "Click to share on Google+" )
  * 

### Like this:

Like Loading...

[featured](https://kozikow.com/tag/featured/)

## Post navigation

[Previous Top pandas, numpy and scipy functions and modules used in github
repos](https://kozikow.com/2016/07/01/top-pandas-functions-used-in-github-
repos/)

![](https://0.gravatar.com/avatar/f573c35b7f2c673e63286774d7d67f34?s=111&d=ide
nticon&r=G)

### About kozikow

I am working with data for quantitative hedge fund, GSA capital. I previously
analysed Google Search logs. I am primarily blogging about Data science, Emacs
and programming contests like TopCoder.

##  3 Comments

  1. ![](https://i2.wp.com/pbs.twimg.com/profile_images/378800000407493871/d60c34a9639e865fb61a7756644ab43a_normal.jpeg?resize=111%2C111) **[Felipe Hoffa (@felipehoffa)](http://twitter.com/felipehoffa)** says:

[ July 10, 2016 at 5:25 pm  ](https://kozikow.com/2016/07/10/visualizing-
relationships-between-python-packages-2/#comment-117)

Nice!

You might be also interested to know that PyPI download stats are also
available in BigQuery:

<https://mail.python.org/pipermail/distutils-sig/2016-May/028>…

(project recently unveiled by Donald Stufft)

I also added your article to the compilation at <https://medium.com/@hoffa
/github-on-bigquery-analyze-all-the>…, thanks for your prolific series of
analysis!

[Reply](https://kozikow.com/2016/07/10/visualizing-relationships-between-
python-packages-2/?replytocom=117#respond)

    1. ![](https://0.gravatar.com/avatar/f573c35b7f2c673e63286774d7d67f34?s=111&d=identicon&r=G) **[kozikow](https://kozikow.wordpress.com)** says:

[ July 11, 2016 at 12:01 am  ](https://kozikow.com/2016/07/10/visualizing-
relationships-between-python-packages-2/#comment-118)

It could be a good idea to join usages on github with usages on pypi. For
example, to see which packages are more used in proprietary vs open source
context.

I suspect that usages on pypi are more representative, so I could create a
visualization with node weight based on pypi, but edge weight based on github.

[Reply](https://kozikow.com/2016/07/10/visualizing-relationships-between-
python-packages-2/?replytocom=118#respond)

  2. ![](https://2.gravatar.com/avatar/2b1a97fe060ca1d62e8851ed0ee25b27?s=111&d=identicon&r=G) **[abhijeetarora](http://abhijeetarora.wordpress.com)** says:

[ July 11, 2016 at 8:56 am  ](https://kozikow.com/2016/07/10/visualizing-
relationships-between-python-packages-2/#comment-126)

Superb! Robert.

Thanks for the detailed analysis, and telling about the alternative of current
network chart.  
I liked how you've explained this complex code in a really simple description,
which makes it easy to understand.

Cheers.. ,\m/

All the best, for what you seek!

[Reply](https://kozikow.com/2016/07/10/visualizing-relationships-between-
python-packages-2/?replytocom=126#respond)

### Leave a Reply [Cancel reply](https://kozikow.com/2016/07/10/visualizing-
relationships-between-python-packages-2/#respond)

Enter your comment here...

Fill in your details below or click an icon to log in:

  *   *   *   *   * 

[ ![Gravatar](https://1.gravatar.com/avatar/ad516503a11cd5ca435acc9bb6523536?s
=25&d=identicon&forcedefault=y&r=G) ](https://gravatar.com/site/signup/)

Email (required) (Address never made public)

Name (required)

Website

![WordPress.com Logo](https://1.gravatar.com/avatar/ad516503a11cd5ca435acc9bb6
523536?s=25&d=identicon&forcedefault=y&r=G)

**** You are commenting using your WordPress.com account. ( [Log Out](javascript:HighlanderComments.doExternalLogout\( 'wordpress' \);) / Change )

![Twitter picture](https://1.gravatar.com/avatar/ad516503a11cd5ca435acc9bb6523
536?s=25&d=identicon&forcedefault=y&r=G)

**** You are commenting using your Twitter account. ( [Log Out](javascript:HighlanderComments.doExternalLogout\( 'twitter' \);) / Change )

![Facebook photo](https://1.gravatar.com/avatar/ad516503a11cd5ca435acc9bb65235
36?s=25&d=identicon&forcedefault=y&r=G)

**** You are commenting using your Facebook account. ( [Log Out](javascript:HighlanderComments.doExternalLogout\( 'facebook' \);) / Change )

![Google+ photo](https://1.gravatar.com/avatar/ad516503a11cd5ca435acc9bb652353
6?s=25&d=identicon&forcedefault=y&r=G)

**** You are commenting using your Google+ account. ( [Log Out](javascript:HighlanderComments.doExternalLogout\( 'googleplus' \);) / Change )

[Cancel](javascript:HighlanderComments.cancelExternalWindow\(\);)

Connecting to %s

Notify me of new comments via email.

Search for:

## Recent Posts

  * [Visualizing relationships between python packages](https://kozikow.com/2016/07/10/visualizing-relationships-between-python-packages-2/)
  * [Top pandas, numpy and scipy functions and modules used in github repos](https://kozikow.com/2016/07/01/top-pandas-functions-used-in-github-repos/)
  * [Top angular directives on github, including custom directives](https://kozikow.com/2016/07/01/top-angular-directives-on-github/)
  * [Top emacs packages used in github repos](https://kozikow.com/2016/06/29/top-emacs-packages-used-in-github-repos/)
  * [Smartparens is super awesome, especially with evil mode](https://kozikow.com/2016/06/18/smartparens-emacs-package-is-super-awesome/)

## Recent Comments

![](https://2.gravatar.com/avatar/205ca3f254857f62b18756beb0a07bd8?s=48&d=iden
ticon&r=G)| rufat on [Installing and configuring
Arc…](https://kozikow.com/2016/06/03/installing-and-configuring-arch-linux-on-
thinkpad-x1-carbon/comment-page-1/#comment-128)  
---|---  
[![](https://2.gravatar.com/avatar/2b1a97fe060ca1d62e8851ed0ee25b27?s=48&d=ide
nticon&r=G)](http://abhijeetarora.wordpress.com)|
[abhijeetarora](http://abhijeetarora.wordpress.com) on [Visualizing
relationships betw…](https://kozikow.com/2016/07/10/visualizing-relationships-
between-python-packages-2/comment-page-1/#comment-126)  
[](https://kozikow.com/2016/07/10/visualizing-relationships-between-python-
packages-2/)| [Visualizing relation…](https://kozikow.com/2016/07/10
/visualizing-relationships-between-python-packages-2/) on [Top angular
directives on gith…](https://kozikow.com/2016/07/01/top-angular-directives-on-
github/comment-page-1/#comment-122)  
[](https://kozikow.com/2016/07/10/visualizing-relationships-between-python-
packages-2/)| [Visualizing relation…](https://kozikow.com/2016/07/10
/visualizing-relationships-between-python-packages-2/) on [More advanced
github code…](https://kozikow.com/2016/06/05/more-advanced-github-code-search
/comment-page-1/#comment-121)  
[](https://kozikow.com/2016/07/10/visualizing-relationships-between-python-
packages-2/)| [Visualizing relation…](https://kozikow.com/2016/07/10
/visualizing-relationships-between-python-packages-2/) on [Top pandas, numpy
and scipy fu…](https://kozikow.com/2016/07/01/top-pandas-functions-used-in-
github-repos/comment-page-1/#comment-120)  
  
## Archives

  * [July 2016](https://kozikow.com/2016/07/)
  * [June 2016](https://kozikow.com/2016/06/)
  * [May 2016](https://kozikow.com/2016/05/)
  * [January 2016](https://kozikow.com/2016/01/)
  * [February 2014](https://kozikow.com/2014/02/)
  * [January 2014](https://kozikow.com/2014/01/)
  * [November 2013](https://kozikow.com/2013/11/)
  * [October 2013](https://kozikow.com/2013/10/)

## Categories

  * [data science](https://kozikow.com/category/data-science/)
  * [emacs](https://kozikow.com/category/emacs/)
  * [keyboard](https://kozikow.com/category/keyboard/)
  * [linux](https://kozikow.com/category/linux/)
  * [programming contests](https://kozikow.com/category/programming-contests/)
  * [Uncategorized](https://kozikow.com/category/uncategorized/)

## Meta

  * [Register](https://wordpress.com/start?ref=wplogin)
  * [Log in](https://kozikow.wordpress.com/wp-login.php)
  * [Entries RSS](https://kozikow.com/feed/)
  * [Comments RSS](https://kozikow.com/comments/feed/)
  * [WordPress.com](https://wordpress.com/ "Powered by WordPress, state-of-the-art semantic personal publishing platform." )

[Blog at WordPress.com.](https://wordpress.com/?ref=footer_blog)  
[The Rebalance Theme](https://wordpress.com/themes/rebalance/ "Learn more
about this theme" ).

[Follow](javascript:void\(0\))

### Follow "Robert Kozikowski's blog"

Get every new post delivered to your Inbox.

Join 719 other followers

[Build a website with WordPress.com](https://wordpress.com/?ref=lof)

%d bloggers like this:

![](https://sb.scorecardresearch.com/p?c1=2&c2=7518284&c3=&c4=&c5=&c6=&c15=&cv
=2.0&cj=1)

![](https://pixel.wp.com/b.gif?v=noscript)

  *[RSS]: Really Simple Syndication

