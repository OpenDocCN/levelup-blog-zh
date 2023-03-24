# JavaScript 中的多维数组

> 原文：<https://levelup.gitconnected.com/multidimensional-array-in-javascript-d968a7efe14>

![](img/a326f1dbf1669db9703f2b995c08a7ff.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Faris Mohammed](https://unsplash.com/@pkmfaris?utm_source=medium&utm_medium=referral) 拍摄的照片

JavaScript 本身没有多维数组，要拥有多维数组，我们必须创建一个数组的数组。

多维数组包含一个元素矩阵。

在这个例子中，我们将使用一个汽车陈列室，其中汽车是停放和排列的元素，就像一个具有行和列的网格。

汽车陈列室将是一个独立数组的多维数组，网格中的每一行都是一个矩阵。

![](img/c3d34eaaa32b1e27b535c23557f0e17b.png)

要访问多维数组中的元素，需要两个索引，如下例所示。

![](img/b22dda9b844cdf9f25396d6111b88de2.png)

但是如果我们的数组中有很多元素需要控制台处理，那就不太现实了。因此，我们可以使用“For 循环”和“嵌套 for 循环”，这是循环中的循环

如果我们只使用下面的外部循环:

![](img/c402e2ea548d4ebd05c076612ca76be8.png)

输出将是每个数组的数组:

![](img/e61d210fcd56e7c9e72c015f8529ba05.png)

为了分别显示每个元素，我们需要使用一个嵌套循环:

![](img/a924ed52d7a4ae7b2111dc4644c2578b.png)

输出将是:

![](img/4fcb1d57039dfd137170e68d05e5d21f.png)

最后，我们还可以显示索引以及每个元素:

![](img/c43c5c03cc3bbae3d7c24bf042ac572c.png)

通过这样做，我们可以看到元素所在的坐标。

![](img/164ec75d61c88ca22b0a7f51b0743e14.png)

现在你有了，JavaScript 中的多维数组…

编码快乐！