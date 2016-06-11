原文：[Guessing mystery travel locations using Pandas, Docker and OS(R)M](http://nbviewer.jupyter.org/gist/mhermans/8c32eea0d5ec29e6b4329acbe7f0d3de)

---

七月份的时候，我们要到一个神秘的地方旅行一个周末。我们收到了一些（关于这个神秘地点的）提示：

  * 这个地点名字里有3个'd'和3个'e'。
  * 从一个给定的起始点(Heusden)向东旅行需要花2个小时的实际，这意味着它在德国境内。
  * 看看在Worms, Koblenz, Aarlen, 和Malmedy之间的路线。
  * 该地的纹章盾中的符号指向[Saint Jude](https://en.wikipedia.org/wiki/Jude_the_Apostle) 和[Saint Simon](https://en.wikipedia.org/wiki/Simon_the_Zealot)。

要对这个地点进行一个_极具_计算性的猜测，我们要

  1. 从[Open Street Map](https://www.openstreetmap.org/) (OSM)中获得所有的德国地名及其坐标。
  2. 过滤掉名字不含3个d和3个e的。
  3. 使用[开源路由机(Open Source Routing Machine)](http://project-osrm.org/) (OSRM)的web服务计算从起始点到每个匹配地点的有效驾驶时间。
  4. 选取那些低于3小时驾驶时间，以及在给定的四个城市的交界处附近的地点。
  5. 看看所得结果的纹章盾。

## 1. 从OSM获取所有的德国地名

要将OSM地名转存储到Pandas以备分析，有一个非常有用的Github仓库[OSMNames](https://github.com/geometalab/OSMNames)，它带有Docker文件和一些脚本。它使用来自[Geofabrik](http://nbviewer.jupyter.org/gist/mhermans/download.geofabrik.de/index.html)的OSM数据转储，在我们的例子中，该数据适用于[德国](http://download.geofabrik.de/europe/germany.html)。

我们克隆Github仓库并下载整个关于德国的OSM数据集：

```python

    git clone https://github.com/geometalab/OSMNames.git
    cd OSMNames
    wget --directory-prefix=./data http://download.geofabrik.de/europe/germany-latest.osm.pbf
    
    
```

然后，使用Docker加载该OSM数据集转储，以在必要时获取我们的环境，并导出德国地名。

```python

    docker-compose up -d postgres      # launches Postgres-database
    docker-compose run import-osm      # import 2.8GB pbf-file into Postgres 
    docker-compose run schema          # 
    docker-compose run export-osmnames # export location names to 500MB plain-text file
    
    
```

为了进行分析，创建一个Python虚拟环境，并使用pip安装所需库。

```python

    mkvirtualenv osmde
    pip install ipython pandas jupyter numpy requests numexpr
    
    
```

加载一个交互式IPython notebook，你可以下载并重新运行所读取的那个。

```python

    ipython notebook
```

## 2. 过滤所有不带3个'd'和3个'e'的地名

In [1]:

```python

    import pandas as pd  # for data loading and manipulation
    import requests      # for the request-function to OSRM
    import numpy as np   # some direct matrix manipulations
    #import numexpr
    
```

In [2]:

```python

    de = pd.read_table('data/germany-latest.tsv', low_memory=False ) # read OSM datadump of DE places
    
```

In [3]:

```python

    de.shape # 3million spatial entities 
    
```

Out[3]:

```python

    (2948639, 18)
```

In [4]:

```python

    de.columns # OSM variables included
    
```

Out[4]:

```python

    Index([u'name', u'class', u'type', u'lon', u'lat', u'place_rank',
           u'importance', u'street', u'city', u'county', u'state', u'country_code',
           u'country', u'display_name', u'west', u'south', u'east', u'north'],
          dtype='object')
```

In [5]:

```python

    de.type.value_counts() # counts by type of spatial feature
    
```

Out[5]:

```python

    residential       1511729
    secondary          229633
    tertiary           210144
    service            198968
    unclassified       179072
    track              141591
    living_street      103261
    primary             92683
    footway             67933
    path                50243
    hamlet              43889
    village             40548
    administrative      22066
    cycleway            12213
    suburb               9783
    steps                8371
    trunk                6025
    motorway             4052
    neighbourhood        2526
    construction         2525
    town                 2520
    primary_link         2405
    secondary_link       2209
    trunk_link           1218
    tertiary_link        1066
    motorway_link         619
    road                  565
    raceway               339
    bridleway             270
    city                   95
    quarter                38
    borough                27
    corridor               13
    dtype: int64
```

In [6]:

```python

    # function to test of the place-name matches the triple e's and d's
    def match_hint(x):
        try:
            if x.lower().count('e') == x.lower().count('d') == 3:
                return True
            else:
                return False
        except AttributeError:
            return False
    
```

In [7]:

```python

    # apply the criteria test-function to each name, and record matches
    de.ismatch = de.name.apply(match_hint)
    
```

In [8]:

```python

    de.ismatch.value_counts() # we have 1483 matches across Germany
    
```

Out[8]:

```python

    False    2947156
    True        1483
    dtype: int64
```

In [9]:

```python

    de_match = de[de.ismatch] # filter out the matched entities
    
```

In [10]:

```python

    de_match.type.value_counts()
    
```

Out[10]:

```python

    residential       568
    secondary         220
    unclassified      113
    path              106
    track             102
    tertiary           93
    service            84
    living_street      44
    administrative     32
    primary            28
    footway            23
    village            18
    hamlet             12
    neighbourhood      10
    suburb              7
    cycleway            6
    construction        4
    tertiary_link       4
    town                3
    steps               2
    bridleway           2
    primary_link        1
    secondary_link      1
    dtype: int64
```

In [11]:

```python

    # it has to be a town of some sort, filter out the rest.
    de_towns = de_match[de_match.type.isin(['village', 'suburb', 'hamlet', 'suburb', 'town', 'neighbourhood', 'city'])]
    
```

In [12]:

```python

    de_towns.type.value_counts()
    
```

Out[12]:

```python

    village          18
    hamlet           12
    neighbourhood    10
    suburb            7
    town              3
    dtype: int64
```

In [13]:

```python

    de_towns.type.shape # we have 50 places left
    
```

Out[13]:

```python

    (50,)
```

In [14]:

```python

    de_towns.name # possible locations with the right name
    
```

Out[14]:

```python

    11679                   Adelheidsgroden
    362646                   Baddeckenstedt
    364889        Bad Neustadt an der Saale
    530409                     Breddewarden
    530410                     Breddewarden
    578347        Buchholz in der Nordheide
    649310                       Dederstedt
    651680                        Deiderode
    658325                       Deudesfeld
    661581                       Diedesfeld
    665404                        Diestedde
    672703                       Dodesheide
    710991             Drei Linden Siedlung
    714318         Drestedt-Valzik Siedlung
    718311         Drosendorf an der Aufseß
    726726                   Dunwarderfelde
    733931     Ebersdorf (bei Ludwigsstadt)
    840590                     Fedderwarden
    903566           Fredersdorf-Vogelsdorf
    915214          Friedersdorf, Oderbruch
    931535        Friedrichswalde-Ottendorf
    1013722                   Göddeckenrode
    1056754                Großfedderwarden
    1073998       Gundelfingen an der Donau
    1187636                 Heidenoldendorf
    1280572                Hohenböddenstedt
    1565076               Klein Waddewarden
    1640130       Kummersdorf-Alexanderdorf
    1829644                       Meddewade
    1841699        Menzenschwand-Vorderdorf
    1928772          Naundorf vor der Heide
    1931349                       Nedderend
    1940174             Neu Duvenstedt-Nord
    1940175              Neu Duvenstedt-Süd
    1965225                Niederdollendorf
    1969279                 Niederstadtfeld
    2020344           Öd in der Pechschneid
    2083754                       Peddenöde
    2427090               Seebad Rüdersdorf
    2435941            Seidfeld (Sauerland)
    2449680          Siedlung Denrodtstraße
    2449685       Siedlung der Freundschaft
    2451328            Siedlung Waldfrieden
    2451329            Siedlung Waldfrieden
    2451330            Siedlung Waldfrieden
    2557288    Studentendorf Waldhäuser-Ost
    2736841         Waldsiedlung Junkerfeld
    2765743                      Weddermöde
    2765807                     Wedderstedt
    2765812                     Weddewarden
    Name: name, dtype: object
```

In [15]:

```python

    coords = zip(de_towns.lon, de_towns.lat) # create a list with latitude-longitude pairs 
    labels = de_towns.name.tolist() # get a list of place-names
    coords.append((5.2205227, 51.023644)) # add the coordinates of Heusden to the list
    labels.append('Heusden') # add Heusden to the list of place names
    
```

## 3. 从OSRM中获取到所有匹配项的驾驶距离

In [16]:

```python

    # Get the pairwise drive time in minutes for a list of coordinates,
    # using the OSM-based open source online routing webservice http://project-osrm.org/
    # (code from python-osrm: https://github.com/ustroetz/python-osrm/blob/master/osrm/core.py#L142 )
    
    def drivetime_table(list_coords, list_ids, output='df',
              host='http://localhost:5000'):
        """
        Function wrapping OSRM 'table' function in order to get a matrix of
        time distance as a numpy array or as a DataFrame
        Params :
            list_coords: list
                A list of coord as [x, y] , like :
                     list_coords = [[21.3224, 45.2358],
                                    [21.3856, 42.0094],
                                    [20.9574, 41.5286]] (coords have to be float)
            list_ids: list
                A list of the corresponding unique id, like :
                         list_ids = ['name1',
                                     'name2',
                                     'name3'] (id can be str, int or float)
            host: str, default 'http://localhost:5000'
                Url and port of the OSRM instance (no final bakslash)
            output: str, default 'pandas'
                The type of matrice to return (DataFrame or numpy array)
                    'pandas', 'df' or 'DataFrame' for a DataFrame
                    'numpy', 'array' or 'np' for a numpy array
        Output:
            - 'numpy' : a numpy array containing the time in minutes
                    (or NaN when OSRM encounter an error to compute a route)
            or
            - 'pandas' : a labeled DataFrame containing the time matrix in minutes
                    (or NaN when OSRM encounter an error to compute a route)
            -1 is return in case of any other error (bad 'output' parameter,
                wrong list of coords/ids, unknow host,
                wrong response from the host, etc.)
        """
        if output.lower() in ('numpy', 'array', 'np'):
            output = 1
        elif output.lower() in ('pandas', 'dataframe', 'df'):
            output = 2
        else:
            print('Unknow output parameter')
            return -1
    
        query = [host, '/table?loc=']
        for coord, uid in zip(list_coords, list_ids):  # Preparing the query
            tmp = ''.join([str(coord[1]), ',', str(coord[0]), '&loc='])
            query.append(tmp)
        query = (''.join(query))[:-5]
        try:  # Querying the OSRM local instance
            rep = requests.get(query)
            parsed_json = rep.json()
        except Exception as err:
            print('Error while contacting OSRM instance : \n{}'.format(err))
            return -1
    
        if 'distance_table' in parsed_json.keys():  # Preparing the result matrix
            mat = np.array(parsed_json['distance_table'], dtype='float64')
            if len(mat) < len(list_coords):
                print(('The array returned by OSRM is smaller to the size of the '
                       'array requested\nOSRM parameter --max-table-size should be'
                       ' increased or function osrm.table_OD(...) should be used'))
                return -1
            mat = mat/(10*60)  # Conversion in minutes
            mat = mat.round(1)
            mat[mat == 3579139.4] = np.NaN  # Flag the errors with NaN
            if output == 1:
                return mat
            elif output == 2:
                df = pd.DataFrame(mat, index=list_ids, columns=list_ids, dtype=float)
                return df
        else:
            print('No distance table return by OSRM local instance')
            return -10
    
```

In [17]:

```python

    # get the driving-time in minutes for all the coords
    time_matrix = drivetime_table(coords, labels, output='dataframe', host='http://router.project-osrm.org/')
    time_matrix.shape # 51x51 matrix, for all german cities + Heusden
    
```

Out[17]:

```python

    (51, 51)
```

In [18]:

```python

    solutions = time_matrix.loc[time_matrix.Heusden < 180, time_matrix.Heusden < 180]
    
```

In [19]:

```python

    solutions # we are left with six posibilities
    
```

Out[19]:

| Deudesfeld | Niederdollendorf | Niederstadtfeld | Peddenöde | Seidfeld (Sauerland) | Siedlung Denrodtstraße | Heusden  
---|---|---|---|---|---|---|---  
Deudesfeld | 0.0 | 90.6 | 11.0 | 149.3 | 190.5 | 169.5 | 131.1  
Niederdollendorf | 91.5 | 0.0 | 80.5 | 70.1 | 109.1 | 90.3 | 119.1  
Niederstadtfeld | 11.1 | 79.6 | 0.0 | 138.2 | 179.5 | 158.5 | 132.8  
Peddenöde | 150.0 | 69.5 | 139.0 | 0.0 | 62.5 | 46.1 | 136.9  
Seidfeld (Sauerland) | 191.1 | 109.9 | 180.5 | 63.9 | 0.0 | 63.8 | 179.3  
Siedlung Denrodtstraße | 171.1 | 90.6 | 160.1 | 47.2 | 62.5 | 0.0 | 132.0  
Heusden | 131.8 | 118.6 | 133.3 | 137.4 | 175.3 | 132.4 | 0.0  
  
## 4. 绘制交叉线

为了方便，使用R Leaflet，加上截图。外部的点是Worms,
Koblenz, Aarlen, 和Malmedy。里面的点是**Deudesfeld**和**Niederstadtfeld**，剩下的是不那么靠近交叉处的点。

![Image of Yaktocat](https://s33.postimg.org/kk7l6rc7z/germany_locations_lines.png)

## 5. 确认最终匹配的纹章盾

[Deudesfeld](https://de.wikipedia.org/wiki/Deudesfeld)的盾牌包含一个斧头和一个樵夫斧头：

![Deudesfeld Weapon](https://upload.wikimedia.org/wikipedia/commons/thumb/6/62/Wappen_von_Deudesfeld.png/140px-Wappen_von_Deudesfeld.png)

Jude的共同属性是斧头，因为他死于其。Simon通常用锯（半锯），或者其他木工工具，例如斧，来描绘。

## 最终判决

**Deudesfeld**