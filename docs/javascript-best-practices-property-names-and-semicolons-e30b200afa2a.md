# JavaScript 最佳实践—属性名和分号

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-property-names-and-semicolons-e30b200afa2a>

![](img/969ecf938017ca3ef964304a264f21d7.png)

照片由[弗兰·霍根](https://unsplash.com/@franagain?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将探讨向对象添加属性以及向 JavaScript 代码添加分号的最佳方式。

# 对象文字属性名的引号

只有当属性不是作为字符串写的，作为属性名无效时，我们才应该在对象文字属性名两边加上引号。

例如，我们不把下面对象的属性用引号括起来，因为属性名是一个有效的标识符。

如果我们有以下对象:

```
const foo = {
  a: 1
};
```

那么我们不需要用引号将`a`括起来，因为`a`在 JavaScript 中已经是一个有效的标识符名称。

在 JavaScript 中，标识符区分大小写，可以包含 Unicode 字母、`$`、`_`和数字。但是，有效的 JavaScript 标识符不能以数字开头。

如果我们想用一个违反上述任何规则的标识符来命名一个属性，那么我们需要用引号将它们括起来，这样它们就可以写成字符串。

例如，如果我们想要一个两个单词之间有空格的属性名，我们可以编写以下代码:

```
const foo = {
  'foo bar': 1
};
```

在代码中，我们有`foo`对象，它有属性`'foo bar'`，由于`foo`和`bar`之间的空格，这个属性必须用引号括起来。

那么我们只能通过使用如下括号符号来访问它:

```
foo['foo bar']
```

获取属性的值。

因此，如果它是一个有效的标识符，那么我们不需要在属性名两边加引号。否则，我们只能用括号符号来访问它。

# 字符串中反斜线、双引号或单引号的一致使用

在所有 3 个字符串分隔符中，反勾号是最通用的，因为我们可以用它来创建模板字符串和常规字符串。

模板字符串允许我们将表达式插入到字符串中，而不是将表达式与多个子字符串连接起来

插值比字符串连接更容易阅读和输入。我们需要编写的代码量要少得多，因为我们不需要到处都有串联操作符。

例如，我们不写:

```
const baz = 1;
const foo = 'foo ' + baz + ' bar';
```

我们可以将上面的字符串写成模板字符串，如下所示:

```
const baz = 1;
const foo = `foo ${baz} bar`;
```

在上面的代码中，我们将表达式`baz`放入模板字符串中，而不是连接。

阅读和输入模板字符串都更容易。

使用模板字符串，我们不必总是插入表达式，我们只需创建一个常规字符串，如:

```
const foo = `foo bar`;
```

此外，我们可以在字符串中使用单引号和双引号，而不必用反斜杠进行转义。

例如，我们可以编写以下代码，将单引号和双引号一起用作字符串中的字符，而不是字符串分隔符:

```
const foo = `'foo' "bar"`;
```

这是使用反斜杠作为字符串分隔符的另一个优点，因为单引号和双引号在句子和子句中使用得更多。然而，反勾号并不是常用的英文标点符号。

因此，反斜杠是很好的字符串分隔符。它甚至更好，因为我们使用反斜线来创建 JavaScript 模板字符串，其中可以插入 JavaScript 表达式。

单引号和双引号适用于引用文本。

![](img/bf97fc4d29300f4e6244b743113bdfaf.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[卢克阿什·vaňátko](https://unsplash.com/@otohp_by_sakul?utm_source=medium&utm_medium=referral)的照片

# 添加我们自己的分号比自动分号插入(ASI)要好

当我们编写 JavaScript 代码时，我们应该总是显式地写出分号，而不是依赖自动分号插入功能来自动插入它们。

ASI 并不总是把它们插在我们想要的地方。例如，如果我们有以下代码:

```
const foo = () => {
  return 
  {
    name: "foo"
  }
}
```

那么它将被解释为:

```
const foo = () => {
  return;
  {
    name: "foo"
  };
}
```

因此当我们调用`foo`时，它将返回`undefined`。

我们可能不希望这样，所以我们应该自己输入分号，如下所示:

```
const foo = () => {
  return {
    name: "foo"
  };
}
```

# 结论

对于不是有效的 JavaScript 标识符名称的属性名称，我们只需要用引号将它们括起来。

反斜杠是最好的字符串分隔符，因为它可以创建模板字符串和常规字符串，并将单引号和双引号作为引号分隔符，而不是字符串分隔符。