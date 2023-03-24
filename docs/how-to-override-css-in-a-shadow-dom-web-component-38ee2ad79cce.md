# 如何在影子 Dom/Web 组件中覆盖 CSS

> 原文：<https://levelup.gitconnected.com/how-to-override-css-in-a-shadow-dom-web-component-38ee2ad79cce>

![](img/7ee2d8d6686d20ce1f2c3c2694f2e061.png)

Web 组件的一个主要目的是提供封装——能够保持标记结构和样式隐藏，并与页面上的其他代码分开，以便不同部分不会冲突；这样，代码可以保持整洁。

Shadow DOM 为我们提供了作用域样式封装，并提供了一种让外界尽可能多(或尽可能少)进入的方法。

> 但是，如果我想让我的组件可以针对某些样式属性进行定制，该怎么办呢？

本文涵盖了使用 CSS 自定义属性穿透影子 DOM 并让您的 web 组件可自定义的基础知识。

# 创建 HTML 元素

我们将使用 JavaScript 类创建自定义 HTML 元素，该类扩展了基本 HTML element。然后我们将使用我们想要创建的标签名和我们刚刚创建的类来调用`customElements.define()`。

在这个例子中，我们将创建这个简单的材料设计卡。当我们在 HTML: `<app-card></app-card>`中添加这个元素时，它就会显示出来

![](img/36d91c760dceb93bfba049d3c0f6e89c.png)

首先，我们创建影子 DOM 根，然后我们将 HTML 和 CSS 字符串分配给影子 DOM 根 innerHTML，如下所示。

# 覆盖尝试

在这个例子中，我们想要修改卡片的背景颜色。如果它是 HTML 中的一个简单的`div`元素，你可以覆盖`card`类或者通过 CSS 选择器来访问`div`元素。但是下面的尝试是行不通的:

```
// access the div 
app-card > div {
  background-color: #2196F3;
}// override card class
app-card > .card {
  background-color: #2196F3;
}
```

# 使用 CSS 自定义属性

为了解决这个问题，我们可以使用 [CSS 自定义属性](https://developer.mozilla.org/en-US/docs/Web/CSS/--*) (CSS 变量)。可以使用 CSS 中定义的 CSS 自定义属性来更改自定义元素中的一些 CSS 属性。

按照我们的例子，我们将使用属性`background-color`上的变量`card-bg`来获取由使用定制元素的用户定义的颜色。

现在我们将使用`app-card`定制元素，并在 Body 元素的 CSS 中创建`card-bg`变量。我们将把十六进制颜色`#2196F3`分配给变量。

![](img/34320f21d6da1f1e2bb32e462efb2b3f.png)

# 结论

使用这种策略，我们可以在你的文档中包含一个封装的 CSS 元素，同时我们可以使用 CSS 定制一些属性。您可以点击查看[完整示例。](https://codepen.io/cesarwbr/pen/abNyEPe)