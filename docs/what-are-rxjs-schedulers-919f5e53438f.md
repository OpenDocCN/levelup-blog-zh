# 什么是 RxJS 调度程序？

> 原文：<https://levelup.gitconnected.com/what-are-rxjs-schedulers-919f5e53438f>

![](img/6a418c2958cec6c3ec765cee7adb9645.png)

照片由[艾玛·马修斯数字内容制作](https://unsplash.com/@emmamatthews?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

调度程序是控制何时开始订阅以及何时传递通知的实体。它有 3 个组成部分:

*   数据结构—它知道如何根据优先级或其他标准存储和排列任务。
*   执行上下文—表示任务执行的位置和时间。它可以是立即的，也可以推迟到以后。
*   虚拟时钟——任务相对于调度器上的`now()` getter 方法指示的时间运行。

# 定义调度程序

我们可以定义一个基本的调度程序如下:

```
import { of, asyncScheduler } from "rxjs";
import { observeOn } from "rxjs/operators";of(1, 2, 3)
  .pipe(observeOn(asyncScheduler))
  .subscribe(val => console.log(val));
```

代码通过使用`asyncScheduler`将我们的同步可观察对象转化为异步可观察对象。

如果我们比较下面同步示例的输出，我们会注意到不同之处:

```
import { of, asyncScheduler } from "rxjs";
import { observeOn } from "rxjs/operators";const observable = of(1, 2, 3);console.log("before sync subscribe");
observable.subscribe({
  next(x) {
    console.log(`got sync value ${x}`);
  },
  error(err) {
    console.error(`something wrong occurred: ${err}`);
  },
  complete() {
    console.log("done");
  }
});
console.log("after sync subscribe");
```

异步示例:

```
import { of, asyncScheduler } from "rxjs";
import { observeOn } from "rxjs/operators";const observable = of(1, 2, 3);console.log("before async subscribe");
observable.pipe(observeOn(asyncScheduler)).subscribe({
  next(x) {
    console.log(`got async value ${x}`);
  },
  error(err) {
    console.error(`something wrong occurred: ${err}`);
  },
  complete() {
    console.log("done");
  }
});
console.log("after async subscribe");
```

我们看到同步示例输出:

```
before sync subscribe
got sync value 1
got sync value 2
got sync value 3
done
after sync subscribe
```

带有`asyncScheduler`的异步示例得到如下输出:

```
before async subscribe
after async subscribe
got async value 1
got async value 2
got async value 3
done
```

正如我们所见，`asyncScheduler`将可观察对象的执行推迟到同步代码运行之后。

`async`调度程序通过`setTimeout`或`setInterval`进行操作。即使延迟为零，它仍然在`setTimeout`或`setInterval`上运行。它将在下一次事件循环迭代中运行。

`asyncScheduler`有一个`schedule()`方法，它接受一个 delay 参数，这个参数指的是相对于调度程序自己的内部时钟的时间量。内部时钟与实际时钟时间没有任何关系。临时操作是由调度程序的时钟决定的，而不是真正的时钟。

这对于测试很有用，因为我们可以很容易地在任务异步运行时伪造测试时间。

# 调度程序类型

除了`asyncScheduler`，RxJS 中还有其他类型的调度程序:

*   `null` —同步递归传递通知。对于常数时间或尾递归运算很有用
*   `queueScheduler` —当前事件帧中队列上的调度。对迭代有用。
*   `asapScheduler` —微任务队列中的时间表，与用于承诺的时间表相同
*   `asyncScheduler` —用于基于时间的操作。工作安排在`setInterval`
*   `animationFrameScheduler` —安排任务在下一次浏览器内容重绘之前运行。它可以用来平滑浏览器动画

![](img/dea3729415be4d11722e308d173d28e1.png)

照片由 [Renáta-Adrienn](https://unsplash.com/@bajkorenata?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 使用调度程序

以下函数采用调度程序参数:

*   `bindCallback`
*   `bindNodeCallback`
*   `combineLatest`
*   `concat`
*   `empty`
*   `from`
*   `fromPromise`
*   `interval`
*   `merge`
*   `of`
*   `range`
*   `throw`
*   `timer`

我们已经在使用调度程序，但没有明确指定它。RxJS 将通过找到一个引入最少并发量的调度器来选择一个默认调度器并完成工作。

例如，对于定时操作，使用`async`，对于发出有限数量消息的简单操作，使用`null`或`undefined`。

一些操作符还带有调度程序参数。包括`bufferTime`、`debounceTime`、`delay`、`auditTime`、`sampleTime`、`throttleTime`、`timeInterval`、`timeout`、`timeoutWith`、`windowTime`等与时间相关的。

其他实例操作符如`cache`、`combineLatest`、`concat`、`expand`、`merge`、`publishReplay`、`startWith`也采用调度器参数。

使用调度程序，我们可以控制 Observables 何时发出数据。有不同种类的调度器，RxJS 会根据创建最少并发性的原则来自动选择。

调度程序根据自己的时钟运行任务。与现实世界的时钟无关。

许多运营商允许我们设置一个调度程序来满足我们的需求。