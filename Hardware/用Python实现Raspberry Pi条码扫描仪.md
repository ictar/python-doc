原文：[Raspberry Pi Barcode Scanner in Python](http://www.codepool.biz/raspberry-pi-barcode-scanner-python.html)

---

之前，我写过一篇文章[使用摄像头和Python实现树莓派条码扫描仪](http://www.codepool.biz/raspberrypi-barcode-scanner-webcam-python.html)，说明了如何使用Dynamsoft Barcode Reader SDK和OpenCV从头构建一个简单的条码扫描仪。方法
**decodeFile() **被用于检测图像文件中的条码。要使用这个API，你必须先编写图像缓存，它是通过OpenCV API读取到一个文件中的。由于该**I/O**操作花费太多时间了，因此这个API对于从摄像头视频流进行实时条码检测并不够好。考虑到此场景，我添加了一个新的Python API **decodeBuffer()**。在这篇文章中，我将说明如何创建和使用这个新的API。

![Raspberry Pi Barcode Scanner in Python](http://www.codepool.biz/wp-content/uploads/2016/11/rpi-python-webcam-small.png)

## 测试环境

  * 设备：**Raspberry Pi 3**
  * 操作系统：**RASPBIAN JESSIE WITH PIXEL**

## 前期准备

  * Dynamsoft Barcode Reader for Raspberry Pi
  * Python 2.7.0
  * OpenCV 3.0.0
  * Raspberry Pi 2 or 3
  * USB摄像头

## 构建和安装

### 如何在树莓派上构建OpenCV

  1. 下载并解压缩[源代码](https://github.com/opencv/opencv/releases)。
  2. 安装依赖：
```python
sudo apt-get install cmake
sudo apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
sudo apt-get install libxvidcore-dev libx264-dev
sudo apt-get install python-dev
```

  3. 设置编译：
```python
cd ~/opencv-3.0.0/
mkdir build
cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE \
                -D CMAKE_INSTALL_PREFIX=/usr/local \
                -D INSTALL_C_EXAMPLES=ON \
                -D INSTALL_PYTHON_EXAMPLES=ON \
                -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib-3.0.0/modules \
                -D BUILD_EXAMPLES=ON ..

```

  4. 编译和安装OpenCV: 
```python
    make -j4
    sudo make install
```

该共享库将会被安装到**/usr/local/lib/python2.7/dist-packages/cv2.so.**

### 如何使用Dynamsoft Barcode Reader SDK构建Python扩展

  1. 下载并解压缩[SDK包](http://www.dynamsoft.com/Downloads/Dynamic-Barcode-Reader-for-Raspberry-Pi-Download.aspx).
  2. 为**libDynamsoftBarcodeReader.so**创建符号链接：
```python
    sudo ln –s <Your dbr path>/lib/libDynamsoftBarcodeReader.so
/usr/lib/libDynamsoftBarcodeReader.so
```

  3. 打开**setup.py**，然后修改include和lib文件的路径：
```python
include_dirs=["/usr/lib/python2.7/dist-packages/numpy/core/include/numpy", "<Your dbr path>/include"],
library_dirs=['<Your dbr path>/lib'],
```

  4. 构建扩展：
```python
    sudo python setup.py build install
```

### 如何实现decodeBuffer方法？

由于源代码是从Windows版本移植过来的，一次呢我们必须定义以下类型和结构：

```python
typedef unsigned long DWORD;
typedef long LONG;
typedef unsigned short WORD;
 
typedef struct tagBITMAPINFOHEADER {
  DWORD biSize;
  LONG biWidth;
  LONG biHeight;
  WORD biPlanes;
  WORD biBitCount;
  DWORD biCompression;
  DWORD biSizeImage;
  LONG biXPelsPerMeter;
  LONG biYPelsPerMeter;
  DWORD biClrUsed;
  DWORD biClrImportant;
} BITMAPINFOHEADER;
```

在**decodeBuffer()**中，将**numpy**数据从Pythonz转换到C。除此之外，我们并行构建一个本地缓冲区，用于条码读取：

```python
#include <ndarraytypes.h>
 
static PyObject *
decodeBuffer(PyObject *self, PyObject *args)
{
    PyObject *o;
    if (!PyArg_ParseTuple(args, "O", &o))
        return NULL;
 
    PyObject *ao = PyObject_GetAttrString(o, "__array_struct__");
    PyObject *retval;
 
    if ((ao == NULL) || !PyCObject_Check(ao)) {
        PyErr_SetString(PyExc_TypeError, "object does not have array interface");
        return NULL;
    }
 
    PyArrayInterface *pai = (PyArrayInterface*)PyCObject_AsVoidPtr(ao);
    if (pai->two != 2) {
        PyErr_SetString(PyExc_TypeError, "object does not have array interface");
        Py_DECREF(ao);
        return NULL;
    }
 
    // Construct data with header info and image data 
    char *buffer = (char*)pai->data; // The address of image data
    int width = pai->shape[1];       // image width
    int height = pai->shape[0];      // image height
    int size = pai->strides[0] * pai->shape[0]; // image size = stride * height
    char *total = (char *)malloc(size + 40); // buffer size = image size + header size
    memset(total, 0, size + 40);
    BITMAPINFOHEADER bitmap_info = {40, width, height, 0, 24, 0, size, 0, 0, 0, 0};
    memcpy(total, &bitmap_info, 40);
 
    // Copy image data to buffer from bottom to top
    char *data = total + 40;
    int stride = pai->strides[0];
    int i = 1;
    for (; i <= height; i++) {
        memcpy(data, buffer + stride * (height - i), stride);
        data += stride;
    }
 
    // Dynamsoft Barcode Reader initialization
    __int64 llFormat = (OneD | QR_CODE | PDF417 | DATAMATRIX);
    int iMaxCount = 0x7FFFFFFF;
    ReaderOptions ro = {0};
    pBarcodeResultArray pResults = NULL;
    ro.llBarcodeFormat = llFormat;
    ro.iMaxBarcodesNumPerPage = iMaxCount;
    printf("width: %d, height: %d, size:%d\n", width, height, size);
    int iRet = DBR_DecodeBuffer((unsigned char *)total, size + 40, &ro, &pResults);
    printf("DBR_DecodeBuffer ret: %d\n", iRet);
    free(total); // Do not forget to release the constructed buffer 
     
    // Get results
    int count = pResults->iBarcodeCount;
    pBarcodeResult* ppBarcodes = pResults->ppBarcodes;
    pBarcodeResult tmp = NULL;
    retval = PyList_New(count); // The returned Python object
    PyObject* result = NULL;
    i = 0;
    for (; i < count; i++)
    {
        tmp = ppBarcodes[i];
        result = PyString_FromString(tmp->pBarcodeData);
        printf("result: %s\n", tmp->pBarcodeData);
        PyList_SetItem(retval, i, Py_BuildValue("iN", (int)tmp->llFormat, result)); // Add results to list
    }
    // release memory
    DBR_FreeBarcodeResults(&pResults);
 
    Py_DECREF(ao);
    return retval;
}
```

## 树莓派条码扫描器

### 如何设置视频的帧速率，帧的宽度和高度？

你可以参考[属性标识符](http://docs.opencv.org/2.4/modules/highgui/doc/reading_and_writing_images_and_video.html#videocapture-set):

  * **CV_CAP_PROP_FRAME_WIDTH**: 视频流中的帧宽度。
  * **CV_CAP_PROP_FRAME_HEIGHT**: 视频流中的帧高度。
  * **CV_CAP_PROP_FPS**: 帧速率。

如果使用属性标识符失败，那么如下直接设置其值：

```python
vc = cv2.VideoCapture(0)
vc.set(5, 30)  #set FPS
vc.set(3, 320) #set width
vc.set(4, 240) #set height
```

### 如何使用API decodeBuffer()?

```python
while True:
        cv2.imshow(windowName, frame)
        rval, frame = vc.read();
        results = decodeBuffer(frame)
        if (len(results) > 0):
            print "Total count: " + str(len(results))
            for result in results:
                print "Type: " + types[result[0]]
                print "Value: " + result[1] + "\n"
 
        # 'ESC' for quit
        key = cv2.waitKey(20)
        if key == 27:
            break
```

### 如何运行这个树莓派条码扫描器？

  1. 将一个USB摄像头连接到树莓派2或者3.
  2. 运行**app.py**: 
```python
    python app.py

```

## 源代码

<https://github.com/yushulx/opencv-python-webcam-barcode-reader/tree/master/raspberrypi>


