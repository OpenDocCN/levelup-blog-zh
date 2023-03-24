# 免费学习量子计算的 4 个平台

> 原文：<https://levelup.gitconnected.com/4-platforms-to-learn-quantum-computing-for-free-c7390c925e57>

## 不需要物理背景！(但好奇心会有所帮助)

![](img/41ed19dba455529865fee8324ab66e6b.png)

来自 [Pixabay](https://pixabay.com/illustrations/physics-quantum-physics-particles-3871218/)

问量子计算结合了计算机科学和物理学，早在 1980 年就开始了，但最近在硬件和系统工程方面取得的进展引起了很多关注。电气和电子工程师协会(IEEE)最近发表了一篇与谷歌梧桐前首席架构师约翰·马丁尼斯(John Martinis)的讨论。讨论试图解决这个问题— [**量子计算实际上进步了多少？**](https://spectrum.ieee.org/quantum-computing-google-sycamore)

虽然量子计算的不远的将来有待讨论，但毫无疑问，商业上可行的量子计算机的发展有可能彻底改变现代社会。许多经典计算机难以解决的问题，如精确模拟化学反应，可以由量子计算机解决。这可能会导致更容易回收的塑料、环保肥料的开发，甚至打破我们常见的加密协议，如 RSA。

对量子计算的公共和私人投资都创下新高。许多大型科技公司正在发布开源框架，以便自己探索量子计算，无论是在本地模拟还是在实际硬件上。虽然物理学背景可能有用，但这不是必需的。您可以学习量子力学的定律，同时使用为利用量子领域的奇怪行为而构建的编程语言。

> 使用以下任何选项，您都可以学习如何操纵量子位(量子位)，通过叠加实现真正的随机性，或者实现基本的量子算法。抓住一杯咖啡和一些空闲时间，探索如何自己学习量子计算！

# 排名第一的是微软的 Azure Quantum

Azure Quantum 为量子爱好者和专业人士提供各种服务。他们最近推出了一项促销活动，可以获得价值 500 美元的计算积分，在真正的量子硬件上运行——你需要做的只是注册一个新账户！

Azure Quantum 上可用的资源列表包括量子开发工具包(QDK)、量子编程语言 Q#以及受量子算法启发的优化方法。微软已经与 Quantinuum 和 IonQ 等公司合作，这两家公司都使用捕获离子架构来实现自己的量子计算机。使用 Azure Quantum，您可以将程序作为本地模拟运行，也可以直接在量子计算机上运行！

AQ 的另一个优点是他们的量子卡塔斯学习资源。卡塔是一套用于互动学习的 Jupyter 笔记本。您将了解量子计算的原理，然后实现代码以通过测试(如果遇到困难，可以使用解决方案和提示)。很快，你就会写出如下的量子程序:

Azure Quantum 于 2021 年 2 月推出，欢迎对其开源库做出贡献。太平洋标准时间每周四上午 8:30 甚至还有办公时间。您可以在此了解有关 Azure Quantum 的更多信息:

[](https://devblogs.microsoft.com/qsharp/explore-quantum-hardware-for-free-with-azure-quantum/) [## 使用 Azure Quantum 免费探索量子硬件

### 如果你一直对探索量子硬件感到好奇，那么今天就开始使用 Azure Quantum 吧！你是否…

devblogs.microsoft.com](https://devblogs.microsoft.com/qsharp/explore-quantum-hardware-for-free-with-azure-quantum/) 

# #2 — IBM 的 Qiskit

用于量子计算的量子信息软件包(Qiskit)是一个开源的 SDK，用于在脉冲、电路和应用模块的级别上与量子计算机一起工作。除了全面的计算选项列表，Qiskit 还提供量子机器学习功能和电路设计的详细工作流程。下面列出了用 Python 编写的一个简单的电路构建和打印程序:

这将打印出:

```
┌───┐          ┌─┐
q_0: ┤ H ├───────■──┤M├───
     ├───┤┌───┐┌─┴─┐└╥┘┌─┐
q_1: ┤ X ├┤ H ├┤ X ├─╫─┤M├
     ├───┤└┬─┬┘└───┘ ║ └╥┘
q_2: ┤ H ├─┤M├───────╫──╫─
     └───┘ └╥┘       ║  ║
c: 3/═══════╩════════╩══╩═
            2        0  1
```

随着电路配置变得越来越复杂，您还可以进一步定制，以获得更多细节。Qiskit 也是开源的，拥有广泛的开发者资源，并在 GitHub 上列出了他们的许多资源库。点击此处了解更多关于 Qiskit 的信息:

[](https://qiskit.org/) [## Qiskit

### Qiskit 包括一套全面的量子门和各种预建电路，因此各级用户都可以使用…

qiskit.org](https://qiskit.org/) 

# **# 3——谷歌的 Cirq**

Cirq 于 2018 年 8 月发布，是一个 Python 库，用于编写、操纵和优化在量子计算机或模拟上运行的量子电路。Google 的框架假设用户对量子计算相当熟悉，建议初学者在阅读 Cirq 文档之前先学习基础知识。总的来说，教程没有跳过数学，并且比其他选项有更陡峭的学习曲线。

总的来说，Cirq 在外展和教育方面的投资较少，他们的硬件需要获得批准才能使用。无论如何，谷歌有自己独特的产品和范例值得探索:

[](https://quantumai.google/cirq) [## Cirq |谷歌量子人工智能

### 导入 cirq #选择一个量子位。量子位= cirq。GridQubit(0，0) #创建一个电路 circuit = cirq。电路(电路质量。x(量子位)**0.5…

quantumai.google](https://quantumai.google/cirq) 

# # 4——亚马逊的 Braket

Braket 提供了一个构建、测试和运行量子算法的单一环境。Braket 符号或 Dirac 符号用于表示量子态。Amazon Braket 提供 Jupyter 笔记本，附带预装的开发人员工具、示例应用程序和教程，可以快速入门。亚马逊还与 IonQ 等顶级公司合作，这样你就可以自己在硬件上运行你的程序。

和这个列表中的其他例子一样，亚马逊在 GitHub 上托管 Braket SDK，并鼓励对其开源库做出贡献。现有的量子硬件处于嘈杂的中间尺度量子(NISQ)时代。目前，量子设备噪声太大，无法实现纯粹的量子算法，如 Shor 的大数因子分解算法或 Grover 的高效搜索算法。Braket 将经典处理器与量子处理器结合起来，以加速经典算法的特定计算，尽管他们也有纯量子算法的例子。

你可以在这里试试 Braket:

[](https://aws.amazon.com/braket/) [## 量子计算服务-亚马逊市场-亚马逊网络服务

### 亚马逊 Braket 每月 1 小时免费模拟时间轻松工作与不同类型的量子计算机和…

aws.amazon.com](https://aws.amazon.com/braket/) 

这些选项都有自己独特的产品，但有一点是相同的。量子计算已经进入了一个新的实验时代，世界各地的政府和公司都在竞相评估一台完全有能力的量子计算机到底有多有形。通过强调开源框架和社区，公司希望与爱好者和专业人士合作。在这样一项未来技术的早期形成阶段，为什么不自己尝试一下量子计算呢？

感谢你阅读我的文章，我希望你觉得这个列表有用。如果你想直接支持我的写作，你可以使用下面我的推荐链接注册 Medium。

[](https://israel-miles.medium.com/membership) [## 加入我的推荐链接-以色列万里行

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

israel-miles.medium.com](https://israel-miles.medium.com/membership)