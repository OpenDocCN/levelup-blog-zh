# React 的 useImperativeHandle 变得简单

> 原文：<https://levelup.gitconnected.com/reacts-useimperativehandle-made-simple-81035a21eef0>

![](img/4722c5ded2089331b6de6808c0f6a649.png)

Mick Haupt 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

一般的 React 风格是数据和逻辑单向流动；也就是说，您应该通过 props 向下传递函数和数据，组件应该只能访问作为 props 传入的内容。在需要双向或横向数据流的情况下，我们可以使用 Redux 或 React 的上下文 API 等库。

**然而，**在某些情况下，导入 redux 或使用 context 简直就是矫枉过正——这就是`useImperativeHandle`的用武之地。这将为我们提供一个轻量级的双向流解决方案。考虑下面的例子:

我们有一个`Settings`组件，允许用户定制他们的用户设置和可访问性设置:

这是标准的做事方式；有一个传入状态的父组件和该状态的更新函数。当用户点击提交按钮时，我们将把用户和可访问性设置提交给它们各自的端点。虽然这种方法可行，但也有一些缺点:

***a)设置组件内部的任何东西都可以修改设置的任何一个状态，即使这些状态应该被隔离到它们各自的组件***

***b)当 onSubmit 在任一子设置组件*** 内被调用时，我们无法监听

我们可以通过使用`useImperativeHandle`来改变这一点。这允许我们通过引用来公开子组件内部的函数。让我们修改一下之前的代码:

所以我们用`useRef`创建了一个 ref，然后通过一个叫做`passedInRef`的属性将这个 ref 传递给 UserSettings。然后我们用`passedInRef`和一个函数调用`useImperativeHandle`。我们的函数返回了一个对象。该对象将被存储在`passedInRef.current`中。在我们的对象中，我们定义了一个`submit`函数。所以现在父对象可以调用`userSettings.current.submit()`并提交`userSettings`。

咻！那是很难接受的。**但是现在我们可以确定我们的 userSettings 状态被隔离到了** `**UserSettings**` **组件**——让我们探索第二个优点；当`submit`被调用时被通知。因为我们现在可以在提交被调用时得到通知(在`UserSettings`的上下文中)，所以我们可以将验证消息之类的东西应用到`UserSettings`组件:

以前，这只能通过引入像 context 或 redux 这样重量级的东西来实现，但是有了`useImperativeHandle`,我们可以用一种更加轻量级的方式来实现同样的事情，允许我们在失败时在`UserSettings`组件内部显示一条错误消息。

# 结论

`useImperativeHandle`为双向数据和逻辑流提供 redux 和 props 之间的中间地带。这不是你经常使用的东西，但在极少数情况下，它非常有用，可以防止你不得不过度使用 redux 这样的极端解决方案，或者绞尽脑汁想出像传入多个道具、状态更新器和孤立的错误消息这样的解决方案——后者是最难的。