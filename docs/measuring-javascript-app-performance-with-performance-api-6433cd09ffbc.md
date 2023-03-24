# 使用性能 API 测量 JavaScript 应用程序的性能

> 原文：<https://levelup.gitconnected.com/measuring-javascript-app-performance-with-performance-api-6433cd09ffbc>

![](img/f9e5e168f15e5b3d6eed630bdd52d400.png)

照片由 [Kyle Wong](https://unsplash.com/@kylewongs?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

有了 JavaScript Performance API，我们有了一种简单的方法来衡量前端 JavaScript 应用的性能。

在这篇文章中，我们将看看如何使用它来衡量我们的应用程序的性能。

# 表演

我们可以用几种方法来衡量一个应用的性能。Performance API 提供了精确且一致的时间定义。API 会给我们一个高精度的时间戳来标记一段代码开始运行和结束的时间。

时间戳以毫秒为单位，应该精确到 5 微秒。如果存在硬件或软件限制，使我们的浏览器无法提供更高精度的值，浏览器可以将值表示为精确到毫秒的时间。

我们可以在下面的例子中使用它:

```
const startTime = performance.now();
for (let i = 0; i <= 10000; i++) {
  console.log(i);
}
const endTime = performance.now();
console.log(endTime - startTime)
```

在上面的代码中，我们使用了`performance`对象来标记循环开始运行和结束运行的时间。

然后，我们通过用`startTime`减去`endTime`来记录时间，得到循环运行所用的时间，单位为毫秒。

# 序列化`Performance`对象

通过`toJSON`方法对`performance`对象进行序列化。

我们可以如下使用它:

```
const js = window.performance.toJSON();
console.log(JSON.stringify(js));
```

然后我们会得到这样的结果:

```
{"timeOrigin":1579540433373.9158,"timing":{"navigationStart":1579540433373,"unloadEventStart":1579540433688,"unloadEventEnd":1579540433688,"redirectStart":0,"redirectEnd":0,"fetchStart":1579540433374,"domainLookupStart":1579540433376,"domainLookupEnd":1579540433423,"connectStart":1579540433423,"connectEnd":1579540433586,"secureConnectionStart":1579540433504,"requestStart":1579540433586,"responseStart":1579540433678,"responseEnd":1579540433679,"domLoading":1579540433691,"domInteractive":1579540433715,"domContentLoadedEventStart":1579540433715,"domContentLoadedEventEnd":1579540433716,"domComplete":1579540433716,"loadEventStart":1579540433716,"loadEventEnd":0},"navigation":{"type":0,"redirectCount":0}}
```

记录在案。

# 测量多个动作

我们可以使用`mark`方法来标记我们的动作，使用`measure`方法通过传入名字来测量动作之间的时间。

例如，我们可以用如下标记来测量时间:

```
performance.mark('beginLoop');
for (let i = 0; i < 10000; i++) {
  console.log(i);
}
performance.mark('endLoop');
performance.measure('measureLoop', 'beginLoop', 'endLoop');
console.log(performance.getEntriesByName('measureLoop'));
```

在上面的代码中，我们在循环开始之前和结束之后调用了`mark`方法。

然后我们用我们创建的名字调用`measure`方法，以获得稍后的时差和两个标记，这样我们就可以从它们那里获得时间并获得时差。

然后我们调用`performance.getEntriesByName(‘measureLoop’)`用返回对象的`duration`属性获得计算的持续时间。

`‘measureLoop’`是我们为了通过名字得到时差而编造的名字，`‘beginLoop'`和`'endLoop'`是我们的时间标记。

我们可以用`getEntriesByType`方法得到用`mark`方法标记的条目。它接受一个字符串作为类型。为此，我们可以写:

```
performance.mark('beginLoop');
for (let i = 0; i < 10000; i++) {
  console.log(i);
}
performance.mark('endLoop');
performance.measure('measureLoop', 'beginLoop', 'endLoop');
console.log(performance.getEntriesByType("mark"))
```

那么`console.log`应该会给我们带来以下内容:

```
[
  {
    "name": "beginLoop",
    "entryType": "mark",
    "startTime": 133.55500000761822,
    "duration": 0
  },
  {
    "name": "endLoop",
    "entryType": "mark",
    "startTime": 1106.3149999827147,
    "duration": 0
  }
]
```

还有一个`getEntriesByName`方法，它分别将名称和类型作为第一个和第二个参数。

例如，我们可以写:

```
performance.mark('beginLoop');
for (let i = 0; i < 10000; i++) {
  console.log(i);
}
performance.mark('endLoop');
performance.measure('measureLoop', 'beginLoop', 'endLoop');
console.log(performance.getEntriesByName('beginLoop', "mark"));
```

然后我们得到:

```
[
  {
    "name": "beginLoop",
    "entryType": "mark",
    "startTime": 137.6299999828916,
    "duration": 0
  }
]
```

来自`console.log`。

我们也可以通过传入一个具有`name`和`entryType`属性的对象来使用`getEntries`，如下所示:

```
performance.mark('beginLoop');
for (let i = 0; i < 10000; i++) {
  console.log(i);
}
performance.mark('endLoop');
performance.measure('measureLoop', 'beginLoop', 'endLoop');
console.log(performance.getEntries({
  name: "beginLoop",
  entryType: "mark"
}));
```

然后我们会得到这样的结果:

```
[
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
    "fetchStart": 0.2849999873433262,
    "domainLookupStart": 0.2849999873433262,
    "domainLookupEnd": 0.2849999873433262,
    "connectStart": 0.2849999873433262,
    "connectEnd": 0.2849999873433262,
    "secureConnectionStart": 0.2849999873433262,
    "requestStart": 2.3250000085681677,
    "responseStart": 86.29499998642132,
    "responseEnd": 94.03999999631196,
    "transferSize": 1486,
    "encodedBodySize": 752,
    "decodedBodySize": 1480,
    "serverTiming": [],
    "unloadEventStart": 101.23999998904765,
    "unloadEventEnd": 101.23999998904765,
    "domInteractive": 126.96500000311062,
    "domContentLoadedEventStart": 126.9800000009127,
    "domContentLoadedEventEnd": 127.21500001498498,
    "domComplete": 128.21500000427477,
    "loadEventStart": 128.2249999931082,
    "loadEventEnd": 0,
    "type": "navigate",
    "redirectCount": 0
  },
  {
    "name": "[https://fiddle.jshell.net/js/lib/dummy.js](https://fiddle.jshell.net/js/lib/dummy.js)",
    "entryType": "resource",
    "startTime": 115.49500000546686,
    "duration": 0,
    "initiatorType": "script",
    "nextHopProtocol": "h2",
    "workerStart": 0,
    "redirectStart": 0,
    "redirectEnd": 0,
    "fetchStart": 115.49500000546686,
    "domainLookupStart": 115.49500000546686,
    "domainLookupEnd": 115.49500000546686,
    "connectStart": 115.49500000546686,
    "connectEnd": 115.49500000546686,
    "secureConnectionStart": 0,
    "requestStart": 115.49500000546686,
    "responseStart": 115.49500000546686,
    "responseEnd": 115.49500000546686,
    "transferSize": 0,
    "encodedBodySize": 0,
    "decodedBodySize": 0,
    "serverTiming": []
  },
  {
    "name": "[https://fiddle.jshell.net/css/result-light.css](https://fiddle.jshell.net/css/result-light.css)",
    "entryType": "resource",
    "startTime": 115.77999999281019,
    "duration": 0,
    "initiatorType": "link",
    "nextHopProtocol": "h2",
    "workerStart": 0,
    "redirectStart": 0,
    "redirectEnd": 0,
    "fetchStart": 115.77999999281019,
    "domainLookupStart": 115.77999999281019,
    "domainLookupEnd": 115.77999999281019,
    "connectStart": 115.77999999281019,
    "connectEnd": 115.77999999281019,
    "secureConnectionStart": 0,
    "requestStart": 115.77999999281019,
    "responseStart": 115.77999999281019,
    "responseEnd": 115.77999999281019,
    "transferSize": 0,
    "encodedBodySize": 49,
    "decodedBodySize": 29,
    "serverTiming": []
  },
  {
    "name": "beginLoop",
    "entryType": "mark",
    "startTime": 128.3699999912642,
    "duration": 0
  },
  {
    "name": "measureLoop",
    "entryType": "measure",
    "startTime": 128.3699999912642,
    "duration": 887.0650000171736
  },
  {
    "name": "endLoop",
    "entryType": "mark",
    "startTime": 1015.4350000084378,
    "duration": 0
  }
]
```

从`console.log`开始。

有了标记，我们可以命名我们的时间标记，所以我们可以测量多个动作。

![](img/78253106ace99bbc410bdc3b4838fccd.png)

照片由[大卫·沙普](https://unsplash.com/@dschap75?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 清除操作

我们可以通过调用`clearMarks`方法来清除性能标记。例如，我们可以这样做:

```
performance.mark("dog");
performance.mark("dog");
performance.clearMarks('dog');
```

还有一个`clearMeasures`方法来清除测量值，还有一个`clearResourceTimings`方法来清除性能条目。

例如，我们可以如下使用它:

```
performance.mark('beginLoop');
for (let i = 0; i < 10000; i++) {
  console.log(i);
}
performance.mark('endLoop');
performance.measure('measureLoop', 'beginLoop', 'endLoop');
performance.clearMeasures("measureLoop");
console.log(performance.getEntriesByName('measureLoop'));
```

那么我们调用`getEntriesByName`的时候应该会看到一个空数组。

要删除所有性能条目，我们可以使用`clearResourceTimings`方法。它清除性能数据缓冲区并将性能数据缓冲区设置为零。

它不需要参数，我们可以如下使用它:

```
performance.mark('beginLoop');
for (let i = 0; i < 10000; i++) {
  console.log(i);
}
performance.mark('endLoop');
performance.measure('measureLoop', 'beginLoop', 'endLoop');
performance.clearResourceTimings();
```

在上面的代码中，我们调用了`clearResourceTimings`方法来将缓冲区和性能数据重置为零，以便我们可以从头开始运行我们的性能测试。

# 结论

我们可以使用 Performance API 来衡量一段前端 JavaScript 代码的性能。

为此，我们可以使用`now`方法来获取时间戳，然后找出两者之间的差异。

我们也可以使用`mark`方法标记时间，然后使用`measure`方法计算测量值。

还有各种方法来获取`performance`条目并清除数据。