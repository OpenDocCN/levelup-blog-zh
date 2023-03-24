# JavaScript 提示—暂停或停止程序、访问脚本元素等等

> 原文：<https://levelup.gitconnected.com/javascript-tips-pausing-or-stopping-programs-accessing-script-elements-e9bc994e33b8>

![](img/2e7a6044c87e5adf5e1aadbca3f5af6f.png)

TS 谢尔盖在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# 将参数传递给 addEventListener 侦听器函数

有几种方法可以将参数传递给事件侦听器函数。

一种方法是创建另一个函数并调用它:

```
element.addEventListener("click", () => {
  foo(someVar);
}, false);
```

我们只是用我们的参数在函数内部调用`foo`。

此外，我们可以在元素上设置一个属性，并通过编写以下内容来访问它:

```
const listener = (evt) => {
  window.alert(evt.currentTarget.foo);
}element.addEventListener('click', listener, false);
element.foo = 'bar';
```

我们获得了具有`currentTarget`属性的元素对象。

此外，我们可以使用`bind`返回一个传入了一些参数的函数:

```
function listener(foo, ev) {
  //...
}
element.addEventListener("click", listener.bind(null, foo), false);
```

我们将`foo`作为`bind`的第二个参数传递给函数。

# 停止 JavaScript 执行

有几种方法可以停止 JavaScript 程序。

一种方法是抛出一个错误。

例如，我们可以写:

```
class FatalError extends Error {}
throw new FatalError('fatal error');
```

我们还可以在函数中使用`return`关键字来阻止它运行:

```
const foo = () => {
  if(someEventHappened) {
     return;
  }
  //...
}
```

# JavaScript 中延期、承诺和未来的区别

延迟的是实现`resolve`、`reject`和`then`的对象。

我们可以创建调用`resolve`、`reject`的承诺，并返回一个调用`then`的对象。

Promise 是一个代理对象，用于存储将来要给出的结果。

它还公开了一个接受另一个目标并返回新承诺的`then`方法。

对这两者来说，未来是一个过时的术语。

# JavaScript 中子节点和子节点的区别

`children`是 JavaScript 中元素的属性。它只返回子元素。

`childNodes`是节点的属性，可以包含任何节点。

# async、await 和 setTimeout 的组合

我们可以用`setTimeout`创建一个承诺，然后我们可以使用 async 并等待它。

例如，我们可以写:

```
const timeout = (ms) => {
  return new Promise(resolve => setTimeout(resolve, ms));
}const foo = async () => {
  await timeout(3000);
  //...
}
```

我们用`Promise`构造函数创建了一个承诺，它调用`resolve`来实现这个承诺。

`ms`是以毫秒为单位的延迟持续时间。

然后我们可以用`async`和`await`在其他任何地方使用。

在节点应用程序中，我们也可以通过编写以下代码来使用`util`包:

```
const sleep = require('util').promisify(setTimeout)
```

# 在 JavaScript 中将整数转换成二进制

我们可以用补零右移运算符将整数转换成二进制。

例如，我们可以写:

```
(dec >>> 0).toString(2);
```

我们用`>>>`运算符将十进制数`dec`转换成二进制数，这将给定的位数向右移动。

在这种情况下，我们向右移动 0 位，使其成为无符号整数。

然后我们用带参数 2 的`toString`把它转换成字符串，把它转换成二进制字符串。

如果我们有一个正整数，那么我们可以调用这个数字的`toString(2)`。

# 引用加载当前正在执行的脚本的脚本标签

我们可以使用`document.currentScript`返回当前正在处理的脚本元素。

简单可靠。

我们不需要修改脚本标签。

它也适用于具有`defer`或`async`属性的异步脚本。

它与动态插入的脚本一起工作。

它不适用于 IE 和类型为`module`的脚本。

我们也可以按 ID 选择脚本:

```
<script id="someScript">
const currentScript = document.getElementById('someScript');
</script>
```

它具有与`document.currentScript`相同的优势，并且得到了普遍支持。

但是我们必须添加一个 ID，在极少数情况下可能会导致奇怪的行为。

此外，我们可以添加自定义数据属性。

例如，我们可以写:

```
<script data-name="someScript">
const currentScript = document.querySelector('script[data-name="someScript"]');
</script>
```

我们通过`data-name`属性获取脚本。

它很简单，可以动态插入脚本。

但是它没有`id`属性得到广泛的支持。

最后，我们还可以通过`src`值得到一个脚本。

例如，我们可以写:

```
const scriot = document.querySelector('script[src="//example.com/script.js"]');
```

这在本地脚本上不起作用。

但是它与`defer`和`async`一起工作，并且在那些情况下是可靠的。

它还可以处理动态插入的脚本。

![](img/b425f38758d548dd8aabab756b174039.png)

照片由[坎巴尼·拉马诺](https://unsplash.com/@kambaniramano?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 结论

有几种方法可以获得页面上的脚本元素。

我们可以用`bind`或调用另一个函数将参数传递给事件监听器。

有几种方法可以暂停或停止 JavaScript 程序的执行。