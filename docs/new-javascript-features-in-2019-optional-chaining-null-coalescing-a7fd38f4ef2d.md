# 2019 年新增的 Javascript 特性:可选链接和空合并

> 原文：<https://levelup.gitconnected.com/new-javascript-features-in-2019-optional-chaining-null-coalescing-a7fd38f4ef2d>

![](img/577379011108ab1f8c32efee0f423e4d.png)

照片由[麦克斯·尼尔森](https://unsplash.com/@maxcodes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

我所有的文章现在都是免费的[这里](https://brandonburrus.com/blog)，不需要订阅 Medium！

您知道 Javascript 代码库中最常见的错误是什么吗？按顺序，它们是:

*   TypeError:无法读取属性
*   type error:“undefined”不是对象
*   TypeError: null 不是对象

那么所有这些错误有什么共同点呢？它们都处理*引用*，特别是不存在的引用，要么是因为引用不存在(它是`undefined`)，要么是引用已经被显式地设置为 nothing(删除或设置为`null`)。

在 Javascript 中，我们需要显式地检查每个引用，以确保它是定义好的。如果我们在一个对象中有一个深度嵌套的引用，它可能看起来像这样:

```
// quick note: this is very abstract pseudo-code!
let referenceToSomeObject = {};let dataThatIWant = referenceToSomeObject
  && referenceToSomeObject.level1
  && referenceToSomeObject.level1.level2
  && referenceToSomeObject.level1.level2.dataIActuallyWant
  || 'This is the fallback value if anything in the above check is falsy';// Now I can actually safely use dataThatIWant with peace of mind!
```

唷！你可以看到，为了安全地使用`dataThatIWant`,你必须让 JS 解释器经受考验，让它检查每一件小事，然后提供一个缺省值，以防检查中的任何事情是假的(是的，假的是正确的词，我们稍后将回到这一点)。

但这是一个相当可笑的例子。实际上，我们只需要对实际上更有可能拥有不存在的引用的东西进行这样的检查。类似于网络获取:

```
let networkFetchResult = await fetch("https://myfriends.com/user/123").then(res => res.json());// Let's assume that the first friend of our request user is our best friend
let bestFriendsName = networkFetchResult
  && networkFetchResult.data
  && networkFetchResult.data.user
  && networkFetchResult.data.user.friends
  && networkFetchResult.data.user.friends[0]
  && networkFetchResult.data.user.friends[0].name
  || "You don't have any friends!";
```

又快又乱又难看，这就是为什么前三种 Javascript 错误都与不存在的引用有关，因为没有人想写那样的代码。对我们来说幸运的是，Javascript 有一些新的特性，这些特性将使编写安全的代码变得干净。

首先，我们可以使用可选的链接安全地遍历对象中的嵌套引用。你可以把它看作是一种点符号链表达式的懒惰计算。如果在链中的任何一点一个引用被破坏，并证明是不存在的，整个可选链表达式简单地返回为' undefined '。使用可选链接，我们的获取现在可能看起来像这样:

```
let networkFetchResult = await fetch("https://myfriends.com/user/123").then(res => res.json());// Still assuming that our first friend is our best friend
let bestFriendsName = networkFetchResult?.data?.user?.friends?.[0]?.name || "You don't have any friends!";
```

哇，我们的代码立刻干净了很多，但仍然相当安全。如果我们提取的用户有一个最好的朋友，我们得到他们的名字，或者我们得到我们提供的默认回退值。我们所要做的就是使用可选的点符号操作符，而不是常规的点符号。这种新语法真正令人惊叹的地方是，您可以看到它也适用于数组索引，因此我们可以安全地访问可能也不存在的数组索引。(这也适用于调用函数时，查看 [TC39 可选链接建议](https://github.com/tc39/proposal-optional-chaining)了解更多详情！)

这就把我们带到了下一部分，因为我们还有一个小问题。如果我们最好的朋友的名字存在，但是是空的，我们的价值会是什么？如果我们最好的朋友的名字是一个空字符串，这被认为是一个假值，这意味着即使我们的值存在，它仍然会使用我们的后备值。幸运的是，这就是我们下一个新功能出现的地方。

Null-Coalescing 操作符有一个听起来很吓人的名字，但是你可以把它看作是`||`操作符的孪生兄弟。不同之处在于，新的操作符`??`仅在评估值为`undefined`或`null`时提供默认值。了解这一点后，让我们在示例中使用它:

```
let networkFetchResult = await fetch("https://myfriends.com/user/123").then(res => res.json());let bestFriendsName = networkFetchResult?.data?.user?.friends?.[0]?.name ?? "You don't have any friends!";
```

这些新特性使得编写安全、简洁的代码变得异常容易。那么我们什么时候可以使用这些新功能呢？嗯，看情况。到目前为止，这些新特性只是最近才被证实会出现在下一个 Javascript 版本中，也就是明年的某个时候。不幸的是，因为这个版本只是对 Javascript 规范的更新，所以何时添加这些新特性取决于浏览器厂商。

幸运的是，多亏了 Typescript，我们将能够更早地使用这些新功能。Typescript 的下一个版本， [version 3.7](https://github.com/Microsoft/TypeScript/wiki/Roadmap#37-november-2019) 将于今年 11 月发布，将包含这两个新特性(以及一些非常好的 Typescript 新增功能)。如果您希望避免 Typescript 而支持 babel，并且仍然希望利用这些新的 JS 特性，请查看 babel 插件中的[可选链接](https://babeljs.io/docs/en/babel-plugin-proposal-optional-chaining)和[无效合并](https://babeljs.io/docs/en/babel-plugin-proposal-nullish-coalescing-operator)。

参考资料:

[[1]滚动条最常见的 Javascript 错误:[https://rollbar.com/blog/top-10-javascript-errors/](https://rollbar.com/blog/top-10-javascript-errors/)

[[2] TC39 可选链接建议:【https://github.com/tc39/proposal-optional-chaining】T2

[[3] TC39 无效合并运算符提议:【https://github.com/tc39/proposal-nullish-coalescing

[[4] Typescript 3.7 路线图:[https://github . com/Microsoft/Typescript/wiki/Roadmap # 37-2019 年 11 月](https://github.com/Microsoft/TypeScript/wiki/Roadmap#37-november-2019) ]

[[5]可选链接 Babel 插件:[https://Babel js . io/docs/en/Babel-Plugin-proposal-Optional-Chaining](https://babeljs.io/docs/en/babel-plugin-proposal-optional-chaining)]

[[6] Nullish-Coalescing Babel 插件:[https://Babel js . io/docs/en/Babel-Plugin-proposal-Nullish-Coalescing-operator](https://babeljs.io/docs/en/babel-plugin-proposal-nullish-coalescing-operator)