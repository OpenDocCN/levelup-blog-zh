# 一瞥 JavaScript 框架作者的想法

> 原文：<https://levelup.gitconnected.com/a-glimpse-into-the-mind-of-a-javascript-framework-author-689216a8619c>

![](img/d5a02d3f78d0b44b3ce10571d34098aa.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

你曾经读过一个流行的库的源代码，并且遇到过解释代码做什么的注释吗？有时给出的解释很简单，但有时却让你摸不着头脑。最近我有一个这样的时刻，在挠头之后，它让我对开源工具的努力有了更深的理解，并激励我写了这篇文章。

在调查前端框架如何将事件处理程序附加到 DOM 元素时，我遇到了问题中的代码。在过去的几个月里，我一直在重建前端堆栈的不同部分，以此来提高我的知识，其中一部分涉及到创建一个基于虚拟 DOM 范式的 UI 框架。

我看到的代码来自 [Mithril](https://mithril.js.org) 。在内部，它通过创建一个对象并将其作为第二个参数传递给`document.addEventListener`方法来注册 DOM 元素上的事件。相关的源代码是:

第三点:"*对象不从* `*Object.prototype*` *继承，以避免任何潜在的干扰(如 setters)。*“是什么引起了我的注意。我通过秘银 [Gitter](https://gitter.im/mithriljs/mithril.js/archives/2019/09/04?at=5d6fce6c11f374371a0d6a8b) 聊天联系了秘银核心维护者 [Isiah Meadows](https://mobile.twitter.com/isiahmeadows1) ，他的解释是我们接下来要讨论的。

# 防范流氓第三方代码

想象一下，`EventDict`的原型不是继承自`null`，而是`Object.prototype`。然后，一个 Mithril 用户编写以下代码或使用一个库来执行以下操作:

然后，应用程序编写如下:

如果提交表单，控制台将记录`I am on the setter`而不是`I am on the form`。getter 和 setter 函数的内容并不重要，但是它们的存在意味着`onsubmit`事件处理程序并没有像预期的那样在表单上注册。

这是因为当事件被触发时，`EventDict.prototype.handleEvent`方法被执行，这一行`var handler = this["on" + ev.type]`返回`Object.prototype`上的`onsubmit`函数，而不是表单元素上指定的函数。

当 Mithril 运行在一个`Object.prototype`以更简单的方式扩展的环境中时，上面的问题也会出现，如下所示:

应用程序代码是:

在本例中，我们将`onreset`事件添加到了表单中。为了理解它带来的问题，我们首先要看看 Mithril 的`updateEvent`方法。每当在 DOM 元素上设置或移除 [on-event 处理程序](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Event_handlers)时，该方法就会运行。

像 Mithril 这样的虚拟 DOM 框架将像`m('button', { type: 'reset'}, 'Reset')`这样的调用转化为表示给定 DOM 元素的对象。在 Mithril 中，这些对象被称为`vnodes`(作为比较，在 React 中这些对象被称为`fibers`。我已经在这里写了关于他们的文章。`key`是事件名称，`value`是被分配来处理该事件的任何对象或函数。

当执行代码时，表单元素的`vnode`对象被传递给`updateEvent`函数。在函数内部，`else if`子句运行，因为`vnode`的`events`属性是`undefined`。一旦函数执行完毕，`vnode`对象将如下所示:

属性`events`被赋予了一个`EventDict`实例，然后这个实例被赋予了一个对`onreset`函数的引用作为它的属性之一。此外，表单 DOM 元素有一个针对`onreset`事件的事件监听器。到目前为止，一切顺利。

`updateEvent`为 DOM 元素上出现的每个事件运行。第二次调用时，它被作为`key`参数传递给`onsubmit`。然而，这一次，第一个`if`子句运行，因为`vnode.events`不再是`null`。

在这个`if`语句中还有另外两个`if`语句。第一个被跳过，因为`vnode.events[key]`不等于`value`，在本例中是`onsubmit`函数。其实`vnode.events[key]`的值就是`1`。为什么？请记住，这段代码运行的环境是有人编写的:`Object.prototype.onsubmit = 1`。由于表单`EventDict`实例还没有`onsubmit`属性，并且它继承自`Object.prototype`，JavaScript 引擎将沿着原型链向上，发现`onsubmit`存在于`Object.prototype`上。

然后下一个`if`子句检查`value`是否有一个函数或对象。这个检查通过了，但是关键的是，`onsubmit`事件的事件监听器没有附加到表单元素上，因为`vnode.events[key]`不是它应该的`null`，它的值是`1`。下一行将`onsubmit`属性和相关函数添加到表单元素`vnode`的`EventDict`实例中。

就像第一个例子中使用`Object.defineProperty`将`onsubmit`属性添加到`Object.prototype`一样，当表单被提交时，它不会像开发人员预期的那样运行。

# 摘要

上面的两个例子看起来都很奇怪，但这是防御性的编码框架和库作者必须要做的事情。使用构造函数方法创建一个不从`null`继承的新对象意味着新创建的对象受`Object.prototype`添加的任何内容的支配。