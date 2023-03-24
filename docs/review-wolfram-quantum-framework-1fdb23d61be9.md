# 综述:Wolfram 量子框架

> 原文：<https://levelup.gitconnected.com/review-wolfram-quantum-framework-1fdb23d61be9>

![](img/277be6c25aee99268dd7f4937e619f3a.png)

[https://www.wolframcloud.com/](https://www.wolframcloud.com/)

# 基于提交的 Classiq 编码竞赛

最近 [Classiq 编码竞赛](https://www.classiq.io/competition)的一份提交文件很早就公开了。[解决方案](https://www.wolframcloud.com/obj/nikm/Published/KakuroFinal.nb)是使用 [Wolfram 量子框架](https://www.wolframcloud.com/)生成的，乍看之下，它看起来像是 [Classiq 的量子算法设计平台](/demo-classiqs-qad-platform-f0bec3608549)的竞争对手。我认为如果解决方案获胜，Classiq 将面临一个非常有趣的困境。然而，经过更仔细的研究，我不再认为它是一个竞争框架。

## 好人

**约束确定。**挑战在于解决一个很容易定义为矩阵的难题。尽管这个难题是手工解决的(见下面的“丑陋的”),但是框架从解决方案中提取了解决这个难题所需的约束。如果你正在学习如何解决的一类难题碰巧有解决方案，这可能会很有用。

**约束设置。我们通常不会为我们已经知道答案的问题设计量子电路。嗯，在开始时，我们将验证他们的工作。但是，对于实际应用，我们需要定义一个问题的约束，并试图解决一个未解决的问题。就如何做到这一点而言，该框架可能与我见过的其他框架不相上下。**

**高效电路。我应该把它排在更高的位置，但是我是按照我写下来的顺序列出来的。该框架不仅自动创建了 Grover 的 oracle，而且效率非常高。我的意思是，声称的完全分解的 CNOT 数比我从任何框架中期望看到的要低得多。这可能是整个评论的关键。**

**图表丰富。**在某个时候，笔记本开始看起来不像是对算法的解释，而更像是对框架及其功能的展示。然而，确实有一个很好的可视化的选择。

## 坏事

**框架测试。**一些代码似乎在验证框架本身正在工作。毕竟，在笔记本的顶部有一条消息表明框架仍在开发中。我指的是对预先确定的测量结果进行多次检查，以及在测试整个问题之前测试一个较小的问题。有了框架，我希望只提供一个表达式就可以了。

**算法不完整。**框架生成了 Grover 的预言，这无疑是构建 Grover 迭代的困难部分。但是，值得注意的是，扩散算子是单独生成的。其他框架允许您为 oracle 指定表达式，但是它们会从那里接管并生成完整的电路。

## 丑陋的

**手动解。**手动解谜，然后从那里开始搭建电路。虽然这并不违反规则，但这意味着格罗弗的算法实际上并没有解决这个难题。框架产生了一个回路，没错，但是没有解决问题。如果它的演示真的解决了问题，我会对这个框架更感兴趣。

**Qiskit 集成。**框架没有生成整个提交。例如，Qiskit 用于门分解，这是一个竞赛要求。因此，如果没有 Qiskit，一个只有框架的提交可能会被取消资格。

**部分可用。我注册使用这个框架，然后开始一行一行地浏览笔记本。所有的电路图都不起作用，建议的安装也无法安装，所以我停下来。**

## 结论

乍一看，我认为 Wolfram 量子框架可能是一个真正的游戏规则改变者，可以轻松地合成高效的电路。然而，仔细研究后，它肯定需要更多的发展。尽管有 Qiskit 的帮助，但由于最终的电路很浅，这个框架还是值得一看的。它将走向何方？

# # ClassiqCodingCompetition 竞赛系列

*   [新大门上了座](https://bsiegelwax.medium.com/new-gates-on-the-block-9cad1bc583fd)
*   [我从 Classiq 的编码竞赛中学到了什么](https://bsiegelwax.medium.com/what-i-learned-from-classiqs-coding-competition-9ebfbb6816bb)
*   [Classiq 的哈密顿问题](https://bsiegelwax.medium.com/classiqs-hamiltonian-problem-31e2992903d0)
*   [Classiq 的分配问题](https://bsiegelwax.medium.com/classiqs-distribution-problem-8e3c7a74afaa)
*   [Classiq 的托夫里问题](https://bsiegelwax.medium.com/classiqs-toffoli-problem-54b7e5084833)
*   [Classiq 的可满足性问题](https://bsiegelwax.medium.com/classiqs-satisfiability-problem-c8e78502f82b)
*   [非安西利亚 MCX](/no-ancilla-mcx-e59f455bb9f6)
*   [回顾:Wolfram 量子框架](/review-wolfram-quantum-framework-1fdb23d61be9)
*   [一个土生土长的托夫里门](/a-native-toffoli-gate-970093e4770c)