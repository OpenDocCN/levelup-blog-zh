# 更多 RxJS 变换运算符-窗口

> 原文：<https://levelup.gitconnected.com/more-rxjs-transformation-operators-window-4399bf333a41>

![](img/2357894a13e47c914959dd584654ef0d.png)

anthony renovato 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

RxJS 是一个反应式编程的库。创建操作符对于从各种数据源生成供观察者订阅的数据非常有用。

在本文中，我们将看看一些窗口操作符，包括`windowCount`、`windowTime`、`windowToggle`和`windowWhen`操作符。

# 窗口计数

`windowCount`操作符将源可观察值分支为嵌套可观察值，每个可观察值最多发出`windowSize`个事件。

它最多需要两个参数。第一个参数是`windowSize`，它是每个窗口发出的值的最大数量。

第二个参数是可选的。是`startWindowEvery`号，默认为 0。这是开始一个新窗口的间隔。间隔是由可观测的源发射的项目的数量来测量的。

例如，我们可以如下使用它:

```
import { interval } from "rxjs";
import { windowCount, mergeAll, take, map, skip } from "rxjs/operators";const nums = interval(1000).pipe(take(1000));
const result = nums.pipe(
  windowCount(3, 3),
  map(win => win.pipe(skip(1))),
  mergeAll()
);
result.subscribe(x => console.log(x));
```

上面的代码有`nums` Observable，它每秒发出一个数字，最大为 1000。

这是`windowCount`操作符的`pipe` d，它一次发出 3 个值，并在从`nums`发出 3 个值后开始一个新窗口。

然后是`map` ped 到`win.pipe(skip(1))`可观察值，每发出 2 个值就跳过 1 个值。

最后，我们通过管道将这些值传递给`mergeAll`以将所有的可观察值合并成一个。

那么我们应该看到每三个数字中没有一个被发射。

# 窗口时间

`windowTime`操作符返回一个可观察对象，该对象在`windowTimeSpan`设置的窗口周期内发出项目窗口。

它最多接受两个参数，即`windowTimeSpan`，第二个是可选参数，即`scheduler`对象。

下面是一个例子:

```
import { interval } from "rxjs";
import { windowTime, mergeAll, take, map } from "rxjs/operators";const nums = interval(1000);
const result = nums.pipe(
  windowTime(1000, 5000),
  map(win => win.pipe(take(2))),
  mergeAll()
);
result.subscribe(x => console.log(x));
```

上面的代码有`nums` Observable，它每秒发出从 0 开始的值。然后发出的值被`pipe` d 给`windowTime`操作符，它每 5 秒启动一个 1 秒长的窗口。然后我们从每个窗口取 2 个值

这将导致每个窗口中有 3 个值被跳过，因为`nums`每分钟都会发出值，但是我们`take`每个窗口只有 2 个值。

# 窗口切换

`windowToggle`将源可观察值分支为嵌套可观察值，从`openings`发射开始，到`closingSelector`发射结束。

它最多需要两个参数。第一个是`openings`，这是一个可观察到的启动新窗口的通知。

第二个是`closingSelector`，它接受由`openings`发出的值，并返回一个发出`next`或`complete`信号的可观察值，该信号将关闭窗口。

我们可以如下使用它:

```
import { interval, EMPTY } from "rxjs";
import { windowToggle, mergeAll } from "rxjs/operators";const interval$ = interval(2000);
const openings = interval(2000);
const result = interval$.pipe(
  windowToggle(openings, i => (i % 3 === 0 ? interval(500) : EMPTY)),
  mergeAll()
);
result.subscribe(x => console.log(x));
```

我们观察到`interval$`每 2 秒钟发出一个数字。然后我们对`openings`有相同的可观察值。`interval$`发出的值是`pipe` d 到`windowToggle`操作符，该操作符将`openings`可观察值作为第一个参数，每 2 秒发出一次。所以我们每 2 秒钟打开一个新窗口。

然后我们有了第二个功能:

```
i => (i % 3 === 0 ? interval(500) : EMPTY)
```

当输入的值不能被 3 整除时关闭窗口。这意味着我们记录了从`interval$`发出的每 3 个值。

![](img/fff3abad668cd4b572406390a56908a3.png)

照片由[克里夫·约翰逊](https://unsplash.com/@cliff_77?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 窗口时间

`windowWhen`操作符使用`closingSelector`函数将源可观察对象分支出来，返回一个关闭窗口的可观察对象。

它采用`closingSelector`函数，该函数采用由`openings`发出的值，并返回一个发出`next`或`complete`信号的可观察值，该信号将关闭窗口。

例如，我们可以如下使用它:

```
import { interval } from "rxjs";
import { mergeAll, take, windowWhen, map } from "rxjs/operators";const interval$ = interval(2000);
const result = interval$.pipe(
  windowWhen(() => interval(Math.random() * 4000)),
  map(win => win.pipe(take(2))),
  mergeAll()
);
result.subscribe(x => console.log(x));
```

在上面的代码中，我们有每 2 秒钟发出一个数字的`interval$`。然后发出的值是`pipe` d 到`windowWhen`操作符，它有`closingSelector` ve:

```
() => interval(Math.random() * 4000)
```

这意味着窗口将关闭并重新打开`Math.random() * 4000`毫秒。

我们应该意识到有些数字比其他数字发射得快。

`windowCount`操作符将源可观察值分支为嵌套可观察值，每个值最多发出`windowSize`个事件。`windowSize`是每个窗口的大小。

`windowTime`运算符返回一个可观测值，该可观测值在`windowTimeSpan`设置的窗口期内发出项目窗口。`windowTimeSpan`设置窗口打开的时间。

`windowToggle`将源可观察值分支为嵌套可观察值，从`openings`发射开始，到`closingSelector`发射结束。`openings`和`closingSelector`功能是可观察值，分别控制每个可观察值的窗口打开和关闭。

`windowWhen`操作符使用`closingSelector`函数分支出源可观察对象，返回一个关闭窗口的可观察对象。