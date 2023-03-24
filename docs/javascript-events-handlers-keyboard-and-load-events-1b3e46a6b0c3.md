# JavaScript 事件处理程序—键盘和加载事件

> 原文：<https://levelup.gitconnected.com/javascript-events-handlers-keyboard-and-load-events-1b3e46a6b0c3>

![](img/9fd2ba27b8c4da913fecb976d405914f.png)

照片由 [Aryan Dhiman](https://unsplash.com/@its_lensation?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 JavaScript 中，事件是应用程序中发生的动作。它们是由各种事情触发的，比如输入、提交表单、调整大小等元素变化，或者应用程序运行时发生的错误等。我们可以分配一个事件处理程序来响应这些事件。

发生在 DOM 元素上的事件可以通过为相应事件的 DOM 对象属性分配一个处理程序来处理。在本文中，我们将了解如何使用可编辑输入元素的`onkeydown`和`onkeypress`属性，以及任何 DOM 元素的`onload`属性。我们还将查看媒体元素的属性`onloadeddata`，如`audio`和`video`。

# `onkeydown`

我们可以为输入 DOM 元素的`onkeydown`属性设置一个事件处理函数来处理`keydown`事件。当任何键被按下时，不管它们是否产生字符值，都会触发`keydown`事件。`keydown`事件提供指示哪个键被按下的代码，而`keypress`事件提供被输入的字符。比如小写的‘a’会用`keydown`报告为键码 65，但是字符码 97 会用`keypress`报告。`keypress`已被弃用，因此不应在生产代码中使用。

在 Firefox 中，从版本 65 开始，`keydown`事件也在 IME 合成期间被触发，以提高中文、日文和韩文用户的跨浏览器兼容性。我们可以在 IME 合成期间忽略`keydown`事件，我们可以检查由`keydown`事件处理程序提供的`event`对象的`isComposing`属性。例如，我们可以写:

```
const input = document.querySelector('input');input.onkeydown =  event => { 
  if (event.isComposing || event.keyCode === 229) {
    return;
  }
};
```

每当我们键入中文、日文或韩文时，`isComposing`属性的值将是`true`，而`keyCode`属性的值将是 229。

我们还可以用它来记录用户在键盘上按下的键码的值。我们可以先放下面的 HTML 代码:

```
<input type="text" id="input" required>
<p id="log"></p>
```

然后在相应的 JavaScript 代码中，我们可以编写以下代码，通过设置我们的`input`元素的`onkeydown`属性，为`keydown`事件附加事件处理函数。事件处理函数将记录被按下的键的键码。

```
const input = document.querySelector('input');
const log = document.getElementById('log');input.onkeydown =  e => { 
 log.textContent += ` ${e.code}`;
};
```

我们应该得到这样的结果:

```
KeyE KeyR KeyF KeyG KeyT KeyG KeyT KeyG KeyH KeyH KeyF KeyV KeyG KeyB KeyG KeyB
```

在 ID 为`log`的元素中。

# onkeyup

输入元素的`onkeyup`属性让我们附加一个事件处理函数来处理`keyup`事件。当用户释放之前按下的键时，触发`keyup`事件。

`keyup`事件提供一个代码，指示哪个键被按下，而`keypress`事件提供输入的字符。比如小写的‘a’会用`keyup`报告为键码 65，但是字符码 97 会用`keypress`报告。

在 Firefox 中，从版本 65 开始，`keyup`事件也在 IME 合成期间被触发，以提高中文、日文和韩文用户的跨浏览器兼容性。我们可以在 IME 合成期间忽略`keyup`事件，我们可以检查由`keyup`事件处理程序提供的`event`对象的`isComposing`属性。例如，我们可以写:

```
const input = document.querySelector('input');input.onkeyup =  event => { 
  if (event.isComposing || event.keyCode === 229) {
    return;
  }
};
```

我们还可以用它来记录用户在键盘上按下的键码的值。我们可以先放下面的 HTML 代码:

```
<input type="text" id="input" required>
<p id="log"></p>
```

然后在相应的 JavaScript 代码中，我们可以编写以下代码，通过设置我们的`input`元素的`onkeydown`属性，为`keydown`事件附加事件处理函数。事件处理函数将记录被按下的键的键码。

```
const input = document.querySelector('input');
const log = document.getElementById('log');input.onkeyup =  e => { 
 log.textContent += ` ${e.code}`;
};
```

我们应该得到这样的结果:

```
KeyD KeyF KeyG KeyT KeyH KeyG KeyT
```

![](img/c2dda1c44271e52b1da292ef6a6cef3e.png)

Artem Beliaikin 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 装载

DOM 元素的`onload`属性允许我们为它设置一个事件处理函数来处理`load`事件，当加载任何元素时都会触发该事件。它在文档加载过程结束时被触发。当`load`事件被触发时，文档中的所有对象都应该在 DOM 中，包括所有图像、脚本、链接和子框架。

还有`DOMContentLoaded`和`DOMFrameContentLoaded`，它们在页面的 DOM 被加载后被触发，但不等待其他资源完成加载。

例如，我们可以使用它在`window`或`img`元素加载后运行一些代码，首先编写 HTML 代码:

```
<!DOCTYPE html>
<html>
  <head>
    <title>Onload</title>
  </head>
  <body>
    <div>Text</div>
    <img
      src="[https://images.unsplash.com/photo-1503066211613-c17ebc9daef0?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1500&q=80](https://images.unsplash.com/photo-1503066211613-c17ebc9daef0?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1500&q=80)"
    />
    <script src="main.js"></script>
  </body>
</html>
```

然后我们可以编写相应的 JavaScript 代码:

```
const img = document.querySelector("img");img.onload = () => {
  console.log("img loaded");
};window.onload = () => {
  console.log("window loaded");
};
```

那么我们应该看到:

```
img loaded
window loaded
```

当我们加载页面时。

# onloadeddata

当媒体的第一帧完成加载时，触发`loadeddata`事件。适用于`audio`、`video`等媒体元素。为了处理这个事件，我们可以设置媒体元素的`onloadeddata`属性，在这个事件被触发时运行一个事件处理函数。

例如，我们也可以在下面的代码中设置`onloadeddata`属性。首先，我们为`video`元素添加 HTML 代码:

```
<video src='[https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4'](https://sample-videos.com/video123/mp4/240/big_buck_bunny_240p_30mb.mp4')></video>
```

然后在相应的 JavaScript 代码中，我们可以用下面的代码设置一个事件处理函数:

```
const video = document.querySelector('video');video.onloadeddata = () => {
 console.log('video loaded');
}
```

事件处理函数也有一个`event`对象作为第一个参数。我们可以像在下面的代码中一样添加一个`event`参数:

```
const video = document.querySelector('video');video.onloadeddata = (event) => {
 console.log(event);
}
```

这让我们明白了:

```
bubbles: false
​cancelBubble: false
​cancelable: false
​composed: true
​currentTarget: null
​defaultPrevented: false
​detail: 0
​eventPhase: 0
​explicitOriginalTarget: <html>
​isTrusted: true
​layerX: 0
​layerY: 0
​originalTarget: HTMLDocument [https://fiddle.jshell.net/_display/](https://fiddle.jshell.net/_display/)
​rangeOffset: 0
​rangeParent: null
​relatedTarget: null
​returnValue: true
​srcElement: HTMLDocument [https://fiddle.jshell.net/_display/](https://fiddle.jshell.net/_display/)
​target: HTMLDocument [https://fiddle.jshell.net/_display/](https://fiddle.jshell.net/_display/)
​timeStamp: 1463
​type: "focus"
​view: Window [https://fiddle.jshell.net/_display/](https://fiddle.jshell.net/_display/)
​which: 0
```

上面的输出是`Event`对象的属性和相应的值。要查看关于`Event`对象的更多细节，我们可以看看以前的文章。

我们可以为输入 DOM 元素的`onkeydown`属性设置一个事件处理函数来处理`keydown`事件。当任何键被按下时，不管它们是否产生字符值，都会触发`keydown`事件。`keydown`事件提供了一个指示哪个键被按下的代码。输入元素的`onkeyup`属性让我们附加一个事件处理函数来处理`keyup`事件。当用户释放之前按下的键时，触发`keyup`事件。`event`对象提供与`keydown`事件处理程序相同的信息。

DOM 元素的`onload`属性允许我们为它设置一个事件处理函数来处理`load`事件，当加载任何元素时都会触发该事件。它在文档加载过程结束时被触发。当触发`load`事件时，文档中的所有对象都应该在 DOM 中，包括所有图像、脚本、链接和子框架。

当`audio`和`video`等媒体的第一帧加载完成时，触发`loadeddata`事件。为了处理这个事件，我们可以设置媒体元素的`onloadeddata`属性，以便在这个事件被触发时运行一个事件处理函数。