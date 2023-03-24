# 动态量子电路，第 7 课

> 原文：<https://levelup.gitconnected.com/dynamic-quantum-circuits-lesson-7-f2c3e8f391e>

![](img/46990b1da67174230294ae5a997b2c82.png)

[https://pix abay . com/photos/recycling-bins-recycle-environment-373156/](https://pixabay.com/photos/recycling-bins-recycle-environment-373156/)

# 可重复使用的经典寄存器

动态量子电路在 [IBM 量子峰会 2022](https://medium.com/@bsiegelwax/ibm-quantum-summit-2022-d1c646169189) 上宣布，并在第二天的 [IBM 量子从业者论坛](https://bsiegelwax.medium.com/ibm-quantum-practitioners-forum-2022-67a31a23407f)上展示了一些细节。简而言之，你在将静态量子电路排队运行之前，先设计它们的整体，而在它们排队运行之后和实际运行期间，你使用经典逻辑来生成动态电路。

动态电路的主要部分是进行中间电路测量，然后将条件逻辑应用于这些测量结果。如果我们关心那些中间电路测量是什么，我们需要将每组测量分配给它自己的[经典寄存器](https://medium.com/gitconnected/dynamic-quantum-circuits-lesson-5-ed3d24101dd0)。然后，我们可以在最后检索一个很长的位串，然后解析它以确定每个度量是什么。

但是，如果我们不关心中间电路的测量呢？除了测量、重置和重用量子位，我们还可以重用经典位。

```
qr = QuantumRegister(1)
cr = ClassicalRegister(1, name="cr")
qc = QuantumCircuit(qr, cr)
qc.x(0)
qc.measure(0, cr)
with qc_reset.if_test((cr, 1)):
    qc.x(0)
qc.measure(0, cr)
```

上面是一个非常简单的测试，但它在真实的硬件上工作。第 2 行仅初始化一个经典位。第 5 行测量量子位 0 到那个位。然后第 8 行测量量子位 0 到相同的经典位。中间电路测量被破坏，我们只检索最终测量。

## 结论

如果你不在乎你的中间电路测量是什么，重用经典寄存器的主要好处是最终的测量将更可读，你可能不必做任何位串解析。你可以直接进行经典的后处理和可视化。

## 动态量子电路系列

*   [动态量子电路，第六课](https://bsiegelwax.medium.com/dynamic-quantum-circuits-lesson-6-69e17e3729fd)
*   [动态量子电路，第五课](https://bsiegelwax.medium.com/dynamic-quantum-circuits-lesson-5-ed3d24101dd0)
*   [动态量子电路，第四课](https://bsiegelwax.medium.com/dynamic-quantum-circuits-lesson-4-4527c4527a2c)
*   [动态量子电路，第三课](https://bsiegelwax.medium.com/dynamic-quantum-circuits-lesson-3-7b9e29e85daa)
*   [动态量子电路，第二课](https://medium.com/gitconnected/dynamic-quantum-circuits-lesson-2-d31d7a287c62)
*   [动态量子电路，第一课](https://medium.com/@bsiegelwax/dynamic-quantum-circuits-lesson-1-f712bc6a9e1d)