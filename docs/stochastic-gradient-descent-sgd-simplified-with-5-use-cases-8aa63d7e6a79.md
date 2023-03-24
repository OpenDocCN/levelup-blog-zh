# 随机梯度下降(SGD):简化，有 5 个用例

> 原文：<https://levelup.gitconnected.com/stochastic-gradient-descent-sgd-simplified-with-5-use-cases-8aa63d7e6a79>

它不必如此困难:机遇与挑战，SGD 最简单的指南。

![](img/8e7b223d90b64f591a6de850458387d5.png)

来自[作者](https://aniltilbe.medium.com):斑马将如何在月球表面导航？

随机梯度下降是一种采用随机微分方程概念的机器学习优化方法。经过多次迭代或历元，该方法寻找给定函数，该函数的导数以小的步长逼近所需的期望函数，该期望函数被定义为损失函数。

从更技术性的意义上来说，随机梯度下降是一种机器学习算法，它对模型参数进行微小的改变，以提高预测的准确性。随机梯度下降的“随机”部分来自于这样一个事实，即在每次迭代中，只有所有训练样本的随机子集用于计算误差和更新模型系数。这种方法加快了训练的速度，因为就所需的计算量而言，每次迭代需要做的工作更少。

![](img/6efd80b13b8233dab2f96800848e2a29.png)

来自[作者](https://aniltilbe.medium.com)

## **理论，简化**

这里有一个相对简单的 SGD 理论的解释。一个核心概念是，我们希望开发一个梯度函数，通过将我们引向更低误差的方向来提高我们的预测精度。为了做到这一点，我们从估计我们的梯度开始，通常称为 [hessian](https://ieeexplore.ieee.org/abstract/document/6796137) ，并使用它来调整我们模型的每个参数，直到我们看到误差下降。一旦修改了所有参数，我们可以用这组新的参数重新计算梯度，并且只要误差[继续减小](http://proceedings.mlr.press/v48/hardt16.html)，就重复这个过程。

![](img/de8423d6ad25e97baeb369c35bb6cbc2.png)

来自[作者](https://aniltilbe.medium.com)

## **两个简单的例子**

随机梯度下降是一种流行的机器学习算法，因为它简单易懂，高效可靠。此外，SGD 在各种任务上表现良好，包括回归和分类问题。例如，SGD 可用于回归问题，以了解最适合输入数据集的模型参数。此外，对于分类问题，SGD 可用于通过调整最适合输入数据集的模型参数来提高预测的准确性。

作为一个例子，考虑这样一个问题，我们想要预测一封电子邮件是垃圾邮件的概率。例如，输入可以是单词形式的字母，输出可以是以下三个可能值之一:0(非垃圾邮件)、1(部分垃圾邮件)或 2 (100%垃圾邮件)。我们可以使用 SGD 来学习一个预测这些概率的模型，方法是根据它在训练数据集上的表现来调整该模型的参数。

![](img/c48cce65c06efafd5a1545949cfbbb37.png)

来自[作者](https://aniltilbe.medium.com)

## **一些变种**

SGD 有各种变体和扩展。反向传播是一种流行的类型。[共轭梯度](https://link.springer.com/chapter/10.1007/3-540-46084-5_218)和[内点逼近](https://proceedings.neurips.cc/paper/2008/hash/a87ff679a2f3e71d9181a67b7542122c-Abstract.html)方法是另外两种方法。

这些意义重大，原因有很多。首先，它们使优化方法能够对模型参数进行非线性调整。其次，它们提供了对每次迭代中下降多少的更大控制(即，陡度)。最后，SGD 变化可用于监督学习，其中标记的训练数据用于“教导”模型如何预测未标记实例的值。

![](img/69c7eb873c5b872f0ea448e56f43df92.png)

来自[作者](https://aniltilbe.medium.com)

## **实施机会**

1.稀疏模型训练:当数据稀疏时，随机梯度下降比其他方法更有效，因为它避免了遍历所有训练样本。

2.梯度上升:SGD 是随机梯度下降的一种更激进的变体，它更频繁地更新参数以更快地达到最优解(相对而言)。

3.随机森林是受监督的机器学习算法，它从随机决策树中构造它们的模型。贝叶斯推理通过将每个节点随机拆分为两个子节点，并在每次拆分时选择一个子节点，消除了过度拟合，从而确保由各个树生成的预测在不同预测之间有所不同。SGD 非常适合这种方法，因为它可以在大量可选配置中有效地搜索可行的树。

4.K-means 聚类:数据集中的聚类数是用 k-means 计算的，方法是将点随机分配给维度中的 k 个节点之一，然后在每个聚类上调用算法，直到所有数据都被处理完。SGD 可以用作 k 均值优化技术；在每次迭代中改变示教点可以进一步优化收敛，同时使用较少的(一般来说)计算资源。

5.Boosting: boosting 是一种概率机器学习技术，当从训练数据集计算正确的贝叶斯估计很困难或过于昂贵时使用(两种情况，但不是唯一的情况)。

![](img/771f642d6823c5355e0538f7c3caec77.png)

来自[作者](https://aniltilbe.medium.com)

## **当然也有挑战**

一个是确定优化参数的最佳第一猜测并不总是可能的。另一个问题是梯度下降可能不稳定，这意味着原始问题中的微小变化会导致解决方案质量的显著变化。例如，考虑从不同的角度将小重物放在钢丝上来平衡一个球。如果重物稍微偏离中心，球就会翻倒并直线下落。同样，梯度下降中的微小变化也会导致解决方案质量的大幅提升，从而可能导致训练数据过度拟合。

![](img/44d9369d441ea796bc7af7e4c41c3689.png)

来自[作者](https://aniltilbe.medium.com)

## **离别的思念**

如果你对这篇文章有任何建议或拓宽主题的建议，我将非常感谢你的来信。只要在这个帖子的任何地方给我发一条私信。

还有，这是我的时事通讯；我希望你能考虑订阅。

如果你喜欢阅读这样的故事，并想支持我成为一名作家，可以考虑注册成为 Medium 会员，并获得无限制访问 Medium 上所有故事的权利。

此外，我还写了以下帖子，你可能会感兴趣:

[](/top-20-machine-learning-algorithms-explained-in-less-than-10-seconds-each-8fd728f70b19) [## 前 20 个机器学习算法，每个用不到 10 秒钟解释

### 对 20 个最重要的机器学习算法的简单解释，每个都在 10 秒内完成。

levelup.gitconnected.com](/top-20-machine-learning-algorithms-explained-in-less-than-10-seconds-each-8fd728f70b19) [](/top-7-deep-learning-methods-each-explained-in-less-than-10-seconds-3683120de455) [## 7 大深度学习方法，每种方法用不到 10 秒钟的时间解释

### 对 7 个最重要的深度学习算法的简单解释，每个都在 10 秒内完成。

levelup.gitconnected.com](/top-7-deep-learning-methods-each-explained-in-less-than-10-seconds-3683120de455) 

参考:这个帖子包含文本嵌入的引用。

阿尼尔·蒂尔贝

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**将像你这样的开发人员安置在顶级创业公司和科技公司**](https://jobs.levelup.dev/talent/welcome?referral=true)