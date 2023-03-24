# 决策树和过度拟合:简化的困难概念

> 原文：<https://levelup.gitconnected.com/decision-trees-and-overfitting-difficult-concepts-simplified-e02c6943c7cc>

## 决策树算法可能变得非常困难:简单的解释

![](img/e3235c601b253526bd8b29e84c5fd99e.png)

来自 Unsplash 的 Fabrice Villard

如今，人工智能不再是一扇紧闭的大门。有多少“免费”或在线培训？—我们都数不清了。

我计划创建一个端到端的人工智能介绍(不特定于机器学习、NLP 或深度学习)。在我进一步发展人工智能介绍之前，我发现现在探索学习决策树算法的时机已经非常成熟了。

正如我在大多数情况下通常做的那样，如果你想深入研究算法，你最好购买一本全面的教科书。或者，你从这些高层次的介绍开始。你想在同一个算法问题空间中进行编码吗？这是一个单独的努力(多部分系列)，至少在我写作的背景下。举个例子，我在 SciPy 上做过一个多部分的文章:我的第二篇文章是以编码为中心的(这里是[1])。

让我们开门见山吧。

**算法目标**

决策树方法通常用于有监督的学习问题(我省略了无监督的决策树实现[4])。)其目的是从训练数据中学习模型，该模型可用于根据尚未看到的测试数据生成预测。决策树是机器学习技术的一种形式，用于回归和分类问题。决策树的核心原理是将特征空间划分成区域，然后利用这些区域来预测目标变量。

![](img/b0f36e8de069fd56938c80563979a0da.png)

从 Pexels 中提取产品

**类型(举几个例子)**

决策树算法有许多不同的**类型**；然而，CART(分类和回归树)和 ID3(迭代二分法 3)经常被使用。在这些算法的每一个中，数据集沿着一个或多个特征递归地切片，直到它们到达每个结果区域包括具有相同目标值的样本的点[2][3]。

**工作原理**

决策树的概念可以用非常简单的方式来理解。目标是生成一个模型，该模型最小化与使用训练好的模型对新数据点进行预测相关联的一些成本函数。为了实现这一点，我们需要在我们的数据中找到最大化某个标准的分裂，如信息增益(IG)或基尼系数。当我们已经识别出这些最佳分裂时，我们将能够通过遵循决策树的分支来构建我们的最终模型，该分支导致在树的每个叶节点处每个类别标签的最高可能预测概率。

决策树通过从数据中学习一系列 if-then-else 条件来进行预测。树中的每个条件(如根节点、内部节点或叶节点)都指向一组具有新条件的新节点。这一直持续到尝试了所有可能的结果(“拟合”[5])。在对过去的数据进行训练后，模型可以在给定新数据时判断出它以前从未见过的树中的哪条路径会导致什么结果(这种方法使用训练期间使用的特定示例来分类或预测模型最初创建时没有想到的新值)。最后，决策树算法通过将输入和输出分解成更小的部分，构建成最符合证据的函数，来学习它们之间的复杂关系。

![](img/b5a8c74c1e29ddac2b618c040eba6720.png)

来自 Unsplash 的 Jake Melara

**过度装配问题和缓解措施**

过度拟合是机器学习中出现的一个问题，具体表现为模型在训练数据上表现良好，但不能很好地推广到新的[9]样本。当模型对于所使用的数据来说过于复杂时，这种情况经常发生(但不限于此)。因为对决策树算法学习新模式的能力几乎没有限制，所以它们特别容易过度拟合。

当使用决策树算法时，可以通过多种不同的方式来防止过拟合，其中一些方式如下:

—预修剪是一种在树达到特定尺寸或深度限制时防止其生长的技术[7]。

—后剪枝需要增长整个树[8]，之后，不必要的节点或几乎相同的节点被逐一删除，直到只留下优化性能的节点。在被修整和修剪之后，所讨论的树在其大小和解释方面将更易于管理，并且这将在不过度损失精度的情况下发生。

— K-fold:在尝试了将数据集分为训练集和测试集的各种策略之后，您应该确定每次单独运行的性能水平，然后取这些结果的平均值。

— Bootstrap aggregating:在原始数据集的随机子样本上拟合多个模型(替换)；之后，通过平均或投票来组合他们的预测[6]。

![](img/eb4c0d6808b757cf313f3da037833a9e.png)

