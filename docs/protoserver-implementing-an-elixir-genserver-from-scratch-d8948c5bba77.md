# ProtoServer:从头开始实现 Elixir 的 GenServer

> 原文：<https://levelup.gitconnected.com/protoserver-implementing-an-elixir-genserver-from-scratch-d8948c5bba77>

## 从 Erlang 流程开始，一路揭开 Elixir GenServers 背后的神秘面纱

Elixir 最显著的特性之一是将*进程*作为核心抽象，这要归功于它基于 Erlang 和 [BEAM](https://www.erlang.org/blog/a-brief-beam-primer/) VM。

Elixir 通过 [GenServer](https://hexdocs.pm/elixir/1.12/GenServer.html) 提高原始流程的可用性，然后通过[代理、](https://hexdocs.pm/elixir/1.13.2/Agent.html) [应用](https://hexdocs.pm/elixir/Application.html)和 [GenStage](https://hexdocs.pm/gen_stage/GenStage.html) 将其提升到更高的水平。

当我开始使用长生不老药的时候，发电机似乎对我有魔力。虽然我最终广泛地使用了它们，但它们实际上是如何工作的，以及它们与[监督树](https://www.erlang.org/doc/man/supervisor.html)的关系是一个谜。

因此，在这里，我们将尝试从 Erlang 进程开始，通过构建一个关于如何实现 Elixir GenServers *的近似模型，来解开它们是如何工作的。我们不会涵盖关于流程、管理器或 Elixir 应用程序的所有内容——仅仅是为了更好地理解 GenServers 在幕后是如何工作的。*

在本练习的最后，我们将编写一个“原型 GenServer”模块，我们称之为`ProtoServer`，我们可以将它`use`为常规的 GenServer，并提供基本的`GenServer.call`语法和功能。

# 处理

Erlang [进程](https://www.erlang.org/doc/reference_manual/processes.html)是它实现大规模、可靠的并发的方式。

让我们看一个教科书示例，看看它们是如何用 Erlang 编写的，然后我们将从这个示例切换到 Elixir。

```
-module(spawn).
-export([start/0, greet/1]).greet(Arg1) ->
   io:format("Hello, ~p!~n", [Arg1]).
start() ->
   Pid = spawn(?MODULE, greet, ["world"]),
   io:fwrite("~p",[Pid]).
```

如果我们将上面的代码写到`spawn.erl`，我们可以使用`erl` shell 来执行它:

```
$ erl
Erlang/OTP 24 [erts-12.2] [source] [64-bit] [smp:12:12] [ds:12:12:10] [async-threads:1] [jit]Eshell V12.2  (abort with ^G)
1> c('spawn').
{ok,spawn}
2> spawn:start().
<0.86.0>Hello, "world"!
ok
```

所以，`spawn`可以接受一个模块、一个函数名和一个参数列表。然后，它作为一个独立的进程执行指定的函数(向其传递参数列表)。

在酏剂中，与上述内容相当的是:

```
defmodule Spawn do
  def greet(name), do: IO.puts "Hello, #{name}"
end:erlang.spawn(Spawn, :greet, ["world"])
```

Elixir 提供了一个简单得多的`[spawn/1](https://elixir-lang.org/getting-started/processes.html#spawn)`函数，如果我们只想在后台执行一些代码(又名“启动并忘记”)，就可以使用这个函数:

```
spawn(fn -> IO.puts("Hello in the background!") end)
```

如果我们让它等待几毫秒，我们可以看到它在后台:

```
iex> spawn(fn -> :timer.sleep(500); IO.puts("Hello in the background!") end)
#PID<0.107.0>
Hello in the background!
```

> `[spawn_link/1](https://elixir-lang.org/getting-started/processes.html#links)`是`spawn/1`的替代，它将错误传播或冒泡到父进程。我们不会在这里深入讨论，但是只知道用`spawn_link`在父进程和产生的子进程之间创建一个“链接”,这样如果父进程失败(或者产生一个错误),错误就会“冒泡”到父进程。

让我们通过编写一个调用自身(递归无限循环)的函数来进一步说明这一点:

```
iex> defmodule Loop do
  def loop() do
    IO.puts("Looping...")
    :timer.sleep(1000)
    loop()
  end
endiex> pid_loop = spawn(&Loop.loop/0)
Looping...
#PID<0.117.0>
Looping...
Looping...
Looping...
...
```

注意，`spawn/1`返回一个 PID 或*进程标识符*。

```
iex> inspect(pid_loop)
"#PID<0.118.0>"
```

现在，如果我们所能做的就是在后台执行代码，那么我们就不会有太多裸露的[线程](https://en.wikipedia.org/wiki/Thread_(computing))。

> 如果您所需要的是在后台执行代码，并且可能确定它是否成功完成或者检查和处理任何失败，那么您应该使用 Tasks 而不是`*spawn/1*`。参见[https://elixir-lang . org/getting-started/processes . html # tasks](https://elixir-lang.org/getting-started/processes.html#tasks)

# 进程间通信

Elixir 进程让我们比低级线程做得更多，因为进程不仅仅是并发执行代码的手段——它们还提供了*消息传递*(在 Erlang 中是*信号*)。

为了更早地退出无限循环函数，我们只需向它发送退出信号:

```
Looping...
Looping...
iex> Process.exit(pid_loop, 0)
trueiex>
```

现在让我们编写另一个无限循环的函数，但这次我们将演示如何发送和接收有意义的消息:

```
iex> defmodule Receiver do
  def loop() do # receive blocks, awaiting a message
    receive do
      {:greet, name} -> IO.puts("Hello, #{name}")
      msg -> IO.puts "Received unknown message: #{inspect(msg)}"
    end
    loop()
  end
endiex> pid = spawn(&Receiver.loop/0)
#PID<0.119.0>
```

当执行`receive`块时，流程等待，直到有消息发送给它，模式匹配收到的消息，然后执行匹配分支。然后，接收器自行循环，等待下一条消息:

```
iex> send(pid, {:greet, "world"})
Hello, world
{:greet, "world"}iex> send(pid, "foo")
Received unknown message: "foo"
"foo"
```

我们现在有了一个持久的、有背景的演员的开端。

## 邮筒

灵药信号不仅仅是一些在后台调用函数的精心设计的方法。当我们说 Elixir 有消息传递时，我们的意思是每个进程实际上都有一个可以充当消息队列的*邮箱*！

我们可以通过在接收器中添加人工延迟，然后一个接一个地发送几条消息来看到这一点:

```
iex> defmodule Mailbox do
  def loop() do
    receive do
      {:greet, name} -> greet(name)
      msg -> IO.puts "Received unknown message: #{inspect(msg)}"
    end
    loop()
  end
  def greet(name) do
    IO.puts("Hello, #{name}")
    :timer.sleep(1000)
  end
endiex> Enum.each(1..5, fn i -> send(pid, {:greet, ["world #{i}"]}) end)
Hello, world 1
:ok
Hello, world 2
Hello, world 3
Hello, world 4
Hello, world 5
```

在这里我们可以看到，即使在发送完所有消息后,`Enum.each/2`循环已经返回，这些消息仍然保存在邮箱中，直到`receive`被调用。一旦接收到所有的消息，下一个对`receive`的调用将等待另一个消息。

# 状态

在面向对象编程中，持久对象用于在堆中保存状态，直到它们被显式释放、垃圾回收或程序退出。

因为 Elixir 是功能性的，而不是让持久的“对象”占用堆上的内存并响应方法调用，我们有并发运行并响应消息的进程。但是持久状态呢？

简单。函数调用可以带参数，函数没有理由不能带参数递归调用自己！

首先让我们编写一个简单的倒计时函数:

```
defmodule Count do
  # call the loop with initial state
  def down do
    IO.puts("Counting down...")
    loop(5)
  end # this recursively calls itself with its own 'state'
  def loop(n) do
    if n > 0 do
      IO.puts("#{n}")
      :timer.sleep(1000)
      loop(n-1)
    end
  end
endpid = spawn(&Count.down/0)
IO.puts("spawn(&Count.down/0) -> #{inspect(pid)}")
:timer.sleep(5000)
```

如果我们把它写到`count_down.exs`，然后用`elixir count_down.exs`运行它，我们得到的输出是:

```
elixir count_down.exs
Counting down...
5
spawn(&Count.down/0) -> #PID<0.100.0>
4
3
2
1
```

正如我们所看到的，即使进程在后台循环，它也保持一个不断减少的计数作为“状态”。

现在让我们看一个更有用的例子，一个可以接收有意义的消息并修改自身状态的计数器:

```
defmodule Counter do
  # call the loop with initial state of 0
  def init do
    IO.puts("Initial state: 0")
    message_loop(0)
  end # Recursively calls itself with its own 'state', and receive messages
  def message_loop(n) do
    receive do
      :inc -> IO.puts("#{n} + 1 -> #{n + 1}"); message_loop(n+1)
      :dec -> IO.puts("#{n} - 1 -> #{n - 1}"); message_loop(n-1)
      :print -> IO.puts("Current state: #{n}"); message_loop(n)
      msg -> raise "Received unknown message: #{inspect(msg)}"
    end
  end
endpid = spawn(&Counter.init/0)
IO.puts("spawn(&Counter.init/0) -> #{inspect(pid)}")
send(pid, :inc) # 0 + 1 -> 1
send(pid, :inc) # 1 + 1 -> 2
send(pid, :inc) # 2 + 1 -> 3
send(pid, :dec) # 3 - 1 -> 2
send(pid, :inc) # 2 + 1 -> 3
send(pid, :print) # Current state: 3
```

当我们使用`elixir counter.exs`运行时，我们得到了预期的输出:

```
Initial state: 0
spawn(&Counter.init/0) -> #PID<0.100.0>
0 + 1 -> 1
1 + 1 -> 2
2 + 1 -> 3
3 - 1 -> 2
2 + 1 -> 3
Current state: 3
```

# 接收响应

如果我们的后台进程只能打印出它自己的状态，那么它就不如一个合适的对象有用，对吗？我们还需要一种方法让我们的对象响应消息，并且可能返回它自己的状态(或者与它的内部状态相关的东西)。

回想一下，我们可以`send/2`将消息发送到后台进程的邮箱。嗯，当前进程或调用者也有邮箱，也可以接收消息！

为了让我们收到对已发送邮件的回复，我们需要:

1.  通过发送调用者的进程 id，告诉接收者如何向调用者发回消息。我们可以使用`self/0`检索当前的进程 id。
2.  在调用过程中设置一个接收块来等待和处理发回的消息。

让我们基于我们的`Counter`例子来看看这是如何进行的。首先，我们将修改我们的`receive_loop`,以便它可以用`{:get, pid}`的*元组*来响应消息，其中`pid`是调用者的进程 id:

```
 def receive_loop(n) do
    receive do
      :inc -> IO.puts("#{n} + 1 -> #{n + 1}"); receive_loop(n+1)
      :dec -> IO.puts("#{n} - 1 -> #{n - 1}"); receive_loop(n-1)
      {:get, caller} -> send(caller, {:counter, n}); receive_loop(n)
      msg -> raise "Received unknown message: #{inspect(msg)}"
    end
  end
```

接下来，我们不只是发送`:print`，而是发送`{:get, self()}`。在发送消息之后，在调用过程中，我们还设置了一个`receive`块来等待来自`Counter`的响应:

```
send(pid, {:get, self()})
receive do
  {:counter, n} -> IO.puts("Got response from counter: #{n}")
  msg -> raise "Received unknown message: #{inspect(msg)}"
end
```

现在，当我们运行修改后的计数器时，输出如预期的那样是:

```
Initial state: 0
spawn(&Counter.init/0) -> #PID<0.100.0>
0 + 1 -> 1
1 + 1 -> 2
2 + 1 -> 3
3 - 1 -> 2
2 + 1 -> 3
Got response from counter: 3
```

这表明`Counter`确实在向调用它的主进程发送回复*返回*。

# 抽象

我们现在已经有了一个适当的发送和接收框架的开端。但是在我们将它提取到一个可重用的模块之前，让我们抽象掉`spawn`、`send`和`receive`位，因为调用者只与`Counter`交互。

首先，让我们给`Counter`添加一个`start()`函数，它只调用`spawn`并返回计数器的 PID:

```
 # spawn the Counter in the background and return its pid
  def start() do
    spawn(&init/0)
  end
```

接下来，让我们一般化 Counter 的`receive_loop`并让它期望*所有的*消息都是调用者的 PID 和实际消息的元组。收到消息后，它会将处理委托给一个`handle_msg`函数，该函数将返回新的状态。然后，它将发送新的状态作为对调用者的回复，然后递归地循环回自身。

```
 def receive_loop(state) do
    receive do
      {from, msg} ->
        new_state = handle_msg(msg, state)
        send(from, new_state)
        receive_loop(new_state)
      msg -> raise "Received unknown message: #{inspect(msg)}"
    end
  end
```

我们现在可以在`handle_msg`中更清楚地表达计数器的逻辑。我们真正做的是从`Counter`的核心中抽象出`send`和`receive`逻辑。

```
 def handle_msg(:inc, n), do: n + 1
  def handle_msg(:dec, n), do: n - 1
  def handle_msg(:get, n), do: n
```

最后，我们将为计数器添加“包装”函数，这样调用者也不需要编写`receive`块:

```
 # Increment the counter and get the new count
  def inc(pid) do
    send(pid, {self(), :inc})
    receive do
      resp -> IO.puts("Counter.inc() -> #{resp}")
    end
  end # Decrement the counter and get the new count
  def dec(pid) do
    send(pid, {self(), :inc})
    receive do
      resp -> IO.puts("Counter.dec() -> #{resp}")
    end
  end # Just get the current count
  def get(pid) do
    send(pid, {self(), :inc})
    receive do
      resp -> IO.puts("Counter.get() -> #{resp}")
    end
  end
```

在这个版本的`Counter`中，调用代码现在被简化了:

```
pid = Counter.start()
IO.puts("Counter.start() -> #{inspect(pid)}")
Counter.inc(pid) # 1
Counter.inc(pid) # 2
Counter.inc(pid) # 3
Counter.dec(pid) # 2
Counter.inc(pid) # 3
Counter.get(pid) # 3
```

## 命名流程

从呼叫者的角度来看，唯一剩下的事情是我们仍然在处理 PID。幸运的是，有一种使用[Process.register/2](https://hexdocs.pm/elixir/1.12/Process.html#register/2)为 PID 注册名称的方法。

让我们重写我们的`Counter.start`,使用模块名(方便地称为`__MODULE__`)作为进程的注册名:

```
 # spawn the Counter in the background and return its pid
  def start() do
    pid = spawn(&init/0)
    Process.register(pid, __MODULE__)
    pid
  end
```

这样，我们现在可以通过使用`Process.whereis/1`检索 PID 来简化所有的`inc`、`dec`和`get`调用，如下所示:

```
 def inc() do
    pid = Process.whereis(__MODULE__)
    send(pid, {self(), :inc})
    receive do
      resp -> IO.puts("Counter.inc() -> #{resp}")
    end
  end
```

这样，我们的调用代码就大大简化了！

```
Counter.start() # We can discard the returned PID
Counter.inc() # 1
Counter.inc() # 2
Counter.inc() # 3
Counter.dec() # 2
Counter.inc() # 3
Counter.get() # 3
```

# 原型服务器

让我们总结一下我们所学的一切，将底层的`Process`调用和消息发送与接收抽象到一个可重用的模块中。

我们希望我们的原型服务器的行为就像一个 GenServer。含义:

*   当我们调用`ProtoServe.start_link/3`(或`start/3`)时，我们想要传入我们的模块，初始状态，并接受一些选项
*   如果给定了`name:`选项，就用它作为进程名，这样我们就不必跟踪 PID 了
*   当进程启动时，用初始状态调用我们模块的`init/1`回调
*   允许`init/1`返回`:ok`和初始状态，或者`:error`和一些错误。在`:ok`上，进入信息循环。在`:error`上，立即出现`raise`错误
*   在消息循环中，在一个`:call`消息上，用消息、调用者 PID 和当前状态调用我们的`handle_call/3`回调

咻！这需要展开很多工作，但是不要担心，它实际上很容易实现和遵循。

我们希望我们的模块照顾到所有必要的“样板文件”，并且简单易用。

在 Elixir 中，当我们`use`另一个模块时，我们允许该模块使用 Elixir [宏](https://elixir-lang.org/getting-started/meta/macros.html)将任何代码“注入”当前模块。全面解释 elixir 宏超出了现在的范围——知道要使`use`工作，我们只需要实现一个`__using__`宏就足够了！

让我们开始编写我们的`ProtoServer`并实现`start_link/3`:

```
# A prototype GenServer
defmodule ProtoServer do # Just link GenServer.start_link/3, spawns the calling
  # calling module, and returns the PID. Also, support the
  # optional `name:` option 
  def start_link(module, state, options \\ %{}) do
    pid = spawn_link(module, :on_spawn, [state]) if Keyword.has_key?(options, :name) do
      Process.register(pid, options[:name])
    end
    pid
  end
```

因此，`start_link`仅仅调用了`spawn_link`，但是我们提供了自己的`on_spawn`函数，它将实现调用模块的`init/1`回调的“魔法”。我们还支持可选的`name:`选项，并使用该名称注册进程，以便以后更容易调用我们的`ProtoServer`模块。

现在我们不能只在`ProtoServer`本身中传递`def on_spawn`，因为当我们`spawn_link`时我们需要在调用模块中传递。否则，我们将编写`spawn_link(ProtoServer, :on_spawn, ...)`，这将使得调用我们模块的`init/1`回调函数变得困难！

相反，这是我们依靠`__using__`宏在调用模块本身中写`on_spawn`的地方！展示比解释容易:

```
 defmacro __using__(_opts) do
    quote do
      def on_spawn(args) do
        case init(args) do
          {:ok, initial_state} -> receive_loop(initial_state)
          {:error, reason} -> raise reason
        end
      end
```

在`ProtoServer.__using__`中，我们使用一个`quote`块来编写`on_spawn/1`启动函数*，就好像我们是在调用模块本身中编写一样！*

也就是说，当我们写作时，例如:

```
defmodule Counter do
  use ProtoServer
```

就好像我们写道:

```
defmodule Counter do
  def on_spawn(args) do...
```

在`on_spawn/1`中，我们用相同的初始参数调用`init/1`，然后根据`init/1`是返回`:ok`还是`:error`，我们要么继续调用`receive_loop`要么失败。

我们在`__using__`宏中使用相同的`quote`块写出`receive_loop/1`，就像我们在调用模块中写一样:

```
 # Recursively calls itself with its own 'state', 
      # and receive messages
      def receive_loop(state) do
        receive do
          {:call, from, msg} -> case handle_call(msg, from, state) do
            {:reply, response, new_state} -> send(from, response); receive_loop(new_state)
            {:no_reply, new_state} -> receive_loop(new_state)
            result -> raise "Received unknown result: #{inspect(result)}"
          end
          msg -> raise "Received unknown message: #{inspect(msg)}"
        end
      end
```

除了这次我们处理`:call`消息并委托给模块的`handle_call/3`回调之外，`receive_loop`与我们之前写的几乎相似。然后我们发回一个响应，什么都不做，或者根据`handle_call/3`是返回`:reply`、`:no_reply`还是一个未知的返回值来引发一个错误。

这就是我们在`__using__`宏中所需要的！现在我们只需要实现`ProtoServer.call/2`。对于`call/2`，我们提供了一种方便的方法来传入一个符号(模块名)而不是一个 PID，我们自己使用`Process.whereis/1`:

```
def call(to, msg) do
    case to do
      pid when is_pid(pid) -> send(pid, {:call, self(), msg})
      name when is_atom(name) -> send(Process.whereis(name), {:call, self(), msg})
    end
    receive do
      resp -> resp
    end
  end
```

我们的全功能`Counter`的最终实现可以用`use ProteServer`精确地写成*，就好像*我们用了一个`GenServer`来代替。您可以在下面的代码中将`ProtoServer`替换为`GenServer`，亲自尝试一下！

```
defmodule Counter do
  use ProtoServer def start_link() do
    ProtoServer.start_link(__MODULE__, 0, name: __MODULE__)
  end def init(initial_value) do
    IO.puts("init(#{initial_value})")
    {:ok, initial_value}
  end def handle_call(msg, _from, state) do
    resp = handle_msg(msg, state)
    {:reply, resp, resp}
  end def handle_msg(:inc, n), do: n + 1
  def handle_msg(:dec, n), do: n - 1
  def handle_msg(:get, n), do: n def inc() do
    resp = ProtoServer.call(__MODULE__, :inc)
    IO.puts("Counter.inc() -> #{resp}")
  end def dec() do
    resp = ProtoServer.call(__MODULE__, :dec)
    IO.puts("Counter.inc() -> #{resp}")
  end def get() do
    resp = ProtoServer.call(__MODULE__, :get)
    IO.puts("Counter.inc() -> #{resp}")
  end
end
```

> `ProtoServer.cast/2`的实现留给读者作为练习。

这篇文章比我想的要长一点，并且涵盖了很多主题——但是我希望您能够跟上，并且现在对`GenServer`有了更深的理解，并且在使用它时有了更大的信心！

干杯！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)