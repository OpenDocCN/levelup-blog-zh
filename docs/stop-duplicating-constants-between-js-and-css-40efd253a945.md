# 停止在 JS 和 CSS 之间复制常量

> 原文：<https://levelup.gitconnected.com/stop-duplicating-constants-between-js-and-css-40efd253a945>

## 防止 JS 和 CSS 中常量重复的两个技巧

![](img/c4bb29d2d952c304821340221f53824c.png)

照片由 [Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我们知道，拥有一个以上的真理来源只会导致疯狂。但是我在几个前端代码库中遇到的问题是 CSS 和 JS 中样式常量(颜色、字体、大小)的重复。我们将 CSS 中的原色放在一个很好的变量中，但是我们需要在 JS 代码中使用它(不管它有什么正当的理由)，所以我们继续为它创建一个常量，因为我们不打算硬编码那个值，哦不！但是我们还是违反了干燥原则。不要重复自己。

所以今天我将向你展示两种技术，要么在 CSS 中声明变量并在 JS 中使用它们，要么相反。

# JS => CSS

所以你决定使用 JavaScript 作为你的真实来源。没关系，你有你的理由。例如，你可能从一个`npm`包中加载这些常量，它们被导出为 JS 常量，你需要将它们注入 CSS 中。

所以首先，你需要有一个从代码中注入 CSS 的方法。这里有一个函数:

```
function **addStyle**(styleString) {
  const style = document.createElement(*'style'*);
  style.textContent = styleString;
  document.head.append(style);
}
```

这个函数接收一个参数`styleString`，那将是我们想要注入的 CSS。该函数的第一行创建了一个`<style>`元素，第二行将该元素的内容设置为`styleString`参数，最后一行将该元素附加到我们的 HTML 的`<head>`部分。就是这样。

一旦有了这个函数，就可以用 CSS 变量的标准声明来调用它。

```
**addStyle**(*`
  :root {
    --color-primary:* ${**primaryColor**}; *}
`*)
```

太好了！所以现在你可以从 CSS 中访问这个变量了！

```
button {
  background: var(--color-primary);
}
```

就是这样！很简单，对吧？

不过，这个方法有一个**警告**。CSS 现在依赖于被加载和执行的 JS。为什么这很重要？通常情况下，CSS 是页面加载的第一个东西，JS 是最后一个。例如，如果您使用 CSS-in-JS，您可能已经遇到了这个问题，但是最好记住它。

这里有一个带示例的 codepen 可以玩！

JS 到 CSS

# CSS => JS

这是一种从 JavaScript 代码中访问 CSS 变量的简便方法。

此方法依赖于以下代码片段:

```
function **getCSSVariable**(varName) {
  return getComputedStyle(document.documentElement)
    .getPropertyValue(varName);
}
```

该函数接收变量名作为参数，并执行以下操作:首先，它为`[document.documentElement](https://developer.mozilla.org/en-US/docs/Web/API/Document/documentElement)`(通常是`<html>`)调用`[window.getComputedStyle](https://developer.mozilla.org/en-US/docs/Web/API/Window/getComputedStyle)`，这返回一个`CSSStyleDeclaration`，从这里我们可以调用`getPropertyValue`并获得我们想要的变量。

这里有一个 codepen，也有一个使用这种技术的例子:

CSS 到 JS

# 预处理器呢？

这些技术都依赖于 [CSS 变量](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)特性，所以如果你使用 SASS/LESS/Stylus 变量，它们就不起作用。

但是，您可以从预处理程序变量中声明 CSS 变量，然后从 JS 中获取它们，就像下面这个使用 SASS 的例子:

```
$red: #ff0000;:root {
  --red: $red;
}
```

也许您甚至可以编写一个很酷的函数/mixin 来生成元编程风格的代码。超级酷吧。

希望这些技巧对你有用！