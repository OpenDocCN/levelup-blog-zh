# 如何用 JavaScript 完成常见的日期任务——明天、昨天和排序

> 原文：<https://levelup.gitconnected.com/how-to-do-common-date-tasks-with-javascript-tomorrow-yesterday-and-sorting-b29686c9eb46>

![](img/9de74d5ef84abaf4fd7df6d91e2232ce.png)

照片由[布鲁克·卡吉尔](https://unsplash.com/@brookecagle?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 JavaScript 中，`Date`构造函数为我们提供了一些日期和时间操作功能。但是，很多操作不能只通过一次方法调用来完成。

在本文中，我们将了解如何从 JavaScript 日期中获取月份名称，获取昨天的日期，明天的日期，按日期对数组进行排序。

# 从 JavaScript 日期中获取月份名称

我们可以用`Date`实例的`toLocaleString`方法从`Date`对象中获取月份名称。

例如，我们可以编写以下代码来获取月份名称，如下所示:

```
const date = new Date(2020, 0, 1);
const month = date.toLocaleString('default', {
  month: 'short'
})
```

在上面的代码中，我们创建了`Date`实例。然后我们用`'default'`作为参数在上面调用`toLocaleString`。此参数是区域设置。`'default'`表示您的本地操作系统设置的语言环境。

我们传入的第二个参数是一个带有想要传入的选项的对象。在这种情况下，因为我们想要获得月份名称，所以我们添加了值为`'short'`的`month`属性来获得月份的缩写名称。

所以`month`的值就是`'Jan’`。

我们也可以通过将`'short'`改为`'long'`获得一个长月份名称，如下所示:

```
const date = new Date(2020, 0, 1);
const month = date.toLocaleString('default', {
  month: 'long'
})
```

然后我们得到`month`的值是`'January'`。

# 获取昨天的日期

我们可以通过获取今天的日期来获取昨天的日期，然后在那个日期对象上调用`getDate`，并将其减去 1，然后将整个表达式传递给`setDate`。

为此，我们可以编写以下代码:

```
const today = new Date();
const yesterday = new Date(today);
yesterday.setDate(yesterday.getDate() - 1)
```

在上面的代码中，我们在第一行创建了`today`的日期。然后我们用`Date`构造函数复制`today`，为`yesterday`创建另一个对象。

然后我们通过传入`yesterday.getDate() — 1`来调用`yesterday`上的`setDate`,用 1 天减去`yesterday`,得到昨天的日期。

然后，当我们记录两个日期对象的值时，如下所示:

```
console.log(today.toDateString())
console.log(yesterday.toDateString())
```

我们得到`yesterday`比`today`晚一天。

# 获取明天的日期

类似于得到昨天的日期，我们可以通过做类似的事情得到明天的日期。

我们可以这样写:

```
const today = new Date();
const tomorrow = new Date(today);
tomorrow.setDate(tomorrow.getDate() + 1)
```

得到昨天的日期和明天的日期的唯一区别是我们用`tomorrow.getDate() + 1`而不是`yesterday.getDate() — 1`来加一天而不是减 1。

那么当我们检查如下值时:

```
console.log(today.toDateString())
console.log(tomorrow.toDateString())
```

我们得到`tomorrow` 比`today`早 1 天。

# 在 JavaScript 中按日期值对数组排序

我们可以使用数组实例的`sort`方法按照日期对数组进行排序。`sort`方法接受一个回调，该回调接受两个参数和要比较的数组项。

我们可以通过将两个`Date`对象转换成 UNIX 时间戳来比较它们，时间戳是整数，然后我们可以减去它们来比较它们。

如果差值为负，则第一个条目晚于第二个条目。否则，第二个条目晚于第一个条目。

例如，如果我们有以下数组:

```
const tasks = [{
    name: 'eat',
    date: new Date(2020, 0, 1)
  },
  {
    name: 'drink',
    date: new Date(2020, 0, 2)
  },
  {
    name: 'sleep',
    date: new Date(2019, 11, 31)
  },
]
```

然后我们可以通过写下升序日期按数组排序:

```
const sortedTasks = tasks.sort((a, b) => +a.date - +b.date);
```

在上面的代码中，我们有一个回调函数，它用一元运算符`+`将两个条目的`date`属性转换成 UNIX 时间戳。然后我们减去两个时间戳，得到哪个先出现。

如果差值为负，则第一个条目在第二个条目之前。否则，第二个在第一个之前。

一旦我们调用了`sort`，我们就会得到一个新的排序后的数组。那么`sortedTasks`应该是:

```
[
  {
    "name": "sleep",
    "date": "2019-12-31T08:00:00.000Z"
  },
  {
    "name": "eat",
    "date": "2020-01-01T08:00:00.000Z"
  },
  {
    "name": "drink",
    "date": "2020-01-02T08:00:00.000Z"
  }
]
```

正如我们所见，日期是按升序排列的。如果我们想按日期降序排序，我们可以写:

```
const sortedTasks = tasks.sort((a, b) => +b.date - +a.date);
```

我们只是将`b`和`a`翻转过来，那么`sortedTasks`应该是:

```
[
  {
    "name": "drink",
    "date": "2020-01-02T08:00:00.000Z"
  },
  {
    "name": "eat",
    "date": "2020-01-01T08:00:00.000Z"
  },
  {
    "name": "sleep",
    "date": "2019-12-31T08:00:00.000Z"
  }
]
```

![](img/7fd8b98b6e37b9e0d6ba11034b7f0615.png)

照片由[苏珊·莫尔](https://unsplash.com/@intheview?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以使用`toLocaleString`方法获得日期的月份名称。

如果想得到昨天的日期，可以调用`getDate`然后减 1。同样，如果我们想得到明天的日期，我们可以调用`getDate`然后加 1。

最后，为了按日期排序，我们可以将日期转换成时间戳，然后在数组实例的`sort`方法的回调中减去时间戳。