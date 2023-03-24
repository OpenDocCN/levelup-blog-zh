# 在 Javascript 中查找两个日期之间的天数

> 原文：<https://levelup.gitconnected.com/find-difference-between-two-dates-in-javascript-117be3e73caf>

![](img/61e928f2d58cc7e66d24078b9ca4b29d.png)

Jonathan J. Castellon 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

学会用 javascript 找出两个日期的区别。

让我们举一个我们将要做的例子。我们有一个接受两个日期作为输入的函数，它返回天数。

如果我们通过 2019 年 5 月 22 日和 2019 年 5 月 28 日，那么它返回 7。

解决问题的方法

```
1\. Convert the date object in to millisecond2\. Subtract the second date from first date.3\. Divide that by number of milliseconds per day (86400000) calculated by (1000 * 60 * 60 * 24)1000 --> 1 second60   --> 60 minutes60  -->  60 seconds24  --> 24 hours
```

**程序**

```
const MILLISECONDS_PER_DAY = 1000 * 60 * 60 * 24;function dayDifference(date1, date2) {     var timeDiff = Math.abs(date2.getTime() - date1.getTime()); var diffDays = Math.ceil(timeDiff / MILLISECONDS_PER_DAY); return diffDays;
}var date1 = new Date(); //May 28 2019 var date2 = new Date(); date2.setDate(22);dayDifference(date, date1); **// 7**
```

**注:** `dateObj.getTime()` →将日期转换为毫秒。

跟随 [Javascript 吉普🚙](https://medium.com/u/f9ffc26e7e69?source=post_page-----117be3e73caf--------------------------------)更多有趣的帖子。

[](https://gitconnected.com/learn/javascript) [## 学习 JavaScript -最佳 JavaScript 教程(2019) | gitconnected

### JavaScript 是世界上最流行的编程语言之一——它随处可见。JavaScript 是一种…

gitconnected.com](https://gitconnected.com/learn/javascript)