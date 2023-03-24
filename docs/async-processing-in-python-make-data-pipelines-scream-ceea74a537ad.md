# Python 中的异步处理:让数据管道尖叫

> 原文：<https://levelup.gitconnected.com/async-processing-in-python-make-data-pipelines-scream-ceea74a537ad>

![](img/84836c7b37c15c7849bcd1a37522d1c8.png)

[https://www.freepik.com/photos/background'](’<a)>kjpargeter 制作的背景照片—[www.freepik.com](http://www.freepik.com)</a>

如何在 python 中启用并行处理？线程化是一种方式，但是构建一个完全有弹性的多线程进程是困难的。在 python 中引入异步处理是在应用程序中引入并行处理的一种更加简单和实用的方式。在这篇非常短的博文中，您将了解如何启用简单的异步功能。

# “正常”的方式

让我们来看看编写程序的标准方式。考虑一个在名为`myproc()`的函数中定义的流程，它做了一件需要 5 秒钟的事情。我们将通过设置 5 秒钟的睡眠来模拟它。当进行函数调用时，我们打印一行“myproc started …”。同样，当函数完成执行时，我们打印“myproc finished …”。使用时间模块，我们将记录经过的时间。这是代码。

```
import timedef myproc():
    print("myProc started ...")
    t1 = time.perf_counter()
    time.sleep(5)
    t = time.perf_counter() - t1
    print(f"   myProc finished in {t:0.5f} seconds.")

def main():
    for _ in range(5):
        myproc()

if __name__ == "__main__":
    start_sec = time.perf_counter()
    main()
    elapsed_secs = time.perf_counter() - start_sec
    print(f"Job finished in {elapsed_secs:0.5f} seconds.")
```

我们把这个文件叫做 sync.py。当我们用 python 执行这个脚本时:

```
myProc started ...
   myProc finished in 5.00262 seconds.
myProc started ...
   myProc finished in 5.00281 seconds.
myProc started ...
   myProc finished in 5.00011 seconds.
myProc started ...
   myProc finished in 5.00042 seconds.
myProc started ...
   myProc finished in 5.00504 seconds.
Job finished in 25.01145 seconds.
```

正如预期的那样，这项工作总共花费了 25 秒，因为函数`myproc()`的每次运行花费了 5 秒。连续运行 5 次，我们在 25 秒内完成了任务。

# 异步方式

现在，让我们以异步方式重写它。下面是修改后的代码。修改后的部分以粗体显示。

```
import asyncio
import timeasync def myproc():
    print("myProc started ...")
    t1 = time.perf_counter()
    **await asyncio.**sleep(5)
    t = time.perf_counter() - t1
    print(f"   myProc finished in {t:0.5f} seconds.")async def main():
    **await asyncio.gather**(
      myproc(),
      myproc(), 
      myproc(), 
      myproc(),
      myproc()
    )if __name__ == "__main__":
    start_sec = time.perf_counter()
    **asyncio.run**(main())
    elapsed_secs = time.perf_counter() - start_sec
    print(f"Job finished in {elapsed_secs:0.5f} seconds.")
```

以下是输出:

```
myProc started ...
myProc started ...
myProc started ...
myProc started ...
myProc started ...
   myProc finished in 5.00337 seconds.
   myProc finished in 5.00347 seconds.
   myProc finished in 5.00349 seconds.
   myProc finished in 5.00351 seconds.
   myProc finished in 5.00353 seconds.
Job finished in 5.00495 seconds.
```

哇哦。整个工作只用了 5 秒钟就完成了(每次运行`myproc()`函数所花的时间)。怎么会这样

# 异步/等待组合

诀窍是在上面的代码中包含 async 和 await 关键字。这些关键字确保函数以异步方式运行。请注意下面一行:

```
**await** sleep(5)
```

await 关键字告诉 python 不要等待它的完成，而是将控制权暂时交还给调用者。让我重复一遍，因为这是一个非常强大的概念。关键字告诉 python 解释器将控制权交还给被调用的程序(在本例中是程序`main()`)，但只是暂时的。一旦执行结束，它就取回控制权。请注意下面一行:

```
**asyncio.run**(main())
```

这告诉`main()`函数以异步方式执行该函数，即不要等待`myproc()`的完成。但是然后主程序里就没别的了；因此，它调用`myproc()`的下一次迭代，再次像以前一样异步，并且一旦遇到 await 关键字，它就传递控制。

然而，回到`main()`的控制权是不是一去不复返了？一点也不。当`myproc()`完成时(按照设计，大约 5 秒钟)，控制权返回给该函数。该函数执行下一行，打印结束行以及经过的时间。

函数调用仍然花了 5 秒钟(正如预期的那样)，但是程序以一种并行的方式执行了所有的函数调用。因此总时间也只有 5 秒。

注意，我用的是“某种平行”。执行实际上不是并行的。我们将条件放在函数的逻辑中，以告知何时将控制权交还给调用者。它模仿了平行，但实际上不是平行的。事实仅仅是被调用的函数是一个**非阻塞**调用。迷茫？

# 我们控制异步部分

为了说明我们如何控制调用的异步性质的微妙概念，让我们修改脚本 async1.py 如下。我们称之为 async2.py，变化用粗体表示。

```
import asyncio
import timeasync def myproc():
    print("myProc started ...")
    t1 = time.perf_counter()
    await asyncio.sleep(**2.5)**
    **time.sleep(2.5)**
    t = time.perf_counter() - t1
    print(f"   myProc finished in {t:0.5f} seconds.")async def main():
    await asyncio.gather(
      myproc(),
      myproc(),
      myproc(),
      myproc(),
      myproc()
    )if __name__ == "__main__":
    start_sec = time.perf_counter()
    asyncio.run(main())
    elapsed_secs = time.perf_counter() - start_sec
    print(f"Job finished in {elapsed_secs:0.5f} seconds.")
```

这是一个简单但重要的变化。我没有让函数异步地花费所有的 5 秒，而是将它分成 2.5 秒，分别用在 asyncio.sleep 和 time.sleep 上(这是同步的)。以下是我们运行脚本时的结果:

```
myProc started ...
myProc started ...
myProc started ...
myProc started ...
myProc started ...
   myProc finished in 5.00751 seconds.
   myProc finished in 7.50905 seconds.
   myProc finished in 10.01197 seconds.
   myProc finished in 12.51726 seconds.
   myProc finished in 15.02254 seconds.
Job finished in 15.02414 seconds.
```

输出很有启发性。注意所有的函数调用是如何像预期的那样立即开始的。然而，现在每次通话花费的时间越来越多，比上一次多了 2.5 秒。为什么？

是因为只有 2.5 秒的通话是异步的。一旦达到这一点，调用返回到函数。第二次睡眠不是异步的；因此，在调用完成之前，函数不会将控制权返回给调用者，在本例中是 main()。因此，这是一个阻塞呼叫。这就是为什么第二个函数必须等待睡眠函数的执行。所以，这不是并行执行。我们控制的是异步执行。

# 通话顺序很重要

来说明这种控制是如何有用和重要的。让我们对程序再做一个小小的改动。在这里，我们将简单地改变调用 async 和 sync sleep 命令的顺序，如粗体所示。

```
import asyncio
import timeasync def myproc():
    print("myProc started ...")
    t1 = time.perf_counter()
 **time.sleep(2.5)
    await asyncio.sleep(2.5)**
    t = time.perf_counter() - t1
    print(f"   myProc finished in {t:0.5f} seconds.")async def main():
    await asyncio.gather(
      myproc(),
      myproc(),
      myproc(),
      myproc(),
      myproc()
    )if __name__ == "__main__":
    start_sec = time.perf_counter()
    asyncio.run(main())
    elapsed_secs = time.perf_counter() - start_sec
    print(f"Job finished in {elapsed_secs:0.5f} seconds.")
```

注意:和 async2.py 完全一样；只是调用睡眠的顺序变了。之前我们先称之为异步睡眠，然后称之为同步睡眠。这里我们先调用同步睡眠。当您运行它时，您将得到以下输出:

```
myProc started ...
myProc started ...
myProc started ...
myProc started ...
myProc started ...
   myProc finished in 12.51053 seconds.
   myProc finished in 10.00526 seconds.
   myProc finished in 7.50407 seconds.
   myProc finished in 5.00093 seconds.
   myProc finished in 5.00068 seconds.
Job finished in 15.01211 seconds.
```

注意函数调用“myProc started …”的第一行现在是如何交错出现的。事实上，它每隔 2.5 秒就出现一次。为什么？简单；该函数有一个睡眠 2.5 秒的阻塞调用；因此，在将控制发送回主程序之前，它必须等待那么长时间。main()程序不能再次调用`myProc()`,直到这段时间过去。

2.5 秒后，该函数遇到异步睡眠调用。此时，它将控制权返回给 main()。然而，当`main()`返回到`myproc()`的第一个调用时，已经是所有其他调用完成之后了。因此，这里有一个有趣的模式:第一个对`myproc()`的调用耗时最长，而前一个调用耗时最长。不过，总的时间保持不变。

这就是为什么**调用函数的同步和异步部分的顺序会影响**程序如何从子组件返回值。总时间不会变化。

为什么重要？好吧，让我们考虑这个程序中的另一个小任务。我们需要计算函数调用中某个数字的总数。为了简单起见，我们将在每次调用函数时将变量 sum 加 1。此外，让我们也展示一下函数是如何依次被调用的。我们将修改函数来接受一个名为`callid`的参数，这个参数仅仅是一个数字，用来清楚地表示每个函数调用。然后我们将创建一个调用 id 链来显示被调用的函数。首先，我们将按以下顺序进行:异步，然后同步。

```
import asyncio
import timechain = ""
sum = 0
async def myproc(callid):
 **global chain
    global sum**
    print(f"myProc {callid} started ...")
    t1 = time.perf_counter()
    await asyncio.sleep(2.5)
 **chain = chain + "->" + str(callid)
    sum = sum + 1**
    time.sleep(2.5)
    t = time.perf_counter() - t1
    print(f"   myProc {callid} finished in {t:0.5f} seconds. sum = {sum} chain {chain}")async def main():
    await asyncio.gather(
      myproc(1),
      myproc(2),
      myproc(3),
      myproc(4),
      myproc(5)
    )if __name__ == "__main__":
    start_sec = time.perf_counter()
    asyncio.run(main())
    elapsed_secs = time.perf_counter() - start_sec
    print(f"Job finished in {elapsed_secs:0.5f} seconds.")
```

以下是输出:

```
myProc 1 started ...
myProc 2 started ...
myProc 3 started ...
myProc 4 started ...
myProc 5 started ...
   myProc 1 finished in 5.00606 seconds. sum = 1 chain ->1
   myProc 2 finished in 7.51137 seconds. sum = 2 chain ->1->2
   myProc 3 finished in 10.01224 seconds. sum = 3 chain ->1->2->3
   myProc 4 finished in 12.51499 seconds. sum = 4 chain ->1->2->3->4
   myProc 5 finished in 15.01671 seconds. sum = 5 chain ->1->2->3->4->5
Job finished in 15.01861 seconds.
```

这些函数是按照我们传递的顺序调用的，如链中所示；但是请注意`sum`这个变量。它在每个函数调用中都有所不同。为什么？

很简单。当第一次调用该函数时，它返回控制，然后控制转到第二次函数调用。然而，当第二个函数调用(callid=2)开始执行同步部分时，第一个函数调用(callid=1)已经开始计算并完成了它的工作。当时 sum 为 0；所以它加了 1 得出 1。当第二个函数调用到达代码的计算部分时，总和已经是 1；于是就想出了 1 + 1 = 2。接下来的电话也是如此。

为什么重要？

如果你需要的只是最终的总数，也就是工作结束时的总数，那就没问题了。数字会是正确的。但是，如果您在函数中使用 sum，例如，您正在检查 sum 是否大于 3，那么显然调用 1、2 和 3 会失败，但调用 4 和 5 会成功。这可能会引入一个错误。

与之形成对比的是一个小小的变化，您颠倒了同步和异步调用的顺序

```
import asyncio
import timechain = ""
sum = 0
async def myproc(callid):
    global chain
    global sum
    print(f"myProc {callid} started ...")
    t1 = time.perf_counter()
    time.sleep(2.5)
    chain = chain + "->" + str(callid)
    sum = sum + 1
    await asyncio.sleep(2.5)
    t = time.perf_counter() - t1
    print(f"   myProc {callid} finished in {t:0.5f} seconds. sum = {sum} chain {chain}")async def main():
    await asyncio.gather(
      myproc(1),
      myproc(2),
      myproc(3),
      myproc(4),
      myproc(5)
    )if __name__ == "__main__":
    start_sec = time.perf_counter()
    asyncio.run(main())
    elapsed_secs = time.perf_counter() - start_sec
    print(f"Job finished in {elapsed_secs:0.5f} seconds.")
```

以下是输出:

```
myProc 1 started ...
myProc 2 started ...
myProc 3 started ...
myProc 4 started ...
myProc 5 started ...
   myProc 1 finished in 12.51241 seconds. sum = 5 chain ->1->2->3->4->5
   myProc 2 finished in 10.01062 seconds. sum = 5 chain ->1->2->3->4->5
   myProc 3 finished in 7.51010 seconds. sum = 5 chain ->1->2->3->4->5
   myProc 4 finished in 5.00613 seconds. sum = 5 chain ->1->2->3->4->5
   myProc 5 finished in 5.00680 seconds. sum = 5 chain ->1->2->3->4->5
Job finished in 15.01523 seconds.
```

行为就不一样了。注意和的值是一样的，链也是一样的。这是为什么呢？这是因为当函数完成时，所有的值都已经被恰当地赋值了。

底线:注意函数调用的哪些部分是同步或异步的，以及在哪里使用变量。它可能会在你不知道的情况下产生不同的结果。

# 概括起来

*   您可以在 python 中使用异步处理来模拟并行处理。
*   但它不是多线程的。您可以控制哪些部分是异步的，哪些不是。
*   两个关键词的组合使之成为可能。async 应用于函数定义，告诉 python 该函数是一个异步调用。await 告诉命令将控制权暂时传递回调用者。
*   注意函数调用内部的逻辑对于同步和异步事件的行为。如果没有在正确的位置访问变量值，可能会导致错误的结果。