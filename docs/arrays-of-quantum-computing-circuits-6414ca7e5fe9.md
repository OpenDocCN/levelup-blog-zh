# 量子计算电路阵列

> 原文：<https://levelup.gitconnected.com/arrays-of-quantum-computing-circuits-6414ca7e5fe9>

![](img/b21980478e94e3390204aaaf224c2e7a.png)

[pixabay](https://pixabay.com/photos/foodie-cheeses-california-gourmet-4641616/)

# 有什么是 Qiskit 做不到的？

我发表这篇文章是因为不久前我还不知道构建量子计算电路阵列是可能的。回想起来，考虑到 Python 语言所能做的一切，这真的不应该让我感到惊讶。然而，我不得不问一些问题，做一些故障排除，所以我会试着为阅读这篇文章的人简化这个学习过程。

## 介绍

我看过的所有量子计算教程和文章，都是只展示一个电路的构造。你建造它，然后运行它。但是，如果你想测试不同的输入状态或不同的旋转或不同的门或其他什么，而你又不想整天坐着点击“run”呢？

事实证明，你不必。您可以动态构建多个电路，将它们添加到一个数组中，单击“运行”，然后去喝杯#qoffee 热咖啡。

## 放弃

虽然下面的代码可以工作，但我相信还有更好的方法。我只需要让它工作，以便测试我设计的电路。

## 第 1 步，共 3 步

qc_list = []

## 第 2 步，共 3 步(重复)

qc_list.append(电路)

## 第 3 步，共 3 步

back end = basic aer . get _ back end(' qasm _ simulator ')
trans piled _ QC =[trans pile(circuit，back end)for circuit in QC _ list]
job _ list =[back end . run(assemble(trans piled _ QC))。
trans piled _ QC]中 qobj 的结果()print(job.get_counts())

## 结论

就是这样。这就是我所需要的。它在 IBM Quantum Jupyter 笔记本中工作。每个单独的回路都是正常构建的，然后附加到一个列表中，然后列表中的所有回路都作为一个批处理作业运行。

## 承认

本文中的代码是通过以下方式汇编的:

*   Paul Nation 和 Jack Woehr 通过 Qiskit Slack 频道
*   多个 Reddit 线程
*   https://medium . com/qi skit/qi skit-backends-what-them-and-how-to-work-with-them-FB 66 B3 BD 0463
*   https://github . com/qi skit/qi skit-terra/blob/master/readme . MD

## 内部笑话

我在 Twitter 上的长期追随者知道，我更喜欢 Qiskit 的发音是“cheese kit”，因此出现了上面的奶酪图片。

这可以追溯到 Twitter 上的一个帖子，开始是我询问 Qiskit 的官方发音。当时还没有正式的读音，建议了四五种不同的读音。其中一个发音是“奶酪包”你想想，气读作“气”，气就是能量，量子计算从根本上讲就是能量，所以说得通。现在有一个 Qiskit 的官方发音，虽然它不是“奶酪包”,但只要有机会，我就想为它的存在邀功…