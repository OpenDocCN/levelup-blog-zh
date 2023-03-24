# 介绍页面可见性 API

> 原文：<https://levelup.gitconnected.com/introducing-the-page-visibility-api-2fe50ee3dcfa>

![](img/fb4f9300b8da6644616f0019a8d9c338.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Jametlene Reskp](https://unsplash.com/@reskp?utm_source=medium&utm_medium=referral) 拍摄

由于大多数现代浏览器都是选项卡式的，所以网页可能位于后台的选项卡中，用户看不到。

页面可见性 API 可以让我们了解一个页面是否对用户可见。

在本文中，我们将研究页面可见性 API、它的用例以及如何使用它。

# 可见性变化事件

当用户最小化窗口或切换到另一个选项卡时，页面可见性 API 发送一个`visibilitychange`事件，让侦听器知道页面的状态已经改变。

我们可以在事件被触发时处理它，并根据可见性状态做一些事情。例如，当页面隐藏时，我们可以暂停视频。

iframe 的可见性状态与 iframe 所在的父文档相同。用 CSS 隐藏一个`iframe`不会触发可见性事件或者改变包含在`iframe`中的文档的状态。

# 用例

有许多使用 API 的用例。其中一些包括以下内容:

*   页面隐藏时暂停图像轮播
*   当页面隐藏时，停止向服务器轮询信息
*   预渲染页面以保持页面浏览量的准确计数
*   当页面未被查看时关闭声音

如果没有页面可见性 API，开发人员会求助于不完美的解决方案，比如监听窗口的模糊或聚焦事件，以帮助检测页面是否可见。

他们不告诉他们是否隐藏，只是他们是否在焦点上。

# 有助于后台页面性能的策略

当页面不在视图中时，大多数浏览器会做一些事情来帮助节省资源。

`requestAnimationFrame`当页面在后台时，不会调用回调来提高性能和电池寿命。

`setTimeout`等定时器都是后台的节流器或者不活跃的标签来提高性能。

在浏览器中也可以通过后台标签来限制 CPU 的使用。

每个背景选项卡都有自己的时间预算，在-150 毫秒到 50 毫秒之间。

在 Firefox 中，浏览器窗口会在 30 秒后受到限制，在 Chrome 中会在 10 秒后受到限制。

仅当时间预算为非负数时，才允许计时器任务。

一旦计时器的代码完成运行，它执行的持续时间将从时间预算中减去。

在 Firefox 和 Chrome 中，预算以每秒 10 毫秒的速度重新生成。

一些进程免除了节流行为。播放音频的标签被视为前台标签，不受限制。

使用实时网络连接的代码将被解除限制，以防止这些连接被关闭。

IndexedDB 进程也不被限制，以避免超时。

如果我们愿意，页面可见性 API 可以让我们手动停止这些事情。

![](img/cf70d509d75b6013e823107b1df24fea.png)

奥斯卡·萨顿在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 使用页面可见性 API

页面可见性 API 是文档对象的一部分。

我们可以通过检查`document.hidden`属性或`document.visibilityState`属性来使用它。它们都是只读的。

为了观察两者的变化，我们可以听听`visibilitychange`事件。

为此，我们可以使用下面的例子。当我们切换到不同的选项卡时，我们的示例将暂停视频。首先，我们为视频添加 HTML 代码，如下所示:

```
<video controls src='[https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4'](https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4')></video>
```

然后在我们的 JavaScript 代码中，我们可以如下监听`visibilitychange`事件:

```
const video = document.querySelector('video');document.addEventListener('visibilitychange', () => {
  if (document.visibilityState !== 'visible') {
    video.pause();
  }
})
```

在我们的事件监听器回调中，当`visibilityState`不是`‘visible’`时，我们暂停视频，这意味着用户无法通过导航离开标签或窗口、最小化窗口或关闭屏幕来看到标签。

另一种方法是将事件处理程序设置为`document`的`onvisibilitychange`属性。

`document.visibilityState`可以取这 3 个值:

*   `visible` —页面对用户来说是一个前景标签
*   `hidden` —用户看不到该页面，因为它在背景中，或者最小化或设备屏幕关闭。
*   `prerender` —页面正在被预渲染，对用户不可见。文档可能以此状态开始，但永远不会从任何其他状态切换到此状态，因为它只能预呈现一次。并非所有浏览器都支持预渲染。
*   `unloaded` —页面正在从内存中卸载。并非所有浏览器都支持这一点。

# 和睦相处

这个 API 已经被支持了一段时间。Chrome 从版本 33 开始支持这个 API。Edge，Firefox，IE 10 或更高版本，Safari 7 或更高版本都支持这个 API。
这些浏览器的移动版本也支持这个 API。

页面可见性 API 对于检测页面的可见性状态非常有用。我们可以监听`visibilitychange`事件，然后用 document.visibilityState 获得可见性状态，这样我们就可以得到我们想要的结果。