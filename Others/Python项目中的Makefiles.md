原文：[Makefiles in python projects —— Do you need such thing as makefile in python projects?](http://krzysztofzuraw.com/blog/2016/makefiles-in-python-projects.html)

---

**当我加入我当前的公司时，我在他们的git仓库中看到了奇怪的文件。没有任何Python代码使用它。它仅仅是存在于项目的主目录下。我问我的同事，这个文件是用来干嘛的？他们告诉我——它让你的生活更轻松。这就是为什么今天我要写写这个文件 - Makefile。**

目录：

  * makefile是什么，以及它的典型用途
  * 具体到Python，你可以把什么放到makefile中
  * 通过在Python项目中使用makefile，你可以获得什么好处

## makefile是什么，以及它的典型用途

根据这个[教程](http://www.cs.colby.edu/maxwell/courses/tutorials/maketutor/):

> makefile是组织代码编译的一种简单方法。

通常情况下，它们用于编写C程序，来减轻代码可以作为程序使用前所需要做的所有东西。你可以指定规则，告诉[make](https://www.gnu.org/software/make/)如何编译你的程序。用于C代码的简单的makefile如下：

```shell
    helloword: helloword.c
        gcc -o hellword hellword.c -I.
    
```

然后运行：

```shell

    $ make helloword
    
```

用gcc来编译C。

这怎么跟Python扯在一起了？Python这门编程语言在调用的时候才编译，因此不需要任何makefile。正如我在开头所说的，在Python项目中，使用makefile，你可以让你的生活变得轻松，并且节省大量的按键操作。

## 具体到Python，你可以把什么放到makefile中

构建Python包后，你有没有想过清理你项目中的`.pyc`文件，或者消除伪迹？或者也许你想要运行覆盖率测试？使用pep8, lint或者isort？或许在docker容器中运行应用，最终写出那些对你的屏幕来说太长的命令？

这就是makefile的用武之地了。你可以将一切放在同一个地方，然后只使用`make clean`来清理不必要的文件，或者使用`make tests`来测试你的应用。

让我们先从我正在使用的makefile的一些例子开始：

```python

    HOST=127.0.0.1
    TEST_PATH=./
    
    clean-pyc:
        find . -name '*.pyc' -exec rm --force {} +
        find . -name '*.pyo' -exec rm --force {} +
        find . -name '*~' -exec rm --force  {} +
    
    clean-build:
        rm --force --recursive build/
        rm --force --recursive dist/
        rm --force --recursive *.egg-info
    
    isort:
        sh -c "isort --skip-glob=.tox --recursive . "
    
    lint:
        flake8 --exclude=.tox
    
    test: clean-pyc
        py.test --verbose --color=yes $(TEST_PATH)
    
    run:
        python manage.py runserver
    
    docker-run:
        docker build \
          --file=./Dockerfile \
          --tag=my_project ./
        docker run \
          --detach=false \
          --name=my_project \
          --publish=$(HOST):8080 \
          my_project
    
```

开头，我为每一个命令添加了两个变量`HOST`和`TEST_PATH`，以便使用它们。规则`clean-pyc`查找所有以`*.pyc`, `*.pyo`或者`*~`结尾的文件，然后删除它们。命令尾部的+号是用于`-exec
command {}`，意味着命令的调用总数会比匹配的文件的数量要少得多。

下一个`clean-build`是用来提出构建伪迹。在`isort`中，shell根据何时的属性执行isort命令，`-c`标记用来从字符串而不是从标准输入读取命令。`lint`和`run`工作在相同的模式上。在`test`中，我添加了在实际的测试之前执行的额外的规则 - `clean-pyc`。最后的`docker-run`构建和运行docker。

你想要添加的额外的东西是一些称为`PHONY`的东西。默认情况下，makefile在文件上进行操作，因此，如果有个名为`clean-
pyc`的文件，那么它会尝试使用它而不是使用命令。要避免这个，则在你的makefile文件开头使用`PHONY`：

```python

    .PHONY: clean-pyc clean-build
    
```

我还喜欢让我的makefile有帮助函数，因此我把这些放在里面的某些地方：

```python

    help:
        @echo "    clean-pyc"
        @echo "        Remove python artifacts."
        @echo "    clean-build"
        @echo "        Remove build artifacts."
        @echo "    isort"
        @echo "        Sort import statements."
        @echo "    lint"
        @echo "        Check style with flake8."
        @echo "    test"
        @echo "        Run py.test"
        @echo '    run'
        @echo '        Run the `my_project` service on your local machine.'
        @echo '    docker-run'
        @echo '        Build and run the `my_project` service in a Docker container.'
    
```

每个`echo`之前都有一个`@`，因为默认情况下，`make`载执行前把每一行都打印到控制台。`At`符号抑制这种行为，而当传递行给shell之前，`@`被丢弃。

但是，如果我想使用makefile，让我的应用程序运行在不同的主机和端口上呢？很简单，只需添加：

```python

    run:
        python manage.py runserver --host $(HOST) --port $(PORT)
    
```

接着，你可以简单地运行：

```python

    $ make run HOST=127.0.0.1 PORT=8000
    
```

最后注意，makefile中的缩进必须使用TAB来完成，而不是空格。

## 通过在Python项目中使用makefile，你可以获得什么好处

正如你所见，在Python项目中使用makefile可以带来很多好东西。如果你已经厌倦了编写复杂的shell命令 —— 那么把它们放在makefile中的一个规则下。想要其他人很容易地运行对项目的测试吗？把pytest调用放在makefile中。创意是无止境的。

在你的项目中使用makefile吗？你觉得它有用，或者没用吗？你把什么其他的东西放在项目中呢？请把它写到评论中吧！

