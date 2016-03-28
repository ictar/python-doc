原文：[Python reflection: how to list modules and inspect functions](http://tomassetti.me/python-reflection-how-to-list-modules-and-inspect-functions/)

---

最近，我一直在玩一些关于对Python应用静态分析，以及在Jetbrains MPS中构建一个Python编辑器的想法。

要做到这些，我需要先构建Python代码模型。最近，我们已经知道了如何[解析Python代码](http://tomassetti.me/parsing-any-language-in-java-in-5-minutes-using-antlr-for-example-python/)，然而，我们仍然需要考虑我们的代码所使用的所有的包。其中一些是内置的，或者通过C扩展实现的。这意味着，我们没有关于它们的Python代码。在这篇文章中，我查看了检索所有模块的列表，然后检查其内容。

MP内部（以及从Java代码中）调用那些脚本。然而，这是未来一篇文章的主题了。

## 模块列举

如果你知道怎么做，那么列举顶层模块是相当容易的。这个脚本列举出所有顶层模块：
```py
import pkgutil
 
for p in pkgutil.iter_modules():
    print(p[1])
```

现在，我们需要在模块内部查找子模块。出于性能的考虑，我想要只在需要的时候进行这项工作：
```py
import pkgutil
import sys

def explore_package(module_name):    
    loader = pkgutil.get_loader(module_name)
    for sub_module in pkgutil.walk_packages([loader.filename]):
        _, sub_module_name, _ = sub_module
        qname = module_name + "." + sub_module_name
        print(qname)
        explore_package(qname)

explore_package(sys.argv[1])
```

例如，对于_xml_，我得到了：
```py
xml.dom
xml.dom.NodeFilter
xml.dom.domreg
xml.dom.expatbuilder
xml.dom.minicompat
xml.dom.minidom
xml.dom.pulldom
xml.dom.xmlbuilder
xml.etree
xml.etree.ElementInclude
xml.etree.ElementPath
xml.etree.ElementTree
xml.etree.cElementTree
xml.parsers
xml.parsers.expat
xml.sax
xml.sax._exceptions
xml.sax.expatreader
xml.sax.handler
xml.sax.saxutils
xml.sax.xmlreader
```

## 检查模块内容，并且识别函数

现在，给定一个模块，我需要列举它所有的内容。我可以根据名字加载模块，然后遍历它，打印出所找到的元素的信息。

我想区分类，子模块（这个我现在会忽略），函数和简单的值。

内置函数需要区别对待：要访问它们的信息，我需要解析它们的文档。不酷，一点儿也不酷。
```py
import sys
import inspect

def describe_builtin(obj):
    """ Describe a builtin function """
    # Built-in functions cannot be inspected by
    # inspect.getargspec. We have to try and parse
    # the __doc__ attribute of the function.
    docstr = obj.__doc__
    args = ''
    if docstr:
        items = docstr.split('\n')
        if items:
            func_descr = items[0]
            s = func_descr.replace(obj.__name__,'')
            idx1 = s.find('(')
            idx2 = s.find(')',idx1)
            if idx1 != -1 and idx2 != -1 and (idx2>idx1+1):
                args = s[idx1+1:idx2]
    return args

package_name = sys.argv[1].strip()
mymodule = __import__(package_name, fromlist=['foo'])

for element_name in dir(mymodule):
    element = getattr(mymodule, element_name)
    if inspect.isclass(element):
        print("class %s" % element_name)
    elif inspect.ismodule(element):
        pass        
    elif hasattr(element, '__call__'):
        if inspect.isbuiltin(element):
            sys.stdout.write("builtin_function %s" % element_name)
            data = describe_builtin(element)
            data = data.replace("[", " [")
            data = data.replace("  [", " [")
            data = data.replace(" [, ", " [")
            sys.stdout.write(data.replace(", ", " "))
            print("")
        else:                    
            try:
                data = inspect.getargspec(element)
                sys.stdout.write("function %s" % element_name)
                for a in data.args:
                    sys.stdout.write(" ")
                    sys.stdout.write(a)
                if data.varargs:
                    sys.stdout.write(" *")
                    sys.stdout.write(data.varargs)
                print("")
            except:
                pass
    else:
        print("value %s" % element_name)
```

下面是对于_os_模块，我得到的东东：
```py
value EX_CANTCREAT
value EX_CONFIG
value EX_DATAERR
value EX_IOERR
value EX_NOHOST
value EX_NOINPUT
value EX_NOPERM
value EX_NOUSER
value EX_OK
value EX_OSERR
value EX_OSFILE
value EX_PROTOCOL
value EX_SOFTWARE
value EX_TEMPFAIL
value EX_UNAVAILABLE
value EX_USAGE
value F_OK
value NGROUPS_MAX
value O_APPEND
value O_ASYNC
value O_CREAT
value O_DIRECT
value O_DIRECTORY
value O_DSYNC
value O_EXCL
value O_LARGEFILE
value O_NDELAY
value O_NOATIME
value O_NOCTTY
value O_NOFOLLOW
value O_NONBLOCK
value O_RDONLY
value O_RDWR
value O_RSYNC
value O_SYNC
value O_TRUNC
value O_WRONLY
value P_NOWAIT
value P_NOWAITO
value P_WAIT
value R_OK
value SEEK_CUR
value SEEK_END
value SEEK_SET
value ST_APPEND
value ST_MANDLOCK
value ST_NOATIME
value ST_NODEV
value ST_NODIRATIME
value ST_NOEXEC
value ST_NOSUID
value ST_RDONLY
value ST_RELATIME
value ST_SYNCHRONOUS
value ST_WRITE
value TMP_MAX
value WCONTINUED
builtin_function WCOREDUMPstatus
builtin_function WEXITSTATUSstatus
builtin_function WIFCONTINUEDstatus
builtin_function WIFEXITEDstatus
builtin_function WIFSIGNALEDstatus
builtin_function WIFSTOPPEDstatus
value WNOHANG
builtin_function WSTOPSIGstatus
builtin_function WTERMSIGstatus
value WUNTRACED
value W_OK
value X_OK
class _Environ
value __all__
value __builtins__
value __doc__
value __file__
value __name__
value __package__
function _execvpe file args env
function _exists name
builtin_function _exitstatus
function _get_exports_list module
function _make_stat_result tup dict
function _make_statvfs_result tup dict
function _pickle_stat_result sr
function _pickle_statvfs_result sr
function _spawnvef mode file args env func
builtin_function abort
builtin_function accesspath mode
value altsep
builtin_function chdirpath
builtin_function chmodpath mode
builtin_function chownpath uid gid
builtin_function chrootpath
builtin_function closefd
builtin_function closerangefd_low fd_high
builtin_function confstrname
value confstr_names
builtin_function ctermid
value curdir
value defpath
value devnull
builtin_function dupfd
builtin_function dup2old_fd new_fd
value environ
class error
function execl file *args
function execle file *args
function execlp file *args
function execlpe file *args
builtin_function execvpath args
builtin_function execvepath args env
function execvp file args
function execvpe file args env
value extsep
builtin_function fchdirfildes
builtin_function fchmodfd mode
builtin_function fchownfd uid gid
builtin_function fdatasyncfildes
builtin_function fdopenfd [mode='r' [bufsize]]
builtin_function fork
builtin_function forkpty
builtin_function fpathconffd name
builtin_function fstatfd
builtin_function fstatvfsfd
builtin_function fsyncfildes
builtin_function ftruncatefd length
builtin_function getcwd
builtin_function getcwdu
builtin_function getegid
function getenv key default
builtin_function geteuid
builtin_function getgid
builtin_function getgroups
builtin_function getloadavg
builtin_function getlogin
builtin_function getpgidpid
builtin_function getpgrp
builtin_function getpid
builtin_function getppid
builtin_function getresgid
builtin_function getresuid
builtin_function getsidpid
builtin_function getuid
builtin_function initgroupsusername gid
builtin_function isattyfd
builtin_function killpid sig
builtin_function killpgpgid sig
builtin_function lchownpath uid gid
value linesep
builtin_function linksrc dst
builtin_function listdirpath
builtin_function lseekfd pos how
builtin_function lstatpath
builtin_function majordevice
builtin_function makedevmajor minor
function makedirs name mode
builtin_function minordevice
builtin_function mkdirpath [mode=0777]
builtin_function mkfifofilename [mode=0666]
builtin_function mknodfilename [mode=0600 device]
value name
builtin_function niceinc
builtin_function openfilename flag [mode=0777]
builtin_function openpty
value pardir
builtin_function pathconfpath name
value pathconf_names
value pathsep
builtin_function pipe
builtin_function popencommand [mode='r' [bufsize]]
function popen2 cmd mode bufsize
function popen3 cmd mode bufsize
function popen4 cmd mode bufsize
builtin_function putenvkey value
builtin_function readfd buffersize
builtin_function readlinkpath
builtin_function removepath
function removedirs name
builtin_function renameold new
function renames old new
builtin_function rmdirpath
value sep
builtin_function setegidgid
builtin_function seteuiduid
builtin_function setgidgid
builtin_function setgroupslist
builtin_function setpgidpid pgrp
builtin_function setpgrp
builtin_function setregidrgid egid
builtin_function setresgidrgid egid sgid
builtin_function setresuidruid euid suid
builtin_function setreuidruid euid
builtin_function setsid
builtin_function setuiduid
function spawnl mode file *args
function spawnle mode file *args
function spawnlp mode file *args
function spawnlpe mode file *args
function spawnv mode file args
function spawnve mode file args env
function spawnvp mode file args
function spawnvpe mode file args env
builtin_function statpath
builtin_function stat_float_times [newval]
class stat_result
builtin_function statvfspath
class statvfs_result
builtin_function strerrorcode
builtin_function symlinksrc dst
builtin_function sysconfname
value sysconf_names
builtin_function systemcommand
builtin_function tcgetpgrpfd
builtin_function tcsetpgrpfd pgid
builtin_function tempnam [dir [prefix]]
builtin_function times
builtin_function tmpfile
builtin_function tmpnam
builtin_function ttynamefd
builtin_function umasknew_mask
builtin_function uname
builtin_function unlinkpath
builtin_function unsetenvkey
builtin_function urandomn
builtin_function utimepath (atime mtime
builtin_function wait
builtin_function wait3options
builtin_function wait4pid options
builtin_function waitpidpid options
function walk top topdown onerror followlinks
builtin_function writefd strin
```

当然，对于函数，我想要构建其接口的一个模型（它接收什么样的参数，其中哪些是可选的，哪些是可变参数，等等）。这里，我们有需要的信息，因此只是将其转换成可表示的形式的事。

## 结论

我仍然需要构建导入类的模型，但是我开始有了可以导入到我的Python代码中的元素的一个像模像样的模型。这将允许我轻松验证哪些导入语句是合法的。当然，这可以在结合virtualenv和必要文件后使用：给定必要条件列表，我会在virtualenv中安装它们，然后构建在那个virtualenv中可用的模块的模型。然后，我可以静态地验证在其上下文中，哪些导入有效。

