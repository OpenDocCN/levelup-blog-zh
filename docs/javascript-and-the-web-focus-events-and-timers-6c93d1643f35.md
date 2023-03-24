# JavaScript 和 Web —焦点事件和计时器

> 原文：<https://levelup.gitconnected.com/javascript-and-the-web-focus-events-and-timers-6c93d1643f35>

![](img/13bfd9772167963e063e30ae70778737.png)

照片由 [Henry Be](https://unsplash.com/@henry_be?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是世界上最流行的编程语言之一。为了有效地使用它，我们必须了解它的基本知识。

在本文中，我们将看看如何处理焦点事件和计时器。

# 焦点事件

当一个元素获得焦点时，`focus`事件被触发。当它失去焦点时，就会触发`blur`事件。这两个事件不会传播。

当子进程获得或失去焦点时，父进程上的处理程序不会得到通知。

例如，如果我们有一个按钮:

```
<button>
  button
</button>
```

然后我们可以通过写来听这两个事件:

```
const button = document.querySelector('button');button.addEventListener('focus', () => {
  console.log('focus');
})button.addEventListener('blur', () => {
  console.log('blur');
})
```

我们只需要叫`addEventListener`去听他们的。

# 加载事件

当窗口和文档体对象加载时，触发`load`事件。

当遇到标签时，脚本标签立即运行，如果我们想运行操作页面的代码，这可能太快了。我们可能想在加载东西后听这个来运行代码。

像图像或脚本这样的元素可以有自己的 load 事件，当加载元素时会触发该事件。当一个页面被关闭或导航离开时，就会触发`beforeunload`事件。它是用来防止我们因为关闭文档而意外丢失的。

如果我们阻止该事件的默认行为，并将对象的`returnVlue`属性设置为一个字符串。浏览器会显示一个对话框，询问我们是否真的要离开这个页面。

它可能包括我们的字符串，但是恶意地试图使用这些对话框来迷惑人们停留在他们的页面上，所以浏览器不再显示`returnValue`文本。

# 事件和事件循环

事件循环对要运行的操作进行排队，直到它们准备好运行。只有在没有其他操作运行时，才会处理这些操作。如果一个事件循环与其他工作捆绑在一起，那么页面上的任何交互都将被延迟，直到有时间处理它。

因此，如果我们正在做一些非常耗时的事情，那么它必须在后台完成。否则，它会冻结页面。

要在后台运行，我们可以使用 Web Workers。

例如，我们可以写:

```
const worker = new Worker("code/cubeworker.js");
worker.addEventListener("message", event => {
  console.log(event.data);
});
worker.postMessage(10);
worker.postMessage(24);
```

我们编写一个`cubeworker`来在后台进行计算，我们可以向它发送消息来发送计算数据。

然后一旦完成，`message`事件监听器获取结果。

# 定时器

`setTimeout`函数让我们安排代码稍后运行。延迟代码的时间以毫秒为单位。

例如，我们可以写:

```
const fooTimer = setTimeout(() => {
  console.log("foo");
}, 500);
```

上面的代码将回调中的代码延迟了 500 毫秒。

我们可以使用`clearTimeout`移除计时器:

```
if (Math.random() < 0.5) {
  console.log("stopped.");
  clearTimeout(fooTimer);
}
```

然后，如果`Math.random`返回小于 0.5 的值，计时器将不会运行，因为我们在它运行之前调用了`clearTimeout`。

同样，还有定期运行代码的`setInterval`函数。

周期也以毫秒为单位。

例如，我们可以写:

```
let ticks = 0;
let clock = setInterval(() => {
  console.log(ticks++);
  if (ticks === 10) {
    clearInterval(clock);
    console.log("stopped");
  }
}, 200);
```

我们看到记录了 0 到 9，然后记录了`'stopped'`，因为我们每隔 200 毫秒运行一次回调来记录`ticks`的值。

然后，当`ticks`达到 10 时，我们通过运行`clearIterval`来停止计时器。

![](img/5ecb64d1a24875c6b0a6ae065d6101d9.png)

照片由 [Gavin Allanwood](https://unsplash.com/@gavla?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 去抖动

去抖意味着我们延迟一些事件处理程序代码，这样它们就不会运行得太频繁。

我们可以用`setTimeout`来做到这一点。

例如，如果我们有一个输入:

```
<input />
```

我们可以通过写入以下内容来延迟值的记录:

```
let input = document.querySelector("input");
let timeout;
input.addEventListener("input", () => {
  clearTimeout(timeout);
  timeout = setTimeout(() => console.log(input.value),  500);
});
```

侦听器清除旧的计时器，然后创建一个新的计时器，在 500 毫秒后记录该值。

# 结论

焦点事件不会传播，所以它们局限于触发它的对象。

我们还可以添加计时器来定期运行或在设定的延迟间隔后运行一次。

我们可以公开声明侦听器代码，这样它们就不会太频繁地运行。