# 同步与异步数据源

> 原文：<https://levelup.gitconnected.com/synchronous-vs-asynchronous-data-sources-f78e40f998c1>

## 在 Javascript 应用程序中

![](img/9500bb59c4d5596e73206a9241e679a1.png)

格伦·卡斯滕斯-彼得斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

一个常见的误解是将同步数据源视为异步，尤其是在测试反应流时。在这篇文章中，我将尝试探索同步和异步数据源之间的差异，以及`RxJS`中相应的例子。

## 同步数据源

每个反应库都提供了从不同类型的数据创建流的可能性。对于这个例子，我们将考虑一个简单的数组[2，12，35]，它将被包装成一个可观察的:

```
const sourceA = from([2, 12, 35]);
sourceA.subscribe(console.log);**Output:** 2 
12
35
```

这个例子表明，当可观察对象被订阅时，数组的值被一个接一个地发出，直到数组用完为止，给出上面概述的输出。包装的数据也可以是字符串、映射或集合。*在将数据项处理到订阅回调中时，每个后续项都必须等待当前项完成处理*。

除此之外，代码还可以包装单个值，而不是数组。下面的示例说明了如何从一个角色创建一个可观察对象，并在用户连接后发出该值:

```
const sourceB = from('a');
sourceB.subscribe(console.log);**Output**:
a
```

## **异步数据源**

在异步数据源的情况下，情况有所不同。在之前的一篇文章中，`Promises`对`RxJS`中的`Observables`，我解释了一个异步操作，比如一个`HTTP`请求将被包装在一个承诺中，它将成功解析并调用一个成功回调或抛出一个错误(拒绝)——就是这样，它最终发出一个**单个**值。下面的示例演示如何创建在 2 秒钟后解析的异步事件。只有在承诺解析后，才会调用订户的回调。

```
**Output:** ‘wait for success promise’
<<AFTER 2 seconds>>
‘success’
```

另一方面，处理不可预测的`DOM`事件，比如鼠标移动，会阻止我们处理承诺，因为它们被限制在一个值内。`RxJS`提供了从`DOM`元素上连接的事件中创建可观察对象的可能性。下面的例子说明了一个 **mousemove** 事件发射器到一个可观察事件的转换。请在文档正文周围移动鼠标，并检查控制台中记录的异步事件。

```
let eventSource = fromEvent(document, 'mousemove', {passive: true});eventSource.subscribe(val => console.log(val as MouseEvent));
```

最后，我想快速讨论一下我在测试异步数据源时目睹的一个常见错误。

假设我们有一个异步数据源，比如一个`DOM`事件发射器，像上面的例子一样被包装在一个可观察对象中，我们想要测试它。我见过一些开发人员用同步数据流测试它的用例，首先从一组测试值中创建一个`Observable`并订阅它。

在我看来，这是错误的方法，尽管它可以提供容易验证的结果，但由于异步事件是不可预测的，所以它们可能并不总是正确的。

进行这种测试的正确方法是使用`Marble Testing`。我将在以后的文章中详细讨论大理石测试。

当心

参考资料:

[](https://www.manning.com/books/rxjs-in-action) [## RxJS 在行动

### 1.1.1.阻塞代码 1.1.2 的问题。具有回调函数的非阻塞代码。理解时间和空间…

www.manning.com](https://www.manning.com/books/rxjs-in-action)