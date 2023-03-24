# 深度学习 PyTorch 简介

> 原文：<https://levelup.gitconnected.com/introduction-to-pytorch-for-deep-learning-d8e090e31b06>

![](img/a54be573c30cbf34637712fb011f3dc4.png)

戴维·克洛德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

人工神经网络(ann)是深度学习中使用的一种流行工具。在人工神经网络中，数据通过设计为多层系列连接节点的数据结构传递。这种数据结构大致基于动物神经系统的结构。为了更详细地了解神经网络的总体情况，并做一个构建简化神经网络的实践练习，你可能想看看[这篇教程](https://towardsdatascience.com/a-quick-dive-into-deep-learning-41bba4cb6d69?sk=c11d52f68ba9076385d50878f545fbe5)。

然而，我建议您利用机器学习库的力量。它们使得标准技术的使用更加容易。PyTorch 是主要的机器学习库之一。它是免费和开源的，主要由脸书人工智能研究所(FAIR)开发。希望通读这篇教程和我的另一篇教程能让你对神经网络的结构有一个基本的概念，并帮助你说明这些库如何让你更有效地构建更复杂的网络。

本教程是对 PyTorch 库的一个简单、基本的介绍。我将展示该库如何工作的一些方面，为使用 PyTorch 构建神经网络做准备。在 Python 3 中打开一个 [Jupyter 笔记本](https://jupyter.org/install)，我们就出发了。

![](img/f8af4ef710357e1a66a94d1e3b132c2c.png)

由 [SpaceX](https://unsplash.com/@spacex?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 张量

在 PyTorch 中，**张量**是一个多维矩阵，它的元素都是相同的数据类型。张量，可能被其他数据类型包装，是处理输入数据的基本结构。它们对于通过神经网络处理数据也很有用。

张量类似于 NumPy 的 ndarrays，可以很容易地在 NumPy 数组之间转换。一个不同之处是，前者可以在专门类型的高通量硬件上运行，以促进大数据的分析。我们将在这里创建一个非常非常简单的张量。

```
import torchdata = [[9, 8], [7, 6]]
data_tensor = torch.tensor(data)
print(data_tensor)
```

打印输出如下:

```
tensor([[9, 8],
        [7, 6]])
```

# 亲笔签名

PyTorch 的**亲笔签名的**包用于创建和实现张量上的操作。这些操作在训练过程中使用，在称为**反向传播**的步骤中，将损失——或神经网络的预测误差——传播回神经网络。反向传播确定神经网络的每个节点造成了多少损失。

![](img/0e0b37e649dcc081366a2a8852d232fb.png)

照片由[布雷特·乔丹](https://unsplash.com/@brett_jordan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

为了说明亲笔签名，让我们用 requires_grad 设置创建一些张量。requires_grad 跟踪对张量执行的操作。这里张量 a 和 b 是**参数**。参数是在训练期间优化的模型的系数。由 a 和 b 组成的张量 E 就是误差。(这个符**的意思是要的力量。所以要创建张量 E，张量 a 是立方，张量 b 是平方。)

```
a = torch.tensor([2., 4.], requires_grad=True)
b = torch.tensor([7., 5.], requires_grad=True)
E = 3*a**3 - b**2
```

我们需要基于参数的误差梯度。**梯度**是表示预测误差相对于输出和预测值的斜率的函数。调用。backward()函数计算并存储这些梯度。让我们用张量 E 来说明这一点，将梯度值存储在 a.grad 和 b.grad 中。

```
external_gradient = torch.tensor([1., 1.])
E.backward(gradient=external_gradient)
print(a.grad)
print(b.grad)
```

打印输出如下:

```
tensor([ 36., 144.])
tensor([-14., -10.])
```

![](img/5d44eec7a123ccacfed4a8ed5135febc.png)

Bobby Stevenson 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

希望你喜欢第一次去 PyTorch。虽然我们还没有建立一个完整的神经网络，但我们已经建立了其中的一些重要组件。当您准备在项目中使用 PyTorch 时，这会让您对它的工作原理有所了解。