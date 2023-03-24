# RxJS 过滤运算符—跳过和采用

> 原文：<https://levelup.gitconnected.com/rxjs-filtering-operators-skip-and-take-3576acda1f1f>

![](img/828f07afc424f6a0487bb81edadbd5ca.png)

[比尔·费尔斯](https://unsplash.com/@moonboyz?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Rxjs 是一个用于进行反应式编程的库。创建操作符对于从各种数据源生成供观察者订阅的数据非常有用。

在本文中，我们将看到更多的过滤操作符，包括`skipUntil`、`skipWhile`、`take`、`takeLast`、`takeUntil`和`takeWhile`操作符。

# `skipUntil`

操作符返回一个可观察对象，它跳过源可观察对象发出的项目，直到第二个可观察对象发出一个项目。

它需要一个参数，即`notifer`可观测值。当它发出一个项目时，那么源可观察对象的发出值将由`skipUntil`操作符发出。

例如，我们可以如下使用它:

```
import { interval } from "rxjs";
import { skipUntil, take } from "rxjs/operators";const numbers = interval(1000).pipe(
  take(10),
  skipUntil(interval(5000).pipe(take(1)))
);
numbers.subscribe(x => console.log(x));
```

上面的代码从`interval(1000)` Observabkem 获取发出的值，然后`pipe`将其传递给`take(10)`操作符以获取前 10 个值，然后将这些值传递给`skipUntil`操作符，该操作符传入了`interval(5000).pipe(take(1))`可观察值。

`interval(5000).pipe(take(1))`在 5 秒后发出一个值，然后触发`interval(1000)`可观察的发射值的发射。

那么我们应该看到:

```
4
5
6
7
8
9
```

# 滑雪时

`skipWhile`操作符返回一个可观察对象，它跳过源可观察对象发出的所有项目，直到`predicate`函数返回的条件变为`false`。

它需要一个参数，这是一个`predicate`函数，用该函数返回的条件测试每一项。

例如，我们可以这样写:

```
import { of } from "rxjs";
import { skipWhile } from "rxjs/operators";const numbers = of(1, 2, 3, 4, 5, 6).pipe(skipWhile(x => x < 3));
numbers.subscribe(x => console.log(x));
```

那么当这个值小于 3 时，`of(1, 2, 3, 4, 5, 6)`可观测的物品就不会被发射。这意味着`numbers`会发出 3，4，5，6。

一旦`predicate`函数中的条件被测试`false`一次，它将继续发射，不管来自源可观测值的发射值是否使条件评估为`false`。

所以如果我们有:

```
import { of } from "rxjs";
import { skipWhile } from "rxjs/operators";const numbers = of(1, 2, 3, 4, 3, 2).pipe(skipWhile(x => x < 3));
numbers.subscribe(x => console.log(x));
```

然后我们得到 3、4、5 和 2，因为一旦第一个 3 由`of(1, 2, 3, 4, 3, 2)`发出，条件就变成了`false`。

# 拿

`take`操作符返回一个可观察对象，它发出由源可观察对象发出的第一个`count`值。

它有一个参数是`count`，它表示要发出的值的最大数量。

例如，我们可以如下使用它:

```
import { of } from "rxjs";
import { take } from "rxjs/operators";const numbers = of(1, 2, 3, 4, 3, 2).pipe(take(2));
numbers.subscribe(x => console.log(x));
```

然后，由于我们将 2 传递给了`take`操作符，所以我们记录了 1 和 2。

# takeLast

`takeLast`运算符返回一个可观察对象，该可观察对象发出由源可观察对象发出的最后一个`count`值。

它有一个参数是`count`，从值序列的末尾发出的值的最大数量。

例如，我们可以如下使用它:

```
import { of } from "rxjs";
import { takeLast } from "rxjs/operators";const numbers = of(1, 2, 3, 4, 5, 6).pipe(takeLast(2));
numbers.subscribe(x => console.log(x));
```

我们应该得到 5 和 6，因为我们将 2 传递给了`takeLast`操作符，这意味着我们只想要来自发出的`of(1, 2, 3, 4, 5, 6)`可观察值的最后 2 个值。

![](img/79368d69fcc257aa00cb6ced07aea331.png)

由 [Mpho Mojapelo](https://unsplash.com/@mpho_mojapelo?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 直到

`takeUntil`操作符返回一个从源可观察对象发出值的可观察对象，直到`notifier`可观察对象发出值。

它接受一个参数，即`notifier`，其发出的值将导致返回可观察对象停止从源可观察对象发出值。

例如，我们可以如下使用它:

```
import { interval, timer } from "rxjs";
import { takeUntil } from "rxjs/operators";const source = interval(1000);
const result = source.pipe(takeUntil(timer(5000)));
result.subscribe(x => console.log(x));
```

上面的代码将发出来自`source`可观察值的值，直到`timer(5000)`发出它的第一个值。

所以我们应该得到这样的结果:

```
0
1
2
3
```

# 抓紧时间

只要满足`predicate`函数中的条件，操作符`takeWhile`就会返回一个发出源可观察值的可观察值，一旦条件不满足，操作就会完成。

它采用参数，即返回条件以检查来自源可观测值的每个值的`predicate`函数。该函数将来自源可观察对象的项作为第一个参数，将发出的值的索引(从 0 开始)作为第二个参数。

例如，我们可以如下使用它:

```
import { interval } from "rxjs";
import { takeWhile } from "rxjs/operators";const source = interval(1000);
const result = source.pipe(takeWhile(x => x <= 10));
result.subscribe(x => console.log(x));
```

那么我们应该得到:

```
0
1
2
3
4
5
6
7
8
9
10
```

由于我们在`predicate`函数中指定了`x => x <= 10`，这意味着任何小于或等于 10 的值都会从源可观测值中被发射出来。

`skipUntil`操作符返回一个可观察对象，它跳过源可观察对象发出的项目，直到第二个可观察对象发出一个项目。

`skipWhile`操作符返回一个可观察对象，它跳过源可观察对象发出的所有项目，直到传递给`predicate`函数的条件变为`false`

`take`操作符返回一个可观察对象，它发出源可观察对象发出的第一批值。

`takeLast`操作符返回一个可观察对象，它发出源可观察对象发出的最后几个值。

`takeUntil`运算符返回一个从源可观察对象发出值的可观察对象，直到传入该运算符的可观察对象发出值。

最后，`takeWhile`操作符返回一个可观察对象，只要传递给它的条件得到满足，这个可观察对象就会发出由源可观察对象发出的值。