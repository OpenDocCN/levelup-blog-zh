# RxJS 运算符的基本用法

> 原文：<https://levelup.gitconnected.com/basic-usage-of-rxjs-operators-b5fdd9feea1d>

![](img/c27b326b7a02bf9b4379dd8f0bb106a2.png)

[科学高清照片](https://unsplash.com/@scienceinhd?utm_source=medium&utm_medium=referral)上 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

有了运营商，我们可以做更多的 RxJS 可观。它们对于用异步代码进行复杂的操作很有用。

运算符是函数。有两种类型的运算符:

*   可管道化操作符——可以使用 Observables 中可用的`pipe`方法将它们管道化到 Observables。它们返回一个新的可观察对象，因此多个可管道化的操作符可以链接在一起
*   创建操作符——这些是创建新的可观察对象的独立函数。比如`of(1,2,3)`会发出 1，2，3。

# 使用运算符

我们可以从 RxJS 导入操作符来操纵我们的可观察结果。

例如，我们可以使用`map`函数将观察值映射到另一个观察值:

```
import { of } from "rxjs";
import { map } from "rxjs/operators";map(x => x * 2)(of(1, 2, 3)).subscribe(val => console.log(val));
```

我们用`of`函数创建了一个发出 1、2 和 3 的可观察对象，然后用回调函数调用`map`，让我们映射这些值，回调函数返回一个函数，我们可以传入我们创建的可观察对象，并返回一个新的可观察对象。

```
map(x => x * 2)(of(1, 2, 3))
```

返回一个我们可以订阅的新的可观察值。然后我们在`subscribe`的回调函数中得到值 2、4 和 6。

# 平静的

我们可以使用 Observable 的`pipe`方法将多个操作组合成一个。

它以多个操作作为参数，这比嵌套它们要干净得多。

例如，我们可以用它来重写上面的例子:

```
import { of, pipe } from "rxjs";
import { map } from "rxjs/operators";of(1, 2, 3)
  .pipe(map(x => x * 2))
  .subscribe(val => console.log(val));
```

我们得到相同的结果，但它要干净得多。

此外，我们可以向`pipe`方法传递多个操作:

```
of(1, 2, 3)
  .pipe(
    map(x => x * 2),
    map(x => x * 3)
  )
  .subscribe(val => console.log(val));
```

然后，我们首先将可观察对象发出的每个值乘以 2，然后将返回值再次乘以 3。结果，我们在`console.log`中得到 6、12 和 18。

# 创建运算符

创建操作符是从零开始或通过将其他可观察对象连接在一起来创建可观察对象的函数。

例如，我们让`interval`函数在我们指定的时间间隔内发出一个从 0 开始的值:

```
import { interval } from "rxjs";interval(5000).subscribe(val => console.log(val));
```

上面的代码将每 5 秒输出一个从 0 开始的整数。

![](img/a1c41d836fa13d1676a8cbe368d1dee1.png)

[科学高清照片](https://unsplash.com/@scienceinhd?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上

# 高阶可观测量

高阶可观测量是可观测量中的可观测量。我们可以用不同的观测值做一些事情。

RxJS 有一个`concatAll()`操作符，它订阅每个内部可观察对象，并复制所有发出的值，直到外部可观察对象完成。

例如，我们可以如下使用它:

```
import { of, pipe } from "rxjs";
import { concatAll, map } from "rxjs/operators";of(1, 2, 3)
  .pipe(
    map(outerVal => {
      console.log(`outerVal ${outerVal}`);
      return of(4, 5, 6);
    }),
    concatAll()
  )
  .subscribe(innerVal => console.log(`innerVal ${innerVal}`));
```

我们应该得到输出:

```
outerVal 1
innerVal 4
innerVal 5
innerVal 6
outerVal 2
innerVal 4
innerVal 5
innerVal 6
outerVal 3
innerVal 4
innerVal 5
innerVal 6
```

正如我们所见，我们首先从`of(1,2,3)`可观测值中获得第一个值，然后从第二个值中获得所有值。然后我们从`of(1,2,3)`得到第二个值，然后从第二个得到所有的值，依此类推。

其他操作员功能包括:

*   `mergeAll()` —订阅到达的每个内部可观察值，并在到达时发出每个值。
*   `switchAll()` —在第一个内部可观察值到达时订阅它，然后在它到达时发出每个值。它取消订阅前一个，然后订阅新的。
*   `exhaust()` —在第一个内部可观察对象到达时订阅它，然后在它到达时发出每个值，在它完成时丢弃所有新到达的可观察对象，并等待下一个内部可观察对象。

它们都给出了与`concatAll()`相同的结果，但区别就在下面。

我们可以通过组合多个观察值或运算符来创建新的观察值。

例如:

```
of(1, 2, 3)
  .pipe(
    map(x => x * 2),
    map(x => x * 3)
  )
```

是一个可观察值，将可观察值`of(1, 2, 3)`的每个值乘以 6。

对于 RxJS，我们可以使用内置的运算符来创建和操作可观察值。此外，我们可以创造新的可观测量，并以不同的方式将它们组合在一起。

可观测量也可以嵌套，所有嵌套可观测量的值都可以用`concatAll()`、`mergeAll()`、`switchAll()`或`exhaust()`得到。