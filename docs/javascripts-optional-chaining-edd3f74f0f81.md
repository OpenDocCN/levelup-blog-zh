# JavaScript 的可选链接

> 原文：<https://levelup.gitconnected.com/javascripts-optional-chaining-edd3f74f0f81>

![](img/3aa9608ebb4f926316a2472bf21df4ca.png)

由[玛丽亚李森科](https://unsplash.com/@manunalys?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果你用 JavaScript 编程过，那么你就会知道使用对象的好处。它们组织我们的数据结构，允许我们通过查找它们的键来直接访问值。

和对象一起工作，你肯定对出现在你的控制台中的这个很熟悉…

```
Uncaught TypeError: Cannot read property '<prop>' of undefined
```

这种情况经常发生，因为您正在进行 API 调用，而响应尚未完成，这是非常正常的。为了克服这一点，我已经用几种不同的方式观察和处理了它。在`if`语句中使用短路评估是可行的。

让我们用这个作为我们的对象…

```
const driver = {
  name: 'Mary',
  car: {
    color: 'black',
    make: 'Hyundai',
    model: 'Elantra',
    year: 2021
  }
}
```

`if`语句条件…

```
if (driver && driver.car) {
  console.log(driver.car.model);
}
```

这没什么错，但是 JavaScript 确实需要查找`driver`三次。`driver`真实吗？好吧，`driver.car`是真的吗？爽，日志`driver.car.model`。

这当然会避免日志错误出现在你的控制台上，但是我们能做得更好吗？当然，我们也可以直接在`console.log`中进行短路评估。

```
console.log(driver && driver.car && driver.car.model);
```

这也将避免错误日志，使我们不必编写`if`语句。如果这些评估中的任何一个短路，控制台将记录`undefined`，而不是查找导致`Uncaught TypeError`的下一个评估。

太棒了。但是，我们能做得更好吗？输入可选链接。乍一看，由于问号是如何放置的，这似乎有点不协调，但它清理了我们的代码，JavaScript 只需要访问我们的对象一次。

```
console.log(driver?.car?.model);
```

简单扼要。这就是我喜欢的[。](https://youtu.be/PMivT7MJ41M)

它将检查`driver`是否正确，然后检查`car`是否正确，最后检查`model`。如果其中任何一个错误，它将记录`undefined`而不是`Uncaught TypeError`。当然，如果它们都是真的，那么它将记录`model`的值。宾果，邦果，邦果💥