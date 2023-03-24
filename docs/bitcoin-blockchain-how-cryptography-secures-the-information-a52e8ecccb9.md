# 比特币区块链:密码学如何保护信息

> 原文：<https://levelup.gitconnected.com/bitcoin-blockchain-how-cryptography-secures-the-information-a52e8ecccb9>

## 确保完整性和安全性

![](img/4f21c1c38a04d440f85003d3775dae12.png)

阿列克西·里斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## **比特币简介**

让我们从讨论加密技术如何确保比特币区块链中信息的完整性和安全性开始。我们不能孤立地讨论密码学，而是必须在比特币区块链的所有元素的背景下看待它。

特别是，我们必须从比特币区块链是分布式账本中的分布式账本说起。每个节点将存储整个区块链或一个分类账和完整的历史。

让我们详细了解一下比特币区块链的形成过程，以了解其工作原理。

2008 年 10 月，2009 年 1 月 3 日，中本聪发布了比特币纸币。他发布了比特币核心软件。然后，他在自己的电脑上下载并安装了比特币核心软件。最开始的时候，比特币软件很小。它不需要那么大的规模或能量，因为比特币网络刚刚起步。没有太多的事务，实际上，没有任何占用计算机空间的事务。因为没有网络，所以什么也没有发生。

所以，计算机一开始并不需要很大的功率来运行比特币节点。但当你今天安装比特币核心软件时，该软件必须下载你电脑上所有的比特币区块链。不仅如此，它还必须检查和验证所有交易和所有数据块，这需要一些时间。

拥有一个分布式账本是比特币区块链安全性的核心。

如果你有一个像银行一样的中央数据库，请使用在线服务。当您拥有一个集中式数据库时，它就变成了一个吸引黑客的蜜罐，这些黑客想要访问数据库、操纵数据库、更改数据库并从中窃取信息。

在像比特币区块链这样的分布式账本中，拥有分布式账本是比特币区块链安全性的关键。首先，所有的数据都是可用和公开的。因此，在这种情况下，黑客的目的不是窃取数据库。

目的是操纵数据库，很可能给他们自己比特币，并表明他们没有花掉他们的比特币，基本上是双重花费或他们想表明他们有更多的比特币给自己。

这变得非常困难，因为你必须改变账本的每个实例或足够多的实例，以向网络显示你是正确的，显示你有更多比特币或你没有花费比特币的账本在网络上更多。

因此，黑客必须同时攻击多个位置、多个节点，以达到他们的目的。这与攻击集中式数据库非常不同。

[](https://pub.towardsai.net/cryptocurrency-a-beginners-guide-to-digital-cash-cea36781389d) [## 加密货币:数字现金初学者指南

### 加密货币是一种计算机化的分期付款框架

pub.towardsai.net](https://pub.towardsai.net/cryptocurrency-a-beginners-guide-to-digital-cash-cea36781389d) [](https://pub.towardsai.net/bitcoin-and-its-history-a-view-to-know-the-cryptocurrency-47ce5908e090) [## 比特币及其历史:认识加密货币的视角

### 比特币及其历史:认识加密货币的视角

比特币及其历史:了解 Cryptocurrencypub.towardsai.net 的视角](https://pub.towardsai.net/bitcoin-and-its-history-a-view-to-know-the-cryptocurrency-47ce5908e090) [](https://pub.towardsai.net/deep-learning-26adbecfd8d0) [## 基于深度学习的比特币价格预测建模理论

pub.towardsai.net](https://pub.towardsai.net/deep-learning-26adbecfd8d0) 

## **比特币区块链上的密码术**

密码术使得验证和确认交易和块变得容易。它可以防止对比特币区块链上的数据进行任何篡改或操纵。

回到中本聪，他在笔记本电脑上下载了比特币核心软件，比特币核心软件下载了所有的比特币区块链，验证了所有的交易和区块。

![](img/bfa610f9b2e339913248b7e9064ceafb.png)

由[弗洛里安·伯杰](https://unsplash.com/@bergerteam?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

比特币核心软件还会做一些别的事情。比特币核心软件将使用加密哈希函数为中本聪创建比特币地址。其方式是生成一个称为私钥的随机数，然后对该数字运行加密哈希函数以生成公钥，然后比特币核心软件对公钥再次运行加密哈希函数以生成比特币地址。

加密技术的功能对于比特币网络的运行和安全至关重要。

## 让我们探索一下加密散列函数是如何工作的

密码散列函数是一种独特的函数。在密码有一个函数，你有一个输入，这是一个消息。它可以有任何长度，你有一个输出，我们称之为一个摘要，它有一个确定的特定长度的基础上，密码必须发挥作用。

比特币网络上使用的主要加密哈希函数是 SHA 256。顾名思义，该密码散列函数的摘要或结果是 256 位。

散列函数最好也是最重要的特性是它从消息到摘要。你不能从摘要中发现什么信息，也不能逆转这个功能。

我希望你喜欢这篇文章。通过我的 [LinkedIn](https://www.linkedin.com/in/data-scientist-95040a1ab/) 和 [twitter](https://twitter.com/amitprius) 联系我。

# 推荐文章

1.[8 Python 的主动学习见解收集模块](https://pub.towardsai.net/8-active-learning-insights-of-python-collection-module-6c9e0cc16f6b)
2。 [NumPy:图像上的线性代数](https://pub.towardsai.net/numpy-linear-algebra-on-images-ed3180978cdb?source=friends_link&sk=d9afa4a1206971f9b1f64862f6291ac0)3。[Python 中的异常处理概念](https://pub.towardsai.net/exception-handling-concepts-in-python-4d5116decac3?source=friends_link&sk=a0ed49d9fdeaa67925eac34ecb55ea30)
4。[熊猫:处理分类数据](https://pub.towardsai.net/pandas-dealing-with-categorical-data-7547305582ff?source=friends_link&sk=11c6809f6623dd4f6dd74d43727297cf)
5。[超参数:机器学习中的 RandomSeachCV 和 GridSearchCV](https://pub.towardsai.net/hyper-parameters-randomseachcv-and-gridsearchcv-in-machine-learning-b7d091cf56f4?source=friends_link&sk=cab337083fb09601114a6e466ec59689)
6。[用 Python](https://medium.com/towards-artificial-intelligence/fully-explained-linear-regression-with-python-fe2b313f32f3?source=friends_link&sk=53c91a2a51347ec2d93f8222c0e06402)
7 全面讲解了线性回归。[用 Python](https://medium.com/towards-artificial-intelligence/fully-explained-logistic-regression-with-python-f4a16413ddcd?source=friends_link&sk=528181f15a44e48ea38fdd9579241a78)
充分解释了 Logistic 回归 8。[数据分发使用 Numpy 与 Python](https://pub.towardsai.net/data-distribution-using-numpy-with-python-3b64aae6f9d6?source=friends_link&sk=809e75802cbd25ddceb5f0f6496c9803)
9。[机器学习中的决策树 vs 随机森林](https://pub.towardsai.net/decision-trees-vs-random-forests-in-machine-learning-be56c093b0f?source=friends_link&sk=91377248a43b62fe7aeb89a69e590860)
10。[用 Python 实现数据预处理的标准化](https://pub.towardsai.net/standardization-in-data-preprocessing-with-python-96ae89d2f658?source=friends_link&sk=f348435582e8fbb47407e9b359787e41)