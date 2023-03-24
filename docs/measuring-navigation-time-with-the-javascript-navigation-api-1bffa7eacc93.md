# 使用 JavaScript 导航 API 测量导航时间

> 原文：<https://levelup.gitconnected.com/measuring-navigation-time-with-the-javascript-navigation-api-1bffa7eacc93>

![](img/52d07fbb31bd9a0cfa09ba4f5ebbecc2.png)

照片由[凯拉·奥托](https://unsplash.com/@kaylahotto?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

为了测量导航事件，比如页面加载和转到不同的页面，我们可以使用导航计时 API。

在本文中，我们将看看如何在各种场景中使用它。

# 导航定时 API

我们可以使用导航计时 API 来收集客户端的性能数据，然后将这些数据传输到服务器。API 还让我们测量以前难以获得的数据，如卸载前一页所需的时间，查找域所需的时间，运行窗口的`load`处理程序所需的总时间等。

所有返回的时间戳都以毫秒为单位。所有属性都是只读的。

测量导航性能的主界面是`PerformanceNavigationTiming`界面。

测量页面加载时间的一个简单示例如下:

```
const perfEntries = performance.getEntriesByType("navigation");
const [p] = perfEntries;
const pageLoadTime = p.loadEventEnd - p.loadEventStart;
console.log(pageLoadTime)
```

上面的代码测量了`loadEventStart`事件和`loadEventEnd`事件被触发之间的时间。

我们还可以使用它来测量文档的 DOM 加载需要多长时间，如下所示:

```
const perfEntries = performance.getEntriesByType("navigation");
const [p] = perfEntries;
const domLoadTime = p.domContentLoadedEventEnd - p.domContentLoadedEventStart;
console.log(domLoadTime)
```

在上面的代码中，我们使用了`domContentLoadedEventEnd`和`domContentLoadedEventStart`属性来分别获取 DOM 完成加载和 DOM 开始加载这两个属性的时间。

我们还可以使用`domComplete`来获取 DOM 何时完成加载，以及页面何时与性能条目的`interactive`属性进行交互。

我们可以这样写:

```
const perfEntries = performance.getEntriesByType("navigation");
const [p] = perfEntries;
console.log(p.domComplete);
```

同样，我们可以做一些类似于测量卸载时间的事情:

```
const perfEntries = performance.getEntriesByType("navigation");
const [p] = perfEntries;
const domLoadTime = p.unloadEventEnd - p.unloadEventStart;
console.log(domLoadTime)
```

# 其他属性

我们可以使用`redirectCount`只读属性在当前浏览上下文中获得自上次非重定向导航以来的重定向次数。

为了使用它，我们可以编写以下代码:

```
const perfEntries = performance.getEntriesByType("navigation");
const [p] = perfEntries;
console.log(p.redirectCount);
```

属性将为我们获取导航类型。该属性的可能值为`'navigate'`、`'reload'`、`'back_forward'`或`'prerender'`。

我们可以得到`type`如下:

```
const perfEntries = performance.getEntriesByType("navigation");
const [p] = perfEntries;
console.log(p.type);
```

![](img/968efea1eb82e2ac955e53f1f0d3419d.png)

由[克里斯托弗·艾利森](https://unsplash.com/@kristopher_allison?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 方法

我们可以用`toJSON`方法序列化一个`PerformanceNavigationTiming`对象。

要使用它，我们可以写:

```
const perfEntries = performance.getEntriesByType("navigation");
const [p] = perfEntries;
console.log(p.toJSON());
```

然后我们从`console.log`那里得到类似下面的东西:

```
{
  "name": "[https://fiddle.jshell.net/_display/](https://fiddle.jshell.net/_display/)",
  "entryType": "navigation",
  "startTime": 0,
  "duration": 0,
  "initiatorType": "navigation",
  "nextHopProtocol": "h2",
  "workerStart": 0,
  "redirectStart": 0,
  "redirectEnd": 0,
  "fetchStart": 0.31999999191612005,
  "domainLookupStart": 0.31999999191612005,
  "domainLookupEnd": 0.31999999191612005,
  "connectStart": 0.31999999191612005,
  "connectEnd": 0.31999999191612005,
  "secureConnectionStart": 0.31999999191612005,
  "requestStart": 2.195000008214265,
  "responseStart": 89.26000000792556,
  "responseEnd": 90.5849999981001,
  "transferSize": 1435,
  "encodedBodySize": 693,
  "decodedBodySize": 1356,
  "serverTiming": [],
  "unloadEventStart": 95.75999999651685,
  "unloadEventEnd": 95.75999999651685,
  "domInteractive": 116.96000001393259,
  "domContentLoadedEventStart": 116.97000000276603,
  "domContentLoadedEventEnd": 117.20500001683831,
  "domComplete": 118.64500000956468,
  "loadEventStart": 118.68000001413748,
  "loadEventEnd": 0,
  "type": "navigate",
  "redirectCount": 0
}
```

# 结论

`PerformanceNavigationTiming`界面对于测量页面导航和加载时间非常方便。

这是很难通过其他方式获得的信息。

该接口为我们提供了各种只读属性，这些属性为我们提供了各种页面加载和导航相关事件发生的时间。所有时间都以毫秒为单位。