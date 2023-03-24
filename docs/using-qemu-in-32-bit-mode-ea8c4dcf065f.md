# QEmu 32 位保护模式下的文本

> 原文：<https://levelup.gitconnected.com/using-qemu-in-32-bit-mode-ea8c4dcf065f>

![](img/b1c9c1455afac92d5a3264b9e0b832be.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

最近我一直在关注[这个关于开发的系列，到](https://dev.to/frosnerd/writing-my-own-boot-loader-3mld)从头开始编写你自己的操作系统——它对我一直在做的 web 工作的节奏做了一个很好的改变，从非常接近抽象的顶部到底部。

我确实遇到了一个我在网上其他地方没有看到过的问题，所以我想我应该把它发布给下一个人。

当您切换到 32 位保护模式并更改文本输出方式时，文本应该出现在 VGA 缓冲区的左上角(最初)。

如果你用`-nographic`(即在文本模式下)运行 [qemu](https://www.qemu.org) ，那么**不会发生**，即使它的其余部分——16 位实模式文本——*会出现。所以你的代码可以完美地工作，但是你看不到它。希望这能节省某人一天的调试时间...*

*实际上，我发现 [Bochs](http://bochs.sourceforge.net) 更适合真正的底层调试(引导区代码等)。我还没有一个调试“内核”的好方法，但是 Bochs 有一个从运行代码中输出到控制台的方法。如果我发现了，我会告诉你的！*