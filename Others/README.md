# Others
其他一些零零散散的文章

## 说明
- [使用图像特征的库存图像相似性(程序是如何比我更时尚的)](./程序是如何比我更时尚的.md) 

    借助Python库indico获得图像特征向量，利用欧几里得距离计算相似性，随着样本的增加，会使得匹配搜寻的准确度越来越高。那么，就让我们看看怎样让程序比我更时尚吧~

- [如何在Python中使用Twilio Lookup API验证电话号码](./如何在Python中使用Twilio Lookup API验证电话号码.md)
    
    使用Twilio Lookup来对电话号码进行常规操作，例如验证有效性，获取运营商信息等。有更多更好的想法，不要忘记联系原作者进行分享~~

- 用Python玩转Worcester Wave恒温器

    * [第一部分](./用Python玩转Worcester Wave恒温器-第一部分.md)

        智能家居盛行的现在，你也可以控制自己的家电。如果你的电器支持移动app控制，那么不知道如何踏出hack the life的第一步，可以看看这篇文章，或许你可以有一种新思路……

    * [第二部分](./用Python玩转Worcester Wave恒温器-第二部分.md)

        紧接着第一部分，我们看看如何用starttls-mitm进行合法的中间人攻击以破解app和服务器之间加密的通信，看看如何使用反编译器反编译apk以获得消息中的信息加密算法以进行逆向，最终获得完整的明文交互信息……

    * [第三部分](./用Python玩转Worcester Wave恒温器-第三部分.md)
        
        收尾章节。我们可以看到如何利用破解出来的信息达到实际的控制。并且，作者还在Github上分享了他的源代码！

- [psutil 4.0.0以及如何获得Python中“真正的”进程内存和环境](./psutil 4.0.0以及如何获得Python中“真正的”进程内存和环境.md) 
    
    描述如何用psutil 4.0.0获得“真正的”进程内存度量以及进程环境变量的一些信息。

- [Python依赖关系分析](./Python依赖关系分析.md) 
 
    通过对pypi上包的依赖关系进行计算，使用“节点核心”，PageRank算法和“中间中心”分析包的重要性，分析开发社区的重要性以及度分布，从而对包的依赖关系进行详细的分析……

- [创造你自己的类IPython服务器](./创造你自己的类IPython服务器.md) 
    介绍如何用很少的代码，基于Flask实现一个类似于IPython notebook的服务器，其中谈到了用exec命令执行Python语句，巧用重定向实现将结果显示在客户端上，并且利用python中环境的存储方式解决不同环境上的变量/功能重叠问题。最后，还提到了错误处理机制。

- [为部署Python web应用程序构建一个更好的用户体验](./为部署Python web应用程序构建一个更好的用户体验.md)

    介绍使用warpdrive简单快捷的在不同的WSGI上部署web应用以及一些关于改进部署体验的做法

- [在Python中导入一个Docker容器](./在Python中导入一个Docker容器.md)

    介绍了一个工具Sidomo，它可以将奇怪的软件转换成在Python代码中无缝运行的漂亮且单纯的Python模块。

- [好吧，你发布了一个损坏的包到PyPI上。那么你现在要怎么办？](./好吧，你发布了一个损坏的包到PyPI上。那么你现在要怎么办？.md)
    
    不小心发布了一个受损的包到PyPI上，该怎么办？本文给出了三个步骤来处理这种任何人都有可能发生的问题。

- [Python中Meta类习语的起源](./Python中Meta类习语的起源.md)

    Django的模型中有一个Meta内部类，可以在很多其他Python API中发现它的使用。本文探索了此Meta内部类的来龙去脉……

- [如何使用Python和Pandas处理大量的JSON数据集](./如何使用Python和Pandas处理大量的JSON数据集.md)

    如何处理大量的（指无法一次性存入内存）JSON数据集呢？本文给出了一个第三方库ijson来迭代的处理此问题。同时，通过一个简单的交通管制数据的分析，向我们娓娓道来如何使用Pandas处理分析可视化数据……

- [复合构建器模式(Composite Builder Pattern)，一个声明式编程的例子](./复合构建器模式(Composite Builder Pattern)，一个声明式编程的例子)
    
    针对于那些属性来自于多个源的复杂对象来说，本文给出了一个例子，提出复合构建器模式以方便编程。

- [Python Mock：简单介绍 —— 第一部分](./Python Mock：简单介绍 —— 第一部分.md)
    
    本文带你走入Python mock，这个用于测试（TDD方法）的库……

- [将Python用于地理空间数据处理](./将Python用于地理空间数据处理.md)
    
    本文带你感受一下，如何将Python这个可爱的语言用于地理空间数据处理……

- [使用Python和Excel进行交互式数据分析](./使用Python和Excel进行交互式数据分析.md)

    想要一个调用你自定义的Python代码的Excel工作表吗？或许xlwings可以帮助你……

- [RPython的魔力](./RPython的魔力.md)

    RPython是个翻译器，一个编译时运行的翻译器。本文关注与RPython本身，主要说明了其特性及相关错误。

- Henry Kupty的函数式编程扫盲系列

    - [了解函数式编程背后的属性：单子(Monad)](./了解函数式编程背后的属性：单子(Monad).md)
    
        本文讲解了函数式编程中的单子，及其三大法则。

- [在Python中使用Behave来开始行为测试](./在Python中使用Behave来开始行为测试.md)

    本文通过讲述一个21点的例子，借助Python中的Behave库，带你体验什么是BDD，行为驱动开发。

- [使用gdb调试CPython进程](./使用gdb调试CPython进程.md)

    想在不重启应用的情况下调试CPython应用？此时pdb可无用武之地哦~ 所以，试试gdb吧！

- [使用Python Newspaper构建Read It Later应用](./使用Python Newspaper构建Read It Later应用.md)

    Python + Tweet + Newspaper + Flask ≈ Pocket？是哒，你可以看看怎么来做到。P.S. 表示对博主的2016年的52个技术系列很感兴趣呢，已收藏~~

- [Python lambda的源代码](./Python lambda的源代码.md)

    这是一篇关于如何显示lambda源代码的hack……

- [使用Python构建一个（半）自动无人机](./使用Python构建一个（半）自动无人机.md)

    无人机盛行的当下，想看看如何用node.js和python对其进行编程，做一些好玩的事情吗？看看作者如何玩玩这一big toy吧！（搞得我也想玩了，但是无人机还是有点贵呢……）

- [如何在Python中创建绿噪音](./如何在Python中创建绿噪音.md)
