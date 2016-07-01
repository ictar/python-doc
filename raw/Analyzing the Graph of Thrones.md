原文：[Analyzing the Graph of Thrones](http://www.lyonwj.com/2016/06/26/graph-of-thrones-neo4j-social-network-analysis/)

---

![](http://www.lyonwj.com/public/img/graphs-are-coming.jpg)

几个月前，数学家[Andrew Beveridge](https://twitter.com/mathbeveridge)和Jie Shan在Math Horizon杂志上发表了[权利的游戏的网络](https://www.macalester.edu/~abeverid/thrones.html)，其中，他们分析了小说“冰雨的风暴”，火爆的“冰与火之歌”（权利的游戏电视剧的基础）的第三卷中的角色互动网络。在他们的论文中，他们详细介绍了如何通过使用文本分析和实体抽取，来发现文本中提到的角色，从而构建角色互动网络。然后，他们应用社交网络分析算法到该网络中，以找到该网络中最重要的角色，并且应用社区检测算法来找到角色集群。

分析和可视化是通过使用Gephi，这一流行的图形分析工具，来完成的。我想，尝试使用Neo4j来复制作者的结果，将会很好玩。

# 导入初始数据到Neo4j

数据可以在[作者的网站](https://www.macalester.edu/~abeverid/thrones.html)上找到，你可以在[这里](https://www.macalester.edu/~abeverid/data/stormofswords.csv)下载。它看起来像这样：

```python

    Source,Target,Weight
    Aemon,Grenn,5
    Aemon,Samwell,31
    Aerys,Jaime,18
    ...
    
```

这里，我们拥有整个文本中角色的邻接表和他们的互动次数。我们将使用一个简单的数据模型`(:Character {name})-[:INTERACTS {weight}]->(:Character {name})`。带有标签的节点`Character`表示文本中的角色，单个关系类型`INTERACTS`从该角色连接到另一个文本中交互的角色。我们将把角色名作为node属性`name`存储，把两个角色之间的交互数作为relationships属性`weight`存储。

首先，我们必须创建一个约束来断言我们架构的完整性：

```python

    CREATE CONSTRAINT ON (c:Character) ASSERT c.name IS UNIQUE;
```

一旦创建了约束（该语句也将构建一个索引，它将提高通过角色名查找的性能），我们可以使用Cypher的`LOAD CSV`语句来导入数据：

```python

    LOAD CSV WITH HEADERS FROM "https://www.macalester.edu/~abeverid/data/stormofswords.csv" AS row
    MERGE (src:Character {name: row.Source})
    MERGE (tgt:Character {name: row.Target})
    MERGE (src)-[r:INTERACTS]->(tgt)
    ON CREATE SET r.weight = toInt(row.Weight)
```

我们有一个非常简单的数据模型：

```python

    CALL apoc.meta.graph()
```

![](http://www.lyonwj.com/public/img/got-datamodel.png) 

**权利的游戏图的数据模型。角色节点通过`INTERACTS`关系连接。 relationship.**

我们可以看到整个图形，但这并没有给我们关于最重要人物或他们如何交互的许多信息：


```python

    MATCH p=(:Character)-[:INTERACTS]-(:Character)
    RETURN p
```

![](http://www.lyonwj.com/public/img/got-graph-full.png)

# 分析网络

我们将使用Cypher，这一图形查询语言来探索权利的游戏的图，在此过程中应用来自于网络分析的一些工具。这里探索的许多思路在优秀的[网络，人群和市场：思考高度连接的世界](https://www.cs.cornell.edu/home/kleinber/networks-book/)中进行了详细的解释。

## 角色数

让我们先从简单的部分入手。在我们的图中，出现了多少个角色？

```python

    MATCH (c:Character) RETURN count(c)
```

```python

                                      ╒════════╕
                                      │count(c)│
                                      ╞════════╡
                                      │107     │
                                      └────────┘
    
```

## 概要统计

每个角色交互的其他角色数的概要统计：

```python

    MATCH (c:Character)-[:INTERACTS]->()
    WITH c, count(*) AS num
    RETURN min(num) AS min, max(num) AS max, avg(num) AS avg_characters, stdev(num) AS stdev
```

```python

                    ╒═══╤═══╤═════════════════╤═════════════════╕
                    │min│max│avg_characters   │stdev            │
                    ╞═══╪═══╪═════════════════╪═════════════════╡
                    │1  │24 │4.957746478873241│6.227672391875085│
                    └───┴───┴─────────────────┴─────────────────┘
    
```

## 网络直径

一个网络的直径（或者测地线）被定义为网络中的最长最短路径。

```python

    // Find maximum diameter of network
    // maximum shortest path between two nodes
    MATCH (a:Character), (b:Character) WHERE id(a) > id(b)
    MATCH p=shortestPath((a)-[:INTERACTS*]-(b))
    RETURN length(p) AS len, extract(x IN nodes(p) | x.name) AS path
    ORDER BY len DESC LIMIT 4
```

```python

          ╒═══╤════════════════════════════════════════════════════════════╕
          │len│path                                                        │
          ╞═══╪════════════════════════════════════════════════════════════╡
          │6  │[Illyrio, Belwas, Daenerys, Robert, Tywin, Oberyn, Amory]   │
          ├───┼────────────────────────────────────────────────────────────┤
          │6  │[Illyrio, Belwas, Daenerys, Robert, Sansa, Bran, Jojen]     │
          ├───┼────────────────────────────────────────────────────────────┤
          │6  │[Illyrio, Belwas, Daenerys, Robert, Stannis, Davos, Shireen]│
          ├───┼────────────────────────────────────────────────────────────┤
          │6  │[Illyrio, Belwas, Daenerys, Robert, Sansa, Bran, Luwin]     │
          └───┴────────────────────────────────────────────────────────────┘
    
```

我们可以看到，在网络中，有许多长度为6的路径。

## 最短路径

我们可以使用Cypher中的`shortestPath`函数来查找图中任意两个角色的最短路径。让我们找找从Catelyn Stark到Kahl Drogo的最短路径：

```python

    // Shortest path from Catelyn Stark to Khal Drogo
    MATCH (catelyn:Character {name: "Catelyn"}), (drogo:Character {name: "Drogo"})
    MATCH p=shortestPath((catelyn)-[INTERACTS*]-(drogo))
    RETURN p
```

![](http://www.lyonwj.com/public/img/shortestpath-got.png)

## 所有最短路径

可能还有其他具有相同长度的连接Catelyn和Drogo的路径。我们可以使用`allShortestPaths` Cypher函数来查找：

```python

    // All shortest paths from Catelyn Stark to Khal Drogo
    MATCH (catelyn:Character {name: "Catelyn"}), (drogo:Character {name: "Drogo"})
    MATCH p=allShortestPaths((catelyn)-[INTERACTS*]-(drogo))
    RETURN p
```

![](http://www.lyonwj.com/public/img/allshortestpaths-got.png)

# 关键节点

如果节点位于网络中的其它两个节点之间的所有最短路径之中，那么该节点被认为是关键的。我们可以找到网络中的所有关键节点：

```python

    // Find all pivotal nodes in network
    MATCH (a:Character), (b:Character)
    MATCH p=allShortestPaths((a)-[:INTERACTS*]-(b)) WITH collect(p) AS paths, a, b
    MATCH (c:Character) WHERE all(x IN paths WHERE c IN nodes(x)) AND NOT c IN [a,b]
    RETURN a.name, b.name, c.name AS PivotalNode SKIP 490 LIMIT 10
```

```python

                              ╒═══════╤═══════╤═══════════╕
                              │a.name │b.name │PivotalNode│
                              ╞═══════╪═══════╪═══════════╡
                              │Aegon  │Thoros │Daenerys   │
                              ├───────┼───────┼───────────┤
                              │Aegon  │Thoros │Robert     │
                              ├───────┼───────┼───────────┤
                              │Drogo  │Ramsay │Robb       │
                              ├───────┼───────┼───────────┤
                              │Styr   │Daario │Daenerys   │
                              ├───────┼───────┼───────────┤
                              │Styr   │Daario │Jon        │
                              ├───────┼───────┼───────────┤
                              │Styr   │Daario │Robert     │
                              ├───────┼───────┼───────────┤
                              │Qhorin │Podrick│Jon        │
                              ├───────┼───────┼───────────┤
                              │Qhorin │Podrick│Sansa      │
                              ├───────┼───────┼───────────┤
                              │Orell  │Theon  │Jon        │
                              ├───────┼───────┼───────────┤
                              │Illyrio│Bronn  │Belwas     │
                              └───────┴───────┴───────────┘
    
```

如果我们翻阅了有趣结果的结果表，那么可以看到，对于Drogo和Ramsay来说，Robb是一个关键节点。这意味着，所有连接Drogo和Ramsay的最短路径都经过Robb。我们可以通过看看连接Drogo和Ramsay的所有最短路径，可视化的验证这点：

```python

    MATCH (a:Character {name: "Drogo"}), (b:Character {name: "Ramsay"})
    MATCH p=allShortestPaths((a)-[:INTERACTS*]-(b))
    RETURN p
```

![](http://www.lyonwj.com/public/img/pivotal-path.png)

# 中心性度量

[中心性度量](https://en.wikipedia.org/wiki/Centrality)为我们提供了网络中重要性的相对措施。有许多不同的中心性度量，而每种度量不同类型的“重要性”。

## 度中心性(Degree Centrality)

度中心性仅是一个节点在网络中的连接数。在权利的游戏的图的上下文中，一个角色的度中心性是该角色交互的其他角色数。我们可以像这样使用Cypher来计算度中心性：

```python

    MATCH (c:Character)
    RETURN c.name AS character, size( (c)-[:INTERACTS]-() ) AS degree ORDER BY degree DESC
```

```python

                                  ╒═════════╤══════╕
                                  │character│degree│
                                  ╞═════════╪══════╡
                                  │Tyrion   │36    │
                                  ├─────────┼──────┤
                                  │Jon      │26    │
                                  ├─────────┼──────┤
                                  │Sansa    │26    │
                                  ├─────────┼──────┤
                                  │Robb     │25    │
                                  ├─────────┼──────┤
                                  │Jaime    │24    │
                                  ├─────────┼──────┤
                                  │Tywin    │22    │
                                  ├─────────┼──────┤
                                  │Cersei   │20    │
                                  ├─────────┼──────┤
                                  │Arya     │19    │
                                  ├─────────┼──────┤
                                  │Joffrey  │18    │
                                  ├─────────┼──────┤
                                  │Robert   │18    │
                                  └─────────┴──────┘
    
```

而且我们看到，Tyrion与网络中的角色具有最多的互动。鉴于他的心计，我觉得这是有道理的。

## 加权度中心性

我们存储一对角色之间的交互数，作为`INTERACTS关系上的`weight`属性。对角色的所有`INTERACTS关系上的该权重求和，我们可以获得他们的_加权度中心性_，或者他们参与的交互总数。同样，我们可以使用Cypher来为所有的角色计算该度量：

```python

    MATCH (c:Character)-[r:INTERACTS]-()
    RETURN c.name AS character, sum(r.weight) AS weightedDegree ORDER BY weightedDegree DESC
```

```python

                            ╒═════════╤══════════════╕
                            │character│weightedDegree│
                            ╞═════════╪══════════════╡
                            │Tyrion   │551           │
                            ├─────────┼──────────────┤
                            │Jon      │442           │
                            ├─────────┼──────────────┤
                            │Sansa    │383           │
                            ├─────────┼──────────────┤
                            │Jaime    │372           │
                            ├─────────┼──────────────┤
                            │Bran     │344           │
                            ├─────────┼──────────────┤
                            │Robb     │342           │
                            ├─────────┼──────────────┤
                            │Samwell  │282           │
                            ├─────────┼──────────────┤
                            │Arya     │269           │
                            ├─────────┼──────────────┤
                            │Joffrey  │255           │
                            ├─────────┼──────────────┤
                            │Daenerys │232           │
                            └─────────┴──────────────┘
    
```

## 中介中心性(Betweenness Centrality)

一个网络中的一个节点的[中介中心性(Betweenness Centrality)](https://en.wikipedia.org/wiki/Betweenness_centrality) 是，the number of shortest paths between two other members in the
network on which a given node appears. Betweenness centality is an important
metric because it can be used to identify “brokers of information” in the
network or nodes that connect disparate clusters.

![](http://www.lyonwj.com/public/img/betweenness-centrality.png) **The red
nodes have a high betweenness centrality and are connectors of clusters.
[Image credit](https://www.linkedin.com/pulse/wtf-do-you-actually-know-who-
influencers-walter-pike)**

To calculate betweenness centrality we will use the new [Awesome Procedures
for Neo4j 3.x or apoc library](https://github.com/neo4j-contrib/neo4j-apoc-
procedures). Once we’ve installed apoc we can call any of the 170+ procedures
from Cypher:

```python

    MATCH (c:Character)
    WITH collect(c) AS characters
    CALL apoc.algo.betweenness(['INTERACTS'], characters, 'BOTH') YIELD node, score
    SET node.betweenness = score
    RETURN node.name AS name, score ORDER BY score DESC
```

```python

                            ╒════════╤══════════════════╕
                            │name    │score             │
                            ╞════════╪══════════════════╡
                            │Jon     │1279.7533534055322│
                            ├────────┼──────────────────┤
                            │Robert  │1165.6025171231624│
                            ├────────┼──────────────────┤
                            │Tyrion  │1101.3849724234349│
                            ├────────┼──────────────────┤
                            │Daenerys│874.8372110508583 │
                            ├────────┼──────────────────┤
                            │Robb    │706.5572832464792 │
                            ├────────┼──────────────────┤
                            │Sansa   │705.1985623519137 │
                            ├────────┼──────────────────┤
                            │Stannis │571.5247305125714 │
                            ├────────┼──────────────────┤
                            │Jaime   │556.1852522889822 │
                            ├────────┼──────────────────┤
                            │Arya    │443.01358430043337│
                            ├────────┼──────────────────┤
                            │Tywin   │364.7212195528086 │
                            └────────┴──────────────────┘
    
```

## 接近中心性(Closeness centrality)

[接近中心性(Closeness centrality)](https://en.wikipedia.org/wiki/Centrality#Closeness_centrality) is
the inverse of the average distance to all other characters in the network.
Nodes with high closeness centality are often highly connected within clusters
in the graph, but not necessarily highly connected outside of the cluster.

![](http://www.lyonwj.com/public/img/closeness-centrality.png) 

**Nodes with
high closeness centrality are connected to many other nodes in a network.
[Image credit](https://www.linkedin.com/pulse/wtf-do-you-actually-know-who-influencers-walter-pike)**

```python

    MATCH (c:Character)
    WITH collect(c) AS characters
    CALL apoc.algo.closeness(['INTERACTS'], characters, 'BOTH') YIELD node, score
    RETURN node.name AS name, score ORDER BY score DESC
```

```python

                            ╒═══════╤═════════════════════╕
                            │name   │score                │
                            ╞═══════╪═════════════════════╡
                            │Tyrion │0.004830917874396135 │
                            ├───────┼─────────────────────┤
                            │Sansa  │0.004807692307692308 │
                            ├───────┼─────────────────────┤
                            │Robert │0.0047169811320754715│
                            ├───────┼─────────────────────┤
                            │Robb   │0.004608294930875576 │
                            ├───────┼─────────────────────┤
                            │Arya   │0.0045871559633027525│
                            ├───────┼─────────────────────┤
                            │Jaime  │0.004524886877828055 │
                            ├───────┼─────────────────────┤
                            │Stannis│0.004524886877828055 │
                            ├───────┼─────────────────────┤
                            │Jon    │0.004524886877828055 │
                            ├───────┼─────────────────────┤
                            │Tywin  │0.004424778761061947 │
                            ├───────┼─────────────────────┤
                            │Eddard │0.004347826086956522 │
                            └───────┴─────────────────────┘
    
```

# 使用python-igraph

One of the things I love about Neo4j is how well it works with other tools,
things like R and Python data science tools. We could continue to use apoc to
run [PageRank](https://neo4j-contrib.github.io/neo4j-apoc-procedures/#_pagerank_algorithm) and [community detection](https://neo4j-contrib.github.io/neo4j-apoc-procedures/#_graph_algorithms) algorithms, but
let’s switch to using [python-igraph](http://igraph.org/python/) to calculate
some analyses. Python-igraph is a port of the R igraph graph analytics
library. To install, just use `pip install python-igraph`.

### 从Neo4j构建一个igraph实例

To use igraph on our graph of thrones data, the first thing we need to do is
pull data out of Neo4j and build an igraph instnace in Python. We can do this
using [py2neo](http://py2neo.org/), one of the Python drivers for Neo4j. We
can pass the results object of a Py2neo query directly into the `TupleList`
constructor of igraph to create an igraph instance:

```python

    from py2neo import Graph
    from igraph import Graph as IGraph
    graph = Graph()
    
    query = '''
    MATCH (c1:Character)-[r:INTERACTS]->(c2:Character)
    RETURN c1.name, c2.name, r.weight AS weight
    '''
    
    ig = IGraph.TupleList(graph.run(query), weights=True)
```

We now have an igraph object that we can use to run any of the graph
algorithms implemented in igraph.

## PageRank

The first algorithm we’ll use in igraph is
[PageRank](https://en.wikipedia.org/wiki/PageRank). PageRank is an algorithm
originally used by Google to rank the importance of web pages and is a type of
[eigenvector centrality](https://en.wikipedia.org/wiki/Centrality#Eigenvector_centrality).

![](http://www.lyonwj.com/public/img/page-rank.png) **PageRank: The size of
each node is proportional to the number and size of the other nodes pointing
to it in the network. Image credit
[wikipedia](https://commons.wikimedia.org/w/index.php?curid=2776582) CC-BY**

Here’s we’ll run Pagerank on our igraph instance, then write the results back
to Neo4j, creating a `pagerank` property on our Character nodes to store the
value we just calculated in igraph:

```python

    pg = ig.pagerank()
    pgvs = []
    for p in zip(ig.vs, pg):
        print(p)
        pgvs.append({"name": p[0]["name"], "pg": p[1]})
    pgvs
    
    write_clusters_query = '''
    UNWIND {nodes} AS n
    MATCH (c:Character) WHERE c.name = n.name
    SET c.pagerank = n.pg
    '''
    
    graph.run(write_clusters_query, nodes=pgvs)
```

We can now query our graph in Neo4j to find the nodes with the highest
PageRank score:

```python

    MATCH (n:Character)
    RETURN n.name AS name, n.pagerank AS pagerank ORDER BY pagerank DESC LIMIT 10
```

```python

                            ╒════════╤════════════════════╕
                            │name    │pagerank            │
                            ╞════════╪════════════════════╡
                            │Tyrion  │0.042884981999963316│
                            ├────────┼────────────────────┤
                            │Jon     │0.03582869669163558 │
                            ├────────┼────────────────────┤
                            │Robb    │0.03017114665594764 │
                            ├────────┼────────────────────┤
                            │Sansa   │0.030009716660108578│
                            ├────────┼────────────────────┤
                            │Daenerys│0.02881425425830273 │
                            ├────────┼────────────────────┤
                            │Jaime   │0.028727587587471206│
                            ├────────┼────────────────────┤
                            │Tywin   │0.02570016262642541 │
                            ├────────┼────────────────────┤
                            │Robert  │0.022292016521362864│
                            ├────────┼────────────────────┤
                            │Cersei  │0.022287327589773507│
                            ├────────┼────────────────────┤
                            │Arya    │0.022050209663844467│
                            └────────┴────────────────────┘
    
```

### Community detection

![](http://www.lyonwj.com/public/img/community-1.png) **[Image credit](http://digitalinterface.blogspot.com/2013/05/community-detection-in-graphs.html)**

Community detection algorithms are used to find clusters in the graph. We’ll
use the [walktrap method](http://arxiv.org/abs/physics/0512106) as implemented
in igraph to find communities of characters that frequently interact within
the community, but not much interaction occurs outside of the community.

We’ll run the walktrap community detection algorithm and then write the newly
discovered community numbers back to Neo4j, with the community each Character
belongs to represented by an integer:

```python

    clusters = IGraph.community_walktrap(ig, weights="weight").as_clustering()
    
    nodes = [{"name": node["name"]} for node in ig.vs]
    for node in nodes:
        idx = ig.vs.find(name=node["name"]).index
        node["community"] = clusters.membership[idx]
    
    write_clusters_query = '''
    UNWIND {nodes} AS n
    MATCH (c:Character) WHERE c.name = n.name
    SET c.community = toInt(n.community)
    '''
    
    graph.run(write_clusters_query, nodes=nodes)
```

We can then query Neo4j to see how many communities (or clusters) we ended up
with and the members of each community:

```python

    MATCH (c:Character)
    WITH c.community AS cluster, collect(c.name) AS  members
    RETURN cluster, members ORDER BY cluster ASC
```

```python

      ╒═══════╤═══════════════════════════════════════════════════════════════════════════╕
      │cluster│members                                                                    │
      ╞═══════╪═══════════════════════════════════════════════════════════════════════════╡
      │0      │[Aemon, Alliser, Craster, Eddison, Gilly, Janos, Jon, Mance, Rattleshirt, S│
      │       │amwell, Val, Ygritte, Grenn, Karl, Bowen, Dalla, Orell, Qhorin, Styr]      │
      ├───────┼───────────────────────────────────────────────────────────────────────────┤
      │1      │[Aerys, Amory, Balon, Brienne, Bronn, Cersei, Gregor, Jaime, Joffrey, Jon A│
      │       │rryn, Kevan, Loras, Lysa, Meryn, Myrcella, Oberyn, Podrick, Renly, Robert, │
      │       │Robert Arryn, Sansa, Shae, Tommen, Tyrion, Tywin, Varys, Walton, Petyr, Eli│
      │       │a, Ilyn, Pycelle, Qyburn, Margaery, Olenna, Marillion, Ellaria, Mace, Chata│
      │       │ya, Doran]                                                                 │
      ├───────┼───────────────────────────────────────────────────────────────────────────┤
      │2      │[Arya, Beric, Eddard, Gendry, Sandor, Anguy, Thoros]                       │
      ├───────┼───────────────────────────────────────────────────────────────────────────┤
      │3      │[Brynden, Catelyn, Edmure, Hoster, Lothar, Rickard, Robb, Roose, Walder, Je│
      │       │yne, Roslin, Ramsay]                                                       │
      ├───────┼───────────────────────────────────────────────────────────────────────────┤
      │4      │[Bran, Hodor, Jojen, Luwin, Meera, Rickon, Nan, Theon]                     │
      ├───────┼───────────────────────────────────────────────────────────────────────────┤
      │5      │[Belwas, Daario, Daenerys, Irri, Jorah, Missandei, Rhaegar, Viserys, Barris│
      │       │tan, Illyrio, Drogo, Aegon, Kraznys, Rakharo, Worm]                        │
      ├───────┼───────────────────────────────────────────────────────────────────────────┤
      │6      │[Davos, Melisandre, Shireen, Stannis, Cressen, Salladhor]                  │
      ├───────┼───────────────────────────────────────────────────────────────────────────┤
      │7      │[Lancel]                                                                   │
      └───────┴───────────────────────────────────────────────────────────────────────────┘
    
```

##  可视化 - 把它们放在一起

![](http://www.lyonwj.com/public/img/graph-of-thrones.png) **The graph of
thrones. Node size is proportional to betweenness centrality, colors indicate
the cluster of the node as determined by the walktrap method, and the edge
thickness is proprtional to the number of interactions between two
characters.**

Now that we’ve computed these analytics for the graph, let’s create a
visualization that allows us to make sense of the data.

The Neo4j Browser is great for visualizing the results of Cypher queries, but
if we want to embed our graph visualizations in another application we might
want to use one of the many great Javascript libraries for graph
visualizations. [Vis.js](http://visjs.org/) is one such library that allows
for building interactive graph visualizations. To make the process of pulling
data from Neo4j and building graph visualizations with vis.js I’ve been
working on [neovis.js](https://github.com/johnymontana/neovis.js), which
combines vis.js and the [Neo4j official Javascript driver](http://www.lyonwj.com/2016/06/26/graph-of-thrones-neo4j-social-network-analysis/). Neovis.js provides a simple API that allows for
configuring the visualization. For example, the following Javascript is all
that is necessary to produce the visualization above:

```python

    var config = {
      container_id: "viz",
      server_url: "localhost",
      labels: {
        "Character": "name"
      },
      label_size: {
        "Character": "betweenness"
      },
      relationships: {
        "INTERACTS": null
      },
      relationship_thickness: {
        "INTERACTS": "weight"
      },
      cluster_labels: {
        "Character": "community"
      }
    };
    
    var viz = new NeoVis(config);
    viz.render();
```

请注意，我们已经指定：

  * 在可视化中，我们想要包含带标签`Character`的节点，使用合适的`name`作为其标题
  * Node size should be proportional to the value of their `betweenness` property
  * 在可视化中，我们想要包含`INTERACTS`关系
  * The thickness of the relationships should be proportional to the value of the `weight` property
  * The nodes should be colored according to the value of the propety `community` which identifies the clusters in the network
  * To pull the data from a Neo4j server on `localhost`
  * To display the visualization in a DOM element with the id `viz`

Neovis.js需要从Neo4j拉取数据，并且基于我们的最低配置创建可视化。

## 资源

  * A. Beveridge and J. Shan, [“Network of Thrones”](http://www.maa.org/sites/default/files/pdf/Mathhorizons/NetworkofThrones%20%281%29.pdf) Math Horizons Magazine , Vol. 23, No. 4 (2016), pp. 18-22.
  * J. Kleinberg and D. Easley, [Networks, Crowds, and Markets: Reasoning About a Highly Connected World.](https://www.cs.cornell.edu/home/kleinber/networks-book/) Cambridge University Press (2010)

所有代码[可以在Github上找到](https://github.com/johnymontana/graph-of-thrones/blob/master/network-of-thrones.ipynb)。
