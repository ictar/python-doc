原文：[Python for geospatial data processing](http://www.machinalis.com/blog/python-for-geospatial-data-processing/)

---

## Python for geospatial data processing

Python is undoubtedly one of the most popular, general purpose, programming languages today.
There are many strong reasons for this but in my opinion the most important ones are: an Open Source
Definition, the simplicity of its syntax, the [batteries included philosophy](https://www.python.org/dev/peps/pep-0206/)
and an awesome, [global community](https://www.python.org/community/).

One interesting example of an area where Python is being adopted massively is the scientific world.
That explains the existence of communities like [PyData](http://pydata.org/) or the
[Scipy](http://www.scipy.org/)  ecosystem.

Another more specific area where I see an increasing interest and use of Python is for geospatial
data processing. A proof of this are the many well known tools using it, such as [GDAL](http://gdal.org/),
[ArcGIS](http://desktop.arcgis.com/en/analytics/python/), [GRASS](https://grass.osgeo.org/),
[QGIS](http://www.qgis.org/) and more.

Then, the objective of this post is to show the advantages and power of the Python ecosystem in this particular ambit.
I’m going to do this through an example of a complex task, which is typical in this field: satellite image classification.


### Satellite Images Classification In Python

Satellite images are classified for an infinite number of reasons. It has uses in agriculture, geology,
emergencies monitoring, surveillance, weather forecast, economical studies, social sciences and more.

For this reason, many GIS and geospatial data management systems include tools to perform classifications.
But this approach has some limitations: the process is manual and you usually have a very small set of
options regarding the classification technique and other hyperparameters.

Another possibility is to classify using implementations in [domain-specific languages](https://en.wikipedia.org/wiki/Domain-specific_language) (“_DSL_”) such as R, IDL, MATLAB, Octave, etc. But in this case you are usually limited
to an experimental context.

Python, thanks to its scripting features and rich ecosystem, provides most of the benefits of other DSLs so it’s great for
doing research and quick prototyping. Moreover, being a widely adopted general purpose language, it is also useful
to develop production-ready, efficient, maintainable, scalable, industrial scale classification systems.

Therefore, let’s see how easy it is to perform an image classification, making use of the tools existing in the
Python _ecosystem_ for geospatial data processing. In a hundred lines of code we are going to
develop a script to:

*   process a Landsat 8 GeoTiff image (raster data),
*   extract training and testing data from Shapefiles (vector data)
*   train and classify using a modern Machine Learning technique
*   assess the results

To try and [kiss](https://en.wikipedia.org/wiki/KISS_principle) to maintain the focus in the proper
classification part of the issue, I’m not going to delve into the depths of data pre-processing
(calibration, geographic transformations, etc.). I know that is a basic part of the job but it’s not
where I want to expand now.

As usual, most of the magic will be done by the tools we are going to be using as main dependencies:


#### GDAL

[GDAL](http://gdal.org/) is a translator library for raster and vector geospatial data formats.
It presents a single raster abstract data model and vector abstract data model for all supported formats.

It is implemented in C/C++, so it is highly performant, and it provides
[Python bindings](http://trac.osgeo.org/gdal/wiki/GdalOgrInPython).

Installing this library may not be a trivial task, specially for those who are not very familiar with
the process of installing Python dependencies. In any case, the GDAL site has got detailed instructions
which are summarized in a [README](https://github.com/machinalis/satimg) file in the code repository related to this post.


#### scikit-learn

[scikit-learn](http://scikit-learn.org/stable/) is an open source machine learning library for Python. It features various classification,
regression and clustering algorithms. It is designed to interoperate with the Python numerical and scientific
libraries NumPy and SciPy.

It is largely written in Python, with some core algorithms written in [Cython](https://en.wikipedia.org/wiki/Cython)
(using C/C++) to achieve performance.


#### Example data

Thanks to the GDAL api, the program that we are going to develop throughout this post
works with many different kind of image formats  and geographically corresponding vectors.
But in case you don’t have a dataset at hand, you can download
[some sample data](https://drive.google.com/file/d/0B64odlXwDnHeUVBWNXVocU84SkU/view?usp=sharing).

It includes part of a Landsat 8 image of an agricultural area and some synthetic vector data
with samples of different crops. It is the kind of data used for precision farming’s products (for example,
crop’s identification).

![http://www.machinalis.com/static/media/uploads/input-raster.png](http://www.machinalis.com/static/media/uploads/input-raster.png)

The file is a compressed data directory with three sub-directories:

*   **image** includes a Geotiff file with the crop of a L1T Landsat scene
(LANDSAT 8, sensor OLI, path 229, row 81, 2016-01-19 19:14:02 UTC).
Only bands 1 to 7 are present (Aerosol, VIS, NIR, SWIR. 30m resolution)
*   **train** has got shapefiles with vector data to be used for training. Each file defines a class,
that is: all the points, poligons, etc. existing within a shapefile are used to define samples of one class.
In our sample data we have 5 classes, named A, B, C, D and E (not too fancy, I know.)
*   **test** is similar to the _test_ directory, but this samples are going to be used to verify
the classification results.

Since this post will not focus on the results of the classification,
I’m not going to go into any details about data quality, requirements or preparation. In case you
want to know or discuss anything in this matter, please use the comments or send me an email.


#### Example program

Next, we will develop a script to classify geospatial data. A more _pythonic_ and complete
version of the program can be found [in the repo](https://github.com/machinalis/satimg/blob/master/classify.py).
That version includes logging, docstrings, comments, pep-8, some error control and other
good programming practices that we are not going to take into consideration in this post.

In the code repository you will also find the simpler version of the script, described in this post
([here](https://github.com/machinalis/satimg/blob/master/classify.blog_post.py)).
Before you download or copy-paste these lines in a file or a Python interpreter, make sure that you install the following
dependencies:
```
GDAL==2.0.1
numpy>=1.10,<1.11
scipy==0.17.0
scikit-learn==0.17
# Optionally, you can install matplotlib
```

##### Preliminars

Now we are ready to code. So, first things first: we import our main dependencies and define a list of colors:
```py
import numpy as np
import os

from osgeo import gdal
from sklearn import metrics
from sklearn.ensemble import RandomForestClassifier

# A list of "random" colors (for a nicer output)
COLORS = ["#000000", "#FFFF00", "#1CE6FF", "#FF34FF", "#FF4A46", "#008941"]
```

From the last import line you can see that we are going to classify using the
[Random Forest](https://en.wikipedia.org/wiki/Random_forest)
technique. Thanks to Scikit-learn, we could easily experiment with or compare many different
[classification techniques](http://scikit-learn.org/stable/supervised_learning.html#supervised-learning)
such as stochastic gradient descent, support vector machines, nearest neighbors, AdaBoost, etc.

The list of colors will be embedded in the GeoTiff output. This will allow you to easily
visualize it in any standard image viewer program.

Next, let’s define some useful functions that we are going to be using later. They are making heavy use
of the GDAL api to manipulate raster and vector data (the code is pretty self explanatory).
```py
def create_mask_from_vector(vector_data_path, cols, rows, geo_transform,
                            projection, target_value=1):
    """Rasterize the given vector (wrapper for gdal.RasterizeLayer)."""
    data_source = gdal.OpenEx(vector_data_path, gdal.OF_VECTOR)
    layer = data_source.GetLayer(0)
    driver = gdal.GetDriverByName('MEM')  # In memory dataset
    target_ds = driver.Create('', cols, rows, 1, gdal.GDT_UInt16)
    target_ds.SetGeoTransform(geo_transform)
    target_ds.SetProjection(projection)
    gdal.RasterizeLayer(target_ds, [1], layer, burn_values=[target_value])
    return target_ds


def vectors_to_raster(file_paths, rows, cols, geo_transform, projection):
    """Rasterize the vectors in the given directory in a single image."""
    labeled_pixels = np.zeros((rows, cols))
    for i, path in enumerate(file_paths):
        label = i+1
        ds = create_mask_from_vector(path, cols, rows, geo_transform,
                                     projection, target_value=label)
        band = ds.GetRasterBand(1)
        labeled_pixels += band.ReadAsArray()
        ds = None
    return labeled_pixels


def write_geotiff(fname, data, geo_transform, projection):
    """Create a GeoTIFF file with the given data."""
    driver = gdal.GetDriverByName('GTiff')
    rows, cols = data.shape
    dataset = driver.Create(fname, cols, rows, 1, gdal.GDT_Byte)
    dataset.SetGeoTransform(geo_transform)
    dataset.SetProjection(projection)
    band = dataset.GetRasterBand(1)
    band.WriteArray(data)
    dataset = None  # Close the file
```

Now we have all that we need to perform the actual classification.
Let’s create some variables to define our input and output:
```py
raster_data_path = "data/image/2298119ene2016recorteTT.tif"
output_fname = "classification.tiff"
train_data_path = "data/test/"
validation_data_path = "data/train/"
```

In the lines above, I assume we are using the sample data described before.


##### Training

Now, we will use the GDAL api to read the input GeoTiff: extract the geographic information and transform
the band’s data into a numpy array:
```py
raster_dataset = gdal.Open(raster_data_path, gdal.GA_ReadOnly)
geo_transform = raster_dataset.GetGeoTransform()
proj = raster_dataset.GetProjectionRef()
bands_data = []
for b in range(1, raster_dataset.RasterCount+1):
    band = raster_dataset.GetRasterBand(b)
    bands_data.append(band.ReadAsArray())

bands_data = np.dstack(bands_data)
rows, cols, n_bands = bands_data.shape
```

Next, we’ll process the training data: project all the vector data, in the training dataset, into a numpy array.
Each class is assigned a _label_ (a number between 1 and the total number of classes). If the value **v** in
the position (i, j) of this new array is not zero, that means that the pixel (i, j) must be used as a training
sample of class **v**.
```py
files = [f for f in os.listdir(train_data_path) if f.endswith('.shp')]
classes = [f.split('.')[0] for f in files]
shapefiles = [os.path.join(train_data_path, f)
              for f in files if f.endswith('.shp')]

labeled_pixels = vectors_to_raster(shapefiles, rows, cols, geo_transform,
                                   proj)
is_train = np.nonzero(labeled_pixels)
training_labels = labeled_pixels[is_train]
training_samples = bands_data[is_train]
```

`training_samples` is the list of pixels to be used for training.
In our case, a pixel is a point in the 7-dimensional space of the bands.

`training_labels` is a list of class labels such that the _i-th_ position indicates
the class for _i-th_ pixel in `training_samples`.

So now, we know what pixels of the input image must be used for training. Next, we instantiate a RandomForestClassifier
from Scikit-learn.
```py
classifier = RandomForestClassifier(n_jobs=-1)
classifier.fit(training_samples, training_labels)
```

There are many parameters that we can play around with here. I encourage you to read the
related documentation and try different possibilities.

Normally, the fine tunning of these parameters depend on the data, the specific domain of study,
memory and processing resources, expected accuracy, etc.

To stay focused and for the sake of simplicity, I’m not going to expand on this issue.
As you can see, I’m only passing an option to use all the cores in my computer.


##### Classifying

And voila! believe it or not, that was the hard part. Now we have a trained model, able to classify (predict)
the class of whatever pixels data we have. So let’s do that.
```py
n_samples = rows*cols
flat_pixels = bands_data.reshape((n_samples, n_bands))
result = classifier.predict(flat_pixels)
classification = result.reshape((rows, cols))
```

We used the trained object to classify all the input image. Our classifier knows how to train
pixels and its `predict` function expects a list of pixels, not an NxM matrix. Because of that, we
reshaped the bands data before and after the classification (so that the output looks like an image
and not just a list of multi-dimensional pixels).

At this point, if you have `matplotlib` installed you can visualize the results:
```py
from matplotlib import pyplot as plt
f = plt.figure()
f.add_subplot(1, 2, 2)
r = bands_data[:,:,3]
g = bands_data[:,:,2]
b = bands_data[:,:,1]
rgb = np.dstack([r,g,b])
f.add_subplot(1, 2, 1)
plt.imshow(rgb/255)
f.add_subplot(1, 2, 2)
plt.imshow(classification)
```

![http://www.machinalis.com/static/media/uploads/input-output.jpg](http://www.machinalis.com/static/media/uploads/input-output.jpg)

But more important than merely watch our astounding, brand new classification is to save it to disk and
asses its accuracy. So let’s do that.

For the first task, we already created an auxiliary function: `write_geotiff`
(that you had already forgotten about, lost in the admiration of the colourful image). We could just
save the pixel’s data using `matplotlib.pyplot.imsave` but then we would loose all the valuable geographic
information (and other metadata) included in the GeoTiff format. And such information is essential for the
GIS and other satellite data processing systems. So we’ll use our GDAL-powered function:
```py
write_geotiff(output_fname, classification, geo_transform, proj)
```

That was simple. Now you should be able to open that new file that we just created, with any image viewer,
GIS or remote sensing data’s processing system.


##### Assess the results

Finally closer to the end, before we can verify our classification’s accuracy, we need to pre-process
our testing dataset in a fashion similar to what we did with the training data:
```py
shapefiles = [os.path.join(validation_data_path, "%s.shp" % c)
              for c in classes]
verification_pixels = vectors_to_raster(shapefiles, rows, cols,
                                        geo_transform, proj)
for_verification = np.nonzero(verification_pixels)
verification_labels = verification_pixels[for_verification]
predicted_labels = classification[for_verification]
```

There we have the expected label for the verification pixels, and the computed labels. So we can analyze
the results. For that, our beloved scikit-learn provides many tools. So let’s use two of them
```py
print("Confussion matrix:\n%s" %
      metrics.confusion_matrix(verification_labels, predicted_labels))
```

That should print something like this:
```
Confussion matrix:
[[ 82   0   6   0   0]
 [  0 180   0   0   0]
 [  0   0  65   0   0]
 [  0   0   2  89   0]
 [  0   0   0   0 160]]
```

Next, for precission and accuracy:
```py
target_names = ['Class %s' % s for s in classes]
print("Classification report:\n%s" %
      metrics.classification_report(verification_labels, predicted_labels,
                                    target_names=target_names))
print("Classification accuracy: %f" %
      metrics.accuracy_score(verification_labels, predicted_labels))
```

Should print something like this:
```
Classification report:
             precision    recall  f1-score   support

    Class C       1.00      0.93      0.96        88
    Class D       1.00      1.00      1.00       180
    Class B       0.89      1.00      0.94        65
    Class A       1.00      0.98      0.99        91
    Class E       1.00      1.00      1.00       160

avg / total       0.99      0.99      0.99       584

Classification accuracy: 0.986301
```

##### Conclusions

In this post we developed a script that processes raster and vector data,
performs a supervised classification using a sophisticated machine learning technique,
visualized the output and assessed the results.

All of this in 100 lines of a general purpose, widely adopted language and using
highly efficient tools.

At least for me, that proves the benefits and power of the Python ecosystem for
geospatial data processing.

Unfortunately, because of NDAs, I cannot share more specific (and very interesting) real-life examples.
Hopefully in the future I’ll be allowed to do so, in order to expand on the advantages and some cool Python
tricks useful in this field.
