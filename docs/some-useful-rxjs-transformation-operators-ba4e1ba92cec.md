# 一些有用的 RxJS 变换运算符

> 原文：<https://levelup.gitconnected.com/some-useful-rxjs-transformation-operators-ba4e1ba92cec>

![](img/90b73707d473938029deb973b6a4df91.png)

沃纳·范·格雷宁在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

RxJS 是一个反应式编程的库。创建操作符对于从各种数据源生成供观察者订阅的数据非常有用。

在本文中，我们将查看一些 RxJS 转换操作符，如`bufferTime`、`bufferToggle`、`bufferWhen`和`concatMap`操作符。

# 缓冲时间

`bufferTime`算子将原始观测数据的发射数据缓冲一段特定的时间。

它需要一个参数`bufferTimeSpan`，即填充每个缓冲区数组的时间。时间跨度的单位是毫秒。

一旦达到指定的时间，就会发出缓冲区数据并重置缓冲区。

它还需要一个`bufferCreationInterval`参数，该参数指定缓冲区数据建立的时间跨度。操作员每隔`bufferCreationInterval`毫秒打开一次缓冲器，每隔`bufferTimeSpan`毫秒发射和复位一次。

该操作符的另一个可选参数是`maxBufferSize`，它是一个指定缓冲项的最大大小的数字。

例如，我们可以如下使用它:

```
import { interval } from "rxjs";
import { bufferTime } from "rxjs/operators";const clicks = interval(2000);
const buffered = clicks.pipe(bufferTime(1000));
buffered.subscribe(x => console.log(x));
```

我们应该看到在发出的数组中每 2 秒钟发出一个新的数字，而其余发出的数组是空的。

`bufferCreationInterval`参数可以如下使用:

```
import { interval } from "rxjs";
import { bufferTime } from "rxjs/operators";const clicks = interval(2000);
const buffered = clicks.pipe(bufferTime(1000, 1000));
buffered.subscribe(x => console.log(x));
```

在上面的代码中，缓冲的数据每秒发出一次，缓冲区每秒创建一次。

# bufferToggle

`bufferToggle`缓冲源可观测值，从`openings`发射开始，到`closingSelector`输出发射结束。

原始可观测值发出的值被缓冲，直到`closingSelector`告诉我们停止从原始可观测值发出值。

例如，如果我们有如下按钮:

```
<button>Click Me</button>
```

我们可以缓冲按钮上的鼠标点击事件，并发出根据`closingSelector`函数规范缓冲的`MouseEvent`对象，如下所示:

```
import { fromEvent, interval, EMPTY } from "rxjs";
import { bufferToggle } from "rxjs/operators";const clicks = fromEvent(document.querySelector("button"), "click");
const openings = interval(1000);
const buffered = clicks.pipe(
  bufferToggle(openings, i => (i % 2 ? interval(1500) : EMPTY))
);
buffered.subscribe(x => console.log(x));
```

上例中的`closingSelector`是:

```
(i % 2 ? interval(1500) : EMPTY)
```

当代码开始发射时，也就是当我们点击按钮时，它将从`openings`可观察对象发射数据，每隔 1.5 秒。发射的数据被缓冲到一个数组中，然后当`i % 2`为`false`时缓冲结束，返回`interval(1500)`可观测值，表示关闭。当`EMPTY`发射时，它会继续缓冲。

![](img/ec8a6d1a5e2e0c3ff27b9b671114b068.png)

照片由[亚历山德罗·德桑蒂斯](https://unsplash.com/@alessandrodesantis?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 布弗亨

`bufferWhen`运算符缓冲来自源可观测数据的数据，直到`closingSelector`函数关闭缓冲区。

它需要一个参数，即`closingSelector`函数来指定何时关闭缓冲区。

例如，我们可以像在下面的代码中一样使用它:

```
import { fromEvent, interval } from "rxjs";
import { bufferWhen } from "rxjs/operators";const clicks = fromEvent(document.querySelector("button"), "click");
const buffered = clicks.pipe(
  bufferWhen(() => interval(1000 + Math.random() * 4000))
);
buffered.subscribe(x => console.log(x));
```

我们所做的是获取按钮点击，然后从缓冲的点击中发出`MouseEvent`的`MouseEvent`对象数组。

一旦我们这样做了，`closingSelector`指定我们每隔`1000 + Math.random() * 4000`毫秒发出缓冲值，清空缓冲区并再次缓冲点击事件。

# 串联图

`concatMap`运算符获取原始可观察对象的每个源值，并等待原始可观察对象的一个值发出，然后再发出下一个发出的值。

它需要两个参数。第一个是`project`函数，该函数返回一个新的可观察对象，我们希望将该操作应用于从原始可观察对象发出的值。第二个参数是`resultSelector`。这是一个可选参数，它是一个函数，用来选择我们希望在返回的可观察值中发出的值。

例如，我们可以如下使用它:

```
import { fromEvent, interval, of } from "rxjs";
import { concatMap, take } from "rxjs/operators";const clicks = fromEvent(document, "click");
const result = clicks.pipe(concatMap(ev => interval(1000).pipe(take(5))));
result.subscribe(x => console.log(x));
```

上面的代码将接收点击事件，然后在 1 秒后发出 0，然后在另一秒后发出 1，直到达到 4。

我们每次点击它都会做同样的事情。

`bufferTime`操作符将原始观测值的发射数据缓冲一段时间，然后缓冲的数据将作为一个数组发射。

`bufferToggle`缓冲源可观测值，从`openings`的发射开始，到`closingSelector`功能的输出发射时结束。

`bufferWhen`操作符缓冲来自源可观察对象的数据，直到`closingSelector`函数发出它的数据。

最后，`concatMap`操作符获取原始可观测值的每个源值，并等待原始可观测值的一个值发出，然后再发出下一个发出的值。