# 性能 JavaScript —事件和对象

> 原文：<https://levelup.gitconnected.com/performant-javascript-events-and-objects-64a809f340a1>

![](img/7587e79d8ce29ad8c7e39c8c58ce6b56.png)

照片由[加里·尼萨姆](https://unsplash.com/@cathus?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何程序一样，如果我们不小心编写代码，JavaScript 程序会变得很慢很快。

在本文中，我们将研究如何改进事件处理和对象操作来提高 JavaScript 应用程序的速度。

# 实施事件委托

事件委托是一种用一个事件处理程序处理来自多个来源的事件的方法。

对于每个事件处理程序，我们必须添加它们，然后删除它们。每个事件处理程序占用更多的资源，因为它们必须能够监听事件。

例如，如果我们想监听来自多个按钮的事件，我们可以编写以下代码:

```
const foo = document.querySelector('#foo');
const bar = document.querySelector('#bar');
const baz = document.querySelector('#baz');foo.addEventListener('click', () => alert('foo clicked'))bar.addEventListener('click', () => alert('bar clicked'))baz.addEventListener('click', () => alert('baz clicked'))
```

那么我们应该重写如下:

```
document.addEventListener('click', (e) => {
  alert(`${e.target.id} clicked`);
})
```

在上面的代码中，我们只有一个事件处理程序，而不是 3 个。这要好得多，因为向任何元素添加事件处理程序都是一项开销很大的操作。

我们还必须记得稍后清理它们，这样我们就可以释放那些被`addEventListener`调用的事件监听器所占用的所有资源。

此外，代码也不太复杂。我们只是监听`document`对象，然后获取我们想要处理的元素的 ID，然后用它做一些事情。

它还可以处理动态生成的元素，不像`addEventListener`，我们必须对每个元素单独显式地调用它。

# 不要两次加载同一个脚本

我们绝对不应该两次加载同一个脚本。把所有东西都加载两次是没有用的，而且会因为加载两次而减慢我们的程序。此外，两次加载脚本可能会导致意外行为。

# 提高物体检测的速度

当我们试图发现一个属性是否存在于一个对象中，并运行存在的函数时，我们应该把函数赋给一个对象，这样我们只有一个对函数的引用。

例如，不是编写以下代码来检测对象，然后调用`if`块中的每个函数，如下所示:

```
const addListener = (element, type, handler) => {
  if (element.addEventListener) {
    element.addEventListener(type, handler);
  } else if (element.attachEvent) {
    element.attachEvent(`on${type}`, handler);
  }
};
```

相反，我们应该编写以下代码:

```
const addListener = (element, type, handler) => {
  if (element.addEventListener) {
    return element.addEventListener(type, handler, false);
  } else if (element.attachEvent) {
    return element.attachEvent(`on${type}`, handler);
  } else {
    return () => {}
  }
};const button = document.querySelector('button');
const listener = addListener(button, 'click', () => alert('clicked'))
```

不同的是，我们返回了函数，然后我们可以用它来调用`removeListener`。

第一个例子没有办法做到这一点，所以我们应该总是返回一个侦听器，这样我们就可以在页面卸载时调用`removeListener`来运行清理代码以删除所有返回的侦听器。

# 不要使用 eval

`eval`是坏函数。它让我们从字符串中提取代码，这并不好，因为恶意代码可能会被注入。

此外，它比从实际代码中运行代码要慢，因为浏览器不能对字符串中的代码进行优化，浏览器不知道它是代码。

因此，我们不应该在代码中使用`eval`。

# 使用函数表达式

函数表达式是当我们创建一个函数，然后把它赋给一个变量时定义的函数。

它们不会被提升，函数只在运行时创建。这意味着它们只在需要的时候被创建。因此，如果我们不需要它，那么它就不会被创建，节省了我们创建函数的时间和内存空间。

![](img/daa2b109b582bf19eca97a3d88e6ff35.png)

照片由[乔·内里克](https://unsplash.com/@jneric?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 没有全局变量

肯定不应该创建全局变量，因为为了找到和使用它们，必须在整个程序中查找它们。

此外，它们很容易发生冲突，因为所有东西都共享同一个全局名称空间。因此，我们没有理由使用它们。

# 结论

事件委托让我们在事件处理程序中处理多个元素的事件，从而缩短了事件处理代码。

这消除了添加和删除元素侦听器所需的大量代码。

全局变量不好，因为需要查找。

`eval`也不好是因为它的安全问题和难以优化。

加载脚本两次除了减慢我们的代码没有任何好处，所以我们不应该这样做。