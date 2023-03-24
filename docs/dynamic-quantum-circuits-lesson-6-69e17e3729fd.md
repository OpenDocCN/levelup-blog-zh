# 动态量子电路，第六课

> 原文：<https://levelup.gitconnected.com/dynamic-quantum-circuits-lesson-6-69e17e3729fd>

![](img/3a37ebf60378e0f8940d5e95347e048d.png)

# 可以不止一个。

动态量子电路在 [IBM 量子峰会 2022](https://medium.com/@bsiegelwax/ibm-quantum-summit-2022-d1c646169189) 上宣布，并在第二天的 [IBM 量子从业者论坛](https://bsiegelwax.medium.com/ibm-quantum-practitioners-forum-2022-67a31a23407f)上展示了一些细节。简而言之，你在将静态量子电路排队运行之前，先设计它们的整体，而在它们排队运行之后和实际运行期间，你使用经典逻辑来生成动态电路。

## 量子机器

我关于动态电路的文章引起了[量子机器](https://www.quantum-machines.co/)的注意。声明我没有亲自使用过 [Qua](https://www.quantum-machines.co/qua-universal-quantum-language/) ，最明显的区别是动态脉冲控制。借助 Qiskit，我们可以动态扩展基于门的量子电路。使用 Qua，您可以向下一层，在脉冲级别做同样的事情。

> QUA 将“原始”格式(脉冲级)的通用量子操作与经典处理(图灵完成)和综合控制流程中使用的通用经典操作统一起来。

Qua 在实时经典处理方面似乎也有更多的灵活性。具体来说，它*可能*有一个比我用 Qiskit 所能找到的更好的方法来实现[数据结构](https://medium.com/gitconnected/dynamic-quantum-circuits-lesson-3-7b9e29e85daa)。

因为量子机器做动态电路的时间更长，这似乎是一种罕见的情况，有人比 IBM 走得更远。希望近期能用上 Qua，适当评价一下。与此同时，请随时联系量子机器，并要求演示。

## IBM 反驳

经过一番挖掘，发现 IBM 现在有潜力使用动态[脉冲控制](https://openqasm.com/language/pulses.html)，但是这种能力还没有经过测试。不管怎样，它已经在 OpenQASM 3 中出现，并且最终会得到支持，但是量子机器在实现和测试方面显然领先。事实上，量子机器的[Dor Israel](https://www.linkedin.com/in/israeli-dor/)是 OpenQASM 3 规范的动态脉冲控制的贡献者，指导规范支持它们现有的能力。

OpenQASM 3 可能还会缩小经典逻辑应用中的潜在差距，但唯一真正的测试是为两个平台设计相同的算法，看看它是否在两个平台上都可行。但是，Quantum Machines 有一个 OpenQASM3-to-Qua 编译器，所以规范只会把它们放在一个平等的基础上。然后，比较将归结为专有特性的实现，这两者似乎都有。

> …我们希望将门级指令连接到由控制器发出的底层微编码刺激程序，以实现每个操作。在 OpenQASM 中，我们通过脉冲级的门定义和用户可选择的脉冲语法的测量来公开这种级别的控制。

## 结论

目前，我不认为 IBM 和量子机器是竞争对手。基于门的量子计算和脉冲控制的量子计算是非常不同的方法。如果你现在想设计基于门的动态电路，你可以使用 Qiskit。如果你现在想设计脉冲控制动态电路，你可以用 Qua。据我所知，只有这两种选择。

最后，我必须说我最喜欢的 Qua 功能是 *play()* 功能。我一直在说和写我玩量子比特，我很高兴看到量子机器欣赏这一点。

## 动态量子电路系列

*   [动态量子电路，第五课](https://bsiegelwax.medium.com/dynamic-quantum-circuits-lesson-5-ed3d24101dd0)
*   [动态量子电路，第四课](https://bsiegelwax.medium.com/dynamic-quantum-circuits-lesson-4-4527c4527a2c)
*   [动态量子电路，第三课](https://bsiegelwax.medium.com/dynamic-quantum-circuits-lesson-3-7b9e29e85daa)
*   [动态量子电路，第二课](https://medium.com/gitconnected/dynamic-quantum-circuits-lesson-2-d31d7a287c62)
*   [动态量子电路，第一课](https://medium.com/@bsiegelwax/dynamic-quantum-circuits-lesson-1-f712bc6a9e1d)