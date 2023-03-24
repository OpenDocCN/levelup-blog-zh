# 量子汇编语言 101

> 原文：<https://levelup.gitconnected.com/quantum-assembly-language-101-4244f2bb2848>

![](img/3052dc1fde08462fd37b5066eeb36de3.png)

[https://commons . wikimedia . org/wiki/File:IBM _ Q _ system _(Fraunhofer _ 2)。jpg](https://commons.wikimedia.org/wiki/File:IBM_Q_system_(Fraunhofer_2).jpg)

# 简单。直觉。强大。

从美学角度来看，量子汇编语言(QASM)是 C 和汇编语言的完美结合。需要学习的命令非常少，如果您目前使用 Qiskit 之类的量子计算框架，您已经知道其中的大部分。然而，与那些框架使用的经典语言不同，没有任何操作与量子处理器或者至少是量子计算模拟器无关。使用 QASM 将你限制在量子计算上，除了量子计算什么都没有，这也是我推荐的量子计算方式。

QASM 被[转化为可以在量子处理器上执行的基本运算。这些基本操作随后被实现为微波脉冲，至少在超导 transmon 量子位的情况下，如 IBM、Google 和 Rigetti 所使用的那些。QASM 简单地定义了用户友好的操作来构建电路，因为单独使用基本操作——你当然可以尝试这样做——是相当具有挑战性的。](/what-is-transpilation-4d12d51e2aa4)

本文特别使用 OpenQASM，它是 IBM 的 QASM 实现。QASM 的其他实现非常相似；实现之间的转换应该只需要一两分钟。但是，您很可能会发现其他实现更加有限，因此，与新操作相反，可能会有一些约束需要学习。

## 为什么是 QASM？

Python 是一种经典语言。Q#也是。所有其他可能适用于量子计算的语言也是如此。因此，你使用经典思想来设计你的算法，并建立你的量子电路。

另一方面，QASM 迫使你定量思考。套用尤达的话来说，我们必须忘掉我们所学的东西。随着时间的推移，量子计算变得非常直观。事实上，如果我使用 OpenQASM 足够长的时间，我会导出到 Qiskit，并且很难再进行经典思考。

我建议使用 QASM 编辑器构建所有电路。只有当你绝对地、肯定地不能再前进时，才导出一个框架。经典地只做你不能有效地定量做的事情。

事不宜迟，让我们看一些代码…

## 不要碰！

在每一个开口的顶端。qasm 文件是两行特定代码。当你找到它们的时候就把它们留下。“include”文件 qelib1.inc 定义了您将在本文中读到的操作，因此您不必自己定义它们。其他 QASM 实现可能完全包含在各自的开发环境中，不一定需要任何代码行，但是 OpenQASM，正如“open”所暗示的，并不特定于任何一个环境。如果您尝试一个新的平台并看到 OpenQASM，这个“包含”文件有助于确保它是您将逐渐了解并喜欢的同一个 OpenQASM。

```
OPENQASM 2.0;
include “qelib1.inc”;
```

## 评论

如果你曾经做过任何类型的编程，你就会知道自由地注释你的代码是明智的。无论你想在单独一行还是在另一行的末尾留下评论，只需用两个正斜杠开始你的评论。

```
// This is a standalone comment.h q[0]; // This applies a Hadamard gate to qubit 0.
```

## 登记

有两种寄存器，量子寄存器和经典寄存器。 *qreg* 语句初始化量子寄存器，即量子位，而 *creg* 语句确定我们希望在测量结果中包含多少经典位。

```
qreg name[size]; // Initialize a quantum register.creg name[size]; // Initialize a classical register.
```

有一些没有记录的命名规则很容易被发现；错误将被加下划线。例如，*爱丽丝*是不允许的，但是*爱丽丝*是允许的。物理量子位是按顺序分配的，所以如果爱丽丝[2]在鲍勃[3]之前，爱丽丝将被分配量子位 0 和 1，鲍勃将被分配量子位 2、3 和 4。我还没有发现对你可以命名的寄存器数量的任何限制，但是如果你试图初始化比你可用的更多的量子位，当你试图运行它时，任务将不会排队。在 IBM Quantum 中，有一条错误消息显示得非常短暂，因此您更有可能注意到作业从未完成，然后当您调查时，您会注意到作业从未排队。

```
qreg alice[2]; // quantum register: Alice has qubits 0 and 1
qreg bob[3]; // quantum register: Bob has qubits 2, 3, and 4
creg mid[2]; // classical register: mid-circuit measurements
creg final[3]; // classical register: final measurements
```

## 基本操作

虽然您通常会发现比您在这里所读到的更多的操作，但我将只讨论其中的要点。你可以用非常少的门电路构建你可能需要的所有电路。

**U**

U gate 是您转换单个量子位的一站式商店。虽然自变量的顺序是 theta，phi，lambda，但它们应该是 lambda，theta，phi。实际旋转是绕 z 轴的λ弧度，然后是绕 y 轴的θ弧度，然后是绕 z 轴的φ弧度。大家可以看到，如此多的其它操作，例如 S、T 和 Z 门，都可以用 U 门和 phi 或 lambda 旋转来代替。

声明:你仍然可以使用你最喜欢的门，比如 H 和 X，我只是说仅仅学习和掌握一个单量子比特操作是可能的。

```
U(theta,phi,lambda) qubit|qreg;EXAMPLE: U(pi/2, 0, pi) q[0]; // Rotate qubit 0 1/2 around the z axis then 1/4 around the y axis; it's a Hadamard gate!EXAMPLE: U(pi/2, 0, pi) q; // Rotate all qubits in the q register 1/2 around the z axis then 1/4 around the y axis; apply Hadamards to an entire register.
```

**CU3**

CU3 gate 是您控制转变的一站式商店。目标量子位的旋转与 U 门的描述完全一样。但是，目标量子位只有在控制量子位为 1 时才会转换。

声明:和 U gate 一样，我不会介意你使用更简单的语句，比如 CX 和 CZ；同样，我只是指出，你可以学习和掌握仅仅一个多量子比特操作。

```
CU3(theta,phi,lambda) control qubit|qreg, target qubit|qreg;EXAMPLE: CU3(pi, 0, pi) alice[0], bob; // If Alice’s qubit 0 is 1, rotate all of Bob’s qubits 1/2 around the z axis and then 1/2 around the y axis; it's a CX, aka CNOT, aka controlled-X, aka controlled-NOT!
```

**CCX**

CCX 门也被称为托夫里门、受控-受控-X 门、受控-受控-非门、和门。它实现布尔和逻辑，并用于构建涉及两个以上量子位的自定义操作。从技术上讲，你可以用多个 U 门和 CU3 门来实现 CCX，但是 CCX 更容易使用，值得包含在内。

```
ccx control qubit|qreg, control qubit|qreg, target qubit|qreg;EXAMPLE: ccx q[0], q[1], q[2]; // If qubit 0 and qubit 1 are both 1, rotate target qubit 2 1/2 around the x axis.
```

**测量**

这就是我们如何从量子位中提取信息。如果量子寄存器和经典寄存器的大小相同，就有捷径可走。

```
measure qubit|qreg -> bit|creg;EXAMPLE: measure q[0] -> c[0]; // Measure qubit 0 to classical bit 0.EXAMPLE: measure q -> c; // If q and c are the same size, measure all qubits in quantum register q to classical register c.
```

## 中间操作

以下操作不太常见，但它们有令人兴奋的应用。

**CSWAP**

受控交换，又名 Fredkin，有[量子机器学习(QML)](/comparing-quantum-states-c6445e1e46fd) 应用，如[分类](https://medium.com/swlh/quantum-classification-cecbc7831be)和[聚类](/quantum-clustering-c498b089b88e)。这些都超出了本文的范围，但是请随意访问这些链接。

```
cswap control qubit|qreg, control qubit|qreg, target qubit|qreg;EXAMPLE: cswap q[0], q[1], q[2]; // If control qubit 0 is 1, SWAP target qubits 1 and 2.
```

**屏障**

因为我写这些文章，所以我主要使用栅栏来使电路更美观，并以这样一种方式清楚地划分电路，以便于解释每个部分在做什么。然而，在运行一个电路之前，移除障碍通常是一个好主意，这样 transpiler 可以优化您的电路。然而，您可能仍然希望将屏障用于非美学目的。例如，您可以使用隔离栅来防止传输器优化电路的某些部分，以使操作和测量更接近的方式延迟操作，从而最大限度地减少退相干。

```
barrier qargs;EXAMPLE: barrier q[0]; // Place a barrier on an individual qubit.EXAMPLE: barrier alice, bob; // Extend a barrier through Alice's and Bob's qubits.
```

**复位**

重置门不仅允许你回收和再利用量子位，它们还允许你实现[伪条件逻辑](/quantum-pseudo-conditional-logic-8134ddff1063)操作。IBM 将在 OpenQASM 3 中正式允许条件逻辑，但是在此期间，您可以通过 reset gates 非正式地实现相同的效果。

```
reset qubit|qreg;EXAMPLE: reset q[0]; // Reset one specific qubit.EXAMPLE: reset q; // Reset all qubits in the q register.
```

## 高级操作

这些操作通常以传统方式执行，但也可以在电路运行时执行。将作业发送到量子处理器并运行一次，比运行作业、检索结果、运行一些经典代码、运行另一个作业、检索结果等等要快。每次从稀释冰箱到芯片来回都浪费时间。

**子程序**

IBM Quantum 使用术语“子例程”，所以我将在这里使用这个术语。然而，根据您选择的编程语言，您会发现它的用法是多种同义名称。我在这里可以互换使用的另一个术语是“自定义门”，因为在量子计算术语中，它本质上就是简单的子例程。

想象一下，我想通过应用一系列交替的 H 和 T 门，将一个量子位置于任意的量子态。例如，我可以定义一个自定义的门控 ht，应用序列的一次迭代。这当然不是很有想象力，但关键是你可以定义任何你可以构建的自定义门。

当然，子程序并不局限于简单的操作。它们的复杂性大多受限于你的想象力。但是，我建议慢慢构建它们，因为有一些未公开的限制。例如，除非他们改变了这一点，否则我在调用子例程之前必须应用重置门，因为重置门在子例程中不起作用。因为那是未发表的，我并不声称知道每一个未发表的限制。

同样值得注意的是，你可以嵌套子程序。换句话说，一个子程序可以调用另一个。

```
gate name(params) qargs { body }EXAMPLE: gate ht a { h a; t a; } // Define a custom gate ht that accepts a qubit or quantum register "a" as an argument and applies a Hadamard gate then a T gate in sequence.gatename(params) qargs;EXAMPLE: ht q[0]; // Apply custom gate ht to qubit 0.
```

**用于循环**

尽管 QASM 没有 FOR 语句，但调用带有量子寄存器的子例程具有运行 FOR 循环的效果。子例程应该在执行期间迭代通过该寄存器的每个量子位。

```
gatename qreg;EXAMPLE: g qr1; // Apply custom gate g to every qubit of quantum register qr1.EXAMPLE: g qr0[0], qr1, qr2[0], qr3; // Run subroutine g with the 0th qubit of quantum register qr0, the 0th qubit of quantum register qr2, and all of the qubits in quantum registers qr1 and qr3\. Disclaimer: this only works if qr1 and qr3 are the same size.
```

**条件逻辑**

你还不能在 IBM 量子处理器上实现条件逻辑，尽管你可以实现我称之为[的伪条件逻辑](/quantum-pseudo-conditional-logic-8134ddff1063)。同时，您可以在模拟器中练习使用 IF 语句。

值得注意的是，条件运算可以是任何量子运算。与 QASM 的任何其他行不同的是，只有在满足所提供的条件时才应用该操作。

此外，还使用了整个经典音域。例如，如果“syn”经典寄存器的大小为 2，那么它可以接受 2 个测量值。虽然测量值是二进制的，但条件是十进制值的十进制表示。1 量子位寄存器接受 0 (0)和 1 (1)作为条件，而 2 量子位寄存器接受 0 (00)、1 (01)、2 (10)和 3 (11)作为条件，以此类推。

```
if(creg==int) qop;EXAMPLE, PART 1: measure q[0] -> syn[0]; measure q[1] -> syn[1]; // Measure qubits 0 and 1 to syndrome bits. Syndrome bits are introduced with Quantum Error Correction (QEC), but they are not limited to that role. In this example, they would've been defined with creg syn[2]; earlier in the code. As with all registers, you can use any valid name.EXAMPLE, PART 2: if(syn==3) x q[1]; // If the syndrome bits measured 11, which is binary for 3, apply a Pauli-X gate to qubit 1\. 
```

**延伸阅读**

OpenQASM 的最佳资源是[规范](https://arxiv.org/pdf/1707.03429.pdf)和[操作词汇表](https://quantum-computing.ibm.com/composer/docs/iqx/operations_glossary)。编码快乐！