# JavaScript 事件处理程序— onblur 和 onerror

> 原文：<https://levelup.gitconnected.com/javascript-events-handlers-onblur-and-onerror-61da707b>

![](img/4b42d863beaaa85424b0e224ff11daa8.png)

照片由 [Alex 在](https://unsplash.com/@alexread?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上阅读

在 JavaScript 中，事件是应用程序中发生的动作。它们是由各种事件触发的，比如输入、提交表单、调整大小等元素变化，或者应用程序运行时发生的错误等。

我们可以分配事件处理程序来响应这些事件。发生在 DOM 元素上的事件可以通过为相应事件的 DOM 对象的属性分配一个事件处理程序来处理。

在本文中，我们将查看`onblur`和`onerror`属性。

# window.document.onblur

`onblur`属性让我们为它设置一个事件处理函数来处理`blur`事件，其中代表可能失去焦点的元素的对象失去了焦点。例如，我们可以通过编写以下代码来附加一个`onblur`事件监听器函数:

```
document.onblur = () => {
  console.log('Document lost focus.');
};
```

添加上面的代码后，当我们在页面外单击时，文档失去焦点。应该被记录。

![](img/054dd76fd5a876aea6daabdddf95e2c8.png)

迈克尔·盖革在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# window.document.onerror

属性让我们附加一个事件处理器来处理触发事件时的情况。

当 JavaScript 运行时错误发生时，触发`error`事件，例如当语法错误或异常在处理程序中被抛出时。在这种情况下，`error`事件处理程序可以接受一个带有`ErrorEvent`对象的参数。

默认情况下，这些错误会冒泡到`window`对象。当图像或脚本元素等资源加载失败时，也会触发该事件。在这种情况下，使用`Event`接口的错误事件将被传递到`error`事件处理程序中。这些错误不会冒泡到`window`对象，但是可以在`window.addEventListener`中处理，将`useCapture`设置为`true`，这是`addEventListener`方法的第三个参数。

由于历史原因，传递给`window.onerror`的参数与传递给`element.onerror`以及错误类型`window.addEventListener`处理程序的参数不同。`window.onerror`的错误处理函数有如下函数签名:

```
window.onerror = function(message, source, lineno, colno, error) { ... }
```

其中`message`是错误消息，`source`是抛出错误的脚本的 URL 字符串。`lineno`参数是一个数字，它包含出现错误的行号。`colno`参数是一个数字，它包含发生错误的行的列号。`error`参数具有`Error`对象。`Error`对象具有以下标准属性:

*   `constructor` —创建实例原型的函数
*   `message` —错误信息
*   `name` —错误名称

它还具有特定于供应商的属性。对于微软浏览器，`Error`对象具有以下附加属性:

*   `description` —错误描述，类似于`message`
*   `number` —错误号

对于 Mozilla 浏览器，以下附加属性被添加到`error`对象中:

*   `fileName` —引发错误的文件的路径
*   `lineNumber` —引发错误的行号
*   `columnNumber` —引发错误的行的列号
*   `stack` —错误的堆栈跟踪

如果一个错误事件处理程序被传递到`window.addEventListener`方法的第二个参数中，那么事件处理程序的函数签名就会有很大的不同。它应该有一个`event`参数，即`ErrorEvent`，因此函数签名应该如下所示:

```
window.addEventListener('error', function(event) { ... })
```

`ErrorEvent`继承了`Event`对象的所有属性，还包括以下属性:

*   `message` —只读属性，作为描述错误的人类可读错误消息的字符串
*   `filename` —只读字符串属性，包含发生错误的文件的脚本名称
*   `lineno` —只读整数，包含发生错误的文件脚本的行号
*   `colno` —只读整数，包含发生错误的脚本的列号
*   `error` —一个只读的 JavaScript 对象，包含关于事件的数据

如果`onerror`是除了`window`之外的任何东西的属性，那么我们分配给它的事件处理程序应该具有与上面相同的签名，它只是`event`参数，当一个事件发生时，它将传入`ErrorEvent`。

因此，使用`onerror`事件处理程序，我们可以处理在加载页面和运行页面上加载的脚本时发生的任何错误。例如，我们可以使用它来处理图像加载失败的情况，方法是提供一个我们可以加载的替代图像。

例如，如果我们想要加载一个可能存在的图像，那么我们可以向`img`元素添加一个`onerror`属性来加载回退图像，如下面的代码所示:

```
<img src="invalid.gif" onerror="this.src='[https://images.unsplash.com/photo-1556742521-9713bf272865?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1534&q=80'](https://images.unsplash.com/photo-1556742521-9713bf272865?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1534&q=80');" />
```

为了在脚本加载期间处理错误，我们可以编写以下代码:

```
<!DOCTYPE html>
<html>
  <head>
    <title>Error</title>
  </head>
  <body>
    <script src="main.js"></script>
    <script src="error.js"></script>
  </body>
</html>
```

然后在`main.js`中，我们添加我们的`onerror`事件处理程序:

```
window.onerror = (message, source, lineno, colno, error) => {
  console.log(message);
  console.log(source);
  console.log(lineno);
  console.log(colno);
  console.log(error.toString());
};
```

然后我们在`error.js`中放入一些抛出错误的表达式或语句，如下面的代码:

```
xyz
```

然后我们应该在`main.js`的`onerror`事件处理程序中的`console.log`语句中记录错误。我们应该从`console.log`语句中得到类似下面的输出:

```
Uncaught ReferenceError: xyz is not defined
[http://localhost:3000/error.js](http://localhost:3000/error.js)
1
1
ReferenceError: xyz is not defined
```

第一个`console.log`输出有错误信息。输出中的第二行包含导致错误消息的脚本的路径。第 3 行是发生错误的脚本的行号。第四行是导致错误的那一行的列号，最后一行是转换成字符串的`Error`对象，它返回错误消息。

如果我们改为使用`window.addEventListener`方法来添加事件处理程序，那么我们可以编写以下代码来记录`ErrorEvent`对象:

```
window.addEventListener("error", event => {
  console.log(event);
});
```

那么我们应该从上面事件处理程序中的`console.log`语句得到类似下面的输出:

```
bubbles: false
cancelBubble: false
cancelable: true
colno: 1
composed: false
currentTarget: Window {parent: Window, postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, …}
defaultPrevented: false
error: ReferenceError: xyz is not defined at [http://localhost:3000/error.js:1:1](http://localhost:3000/error.js:1:1)
eventPhase: 0
filename: "[http://localhost:3000/error.js](http://localhost:3000/error.js)"
isTrusted: true
lineno: 1
message: "Uncaught ReferenceError: xyz is not defined"
path: [Window]
returnValue: true
srcElement: Window {parent: Window, postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, …}
target: Window {parent: Window, postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, …}
timeStamp: 68.510000128299
type: "error"
```

在上面的输出中，我们从`Event`对象中获取所有内容，加上来自`ErrorEvent`对象的附加属性，如上面列出的`error`属性、`message`属性和`filename`属性。

# 结论

使用`onblur`属性，我们可以设置一个可以处理模糊事件的事件处理程序。`onerror`属性让我们设置`error`事件发生时的事件处理程序，当图像或脚本由于某种错误而无法加载时，就会发生这种情况。

这些都是方便的事件处理程序，让我们分别处理用户交互和资源加载时的意外错误。