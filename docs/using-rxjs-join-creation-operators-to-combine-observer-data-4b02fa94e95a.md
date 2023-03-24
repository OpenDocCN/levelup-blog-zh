# 使用 RxJS 连接创建运算符合并观察点数据

> 原文：<https://levelup.gitconnected.com/using-rxjs-join-creation-operators-to-combine-observer-data-4b02fa94e95a>

![](img/de731558e105865a3d24852c6eec8a69.png)

照片由✍拍摄🏻| Iphone 6 摄影 on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

RxJS 是一个反应式编程的库。创建操作符对于从各种数据源生成供观察者订阅的数据非常有用。

在本文中，我们将研究一些连接创建操作符，将来自多个可观察对象的数据组合成一个可观察对象。我们将看看`combineLatest`、`concat`和`forkJoin`操作符。

# 组合测试

我们可以使用`combineLatest`将多个观察值合并为一个，其值是根据每个输入观察值的最新值计算出来的。

它接受两个或更多的可观察值作为自变量，或者一个可观察值数组作为自变量。它返回一个发出值的可观察对象，这些值是传入的所有可观察对象的值的数组。

`combineLatest`还采用了一个可选的`project`函数，该函数采用通常由结果可观察对象发出的所有值的参数，然后我们可以返回我们想要的给定函数中的值。

`combineLatest`按顺序订阅每个 Observable ke，每当一个 Observable 发出时，将发出的数据收集到每个 Observable 的最新值的数组中。然后由返回的可观察对象发出值数组。

为了确保输出数组总是有相同的长度，`combineLastest`在开始输出结果之前，等待所有的输入观测值至少发出一次。如果一些可观察对象在其他对象之前发出值，那么这些值将会丢失。

如果一些 Observable 没有在完成时发出，那么返回的 Observable 将完成而不发出任何东西，因为那个 Observable 没有发出任何值。

如果至少有一个可观察对象被传入`combineLatest`并且它们都发出了一些东西，那么当所有组合流完成时，返回的可观察对象也将完成。在这种情况下，该值将始终是先前完成的观察值的最后一个发出的值。

例如，我们可以如下使用它:

```
import { combineLatest, of } from "rxjs";const observable1 = of(1, 2, 3);
const observable2 = of(4, 5, 6);
const combined = combineLatest(observable1, observable2);
combined.subscribe(value => console.log(value));
```

然后我们得到:

```
[3, 4]
[3, 5]
[3, 6]
```

因为`observable1`在`observable2`之前发出了它的所有值。

我们还可以使用可选的第二个参数来进行一些计算:

```
import { combineLatest, of } from "rxjs";
import { map } from "rxjs/operators";const observable1 = of(1, 2, 3);
const observable2 = of(4, 5, 6);
const combined = combineLatest(observable1, observable2).pipe(
  map(([a, b]) => a + b)
);
combined.subscribe(value => console.log(value));
```

在上面的代码中，我们得到了这些值的总和。然后我们得到:

```
7
8
9
```

这些是我们之前所有条目的总和。

# 串联

我们可以使用`concat`操作符来获取多个可观察值，并返回一个新的可观察值，这个新的可观察值顺序地从传入的每个可观察值中发出值。

它的工作方式是一次订阅一个，并将结果合并到可观察的输出中。我们可以传入一组观察值，或者直接将它们作为参数。传入一个空数组将导致一个立即完成的可观察对象。

`concat`不会以任何方式影响可观测量。当一个 Observable 完成时，它将订阅下一个 Observable 并发出它的值。这一过程将一直重复，直到操作员看不到任何东西。

`merge`算子会同时输出可观测量的值。

如果一些输入的可观察值永远不会完成，`concat`也永远不会完成，它们后面的可观察值永远不会被订阅。如果某个可观察对象完成时没有发出任何值，那么它对`concat`来说将是不可见的。

如果链中的任何可观察对象发出错误，那么该错误将立即出错。在错误永远不会被订阅的可观察值之后被订阅的可观察值。

我们可以在同一个可观察的订阅中反复传递同一个。

例如，我们可以如下使用它:

```
import { concat, of } from "rxjs";const observable1 = of(1, 2, 3);
const observable2 = of(4, 5, 6);
const concatted = concat(observable1, observable2);
concatted.subscribe(value => console.log(value));
```

然后我们得到:

```
1
2
3
4
5
6
```

正如我们所料。

![](img/6529a9657a927afb3e3c6384f5858c9d.png)

Dilyara Garifullina 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 叉连接

`forkJoin`接受一个 Observables 数组，并发出一个与传递的数组顺序完全相同的值数组或一个与传递的字典形状相同的值字典。

返回的可观察对象将发出每个可观察对象发出的最后一个值。例如，我们可以写:

```
import { forkJoin, of } from "rxjs";const observable1 = of(1, 2, 3);
const observable2 = of(4, 5, 6);
const joined = forkJoin(observable1, observable2);
joined.subscribe(value => console.log(value));
```

然后我们得到`[3, 6]`。

我们还可以传入一个具有可观察属性的对象:

```
import { forkJoin, of } from "rxjs";const observable1 = of(1, 2, 3);
const observable2 = of(4, 5, 6);
const joined = forkJoin({ observable1, observable2 });
joined.subscribe(value => console.log(value));
```

然后我们得到:

```
{observable1: 3, observable2: 6}
```

# 结论

`combineLatest`、`concat`和`forkJoin`操作符对于组合来自多个观测值的发射数据非常有用。

使用`combineLatest`，我们可以组合来自多个可观察对象的发射数据，并获得由我们传入的每个可观察对象发射的最新值组成的值数组。

`concat`操作符订阅我们顺序传入的每个可观察对象，并返回一个从每个可观察对象顺序发出值的可观察对象。如果一个错误发生在任何一个可观察对象上，那么返回的可观察对象就会发出一个错误。

最后，`forkJoin`操作符返回一个可观察对象，它从每个可观察对象中获取最新的值，并根据您传入的是可观察对象的字典还是可观察对象的数组，将值作为对象或数组发出。