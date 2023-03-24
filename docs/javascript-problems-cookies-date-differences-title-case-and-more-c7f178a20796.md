# JavaScript 问题——cookie、日期差异、标题大小写等等

> 原文：<https://levelup.gitconnected.com/javascript-problems-cookies-date-differences-title-case-and-more-c7f178a20796>

![](img/971766b2034d29664f5f62710c398469.png)

照片由[莉莲·迪伯恩](https://unsplash.com/@lilianovich?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何种类的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# 获取两个日期之间的差异

为了得到差值 2 的日期，我们可以得到它们时间戳之间的差值，然后除以每天的毫秒数。

例如，我们可以写:

```
const MS_PER_DAY = 1000 * 60 * 60 * 24;
const utc1 = Date.UTC(a.getFullYear(), a.getMonth(), a.getDate());
const utc2 = Date.UTC(b.getFullYear(), b.getMonth(), b.getDate());
const diff = Math.floor((utc2 - utc1) / MS_PER_DAY);
```

我们使用`Date.UTC`将日期转换为 UTC 时间戳，方法是传入年、月和月中的日。

从世界协调时 1970 年 1 月 1 日上午 12 点开始，`utc1`和`utc2`都是以毫秒为单位。

然后我们减去 2 个时间戳，除以每天的毫秒数，得到它的下限，得到 2 个日期之间的天数差。

为了让我们的生活更轻松，我们也可以使用 moment.js 来获得差异。

例如，我们可以写:

```
const a = moment('7/11/2020','M/D/YYYY');
const b = moment('12/12/2020','M/D/YYYY');
const diffDays = b.diff(a, 'days');
```

我们用`moment`函数创建了两个日期，然后对其调用`diff`。

我们传入`'days'`来得到天数的差异。

# 将字符串转换为标题大小写

要将字符串转换为大写，我们可以搜索空白后面的任何单词，然后用大写字符替换它。

例如，我们可以写:

```
str.replace(/\w\S*/g, (txt) => {
  return `${txt[0].toUpperCase()}${txt.substr(1).toLowerCase()}`;
});
```

我们在整个字符串中搜索由`g`标志指示的空白后面带有`/\w\S*/g`的任何单词。

然后我们传入一个回调函数，将单词替换为第一个大写字符，其余的小写字符。

# 有条件地向反应组件添加属性

我们可以添加属性条件来反应组件。

为此，我们将一个布尔值传递给一个属性。

例如，我们可以写:

```
<input type="text" disabled={disabled} required={required} />
```

其中`disabled`是布尔状态，`required`也是布尔状态。

# 使用 JavaScript 从数组中移除对象

要从数组中移除一个对象，我们可以调用一些方法。

我们可以调用`shift`来删除第一个元素。

同样，我们可以调用`slice(1)`remove 来删除第一个元素。

`splice(0, 1)`也删除第一个元素。

`pop`删除最后一个元素。

`slice(0, a.length — 1)`返回移除了最后一个元素的新数组。

`arr.length = arr.length — 1`从数组中删除最后一项。

我们也可以通过使用`splice`的索引来删除该项目:

```
array.splice(x, 1);
```

其中`x`是我们想要移除的项目的索引。

此外，我们可以写:

```
array = array.slice(0, x).concat(array.slice(-x));
```

从数组中删除索引为`x`的项目。

`slice(0, x)`从索引 0 include 和`x` exclusive 中获取数组的切片。

我们调用`concat`将它与`slice(-x)`结合起来，后者获取从最后一个元素到最后一个元素的数组。

# 用 JavaScript 设置 Cookie 和获取 Cookie

我们可以用 JavaScript 通过设置 cookie 的键和值来设置 cookie。

同样，我们在它后面附加了`expires`和`path`键和值。

例如，我们可以写:

```
const date = new Date();
date.setTime(date.getTime() + (days*24*60*60*1000));
document.cookie = `name=${value};expires=${date.toUTCString()};path=/`;
```

我们将字符串中的`name`、`expires`和`path`键一起设置为`document.cookie`的值。

要解析 cookie，我们必须通过手动解析字符串来获取值。

我们可以写:

```
const getCookie = (name) => {
  const nameEq = `${name}=`;
  const ca = document.cookie.split(';');
  for (const c of ca) {
    while (c.charAt[0] === ' ') {
      c = c.substring(1, c.length);
    }
    if (c.indexOf(nameEq) === 0) {
      return c.substring(nameEq.length, c.length);
    }
  }
}
```

我们通过分割`document.cookie`字符串来搜索带有给定键的子字符串。

然后，我们遍历从`split`返回的所有条目，并搜索带有等号的给定键名的条目。

然后我们返回相应键的值。

![](img/bd2ac00a4e9ca5358506ce88c278ff6e.png)

帕斯卡尔·莫尔霍弗在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以用`Date`构造函数或库得到两个日期之间的差异。

从数组中移除对象有很多种方法。

我们必须通过用普通 JavaScript 手动解析字符串来获取和设置 cookie。