# 获取正在运行的 Python 程序的堆栈跟踪

> 原文：<https://levelup.gitconnected.com/getting-the-stack-trace-of-a-running-python-program-f6e2f156f53a>

![](img/17ccedf59e3814891b75ecbdf8fa0d33.png)

最近想考察一个多线程 Python 程序，花了很长时间才完成，看起来卡死了，甚至偶尔崩溃。

之前有过 java 背景，我希望用一些实用程序如 [jstack](https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/tooldescr016.html) 来获得堆栈跟踪。令我惊讶的是， **Python 并没有这样的实用程序，但是我们自己做一个却很容易！**

# 代码

很明显——检查正在运行的线程，格式化堆栈跟踪并附加它，然后返回它。

它使用纯 Python 库，不需要任何插件。

示例输出可能如下所示(主要关注底部):

# 测试

上面的输出是测试中 print 语句的结果——注意，我们期望看到两个线程:主测试线程和我们刚刚创建的线程，以及堆栈跟踪中的测试函数和测试语句。

# 后续步骤/注意事项

因此，我们有了 util 函数，它可以工作，但是我们需要将它合并到我们的代码中:

1.  如果我们的程序是一个 web 服务器，我们可以有一个类似 REST API 的入口点来按需执行它。
2.  或者，我们可以运行一个守护线程，偶尔打印输出，这样我们就可以持续了解程序的运行情况。

就是这样！希望这个小工具能在需要的时候帮到你。