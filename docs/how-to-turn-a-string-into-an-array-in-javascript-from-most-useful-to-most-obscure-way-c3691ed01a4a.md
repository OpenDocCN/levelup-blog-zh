# 在 JavaScript 中将字符串转化为数组的 8 种方法——从最有用到最晦涩

> 原文：<https://levelup.gitconnected.com/how-to-turn-a-string-into-an-array-in-javascript-from-most-useful-to-most-obscure-way-c3691ed01a4a>

![In](img/da1a6e088cfb07b0998e7372ad9f248c.png) 在  JavaScript 中，将字符串转换成数组是一个常见的任务，可以通过许多不同的方式来完成。有许多方法可以将一个字符串拆分成一个数组，不管它是一个长字符串还是一个短字符串。已经有一些文章讨论了如何将一个字符串转换成一个数组，但是它们并没有涵盖所有的技术，也没有深入讨论每种技术的优缺点。

在这篇博客文章中，我们将研究 JavaScript 中把字符串转换成数组的一些不同方法，比如 split()方法、spread 操作符和许多其他您可能从未听说过的技术。我们还将讨论每种方法的优缺点，以便您可以根据自己的具体需求选择最佳方法。因此，如果您对学习如何在 JavaScript 中将字符串转换为数组感兴趣，让我们开始吧！

这只是众多关于 JavaScript 和软件开发的文章中的一篇。欢迎关注我们，获取更多关于 JavaScript 和软件开发的精彩内容。我们尝试一周发布多次。请确保不要错过我们的任何精彩内容。

![](img/73acd244ccaf10ac82fac91464580f8f.png)

照片由 [xandtor](https://unsplash.com/@xandtor?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 JavaScript 中，有几种方法可以将字符串转换为数组:

## 扩展运算符

```
const string = 'Hello, world!';
const array = […string];
```

spread 运算符(…)可用于将字符串扩展为数组文字，从而将字符串转换为字符数组。

好处:

*   扩展运算符简洁且易于使用。
*   它能正确处理 Unicode 字符。

陷阱:

*   spread 运算符仅适用于支持 spread 语法的现代浏览器。

## Array.from 方法

```
const string = 'Hello, world!';
const array = Array.from(string);
```

Array.from 方法从 iterable 对象(如字符串)创建一个新数组。

好处:

*   Array.from 方法得到了广泛的支持，可以在所有现代浏览器中使用。
*   它能正确处理 Unicode 字符。

陷阱:

*   Array.from 方法比其他方法更详细。

## 使用 for 循环

```
const string = 'Hello, world!';
const array = [];
for (let i = 0; i < string.length; i++) {
 array.push(string[i]);
}
```

for 循环可用于迭代字符串中的字符，并将它们推入数组。

好处:

*   for 循环是一个简单灵活的解决方案，适用于所有
    环境。
*   它能正确处理 Unicode 字符。

陷阱:

*   for 循环很冗长，可能不像其他方法那样容易理解。

## 拆分方法

```
const string = 'Hello, world!';
const array = string.split('');
```

split 方法根据指定的分隔符将字符串拆分为子字符串数组。在上面的示例中，split 方法与空分隔符一起使用，因此它将字符串拆分为一个由单个字符组成的数组。

好处:

*   拆分法使用简单，容易理解。
*   它允许您指定一个分隔符来分割字符串，这样您就可以控制如何将字符串分割成子字符串。

陷阱:

*   split 方法不能正确处理 Unicode 字符。例如，如果字符串包含一个占据两个或更多代码点的 Unicode 字符，split 方法将无法正确拆分它。

## match()方法

```
const string = "hello world";
const array = string.match(/\w/g);
// ['h', 'e', 'l', 'l', 'o', 'w', 'o', 'r', 'l', 'd']
```

要使用 match()方法，可以将一个 regex 模式作为参数传递，该方法将返回一个包含匹配项的数组。match()方法将返回在字符串中找到的所有匹配项的数组，如果没有找到匹配项，则返回 null。

好处:

*   它能正确处理 unicode
*   简洁明了
*   根据数组的大小，正则表达式在较大的数组上可能会更快

陷阱:

*   没有多少人会理解正则表达式

## 正则表达式

```
const string = "hello world";
const regex = /\w/g;
const array = [];
let match;
while (match = regex.exec(string)) {
 array.push(match[0]);
}
// ['h', 'e', 'l', 'l', 'o', 'w', 'o', 'r', 'l', 'd']
```

或者，可以使用 regex 文本创建一个 regex 对象，并使用 exec()方法迭代字符串中的匹配项。此方法允许您迭代字符串中的所有匹配项，并将它们放入一个数组中。这两种方法都使用正则表达式来匹配字符串中的单个字符，并返回包含所有匹配项的数组。所以，好处和缺点本质上与 match()方法相同):

利益

*   它能正确处理 unicode
*   简洁明了
*   根据数组的大小，正则表达式在较大的数组上可能会更快

陷阱:

*   没有多少人会理解正则表达式

## map()方法

```
const string = "word";
const array = Array.prototype.map.call(string, letter => letter);
```

你没看错。您可能熟悉 map()函数。在这个例子中，它以数组的形式遍历字符串，并返回每个字母。

好处:

*   正确处理 unicode

陷阱:

*   不是最习惯的方式

## concat()方法

```
const string = new String("asdf");
string[Symbol.isConcatSpreadable] = true;
const array = [].concat(string);
```

使用 ES6，您基本上可以重新定义 concat()方法，使其行为类似于 spread 运算符。

好处:

*   它能正确处理 unicodes

陷阱:

*   该字符串必须被定义为一个对象，而不是一个字符串才能工作。此外，定义字符串对象可能会对性能产生负面影响。
*   它修改 String 对象的 Symbol.isConcatSpreadable 属性，如果应用程序中的其他代码依赖于它，这可能会产生意想不到的后果。
*   它不是将字符串转换为数组的标准方法，其他开发人员阅读您的代码时可能不会马上理解它。
*   Symbol.isConcatSpreadable 是在 ES6 中引入的，这意味着该属性只能在较新的浏览器上使用

正如你所看到的，有很多方法可以将字符串转换成数组。虽然不是每一个都是地道的。有些不能正确处理 unicodes。其他的很难理解。因此，请再次通读一下优缺点列表，以便在尝试将字符串转换为数组时做出明智的决定。根据我们的经验，最好坚持使用 spread 操作符、Array.from()方法或 for 循环。

![](img/5c7fdb823e2c7f4190f716ff6bed224c.png)

你以前试过把一个字符串变成一个数组吗？在什么背景下？或者你有什么问题吗？请在下方留言评论。也别忘了跟着我们。我们每周发表多篇文章。所以，不要错过任何一个。再见