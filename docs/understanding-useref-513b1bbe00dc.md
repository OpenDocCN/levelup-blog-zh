# 了解 useRef

> 原文：<https://levelup.gitconnected.com/understanding-useref-513b1bbe00dc>

## refs 和 React 挂钩简介

![](img/05a6f1cc03c0a6603aa045a22d4a1c6a.png)

照片由 [Nathan Shively](https://unsplash.com/@shivelycreative?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

`useRef`是 React 16.8 中的开箱即用挂钩。在 React 的类(有状态)组件中使用的是替代`createRef()`的功能组件。无论你是否熟悉 refs、`createRef`或`useRef`，这篇文章都将引导你了解这个非常有用的钩子的基础知识。

首先，React 功能组件中的`useRef`钩子有两个主要用途:访问 DOM 元素和在状态中存储可变信息，而不会触发组件的重新呈现。我们将看看这两者的一个例子，并讨论一些需要记住的事情。

## 一个警告

一般来说，我们应该避免在 React 中使用`useRef`来做任何可以用*声明的事情——也就是说，我们应该让 React 尽可能地处理应用程序的状态和每个组件的“如何”。然而，在某些情况下，您需要*强制性地*改变 DOM 元素或与之交互，告诉它“如何”处理(或不处理)某些东西，从而绕过状态。一些常见的 DOM 元素示例包括管理特定元素的焦点、选择文本或媒体回放。我们还将探索一些我们可能需要 React 的其他原因。*

## 什么是裁判？

在 React 中，ref 是一个可以在元素标记或组件标记本身中使用的属性。ref 提供了“一种访问在 render 方法中创建的 DOM 节点或 React 元素的方法，”( [Refs 和 DOM](https://reactjs.org/docs/refs-and-the-dom.html) )。在普通 JavaScript 中，我们通过调用`document.getElementById()`，传入给定元素的 id 来处理 DOM 元素。有了 refs in React，我们不再需要这样做了。`ref`属性将直接引用该元素，并让我们访问它的方法。

下面是一个在输入标签中放置引用的示例:

```
<input type="text" ref={textInput} />
```

我们还可以在组件内部使用 ref(但只能在父组件的 render 方法中使用):

```
<FormComponent ref={formRef} />
```

ref 属性是`useRef`的灵感来源，但它不是使用这个钩子的唯一原因。让我们更深入地了解一下`useRef`。

## 什么是 useRef？

> `useRef`返回一个可变的 ref 对象，其`.current`属性被初始化为传递的参数(`initialValue`)。返回的对象将在组件的整个生存期内保持不变。 *(* [*钩子 API 引用— React*](https://reactjs.org/docs/hooks-reference.html#useref) *)*

这是一个令人兴奋的定义——`useRef`的基础是，它是一个可以存储可变信息而不触发重新呈现的对象。`.current`属性是将要初始化的内容，也是存储最新信息的地方。正如我提到的，这对于处理 DOM 元素特别有用，但是这个钩子的用处远不止于此，因为它可以在组件的生命周期中随时改变，而不会触发重新呈现。

## 入门指南

将`useRef`钩子导入到顶部的 React 组件中:

```
import React, { useRef } from 'react';
```

一旦导入了钩子，就可以开始使用它了。`useRef`挂钩的初始化方式与`useState`挂钩相同:

```
const textInput = useRef('');
```

传递给 useRef 的值将是初始值，它存储在`.current`属性中。在这个例子中:`textInput.current = '';`

## 使用`useRef`访问元素

运行我们的文本输入示例，让我们看看如何使用`useRef`钩子通过将焦点切换到输入框来与 DOM 元素交互:

这里我们给按钮一个`onClick`函数，它调用我们的`useRef`钩子`textInput`上的`focus()`方法。当按钮单击时，用户在页面上的焦点切换到该文本框。因为我们将 ref 放在 input 元素中，所以我们可以访问特定 HTML 元素的任何方法，比如`focus()`方法。

## 使用 useRef 在状态中存储信息

正如我已经说过几次的,`useRef`钩子可以存储在组件的整个生命周期中可以改变的信息，而不会触发重新呈现。

这有很多用例，但是作为例子，让我们假设我们想要记录一个组件被重新渲染的次数。我们可以尝试这样做:

然而，由于存储在状态中的任何东西在改变时都会触发重新渲染，所以每次我们增加`count`时，我们的组件都会重新渲染，从而创建一个无限循环。所以这是一个不应该做的事情的例子！但是我们确实需要将这个数字*存储在状态中的某个地方*，这样组件就可以在它的整个生命周期中跟踪它。

再次输入`useRef`。通过简单地将`count`改为`useRef`挂钩而不是`useState`挂钩，我们可以安全地存储这些变异的数据，而无需重新呈现:

注意`useRef`不像`useState`钩子那样自带设置器。要更改 ref，我们只需重新分配 useRef 的`.current`属性中存储的任何值。

另一个注意事项:建议在`useEffect`或`useLayoutEffect`中运行任何副作用，比如改变 ref，以避免 bug。

## 结论

`useRef`钩子是一个非常方便的选项，既可以与 DOM 元素交互，也可以在应用程序中存储变异信息，而不会触发重新渲染。但是，建议仅在必要时使用`useRef`并且在`useEffect`内，以避免错误。

这篇文章是对`useRef`钩子的快速介绍。React 中的`useRef`和`refs`世界里发生了很多事情。以下是一些帮助您入门的文章:

*   [React:使用 Refs 和 useRef 挂钩](https://medium.com/@rossbulat/react-using-refs-with-the-useref-hook-884ed25b5c29)作者 Ross Bulat
*   反应 useRef 和 useLayoutEffect vs useEffect 鲁本·莱贾著
*   [如何在并发模式下正确使用 React useRef 钩子](https://medium.com/better-programming/how-to-properly-use-the-react-useref-hook-in-concurrent-mode-38c54543857b)Daishi Kato