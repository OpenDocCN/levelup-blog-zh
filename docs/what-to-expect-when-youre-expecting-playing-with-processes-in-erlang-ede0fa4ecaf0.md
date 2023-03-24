# 当你期待时，期待什么:在 Erlang 中使用进程

> 原文：<https://levelup.gitconnected.com/what-to-expect-when-youre-expecting-playing-with-processes-in-erlang-ede0fa4ecaf0>

![](img/c0c5aa3e3e36b58739a35fdc4118053b.png)

克里斯托夫·高尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

rlang 的关键组织概念是流程，一个发送和接收消息的独立组件(由功能构建而成)。程序被部署为相互通信的进程集。Erlang 并发是基于进程的。这些小型的独立虚拟机可以评估 Erlang 函数。

在 Erlang 中:

*   创建和销毁进程的速度非常快。
*   在进程之间发送消息非常快。
*   在所有操作系统上，进程的行为都是一样的。
*   我们可以有非常多的进程。
*   进程不共享内存，完全独立。
*   流程交互的唯一方式是通过消息传递。

由于这些原因，Erlang 有时被称为纯粹的消息传递语言。

# 使用流程

我们所知道的关于顺序编程的一切对于并发编程仍然是正确的。我们所要做的就是添加以下原语:

## 创建流程

您可以使用 ***spawn*** 功能创建一个新流程:

```
Pid = spawn(Mod, Func, Args)
```

新进程与调用者并行运行。 ***spawn*** 返回一个 ***Pid*** (进程标识符的简称)。您可以使用一个 ***Pid*** 向流程发送消息。
注意，函数 ***Func*** 带 arity***length(Args)***必须从模块 ***Mod 中导出。*** 创建新流程时，使用定义代码的模块的最新版本。

```
Pid = spawn(Fun)
```

创建一个评估 ***Fun()*** 的新并发进程。这种形式的 ***spawn*** 总是使用正在评估的乐趣的当前值，而这个乐趣并不一定要从模块中导出。两种形式的 ***spawn*** 的本质区别在于动态代码升级。

## 发送消息

消息发送是异步的。发送方不会等待，而是继续做它正在做的事情。要使用以下语法向进程发送消息:

```
Pid ! Message
```

这会将`***Message***`发送给标识符为`***Pid***`的进程。`***!***` 被称为寄符。

## 接收消息

当消息到达流程时，系统会尝试使用模式匹配来匹配它。要在流程中接收消息，请使用以下语法:

```
receive
    Pattern1 [when Guard1] ->
        Expressions1;
    Pattern2 [when Guard2] ->
        Expressions2;
    ...
end
```

***接收… end*** 接收已经发送给流程的消息。在上面的示例中，系统尝试将消息与 ***模式 1*** (与可能的守卫 ***守卫 1*** )进行匹配；如果成功，它将对 ***表达式 1*** 求值。如果第一个模式不匹配，则尝试 ***模式 2*** ，以此类推。如果没有匹配的模式，则保存该消息供以后处理，并且该过程等待下一条消息。

就是这样。你不需要线程、锁、信号量和人工控制。

# 外壳是一个进程

shell 是一个容易发送和接收消息的地方(至少对于测试来说)。首先要探究的是进程标识符，通常称为 ***pid*** 。最容易得到的 ***pid*** 就是你自己。在 shell 中，您可以运行 self()函数:

```
mep@mep:~$ erl
Erlang/OTP 22 [erts-10.4.4] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:1]Eshell V10.4.4  (abort with ^G)
1> self().
<0.78.0> 
```

## 在外壳中发送消息

第 1 行将 shell 的 pid 赋给一个名为*的变量，这个 Pid 是用 ***self()*** 函数获取的，然后第 2 行使用这个 ***Pid*** 变量发送一个包含原子 ***testMessage*** 的消息。*

```
*Eshell V10.4.4 (abort with ^G)
1> Pid = self().
<0.78.0>
2> Pid ! testMessage.
testMessage
3>*
```

## *在 shell 中接收消息*

*让我们使用 ***receive … end 将我们在 sell 中收到的金额翻倍。*** 语法*

```
*Eshell V10.4.4 (abort with ^G)
1> Pid = self().
<0.78.0>
2> Pid ! 71\. 
71
3> receive X->X*2 end. 
142*
```

# *让我们打乒乓球吧*

*为了演示我们到目前为止所学的内容，我们来玩一些关于流程的乒乓游戏。首先用下面的代码创建两个名为 ping (ping.erl 文件)和 pong (pong.erl 文件)的模块:*

*用于乒乓操作的乒乓模块*

*现在，在 shell 中，运行以下命令(编译您的模块):*

```
*Eshell V10.4.4  (abort with ^G)
1> c(ping).
{ok,ping}
2> c(pong). 
{ok,pong}
3>*
```

*最后，您可以使用 ***启动*** 功能从任意一侧进行乒乓操作:*

```
*3> ping:start(7).
ping pong ping pong ping pong ping pong ping pong ping pong ping pong ok*
```

*来源:*

*[Erlang 文档](https://www.erlang.org/docs)*