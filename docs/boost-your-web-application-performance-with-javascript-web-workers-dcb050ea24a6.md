# 使用 JavaScript Web Workers 提升您的 Web 应用程序性能

> 原文：<https://levelup.gitconnected.com/boost-your-web-application-performance-with-javascript-web-workers-dcb050ea24a6>

## 使用 web workers 进行并行编程，同时执行多个操作。

![](img/8202adde5bbc89b3da10e705dbc4f468.png)

通过这篇文章，我将向您介绍 web workers，以及如何将它们集成到您的 web 应用程序中。

# 网络工作者简介

JavaScript web workers 允许开发人员执行并行编程，以帮助应用程序获得同时运行多个计算的能力。

这些 web workers 可以在后台线程中运行脚本，后台线程完全独立于主线程。因此，主线程减少了大量的工作量。这种工作负载分离的主要好处是，您可以在一个独立的线程中执行 CPU 密集型操作，这不会中断或影响主线程的反应性和可用性。当后台线程完成它的任务时，它通过一个事件立即通知主线程结果，这个事件通过普通的 JavaScript 事件处理来管理。

但是，这些 web worker 有一些限制，比如不能访问 DOM，不能访问 web worker 的父页面(创建它的页面)。记住这一点，您现在可以继续学习如何创建 web workers。

# 验证 Web Worker 支持

为了更好地控制错误处理和向后兼容性，建议将 web worker 的代码包含在下面的检查中。这将有助于检测您的浏览器是否支持 web workers。

```
if (window.Worker){
    // Your web worker's code goes here
}
```

现在几乎所有的浏览器都支持 web workers。

# 创建 Web Worker

您可以通过创建 worker 的新实例来创建 web worker。您必须提供您希望 worker 执行的脚本的路径，作为它的参数。

```
const worker = new Worker('./worker-script.js');
```

在上面的代码中，变量`worker`变成了一个`Worker`实例，它将在`worker-script.js`上执行脚本。

这个 web worker 现在可以借助消息与主线程和其他 web worker 进行通信。消息可以包含任何可以序列化和不序列化的内容。函数或 HTML 元素不能在消息中发送。但是，可以发送由数字、字符串、普通对象和普通数组组成的消息。

> 创建 web workers 将产生真正的操作系统级线程，这些线程将消耗系统资源。如果使用不当，这可能会影响用户整个计算机的性能，而不仅仅是 web 浏览器。

# 从主线程向工作线程发送消息

一旦创建了 web worker，就可以使用`postMessage()`方法启动它。

```
worker.postMessage(message);
```

参考下面的代码片段，了解如何在项目中实现上述内容。

您可以通过实现一个`onmessage()`事件处理程序在 web worker 中捕获这个消息。参考下面的代码片段，了解如何处理该消息。

# 从工作线程向主线程发送消息

您可以通过调用 worker 脚本中的`postMessage()`方法从 web worker 发送消息。参考下面的代码片段，了解如何将响应或消息发送回主线程。

回到主线程，您可以将`onmessage()`方法绑定到 worker 对象，以监听从 web worker 发回的消息。参考下面的代码片段，了解如何倾听员工的回应。

> 当消息在主线程和 web worker 之间传输时，它只是被复制或传输(移动)，而不是共享。

# 终止 Web Worker

您可以直接从主线程或从工作线程终止 web 工作线程。在主线程中，您可以通过调用 web worker 的 API 的方法`terminate()`来终止。

```
worker.terminate();
```

您还可以使用 web worker 的`close()`方法从 worker 线程本身终止它。

```
close();
```

当`terminate()`被触发时，web worker 立即被终止，没有任何机会完成正在进行的或未完成的操作。而且，它没有时间清理。因此，突然终止 web worker 可能会导致内存泄漏。类似地，当`close()`在 web worker 内部被触发时，事件循环中出现的任何排队任务都被拒绝，web worker 的作用域被关闭。

> 建议负责任地使用 web workers，并在它们完成执行任务后停止它们。这有助于为客户端计算机上的其他应用程序释放系统资源。

太棒了。您已经学习了如何创建 web worker、如何在两个线程之间有效地发送消息以及如何响应这些消息的基础知识。我很高兴听到你如何在你自己的项目中使用这些网络工作者。请在评论区告诉我。

> 如果你喜欢读这篇文章，并且学到了一些新的东西，那么请鼓掌，与你的朋友分享，并关注我以获得我即将发布的文章的更新。你可以在 [LinkedIn](https://www.linkedin.com/in/tara-prasad-routray-b83027145/) 上联系我。