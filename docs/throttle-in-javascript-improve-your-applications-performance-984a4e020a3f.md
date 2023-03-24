# JavaScript 中的节流:提高应用程序的性能

> 原文：<https://levelup.gitconnected.com/throttle-in-javascript-improve-your-applications-performance-984a4e020a3f>

![](img/8b1d455050acb354680e50ea75d5dc76.png)

[***原载于***](https://skilled.dev/course/throttle)

节流是浏览器中常用的一种技术，通过限制代码需要处理的事件数量来提高应用程序的性能。当您希望以受控的速率执行回调时，应该使用 throttle，允许您在每个固定的时间间隔重复处理中间状态。

我将从现实世界的类比开始，然后描述 web 环境中的节流，最后提供一个如何实现节流的注释代码示例。在文章的最后，有一个带有 throttle 示例的 Codepen，您可以与它进行交互来查看它的工作情况。如果您只关心代码，请跳到“用 JavaScript 实现节流”一节。

throttle 是[去抖](/debounce-in-javascript-improve-your-applications-performance-5b01855e086?source=friends_link&sk=609d18e56befb764f6606141a2eaf481)的表亲，它们都提高了 web 应用程序的性能。但是，它们用于不同的情况。当您只关心最终状态时，可以使用去抖。例如，等待直到用户停止键入来获取提前键入搜索结果。当你想处理所有的中间状态，但速度可控时，最好使用节流。例如，在用户调整窗口大小时跟踪屏幕宽度，并在页面内容改变时重新排列页面内容，而不是等到用户完成。

![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)

创建和维护一份简历并不有趣。相反，让我们为你生成一份令人敬畏的简历:) [**简历生成器>**](https://gitconnected.com/resume-builder)

[](https://gitconnected.com/resume-builder) [## 软件工程师简历生成器和示例| gitconnected

### 一份有价值的简历模板，使用您个人资料中的详细信息构建。从你的投资组合网站链接到你的简历或…

gitconnected.com](https://gitconnected.com/resume-builder) ![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)

## Web 开发环境中的节流

假设您有一个 scroll 事件处理程序，您希望在用户向下移动页面时向他们显示新内容。如果我们在用户每次滚动一个像素时执行回调，那么当用户快速滚动时，我们会很快被事件堵塞，因为它会连续发送成百上千个事件。相反，我们节流它，以便我们只检查滚动量每 100 毫秒，所以我们得到每秒只有 10 个回调。对于用户来说，响应仍然是即时的，但是计算效率更高。

throttle 创建均匀分布的函数调用。想象一下，如果您正在事件处理程序回调函数中执行一些繁重的计算或 API 请求。通过限制这些回调，您可以防止应用程序冻结或不必要地向您的服务器发送请求。

## JavaScript 中的节流实现

让我们立即进入油门的代码。我将在下面描述它，然后提供该函数的注释版本。

> 这个 throttle 实现应该是最简单易懂的。它是出于教育目的，并不一定是最有效的或使用最少的代码行。

throttle 是一个高阶函数，它是一个返回另一个函数的函数(为了清楚起见，这里命名为`throttledEventHandler`)。这样做是为了在`callback`、`delay`、`throttleTimeout`和`storedEvent`功能参数周围形成一个[闭合](https://medium.freecodecamp.org/lets-learn-javascript-closures-66feb44f6a44)。这保存了执行`throttledEventHandler`时要读取的每个变量的值。以下是每个变量的定义:

*   `callback`:希望以给定速率执行的节流功能。
*   `delay`:您希望节流功能在执行`callback`之间等待的时间。
*   `throttleTimeout`:用于指示我们`setTimeout`创建的运行油门的值。
*   `storedEvent`:你想用节流`callback`处理的事件。该值将不断更新，直到节流结束。然后，它将使用最近的值执行`callback`。

我们可以在下面的代码中使用我们的 throttle:

```
var returnedFunction = throttle(function() {
  // Do all the taxing stuff and API requests
}, 500);

window.addEventListener('scroll', returnedFunction);
```

由于 throttle 返回一个函数，第一个示例中的`throttledEventHandler`和第二个示例中的`returnedFunction`函数实际上是同一个函数。用户每滚动一次就会执行`throttledEventHandler` / `returnedFunction`。

让我们逐步了解当我们限制一个函数时会发生什么。首先，我们围绕变量创建一个闭包，这样每次执行时它们都可以被`throttledEventHandler`使用。`throttledEventHandler`接收 1 个自变量，即为事件。它将此事件存储在`storedEvent`变量中。

然后我们检查是否有超时运行。主动节流阀)。如果我们确实有一个节流阀，那么`throttledEventHandler`已经完成了这个执行并等待执行回调。如果油门没有被激活，我们可以通过回调函数立即处理这个事件。然后我们调用`setTimeout`并存储超时值，这表明我们的油门正在运行。

当超时处于活动状态时，总是存储最近的事件。回调执行被绕过，这使我们不必执行 CPU 密集型任务或调用我们的 API。

当`setTimeout`结束时，我们将`throttleTimeout`置空，表示该功能不再受限制，可以处理事件。如果有`storedEvent`的话，我们想立即处理它，为此我们递归地调用`throttledEventHandler`。`setTimeout`内部的递归调用允许我们以恒定的速率处理事件。只要新的事件继续出现，它就会在一个期望的延迟后重复执行相同的过程。

该函数的注释版本也是如此:

## 交互式示例

## 包裹

对于 JavaScript 开发人员来说，节流是一个非常重要且值得学习的概念。这是一种提高 web 应用程序性能的常用工具，从头开始实现节流也加强了高级 JS 技术，例如闭包、异步事件处理、高阶函数和递归。

—[@特雷胡芬](https://twitter.com/treyhuffine) | [@gitconnected](https://twitter.com/gitconnected)