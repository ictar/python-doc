原文：[How to work with large JSON datasets using Python and Pandas](https://www.dataquest.io/blog/using-json-data-in-pandas/)

---

处理大量的JSON数据集可能会很痛苦，特别是当它们太大而无法装入内存时。在这种情况下，命令行工具和Python的组合可以为探索和分析数据提供一种有效的方式。在这篇文章中，我们将看看如何利用像Pandas这样的工具来探索和绘制出Maryland州Montgomery郡的警察活动。我们开始会一起看一下JSON数据，然后过渡到勘探和分析。

当数据存储在SQL数据库时，它倾向于遵循一个看起来像一个表的刚性结构。下面是SQLite数据库中的一个例子：
```
id|code|name|area|area_land|area_water|population|population_growth|birth_rate|death_rate|migration_rate|created_at|updated_at
1|af|Afghanistan|652230|652230|0|32564342|2.32|38.57|13.89|1.51|2015-11-01 13:19:49.461734|2015-11-01 13:19:49.461734
2|al|Albania|28748|27398|1350|3029278|0.3|12.92|6.58|3.3|2015-11-01 13:19:54.431082|2015-11-01 13:19:54.431082
3|ag|Algeria|2381741|2381741|0|39542166|1.84|23.67|4.31|0.92|2015-11-01 13:19:59.961286|2015-11-01 13:19:59.961286
```

正如你所看到的，数据由行和列组成，其中每列映射到一个定义的属性，像`id`或`code`。在上面的数据集中，每一行代表一个国家，每一列代表关于这个国家的一些事实。

但随着我们所采集的数据量的增加，我们常常不知道存储它时确切的数据结构。这就是所谓的非结构化数据。一个很好的例子是一个网站上的访客事件的列表。下面是一个发送到服务器的事件列表的例子：

```json
  {'event_type': 'started-mission',
   'keen': {'created_at': '2015-06-12T23:09:03.966Z',
    'id': '557b668fd2eaaa2e7c5e916b',
    'timestamp': '2015-06-12T23:09:07.971Z'},
   'sequence': 1}

  {'event_type': 'started-screen',
   'keen': {'created_at': '2015-06-12T23:09:03.979Z',
    'id': '557b668f90e4bd26c10b6ed6',
    'timestamp': '2015-06-12T23:09:07.987Z'},
   'mission': 1,
   'sequence': 4,
   'type': 'code'}

  {'event_type': 'started-screen',
   'keen': {'created_at': '2015-06-12T23:09:22.517Z',
    'id': '557b66a246f9a7239038b1e0',
    'timestamp': '2015-06-12T23:09:24.246Z'},
   'mission': 1,
   'sequence': 3,
   'type': 'code'},
```

正如你所看到的，上面列出了三​个独立的事件。每个事件具有不同的字段，并且一些字段被嵌套到另一个字段内。这种类型的数据是非常难存储到常规的SQL数据库中的。这样的非结构化数据通常存储在一个称为[JavaScript Object Notation](http://json.org/) (JSON)的格式中。JSON是一种编码像列表和字典这样的数据结构为字符串的方式，它确保它们容易被机器所读取。即使JSON由单词Javascript开头，但是它实际上只是一种格式，并且可以通过任何语言来读取。

Python对JSON有很大的支持，它拥有[json](https://docs.python.org/3/library/json.html)库。我们既可以转换列表和字典到JSON，也可以将字符串转换为表和字典。JSON数据看起来很像Python中的一个字典，它存储了键和值。

在这篇文章中，我们将在命令行上探讨一个JSON文件，然后将其导入Python，并使用Pandas对其进行处理。

## 数据集

我们将着眼于一个包含Maryland州Montgomery郡交通违法行为信息的数据集。你也可以在[这里](https://catalog.data.gov/dataset/traffic-violations-56dda)下载数据。该数据包含有关违规发生的地点信息，车的类型，关于收到违规的人的人口统计，以及其他一些有趣的信息。我们可以用这个数据集回答相当多的问题，其中包括了几个问题：

*   什么类型的汽车是最有可能被超速拦停？
*   一天的哪些时候警察最活跃？
*   “超速陷阱”有多常见？或者在地理位置方面，交通罚款单是否相当均匀分布？
*   人们被拦下来最常见的原因是什么？

不幸的是，我们不知道JSON文件前期的结构，所以我们需要做一些探索来弄明白它。我们将使用[Jupyter Notebook](http://jupyter.org/)进行这种探索。

## 探索数据

即使JSON文件只有600MB，但是我们将把它当成更大进行处理，这样的话，我们就可以探索如何分析不适合读入的一个JSON文件。我们要做的第一件事就是看看`md_traffic.json`文件的前几行。一个JSON文件只是一个普通的文本文件，所以我们可以使用所有标准的命令行工具来与它进行交互：
```py
In [1]:%%bash
        head md_traffic.json
```
(输出)
```
{
  "meta" : {
    "view" : {
      "id" : "4mse-ku6q",
      "name" : "Traffic Violations",
      "averageRating" : 0,
      "category" : "Public Safety",
      "createdAt" : 1403103517,
      "description" : "This dataset contains traffic violation information from all electronic traffic violations issued in the County.  Any information that can be used to uniquely identify the vehicle, the vehicle owner or the officer issuing the violation will not be published.\r\n\r\nUpdate Frequency:  Daily",
      "displayType" : "table",
```

从中我们可以看到，该JSON数据是一个字典，并且格式良好。`meta`是一个顶级键，缩进两个空格。我们可以使用[grep](https://en.wikipedia.org/wiki/Grep)命令让所有的顶级键打印任何有两个前导空格的行：
```py
In [2]:%%bash

        grep -E '^ {2}"' md_traffic.json
```
(输出)
```
  "meta" : {
  "data" : [ [ 1889194, "92AD0076-5308-45D0-BDE3-6A3A55AD9A04", 1889194, 1455876689, "498050", 1455876689, "498050", null, "2016-02-18T00:00:00", "09:05:00", "MCP", "2nd district, Bethesda", "DRIVER USING HANDS TO USE HANDHELD TELEPHONE WHILEMOTOR VEHICLE IS IN MOTION", "355/TUCKERMAN LN", "-77.105925", "39.03223", "No", "No", "No", "No", "No", "No", "No", "No", "No", "No", "MD", "02 - Automobile", "2010", "JEEP", "CRISWELL", "BLUE", "Citation", "21-1124.2(d2)", "Transportation Article", "No", "WHITE", "F", "GERMANTOWN", "MD", "MD", "A - Marked Patrol", [ "{\"address\":\"\",\"city\":\"\",\"state\":\"\",\"zip\":\"\"}", "-77.105925", "39.03223", null, false ] ]
```

这向我们表明，`meta`和`data`是`md_traffic.json`数据中的顶级键。一个列表的列表似乎与`data`一同出现，而这有可能包括我们交通违法行为集中的每个记录。每一个内部的列表都是一个记录，而第一条记录出现在`grep`命令的输出中。这非常类似于当我们操作CSV文件或SQL表格时使用的那种格式化数据。下面是数据可能看起来的样子的一个截断视图：
```py
[
    [1889194, "92AD0076-5308-45D0-BDE3-6A3A55AD9A04", 1889194, 1455876689, "498050"],
    [1889194, "92AD0076-5308-45D0-BDE3-6A3A55AD9A04", 1889194, 1455876689, "498050"],
    ...
]
```

这看起来很像我们已经习惯使用的行和列。我们只是缺少告诉我们每列表示的含义的头。我们也许能够在`meta`键下找到此信息。

`meta`通常是指关于数据本身的信息。让我们深入了解`meta`，看看有什么信息都包含在那里。使用`head`命令，我们知道至少有`3`个级别的键，`meta`包含一个键`view`，而`view`又包含`id`, `name`, `averageRating`等。我们可以通过使用grep来打印出JSON文件的全键结构，打印出任何具有`2-6`前导空格的行：
```py
In [6]:%%bash

        grep -E '^ {2,6}"' md_traffic.json
```
(输出)
```
  "meta" : {
    "view" : {
      "id" : "4mse-ku6q",
      "name" : "Traffic Violations",
      "averageRating" : 0,
      "category" : "Public Safety",
      "createdAt" : 1403103517,
      "description" : "This dataset contains traffic violation information from all electronic traffic violations issued in the County.  Any information that can be used to uniquely identify the vehicle, the vehicle owner or the officer issuing the violation will not be published.\r\n\r\nUpdate Frequency:  Daily",
      "displayType" : "table",
      "downloadCount" : 2851,
      "iconUrl" : "fileId:r41tDc239M1FL75LFwXFKzFCWqr8mzMeMTYXiA24USM",
      "indexUpdatedAt" : 1455885508,
      "newBackend" : false,
      "numberOfComments" : 0,
      "oid" : 8890705,
      "publicationAppendEnabled" : false,
      "publicationDate" : 1411040702,
      "publicationGroup" : 1620779,
      "publicationStage" : "published",
      "rowClass" : "",
      "rowsUpdatedAt" : 1455876727,
      "rowsUpdatedBy" : "ajn4-zy65",
      "state" : "normal",
      "tableId" : 1722160,
      "totalTimesRated" : 0,
      "viewCount" : 6910,
      "viewLastModified" : 1434558076,
      "viewType" : "tabular",
      "columns" : [ {
      "disabledFeatureFlags" : [ "allow_comments" ],
      "grants" : [ {
      "metadata" : {
      "owner" : {
      "query" : {
      "rights" : [ "read" ],
      "sortBys" : [ {
      "tableAuthor" : {
      "tags" : [ "traffic", "stop", "violations", "electronic issued." ],
      "flags" : [ "default" ]
  "data" : [ [ 1889194, "92AD0076-5308-45D0-BDE3-6A3A55AD9A04", 1889194, 1455876689, "498050", 1455876689, "498050", null, "2016-02-18T00:00:00", "09:05:00", "MCP", "2nd district, Bethesda", "DRIVER USING HANDS TO USE HANDHELD TELEPHONE WHILEMOTOR VEHICLE IS IN MOTION", "355/TUCKERMAN LN", "-77.105925", "39.03223", "No", "No", "No", "No", "No", "No", "No", "No", "No", "No", "MD", "02 - Automobile", "2010", "JEEP", "CRISWELL", "BLUE", "Citation", "21-1124.2(d2)", "Transportation Article", "No", "WHITE", "F", "GERMANTOWN", "MD", "MD", "A - Marked Patrol", [ "{\"address\":\"\",\"city\":\"\",\"state\":\"\",\"zip\":\"\"}", "-77.105925", "39.03223", null, false ] ]
```

这展示了`md_traffic.json`中的全键结构，并告诉我们JSON文件的哪些部分是与我们相关的。在这种情况下，`columns`键看起来有趣，因为它有可能包含`data`键中的列表的列表中的列的信息。

## 提取列信息

现在，我们知道了哪个键包含关于列的信息，接着我们需要读入该信息。由于假设JSON文件无法全部读入内存，因此不能直接使用[json](https://docs.python.org/3/library/json.html)库将其读入。相反，我们将要以一种内存高效的方式迭代读入。

我们可以使用[ijson](https://pypi.python.org/pypi/ijson/)包来实现。ijson会迭代的解析JSON文件，而不是一次性将它们全部读入。这比直接读入整个文件慢，但它使得我们可以处理那些无法完全读入内存的大文件。要使用ijson，我们要指定一个想要从中提取数据的文件，然后指定一个键路径来提取：

```py
In [7]:
import ijson

filename = "md_traffic.json"
with open(filename, 'r') as f:
    objects = ijson.items(f, 'meta.view.columns.item')
    columns = list(objects)
```

在上面的代码中，我们打开了`md_traffic.json`文件，然后使用ijson中的`items`方法来从该文件中提取一个列表。我们使用`meta.view.columns`符号指定列表路径。回想一下，`meta`是一个顶级键，它包含`view`，而`view`又包含`columns`。然后，指定`meta.view.columns.item`来表明我们应该提取`meta.view.columns`列表中的每一个单独的项。`items`函数将返回一个[生成器](https://wiki.python.org/moin/Generators)，所以使用list方法来将该生成器转换成Python列表。我们可以打印出该列表中的第一个项：
```py
In [36]:
print(columns[0])
{'renderTypeName': 'meta_data', 'name': 'sid', 'fieldName': ':sid', 'position': 0, 'id': -1, 'format': {}, 'dataTypeName': 'meta_data'}
```

根据上面的输出，似乎`columns`中的每一个项都是一个包含每一个列信息的字典。要获取头部，看起来`fieldName`就是要提取的相关键。要获取列名，只需从`columns`中的每一个项提取`fieldName`即可：
```py
In [33]:
column_names = [col["fieldName"] for col in columns]
column_names
Out[33]:
[':sid',
 ':id',
 ':position',
 ':created_at',
 ':created_meta',
 ':updated_at',
 ':updated_meta',
 ':meta',
 'date_of_stop',
 'time_of_stop',
 'agency',
 'subagency',
 'description',
 'location',
 'latitude',
 'longitude',
 'accident',
 'belts',
 'personal_injury',
 'property_damage',
 'fatal',
 'commercial_license',
 'hazmat',
 'commercial_vehicle',
 'alcohol',
 'work_zone',
 'state',
 'vehicle_type',
 'year',
 'make',
 'model',
 'color',
 'violation_type',
 'charge',
 'article',
 'contributed_to_accident',
 'race',
 'gender',
 'driver_city',
 'driver_state',
 'dl_state',
 'arrest_type',
 'geolocation']
```

好极了！现在，我们具备了列名，可以接着提取数据了。

## 提取数据

你可能还记得，数据被藏在`data`键的一个列表的列表中。我们需要将该数据读取到内存中以便操纵它。幸运的是，我们可以使用刚刚提取的列名来只抓取相关的列。这将节省大量空间。如果数据集更大，那么可以迭代的处理这些行。所以读取第一个`10000000`行，进行一些处理，然后下一个`10000000`行，等等。在这种情况下，我们可以定义关心的列，然后再次使用`ijson`来迭代地处理该JSON文件：
```py
In [9]:
good_columns = [
    "date_of_stop", 
    "time_of_stop", 
    "agency", 
    "subagency",
    "description",
    "location", 
    "latitude", 
    "longitude", 
    "vehicle_type", 
    "year", 
    "make", 
    "model", 
    "color", 
    "violation_type",
    "race", 
    "gender", 
    "driver_state", 
    "driver_city", 
    "dl_state",
    "arrest_type"
]

data = []
with open(filename, 'r') as f:
    objects = ijson.items(f, 'data.item')
    for row in objects:
        selected_row = []
        for item in good_columns:
            selected_row.append(row[column_names.index(item)])
        data.append(selected_row)
        In [9]:
good_columns = [
    "date_of_stop", 
    "time_of_stop", 
    "agency", 
    "subagency",
    "description",
    "location", 
    "latitude", 
    "longitude", 
    "vehicle_type", 
    "year", 
    "make", 
    "model", 
    "color", 
    "violation_type",
    "race", 
    "gender", 
    "driver_state", 
    "driver_city", 
    "dl_state",
    "arrest_type"
]

data = []
with open(filename, 'r') as f:
    objects = ijson.items(f, 'data.item')
    for row in objects:
        selected_row = []
        for item in good_columns:
            selected_row.append(row[column_names.index(item)])
        data.append(selected_row)
```

现在，我们已经读取了数据，可以打印出`data`中第一项了：
```py
In [10]:
data[0]
Out[10]:
['2016-02-18T00:00:00',
 '09:05:00',
 'MCP',
 '2nd district, Bethesda',
 'DRIVER USING HANDS TO USE HANDHELD TELEPHONE WHILEMOTOR VEHICLE IS IN MOTION',
 '355/TUCKERMAN LN',
 '-77.105925',
 '39.03223',
 '02 - Automobile',
 '2010',
 'JEEP',
 'CRISWELL',
 'BLUE',
 'Citation',
 'WHITE',
 'F',
 'MD',
 'GERMANTOWN',
 'MD',
 'A - Marked Patrol']
```

## 将数据读到Pandas中

现在，我们有了一个作为列表的数据列表，及一个列头部列表，我们可以创建一个[Pandas](http://pandas.pydata.org/) Dataframe来分析这些数据了。如果你不熟悉Pandas，其实它就是一个数据分析库，使用了一种名为Dataframe的高效的，表格式的数据结构来展示数据。Pandas允许你将一个列表的列表转换为一个Dataframe，并且分别指定列名。
```py
In [11]:
import pandas as pd

stops = pd.DataFrame(data, columns=good_columns)
```

现在，我们有了一个存储在Dataframe的数据，可以进行一些有趣的分析了。下面是一个表格，表示了按照汽车颜色分类，有多少管制：
```py
In [12]:
stops["color"].value_counts()
Out[12]:
BLACK          161319
SILVER         150650
WHITE          122887
GRAY            86322
RED             66282
BLUE            61867
GREEN           35802
GOLD            27808
TAN             18869
BLUE, DARK      17397
MAROON          15134
BLUE, LIGHT     11600
BEIGE           10522
GREEN, DK       10354
N/A              9594
GREEN, LGT       5214
BROWN            4041
YELLOW           3215
ORANGE           2967
BRONZE           1977
PURPLE           1725
MULTICOLOR        723
CREAM             608
COPPER            269
PINK              137
CHROME             21
CAMOUFLAGE         17
dtype: int64
```

伪彩似乎是一种非常受欢迎的汽车颜色。下面是一个表格，表示什么样的警卫队创建了罚单：
```py
In [13]:
stops["arrest_type"].value_counts()
Out[13]:
A - Marked Patrol                         671165
Q - Marked Laser                           87929
B - Unmarked Patrol                        25478
S - License Plate Recognition              11452
O - Foot Patrol                             9604
L - Motorcycle                              8559
E - Marked Stationary Radar                 4854
R - Unmarked Laser                          4331
G - Marked Moving Radar (Stationary)        2164
M - Marked (Off-Duty)                       1301
I - Marked Moving Radar (Moving)             842
F - Unmarked Stationary Radar                420
C - Marked VASCAR                            323
D - Unmarked VASCAR                          210
P - Mounted Patrol                           171
N - Unmarked (Off-Duty)                      116
H - Unmarked Moving Radar (Stationary)        72
K - Aircraft Assist                           41
J - Unmarked Moving Radar (Moving)            35
dtype: int64
```

随着冲红灯摄影机和高速激光器的崛起，有趣的是巡逻车目前为止仍是罚单的主要来源。

## 转换列

现在，我们几乎准备好进行一些基于时间和地点的分析了，但是需要先将`longitude`, `latitude`, 和`date`列从字符串转换成浮点。可以使用下面的代码来转换`latitude`和`longitude`:
```py
In [40]:
import numpy as np

def parse_float(x):
    try:
        x = float(x)
    except Exception:
        x = 0
    return x
stops["longitude"] = stops["longitude"].apply(parse_float)
stops["latitude"] = stops["latitude"].apply(parse_float)
```

奇怪的是，管制当天的时间以及管制日期分别存储在两个单独的列，`time_of_stop`和`date_of_stop`中。我们要解析这两个列，并将它们转换成一个单一的日期列：
```py
In [42]:
import datetime
def parse_full_date(row):
    date = datetime.datetime.strptime(row["date_of_stop"], "%Y-%m-%dT%H:%M:%S")
    time = row["time_of_stop"].split(":")
    date = date.replace(hour=int(time[0]), minute = int(time[1]), second = int(time[2]))
    return date

stops["date"] = stops.apply(parse_full_date, axis=1)
```

现在，我们可以绘制一张表示哪些天有最多交通管制：
```py
In [50]:
import matplotlib.pyplot as plt
%matplotlib inline 

plt.hist(stops["date"].dt.weekday, bins=6)
Out[50]:
(array([ 112816.,  142048.,  133363.,  127629.,  131735.,  181476.]),
 array([ 0.,  1.,  2.,  3.,  4.,  5.,  6.]),
 <a list of 6 Patch objects>)
```
![](/blog/images/pandas_json/by_day.png)

在这张图中，星期一是`0`，而星期天是`6`。看起来，星期天有最多的管制，而星期一则最少。这也可能是数据质量问题，即出于某种原因，星期天出现了无效日期。你必须更深入挖掘`date_of_stop`列，以明确找出原因（这超出了本文的范围）。

我们也可以绘制出最常见的交通管制时间：
```py
In [51]:
plt.hist(stops["date"].dt.hour, bins=24)
Out[51]:
(array([ 44125.,  35866.,  27274.,  18048.,  11271.,   7796.,  11959.,
         29853.,  46306.,  42799.,  43775.,  37101.,  34424.,  34493.,
         36006.,  29634.,  39024.,  40988.,  32511.,  28715.,  31983.,
         43957.,  60734.,  60425.]),
 array([  0.        ,   0.95833333,   1.91666667,   2.875     ,
          3.83333333,   4.79166667,   5.75      ,   6.70833333,
          7.66666667,   8.625     ,   9.58333333,  10.54166667,
         11.5       ,  12.45833333,  13.41666667,  14.375     ,
         15.33333333,  16.29166667,  17.25      ,  18.20833333,
         19.16666667,  20.125     ,  21.08333333,  22.04166667,  23.        ]),
 <a list of 24 Patch objects>)
```
![](/blog/images/pandas_json/by_hour.png)

看起来管制最常发生在午夜，而最少发生在凌晨5点左右。这可能是有意义的，因为人们可能深夜从酒吧和晚餐开车回家，并且可能受伤。这也可能是一个数据质量问题，而为了能得到一个完整的答案，则必须通过`time_of_stop`列。

## 构造管制子集

我们已经转换了地点和日期列，可以绘制出交通管制了。由于绘图是CPU资源和内存非常密集的，所以需要先过滤`stops`中使用的行：
```py
In [43]:
last_year = stops[stops["date"] > datetime.datetime(year=2015, month=2, day=18)]
```

在上面的代码中，我们选择了所有日期在过去一年中的行。可以进一步缩小它，只选择那些在高峰时段出现的行 —— 当每个人都去上班的早晨时段：
```py
In [44]:
morning_rush = last_year[(last_year["date"].dt.weekday < 5) & (last_year["date"].dt.hour > 5) & (last_year["date"].dt.hour < 10)]
print(morning_rush.shape)
last_year.shape
(29824, 21)
Out[44]:
(234582, 21)
```

使用优秀的[folium](https://github.com/python-visualization/folium)包，现在可以可视化所有的管制在哪里发生。folium允许你轻松的通过利用[leaflet](http://leafletjs.com/)来创建Python中交互式地图。为了保持性能，我们将只可视化`morning_rush`的前`1000`行：
```py
In [45]:
import folium
from folium import plugins

stops_map = folium.Map(location=[39.0836, -77.1483], zoom_start=11)
marker_cluster = folium.MarkerCluster().add_to(stops_map)
for name, row in morning_rush.iloc[:1000].iterrows():
    folium.Marker([row["longitude"], row["latitude"]], popup=row["description"]).add_to(marker_cluster)
stops_map.create_map('stops.html')
stops_map
Out[45]:
```

（注：结果图是<iframe>格式，不知道怎么放上来。想看结果图的请到原文去看哈。多有不便，烦请原谅~~）

这表明，许多交通管制都集中在该郡的右下角。我们可以使用一个热度进一步分析：

```py
In [46]：
stops_heatmap  =  folium . Map ( location = [ 39.0836 ,  - 77.1483 ],  zoom_start = 11 ) 
stops_heatmap . add_children ( plugins . HeatMap ([[ row [ "longitude" ],  row [ "latitude" ]]  for  name ,  row  in  morning_rush . iloc [: 1000 ] . iterrows ()])) 
stops_heatmap . save ( "heatmap.html" ) 
stops_heatmap
```

（注：结果图是<iframe>格式，不知道怎么放上来。想看结果图的请到原文去看哈。多有不便，烦请原谅~~）

## 总结

在这篇文章中，我们学习了如何使用命令行工具，ijson, Pandas, matplotlib,和folium，从原始的JSON数据到功能全面的地图。如果你想了解更多关于这些工具的信息，可以看看我们在[Dataquest](https://www.dataquest.io)上的[数据分析](https://www.dataquest.io/section/data-analysis), [数据可视化](https://www.dataquest.io/section/data-visualization), 和[命令行](https://www.dataquest.io/section/the-command-line)课程。

如果你想进一步探索这个数据集，这里有一些等待回答的问题：

*   管制类型是否因地而异？
*   收入与管制次数有何关联？
*   人口密度与管制次数有何关联？
*   午夜时分最常见哪种类型的管制？
