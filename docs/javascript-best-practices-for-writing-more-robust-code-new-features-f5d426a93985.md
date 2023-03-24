# 编写更健壮代码的 JavaScript 最佳实践—新特性

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-for-writing-more-robust-code-new-features-f5d426a93985>

![](img/10d25ef6bfb6d321384242daa37d2912.png)

由[理查德·伯顿](https://unsplash.com/@richardworks?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的 JavaScript 代码。

在本文中，我们将了解如何向我们的应用程序添加填充和语言功能，以添加我们目标浏览器中没有内置的功能。

# 多填充物

聚合填充对于使我们的客户端 JavaScript 代码健壮非常重要。聚合填充是添加浏览器可能不支持的功能的脚本。

它实现了浏览器可能还不支持的特定 JavaScript APIs。我们针对的浏览器中没有的 JavaScript 标准库的一部分也需要 polyfills。

它们只是我们添加的库，就像其他库一样。例如，像 Fetch API 或网络信息 API 这样的 API 可能还不可用。

但是，我们希望使用它们，以便我们可以发出 HTTP 请求，分别添加一个额外的库或监视网络连接类型的变化。

然后我们需要为它们添加 polyfills。如果我们需要为不支持获取 API 的浏览器添加对获取 API 的支持，那么我们需要按照[https://github.com/github/fetch](https://github.com/github/fetch)中的说明安装一个获取填充。

如果本地版本不可用，我们应该只使用 polyfill 版本，因为本地版本更快，因为它是内置在浏览器中的，而不是动态添加的。

同样，在 https://www . npmjs . com/package/Network-Information-API-poly fill 中有一个网络信息 API polyfill，这样我们就可以在旧浏览器中使用网络信息 API。

添加到浏览器全局对象的新功能，如`Array`可能也需要多填充才能工作。

例如，如果我们需要在 IE 中使用数组实例的`find`方法，那么我们需要为它添加一个 polyfill。它的脚本可以从[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Array/find](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find)获得。

还有[https://polyfill.io/v3/url-builder/](https://polyfill.io/v3/url-builder/)，这是一个一站式商店，提供我们需要的所有多种功能，为浏览器添加新的本地功能，这些功能可能是不可用的。

音频和视频流访问等一些功能没有聚合填充，因为它们需要本机硬件支持。因此，如果我们的应用程序支持没有内置这些功能的旧浏览器，则需要在我们的应用程序中禁用这些功能。

# 巴别塔编译器

要添加许多浏览器不支持的新 JavaScript 功能，我们需要在浏览器中添加一个编译步骤，这样当我们在应用程序中使用新的 JavaScript 语言功能时，它会编译成与我们的目标浏览器兼容的代码。

我们可以使用 Babel 编译器来完成这项工作。除了实验性的特性之外，它还支持所有当前的特性。

在生产应用程序中最好坚持使用当前的版本，因为实验性的版本可能会有突破性的变化。

将成为标准的更改处于第 4 阶段，这意味着它们肯定会作为标准添加到 JavaScript 语言中。

有用的 JavaScript 特性将成为标准特性，如可选的链接操作符。

例如，如果我们有一个用 Parcel 编译的普通 JavaScript，它已经和 Babel 一起提供了，那么我们可以通过运行以下命令来添加`@babel/plugin-proposal-optional-chaining` Babel 插件:

```
npm i @babel/plugin-proposal-optional-chaining
```

然后在项目的`.babelrc`文件中，我们添加:

```
{
  "plugins": [
    "@babel/plugin-proposal-optional-chaining"
  ]
}
```

然后我们可以在 JavaScript 代码中使用它，添加:

```
const foo = {};
const x = foo?.bar?.baz;
console.log(x);
```

Babel 支持更多的特性，而不仅仅是可选的链接操作符。它也适用于 Node.js 和客户端 JavaScript。

Babel 支持更多的 JavaScript 特性，大部分没有插件。像`let`、`const`、`for...of`这样的特征，模块、地图、集合、符号、剩余和扩展运算符、析构等。都是巴别塔支持的。

我们还可以添加 Babel 作为遗留应用程序或原型的脚本，方法是添加:

```
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
```

到我们的 HTML。

然后，如果我们有一个将`type`属性设置为`text/babel`的脚本标签，那么我们可以添加现代的 Javascript 特性，而不用担心在旧浏览器中破坏应用程序。

![](img/bacfb76401bfd46a56ca0607641da782.png)

阿瑟·戈尔茨坦在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

聚合填充是为旧浏览器中可能不可用的功能添加支持的脚本。

要添加我们的应用所针对的浏览器中可能没有的原生 JavaScript 特性，我们可以使用 Babel 编译器来添加对它们的支持。我们也可以添加巴别塔插件来支持更多的前沿功能。

此外，我们可以通过使用 Babel 的脚本标签版本来动态编译现代 JavaScript 代码，并添加我们自己的脚本，将`type`属性设置为`text/babel`以添加对它的支持，而无需构建。