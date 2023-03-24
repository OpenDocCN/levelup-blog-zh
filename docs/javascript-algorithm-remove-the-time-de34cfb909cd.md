# JavaScript 算法:去掉时间

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-remove-the-time-de34cfb909cd>

## 我们将编写一个函数，输出以某种格式编写的时间戳。

![](img/acd8773859364c42d0433c04516b4329.png)

今天我们将编写一个名为`shortenToDate`的函数，它将接受一个字符串`longDate`作为输入。

假设你正在设计一个博客，谈论最新的小猫毛衣时尚。每篇博客文章的日期格式如下:

*星期几月几日，时间*例如，8 月 21 日星期一下午 4 点

你想让日期有一个较短的格式，这样就可以显示除了时间之外的所有事情**。**

*星期几月几日*例如，8 月 21 日星期一

您会得到一个遵循长日期格式的字符串。该函数的目标是输出较短的时间格式。

因为我们的输入是一个字符串，我们要把我们的字符串变成一个数组，并在逗号所在的地方分割字符串。

```
longDate.split(",");
```

通过在逗号处分割字符串，我们创建了一个两个元素长的数组。第一个元素包含我们想要的格式的日期，第二个元素包含时间。

```
// [day of the week month day, time]
```

因为我们希望格式不包括时间，所以我们只需要数组中的第一项。我们返回第一个数组项:

```
return longDate.split(",")[0];
```

以下是剩余的代码:

```
function shortenToDate(longDate) {
  return longDate.split(",")[0];
}
```

如果你觉得这个算法有帮助，可以看看我最近的其他 JavaScript 算法解决方案:

[](https://medium.com/@endubueze00/javascript-algorithm-name-shuffler-e0308ff9b449) [## JavaScript 算法:命名洗牌机

### 我们将编写一个函数来颠倒或交换某人全名的顺序。

medium.com](https://medium.com/@endubueze00/javascript-algorithm-name-shuffler-e0308ff9b449) [](https://medium.com/@endubueze00/javascript-algorithm-what-is-between-246e27e90dac) [## JavaScript 算法:之间是什么？

### 我们将创建一个数组，输出从 a 到 b 的所有数字(我们的两个参数)。

medium.com](https://medium.com/@endubueze00/javascript-algorithm-what-is-between-246e27e90dac) [](/javascript-algorithm-can-we-divide-it-c86842cb5657) [## JavaScript 算法:可以分吗？

### 我们要写一个函数，如果参数“数”能被 a 和 b 整除，则返回 true 或 false。

levelup.gitconnected.com](/javascript-algorithm-can-we-divide-it-c86842cb5657)