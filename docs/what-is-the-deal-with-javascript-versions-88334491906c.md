# Javascript 版本有什么问题？

> 原文：<https://levelup.gitconnected.com/what-is-the-deal-with-javascript-versions-88334491906c>

## 看看 ECMAScript、Javascript 解释器和 transpilers。

Javascript 世界可能是一个令人困惑的世界。人们说 ECMAScript，Javascript，ES5，ES2015(顺便说一下和 ES6 一样)。

在本文中，我将解释 Javascript 的前景。

![](img/8c79642eeeca6b5fa54914ecd01c0de1.png)

Javascript 黄色——照片由[约瑟夫·格鲁恩塔尔](https://unsplash.com/@josephgruenthal?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/confused?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# ECMAScript

ECMAScript 是一个**语言** **规范**，Javascript 实现了这个规范。

每隔几年 ECMAScript 就会发布一个新版本。基本上，新版本包含了应该添加的新功能列表。

例如，在新的 ECMAScript 版本中，可能会有一个新函数:`add()`将两个数字相加。

这样，你最终可以用一个函数将两个数相加— *我知道这不是一个有用的特性，但是为了这个例子，请原谅我🙌*

```
const result = add(12, 4);
console.log(result);
```

当这个决定下来后，就由所有的 *Javascript 解释器*来实现这个新特性。

# Javascript 解释器

Javascript 解释器是一个读取并执行 Javascript 代码的程序。所有支持 Javascript 的浏览器都是 Javascript 解释器。

除了浏览器，还有其他 Javascript 解释器。NodeJS 是一个解释器的主要例子，它让我们可以在浏览器之外执行 JavaScript。它是一个解释器，因为它也读取和执行 Javascript(以及向操作系统提供 API)。

不是每个人都更新他们的浏览器，也不是所有的浏览器都支持相同的 Javascript 特性。这就是事情变得混乱的地方。

最新的 ECMAScript 版本是 *ES10。*然而*，*并不是所有的浏览器都支持这个新的 ES 版本，因为它们仍然需要实现新的功能。

此外，有很多人的浏览器版本已经过时，所以要让所有用户都能使用这些新奇的 *ES10* 功能还需要一段时间。

# ES 版本

我们稍微绕一下道，说说 ES 版本。

正如我在前面提到的，ECMAScript 的最新版本是 *ES10。现在对于 ECMAScript 版本来说，并没有真正的命名约定。所以 *ES10* 也叫 *ECMAScript2019* 和 *ES2019* 。*

此外，该语言最后一次真正的重大变化是在 2015 年推出的 *ES6* ，因此它有时也被称为 *ES2015 —* 是的，这令人困惑，我知道*😌*

因为在 *ES6* 之后只有微小的语言变化，所以 *ES6* 之后的所有版本有时也被称为 *ESNext。*

# Javascript 传输程序

还记得不是所有浏览器都支持最新 ES10 功能的问题吗？有一个解决办法。Transpilers！

Babel 是 Javascript transpiler 的一个例子。Babel 会将新的 Javascript 版本转换成旧的版本。这样你就可以使用新的花式 *ES10* 功能，并让 Babel 将其移植到广泛支持的 *ES5* 版本。

# 节点和浏览器的区别

NodeJS 和浏览器都是 Javascript 解释器。根据您使用的浏览器版本和节点版本，它们可以支持相同版本的 ECMAScript。

然而，Node 是直接在计算机上执行的。这意味着有许多与 Node 捆绑在一起的库不能在浏览器中工作。

例如，在 NodeJS 中，您可以创建新文件。在浏览器中，这将是一个主要的安全风险，所以这是不可能的。

这就是为什么用相同 ECMAScript 版本编写的 NodeJS 程序仍然不能在支持相同版本的浏览器中运行的原因。

就是这样，我花了一段时间才真正理解 Javascript 版本控制系统。我希望这篇文章对你有所帮助！