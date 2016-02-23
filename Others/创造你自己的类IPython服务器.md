原文：[Creating your own IPython-like server](http://blog.claymcleod.io/2016/02/18/Creating-your-own-IPython-like-server/)

---

最近我一直不断在使用Jupyter（前身是IPython）notebook进行可重复的研​​究，而且，我一直想知道它是如何工作的。此外，我需要一些IPython默认情况下不包括的自定义功能。我并不是要扩展IPython，而是决定尝试构建自己简单的IPython内核，它可以在我的GPU处理机运行的远程服务器上工作。我不会担心安全或并发性，因为我将是唯一有权访问该服务器的人。这次练习应该会给大家介绍，在Python中，基于服务器的编码环境是如何工作的。

因为这不是一个生产服务器，所以Flask非常适合我们的需要。让我们先从一个什么也不做的简单Flask服务器开始。我会包含一些在以后需要的导入。
```py
import sys
import traceback
from cStringIO import StringIO
from flask import Flask, jsonify, request

app = Flask(__name__)

if __name__ == "__main__":
    app.run()
```

## [](#Executing-Code "Executing Code")执行代码

实际上这里只介绍一个神奇的代码段：Python如何接收代码字符串，执行它，然后返回输出？让我们先从新方法开始。

你可以使用`exec()`命令执行任何Python语句。我将创建一个Flask终端，它接受一个名为‘code’的POST参数，根据新行分割命令，然后在序列中运行每个命令。下面是代码。

```py
import sys
import traceback
from cStringIO import StringIO
from flask import Flask, jsonify, request

app = Flask(__name__)

@app.route("/", methods=['POST'])
def kernel():
    code_lns = request.form['code'].split('\\n')
    for line in code_lns: exec(line)
    return 'Success'

if __name__ == "__main__":
    app.run()
```

很容易！你已经有了一个15行代码（包括未使用的导入和正确的间隔）的最小的执行Python的服务器。为了测试这一点，我使用[POSTMAN客户端](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop)，通过POST请求来访问我的本地服务器。

设置POST参数‘code’为`print('hello world')`，然后发送POST请求到`http:localhost:5000/`，就像下面的图片一样，然后点击‘Send’。正如预期的那样，服务器读取代码，打印出‘Hello world’，然后退出。

![output1.png](http://blog.claymcleod.io/2016/02/18/Creating-your-own-IPython-like-server/output1.png)

## [](#Redirecting-Output "Redirecting Output")重定向输出

这对我们并不是非常有用，虽然服务器成功接收并执行的代码，但是客户端只收到一个“Success”消息。理想的情况下，我们想要从程序执行到客户端重定向输出。为了实现这一目标，我们必须把被写到标准输出缓冲中的东西捕捉到一个字符串缓冲区中，然后返回这个字符串给客户端。经过一番研究，我确定这可以通过暂时重定向标准输出到一个StringIO缓冲区来做到，像这样：
```py
@app.route("/", methods=['POST'])
def kernel():
    code_lns = request.form['code'].split('\\n')

    # Store old stdout location
    old_stdout = sys.stdout

    # Redirect stdout to our string buffer
    sys.stdout = strstdout = StringIO()

    for line in code_lns:
        exec(line)

    # Reset stdout to its origin location
    sys.stdout = old_stdout

    # Get the value stored in the string buffer
    return strstdout.getvalue()
```

看着Postman客户端的输出，我们可以看到，如预期那样，服务器现在将标准输出传送回客户端。

![output2.png](http://blog.claymcleod.io/2016/02/18/Creating-your-own-IPython-like-server/output2.png)


**注**：以这种方式重定向标准输出将为所有客户端连接重定向输出。因此，如果有多个人同时运行代码，那么输出将会重叠。不要这样做。这就是为什么我说这不是一个可以投入生产的服务器。

## [](#Different-Environments "Different Environments")不同的环境

我们的实现的另一个主要问题是，一切都在相同的环境中执行。关于IPython的好处之一是，你可以同时在几个不同的notebook上工作，并且没有一个变量或功能重叠。这个概念并不存在我们的设计中：如果在同一时间进行有两个不同的想法，那么这两个脚本之间所有的变量都会被共享。

问题就出在`exec()`命令，也就是之前我提到的新方法。请记住，在Python中，环境（技术上来说，是Python中的命名空间）中的一切只是作为`__dict__`字段中的一个字典被存储（见[这篇文章](https://docs.python.org/2/reference/datamodel.html)以获取更多信息）。我们可以通过像这样做，从而在不同的环境中执行代码：

```py
env = {}
code = compile('j = 1', '<string>', 'exec')
exec code in env
```

在这些代码段已经执行之后，`env['j']`将保存值`1`。此外，能够在我们的代码中使用`env`中的任何变量。我们可以利用这种技术在多个不同的环境中运行代码。

首先，我们来介绍一些创建，删除和获取有关新的`environments`变量（包含一个给定的环境id的所有环境的字典的字典）信息的样板功能。

```py
environments = {}

@app.route('/env/create', methods=['POST'])
def create():
    env_id = request.form['id']
    if env_id not in environments:
        environments[env_id] = {}
    return jsonify(envs=environments.keys())

@app.route('/env/delete', methods=['POST'])
def delete():
    env_id = request.form['id']
    if env_id in environments:
        del environments[env_id]
    return jsonify(envs=environments.keys())

@app.route('/env/get', methods=['POST'])
def getenv():
    env_id = request.form['id']
    if env_id in environments:
        return jsonify(env=environments[env_id].keys())
    else:
        return jsonify(error='Environment does not exist!')
```


现在，如果我设置POST参数为`{id: 1}`，并发送POST请求到`http://localhost:5000/env/create`，那么服务器将为该环境id创建一个空的字典，然后将所有被创建的环境信息发送给我。同样，我可以删除环境信息或获取环境中所有可用信息。

将其与我们的代码执行相挂钩也是非常简单的。

```py
@app.route("/", methods=['POST'])
def kernel():
    env_id = request.form['id']
    if env_id not in environments:
        return jsonify(error='Kernel does not exist!')
    code_lns = request.form['code'].split('\\n')
    old_stdout = sys.stdout
    sys.stdout = strstdout = StringIO()
    for line in code_lns:
        code = compile(line, '<string>', 'exec')
        exec code in environments[env_id]
    sys.stdout = old_stdout
    return jsonify(message=strstdout.getvalue())
```

需要注意的是，现在，我已经在给定的环境id下小心翼翼的执行每个代码语句了。

## [](#Error-Handling "Error Handling")错误处理

在我们的代码中，还有最后一个有目共睹的bug：当发生错误时，我们的设计会惨败。如果你目前在本教程中打错任何东西，如发送`prnt('hi')`到服务器上，你会收到一个严肃的500错误，表示我们的服务器没有额外的信息返回。理想情况下，比起如此不透明的响应，我们宁愿在客户端收到堆栈跟踪！

添加错误处理到我们的服务器正如捕捉错误并打印堆栈跟踪到标准输出一样简单。我们可以通过调用`traceback.format_exc()`获得堆栈跟踪。因为我想使它明显表示已经发生了错 ​​误，所以我会观察错误是否发生，然后在‘error’键下发送回堆栈跟踪。

我们可以稍微修改我们的内核方法来得到我们需要的功能。

```py
@app.route("/", methods=['POST'])
def kernel():
    error = False
    env_id = request.form['id']
    if env_id not in environments:
        return jsonify(error='Kernel does not exist!')
    code_lns = request.form['code'].split('\\n')
    old_stdout = sys.stdout
    sys.stdout = strstdout = StringIO()
    for line in code_lns:
        try:
            code = compile(line, '<string>', 'exec')
            exec code in environments[env_id]
        except:
            print(traceback.format_exc())
            error = True
    sys.stdout = old_stdout
    if error: return jsonify(error=strstdout.getvalue())
    else: return jsonify(message=strstdout.getvalue())
```

## [](#Final-Thoughts "Final Thoughts")最后的思考

总而言之，创造我们自己的类似IPython的服务器还有很长的路要走。编写了一个简单的前端与基于JSON的服务器来回交互已经超出了我想在这里做的范围，但它肯定是不难的。

至于并发和安全问题，这些许多可以通过使用Docker容器](https://www.docker.com/)来解决，它允许沙箱，并且可以在客户端连接时进行加速或分解。这个沙盒也可以解决标准输出重定向问题。
 
下面是最终的代码。假如要我自己说，对于一个全功能的，优雅的，基于会话的Python内核，52行的代码是不是太寒酸了。如果你有任何关于如何简化/提高代码的其他的想法，请让我知道。
```py
import sys
import traceback
from cStringIO import StringIO
from flask import Flask, jsonify, request

app = Flask(__name__)
environments = {}

@app.route('/env/create', methods=['POST'])
def create():
    kernel_id = request.form['id']
    if kernel_id not in environments:
        environments[kernel_id] = {}
    return jsonify(envs=environments.keys())

@app.route('/env/delete', methods=['POST'])
def delete():
    kernel_id = request.form['id']
    if kernel_id in environments:
        del environments[kernel_id]
    return jsonify(envs=environments.keys())

@app.route('/env/get', methods=['POST'])
def getenv():
    kernel_id = request.form['id']
    if kernel_id in environments:
        return jsonify(env=environments[kernel_id].keys())
    else:
        return jsonify(error='Environment does not exist!')

@app.route("/", methods=['POST'])
def kernel():
    error = False
    kernel_id = request.form['id']
    if kernel_id not in environments:
        return jsonify(error='Kernel does not exist!')
    code_lns = request.form['code'].split('\\n')
    old_stdout = sys.stdout
    sys.stdout = strstdout = StringIO()
    for line in code_lns:
        try:
            code = compile(line, '<string>', 'exec')
            exec code in environments[kernel_id]
        except:
            print(traceback.format_exc())
            error = True
    sys.stdout = old_stdout
    if error: return jsonify(error=strstdout.getvalue())
    else: return jsonify(message=strstdout.getvalue())

if __name__ == "__main__":
    app.run()
```