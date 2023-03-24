# 用 JavaScript 的 RelativeTimeFormat 构造函数格式化相对时间

> 原文：<https://levelup.gitconnected.com/formatting-relative-time-with-javascripts-relativetimeformat-constructor-a5c584ce79a8>

![](img/2846385d5a275e4405ac3e5be784a377.png)

[阿格巴洛斯](https://unsplash.com/@agebarros?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

使用`Intl.RelativeTimeFormat`构造函数，我们可以轻松地以一种区域敏感的方式格式化相对时间。我们可以用不同的方式来设计风格，用我们想要的方式来格式化它。这让我们可以格式化相对时间字符串，而没有操作字符串带来的麻烦。构造函数有两个参数。第一个参数是一个区域设置字符串或此类字符串的数组。第二个参数是一个对象，它接受各种参数来按照我们想要的方式调整相对时间字符串。这个构造函数的实例有几个返回格式化字符串的方法，格式化字符串是子字符串的数组，还有一个方法返回我们为格式化字符串设置的选项。

`Intl.RelativeTimeFormat`构造函数的第一个参数是区域设置，它应该是一个 [BCP 47 语言标签](https://github.com/libyal/libfwnt/wiki/Language-Code-identifiers)或者一个这样的区域设置字符串数组。这是一个可选参数。

第二个参数接受一个具有几个属性的对象— `localeMatcher`、`numeric`和`style`。

`localeMatcher`选项指定要使用的语言环境匹配算法。可能的值是`lookup`和`best fit`。查找算法搜索区域设置，直到找到符合正在比较的字符串的字符集的区域设置。`best fit`找到至少比`lookup`算法更适合的语言环境。

`numeric`选项让我们设置如何输出格式化字符串信息的选项。可能的值有`always`，比如“2 天前”，或者`auto`，比如“昨天”。`auto`允许我们不总是使用数值输出。`style`选项让我们改变国际化消息的长度。可能的值有`long`、`short`或`narrow`。`long`会输出类似“2 个月内”的内容，`short`会输出类似“2 个月内”的内容，而`narrow`应该是类似于“在 2 个月内”在某些地区，它可能类似于`short`样式。

![](img/80194c78a5d9515eb64ee799f6eee45d.png)

由[凯拉·奥托](https://unsplash.com/@kaylahotto?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

`Intl.RelativeTimeFormat`构造函数的实例有一些方法。它有一个`format`方法，可以根据区域设置和构造函数中给定的格式化选项来获取带值和单位的格式化相对时间字符串。`formatToParts`方法类似于`format`方法，除了格式化字符串作为数组而不是字符串返回。`resolvedOptions`方法返回一个对象，该对象带有我们在构造函数中设置的选项，用于格式化字符串和设置的区域设置。

`format`方法有两个参数。第一个是相对日期的数量值，第二个是字符串形式的时间单位。例如，我们可以用下面的代码中的`format`方法格式化相对日期:

```
const rtf = new Intl.RelativeTimeFormat("en", {
  localeMatcher: "best fit",
  numeric: "always",
  style: "long",
});console.log(rtf.format(-1, "day"));
```

上面的代码将记录“1 天前”，因为我们将相对日期的值指定为-1，这意味着今天之前的 1 天，时间单位是`day`。我们也可以放入其他单位。例如，如果我们想要分钟，那么我们得到:

```
const rtf = new Intl.RelativeTimeFormat("en", {
  localeMatcher: "best fit",
  numeric: "always",
  style: "long",
});console.log(rtf.format(-10, "minute"));
```

然后我们从`console.log`语句中得到‘10 分钟前’。我们也可以改变样式和长度。例如，我们可以写:

```
const rtf = new Intl.RelativeTimeFormat("en", {
  localeMatcher: "best fit",
  numeric: "auto",
  style: "short",
});
console.log(rtf.format(10, "minute"));
```

然后我们得到' 10 分钟内'从`console.log`语句中，因为我们有正 10 而不是负 10，即距离当前时间 10 分钟。此外，我们有`short`样式，它是单位的缩写。

我们还可以为不同的地区更改地区。例如，我们可以写:

```
const rtf = new Intl.RelativeTimeFormat("zh-hant", {
  localeMatcher: "best fit",
  numeric: "auto",
  style: "long",
});
console.log(rtf.format(1, "minute"));
```

This gets the relative date-time string in Chinese Traditional characters instead of English. If we run the `console.log` statement in the code above, we get ‘1 分鐘後’, which means 1 minute later.

我们可以用`formatToParts()`方法获得字符串部分数组中的格式化字符串。它返回格式化字符串的子字符串数组。例如，我们可以像下面的代码那样调用它:

```
const rtf = new Intl.RelativeTimeFormat("en", {
  localeMatcher: "best fit",
  numeric: "always",
  style: "long",
});const parts = rtf.formatToParts(-1, "day");
console.log(parts);
```

上面的代码会让我们:

```
[
  {
    "type": "integer",
    "value": "1",
    "unit": "day"
  },
  {
    "type": "literal",
    "value": " day ago"
  }
]
```

用上面代码中的`console.log`语句。

这种方法同样适用于非英语地区。例如，我们可以写:

```
const rtf = new Intl.RelativeTimeFormat("zh-hant", {
  localeMatcher: "best fit",
  numeric: "auto",
  style: "long",
});const parts = rtf.formatToParts(1, "minute")
console.log(parts);
```

然后我们得到:

```
[
  {
    "type": "integer",
    "value": "1",
    "unit": "minute"
  },
  {
    "type": "literal",
    "value": " 分鐘後"
  }
]
```

用上面代码中的`console.log`语句。

`resolvedOptions()`方法为我们获取一个对象，该对象带有我们在构造函数中设置的选项，用于格式化字符串和设置的区域设置。我们可以在下面的代码中使用它:

```
const rtf = new Intl.RelativeTimeFormat("zh-hant", {
  localeMatcher: "best fit",
  numeric: "auto",
  style: "long",
});
console.log(rtf.resolvedOptions());
```

使用上面的代码，我们从`console.log`语句中得到以下内容:

```
{
  "locale": "zh-Hant",
  "style": "long",
  "numeric": "auto",
  "numberingSystem": "latn"
}
```

`Intl.RelativeDateFormat`构造函数也有一个`supportedLocalesOf`方法来获取格式化日期和时间所支持的地区。它将一组 BCP 47 语言环境字符串作为参数。Unicode 扩展键将与区域设置代码一起返回，即使它与日期格式无关(如果提供的话)。它使用`localeMatcher`选项来指定要使用的区域匹配算法。可能的值是`lookup`和`best fit`。查找算法搜索区域设置，直到找到符合正在比较的字符串字符集的区域设置。`best fit`查找至少比`lookup`算法更适合的语言环境。

例如，我们可以在下面的代码中使用它:

```
const locales = ['en-ca', 'id-u-co-pinyin', 'ban'];
const options = {
  localeMatcher: 'lookup'
};console.log(Intl.RelativeTimeFormat.supportedLocalesOf(locales, options));
```

然后我们得到`[“en-CA”, “id-u-co-pinyin”]`。这是因为巴厘语与印尼语非常相似，对于`lookup`算法来说是相同的。请注意，输入数组中的 Unicode 扩展会随输出一起返回，即使它与上下文无关。

JavaScript `Intl.RelativeTimeFormat`构造函数让我们能够轻松地以一种区域敏感的方式格式化相对时间。我们可以用不同的方式来设计风格，用我们想要的方式来格式化它。这让我们可以格式化相对时间字符串，而没有操作字符串带来的麻烦。构造函数有两个参数。第一个参数是区域设置字符串或此类字符串的数组。第二个是一个对象，它接受各种参数来按照我们想要的方式调整相对时间字符串。这个构造函数的实例有几个返回格式化字符串的方法，格式化字符串是子字符串的数组，还有一个方法返回我们为格式化字符串设置的选项。我们还可以检查支持静态`supportedLocalesOf`方法的地区。