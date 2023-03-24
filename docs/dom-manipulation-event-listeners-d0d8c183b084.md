# DOM 操作—事件侦听器

> 原文：<https://levelup.gitconnected.com/dom-manipulation-event-listeners-d0d8c183b084>

![](img/0fd6c7d45339b5ebf7582357060ab99d.png)

K. Mitch Hodge 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

JavaScript 是世界上最流行的编程语言之一。为了有效地使用它，我们必须了解它的基本知识。

在本文中，我们将研究如何向各种节点添加事件侦听器。

# 向元素节点添加事件侦听器

我们可以通过使用`addEventListener`向元素节点添加事件监听器。

例如，我们可以写:

```
window.addEventListener('mousemove', (e) => {
  console.log(e.clientX, e.clientY);
}, false);
```

监听`mousemove`事件，并在鼠标移动时记录鼠标坐标。

对元素也可以这样做。如果我们有:

```
<button>
  click me
</button>
```

然后我们可以写:

```
const button = document.querySelector('button');button.addEventListener('click', () => {
  console.log('clicked');
}, false);
```

听按钮上的咔哒声。

# 删除事件侦听器

如果原始监听器是一个命名函数，我们可以使用`removeEventListenr`来删除事件监听器。

例如，如果我们有:

```
<button>
  click me
</button>
```

然后我们可以写:

```
const button = document.querySelector('button');const listener = () => {
  console.log('clicked');
  button.removeEventListener('click', listener);
}button.addEventListener('click', listener, false);
```

仅在第一次点击时记录`'clicked'`。

这是因为我们在`button`的`click`事件上调用了`removeListener`,以便在调用一次后移除监听器。

# 从事件对象获取事件属性

我们可以从事件对象中获取事件属性。

这是监听器的第一个参数。

例如，我们可以写:

```
window.addEventListener('load', (event) => {
  console.log(event);
});
```

记录`event`对象，它有各种属性。

它有许多属性和`stopPropgation`、`stopImmediatePropagation`和`preventDefault`方法。

`stopPropgation`停止向所有祖先元素传播事件。

`stopImmediatePropagation`停止调用同一事件的其他监听器。

`preventDefault`停止运行元素的默认动作。

# addEventListener 内部的值

我们传递给`addEventListener`的事件监听器函数中的`this`的值是对事件附加到的节点或对象的引用。

这只适用于传统功能。

例如，如果我们有一个按钮:

```
<button>
  click me
</button>
```

那么如果我们写:

```
const button = document.querySelector('button');button.addEventListener('click', function(event) {
  console.log(this);
});
```

`this`监听器注册到的按钮。

我们可以使用`event.currentTarget`来获得相同的引用。

它比`this`更可靠，因为它也适用于箭头功能。

# 引用事件的目标

我们可以使用`event.target`属性来获取作为事件目标的元素。

这可能与调用事件的节点不同。

例如，我们可以写:

```
document.body.addEventListener('click', (event) => {
  console.log(event.target);
}, false);
```

获取被点击的元素。

# 取消默认浏览器事件

我们可以使用`preventDefault`取消默认的浏览器事件。

例如，我们可以通过使用这个方法来停止链接的默认行为。

如果我们有下面的 HTML:

```
<a href="http://example.com">don't go</a>
```

然后，当用户点击链接时，我们可以通过编写以下内容来阻止用户前往 http://example.com:

```
document.querySelector('a').addEventListener('click', (event) => {
  event.preventDefault();
}, false);
```

当我们单击链接时，它什么也不做。

`preventDefault`不会阻止事件在冒泡或捕获阶段的传播。

在事件监听器函数体的末尾添加`return false`与`preventDefault`的结果相同。

事件对象具有`cancelable`属性来指示事件是否会响应`preventDefault`。

# 停止事件流

为了阻止事件流向祖先元素，反之亦然，我们可以使用`stopPropagation`方法。

例如，我们可以写:

```
const button = document.querySelector('button')button.addEventListener('click', (e) => {
  e.stopPropagation();
}, false);
```

停止从事件起源的元素向祖先传播。

![](img/413f6a22702f943a2bddf08490028dfb.png)

由 [Max Baskakov](https://unsplash.com/@snowboardinec?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 停止同一目标上的事件流和其他类似事件

`stopImmediatePropagation`停止事件流阶段以及附加到事件目标的任何其他类似事件，这些事件附加在事件侦听器之后。

例如，我们可以写:

```
const button = document.querySelector('button')button.addEventListener('click', (e) => {
  e.stopImmediatePropagation();
}, false);
```

那么如果我们有:

```
const button = document.querySelector('button')button.addEventListener('click', (e) => {
  e.stopImmediatePropagation();
}, false);document.querySelector('button').addEventListener('click', () => {
  //...
}, false);document.body.addEventListener('click', () => {
  //...
}, false);
```

第一个监听器下面的监听器都不会被调用，因为我们在第一个监听器中调用了`stopImmediatePropagation`。

# 结论

我们可以用`stopPropagation`和`stopImmediatePropagation`来阻止事件的传播。

同样，我们可以用`addEventListener`向元素添加事件监听器，用`removeEventListener`移除它们。