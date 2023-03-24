# 更多 JavaScript 错误——对象、异步代码和事件监听器

> 原文：<https://levelup.gitconnected.com/more-javascript-mistakes-objects-async-code-and-event-listeners-ec478018a996>

![](img/ab4dca5211792b04184566f4f4d2a25c.png)

照片由[hkon Helberg](https://unsplash.com/@hakonbrakon?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种比世界上许多其他编程语言更友好的语言。然而，在编写 JavaScript 代码时，由于误解或忽略我们已经知道的东西，仍然很容易犯错误。通过避免下面的一些错误，我们可以通过防止代码中的错误和错别字让我们的生活变得更容易，这些错误和错别字会让我们陷入意想不到的结果。在本文中，我们将会看到将对象键误认为数组索引，不理解异步代码，误用事件监听器。

# 混淆对象键和数组索引

在 JavaScript 中，括号可以用来接受一个对象，该对象可以接受一个包含属性名的字符串，然后从中获取值。例如，如果我们有以下对象:

```
const obj = {
  foo: 1,
  bar: 2
}
```

然后，我们可以通过编写以下内容之一来访问`foo`属性的值:

```
obj.foo
```

或者我们可以使用括号符号并写下:

```
obj['foo']
```

括号符号也用于通过索引访问数组条目。例如，如果我们有:

```
const arr = [1, 2, 3];
```

然后我们写道:

```
arr[0]
```

来获取`arr`数组的第一个条目。

我们必须小心，不要混淆普通对象和数组，因为它们的值可以用括号符号访问，JavaScript 解释器不会区分这两者。这意味着像这样的代码:

```
arr['foo']
```

在 JavaScript 中仍然是允许的。它不能阻止我们犯访问数组的错误——就像我们访问常规对象一样。如果我们记录下`arr[‘foo’]`的值，我们只得到`undefined`。

如果我们不知道一个变量是一个常规对象，一个数组还是其他什么，那么我们可以使用`Array.isArray`方法来检查我们传入的值是否是一个数组。它有一个参数，是我们想要检查的任何对象，看它是否是一个数组。

通过使用`Array.isArray`方法，如果`Array.isArray`返回`true`，我们将确保我们正在访问一个数组。例如，我们可以将其用作以下代码:

```
const arr = [1, 2, 3]if (Array.isArray(arr)) {
  console.log(arr[0]);
}
```

这样，在尝试访问数组条目之前，我们可以确定`arr`是一个数组。

# 不理解异步代码

由于 JavaScript 的单线程特性，许多代码将会是异步的，因为如果等待需要一些时间的东西，比如来自 HTTP 请求的响应，那么一直一行行地运行代码将会使计算机挂起。

对于异步代码，我们经常会运行回调函数中的代码，它的运行时间是不确定的。它们不能像同步代码那样逐行运行。

例如，如果我们想从 get 请求中获取一些数据，我们通常必须使用一个 HTTP 客户端，该客户端异步发出请求，并在不确定的时间内获得响应。

如果我们使用大多数现代浏览器内置的 Fetch API，我们将异步获得响应数据。例如，我们会有如下所示的代码:

```
fetch('[https://jsonplaceholder.typicode.com/todos/1'](https://jsonplaceholder.typicode.com/todos/1'))
  .then((response) => {
    return response.json()
  })
  .then((responseBody) => {
    console.log(responseBody);
  })
```

正如我们看到的，我们有多个回调函数做不同的事情。如果我们写下如下内容:

```
const response = fetch('[https://jsonplaceholder.typicode.com/todos/1'](https://jsonplaceholder.typicode.com/todos/1'))const responseBody = response.json()
console.log(responseBody);
```

这是行不通的，因为正确的代码不会一行一行地运行。在正确的代码中，调用了`fetch`函数，该函数返回一个承诺，然后在第一个`then`方法的回调函数中，我们返回了`response.json()`对象的结果，该对象返回另一个承诺，该承诺解析为响应的 JSON 主体。然后在第二个`then`方法的回调函数中，我们记录了通过解析`response.json()`调用返回的承诺得到的`responseBody`。

通过链接，承诺可以按顺序运行，但它们不会瞬间运行，因此它们不像同步代码那样运行。

一种看起来更像同步代码的简写方式是使用 ES2017 中引入的`async`和`await`关键字。我们可以写:

```
fetch('[https://jsonplaceholder.typicode.com/todos/1'](https://jsonplaceholder.typicode.com/todos/1'))
  .then((response) => {
    return response.json()
  })
  .then((responseBody) => {
    console.log(responseBody);
  })
```

收件人:

```
(async () => {
   const response = await fetch('[https://jsonplaceholder.typicode.com/todos/1'](https://jsonplaceholder.typicode.com/todos/1'));
   const responseBody = await response.json();
   console.log(responseBody);
 })();
```

一个`async`函数看起来像是逐行运行的，但它只是内部传递回调的`then`方法调用的简写。除了在`async`函数中的承诺，我们不能返回任何东西。

![](img/3c7bfd38fcc465da27346079148daacd.png)

[Jade Masri](https://unsplash.com/@jademasri?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 添加太多事件侦听器

如果我们有下面的 HTML 代码:

```
<input type="checkbox" /><button>Click</button>
```

以及相应的 JavaScript 代码:

```
const checkbox = document.querySelector('input[type=checkbox]');
const button = document.querySelector('button');checkbox.addEventListener('change', () => {
  if (checkbox.checked) {
    button.addEventListener('click', () => {
      alert('Alert');
    });
  }
});
```

然后我们会看到，如果我们打开和关闭复选框几次，然后单击按钮，我们会看到“警报”弹出多次。这是因为我们在`button`对象上使用了`addEventListener`方法，该对象表示 HTML 中的按钮元素。每次复选框被选中时，`addEventListener`方法都会附加一个新的点击事件监听器。这意味着我们有多个事件监听器监听按钮的 click 事件。当单击按钮最终触发 click 事件时，我们附加到按钮的所有 click 事件侦听器函数都将运行，导致“Alert”弹出窗口出现多次。

这意味着我们在按钮上附加了太多的事件监听器。

正确的做法是用相同的事件监听器函数来设置`button`的`onclick`属性。同样，我们也可以对`checkbox`对象做同样的事情，即使我们没有相同的一致性问题。我们可以改为写:

```
const checkbox = document.querySelector('input[type=checkbox]');
const button = document.querySelector('button');checkbox.onchange = () => {
  if (checkbox.checked) {
    button.onclick = () => {
      alert('Alert');
    };
  }
};
```

通过这种方式，我们一直覆盖分配给`button.onclick`属性的`onclick`事件监听器，而不是一直给它附加新的点击事件监听器。

在 JavaScript 中，括号可以用来接受一个对象，该对象可以接受一个包含属性名的字符串，然后从中获取值。对于数组，我们可以使用括号符号通过索引来访问数组元素。仅仅通过查看代码，没有办法区分一个对象是否有数组。因此，在试图通过索引访问项之前，我们应该检查一个对象实际上是否是一个数组。

JavaScript 中的许多代码都是异步的，因为 JavaScript 是单线程的，所以让所有代码一行一行地运行会使浏览器停止。这意味着我们必须使用异步代码，通过让没有立即返回结果的代码在后台等待来避免计算机停止工作。我们必须意识到代码在异步函数的回调函数中，因为它们不能存在于回调函数之外。

最后，我们不应该过于频繁地使用`addEventListener`,因为它会不断地向 DOM 对象添加新的事件监听器，而不会丢弃旧的。相反，我们可以使用将事件监听器函数设置为 DOM 对象的属性，该对象监听我们想要监听的事件。例如，如果我们想监听 click 事件，那么我们可以用我们定义的事件处理函数来设置 DOM 对象的`onclick`属性。