# 用 JavaScript 处理日期

> 原文：<https://levelup.gitconnected.com/handling-dates-in-javascript-3587ace328c9>

![](img/1125fb7c9c602502e288b7ed5b73fcfd.png)

[Icons8 团队](https://unsplash.com/@icons8?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在许多 JavaScript 程序中，处理日期和时间也很重要。这就是为什么 JavaScript 有一个内置的`Date`对象来处理日期操作。Dates 对象包含一个数字，表示自 UTC(Linux 纪元时间)1970 年 1 月以来的毫秒数。

我们可以用 Date 对象做很多事情来操作日期。然而，它有许多怪癖和缺失的功能。因此，在许多情况下，第三方库处理日期比使用内置的日期对象更好。Moment.js 是其中比较流行的一个，所以当我们需要在 JavaScript 中操作日期时，它自然是第一选择。

# 内置日期对象

为了在 JavaScript 中处理日期，我们必须创建一个`new Date`对象。date 对象的构造函数接受零个或多个参数。如果没有传入参数，那么创建的 Date 对象将具有您本地时区的当前日期和时间，从实例化时开始。

它可以接受的参数包括日期字符串、另一个日期对象或(年、月索引、日、小时、分钟、秒和毫秒)的组合。我们可以向日期构造函数传递如下日期字符串:

```
new Date('2019-01-01');
```

如果我们记录结果，我们会得到`‘Mon Dec 31 2018 16:00:00 GMT-0800 (Pacific Standard Time)’`，因为日期存储在 UTC 中，浏览器运行在太平洋时区的计算机上，这比 2019 年 1 月 1 日的 UTC 晚 8 个小时，即太平洋标准时间。

正如我们所见，将日期字符串传递给`Date`构造函数令人困惑。不同的浏览器解析日期字符串的方式不同。同样，`Date.parse`方法也不应该用于解析日期字符串。我们可以看到，像我们传入的只包含日期的字符串被视为 UTC，而不是本地时间。

如果我们想把一个日期对象传递给构造函数，我们可以写:

```
new Date(new Date());
```

当我们记录结果时，我们会得到类似“太阳 2019 年 10 月 20 日 10:57:58 GMT-0700(太平洋夏令时)”的信息。这是以当地时间显示的。

我们可以将年、月索引、日、小时、分钟、秒和毫秒传递给构造函数。只需要年份和月份。所以我们可以这样写:

```
new Date(2019,0,1,0,0,0,0)
```

如果我们记录它，将返回“2019 年 1 月 1 日星期二 00:00:00 GMT-0800(太平洋标准时间)”。注意，我们在构造函数的第二个参数中有 0。第二个参数是月份索引，0 表示一月，1 表示二月，依此类推。所以 12 月是 11 日。其余的论点是我们所期望的。第一个是年，第三个是日，第四个是小时，第五个是分钟，第六个是秒，最后一个是毫秒。

我们可以用一元运算符`+`将 Date 对象转换成 UNIX 时间戳，即自 1970 年 1 月 1 日 UTC 以来的毫秒数，因此如果我们写:

```
+new Date(2019,0,1,0,0,0,0)
```

当我们记录输出时，我们得到了`1546329600000`,因为它将日期强制为一个数字。

另一种获取时间戳的方法是使用`.getTime()`方法。

要改变日期对象的各个组成部分，如设置小时或天，或者获取日期的组成部分，如一周中的某一天或一个月中的某一天，在日期对象中有一些方法可以做到这一点。

# 日期对象中的方法

Date 对象中有一些方法可以获取和设置部分日期，并将时间转换为 UTC，或者设置时区偏移量。一些重要的日期对象方法包括:

## Date.now()

以忽略闰秒的 UNIX 时间戳的形式返回当前时间。这个方法是静态的。

## 约会。世界协调时()

以忽略闰秒的 UNIX 时间戳的形式返回当前时间。这个方法是静态的。它接受与 Date 构造函数的最长参数列表相同的参数，即年、月索引、日、小时、分钟、秒和毫秒，但只有年和月是必需的。我们可以在下面的代码中使用它:

```
Date.UTC(2019,0,1,1,0,0,0)
```

我们返回类似于`1546304400000`的东西。

# Getter 函数

## Date.getDate()

根据本地时间返回一个月中的某一天。例如，如果我们有:

```
const date = new Date(2019,0,1,0,0,0,0);
console.log(date.getDate());
```

我们将得到 1，因为我们在构造函数的参数中将它指定为一个月中的某一天。

## Date.getDay()

根据本地时间返回指定日期的星期几(0–6)，其中 0 表示星期日，6 表示星期六，1 到 5 表示两天之间的天数。例如，我们可以写:

```
const date = new Date(2019,0,1,0,0,0,0);
console.log(date.getDay());
```

然后我们得到 2，也就是星期二。

## Date.getFullYear()

根据本地时间返回指定日期的 4 位数年份。例如，我们可以写:

```
const date = new Date(2019,0,1,0,0,0,0);
console.log(date.getFullYear());
```

然后我们得到 2019 年，也就是`date`的 4 位数年份。

## Date.getHours()

根据本地时间返回指定日期的小时(0–23)。例如，我们可以写:

```
const date = new Date(2019,0,1,0,0,0,0);
console.log(date.getHours());
```

然后我们得到 0，这是我们在构造函数的小时参数中指定的。

## 约会。getMilliseconds()

根据本地时间返回 0 到 999 之间的指定日期的毫秒数。例如，我们可以写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
console.log(date.getMilliseconds());
```

这将获得在`date`对象中指定的毫秒数，它应该像我们在构造函数中指定的那样返回 0。

## Date.getMinutes()

以本地时间返回指定日期的分钟(0–59)。例如，我们可以写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
console.log(date.getMinutes());
```

然后我们得到 0 分钟，就像我们在构造函数中指定的那样。

## Date.getMonth()

根据本地时间返回指定日期的月份(从 0 表示 1 月到 11 表示 12 月)。例如，我们可以写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
console.log(date.getMonth());
```

然后我们得到一月的月份 0，就像我们在构造函数中指定的那样。

## Date.getSeconds()

以本地时间返回指定日期中的秒(0–59)。例如，我们可以写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
console.log(date.getSeconds());
```

然后我们得到 0 秒，就像我们在构造函数中指定的那样。

## Date.getTime()

返回指定日期的 UNIX 时间戳，即自 1970 年 1 月 1 日 00:00:00 UTC 以来的毫秒数。对于以前的时间，它将是负的。例如，我们可以写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
console.log(date.getTime());
```

然后我们得到从`getTime()`函数返回的 1546329600000。对此的一种简化方法是使用一元`+`运算符，如下面的代码所示:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
console.log(+date);
```

如果我们运行上面的代码，我们会得到相同的结果。

## Date.getTimezoneOffset()

返回主机设备上设置的当前位置的时区偏移量(以分钟为单位)。例如，如果我们写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
console.log(date.getTimezoneOffset());
```

如果您的主机设备设置为太平洋时区，我们将得到 480，因为 2019 年 1 月 1 日，太平洋时区比 UTC 晚 8 小时，所以我们得到时区偏移是 480 分钟，也就是 8 小时。

## Date.getUTCDate()

根据 UTC 返回指定日期中一个月中的第几天，从 1 到 31。例如，如果我们写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
console.log(date.getUTCDate());
```

因为我们将 2019 年 1 月 1 日指定为日期，所以我们得到 1。

## Date.getUTCDay()

根据 UTC 中的指定日期，返回 0 到 6 之间的星期几。例如，如果我们写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
console.log(date.getUTCDay());
```

那么我们得到 2，因为 2019 年 1 月 1 日是星期二。

## Date.getUTCFullYear()

根据 UTC 返回指定日期中的 4 位数年份。例如，如果我们写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
console.log(date.getUTCFullYear());
```

我们得到 2019，因为这是我们在日期构造函数中指定的。

## Date.getUTCHours()

根据 UTC 返回指定日期中从 0 到 23 的小时数。例如，如果我们写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
console.log(date.getUTCHours());
```

如果区域设置设置为太平洋时区，我们会得到 8，因为我们指定了当地时间的小时数为 0，这与 2019 年 1 月 1 日的 UTC 时间 8 相同。

## Date.getUTCMilliseconds()

根据 UTC 返回 0 到 999 之间的指定日期的毫秒数。例如，我们可以写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
console.log(date.getUTCMilliseconds());
```

这将获得在`date`对象中指定的毫秒数，它应该像我们在构造函数中指定的那样返回 0。

## Date.getUTCMonth()

根据 UTC 返回指定时间内从 0 表示 1 月到 11 表示 12 月的月份。例如，如果我们写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
console.log(date.getUTCMonth());
```

这将获得在`date`对象中指定的月份，该对象应该像我们在构造函数中指定的那样返回 0。

## Date.getUTCSeconds()

以 UTC 返回指定日期中的秒(0–59)。例如，我们可以写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
console.log(date.getUTCSeconds());
```

然后我们得到 0 秒，就像我们在构造函数中指定的那样。

## Date.getYear()

根据当地时区，返回指定日期中的年份，通常为 2 到 3 位数。

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
console.log(date.getUTCSeconds());
```

那么我们得到 2019 年的 119。

如果我们有:

```
const date = new Date(1900, 0, 1, 0, 0, 0, 0);
console.log(date.getYear());
```

1900 年我们得到 0。如果我们有:

```
const date = new Date(2000, 0, 1, 0, 0, 0, 0);
console.log(date.getYear());
```

那么 2000 年我们得到 100。所以根据`getYear()`方法，1900 年是 0 年。使用`getFullYear()`方法来从 Date 对象中获取正确的年份要好得多。

# Setter 函数

我们上面提到的每个 getter 函数都有相应的 setter 函数。我们为日期对象提供了以下 setter 函数:

## Date.setDate()

根据本地时间设置一个月中的某一天。例如，如果我们有:

```
const date = new Date(2019,0,1,0,0,0,0);
date.setDate(2)
console.log(date);
```

当主机设备设置为太平洋时区时，我们将得到“Wed Jan 02 2019 00:00:00 GMT-0800(太平洋标准时间)”，因为我们在`setDate`函数中指定了 2。

## Date.setFullYear()

根据本地时间设置指定日期的 4 位数年份。例如，我们可以写:

```
const date = new Date(2019,0,1,0,0,0,0);
date.setFullYear(2020)
console.log(date.getFullYear());
```

然后我们得到 2020 年，也就是`date`的 4 位数年份。

## Date.setHours()

根据本地时间设置指定日期的小时(0–23)。例如，我们可以写:

```
const date = new Date(2019,0,1,0,0,0,0);
date.setHours(3)
console.log(date.getHours());
```

然后，当我们调用`getHours`时，我们得到 3，这是我们在`setHours`函数的小时参数中指定的。

## 约会。setMilliseconds()

根据本地时间将指定日期的毫秒数设置为 0 到 999。例如，我们可以写:

```
const date = new Date(2019,0,1,0,0,0,0);
date.setMilliseconds(3)
console.log(date.getMilliseconds());
```

这将设置在`date`对象中指定的毫秒数，当我们调用`getMilliseconds`函数时，它将返回 3，就像我们在`setMilliseconds`函数中指定的那样。

## Date.setMinutes()

以本地时间设置指定日期的分钟(0–59)。例如，我们可以写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
date.setMinutes(20)
console.log(date.getMinutes());
```

然后我们从`getMinutes()`返回 20 分钟，就像我们在`setMinutes`函数中指定的那样。

## Date.setMonth()

根据本地时间设置指定日期的月份(0 表示 1 月，11 表示 12 月)。例如，我们可以写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
date.setMonth(3)
console.log(date.getMonth());
```

然后，在我们通过`setMonth()`设置后，我们从`getMonth()`返回第 3 个月

## Date.setSeconds()

以本地时间设置指定日期中的秒(0–59)。例如，我们可以写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
date.setSeconds(10);
console.log(date.getSeconds());
```

然后我们从`getSeconds()`得到 10 秒，就像我们在`setSeconds`函数中指定的那样。

## Date.setTime()

为指定日期设置 UNIX 时间戳，该日期是自 1970 年 1 月 1 日 00:00:00 UTC 以来的毫秒数。对于以前的时间，它将是负的。例如，我们可以写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
date.setTime(100)
console.log(date.getTime());
```

然后我们从`getTime()`函数中得到 100，因为我们用`setTime`将它设置为 100。

## Date.setUTCDate()

根据 UTC 设置指定日期中 1 到 31 之间的某一天。例如，如果我们写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
date.setUTCDate(2)
console.log(date.getUTCDate(2));
```

我们得到 2，因为我们在`setUTCDate`中指定了 2

## Date.setUTCFullYear()

根据 UTC 设置指定日期中的 4 位数年份。例如，如果我们写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
date.setUTCFullYear(2020)
console.log(date.getUTCFullYear());
```

我们用`getUTCFullYear()`得到 2020，因为这是我们在日期`setUTCFullYear()`函数中指定的。

## Date.setUTCHours()

根据 UTC 设置指定日期中从 0 到 23 的小时。例如，如果我们写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
date.setUTCHours(2)
console.log(date.getUTCHours());
```

我们用`getUTCHours()`得到 2，因为我们在调用`setUTCHours()`时指定了它。

## Date.setUTCMilliseconds()

根据 UTC 将指定日期的毫秒数设置为 0 到 999。例如，我们可以写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
date.setUTCMilliseconds(10)
console.log(date.getUTCMilliseconds());
```

这用`getUTCMilliseconds()`得到毫秒，它应该像我们在`setUTCMilliseconds()`中指定的那样返回 10。

## Date.setUTCMonth()

根据 UTC 返回指定时间内从 0 表示 1 月到 11 表示 12 月的月份。例如，如果我们写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
date.setUTCMonth(3);
console.log(date.getUTCMonth());
```

这将获得在`date`对象中指定的月份，该对象将从`getUTCMonth()`返回 3，就像我们在`setUTCMonth()`调用中指定的那样。

## Date.setUTCSeconds()

以 UTC 设置指定日期中的秒(0–59)。例如，我们可以写:

```
const date = new Date(2019, 0, 1, 0, 0, 0, 0);
date.setUTCSeconds(10);
console.log(date.getUTCSeconds());
```

那么当我们调用`getUTCSeconds()`时，我们得到 10 秒，因为我们在调用`setUTCSeconds()`时指定了 10 秒。

这个`setYear()`法套住了叶。使用`setFullYear()`方法来从日期对象中获取正确的年份要好得多。

Date 对象有一些限制。例如，它不支持时区。当我们需要日期中的时区数据时，这就产生了问题。JavaScript 日期仅支持本地时区、UTC 或相对于 UTC 的时区偏移量。操作 date 对象时，只允许这些时区之间的部分转换。例如，当我们将日期对象转换为日期字符串时，我们只能在本地时间和 UTC 之间进行选择。JavaScript Date 对象在内部以 UTC 格式存储日期。

![](img/a1bd8830aa297ebb56f2129977658d77.png)

德里克·汤姆森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# Moment.js

由于我们上面讨论的限制，在 JavaScript 中操作时间和日期是很痛苦的。即使有了`Date`对象可用的函数，还是有很多机会出现 bug 和错误。此外，没有格式化时间和日期的功能，这是一个大问题。为了解决这个问题，我们使用 moment.js。这是处理时间的最佳库。

本机 JavaScript 日期对象在将 YYYY-MM-DD 日期解析为 UTC 时间而不是本地时间时存在时区问题。这对没有意识到这个问题的开发人员来说是个问题。参见[https://stack overflow . com/questions/29174810/JavaScript-date-time zone-issue](https://stackoverflow.com/questions/29174810/javascript-date-timezone-issue)

不同浏览器对部分日期的支持也有所不同。(参见[https://stack overflow . com/questions/11253351/JavaScript-date-object-issue-in-safari-and-ie](https://stackoverflow.com/questions/11253351/javascript-date-object-issue-in-safari-and-ie))

用内置的日期函数添加和减去时间戳也很困难。如果不编写大量代码和进行大量检查，就没有办法做到这一点。还有，没办法对比 2 次。

如果不编写自己的代码来使用第三方库，格式化日期也是不可用的。

Moment.js 通过提供内置函数来完成所有这些常见操作，从而解决了所有这些问题。它提供了解析和格式化日期的函数。

在`moment`构造函数中，您可以传入一个日期字符串，然后会创建一个`moment`对象。例如，您可以传入:

```
moment('2019-08-04')
```

你会得到一个`moment`，你可以与其他`moment`对象进行比较，并增加或减少不同的时间跨度。

如果您没有向`moment`构造函数传递任何东西，那么您将获得当前的日期和时间。

这也需要第二个论证。如果您想确保一个日期被解析为 YYYY-MM-DD 日期，那么编写`moment(‘2019–08–04’, 'YYYY-MM-DD')`。如果您不知道日期或时间的格式，那么您可以传入一组可能的格式，Moment 将选择正确的格式:

```
moment('2019–08–04', ['YYYY-MM-DD', 'DD-MM-YYYY']);
```

创建 Moment 对象后，您可以做许多事情，如格式化日期:

```
const a = moment('2019–08–04', 'YYYY-MM-DD').format('MMMM Do YYYY, h:mm:ss a');
console.log(a);// August 4th 2019, 12:00:00 amconst b = moment('2019–08–04', 'YYYY-MM-DD').format('dddd');
console.log(b);// Sundayconst c = moment('2019–08–04', 'YYYY-MM-DD').format("MMM Do YY");
console.log(c);// Aug 4th 19const d = moment('2019–08–04', 'YYYY-MM-DD').format('YYYY [escaped] YYYY');    
console.log(d);// 2019 escaped 2019const e = moment('2019–08–04', 'YYYY-MM-DD').format();
console.log(e);// 2019-08-04T00:00:00-07:00
```

从上面的例子中，我们看到我们可以用任何我们想要的方式格式化日期。

我们还可以通过写下以下内容来判断一个日期相对于另一个日期的时间跨度:

```
const augDate = moment('2019–08–04', 'YYYY-MM-DD');
const sepDate = moment('2019–09–04', 'YYYY-MM-DD');
console.log(augDate.from(sepDate)); // a month ago
```

我们还可以增加或减少时刻日期:

```
const augDate = moment('2019–08–04', 'YYYY-MM-DD');
const sepDate = moment('2019–09–04', 'YYYY-MM-DD');
console.log(augDate.add(10, 'days').calendar()); // 08/14/2019
console.log(augDate.subtract(10, 'days').calendar()); // 07/25/2019
```

比较两个日期很容易:

```
moment('2010-01-01').isSame('2010-01-01', 'month'); // true
moment('2010-01-01').isSame('2010-05-01', 'day');   // false, different month
moment('2008-01-01').isSame('2011-01-01', 'month'); // false, different year
```

您还可以检查某个日期是否实行夏令时:

```
const augDate = moment('2019–08–04', 'YYYY-MM-DD');
const decDate = moment('2019–12–04', 'YYYY-MM-DD');
console.log(augDate.isDST()) // true
console.log(decDate.isDST()) // false
```

您可以随时通过调用 Moment 对象上的`toDate()`函数转换回 JavaScript date。

正如我们所见，JavaScript 的 Date 对象在处理日期时功能有限。它也不能很好地解析日期，也不能很容易地格式化日期。这就是为什么我们需要使用像 Moment.js 这样的库来填补空白，这样我们就可以创建更容易操纵日期的无错误代码。