# Flask 中的按需请求记录

> 原文：<https://levelup.gitconnected.com/on-demand-request-logging-in-flask-a082cb99f2a>

![](img/c8a13cbde1a771acb67e4147f0ace019.png)

*原载于 2020 年 6 月 5 日*[*https://preslav . me*](https://preslav.me/2020/06/05/on-demand-request-logging-in-flask/)*。*

打印出适量的生产日志是一种艺术，有时甚至接近黑魔法的领域。从我构建软件的经验来看，大多数开发人员要么记录太多，要么根本不记录任何东西。没有足够的信息会使寻找 bug 变得更加困难。另一方面，太多的日志跟踪会妨碍代码的可读性，并且不一定会使一个人的职责变得更容易。

实际上，每个人想要的是**按需登录**。大多数编程环境已经通过单独记录错误来部分解决这个问题。然而，错误的行为并不总是会导致错误。另外，错误日志很少包含导致错误的特定执行上下文。

# 基本请求跟踪

如果能够手动打开日志记录，那就太好了，而且只是在某些情况下。在 web 应用的上下文中，*某些情况* == *某些请求*。对于标准的类似 Flask 的 Python Web 应用程序，这实际上非常容易实现。

我会用 Flask，因为它最容易演示。这个想法应该相对容易在其他框架中重用。

比方说，我们想在每次使用某个 URL 参数调用 Flask API 时写一个日志(姑且称之为`trace_id`)。可以做的最简单的事情是添加一个`@before_request`拦截器:

```
app = Flask(__name__)[@app](http://twitter.com/app).before_request
def request_tracer():
    if "trace_id" in request.args:
        # DO something, like log the request args, body, etc
        # Make sure to include the trace_id as well, so you can
        # search for it in the logs
```

这个函数将在每次请求前被调用。如果您现在将`trace_id=[RANDOM_ID_THAT_YOU_CAN_TRACE]`添加到您想要手动测试的请求中，或者添加到另一个服务所使用的请求中，那么您应该能够很容易地在日志中搜索到`RANDOM_ID_THAT_YOU_CAN_TRACE`。最好的部分是，打开或关闭它是一个使用/不使用 URL 参数的问题。

当然，上面的例子相当幼稚。这对任何人都没有多大用处，因为大多数错误发生在调用链的更深处。让我们举下面的例子:

# 稍微高级一点的请求跟踪器

```
app = Flask(__name__)[@app](http://twitter.com/app).route("/")
def index():
    name = request.args.get("name")
  name = layer1(name)
    return f"Hello, {name}"def layer1(name: str):
    return layer2(name)def layer2(name: str):
    return name.upper()
```

我们的 API 处理程序调用另一个函数，该函数返回另一个函数的结果。典型的生产应用程序要复杂得多，到处都是循环和条件逻辑。嵌套调用链的问题是，要一直进行条件跟踪，需要将它添加到每个函数中。

```
if "trace_id" in request.args:
    # log trace_id, inputs and outputs
```

这是非常不切实际的，会降低代码的可读性。

# 自定义跟踪装饰器

我能想到的最不显眼的选择是创建一个定制的装饰器。我们不会不修改我们的代码，但是在每个函数上有一个装饰器不会直接影响代码的可读性。这是我们上面的小应用程序的样子:

```
app = Flask(__name__)tracer = Tracer()[@app](http://twitter.com/app).route("/")
def index():
    name = request.args.get("name")
  # pass the tracer_id to the first function in your call chain
  name = layer1(name, trace_id=request.args.get('trace_id'))
    return f"Hello, {name}"[@tracer](http://twitter.com/tracer).trace
def layer1(name: str):
    return layer2(name)[@tracer](http://twitter.com/tracer).trace
def layer2(name: str):
    return name.upper()
```

这是跟踪装饰器的一个可能的实现:

```
class Tracer:
    def __init__(self) -> None:
        self.current_trace_id = Nonedef trace(self, f):
        [@wraps](http://twitter.com/wraps)(f)
        def wrapper(*args, **kwargs):
            trace_id = kwargs.pop("trace_id", None)
            if trace_id:
                if self.current_trace_id:
                    # Do you really want to raise an error?
                    # Perhaps, logging the concurrent access and
                    # silently moving on is better.
                    raise Exception("Concurrent tracing requests are not allowed!") # "open" a new request
                self.current_trace_id = trace_id
                print(f"Tracing ID: {self.current_trace_id} -               function call: {__name__}.{f.__name__} - Arguments: {args}")
                res = f(*args, **kwargs) # "close" the request, by simply clearing its state
                self.current_trace_id = None
                return res
            elif self.current_trace_id:
                print(f"Tracing ID: {self.current_trace_id} - function call: {__name__}.{f.__name__} - Arguments: {args}")
                return f(*args, **kwargs)
            else:
                return f(*args, **kwargs)return wrapper
```

请记住我的方法的明显局限性。`tracer`实例是全局的。将状态存储在全局变量中通常不是一个好主意，但是在某些情况下，您别无选择。

在 WSGI 应用程序中，全局范围通常意味着每个进程的*全局。传统上，WSGI 服务器会产生一定数量的 Python 进程，并在它们之间处理传入的请求。单个请求将作为单个流程的一部分端到端运行。如果是这种情况，那么由于进程隔离，我们的小装饰器会做得非常好。当线程或`async-await`开始发挥作用时，将装饰器的范围限定为手边的特定请求将变得很重要。*

在 Flask 的情况下，可以使用它的[请求范围](https://flask.palletsprojects.com/en/1.1.x/reqcontext/)，这将确保并发请求不会覆盖当前正在运行的请求的`trace_id`值。当然，使用特定于库的构造会使代码更难测试。它还需要对装饰器进行修改，以防您切换您选择的 Web 框架(幸运的是，大多数框架都内置了相同的请求范围概念)。

到最后，就没那么重要了。Python 的精神之一是编写简单的代码来完成大部分工作，而不是沉迷于让事情变得超级通用。如果我的方法对你的情况不起作用，很容易适应。

我的灵感不是来自 Python 社区，而是来自于 Elixir 的 Phoenix 框架。在其最新版本中，Phoenix 附带了一个现成的 LiveDashboard，这对于在生产中运行 Elixir 应用程序非常有帮助。在许多很酷的特性中，仪表板有一个请求记录器，它几乎可以完成我上面描述的功能。