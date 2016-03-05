原文：[Import a Docker Container in Python](http://blog.deepgram.com/import-a-docker-container-in-python/)

---

### 为什么要这样做？

Docker容器对于应用之间彼此隔离是非常棒的，但如果你想要它们之间彼此通信呢？例如，如果你正在用Python开发一个应用，而这个应用需要与其他语言编写的软件进行交互。有[一些技巧](https://wiki.python.org/moin/IntegratingPythonWithOtherLanguages)可以用来实现Python与其他流行语言之间低级别的互操作。但是如果你处于一种[奇怪的情况](http://stackoverflow.com/questions/546160/what-is-erlang-written-in?answertab=votes#tab-top)，或使用一些复杂的传统软件，这将变得困难，甚至是不可能的。

### 想法：作为模块的容器

我们创建了[sidomo - 简单的Docker模块](https://github.com/deepgram/sidomo)*，这样，如果你让你奇怪的应用程序在任何linux环境上运行，那么你可以立即以零添加的形式从Python中调用它。

现在，大多数人使用Docker Daemon API来管理执行他们应用程序的容器。([Kubernetes](http://kubernetes.io/) / [Mesos](http://mesos.apache.org/)是这方面很好的例子)。Sidomo为容器开辟了一个全新的用例 —— 将奇怪的软件转换成在Python代码中无缝运行的漂亮且单纯的Python模块。

*并不是一个[AWS服务](https://www.expeditedssl.com/aws-in-plain-english)

## 如何使用sidomo

请确保你安装了Docker，并且运行了一个Docker守护进程。如果你不确定是否是这样，那么运行`docker ps`，然后看看是否获得"CONTAINER ID ..."输出。如果你不确定如何正确设置Docker，那么你可以看看[这个链接](https://docs.docker.com/engine/installation/)或者[搜索这里](https://www.google.com/search?q=install+docker)来查找对应的方法。

### 设置Sidomo: 单行方式

你可以使用pip直接从git仓库中安装sidomo。只需在你的shell中运行下面这个命令：
```sh
pip install -e 'git+https://github.com/deepgram/sidomo.git#egg=sidomo' 
```

### 例子：一个简单的Hello  World

这将从Ubuntu基本镜像中启动一个容器，运行`echo hello from`，然后运行`echo the other side`，然后从该过程中打印输出行。要为这个例子做准备，你需要使用一个shell命令将Ubuntu镜像拉到你的机器上。

###### shell
```sh
# Get the latest Ubuntu image
docker pull ubuntu
```

###### Python
```py
from sidomo import Container

with Container('ubuntu') as c:  
    for line in c.run('bash -c "echo hello from; echo the other side;"'):
        print(line)
```

### 例子：使用sidomo处理FFMPEG 

现在，让我们用sidomo实际做一些有用的东西。[FFMPEG](https://www.ffmpeg.org/)是一个比较复杂的软件，对于大多数用途，它可以有效地操纵媒体文件，但是，它不容易在不同平台上进行一致的安装，并且没有最新的Python绑定它。使用Sidomo，你可以用Docker拉取FFMPEG，并且轻松地从Python运行它。

###### shell
```py
docker pull cellofellow/ffmpeg 
```

###### Python

下面的例子将从URL中抓取音频，对其进行转码，并打印调试信息来证明其有效。该进程的标准输出（原始音频输出）被禁用了，因为我们只希望看到调试信息。
```py
from sidomo import Container  
url = 'http://www2.warwick.ac.uk/fac/soc/sociology/staff/sfuller/media/audio/9_minutes_on_epistemology.mp3'  
with Container(  
    'cellofellow/ffmpeg',
    stdout=False
) as c:
    for line in c.run(
        'bash -c \"\
            wget -nv -O tmp.unconverted %s;\
            ffmpeg -i tmp.unconverted -f wav -acodec pcm_s16le -ac 1 -ar 16000 tmp.wav;\
            cat tmp.wav\
        \"\
        ' % url
    ):
        print line
```

如果你确实想在这个进程中保存转码后的音频，那么你可以用`stderr=False`替换`stdout=False`行，然后确保将容器进程中的每一行输出（原始音频数据）写到一个文件中。

## 乐享未来

如果你必须为一些复杂的软件编写Python绑定，那么可以考虑容器化软件来代替。使用sidomo把一个容器化的应用程序转换成一个Python模块是不费劲的，并且是干净的。

如果你发现对于那些不存在合适的绑定的进程，你自己经常使用子进程与代码进行交互，那么容器化这些过程可能会让一些事情变得更简单。

![](http://www.adweek.com/socialtimes/files/2014/01/twitter-nesting-dolls.jpg)

如果你在这样的一个Python应用中使用sidomo —— 它以开发了复杂依赖告终 —— 那么你可能需要将它包装在自己的容器中，并在外面从一个具有较少依赖的应用程序调用它。Sidomo也支持这样做，因为[docker支持嵌套容器](https://blog.docker.com/2013/09/docker-can-now-run-within-docker/)。你可以通过使用sidomo导入sidomo导入sidomo来做自己的软件俄罗斯套娃....

祝你好运！只要记住，你不能无限期地容器化复杂度。或者，你可以？

[在github上的Sidomo](https://github.com/deepgram/sidomo)

## 为什么我们做这个？

我们创建了DeepGram API，一个用于音频和视频的搜索引擎，它使得语音可搜索。DeepGram使用一个信号处理的复杂堆栈，统计和机器学习的软件协同工作，以提供一个无缝的“上传和搜索”体验。Sidomo让我们迅速地容器化挑剔的软件，并将其与Python，我们的胶水，整合在一起。

你可以在[www.deepgram.com](http://www.deepgram.com)上获得一个带有API访问权限的帐户。该帐户每月可以进行40小时免费上传（这个很长的路要走！）。来看看吧，让我们知道[你在想些什么](http://www.deepgram.com/contact)。