出自[作者](https://medium.com/@aniltilbe)【10】

# 离别的思绪

如果你对这篇文章有任何建议或拓宽主题的建议，我将非常感谢你的来信。

此外，我还写了以下帖子，你可能会感兴趣:

[](/top-20-machine-learning-algorithms-explained-in-less-than-10-seconds-each-8fd728f70b19) [## 前 20 个机器学习算法，每个用不到 10 秒钟解释

### 对 20 个最重要的机器学习算法的简单解释，每个都在 10 秒内完成。

levelup.gitconnected.com](/top-20-machine-learning-algorithms-explained-in-less-than-10-seconds-each-8fd728f70b19) [](https://uxplanet.org/simplest-artificial-intelligence-guide-top-15-models-with-10-second-explanations-13325967d322) [## 最简单的人工智能指南:10 秒钟解释的 15 大模型

### 对 15 个最重要的 NLP、机器学习和深度学习模型的简单解释，全部在 10 秒钟内完成…

uxplanet.org](https://uxplanet.org/simplest-artificial-intelligence-guide-top-15-models-with-10-second-explanations-13325967d322) [](/top-7-deep-learning-methods-each-explained-in-less-than-10-seconds-3683120de455) [## 7 大深度学习方法，每种方法用不到 10 秒钟的时间解释

### 对 7 个最重要的深度学习算法的简单解释，每个都在 10 秒内完成。

levelup.gitconnected.com](/top-7-deep-learning-methods-each-explained-in-less-than-10-seconds-3683120de455) 

如果你喜欢阅读这样的故事，并想支持我成为一名作家，可以考虑报名成为一名媒体成员。每月 5 美元，可以无限制地阅读媒体上的所有报道。如果你注册使用我的链接，我会赚一小笔佣金，不需要你额外付费。

[](https://medium.com/@AnilTilbe/membership) [## 通过我的推荐链接加入 Medium-Anil til be

### 阅读阿尼尔·蒂尔贝(以及媒体上成千上万的其他作家)的每一个故事。你的会员资格直接支持作家…

medium.com](https://medium.com/@AnilTilbe/membership) 

还有，这是我的时事通讯；我希望你能考虑订阅。

[](https://predictiveventures.substack.com) [## 预测风险简讯

### 人工智能和产品的交集。让我先看看预测风险投资时事通讯…

predictiveventures.substack.com](https://predictiveventures.substack.com) 

## 参考资料:

1.蒂尔贝，阿尼尔。(2022 年 8 月 15 日)。线性代数的 8 个最基本的科学函数。更好的编程。[https://better programming . pub/8-most-essential-scipy-functions-for-linear-algorithm-programming-simplified-e 3c 7 a4 db 0 b 58](https://betterprogramming.pub/8-most-essential-scipy-functions-for-linear-algebra-programming-simplified-e3c7a4db0b58)

2.决策树分类器在计算机入侵检测中的应用。信息与通信技术汇刊，25。【https://doi.org/10.2495/DATA000371 

3.基于半监督决策树子空间划分的混合分类算法。模式识别，60，157–163。[https://doi.org/10.1016/j.patcog.2016.04.016](https://doi.org/10.1016/j.patcog.2016.04.016)

4.通过构造无监督决策树的可解释层次聚类。IEEE Xplore。[https://ieeexplore.ieee.org/abstract/document/1363769](https://ieeexplore.ieee.org/abstract/document/1363769)

5.通过特征选择、平滑和修剪处理测试代价敏感决策树学习中的过拟合。系统与软件杂志，83(7)，1137–1147。[https://doi.org/10.1016/j.jss.2010.01.002](https://doi.org/10.1016/j.jss.2010.01.002)

6.王，琼斯，帕特里奇。(2000 年 1 月 1 日)。用于构建多分类器系统的神经网络和决策树之间的差异。施普林格柏林海德堡。[https://link.springer.com/chapter/10.1007/3-540-45014-9_23](https://link.springer.com/chapter/10.1007/3-540-45014-9_23)

7.芬恩，吉文。在线集成学习:一项实证研究。机器学习，53(1)，71–109 页。【https://doi.org/10.1023/A:1025619426553 号

8.【http://www.ijmlc.org/vol7/641-LC0045.pdf 

9.为什么你的公司需要联合学习技术？[https://scope new . com/why-your-company-need-federated-learning-techniques/](https://scopenew.com/why-does-your-company-need-federated-learning-techniques/)

10.OpenAI 协助开发了这一可视化工具

到这里 [**了解我**](https://medium.com/@AnilTilbe/about) **。**

阿尼尔·蒂尔贝