# 半监督学习的简单介绍

> 原文：<https://levelup.gitconnected.com/a-simple-introduction-to-semi-supervised-learning-e20b2fe29ca0>

## 理解并使用 scikit

半监督学习是一种结合有标记和无标记数据的分类方法。半监督学习的主要原因是缺乏标记数据，一旦标记过程昂贵且耗时。另一方面，有很多未标记的数据可用，但与它们没有太大关系[**【1】**](https://minds.wisconsin.edu/bitstream/handle/1793/60444/TR1530.pdf?sequence=1)。冠状病毒数据的现状可以很好地描述这种情况，因为**的标签取决于测试**，而我们在一些国家(如[巴西](https://thehill.com/policy/international/americas/492644-coronavirus-cases-in-brazil-likely-12-times-higher-than))只有很少的测试人员和大量未测试人员。

![](img/1a8e714083735a31b6d38a1d508a4e5d.png)

克林特·王茂林在 [Unsplash](https://unsplash.com/) 上拍摄的照片

通常，当您没有足够的标记数据来使用监督学习解决问题时，替代方法是使用非监督学习。然而，一旦没有关于数据的先前信息(标签)，这种方法与许多假设一起工作，并且那些假设不总是代表真理[**【2】**](https://repositorio.unesp.br/handle/11449/191774)。但是即使是少量的标记数据也可能包含非常重要的信息。这就是半监督学习的用武之地。

# 算法

想象一下一个分类器(例如 SVM)，一个只有很少标记数据的训练集，和一个有很多未标记数据的数据集。目标是对整个数据集进行分类，但是我们的训练集太小了。为了避免这种情况，我们将提出以下策略:用所有已标记的数据进行训练，并预测所有未标记的数据。现在，使用一些度量标准，我们将对模型做出的最有信心的预测进行排名。让我们考虑离超平面距离最高的样本是最有把握的。那些最有把握的预测将不再是未标记集的一部分，而是将成为标记/训练集的一部分。然后使用新的训练集，我们再次预测整个数据集。重复该过程，直到没有未标记的样本留下。这种策略叫做自我训练，是最简单的半监督方法之一。

有一堆半监督算法使用几种不同的方法。与自我训练相结合，其他常用的方法有基于图形的方法、协同训练、生成模型等。一些基于图的方法也非常容易理解， [Vijini](https://medium.com/u/8002c1aed6e7?source=post_page-----e20b2fe29ca0--------------------------------) 在以下媒体出版物中明智地解释了**标签传播算法** ( [可在 scikit-learn](https://scikit-learn.org/stable/modules/generated/sklearn.semi_supervised.LabelPropagation.html) 获得):

[](https://towardsdatascience.com/label-propagation-demystified-cd5390f27472) [## 标签传播去神秘化

### 基于图的标签传播的简单介绍

towardsdatascience.com](https://towardsdatascience.com/label-propagation-demystified-cd5390f27472) 

# 使用 Python 进行半监督

现在，您对半监督学习有了一点了解，让我们使用古老的 iris 数据集，以便用 scikit-learn 测试标签传播算法。**下面开发的代码在** [**Github**](https://github.com/caiocarneloz/sklearn-semi) 上有。

不幸的是，scikit-learn 上只有两种半监督算法。不过，你可以在 Github 上找到很多，只要找一下[“半监督”](https://github.com/topics/semi-supervised)标签。在我的硕士学位，我用 Python 编写了粒子竞争与合作算法，这是一种生物启发的半监督算法，由我的导师 [Fabricio Breve](https://medium.com/u/af19cb0d8900?source=post_page-----e20b2fe29ca0--------------------------------) 开发。你可以在这里找到关于这个算法[的原始出版物，你也可以通过在](https://ieeexplore.ieee.org/document/5871621) [Github](https://github.com/caiocarneloz/pycc) 上克隆代码或者通过用 PyPI 安装包来使用它:

```
pip install pypcc
```

它的用法与 scikit-learn 算法的用法完全相同，Github 存储库中有演示代码，可以随意使用。

# sci kit-学习

为了测试 scikit-learn 上可用的半监督算法，我们首先需要安装 sklearn 包。所以使用 PyPI，只需运行:

```
pip install sklearn
```

## 虹膜数据集

之后，我们需要虹膜数据集。我们可以使用 sklearn 的“数据集”模块来获得这个数据集，不需要下载任何文件。该数据集由 150 个涉及鸢尾属植物的样本组成。有 3 类，每类 50 个样品:刚毛藻、杂色藻和海滨藻。

## 数据集目标

由于这个数据集是由带标签的样本组成的，我们将需要“取消标签”其中的一些来测试我们的半监督模型。下面的代码可以在我的 [Github](https://github.com/caiocarneloz/masksemi) 上找到并得到更好的解释。其目的是平等地取消每一类样本的标记。**默认情况下，scikit-learn 的半监督算法将“-1”视为无标签样本**。

![](img/a8a4d5604e1ae5619ba1a5af25a9e70e.png)

由[杰西卡·鲁斯切洛](https://unsplash.com/@jruscello)在 [Unsplash](https://unsplash.com/) 上拍摄的照片

“百分比”参数表示您想要的标记百分比。这意味着如果“百分比”等于 0.05，则 95%的数据集将是未标注的，5%将是已标注的。对于本例，标记数据的百分比将为 5%。这样，每个班级将有 2 个带标签的样本(总共 150 个中的 6 个)。创建一个名为“utils.py”的文件，并粘贴这个掩码函数:

## 主要功能

下一步是实例化**标签传播**模型，并使用该库中可用的大多数机器学习模型的相同语法来运行它。“拟合”函数不会像监督学习方法那样训练模型，因为该算法中没有训练步骤。但是，该函数将构建模型图，并准备标记过程所需的所有结构，该过程将由“预测”函数执行。

运行上面的代码后，我们已经得到了每个类的预测。为了评估这一点，因为这是一个分类问题，我们可以使用一个[混淆矩阵](https://en.wikipedia.org/wiki/Confusion_matrix)。有必要注意这一步，因为带有预测的数组中有一些之前标记的样本(在本例中是 5%)。我们不能认为这些都是正确标记的，所以最终的代码应该是这样的:

最后可以保存在与“utils.py”相同的文件夹下，命名为“sklearn-semi.py”。现在你可以使用同样的结构，不仅使用 sklearn 算法，还可以使用你找到的任何其他半监督算法。

希望你学到了一些东西，感谢阅读。如有任何建议或问题，请随时通过这里或我的 [Linkedin 个人资料](https://www.linkedin.com/in/caiocarneloz/)联系我。再见！

# 参考

[1]朱晓军，[半监督学习文献综述](https://minds.wisconsin.edu/bitstream/handle/1793/60444/TR1530.pdf?sequence=1) *(* 2005)，威斯康星大学麦迪逊分校计算机科学系

[2] Caio Carneloz, [使用粒子间竞争与合作的磁共振成像辅助诊断阿尔茨海默病](https://repositorio.unesp.br/handle/11449/191774) (2019), 保利斯塔州立大学 - UNESP