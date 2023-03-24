# 更有用的 RxJS 转换运算符

> 原文：<https://levelup.gitconnected.com/more-useful-rxjs-transformation-operators-1ab617f28814>

![](img/d8d00d9eef449ece6f5979978b335133.png)

照片由 [Ricky Kharawala](https://unsplash.com/@sweetmangostudios?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

RxJS 是一个反应式编程的库。创建操作符对于从各种数据源生成供观察者订阅的数据非常有用。

在本文中，我们将查看一些 RxJS 转换操作符，如`concatMapTo`、`expand`、`exhaust`和`exhaustMap`操作符。

# concatMapTo

`concatMapTo`操作符获取可观察对象的源值，并将每个值映射到同一个内部可观察对象。

它最多需要两个参数。第一个是`innerObservable`，它是将每个值从原始可观察值映射到的可观察值。

第二个参数是一个`resultSelector`函数，让我们选择结果。

不管源值如何，每个原始可观察值的发射值都被映射到给定的`innerObservable`。然后将这些结果的可观察值合并成一个可观察值。这是返回的可观测值。

发出的每个新的`innerObservable`与其他的值连接在一起。

如果源值没完没了地到达，并且比相应的内部观察值完成的速度快，那么就会导致内存问题，因为会有大量的数据聚集在一起等待订阅。

例如，我们可以如下使用它:

```
import { fromEvent, interval } from "rxjs";
import { take, concatMapTo } from "rxjs/operators";const clicks = fromEvent(document, "click");
const result = clicks.pipe(concatMapTo(interval(1000).pipe(take(5))));
result.subscribe(x => console.log(x));
```

上面的代码将从文档中获取 click 事件，然后在 1 秒钟后发出 0，再等一秒钟后发出 4。

# 废气

`exhaust`算子将一个较高层次的可观测值，即发射可观测值的可观测值，转化为发射内部可观测值的一阶可观测值，而之前的内部可观测值尚未完成。

结果将是来自所有可观察值的值被展平并由一个可观察值发出，该可观察值是该运算符返回的值。

例如，我们可以写:

```
import { interval, of } from "rxjs";
import { exhaust, map, take } from "rxjs/operators";const observable = of(1, 2, 3);
const higherOrder = observable.pipe(map(ev => interval(1000).pipe(take(5))));
const result = higherOrder.pipe(exhaust());
result.subscribe(x => console.log(x));
```

在上面的代码中，我们有一个发出 1、2 和 3 的`observable`可观察对象。使用进入`interval(1000).pipe(take(5)`的`map`操作符将`observable`发出的值映射到内部可观察值。

由于没有完成的内部观察值被`exhaust`操作符丢弃，我们将得到`interval(1000).pipe(take(5)`只被发出一次，因为只有它的第一个实例被启动。然后我们得到每秒发射一个数，从 0 开始，到 4 停止。

# 耗尽图

像`exhaust`操作符一样，`exhaustMap`操作符也采用从原始可观察值映射而来的第一个内部可观察值。不同之处在于，它采用了一个函数，让我们将原始可观测值的发射值映射到另一个可观测值，但只采用该操作符的第一个发射可观测值。

例如，如果我们有以下代码:

```
import { interval, of } from "rxjs";
import { take, exhaustMap } from "rxjs/operators";const observable = of(1, 2, 3);
const result = observable.pipe(exhaustMap(ev => interval(1000).pipe(take(5))));
result.subscribe(x => console.log(x));
```

我们得到与`exhaust`相同的结果，但我们不必将`observable`的发射值`pipe`和`map`转换成新的可观测值。相反，我们用`exhaustMap`操作符将所有东西组合在一起。

![](img/7c96c43650e60e96343a8b8591e13fab.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 发展

`expand`操作符递归地将每个源值投影到一个可观察值，然后将其合并到输出可观察值中。

它有 3 个参数，即`project`函数，应用于源可观察对象或输出可观察对象发出的项目，并返回一个新的可观察对象。

第二个参数是`concurrency`，这是一个可选参数，表示同时订阅的输入观察值的最大数量。默认为`Number.POSITIVE_INFINITY`。

最后一个参数是可选的`scheduler`对象，这是一个调度器，我们可以用它来为值的发出计时。

该运算符的一个简单用法如下:

```
import { of } from "rxjs";
import { expand, delay, take } from "rxjs/operators";const powersOfTwo = of(1, 2, 3).pipe(
  expand(x => of(x).pipe(delay(1000))),
  take(10)
);
powersOfTwo.subscribe(x => console.log(x));
```

在上面的代码中，我们有`of(1, 2, 3)`个可观察对象，它们有从`pipe` d 到`expand(x => of(x).pipe(delay(1000)))`可观察对象的发射值。然后前 10 个值取自`expand`返回的可观测值，后者取`of(1, 2, 3)`可观测值的发射值，重复发射，直到我们发射了 10 个值，每组在等待 1 秒后发射。

这是因为我们指定了`of(x)`，它获取`of(1, 2, 3)`值，然后不做任何修改就发出它们。`pipe(delay(1000))`将每组数值 1、2 和 3 的发射延迟一秒钟。

这应该会产生以下输出:

```
1
2
3
1
2
3
1
2
3
1
```

# 结论

`concatMapTo`运算符获取可观察值的源值，并将每个值映射到相同的内部可观察值。

`exhaust`算子转换一个发出可观测值的可观测值，然后发出第一个内部可观测值的值，并丢弃没有发出的值。

`exhaustMap`操作符也获取第一个内部可观察值，然后将它们映射到内部可观察值中，然后发出值。

最后，`expand`操作符递归地将每个源值投影到一个可观察值上，然后将其合并到输出可观察值中并发出。