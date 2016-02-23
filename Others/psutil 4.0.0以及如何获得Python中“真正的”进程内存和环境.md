原文：[psutil 4.0.0 and how to get "real" process memory and environ in Python](http://grodola.blogspot.com/2016/02/psutil-4-real-process-memory-and-environ.html)

---
现在，psutil 4.0.0已经出来了，它带来了关于进程内存度量的一些有趣的消息。我将开门见山说明有什么新东西。

# "真正的"进程内存信息

确定一个过程真的使用多少内存并不是一件容易的事（见[这个](https://lwn.net/Articles/230975/) 和 [这个](http://bmaurer.blogspot.it/2006/03/memory-usage-with-smaps.html)）。RSS（驻留集大小），这是大多数人平时靠的指标，它其实是会误导，因为它同时包含了进程独一无二的内存和与其他进程共享的内存。在分析方面更有趣的是，如果该进程即刻被终止，将释放的内存。在Linux的世界中，这被称为USS（唯一集大小），并且这是在psutil 4.0.0引入（不仅适用于Linux，也适用于Windows和OSX）的主要功能。

# USS内存

USS（唯一集大小）是一个进程特有的内存，如果这个过程被终止，它将即刻被释放。在Linux上，这可以通过分析在`/proc/pid/smaps`中的所有“私有”块进行确定。Firefox团队进一步对其进行推动，并且也成功地在[OSX和Windows](https://dxr.mozilla.org/mozilla-central/rev/aa90f482e16db77cdb7dea84564ea1cbd8f7f6b3/xpcom/base/nsMemoryReporterManager.cpp)上做了同样的事。这是很棒的。psutil新版本现在能够做同样的事：
```py
>>> psutil.Process().memory_full_info()
pfullmem(rss=101990, vms=521888, shared=38804, text=28200, lib=0, data=59672, dirty=0, 
         uss=81623, pss=91788, swap=0)
```

# PSS和swap

在Linux中，有两个也可以通过`/proc/pid/smaps`确定的额外指标：PSS和swap。PSS，又名“比例设置大小”，表示与其他进程共享的内存大小，它以这种方式进行计算：**总量最终在共享的进程之间平分**。也就是说，如果一个进程有10 MB都归自己（USS），并与另一个进程共享10 MB，那么其PSS将是15 MB。“swap”很简单，就是已经被交换到磁盘的内存量。使用[memory_full_info()](https://pythonhosted.org/psutil/#psutil.Process.memory_full_info)可以实现一个[像这样](https://github.com/giampaolo/psutil/blob/master/scripts/procsmem.py)的工具，类似于Linux上的[smem](https://www.selenic.com/smem/)，它提供了根据“USS”排序的进程列表。有趣的是，注意RSS和USS的区别：
```sh
~/svn/psutil$ ./scripts/procsmem.py
PID     User    Cmdline                            USS     PSS    Swap     RSS
==============================================================================
...
3986    giampao /usr/bin/python3 /usr/bin/indi   15.3M   16.6M      0B   25.6M
3906    giampao /usr/lib/ibus/ibus-ui-gtk3       17.6M   18.1M      0B   26.7M
3991    giampao python /usr/bin/hp-systray -x    19.0M   23.3M      0B   40.7M
3830    giampao /usr/bin/ibus-daemon --daemoni   19.0M   19.0M      0B   21.4M
20529   giampao /opt/sublime_text/plugin_host    19.9M   20.1M      0B   22.0M
3990    giampao nautilus -n                      20.6M   29.9M      0B   50.2M
3898    giampao /usr/lib/unity/unity-panel-ser   27.1M   27.9M      0B   37.7M
4176    giampao /usr/lib/evolution/evolution-c   35.7M   36.2M      0B   41.5M
20712   giampao /usr/bin/python -B /home/giamp   45.6M   45.9M      0B   49.4M
3880    giampao /usr/lib/x86_64-linux-gnu/hud/   51.6M   52.7M      0B   61.3M
20513   giampao /opt/sublime_text/sublime_text   65.8M   73.0M      0B   87.9M
3976    giampao compiz                          115.0M  117.0M      0B  130.9M
32486   giampao skype                           145.1M  147.5M      0B  149.6M
```

# 实现

要获得这些值(USS, PSS和swap)，我们需要遍历珍格格进程地址空间。这通常需要更高的用户权限，并且比通过[Process.memory_info()](https://pythonhosted.org/psutil/#psutil.Process.memory_info)获得“正常”的内存度量慢得多，这可能就是为什么像`ps`和`top`之类的工具显示RSS/VMS而不是USS。非常感谢Mozilla团队，他们在[Windows和OSX](https://dxr.mozilla.org/mozilla-central/rev/aa90f482e16db77cdb7dea84564ea1cbd8f7f6b3/xpcom/base/nsMemoryReporterManager.cpp)上都考虑到了这些，同时感谢Eric Rahm，他为psutil把PR都放在一起了(见[#744](https://github.com/giampaolo/psutil/pull/744), [#745](https://github.com/giampaolo/psutil/pull/745)和[#746](https://github.com/giampaolo/psutil/pull/746))。对于你们那些不使用Python，但是像将代码移植到另一个语言上的人，下面是有趣的部分：

*   [Linux](https://github.com/giampaolo/psutil/blob/42b34049cf96e845b6423db91f991849a2f87578/psutil/_pslinux.py#L1026)
*   [OSX](https://github.com/giampaolo/psutil/blob/50fd31a4eaca3e24905b96d587fd08bcf313fc6b/psutil/_psutil_osx.c#L568)
*   [Windows](https://github.com/giampaolo/psutil/blob/50fd31a4eaca3e24905b96d587fd08bcf313fc6b/psutil/_psutil_windows.c#L811)


# 内存类型百分比

经过[重组进程内存API](https://github.com/giampaolo/psutil/pull/744#issuecomment-180054438)后，我决定添加一个新的`memtype`参数到[Process.memory_percent()](https://pythonhosted.org/psutil/#psutil.Process.memory_percent)中。与此，它现在可以将一个指定的内存类型（不仅仅是RSS）与总物理内存进行比较了。例如：
```py
>>> psutil.Process().memory_percent(memtype='pss')
0.06877466326787016
```

# 进程环境变量

psutil 4.0.0中第二大改进是确定[进程环境变量](https://pythonhosted.org/psutil/#psutil.Process.environ)的能力。这将开启进程识别和监控技术的有趣的可能。例如，有人会通过传递某些自定义环境变量来启动一个进程，然后遍历所有进程来找到感兴趣的那个进程（并弄清楚它是否正在运行）：
```py
import psutil
for p in psutil.process_iter():
    try:
        env = p.environ()
    except psutil.Error:
        pass
    else:
        if 'MYAPP' in env:
            ...
```

进程环境变量是一个[长期存在的问题](https://code.google.com/archive/p/psutil/issues/52)， (在2009年)因为Windows实现仅对当前进程有效，所以我放弃实现。Frank Benkstein [解决了这个问题](https://github.com/giampaolo/psutil/pull/747)，所以现在对于所有进程，都可以在Linux, Windows和OSX上确定进程变量（当然，你仍然可以碰到其他用户拥有的进程的 `AccessDenied`）：
```py
>>> import psutil
>>> from pprint import pprint as pp
>>> pp(psutil.Process().environ())
{...
 'CLUTTER_IM_MODULE': 'xim',
 'COLORTERM': 'gnome-terminal',
 'COMPIZ_BIN_PATH': '/usr/bin/',
 'HOME': '/home/giampaolo',
 'PWD': '/home/giampaolo/svn/psutil',
  }
>>>
```
必须指出的是，所得到的字典通常不反映进程开始之后所做的更改（例如`os.environ['MYAPP'] = '1'`）。同样，对于任何有兴趣在其他语言中这样做的人，这里是有趣的部分：

*   [Linux](https://github.com/giampaolo/psutil/blob/50fd31a4eaca3e24905b96d587fd08bcf313fc6b/psutil/_pslinux.py#L928)
*   [OSX](https://github.com/giampaolo/psutil/blob/50fd31a4eaca3e24905b96d587fd08bcf313fc6b/psutil/arch/osx/process_info.c#L241)
*   [Windows](https://github.com/giampaolo/psutil/pull/747)

# 扩展磁盘IO统计信息

[psutil.disk_io_counters()](https://pythonhosted.org/psutil/#psutil.disk_io_counters)已经被扩展为报告Linux和FreeBSD上的附加度量：

*   `busy_time`，这是进行实际I/O花费的时间(毫秒)。
*   `read_merged_count`和`write_merged_count`(只用于Linux)，这是合并读数和合并写数(见[iostats](https://www.kernel.org/doc/Documentation/iostats.txt)文档)

有了这些新的度量，就有可能有[实际磁盘利用率](https://github.com/giampaolo/psutil/issues/756)的一个更好的表示，类似于Linux上的`iostat`命令。


# OS常量

Given the increasing number of platform-specific metrics I added a new set of 由于特定于平台的度量数量的增加，我添加了一套新的[常量](https://pythonhosted.org/psutil/#psutil.POSIX)来快速区分你是在什么平台上：`psutil.LINUX`, `psutil.WINDOWS`等等。

# 主要bug修复

*   [#734](https://github.com/giampaolo/psutil/issues/734): 在Python 3上，对于[name()](https://pythonhosted.org/psutil/#psutil.Process.name), [cwd()](https://pythonhosted.org/psutil/#psutil.Process.cwd), [exe()](https://pythonhosted.org/psutil/#psutil.Process.exe), [cmdline()](https://pythonhosted.org/psutil/#psutil.Process.cmdline) 和[open_files()](https://pythonhosted.org/psutil/#psutil.Process.open_files)方法，无效的UTF-8数据不能正确地被处理，从而导致`UnicodeDecodeError`。这影响到所有平台。现在[surrogateescape](https://www.python.org/dev/peps/pep-0383/)错误处理成千被用作替换损坏的数据的解决方法。
*   [#761](https://github.com/giampaolo/psutil/issues/734): [Windows] [psutil.boot_time()](https://pythonhosted.org/psutil/#psutil.boot_time) 在49天后不会再变成0。
*   [#767](https://github.com/giampaolo/psutil/issues/767): [Linux] 在2.6内核上，disk_io_counters()可能会引发ValueError错误，而在2.4内核上可能会崩溃。
*   [#764](https://github.com/giampaolo/psutil/issues/764): 现在可以在NetBSD-6.X上编译psutil了。
*   [#704](https://github.com/giampaolo/psutil/issues/704): 现在可以在Solaris sparc上编译psutil了。

完整的bug修复列表在[这里](https://github.com/giampaolo/psutil/blob/master/HISTORY.rst)。

# 移植代码

4.0.0作为一个主要版本，我抓住机会（稍微的）修改/破坏某些API。

*   [Process.memory_info()](https://pythonhosted.org/psutil/#psutil.Process.memory_info)不再只返回一个`(rss, vms)`命名元组。相反，它会返回一个可变长度，基于平台修改的命名元组（虽然`rss`和`vms`总是存在，在Windows上也是）。基本上，它返回与老的[process_memory_info_ex()](https://pythonhosted.org/psutil/#psutil.Process.memory_info_ex)相同的结果。这不应该破坏你已有的代码，触发你正在使用`"rss, vms = p.memory_info()"`。
*   同时，[process_memory_info_ex()](https://pythonhosted.org/psutil/#psutil.Process.memory_info)现已启用。该方法作为[memory_info()](https://pythonhosted.org/psutil/#psutil.Process.memory_info)的别名仍然存在，它会发出一个弃用警告。
*   [psutil.disk_io_counters()](https://pythonhosted.org/psutil/#psutil.disk_io_counters)在Linux上返回两个额外的字段，在FreeBSD上返回一个额外的字段。
*   NetBSD和OpenBSD上的[psutil.disk_io_counters()](https://pythonhosted.org/psutil/#psutil.disk_io_counters)不再返回`write_count`和`read_count`度量，因为内核不再提供它们（取而代之，我们返回忙绿时间）。我也不希望这是一个大问题，因为OpenBSD支持是非常近的。

# 最后说明，以及找工作

好了，就是这些了。（译者：后面是原文作者的一堆找工作的信息，有兴趣的请到原文上查看，这里就不进行翻译了）

# 外部链接

*   [reddit](https://www.reddit.com/r/Python/comments/469p2c/psutil_400_real_process_memory_info_and_process/)
*   [hacker news](https://news.ycombinator.com/item?id=11119298)
