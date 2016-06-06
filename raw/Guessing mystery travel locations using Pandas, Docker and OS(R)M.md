原文：[Guessing mystery travel locations using Pandas, Docker and OS(R)M](http://nbviewer.jupyter.org/gist/mhermans/8c32eea0d5ec29e6b4329acbe7f0d3de)

---

In July we are travelling for a weekend to a mystery location. Some tips we
have received:

  * Three times an 'd' and three times an 'e' in the locations' name.
  * A good two hours travel eastwards from a given starting location (Heusden), which means it is in Germany.
  * Look at the lines between Worms, Koblenz, Aarlen, and Malmedy.
  * The symbols in the heraldic shield of the location refer to [Saint Jude](https://en.wikipedia.org/wiki/Jude_the_Apostle) and [Saint Simon](https://en.wikipedia.org/wiki/Simon_the_Zealot).

To take a _very_ calculated guess at the location, we will

  1. Get all the German location names and their coordinates from [Open Street Map](https://www.openstreetmap.org/) (OSM).
  2. Filter the names on triple a's and triple e's.
  3. Calculate the effective driving time from the starting location to each of the matching locations using the webservice of the [Open Source Routing Machine](http://project-osrm.org/) (OSRM).
  4. Select those below 3h driving time, and near the intersection of the lines drawn from the four given cities.
  5. Look a the heraldic shields of the remaining locations.

## 1\. Get all the German placenames from OSM¶

To get a dump of OSM location names into Pandas for analysis, there is a very
useful Github repository [OSMNames](https://github.com/geometalab/OSMNames)
with Docker files and some scripts. It uses OSM data-dumps from [Geofabrik](http://nbviewer.jupyter.org/gist/mhermans/download.geofabrik.de/index.html), in
our case the data available for
[Germany](http://download.geofabrik.de/europe/germany.html).

We clone the Github repository and download the entire OSM-dataset for
Germany:

```python

    git clone https://github.com/geometalab/OSMNames.git
    cd OSMNames
    wget --directory-prefix=./data http://download.geofabrik.de/europe/germany-latest.osm.pbf
    
    
```

Then we load the OSM-database dump using Docker to get our environment in a
pinch, and export the German location names

```python

    docker-compose up -d postgres      # launches Postgres-database
    docker-compose run import-osm      # import 2.8GB pbf-file into Postgres 
    docker-compose run schema          # 
    docker-compose run export-osmnames # export location names to 500MB plain-text file
    
    
```

For analysis, create a Python virtual environment, and install the required
libraries with pip.

```python

    mkvirtualenv osmde
    pip install ipython pandas jupyter numpy requests numexpr
    
    
```

Launch an interactive IPython notebook, you can download and re-run the one
you are reading.

```python

    ipython notebook
```

## 2\. Filter all the place names with triple 'a' and triple 'e'¶

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

## 3\. Get the driving distance to all matches from OSRM¶

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
  
## 4\. Plot the crossing lines¶

Used R Leaflet for convenience, plus screenshot. Outer points are Worms,
Koblenz, Aarlen, and Malmedy. Inner points are **Deudesfeld** and
**Niederstadtfeld**, the rest are not that close to the intersection.

![Image of Yaktocat](https://s33.postimg.org/kk7l6rc7z/germany_locations_lines.png)

## 5\. Verify the heraldic shiels of the final matches¶

The shield of [Deudesfeld](https://de.wikipedia.org/wiki/Deudesfeld) contains
an axe and a woodcutters hatchet:

![Deudesfeld Weapon](https://upload.wikimedia.org/wikipedia/commons/thumb/6/62/Wappen_von_Deudesfeld.png/140px-Wappen_von_Deudesfeld.png)

A common attribute of Jude is an axe, given his death at the end of one. Simon
is commonly depicted with a saw (sawn in half), or other woodworking tools
such as a hatchet.

## Final verdict:¶

**Deudesfeld**