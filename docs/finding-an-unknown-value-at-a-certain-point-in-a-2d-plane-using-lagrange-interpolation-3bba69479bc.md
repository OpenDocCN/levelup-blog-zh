# 用拉格朗日插值法求 2D 平面上某一点的未知值。

> 原文：<https://levelup.gitconnected.com/finding-an-unknown-value-at-a-certain-point-in-a-2d-plane-using-lagrange-interpolation-3bba69479bc>

## 理论

假设您拥有一家企业，销售产品已有三年，并且您还有每个月的销售历史记录，您希望预测明年第五个月的销售额。

为此，我们可以按月对您以前的所有历史进行插值，并得到所需的结果。

[**约瑟夫·路易斯·拉格朗日**](https://en.wikipedia.org/wiki/Joseph-Louis_Lagrange) 一位意大利数学家发现了一种利用以前的值来寻找一个函数值和一个线性函数中某些数据点的方法。

所以我用 C++写了一个函数，通过给这个函数提供以前的数据点，它最终会给你一个你感兴趣的点的值。

看看这个:

尝试这个代码，让我知道你面临的任何问题。