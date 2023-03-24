# 如何在 TypeScript 中键入从外部源接收的有效负载

> 原文：<https://levelup.gitconnected.com/how-to-type-a-payload-received-from-an-external-source-in-typescript-e12a31c6777a>

![](img/ebf28a4e929fb8851891c48744252153.png)

当我们从外部来源接收到一些数据时(比如当您的 web 服务器接收到一个 HTTP 请求时，或者当我们调用一个外部 API 来获取一些数据时)，我们不知道数据看起来像什么，直到我们验证它。

假设我们有一个啤酒服务，有一个函数`addFavoriteBeer`，我们可以调用它来添加我们喜欢的新啤酒。

现在，让我们考虑一下控制器，它将接收 HTTP 请求，请求添加喜爱的啤酒，并调用这个服务来添加它们。

# 我们不应该这样做

Typescript 不会抱怨，因为从类型的角度来看它是有效的。但是如果我们在 HTTP 请求中给出下面的有效负载会怎么样呢？

```
{ "hello": "world" }
```

在这种情况下，我们将把不是一个`Beer`的东西传递给`addFavoriteBeer`，而是像使用一个`Beer`一样使用它。这很糟糕。

![](img/42abd8f95840b39b63b2c348735974d1.png)

# 我们应该避免这样做

我们可以使用 [Joi](https://www.npmjs.com/package/@hapi/joi) 或另一个模式验证库(或者甚至手动操作，如果我们感觉到的话)来确保我们得到的是我们所期望的。

这里有一个 Joi 的例子

现在，当我们调用`addFavoriteBeer`时，我们知道它与`beerSchema`匹配，在这种情况下，它工作得很好，因为`beerSchema`与`Beer`类型匹配。但是它仍然不是类型安全的，因为我们仍然将`any`传递给`addFavoriteBeer`。

如果我们这样改变`Beer`的定义会怎么样？

现在我们在`Beer`和`beerSchema`之间有了不一致，但是对于 Typescript 来说一切都很好。

如果我们将有效载荷发送给 HTTP 请求，它将通过验证，但不应该通过，因为`isFrenchBeer`不是布尔值。

```
{
    "name": "Chimay",
    "degree": 9,
    "isFrenchBeer": "hello world"
}
```

# 我们应该更喜欢这个

为了解决我们之前看到的问题，我们可以使用像 [io-ts](https://www.npmjs.com/package/io-ts) 这样的库，它返回(在验证成功的情况下)一个对象，该对象具有我们在控制器中为验证定义的模式的形状。

当`beer`匹配类型`Beer`时，`addFavoriteBeer`接受。

但是现在，如果我们像之前看到的那样在服务中更改类型`Beer`来添加布尔值`isFrenchBeer`，Typescript 将会引发一个错误，因为`beer`缺少这个属性！为了解决这个问题，我们必须将这个属性添加到`BeerIO`中。

**因此，如果我们在控制器中定义的模式和服务类型之间存在不一致，Typescript 会引发错误，代码无法构建。**

# 结论

*   不要做 ***中提到的事情不要做这个*** 部分，因为这可能会让你的应用崩溃如果你收到了预期的有效负载(`cannot read property of undefined`)或者最坏的情况，你的应用可能会继续运行，但奇怪的事情会发生，你可能不会注意到它，或者你可能有安全问题。
*   ***中提出的避免做这个*** 部分是为了避免，如说。因为如果您的验证模式和预期类型不一致，Typescript 不会抱怨，您可能会遇到与 ***不要这样做*** 类似的问题。但是当你写 Javascript 而不是 Typescript 的时候，使用 Joi 这样的库仍然是一个很好的解决方案。
*   更喜欢 ***中呈现的内容更喜欢这个*** 部分，因为它更安全。并且你将知道在你验证的和你的类型之间是否有不一致。

如果你知道这种问题的其他解决方案，请在评论中分享，我很好奇:)