# 更多 JavaScript 代码调优技术

> 原文：<https://levelup.gitconnected.com/more-javascript-code-tuning-techniques-e081bcf46122>

![](img/77939cc63e49b3fa3b67c76971b18f0c.png)

照片由[艾琳·威尔逊](https://unsplash.com/@erinw?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将研究 JavaScript 代码的一些常见代码调优技术，包括减少操作和使用资源较少的操作。

# 前哨值

标记值是保证终止一段代码的值。

例如，我们可以编写以下内容:

```
let found = false;
let i = 0;
while ((!found) && (i < items.length)) {
  if (items[i] === value) {
    found = true;
  } else {
    i++;
  }
}
```

在上面的代码中，`value`是标记值，当遇到`found`变成`true`时，它将停止`while`循环。

这将减少运行循环所需的迭代次数，因为它不必一直运行到最后。

# 将最繁忙的环路放在内部

我们应该将具有更多迭代的循环放在内部，以加速我们的代码。

例如，如果我们有:

```
for (let column = 0; column < 100; column++) {
  for (let row = 0; row < 5; row++) {
    //..
  }
}
```

然后我们得到外部循环的 100 次迭代加上嵌套循环的 5 x 100 次迭代，总共 600 次迭代。

如果我们把它们翻转过来:

```
for (let row = 0; row < 5; row++) {
  for (let column = 0; column < 100; column++) {
    //..
  }
}
```

然后，我们得到外部循环的 5 次迭代，加上嵌套循环的 5 x 100 次迭代，总共 505 次迭代。

正如我们所见，这大大减少了总迭代次数。

# 强度降低

我们可以用占用较少资源的操作来替代资源更密集的操作。

例如，乘法可以用重复加法代替。

# 使用尽可能少的数组维数

减少数组维数使得对它们的操作更快更容易。因此，没有理由不降低维度。

嵌套越多，代码就越难阅读。因此我们也应该降低维度。

# 最小化数组引用

最小化阵列访问也节省了资源。

如果我们不需要数组，那么我们就不应该使用它们。

# 使用缓存

我们可以缓存我们计算结果的结果，这样我们就不必再计算它们了。

例如，我们可以这样做:

```
let a = 1,
  b = 2;
const cache = {};
const cachedSum = (a, b) => {
  if (cache.hasOwnProperty([a, b])) {
    return cache[[a + b]];
  } else {
    cache[[a + b]] = a + b;
    return a + b;
  }
}
```

我们将结果缓存在`cache`对象中，然后根据需要从那里返回结果。

# 利用代数恒等式

我们可以用代数恒等式来省去一些运算。

例如，不写:

```
!a && !b
```

我们可以写:

```
!(a || b)
```

如果我们在写:

```
x ** 0.5 < y ** 0.5
```

那么我们可以替换为:

```
x < y
```

因为平方根一直在增加。

# 使用强度缩减

我们可以用更便宜的手术来代替许多昂贵的手术。

它们包括:

*   用加法代替乘法
*   用乘法代替幂运算
*   用三角恒等式代替三角函数
*   用移位运算代替 2 的乘法和 2 的除法

![](img/56175ab201432d3bec527ed6eb793b44.png)

[瓦西姆·舒阿克](https://unsplash.com/@wassoph?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 预计算结果

只要有可能，我们应该将预先计算的结果存储到它们自己的变量中，以便我们可以在以后重用该结果。

如果我们有多次使用的表达式，那么可以将它们赋给自己的变量，这样我们就可以在任何地方使用它们。

例如，我们可以写:

```
let prices = applePrice + orangePrice;
//...
let subtotal = prices + taxes;
let discount = prices * 0.2;
//...
```

现在我们可以用`prices`代替到处重复`applePrice + orangePrice`。

# 结论

我们可以通过最小化数组引用和减少必须通过缓存完成的计算量来提高代码的性能。

此外，如果我们有更多资源密集型操作，那么它们应该被使用较少资源的操作所取代。

我们还可以使用代数恒等式来保存代码中的操作。