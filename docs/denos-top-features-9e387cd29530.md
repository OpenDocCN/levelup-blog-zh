# Deno 为什么牛逼

> 原文：<https://levelup.gitconnected.com/denos-top-features-9e387cd29530>

你可能听说过 Deno，传说中的新 Javascript 运行时，据说它解决了 node 的许多固有问题。Deno 由 NodeJS 的开发者 Ryan Dahl 创建，它包含了各种各样的特性，使开发者的生活变得更加简单。

像大多数 JS 开发人员一样，当我听到另一个 JS 框架时，我首先想到的是恐惧和为学习新技术的痛苦过程做准备，但 Deno 在开发现代和快速的 JavaScript 代码方面令人惊讶地令人敬畏。

我们先来看看 2020 年 Deno 为什么对开发者这么有吸引力。

![](img/f22b785dc7e387918e7b4968c2ff283b.png)

照片由 [Safar Safarov](https://unsplash.com/@codestorm?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 对现代 JS 导入语法的依赖

回到 2009 年创建 Node 时，模块导入语法依赖于`require`方法。现代 Javascript 使用`import`语法。例如，让我们来看看这段代码:

```
// Traditional JS Method
const module = require('module');

// ES6 Module Method
import { module } from 'module';
```

如果您使用像 React 或 Angular 这样的现代框架，您可能会使用 ES6 模块语法。默认情况下，Deno 使用 ES6 模块语法。

**为什么 ES6 模块导入语法更好**

1.  使用`import`，你可以有选择地从一个包中加载模块，节省内存
2.  使用`require`，加载是同步的(意味着它发生在进程前台)，使用`import`加载是异步的，这极大地提高了导入模块时的性能。

## 分散包

对于 NodeJS，您可能习惯于使用 NPM 来跟踪和加载使用`package.json`的模块。每当您想要使用外部软件包时，您必须首先安装该软件包:

```
npm i package
```

然后导入它:

```
const moment = require("moment")
```

每当有人想在本地运行你的包时，他们必须单独安装所有的包。如果您在您的机器上运行依赖相同包的多个项目，没有简单的方法在项目之间共享包，所以重复的包将被安装在您的机器上，浪费空间。

在 Deno 中，包是从 URL 导入的:

```
import { moment } from 'https://deno.land/x/moment/moment.ts.'
```

Deno 在安装后会自动在你的机器上缓存包，所以**包只安装一次**。

## 本机打字稿

如果你不知道什么是 TypeScript，你可能应该在这里做一些阅读。通常，在 Node 中，让 TypeScript 工作是一个多步骤的过程。您必须安装 typescript，更新`package.json`、`tsconfig.json`，并确保您的模块支持@types。

有了 Deno， **TypeScript 支持是原生的！**

## 顶级等待

在 Node 中，`await`关键字只能在异步函数中使用:

```
const getData = async () => {
	const data = await fetch('https://google.com');
	const result = await data.json();
}
```

有了 Deno，你可以在任何地方使用 await，包括顶级代码，所以你不必在使用 await 之前声明一个异步函数！

```
// No Async Needed!
const data = await fetch('https://google.com');
const result = await data.json();
```

这是一个巨大的改进，使代码更简单、更容易编写！

## 对浏览器 API 的访问

使用浏览器 API，包括访问像 fetch 这样的方法，默认情况下通常是不可访问的，你必须安装一个 NPM 包。

Deno 自动访问浏览器 API，所以你可以调用 fetch 而不需要导入任何其他的包。

这大大简化了代码，并且消除了导入额外模块的必要。

![](img/96155a0f8abce22318a9b6c17bf7fbd4.png)

Javier Allegue Barros 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 德诺的未来

除此之外，Deno 还有许多其他优点，远远超出了本文的范围。

结合起来，所有这些特性使得编写干净、现代和快速的 JavaScript 代码变得更加容易。作为一个 React 和 Angular 开发者，Deno 的现代特性和原生 TypeScript 支持自然很有吸引力。

Deno 会取代 NodeJS 吗？可能不会很快。NodeJS 在市场上已经根深蒂固，但是越来越多的 JavaScript 开发人员正在转向 Deno 进行他们的下一个项目。

# 保持联络

有很多内容，我很感谢你读我的。我是一个年轻的企业家，我写关于软件开发和我经营公司的经验。你可以在这里注册我的简讯

请随时联系我，在 Linkedin 或 Twitter 上与我联系。