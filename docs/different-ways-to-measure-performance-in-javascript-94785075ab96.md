# JavaScript 中测量性能的不同方法

> 原文：<https://levelup.gitconnected.com/different-ways-to-measure-performance-in-javascript-94785075ab96>

## 学习不同的方法来测量 JavaScript 中执行程序所花费的时间

![](img/9788995666959becb2d0655baeef339a.png)

图片由 [Aron Visuals 制作](https://unsplash.com/@aronvisuals?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

要了解 JavaScript 中一个函数、循环或任何操作的执行时间，我们可以使用以下工具之一:

*   使用`console.time`和`console.timeEnd`
*   使用`performance.now`方法
*   使用在线性能测试工具

# 1.使用控制台. time 和控制台. timeEnd

`console.time`和`console.timeEnd`将打印在函数调用之间执行代码所用的`millisecond`的数量。

```
function test() {
   for(let i =0 ; i < 100000; i++) {
      //operation;
   }
}console.time();test();console.timeEnd();// This will produce default: 1.2529296875ms (time taken to execute test function, value may vary ).
```

我们也可以使用标签来标记不同的计时操作。

```
console.time("test");test();console.timeEnd("test");// test: 1.117919921875ms
```

我们可以使用不同标签的嵌套`console.time`

```
console.time("test1"); test(); console.time("test2");
   test();
   console.timeEnd("test2");console.timeEnd("test1");**test2: 0.875ms
test1: 2.1748046875ms**
```

您可以编写一个自动处理日志记录性能的方法

```
function runTest(fn, n = 1){ **console.time("Test");** for(let i =0 ; i < n; i++) { fn(); } **console.timeEnd("Test");**}// we can pass the function and number of iteration . 
```

为了比较两个函数

```
function compareFunction(fn1, fn2, n = 1) { **console.time("Fn1");** for(let i =0 ; i < n; i++) { **fn1();** } **console.timeEnd("Fn1");** **console.time("Fn2");** for(let i =0 ; i < n; i++) { ** fn2();** } **console.timeEnd("Fn2");** }// we can pass the function and number of iteration .
```

# 2.使用`performance.now()`

`performance.now()`方法将返回一个以毫秒为单位的时间戳，表示从您加载页面到现在已经过了多长时间。

让我们尝试在窗口加载后打印该值。

```
window.onload = function (ev) { console.log(performance.now()); // the value will be around 100ms}
```

让我们用它来检查性能:

```
var start = performance.now();test();var end = performance.now();var timeTaken = end - start; // 1.1350000277161598
```

但是 Mozilla 说

> 时间戳实际上不是高分辨率的。为了缓解诸如 [Spectre](https://spectreattack.com/) 之类的安全威胁，浏览器目前在不同程度上对结果进行了四舍五入。(Firefox 在 Firefox 60 中开始四舍五入到 1 毫秒)。一些浏览器也可能稍微随机化时间戳。在未来的版本中，精度可能会再次提高；浏览器开发人员仍在研究这些计时攻击以及如何最好地减轻它们。

# 在线测试工具

在线性能测试工具将执行我们的 JavaScript 代码，运行基准测试，并给出性能结果。三个最好的在线测试工具是:

1.  [jsPerf](https://jsperf.com)
2.  [jsBen。CH](http://jsben.ch/)
3.  [jsBench.me](https://jsbench.me/)

握住你的手🤚与我一起 [JavaScript Jeep🚙💨](https://medium.com/u/f9ffc26e7e69?source=post_page-----98efbae5e8aa----------------------)。

[**给我买杯咖啡**](https://www.buymeacoffee.com/Jagathish) **。**

![](img/6cc6c127d4371408ba6fece0f75c5e57.png)

[**给我买杯咖啡**](https://www.buymeacoffee.com/Jagathish) **。**