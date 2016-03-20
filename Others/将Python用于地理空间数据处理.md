原文：[Python for geospatial data processing](http://www.machinalis.com/blog/python-for-geospatial-data-processing/)

---

毫无疑问，Python是当今最流行，最通用的编程语言之一。这有很多种强有力的原因，但在我看来，最重要的是：开源定义，语法简单，[包括电池的理念(batteries included philosophy)](https://www.python.org/dev/peps/pep-0206/)以及一个棒棒哒的[全球社区](https://www.python.org/community/)。

Python被广泛采用的领域的一个有趣的例子就是科学世界。这也解释了像[PyData](http://pydata.org/) 或者[Scipy](http://www.scipy.org/)生态圈这类社区的存在。

另一个我看到的越来越多人感兴趣并使用的更具体的区域是地理空间数据处理。这方面的一个证明是许多知名的工具使用它，例如[GDAL](http://gdal.org/),
[ArcGIS](http://desktop.arcgis.com/en/analytics/python/), [GRASS](https://grass.osgeo.org/),[QGIS](http://www.qgis.org/)等等。

因此，这篇文章的目的是为了展示在这个特定的范围里，Python生态圈的优势和力量。我将通过一个复杂任务的例子来说明，这个例子是该领域的典型例子：卫星图像分类。


### Python中的卫星图像分类

出于无数种原因，会对卫星图像进行分类。它用于农业，地质，突发事件的监测，监视，天气预报，经济研究，社会科学等等。

出于这个原因，许多GIS和地理空间数据管理系统包含工具来执行分类。但是，这种方法有一定的局限性：这个过程是手动的，你通常有一套非常小的关于分类技术及其他超参数选项。

另一种可能性是使用[领域特定语言](https://en.wikipedia.org/wiki/Domain-specific_language) (“_DSL_”) 进行分类，如R, IDL, MATLAB, Octave，等等。但是在这种情况下，你通常局限于一个实验环境。

多亏了Python的脚本功能和丰富的生态系统，它提供了其他DSL的大多数好处，因此它非常适合于进行研究和快速原型。此外，作为一个广泛采用的通用语言，它也有益于开发用于生产的，高效，可维护，可扩展，产业规模的分类系统。

因此，让我们看看利用存在于Python生态圈中用于地理空间数据处理的工具，处理图像分类是有多容易。在几百行代码中，我们将要开发的脚本：

*   处理一张陆地卫星8 GeoTiff图像（栅格数据），
*   从形状文件(Shapefile)中提取训练和测试数据（矢量数据）
*   使用现代机器学习技术训练和分类
*   评估结果

为了尝试及[kiss](https://en.wikipedia.org/wiki/KISS_principle)保持这个问题的正确分类部分的重点，我不打算深入数据预处理（校准、地理变换等等）。我知道这是工作的一个基本组成部分，但它不是我现在要扩展的部分。

像往常一样，大部分神奇的部分将使用作为主要依赖的工具完成：


#### GDAL

[GDAL](http://gdal.org/)是一个栅格和适量地理空间数据格式的翻译库。对于所有支持的格式，它可以展现为栅格抽象数据模型或矢量抽象数据模型。

它是用C/C++实现的，所以具有高性能，同时，它提供了[Python绑定](http://trac.osgeo.org/gdal/wiki/GdalOgrInPython)。

安装这个库可能不是一个小任务，特别是对于那些不是很熟悉安装Python依赖的过程的人来说。在任何情况下，GDAL网站已经提供了详细的指令，它们归纳在一个[README](https://github.com/machinalis/satimg)文件中，位于与这篇文有关的代码库中。


#### scikit-learn

[scikit-learn](http://scikit-learn.org/stable/)是一个Python开源机器学习裤。它具有多种分类、回归和聚类算法。它被设计用于与Python数值与科学库numpy和scipy协同工作。

它大部分使用Python编写，一些核心算法是使用[Cython](https://en.wikipedia.org/wiki/Cython)(使用C/C++)编写的，以获得高性能。


#### 示例数据

幸亏有了GDAL API，这篇文章中我们将开发的程序与许多不同类型的图像格式和地理相应的载体一起工作。但万一你手边没有数据集，可以下载[一些示例数据](https://drive.google.com/file/d/0B64odlXwDnHeUVBWNXVocU84SkU/view?usp=sharing)。

它包括农业区的一张陆地卫星8图像，以及不同的作物样本的某些合成矢量数据的一部分。它是那种用于精耕细作的产品数据（例如，作物的标识）。

![http://www.machinalis.com/static/media/uploads/input-raster.png](http://www.machinalis.com/static/media/uploads/input-raster.png)

该文件是一个压缩数据目录，它有三个子目录：

*   **image** 包含了一个带有L1T陆地卫星场景的收成的Geotiff文件(LANDSAT 8, sensor OLI, path 229, row 81, 2016-01-19 19:14:02 UTC).
只存在频带1到7 (Aerosol, VIS, NIR, SWIR. 30m resolution)
*   **train** 已经带有了用于训练的附带矢量数据的shapefile。每一个文件定义了一个类别，即：所有的点，poligon等等。存在于一个shapefile中，被用于定义一个类别的样本。在我们的样本数据中，我们有5个列表，即A, B, C, D和E (我知道，不是太花哨。)
*   **test**类似于测试目录，但是将使用该样本来验证分类结果。

由于本文将不关注分类的结果，我将不谈论任何关于数据质量，要求或准备的任何细节。如果你想要知道或者讨论关于这个问题的任何东西，请使用评论，或者给我发邮件。


#### 示例程序

接下来，我们将开发一个脚本来分类地理空间数据。该程序的一个更Pythonic和完备的版本可以[在这个仓库](https://github.com/machinalis/satimg/blob/master/classify.py)找到。那个版本包括我们在这篇文章中将不会考虑的日志，文档字符串，评论，pep-8，一些错误控制和其他好的编程实践。

在代码库中，你也将找到在本文中描述的脚本的一个更简单的版本([这里](https://github.com/machinalis/satimg/blob/master/classify.blog_post.py))。在你下载或复制粘贴这些行到一个文件中或一个Python解析器中之前，确保安装了下述依赖：
```
GDAL==2.0.1
numpy>=1.10,<1.11
scipy==0.17.0
scikit-learn==0.17
# Optionally, you can install matplotlib
```

##### 准备工作

现在，我们已经准备好写代码了。所以，第一件事是：导入我们的主要依赖，然后定义一个颜色列表：
```py
import numpy as np
import os

from osgeo import gdal
from sklearn import metrics
from sklearn.ensemble import RandomForestClassifier

# A list of "random" colors (for a nicer output)
COLORS = ["#000000", "#FFFF00", "#1CE6FF", "#FF34FF", "#FF4A46", "#008941"]
```

从最后的导入行可以看到，我们将使用[Random Forest](https://en.wikipedia.org/wiki/Random_forest)技术来进行分类。多亏了Scikit-learn，我们可以轻松地实验/比较许多不同[分类技术](http://scikit-learn.org/stable/supervised_learning.html#supervised-learning)，例如随机梯度下降，支持向量机，最近邻，AdaBoost等等。

颜色列表将被嵌入到GeoTiff输出中。这将允许你轻松地在任何标准的图像浏览器程序中对其可视化。

接下来，让我们定义一些稍后将要用到的有用的函数。它们大量使用GDAL API来操纵栅格和矢量数据（代码很好的自解释）。
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

现在，我们有了所有我们需要进行真实的分类的东西。让我们创造一些变量来定义输入和输出：
```py
raster_data_path = "data/image/2298119ene2016recorteTT.tif"
output_fname = "classification.tiff"
train_data_path = "data/test/"
validation_data_path = "data/train/"
```

在上面的几行中，我假设我们将使用前述的样例数据。


##### 训练

现在，我们将使用GDAL API来读取输入的GeoTiff：提取地理信息，并将其转换到一个numpy数组中：
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

接下来，我们将处理训练数据：投影训练数据集中所有的矢量数据到一个numpy数组中。为每一个类分配一个标签（位于1到类的总数量之间的一个数字）。如果在这个新数组中的(i, j)位置上的值**v**非零，那么意味着像素(i, j)必须被用作类**v**的训练样本。
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

`training_samples`是用于训练的像素列表。在我们的样例中，一个像素是带的7维空间中的一点。

`training_labels`是类标签列表，_i-th_ 位置表示`training_samples`中_i-th_像素的类。

所以现在，我们知道输入图像的哪些像素必须用于训练。接下来，实例化Scikit-learn中的一个RandomForestClassifier。
```py
classifier = RandomForestClassifier(n_jobs=-1)
classifier.fit(training_samples, training_labels)
```

这里，有许多参数我们可以玩一玩。我建议你阅读相关文档，并尝试不同的可能性。

通常情况下，这些参数的精细调谐依赖于数据，学习的特定域，记忆和处理资源，预期精度等

为了集中精力和简单起见，我不会在这个问题上展开。正如你所看到的，我只是传递一个选项来使用我的电脑中的所有内核。


##### 分类

看！不管你信不信，这都是最难的部分。现在，我们有了一个训练模型，可以分类（预测）我们所拥有的像素数据的类别。让我们放手去做吧。
```py
n_samples = rows*cols
flat_pixels = bands_data.reshape((n_samples, n_bands))
result = classifier.predict(flat_pixels)
classification = result.reshape((rows, cols))
```

我们使用了训练对象来对所有的输入图像进行分类。我们的分类器知道如何训练像素，并且其`predict`函数期望一个像素列表，而不是一个N×M矩阵。正因为如此，我们先重塑频带数据，然后再分类（这使得输出看起来像一个图像，而不仅仅是一个多维象素的列表）。

在这一点上，如果你已经安装了`matplotlib`，你可以想像结果：
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

但比仅仅看我们惊人的频带新分类更重要的是将其保存到磁盘并评估其准确性。因此，让我们做到这一点。

对于第一个任务，我们已经创建了一个辅助函数：`write_geotiff` （你已经忘了它，迷失在对彩色图像的惊叹中）。我们可以只是使用`matplotlib.pyplot.imsave`保存像素的数据，但之后我们会失去所有包含在GeoTiff格式中有价值的地理信息（和其他元数据）。而这样的信息对于GTS和其他卫星数据处理系统是必不可少的。因此，我们将使用我们的GDAL驱动函数：
```py
write_geotiff(output_fname, classification, geo_transform, proj)
```

这很简单。现在，你应该能够打开我们刚刚创建的新文件，使用任何图像浏览器，GIS和遥感数据处理系统。


##### 评估结果

终于接近结束时，在可以验证我们的分类的准确性之前，我们需要以一种与我们处理训练数据相似的方式预处理我们的测试数据：
```py
shapefiles = [os.path.join(validation_data_path, "%s.shp" % c)
              for c in classes]
verification_pixels = vectors_to_raster(shapefiles, rows, cols,
                                        geo_transform, proj)
for_verification = np.nonzero(verification_pixels)
verification_labels = verification_pixels[for_verification]
predicted_labels = classification[for_verification]
```

上面，我们有了要验证像素的期望标签以及所计算的标签。因此，我们可以分析结果了。为此，我们可爱的scikit-learn提供了许多工具。所以，让我们使用其中两个。
```py
print("Confussion matrix:\n%s" %
      metrics.confusion_matrix(verification_labels, predicted_labels))
```

这应该打印出一些像这样的东西：
```
Confussion matrix:
[[ 82   0   6   0   0]
 [  0 180   0   0   0]
 [  0   0  65   0   0]
 [  0   0   2  89   0]
 [  0   0   0   0 160]]
```

接下来是精度和准确性：
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

##### 总结

在这篇文中，我们开发了一个脚本，它处理栅格和矢量数据，使用复杂的机器学习技术进行监督分类，可视化输出并评估结果。

所有这一切都在一个通用的，广泛采用的语言的100行中实现，并使用了高效的工具。

至少对我来说，这证明了Python生态系统的地理空间数据处理的好处和能力。

不幸的是，由于保密协议，我不能分享更具体的（和非常有趣的）现实生活中的例子。希望将来，为了扩展在这个领域的优势以及一些有用的很酷的Python技巧，我会被允许这样做。