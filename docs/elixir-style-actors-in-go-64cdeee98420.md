# 围棋里的二郎式演员

> 原文：<https://levelup.gitconnected.com/elixir-style-actors-in-go-64cdeee98420>

![](img/2a612f38480d9b2c8a55cab44f688bab.png)

[丹尼斯·内沃扎伊](https://unsplash.com/@dnevozhai?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

*原载于 2020 年 3 月 7 日*[*https://preslav . me*](https://preslav.me/2020/03/07/elixir-style-actors-in-golang/)*。*

我对[长生不老药](https://elixir-lang.org/)的尝试，让它和围棋之间的相似之处比我最初预期的要多得多。其中之一是两者如何处理并发性。关于这一点，在 Go 中创建 Elixir 风格的有状态参与者非常容易。回答这个问题，一个人是否需要它们，或者是否能利用它们，我会留给用户。如果你回过头来对我说，同样的事情可以用一个频道或者一张上面有`sync.Mutex`的地图来实现，你(几乎)是对的。然而，探索不同的思维方式是值得的。

对于那些不知道它的人来说，Elixir 是一种函数式语言。一切都在不可变的函数范围内运行，没有状态保留在表上。一个函数只能对它所接受的东西起作用。函数驻留在模块中，并在 Erlang 进程中执行。

抛开技术上的差异，你可以把进程看作是围棋的 goroutines 的等价物。函数链调用在进程/goroutine 内部运行。当最外层的函数返回时，进程/goroutine 结束。一个进程/goroutine 可以衍生出其他进程/go routine 来实现任务的并发执行。Go 通过通道进行同步，而 Elixir 通过内置于每个进程中的消息收件箱进行同步。使用内置的 receive 关键字，函数的执行会阻塞在一个进程中，直到收到某种类型的消息

```
receive do
    # Don't get too caught up on the Elixir syntax.
    # For now, it is only important to know that :message_a is           
    # equivalent to a string with the value of "message_a"
    # Those are called "atoms" and are quite often used in Ruby-like
    # languages 
    {:message_a, msg} ->
        do_something_with(msg)
end
```

从根本上说，这与让 [goroutine 阻塞它的执行并等待通道](https://play.golang.org/p/rZkdET2ZhJl)是一样的:

```
type message struct {
    val string
}

msgStream := make(chan message)

go func(out chan message) {
    out <- message{val: "hello world"}
}(msgStream)

msg := <-msgStream
fmt.Printf("%+v", msg)
```

对我来说，无论你是在等待一条消息到达你的收件箱，还是明确地设置一个阻塞通道作为一种交流机制，它描述了相同的范例。

清楚了吗？好吧。我们继续吧。我已经提到过，Elixir 是一种函数式语言。传递给函数的一切都是不可变的，改变它的唯一方法是返回它的新版本。这意味着循环结构是不可能的，因为它意味着修改和跟踪计数器变量。函数式语言实现循环效果的方式是通过递归(或者更准确地说是尾部递归):

```
def loop(5) do
    # Elixir uses pattern-matching when choosing which function to call.
    # In our case, as soon as its gets a count == 5, it will stop the loop
    5
end

def loop(count) do
    # Just print the count, but use pipes (|>)
    # instead of wrapping in a function call -> IO.puts(count)
    # Pipes totally save the day, when you have multiple call chains
    count
    |> IO.puts()

    loop(count + 1)
end
```

# 从递归到演员

如果我们把这个递归的例子看作是一个永无止境的循环。对该函数的第一次调用设置了初始状态，并且该函数一直不断地调用自己。

现在，这就是纯粹的函数式范式被打破的地方。我们已经了解到 Erlang 允许其他进程与我们通信。这意味着，如果我们的永无止境递归函数从外部接收到一条消息，它可以使用它的有效载荷，用它的初始状态的修改版本来调用它自己。请记住，接收消息是一个阻塞操作，该进程将只是徘徊，不使用任何 CPU 资源，直到我们的正确消息到达。

我们可以使用相同的消息传递范式来深入了解我们永无止境的函数的状态。由于它运行在一个单独的进程中，唯一的方法就是向它发送一个适当的消息，传递我们当前的进程 ID (PID ),并让它向我们发回一个消息。

```
defmodule Calculator do
    def start do
    # creates a separate process with its own internal state
    spawn(fn -> loop(0) end)
    end

    defp loop(current_value) do
    new_value =
        receive do
        # with this type of message, we can fetch the state of our calculator
        {:get, caller_pid} ->
            send(caller_pid, {:response, current_value})
            current_value

        # with this type of message, we can modify the state of our calculator
        {:add, value} ->
            current_value + value
        end

    loop(new_value)
    end
end
```

让我们测试一下我们的计算器流程:

```
defmodule CalculatorTest do
    def test_calculator do
    calc_pid = Calculator.start()

        # Like `receive`, `send` is built-in and take a PID, as well as a message
        # self() returns the process id (PID) of the current process
        # Like in Go, every piece of Elixir/Erlang code runs in a process
    send(calc_pid, {:get, self()})

        # `receive` will block, until we receive a message,
        # that matches the expected pattern - {:response, value}
    receive do
        {:response, value} ->
        value |> IO.puts()
    end

    send(calc_pid, {:add, 100})

    send(calc_pid, {:get, self()})

    receive do
        {:response, value} ->
        value |> IO.puts()
    end
    end
end
```

本质上，我们永无止境的功能变成了 Elixir 所说的有状态服务器进程，一个[角色模型](https://en.wikipedia.org/wiki/Actor_model)的实现。Actors 非常适合隔离关键状态，并允许与它进行并发通信，确保一次只发生一个变化。

# 从仙丹到外带

好了，现在我们知道了仙丹世界的工作原理，在 Go 上实现同样的事情非常简单。

```
func main() {
    in := make(chan message)
    out := make(chan int)
    go newCalculator(0, in, out)

    in <- message{operation: "get"}
    state := <-out
    log.Printf("Current state: %d", state)

    in <- message{operation: "add", value: 100}
    in <- message{operation: "get"}
    state = <-out
    log.Printf("Current state: %d", state)
}

type message struct {
    operation string
    value     int
}

func newCalculator(initialState int, in chan message, out chan int) {
    state := initialState
    for {
        p := <-in
        switch p.operation {
        case "add":
            log.Printf("Adding %d to the current state", p.value)
            state += p.value

        case "get":
            out <- state
        }
    }
}
```

需要注意的一点是，既然我们可以使用无限循环，我们就应该使用它，特别是，根据我的知识，Go 并不特别适合长循环递归。但大前提不变。一个函数在某个初始状态下被调用，并返回一个通道。该函数开始一个无限循环，在通道上阻塞。如果我们将一个值推送到该通道，函数将接受它，更新状态并再次阻塞。

# 私有国家

所以，现在我们揭开了演员背后的神秘面纱，讨论他们可能有什么用处是个好主意。

我马上想到的一件事是实现全局可访问，但真正私有的同步状态。这目前是通过使用通道`sync.Mutex`或新的`sync.Map`来实现的。

```
type SynchronizedMap struct {
    sync.RWMutex
    internal map[string]interface{}
}

func (rm *SynchronizedMap) Store(key string, value interface{}) {
    rm.Lock()
    rm.internal[key] = value
    rm.Unlock()
}
```

这种方法的脆弱性来自于 Go 应用程序中没有真正的私有状态。在上面的例子中，我们命名为`internal`的地图只受到外部访问的保护。与我们的`SynchronizedMap`在同一个包中的任何一段代码都可以自由地访问和修改它的内部，导致意想不到的后果。虽然在大多数情况下这不应该是一个问题，但在特殊情况下记住这一点绝对是有好处的。

# 有状态自治代理

演员模型的亮点在于演员实例系统的编排——自治代理。每个 Actor 实例都能够改变其状态，对发送给它的消息做出反应。Actor 实例可以很容易地衍生出其他 Actor 实例，只有创建 Actor(主管)才能控制这些实例(记住，私有状态)。主管还可以接管他们所负责的参与者的失败，可能会杀死一些参与者，并以干净的状态重新启动它们。举个极端的例子，groroutines 相当便宜，人们可以很容易地想象成千上万的 Actor 实例，在一个深度嵌套的层次结构中，多个级别的监督 Actor 接管它们的“后代”。这是 Erlang 的独特销售主张，但正如我希望展示的那样，也可以在 Go 中复制。

正如在开始时所讨论的，我将把关于这种方法的实用性以及它的其他应用的讨论留给读者。我很想听听你的想法。不要犹豫给我留言，或者开始新的讨论。

# 资源

[](https://www.goodreads.com/book/show/38732242-elixir-in-action) [## 长生不老药

### 非常棒的关于长生不老药的书...我已经知道了 Erlang，所以更容易跟上...但是这本书仍然做了一个漂亮的…

www.goodreads.com](https://www.goodreads.com/book/show/38732242-elixir-in-action) 

关于学习长生不老药的最好的书之一，当然，也是启发我写这篇文章的书。SAA juri 的解释清晰明了，尤其是像这样复杂的话题。如果你喜欢这个博客，并且愿意支持我阅读伟大书籍的热情，你可以[使用这个特殊链接](https://amzn.to/39AXJ6T)在亚马逊上购买。谢谢！