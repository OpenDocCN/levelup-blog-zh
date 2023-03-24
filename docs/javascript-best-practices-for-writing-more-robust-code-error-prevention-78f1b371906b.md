# 编写更健壮代码的 JavaScript 最佳实践—错误预防

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-for-writing-more-robust-code-error-prevention-78f1b371906b>

![](img/eaae6010aa5651951c1bc6eaea37d490.png)

[Geronimo Giqueaux](https://unsplash.com/@ggiqueaux?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的 JavaScript 代码。

在本文中，我们将研究在处理 JSON 时编写更健壮的代码的方法，以及在编写代码时更好地处理错误和防止错误发生的最佳实践。

# 使用 JSON.parse 或 JSON.stringify 时使用 try…catch

`JSON.parse`和`JSON.stringify`遇到问题会抛出错误。

每当`JSON.parse`在字符串中遇到无效的 JSON，它就会抛出一个错误。`JSON.stringify`试图将循环结构转换成 JSON 时抛出错误。

因此，我们应该使用`try...catch`来包装`JSON.stringify`和`JSON.parse`，以防止错误阻止我们的程序运行。

例如，我们应该这样写:

```
try {
  const json = '{}';
  const parsed = JSON.parse(json);
} catch (ex) {
  console.error(ex);
}
```

这样`JSON.parse`就不会在解析 JSON 遇到问题时使我们的程序崩溃。对于使用它的代码，我们也可以用`JSON.stringify`替换`JSON.parse`。

# 使用 ESLint 或其他 Linter 尽早捕捉错误

ESLint 是 JavaScript 的标准 linter。它检查语法错误和混乱的代码，以防止开发人员提交多种有问题的代码或潜在的 bug。

它有一个很大的规则列表，用于检查可能的错误或无用和混乱的代码。

例如，它检查 getters 中的 return 语句。这是因为使用一个不返回 JavaScript 或类中任何内容的 getter 是没有意义的。一个不返回任何东西的 getter 是没有用的，如果它存在的话，很可能是一个错误。

其他事情包括在条件表达式中不允许赋值操作符，因为我们可能想使用`==`或`===`来比较事物，但我们忘记了额外的等号。

在 https://eslint.org/docs/rules/有更多的规则。

它很容易为任何项目设置。我们可以通过运行以下命令将`eslint`安装到我们的项目文件夹中:

```
npm install eslint --save-dev
```

然后，我们通过运行以下命令来创建 ESLint 配置文件:

```
npx eslint --init
```

自定义我们想要启用的规则。它非常灵活，我们不必启用对所有规则的检查。

它也有许多插件来添加更多的规则。流行的规则包括 Airbnb 和默认规则。我们还可以包括 Node.js 规则、Angular、React、Vue 等。

# 不要弄乱内置的对象原型

我们永远不应该弄乱内置全局对象的原型。像`Object`、`String`、`Date`等对象。所有的实例、方法和变量在它们的原型中都不应该被打乱。

如果我们错误地改变了它们，那么我们的代码可能不会按照我们期望的方式工作，因为它们经常从这些内置对象中调用实例方法，并且在我们的代码中操纵它们可能会破坏我们的代码。

本机对象的成员也可能与我们自己代码中的成员发生名称冲突。

这也令人困惑，因为如果我们改变内置对象的原型，很多人可能会认为它实际上是标准库的一部分。

我们不希望人们犯这样的错误，编写更多糟糕的代码，并在以后制造更多的问题。

因此，我们绝不应该修改内置对象及其原型。

如果我们需要内置本机对象没有提供的任何新功能，那么我们应该从头开始编写自己的代码，并在必要时引用内置本机项目。

此外，如果我们想使用你的应用所针对的浏览器中不存在的新的本地方法，那么我们可以使用 polyfills 来添加该功能，而不是从头开始实现它。

![](img/ca6208417ec2dd61cc36368fec40f740.png)

肖恩·麦基在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 总是使用严格模式

JavaScript 曾经让人们做很多人们不应该做的事情。

当我们编写不应该编写的代码时，严格模式会抛出错误，比如给`null`或`undefined`赋值，或者通过省略`var`、`let`或`const`意外创建全局变量。

引发错误的其他事情包括试图扩展应用了`Object.freeze`方法的对象的新属性。

例如，如果没有严格模式，以下内容将不起作用:

```
const foo = {};
Object.preventExtensions(foo);
foo.newProp = 'foo';
```

但是，在严格模式下:

```
'use strict'
const foo = {};
Object.preventExtensions(foo);
foo.newProp = 'foo';
```

我们将得到一个错误“未捕获类型错误:无法添加属性 newProp，对象不可扩展”。

它还禁止我们使用可能在 JavaScript 未来版本中使用的关键字和结构。例如，我们不能声明一个名为`class`的变量，因为它是一个保留关键字。

默认情况下，严格模式适用于所有 JavaScript 模块。然而，默认情况下，旧式脚本没有严格模式。因此，我们应该通过在我们代码的顶部添加`'use strict'`来确保它们有严格的模式。

我们也可以在函数中添加`'use strict'`,但是我们肯定希望严格模式无处不在，所以我们应该把它放在脚本文件的顶部。

由于严格模式防止我们犯这么多种错误，我们应该一直打开它。

# 结论

使用 ESLint 和 strict 模式很容易防止 JavaScript 代码错误。它们防止我们犯各种各样的错误，这将节省我们调试糟糕代码的大量时间。

此外，我们应该使用`try...catch`在解析和字符串化 JSON 时捕捉错误。