# OvernightJS:使用 ExpressJS 和 TypeScript 的最佳方式

> 原文：<https://levelup.gitconnected.com/overnightjs-the-best-way-to-use-expressjs-with-typescript-35d71941aade>

ExpressJS web 框架的 TypeScript 装饰器

![](img/cb9c56c1e84169272e7c4c6d67b30e51.png)

隔夜比快递快，懂吗？

# 通宵游戏攻略

OvernightJS 是一个简单的库，用于向 ExpressJS 路由添加 TypeScript 装饰器。它并不意味着作为 Express 之上的一个额外的层，或者让你远离编写后端 web APIs 的 RESTful 风格。有一些复杂的库确实添加了 TypeScript decorators 来表达，但它们实际上是带有大量文档的全新框架。如果您已经对 ExpressJs 有所了解，OvernightJS 是简单、容易的，并且几乎不需要花费任何时间来学习。

# 它是如何工作的

一旦安装了 OvernightJS，只需导入控制器装饰器，将其放在 TypeScript 类之前，并向其传递一个路由(作为字符串)。该字符串将被添加到控制器中所有方法路径的前面。您可以通过使用 decorators 为 Express GET、POST、PUT 和 DELETE 路由添加路由。您的方法将与放在快速路由回调方法中完全一样。装饰者也支持 ExpressJS 中间件。任何通常作为第二个参数传递给快速路由的中间件，只需将其作为第二个参数传递给装饰器。

所以与其这样做…

你可以这样做:)

一旦你开始积累大量的控制器和方法，这将为你节省大量的代码。你说我们开始怎么样？

# 装置

```
$ npm install --save @overnightjs/core express$ npm install --save-dev @types/express
```

# 辅导的

安装 OvernightJS 之后，导入 TypeScript 文件顶部的“控制器*”*decorator，以及任何您想要用于 API 路由的 decorator。如果你想使用 async/await，你猜怎么着？那些工作得很好。“next”参数也是可选的。在这里，我试着向你们展示尽可能多的不同路线的例子。

设置好控制器后，将其导入到服务器文件的顶部。“Server”父类是@overnightjs/core 包的一部分，是启用路由所必需的。这个父类也给了我们一个 ExpressJS 实例，我们可以像其他 express 实例一样正常地与之交互。使用下面第 18 行中的“addControllers()”方法初始化路由。这个方法可以传递给单个控制器或一组控制器。

这个库的另一个很酷的特性是你不需要使用内置的 express。Router()对象。可以通过将您选择的自定义库作为第二个参数传递给“addControllers()”方法来添加它。如果不指定路由器对象，默认的 express。使用路由器()1。下面的代码片段包含两个不同的“addControllers()”使用示例。

这就是全部了。很简单，对吧？如果您想了解 OvernightJS 的运行情况，主 GitHub 库(下面的链接)包含一个示例应用程序。

# **链接**

[](https://github.com/seanpmaxwell/overnight) [## seanpmaxwell/隔夜

### ExpressJS 服务器的 TypeScript decorators。通过创建帐户为 seanpmaxwell/隔夜开发做贡献…

github.com](https://github.com/seanpmaxwell/overnight) [](https://www.npmjs.com/package/@overnightjs/core) [## @overnightjs/core

### Express web 服务器的 TypeScript decorators。OvernightJS 项目的一部分。

www.npmjs.com](https://www.npmjs.com/package/@overnightjs/core) [![](img/d9fe9ae1a34de6a28338eae427dd6438.png)](https://levelup.gitconnected.com)