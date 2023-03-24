# 动态量子电路，第三课

> 原文：<https://levelup.gitconnected.com/dynamic-quantum-circuits-lesson-3-7b9e29e85daa>

![](img/32e76f1557d0bbfdb6a5071922460091.png)

[https://pix abay . com/vectors/spreadsheets-graph-business-report-24956/](https://pixabay.com/vectors/spreadsheets-graph-business-report-24956/)

# 数据伪结构

动态量子电路在 [IBM 量子峰会 2022](https://medium.com/@bsiegelwax/ibm-quantum-summit-2022-d1c646169189) 上宣布，并在第二天的 [IBM 量子从业者论坛](https://bsiegelwax.medium.com/ibm-quantum-practitioners-forum-2022-67a31a23407f)上展示了一些细节。简而言之，你在将静态量子电路排队运行之前，先设计它们的整体，而在它们排队运行之后和实际运行期间，你使用经典逻辑来生成动态电路。

虽然 OpenQASM 3 支持使用变量，但它还不支持数据结构。那么，如果我们需要的条件逻辑用列表、字典、数组、集合、元组或其他什么来表示，我们能做什么呢？

## 循环逻辑

我们可以使用的一种方法是将数据结构转换成条件逻辑。这样做会让你更加欣赏数据结构，但至少它是可行的。

```
for data_structure_index in range(0, len(data_structure)):
    with qc.if_test(classical_register, data_structure_index):
        qc.something(parameters)
```

在上面的代码中，我们已经实现了中间电路测量，因此我们有一些经典位要分析。我们可以循环遍历所有可能的测量值( *len(data_structure)* )，这些值在十进制中对应于数据结构的索引，如果测量值(*classic _ register*)等于数据结构的索引( *data_structure_index* )，我们将基于数据结构中的数据动态扩展电路。

重要的是要记住，这不是在您的本地 Jupyter 笔记本上完成的。循环通过 *qc.if_test()* 是向您的量子电路添加条件语句，以便当您的电路运行并进行中间电路测量时，经典逻辑就在量子处理器旁边。这就是为什么我们不能只使用 Python 字典，例如，因为字典不能被发送到控制系统。反正还没有。因此，我们必须将字典转换成我们可以发送到控制系统的东西，我们可以实现简单的条件逻辑测试。

## 警告

不要期望数据伪结构能在真正的硬件上发挥很大的作用。你的整个电路必须在量子位的相干时间内执行，这是一个降低电路执行速度的好方法。您可以在模拟器上测试它，并观察当您实现更大的数据结构或增加迭代次数时速度的下降。但是，在真实的硬件上，我们只有不到一秒钟的时间来工作，而且你的量子操作和测量仍然需要消耗一些时间。

## 动态量子电路系列

*   [动态量子电路，第二课](https://medium.com/gitconnected/dynamic-quantum-circuits-lesson-2-d31d7a287c62)
*   [动态量子电路，第一课](https://medium.com/@bsiegelwax/dynamic-quantum-circuits-lesson-1-f712bc6a9e1d)