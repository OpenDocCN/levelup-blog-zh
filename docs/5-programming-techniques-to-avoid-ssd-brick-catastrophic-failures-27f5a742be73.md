# 避免 SSD Brick 灾难性故障的 5 种编程技巧

> 原文：<https://levelup.gitconnected.com/5-programming-techniques-to-avoid-ssd-brick-catastrophic-failures-27f5a742be73>

## *防止严重故障触手可及*

![](img/485b87b1fa64c73fec4e99407a1f5fa1.png)

> *TL；DR:用成熟的工具做成熟的软件。*

# 问题是

2022 年 7 月 9 日，[又一次硬件故障](https://twitter.com/girlhacker/status/1545646297348050946)瘫痪了几台服务器。

着眼于失败的根本原因，我们可以吸取教训。

在[这个线程](https://news.ycombinator.com/item?id=32028511)上，我们可以发现发生了什么:

> *我曾经有一小组固态硬盘出现故障，因为它们的一些正常运行时间计数器在 4.5 年后溢出，并且不知何故持续破坏了一些内部数据结构。它把它们变成了不可回收的小砖块。看到一堆服务器按照我们最初启动的顺序变暗并不可怕。一点都不好玩。*

[](https://www.techradar.com/news/new-bug-destroys-hpe-ssds-after-40000-hours) [## 新的 bug 在 40，000 小时后破坏 HPE 固态硬盘

### (图片鸣谢:惠普企业)惠普企业(HPE)再次向其发出警告…

www.techradar.com](https://www.techradar.com/news/new-bug-destroys-hpe-ssds-after-40000-hours) 

# 原因

由[戴尔 EMC 固件](https://www.dell.com/support/home/es-ar/drivers/driversdetails?driverid=8h6hj&oscode=w12r2)修复的故障与一个 Assert 函数有关，该函数具有验证循环缓冲区索引值的错误检查。不是检查最大值为 N，而是检查 N-1。修复程序更正了断言检查，将最大值用作 n。

# 预防

我们是严肃的软件工程师，我们有成熟的工具。

我们如何防止这样的缺陷(它们不是[bug](https://mcsee.medium.com/stop-calling-them-bugs-370db4691360))

[](https://codeburst.io/stop-calling-them-bugs-370db4691360) [## 别再叫他们“虫子”了

### 让我们正确地命名事物

codeburst.io](https://codeburst.io/stop-calling-them-bugs-370db4691360) 

# 1 — TDD

使用 [TDD](https://blog.devgenius.io/tdd-conference-2021-all-talks-e1eeef89497e) ，我们只能在失败的测试后编写代码。

这样，我们需要考虑 N 个场景，并显式检查案例。

否则，我们无法编写代码。

TDD 对于嵌入式系统来说非常好。

# 2 —僵尸

[僵尸](https://blog.devgenius.io/how-i-survived-the-zombie-apocalypse-19905db22043)是一个伟大的测试工具，也是一个惊人的 TDD 伴侣。

在*僵尸*中代表边界的“B”告诉我们要明确检查边界情况。

在这种情况下是 N-1、N 和 N +1。

[](https://blog.devgenius.io/how-i-survived-the-zombie-apocalypse-19905db22043) [## 我是如何在僵尸启示录中幸存的

### 选择优秀的测试用例非常困难。除非你召唤亡灵。

blog.devgenius.io](https://blog.devgenius.io/how-i-survived-the-zombie-apocalypse-19905db22043) 

# 3 —突变测试

每当我们使用算术或 [IF 条件](https://blog.devgenius.io/how-to-get-rid-of-annoying-ifs-forever-317033474484)时，我们可能会检查如果我们犯了一个错误(比如本文中的错误)会发生什么，并将<改为< =。

突变测试是检查边界场景的一个非常强大的工具。

# 4-模型循环缓冲器

嵌入式系统和硬件系统经常被调优以获得最佳性能。

它们跳过一些检查，用低级语言编程。

他们大多避免映射真实世界，使用短整数作为索引。

根据我们的[映射器](https://medium.com/@mcsee/what-is-software-9a78c1172cf9)，一个*整数*不是一个*短整型*(或*长整型*)*短整型*不是一个*整数*。

# 5 —快速失败

任务关键型软件有时具有恢复或容错例程。

遵循[快速失败](https://codeburst.io/fail-fast-3f3f036032b0)原则，我们可以预见灾难，让另一段代码接管，而不是阻塞磁盘。

# 结论。

系统不会失败。

作为软件工程师，我们失败了，并且一次又一次地犯同样的错误。

我们需要谦虚，从过去的错误中吸取教训。

# 信用

帕特里克·帕金斯在 [Unsplash](https://unsplash.com/s/photos/disaste) 上拍摄的照片