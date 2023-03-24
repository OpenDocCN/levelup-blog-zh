# ES2020 的新功能—字符串、数字和这个

> 原文：<https://levelup.gitconnected.com/new-es2020-features-strings-numbers-and-this-4ecabbca1162>

![](img/01789622992033a76e7098833bd4b70a.png)

照片由 [Gift Habeshaw](https://unsplash.com/@gift_habeshaw?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

自 2015 年以来，JavaScript 一直在快速发展，每次迭代都会推出许多新功能。

JavaScript 语言规范的新版本几乎每年都在更新，新语言特性提案的完成速度比以往任何时候都要快。

这意味着新特性正以前所未有的速度融入现代浏览器和 Node.js 等其他 JavaScript 运行时引擎。

2019 年，有许多新功能处于“阶段 3”阶段，这意味着它非常接近完成，浏览器现在开始支持这些功能。

如果我们想将它们用于生产代码，我们可以使用 Babel 之类的东西将它们转换成旧版本的 JavaScript，以便在需要时可以在旧版本的浏览器中使用，如 Internet Explorer。

在本文中，我们将研究在所有环境中获取全局对象的`matchAll`方法、数字分隔符和`globalThis`对象。

# String.matchAll()

在当前版本的 JavaScript 中，没有 string 方法可以用来搜索所有出现的字符串和捕获组。

要搜索一个字符串的所有实例，我们必须编写自己的正则表达式，并使用`match`方法来搜索我们想要搜索的子字符串。

例如，我们可以使用带有我们自己的正则表达式的`match`方法来搜索一个字符串的所有实例，如下面的代码所示:

```
const str = 'foo bar foo';
const matches = str.match(/foo/g);
console.log(matches);
```

当我们运行上面的代码时，我们应该得到:

```
[
  "foo",
  "foo"
]
```

`match`方法根据正则表达式得到一个字符串的所有匹配的数组。注意，我们在正则表达式文字的末尾有一个`g`标志。否则，将只返回第一个匹配项。当包含`g`标志时，捕捉组匹配不包含在内。

现在我们有了另一个叫做`matchAll`的方法，它与`match`非常相似。

然而，它返回一个不可重启的迭代器，因此我们可以循环遍历由`matchAll`方法返回的迭代器，以获得找到的所有匹配。

它还接受一个正则表达式，并用`RegExp`构造函数将任何非正则表达式对象转换成正则表达式。

因为它返回一个迭代器，所以我们可以对它使用扩展操作符、`for...of`循环和`Array.from`方法。

与`match`方法的另一个区别是，捕获组结果从`matchAll`方法返回，而不是从`match`方法返回。

如果我们在下面的代码中使用`match`:

```
const str = 'foo bar foo foobar foofoobarbarbar foobarbar';
const matches = str.match(/foo(bar)+/g);
```

我们得到:

```
[
  "foobar",
  "foobarbarbar",
  "foobarbar"
]
```

如果我们使用下面代码中的`matchAll`:

```
const str = 'foo bar foo foobar foofoobarbarbar foobarbar';
const matches2 = str.matchAll(/foo(bar)+/g);
```

我们得到:

```
[
  [
    "foobar",
    "bar"
  ],
  [
    "foobarbarbar",
    "bar"
  ],
  [
    "foobarbar",
    "bar"
  ]
]
```

正如我们所看到的，捕获组`(bar)`返回带有`matchAll`的每个结果，但是在正则表达式中，它没有返回带有`g`标志的`match`。

另外，另一个重要的区别是由`matchAll`方法返回的迭代器是不可重启的。一旦迭代器返回的值用完了，如果我们想再次得到结果，我们必须再次调用方法来创建一个新的迭代器。

# 数字分隔符

在早期版本的 JavaScript 中，阅读长数字是一件痛苦的事情，因为没有任何东西来分隔数字。

这意味着人眼几乎不可能分辨出大数字中有多少位数。

现在 JavaScript 增加了数字分隔符，用下划线将数字分成更小的数字组。

例如，我们可以像在下面的代码中一样使用它们:

```
const x = 1_000_000_000_000
```

如果我们在上面运行`console.log`，那么`x`将显示 10000000000。它也适用于十进制数:

```
const x = 1_363_372_373.378_473
```

如果我们在上面运行`console.log`，那么`x`将显示 1363372373.378473。我们不必把数字分成 3 个一组。它可以按我们喜欢的任何方式分组，所以如果我们有:

```
const x = 37378_44748
```

如果我们运行这个函数，就会从`x`的`console.log`中得到 3737844748。它也适用于其他基数，如二进制、十六进制或八进制数。例如，我们可以写:

```
const binary = 0b111_111_111;
const bytes = 0b1111111_11111111_1111111;
cons**t** hex = 0xFAB_F00D;
```

唯一的问题是，如果我们想将数字串传递给`Number`或`parseInt`函数，就不能在数字串中使用下划线。如果我们写`Number(‘123_456’)`，那么我们得到`NaN`。如果我们运行 `parseInt(‘123_456’)`，那么我们得到 123。正如我们所看到的，它们都是不正确的。这意味着当使用数字分隔符时，我们应该坚持数字文字。

![](img/938cd96db6fb64f79585b080697833af.png)

照片由[凯尔·格伦](https://unsplash.com/@kylejglenn?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# globalThis

JavaScript 的最新版本也有`globalThis`方法。这让我们可以获得应用程序的真正全局对象，而不是依赖于`this`对象，后者可能会根据它所在的上下文而改变。例如，如果我们运行以下代码:

```
console.log(globalThis);function foo() {
  console.log(globalThis)
}
new foo();
```

我们可以看到两个`console.log`都输出了`window`对象，这个对象包含了浏览器标签的内容。

如果我们在`foo`函数中`console.log`了`this`关键字而不是`globalThis`如果我们用`new`关键字创建了一个`foo`的新实例，我们将不会得到`window`对象，而是得到`foo`函数。

例如，如果我们用关键字`this`替换`globalThis`，如下面的代码所示:

```
console.log(this);function foo() {
  console.log(this)
}
new foo();
```

我们将在第一个`console.log`中获取`window`对象，但是`foo`函数中的`console.log`将为我们获取`foo`构造函数。其他环境，如 web 或服务人员，`self`将是一个全局对象。

在 Node.js 中，全局对象被称为`global`。`globalThis`适用于所有环境，因此我们可以使用它来访问真正的全局对象。

它适用于许多浏览器的最新版本和 Node.js 的最新版本。

它保证在窗口和非窗口环境中都可以工作。它通过引用我们不能直接访问的实际全局对象周围的代理来工作。

# 结论

在当前版本的 JavaScript 中，没有字符串方法可以用来搜索字符串和捕获组的所有匹配项。

使用`matchAll`方法，我们可以找到所有的匹配，并得到所有的捕获组。在早期版本的 JavaScript 中，阅读长数字是一件痛苦的事情，因为没有任何东西来分隔数字。

这意味着人眼几乎不可能分辨出大数字中有多少位数。

现在 JavaScript 增加了数字分隔符，用下划线将数字分成更小的数字组。

我们可以用分隔线随意分组。JavaScript 的最新版本也有`globalThis`方法。

这让我们可以获得应用程序的真正全局对象，而不是依赖于`this`对象，后者可能会根据它所在的上下文而改变。

它还可以在窗口和非窗口环境中工作，这样像 Node.js 这样的运行时环境也可以使用`globalThis`。