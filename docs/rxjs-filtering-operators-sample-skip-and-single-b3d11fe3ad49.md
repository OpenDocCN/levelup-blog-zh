# RxJS 过滤运算符—样本、跳过和单个

> 原文：<https://levelup.gitconnected.com/rxjs-filtering-operators-sample-skip-and-single-b3d11fe3ad49>

![](img/9ef9c5f2a4267ddc3195579dde93084e.png)

[Joshua Hoehne](https://unsplash.com/@mrthetrain?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

RxJS 是一个用于进行反应式编程的库。创建操作符对于从各种数据源生成供观察者订阅的数据非常有用。

在本文中，我们将看看一些过滤操作符，包括`sample`、`sampleTime`、`skip`、`single`和`skipLast`操作符。

# `sample`

`sample`操作符返回一个可观察对象，当`notifier`可观察对象发出时，该可观察对象发出来自源可观察对象的值。

它需要一个参数，即`notifer`可观测值。

例如，我们可以如下使用它:

```
import { interval } from "rxjs";
import { sample } from "rxjs/operators";const seconds = interval(1000);
const result = seconds.pipe(sample(interval(5000)));
result.subscribe(x => console.log(x));
```

在上面的代码中，我们有每秒发出一个数字的`seconds` Observable。然后我们将它的结果`pipe`给`sample`操作符，在这里我们设置`notifier`可观察值为`interval(5000)`，它每 5 秒发出一次。

因此，我们有了一个新的可观察对象，它每 5 秒钟从`seconds`发出一次值。

我们应该会看到从`seconds`记录的每几个值。

# 采样时间

`sampleTime`运算符以周期性间隔从可观测的源中发出最近发出的值。

它最多需要两个参数。第一个是`period`，这是等待从源可观测值发出一个值的时间段。以毫秒或可选`scheduler`的时间单位计量。

第二个参数是可选的`scheduler`，默认为`async`。

它返回一个新的可观察对象，该对象在指定的时间间隔内从源可观察对象发出值。

例如，我们可以如下使用它:

```
import { interval } from "rxjs";
import { sampleTime } from "rxjs/operators";const seconds = interval(1000);
const result = seconds.pipe(sampleTime(5000));
result.subscribe(x => console.log(x));
```

上面的代码将从`seconds`观察器发出的值和`pipe`发送到`sampleTime`操作器，操作器将每 5 秒从`seconds`发出值。

结果将是我们每隔 5 秒钟从`result`可观测值中获取值，从`seconds`可观测值中获取值。

我们应该会看到一些来自`console.log`的数字。

# 单一的

`single`返回一个可观察对象，它从源可观察对象中发出与`predicate`函数返回的给定条件相匹配的单个项目。

如果源可观察对象发出一个以上这样的项目或没有项目，那么我们分别得到 IllegalArgumentException 或 NoSuchElementException。

如果源可观察对象发出项目，但没有一个符合`predicate`函数中的条件，则发出`undefined`。

它接受一个参数，即`predicate`函数，该函数返回匹配从源可观察对象发出的项目的条件。

例如，如果我们有:

```
import { of } from "rxjs";
import { single } from "rxjs/operators";const numbers = of(1).pipe(single());
numbers.subscribe(x => console.log(x));
```

然后我们记录了 1。

如果我们有:

```
import { of } from "rxjs";
import { single } from "rxjs/operators";const numbers = of(1, 2).pipe(single());
numbers.subscribe(x => console.log(x));
```

然后我们记录了错误“序列包含多个元素”。

如果我们有:

```
import { of } from "rxjs";
import { single } from "rxjs/operators";const numbers = of(1, 2).pipe(single(x => x % 2 === 0));
numbers.subscribe(x => console.log(x));
```

然后我们记录了 2 个，因为 2 个匹配条件`x => x % 2 === 0`。

# 跳跃

`skip`操作符返回一个可观察对象，它跳过源可观察对象发出的第一个`count`项，并发出其余的项。

它采用必需的`count`参数，这是要跳过的来自源可观察项的数量。

例如，如果我们有:

```
import { of } from "rxjs";
import { skip } from "rxjs/operators";const numbers = of(1, 2, 3, 4, 5).pipe(skip(2));
numbers.subscribe(x => console.log(x));
```

然后我们得到:

```
3
4
5
```

记录是因为我们指定要跳过从`of(1, 2, 3, 4, 5)`发出的前 2 个值。

![](img/4439bbc68199b6bcfa4ef78022064388.png)

舒米洛夫·卢德米拉在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# skipLast

`skipLast`运算符返回一个可观察对象，它跳过了从源可观察对象发出的最后一个`count`值。

它采用必需的`count`参数，即从源可观察对象的末尾跳过的项目数。

例如，我们可以写:

```
import { of } from "rxjs";
import { skipLast } from "rxjs/operators";const numbers = of(1, 2, 3, 4, 5).pipe(skipLast(2));
numbers.subscribe(x => console.log(x));
```

然后我们得到:

```
1
2
3
```

自从我们指定要跳过最后两个发出的值后，就记录了日志。

`sample`和`sampleTime`运算符分别在`notifier`可观察对象发出时或在指定的时间间隔返回一个从源可观察对象发出值的可观察对象。

`single`返回一个可观察对象，该可观察对象从源可观察对象中发出与给定条件匹配的单个项目。

`skip`操作符返回一个可观察对象，该可观察对象在开始从可观察对象发出值时跳过项目的数量。

最后，`skipLast`操作符返回一个可观察对象，它跳过了源可观察对象发出的最后几个值。