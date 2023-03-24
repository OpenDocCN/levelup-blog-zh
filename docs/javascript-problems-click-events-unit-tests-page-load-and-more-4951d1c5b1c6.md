# JavaScript 问题——点击事件、单元测试、页面加载等等

> 原文：<https://levelup.gitconnected.com/javascript-problems-click-events-unit-tests-page-load-and-more-4951d1c5b1c6>

![](img/68a782b98e016da2a91d6c6deaa5c1f2.png)

照片由[JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# JavaScript 代码单元测试工具

有许多用 JavaScript 应用程序创建和运行单元测试的测试运行程序。

**艾娃**是一个快速的测试运行程序，它让我们可以运行单元测试。它支持承诺和 ES6 语法。此外，每个文件的环境都是独立的。

它还支持生成器和异步函数。也支持可观测量。堆栈跟踪比其他跑步者更容易阅读，因为它是干净的。

**Buster.js** 是另一个试跑者。它是跨平台的，可以在 Windows、Mac 和 Linux 上运行。测试从浏览器上运行，没有标题。多个客户端可以同时运行测试。也可以测试节点应用。可以编写 xUnit 或 BDD 风格的测试。

支持多种 JavaScript 测试框架。这是 SinonJS 附带的。测试在保存时自动运行。

**Jasmine** 是一个 BDD 测试框架。它的语法看起来像 RSpec 语法，用于测试 Ruby 和 Rails 应用程序。

QUnit 让我们测试 JavaScript 前端代码。它主要基于 jQuery、jQuery UI 和 jQuery Mobile。

Sinon 是另一个测试工具。这是一个简单的测试运行程序，让我们模拟代码并运行它们。

它没有依赖关系。

**Jest** 是另一个强大的测试框架，内置了测试运行器和许多工具来帮助我们做测试。它支持所有现代语法，它支持异步和模块，包括模仿它们。

# JavaScript 静态变量

我们可以通过将属性直接附加到构造函数来添加静态变量。

例如，如果我们有:

```
function MyClass () {
  //...
}
```

然后我们可以写:

```
MyClass.staticProp  = "baz";
```

我们直接把`staticProp`附加到`MyClass`上。

# 检查数字是浮点数还是整数

当一个数被 1 除时，我们可以通过使用余数运算符得到它的余数来检查它是否是一个整数。

比如，我们可以写；

```
const isInt = (n) => {
  return n % 1 === 0;
}
```

如果是整数，那么就是`true`。

我们可以通过将`===`改为`!==`来检查它是否是浮点数。我们写道:

```
const isFloat = (n) => {
  return n % 1 !== 0;
}
```

# 检查键盘上的哪个键被按下

我们可以使用按键事件的`keyCode`或`which`属性。

它们都有被按下的键盘键的整数代码。

例如，我们可以写:

```
const code = e.keyCode || e.which;
if(code === 13) { 
  //Do something
}
```

我们检查是否是 13 来检查回车键。

# window.onload vs document.onload

`window.onload`和`document.onload`是有区别的。

`window.onload`在整个页面加载时被触发，包括图像、CSS、脚本等所有内容。

`documebt.onload`在 DOM 准备就绪时调用，这可以在加载图像和其他外部内容之前。

# 对象文字中的自引用

我们可以用`this`来引用`this`当前所在的对象。

例如，如果我们有:

```
const foo = {
  a: 1,
  b: 2,
  get c() {
    return this.a + this.b;
  }
}
```

那么`this.a`是 1`this.b`是 2，那么`c`就是 3。

我们只能用顶级方法做到这一点，包括 getters 和 setters。

否则，`this`的值可能会不同。

# addEventListener vs onclick

要将一个点击监听器附加到一个元素上，我们有几种方法。

我们可以用`'click'`事件调用`addEventListener`。

此外，我们可以将`onclick`属性添加到元素中。

我们还可以获取元素并将回调设置为`onclick`属性的值。

例如，我们可以写:

```
element.addEventListener('click', onClick, false);
```

`onClick`是一个函数。

`false`禁用从根元素向下传播事件的捕获模式。

同样，我们可以写:

```
<a href="#" onclick="alert('hello');">click me</a>
```

我们有带有属性`onclick`的`a`元素，其中包含 JavaScript 表达式。

同样，我们可以写:

```
element.onclick = () => { 
  //...
};
```

我们为 DOM 元素对象的`onclick`方法设置了一个监听器函数。

他们做同样的事情。

![](img/8d457a4c7ad632d91c7a26a3f50699a0.png)

由[托阿·海夫蒂巴](https://unsplash.com/@heftiba?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以用各种方式附加点击监听器，

同样，我们可以使用`this`在顶级方法中引用它自己。

还有很多工具可以为 JavaScript 应用编写和运行单元测试。