# 初学者打字稿

> 原文：<https://levelup.gitconnected.com/typescript-for-beginners-97b568d3e110>

## 学习打字稿的实用方法

![](img/4c23a29460927270955917c0fce066a4.png)

凯文·Ku 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

TypeScript 变得比以往任何时候都更受欢迎。作为初学者，我和 TypeScript 并不是一见钟情，但我们已经相互了解。目前，我不会在不使用 TypeScript 的情况下启动项目！

在这篇文章中，我想深入研究 TypeScript 的基础知识。我们将学习一些理论，但是因为我相信通过建立一些东西来学习，我们将会是实际的。

# 什么是 TypeScript？

简单地说，我会把它解释为 JavaScript 之上的一个类型系统层，它编译跨浏览器的 JavaScript。在 TypeScript 网站上，他们是这样解释的。

> *TypeScript 是 JavaScript 的类型化超集，编译成普通 JavaScript。*

对于有 Java、#C 和其他几种语言经验的人来说，熟悉类型。这些类型将帮助用户使我们的 JavaScript 更加可预测。

因为已经有这么多用 TypeScript 构建的库，或者支持 TypeScript，所以我们的 IDE 可以帮助我们编写 JavaScript。

比如它会告诉我们某个对象有什么样的方法或者属性。在函数的情况下，它会告诉函数采用哪种参数以及返回类型。

# 打字稿基础

您可能知道 TypeScript 正在解决我们作为 JavaScript 开发人员时都经历过的一系列问题。在数字上使用字符串方法，尝试访问在某个对象中不可用的属性。

如果你看不到学习 TypeScript 的意义，请阅读这篇关于为什么现在开始学习 TypeScript 是一个好选择的帖子，希望你喜欢。

# 通过构建来学习

让我们建立一个虚拟的比萨饼店。(*不，我们不会建立一个真正的*)通过建立这个，你将学习所有的基本打字稿。如果你有后端编程语言的经验，一些术语对你来说会很熟悉，那太好了！

如果你没有任何后端编程语言的经验，不要害怕，我会教你一切。我确信你能学会。我也是一个非常视觉化的人，当我第一次开始使用 TypeScript 时，它很难，但随着时间的推移，我开始爱上它。给自己一点时间练习就好。

在我的例子中，我想给出一个非常实用的方法来立即开始使用 TypeScript，而不是只学习理论，这样你就必须自己实现它。我喜欢实用性！

