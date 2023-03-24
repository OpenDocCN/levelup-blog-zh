# 如何在 JavaScript 上访问数组中的 JSON 数据

> 原文：<https://levelup.gitconnected.com/how-to-access-json-data-within-an-array-on-javascript-af5cfd10aba3>

## #解释喜欢我 m5

![](img/20150f8053e5fecde766beab766a675d.png)

费伦茨·阿尔马西在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

您是否遇到过方括号偷偷进入 JSON 数据，却不知道如何在 JavaScript 中获取所需内容的情况？

![](img/404d331272545159398e968b3ae60db0.png)

我就知道。

别害怕，这比你想象的要容易得多。我主要使用 Typescript 和 React，但是下面的方法应该适用于任何基于 JavaScript 的框架。

让我们以一些 JSON 数据为例，称之为*数据集*:

假设我们想获取“*销售价格范围*”内“*上限*”的值。预期结果为 ***418000*** 。

在没有数组的 JSON 上，我们可以简单地通过执行以下操作来访问数据:

```
console.log(dataset.valuations.salePriceRange.upper);
```

但是，在我们的例子中，哈希表开始前有两个方括号。如果我们试图忽略方括号，我们将得到一个未定义的错误，因为您试图达到的数据是不可及的。

在这种情况下，我们可以通过以下方式简单地访问数据:

```
console.log(dataset.valuations[0]?.[0].salePriceRange.upper);
```

这应该会返回我们预期的结果 ***418000*** 。

我们所做的只是访问第一个数组的**第一项(记住:数组的第一项总是 0！)中，不要忘记使用可选的链接操作符，然后在这个第一个数组中，再次访问**的第一个项目**，只有这样我们才能访问“*sale price range”*散列表。**

还在困惑如何访问混合在同一个 JSON 中的数组和对象吗？

![](img/9bed19098f4d2f1196a8a13ff7de3aca.png)

数组中的第一项。什么？

让我们来看第二个例子。这里，我们有一个水果和蔬菜商店的数据集:

要获得商店的名称，您可以:

```
console.log(dataset.store);
```

并且它将返回结果 ***Freshland*** 。

要获得我们水果目录的第*第二*项，您只需:

```
console.log(dataset.fruits[1]);
```

结果应该是返回产品 ***苹果*** 。

为了获取葱类蔬菜的第*个*项，我们编写代码:

```
console.log(dataset.vegetables.allium[0]);
```

并且它返回 ***洋葱*** 。

我希望这有助于澄清{ 0 }(散列表)、[](数组)的概念，以及混合对象和数组时 JSON 文件上的数据访问。如果你觉得这很有帮助，请留下你的掌声！

![](img/384e6ee889436f8d22a45e891d46e670.png)

你有什么问题，或者你想逐步学习的东西吗？请在下面的评论区告诉我！如果你有兴趣看更多这个系列的教程，可以在这里看[。下期#ExplainLikeI'm5 再见！](https://medium.com/@ithos)