# JavaScript 基础—模块

> 原文：<https://levelup.gitconnected.com/javascript-basics-modules-34d700d805c5>

![](img/d7423aa55180ed2950a181c7727e497e.png)

照片由 [Jannis Brandt](https://unsplash.com/@jannisbrandt?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

JavaScript 是世界上最流行的编程语言之一。为了有效地使用它，我们必须了解它的基本知识。

在本文中，我们将看看如何定义和使用模块。

# 模块

我们可以使用模块将代码分成易于管理的部分。

它们就像积木一样，我们可以把它们拼凑成一个程序。

模块文件很好，因为成员不共享全局名称空间。

因此，它们不能互相干扰。

# 包装

多个模块可以组合成一个包。

包是一组代码，可以被一个或多个模块分发和使用。

当在它们中发现问题时，就更新包。

JavaScript 包由 NPM 包库提供。

NPM 是一项在线服务，我们可以上传和下载软件包以及帮助我们安装它们的程序。

NPM 上有超过 50 万个包裹。

大多数都是垃圾，但大多数有用的软件包可以在那里找到。

那里有高质量的软件包可供下载，它们非常有价值。

大多数 NPM 软件包都有许可证，明确允许人们使用它。

有些可能还要求我们用相同的许可证发布我们的代码。

然而，大多数都没有那么苛刻。

# 简易模块

2015 年之前，JavaScript 没有模块系统。

然而，人们仍然需要一种方法来组织他们的代码，因此第三方模块系统被设计来解决这个问题。

此外，我们将代码放在函数中，这样它们就保存在函数中。

例如，我们可以写:

```
const module = (() => {
  let foo = 1;
  let bar = 2;
  //...
  return {
    bar
  }
})()
```

只返回我们想要公开的项目。

我们创建一个函数，然后调用它返回我们想要的结果。

# 将数据评估为代码

我们可以用`eval`函数运行字符串代码。

这让我们可以将代码存储在一个字符串中，并在需要时用`eval`运行它们。

同样，我们可以用`Function`构造函数做同样的事情。

然而，它们都不好，因为我们必须将代码存储在字符串中，这意味着它们几乎不可能被调试。

安全风险也很高，因为我们从字符串运行代码，而字符串可以从任何地方提供。

此外，不能对代码进行优化，因为它们都在字符串中。

# CommonJS

Node.js 使用 CommonJS 作为它们的模块系统。

标准的 JavaScript 模块只有最近版本的 Node 才有，所以仍然有很多包有 CommonJS 模块。

我们使用`require`函数获取模块内容并返回它。

为了导出一个模块，我们使用`module.exports`。

所以我们可以写:

```
module.exports = {
  getDate() {
    return new Date();
  }
}
```

创建一个模块并导出`getDate`函数。

然后我们可以通过写来使用它:

```
const { getDate } = require('./module');console.log(getDate());
```

CommonJS 模块在 NPM 上仍然很常见。

# ES 模块

ES 模块是标准的 JavaScript 模块。

它是在 2015 年作为标准引入的。

主要概念和依赖关系保持不变，但细节有所不同。

我们使用`import`和`export`语句，它们分别是使用和创建模块语言的一部分。

`export`使用函数、类或变量。

我们可以在一个模块中有多个`export`语句来导出多个成员。

然而，如果我们用关键字`default`创建一个默认导出，那么我们只能有一个导出。

我们可以写:

`module.js`

```
export const foo = 1;
export const bar = 1;
const baz = 2;
export default baz;
```

创建多个导出。

然后，我们可以通过编写以下内容来导入它们:

`index.js`

```
import baz, { foo, bar } from "./module";console.log(bar, foo, baz);
```

我们导入了默认的导出，没有用花括号括起来，命名的导出用花括号括起来。

![](img/182c7688177fac4b27a935616df28820.png)

照片由[布鲁克·拉克](https://unsplash.com/@brookelark?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 集束

我们必须将代码编译成普通的旧 JavaScript，以便它们可以在浏览器中运行。

它们还需要缩小，这样用户就不必下载这么多数据。

许多模块和应用在发布前都要经历多个转换阶段。

# 结论

模块对于划分代码很有用。

最流行的模块系统是 CommonJS，主要用于 Node.js 应用程序和标准 JavaScript 模块。

我们必须将它们转换成包，以便浏览器可以在其中运行它们。