> *我所有的代码都可以在我为这个教程创建的* [*CodeSandbox 环境*](https://codesandbox.io/s/pizza-store-typescript-demo-8mw4j) *中找到。*

# 设置您的工作区

为了使用 TypeScript，我们需要在电脑上安装一些东西。

## NodeJS

通过 [NodeJS 网站](https://nodejs.org/en/)安装 NodeJS，或者通过 [Homebrew 或 NVM](https://pawelgrzybek.com/install-nodejs-installer-vs-homebrew-vs-nvm/) 安装，如果你在 mac 上的话。

## 以打字打的文件

```
npm install -g typescript
```

这将全局安装 TypeScript。安装后，可通过`tsc`命令使用。

## 最佳编辑

我选择 [Visual Studio 代码](https://code.visualstudio.com)，因为它集成了 TypeScript。

或者如果你只是想摆弄一下 TypeScript，我会推荐使用 [CodeSandbox](http://codesandbox.io/) 和他们的 TypeScript starter，这会帮助你现在就开始。

TypeScript 网站提供了一个很好的平台来使用 TypeScript，因此您可以看到 JavaScript 编译后的样子。

现在我们可以走了！

# 基元

希望你知道 JavaScript 中所有的[原始数据和结构类型。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures)

*   字符串`'string'`
*   数量`785`
*   布尔型`true`
*   未定义`undefined`
*   空值`null`
*   对象`{}`
*   功能`function fake() {...}`

如果你不知道它们，我强烈建议你先开始学习它们。它们对于使用 JavaScript 和 TypeScript 是必不可少的。关于[数据类型和数据结构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures)，查看 MDN web docs 部分。

在 TypeScript 中，我们在一个`interface`中使用这些原始值来形成一个`Object`或`Class`的蓝图，如下例所示。

# 连接

TypeScript 中的大多数模型都是接口和类的组合。一个`interface`是一个类或对象的蓝图。在这个`IPizza`接口中，我们定义了比萨饼的所有属性。在每个属性中，我们定义了信息的数据类型。

在`interface`中定义的每个属性都是必需的。如果你想让它可选，你必须使用`?`。例如`propertyName?: string`如果我们在一个接口中定义这个属性，它就是可选的。如果属性在`object`中丢失，TypeScript 不会给出错误。另一方面，如果一个属性是必需的，那么如果该属性丢失，它将给出一个错误。

当一个属性没有在`interface`中定义时，你会从 TypeScript 编译器那里得到一个错误，因为数据与蓝图不符。

## 例子

我们都可以想出披萨的特性。

*   名字
*   切片(切片数量)
*   浇头
*   价格
*   奶酪皮
*   严格的素食主义者
*   素食者

让我们把它们放到接口中，决定它们是什么样的数据类型。

在上面的例子中，我们看到了比萨饼的`interface`。我们给所有的属性一个单一的数据类型。现在我们可以创建我们的 Pizza 对象，并使用接口来确保它具有正确的属性。

现在的`pizza`就是根据界面。`interface`现在是数据验证的一种形式。如果我们要添加不在`interface`中的属性或者具有错误数据类型的属性，TypeScript 将给出错误。

有了这个对象，你会得到错误！

# 多重值

但是如果我们想要一个字符串或数字的数组来给出我们的浇头或尺寸呢？我们可以很容易地做到这一点，只需在`interface`中写入`string[]`或`number[]`。

现在我们的`pizza`对象是有效的。

如果我们想用多个 pizza 对象创建一个数组，我们可以用同样的方法用`IPizza[]`来做。

# 枚举

在`IPizza`中，我们已经设置浇头的值需要是一个字符串数组`string[]`。但是我们可以做得更聪明，因为我们的比萨饼没有太多的配料。(*我们也可以将此应用于尺寸*)

假设我们的披萨有 4 种配料和 5 种尺寸。我们可以为此定义一个`enum`。默认情况下，枚举中定义的第一个选项将具有值`0`。但是如果您愿意，您可以设置其他值。

在`interface`中，我们将它添加到我们的属性中。有了菜单，我们可以对尺寸和配料有多种选择。

所以我们的对象看起来像这样。

# 班级

我们可以使用一个`class`来代替创建一个普通的`interface`，但是我们使用`constructor`中的`interface`来验证我们放入`class`中的数据。

类很方便，因为你可以给你的`class`方法、getter 和 setter，这些在`interface`中是不可能的。

一个`interface`不会被编译进你的 JavaScript 文件，一个`class`会，因为它是一个有效的数据结构。

让我们基于我们的`IPizza`接口创建一个类。

您可能知道，`class`非常适合创建特殊类型的`object`的新实例。

如果你检查你的控制台，你会看到这个`bbqPizza`来自类型`Pizza`而不是直接来自类型`object`。当然，这是引擎盖下的一个`object`！

# 排列

但是只有一个披萨的披萨店是不够的。让我们做一个比萨饼的大目录。

`Pizza[]`将告诉 TypeScript 属性`list`将是一个数组。无论你在什么地方放一个`[]`在后面，它都会变成一个数组。像`string[]`、`number[]`或者`Pizza[]`，随便挑一个类型都可以。

现在我们可以用`PizzaCatalog`类制作一份披萨清单。

当您将它放入 console.log 中时，它会输出以下内容。

我们可以添加更多的比萨饼。

结果会是。

完美的循环，并围绕它建立一个很酷的网店。

# 遍历枚举

为了以更易读的方式获得我们的比萨配料和大小，我们必须通过枚举来映射。

来解释它的作用。首先我们`map`遍历 enum 的所有键，然后我们检查一个键是否是`number`，因为默认情况下`enum`值是数字。我们将枚举字符串设置为`lowercase`，然后过滤掉所有的`undefined`值。

这些变量的输出是这样的。

# 功能

现在我们有了`Pizza`和`PizzaCatalog`类，是时候添加一个函数来计算基于大小的披萨价格了。在函数中，我们遍历所有的大小，计算(在这种情况下是随机的)一个加法，并将其与`price`属性值相加。

对于用这个`class`创建的每一个比萨饼，您将得到一个价格数组，因为我们在`constructor`中计算它们，并将它们添加到`prices`属性中。

在这种情况下，我们定义了`getPizzaPrices`方法来将`IPizzaPrice[]`输出为一个数组。但是如果这是一个不返回值的函数，我们应该像这样在`void`中输入它。

# 任何的

当我在 Angular 团队刚刚推出 Angular 2.0 的时候开始使用 TypeScript 的时候，我根本不明白 TypeScript 的好处。所以当我遇到错误时，我就用`any`输入所有内容🙈太糟糕了。

类型`any`可以是你想要的任何类型。如果您必须处理大量的泛型类型，而您不知道它可能是什么类型或者它可能是任何类型，那么这将非常方便。

# 空且未定义

希望你知道`null`和`undefined`的区别。因为`null`可以简单的用空值来解释。`undefined`未定义，在`object`属性的情况下，可以说该属性的值未定义。

在 TypeScript 中，行为与普通的 JavaScript 相同。但是您可以在带有类型的`class`中键入 property，并说它有一个默认值`null`或`undefined`。就像我们在`Pizza`课上做的一样。

`null`的奇怪之处在于，如果你和`typeof`核实，它会显示为`object`，这可能会很奇怪。

那是一个包裹。现在该是你用 TypeScript 构建酷东西的时候了！如果你想在评论中分享你的项目，请吧😉。

# 结论

![](img/e22044cc6ad559c2138a7d444ba65671.png)

我希望使用 TypeScript 后一切都清楚了。它是如何工作的，为什么它是开发人员工具箱的一个重要补充。

*如果你想知道为什么学习打字稿是个好主意，看看我的另一篇文章* [*今天学习打字稿有意义吗？*](https://www.kirupa.com/hodgepodge/learning_typescript.htm)

*读完这个故事后，我希望你学到了一些新的东西，或者受到启发去创造一些新的东西！🤗*

*如果我给你留下了问题或一些要说的话作为回应，向下滚动并给我键入一条消息。如果你想保密，请在 Twitter @DevByRayRay 上给我发一条 [DM。我的 DM 永远是开放的😁](https://twitter.com/@devbyrayray)*

*[**通过电子邮件获取我的文章点击这里**](https://blog.byrayray.dev/subscribe) **|** [**购买 5 美元中等会员**](https://blog.byrayray.dev/membership)*

# *阅读更多*

*[](https://betterprogramming.pub/how-to-use-a-typescript-interface-4cbced319ee2) [## 如何使用 TypeScript 接口

### 通过早餐做披萨

better 编程. pub](https://betterprogramming.pub/how-to-use-a-typescript-interface-4cbced319ee2) [](https://betterprogramming.pub/classes-with-private-properties-in-typescript-3-8-9fdb91c26ab1) [## TypeScript 3.8 中具有私有属性的类

### 最后，TypeScript/JavaScript 类中的隐私

better 编程. pub](https://betterprogramming.pub/classes-with-private-properties-in-typescript-3-8-9fdb91c26ab1) [](https://betterprogramming.pub/an-introduction-to-typescript-property-decorators-1c9db23b6ca1) [## TypeScript 属性装饰器简介

### 对 TypeScript 装饰器的深入探究

better 编程. pub](https://betterprogramming.pub/an-introduction-to-typescript-property-decorators-1c9db23b6ca1) [](https://betterprogramming.pub/a-practical-introduction-to-typescript-class-decorators-afb996af0763) [## TypeScript 类装饰器实用介绍

### 使用类装饰器的 TypeScript 中着火的类

better 编程. pub](https://betterprogramming.pub/a-practical-introduction-to-typescript-class-decorators-afb996af0763)*