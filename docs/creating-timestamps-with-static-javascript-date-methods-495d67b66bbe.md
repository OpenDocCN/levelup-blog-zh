# 用静态 JavaScript 日期方法创建时间戳

> 原文：<https://levelup.gitconnected.com/creating-timestamps-with-static-javascript-date-methods-495d67b66bbe>

![](img/a9758e7d95c76bca527bf4191243acd0.png)

照片由 [JC Gellidon](https://unsplash.com/@jcgellidon?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

JavaScript `Date`构造函数为我们提供了一些静态方法来创建日期对象。

在本文中，我们将研究如何在代码中使用它们来创建 UNIX 时间戳。

# `**Date.now()**`

`Date.now()`方法返回自 1970 年 1 月 1 日 00:00:00 UTC 以来经过的毫秒数，这是 UNIX 时间戳。

例如，我们可以如下使用它:

```
let t = Date.now()
```

为了防止计时攻击和指纹识别，`Date.now()`的精度可能会根据浏览器的设置进行四舍五入。

例如，Firefox 有`privacy.reduceTimerPrecision`设置，默认启用，在 Firefox 59 中默认为 20 微秒，在 60 中默认为 2 毫秒。

`privacy.resistFingerprinting`是 Firefox 的另一个设置，用于将返回的时间戳四舍五入到最接近的 100ms 或`privacy.resistFingerprinting.reduceTimerPrecision.microseconds`的值，以较高者为准。

# `**Date.parse()**`

`Date.parse()`将日期字符串作为参数，并返回自 1970 年 1 月 1 日 00:00:00 UTC 以来的毫秒数，如果该字符串不能被解析为日期或具有非法日期值，则返回`NaN`。

在 ES5 之前它是不可靠的，因为在那之前字符串解析完全依赖于实现。

例如，我们可以如下使用它:

```
let t = Date.parse('2019-01-01');
```

那么`t`就是`1546300800000`。

如果我们传入一个无效的日期字符串，它将返回`NaN`。例如，如果我们写:

```
let t = Date.parse('2019-13-01');
```

那么`t`就是`NaN`。

要返回日期加上时间的时间戳，我们可以写:

```
let t = Date.parse('2019-01-01T14:10:00');
```

那么`t`就是`1546380600000`。

为了指定日期时间是 UTC，我们可以在日期字符串的末尾添加`Z`:

```
Date.parse("2019-01-01T00:00:00.000Z")
```

我们应该坚持使用格式化为标准日期字符串的日期字符串。否则，我们可能会遇到正确解析日期字符串的问题。

![](img/c050d51dbd48c2f28a5148e309d57eb0.png)

由 [Alvan Nee](https://unsplash.com/@alvannee?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# `**Date.UTC()**`

`Date.UTC`接受类似于`Date`构造函数的参数，并返回一个 UTC，即自 1970 年 1 月 1 日 00:00:00 UTC 以来的毫秒数。

对于 ES2017 或更高版本，它以以下格式调用:

```
Date.UTC(year[, month[, day[, hour[, minute[, second[, millisecond]]]]]])
```

在 ES2016 或更早版本中，如下所示:

```
Date.UTC(year, month[, day[, hour[, minute[, second[, millisecond]]]]])
```

`year`是整年。

`month`是介于 0(1 月)和 11(12 月)之间的整数，代表月份。在 ES2017 或更高版本中是可选的。

`day`是可选的，它是 1 到 31 之间的一个整数，代表一个月中的某一天。默认为 1。

`hour`是可选的，是一个 0 到 23 之间的整数，代表小时。默认值为 0。

`minute`是可选的，它介于 0 和 59 之间，代表分钟。默认值为 0。

`second`可选，0 到 59 之间表示秒。默认值为 0。

`millisecond`是可选的，介于 0 和 999 之间，代表毫秒。默认值为 0。

例如，我们可以获取 2019 年 1 月 1 日上午 12 点的 UNIX 时间戳，如下所示:

```
let t = Date.UTC(2019, 0, 1, 0, 0, 0)
```

然后我们得到 1546300800000 作为`t`的值。

# 结论

JavaScript `Date`构造函数的静态方法都返回 UNIX 时间戳，即给定日期和 1970 年 1 月 1 日 00:00:00 UTC 之间经过的时间(以毫秒计)。

`Date.now()`返回从现在到 1970 年 1 月 1 日 00:00:00 UTC 之间经过的毫秒数。

`Date.parse()`返回给定的本地日期时间字符串与 1970 年 1 月 1 日 00:00:00 UTC 之间经过的毫秒数。

`Date.UTC()`返回给定的 UTC 日期时间字符串和 1970 年 1 月 1 日 00:00:00 UTC 之间经过的毫秒数。