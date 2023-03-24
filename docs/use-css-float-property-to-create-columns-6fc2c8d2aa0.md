# 使用 CSS float 属性创建列

> 原文：<https://levelup.gitconnected.com/use-css-float-property-to-create-columns-6fc2c8d2aa0>

## 使用 float 创建列，并了解为什么内联块不能有效地生成列宽

在 HTML 中，当我们希望将页面分成不同的列时，有多种方法可以在 CSS 中实现。一种更简单的方法是将`display`属性设置为`inline-block`。让我们通过一个代码示例来看看如何实现。

```
<body>
    <div class="div-1">This is 33.33%</div>
    <div class="div-2">This is 66.66%</div>
</body>div {
    display: inline-block;
    padding: 20px;
    text-align: center;
}.div-1 {
    width: 33.33%;
    background: #FF6347;
}.div-2 {
    width: 66.66%;
    background: #0EB36D;
}
```

![](img/e714ce2bf7845c5a9e91527dc395bc19.png)

我们可以看到，尽管我们已经将 div 的 display 属性设置为`inline-block`，并将它们的宽度设置为 33.33%和 66.66%(加起来是 100%)，但是第二个 div 并没有与第一个 div 对齐，而是被放置在第一个 div 的下方。

这是因为在这两个 div 元素之间有一个回车符`\n`，因为它们在 HTML 代码中位于不同的行。浏览器将这个回车符(或换行符)解析为一个空格字符，并将其添加到两个 div 之间。因此，第二个 div(占屏幕宽度的 66.66%)无法找到容纳它自己的必要空间，并运行到下一行。

为了进一步理解，让我们看看当我们修改上面的代码将 div 的宽度设置为 40%时会发生什么。

![](img/02e1ca26054b54e0b8eb15c097e891a2.png)

我们可以看到，虽然两个 div 现在出现在同一行上，但它们之间有一个小的空间。这个空格也是因为回车被 HTML 解析器解释为额外的空格。

有许多方法(或者你可以称之为黑客)来克服这个问题。其中之一是注释掉第一个元素的结束标记和第二个元素的开始标记之间的空格。您还可以将父元素的字体大小设置为 0px，并单独设置子元素的字体大小。然而，我们不打算在本文中深入讨论它们。相反，我们将查看 CSS `float`属性来对齐我们的元素。

CSS 中的浮动属性最初是为了允许文本在文章中的图像周围浮动而引入的。但是由于 float 工作的本质，我们可以使用它在父元素中创建列，而没有使用我们之前讨论的方法的缺点。让我们看一个代码示例，如何修改我们的 CSS 来使用`float`，并让宽度为 33.33%和 66.66%的两个 div 出现在同一行上。

```
div {
    padding: 20px;
    text-align: center;
}.div-1 {
    width: 33.33%;
    background: #FF6347;
    float: left;
}.div-2 {
    width: 66.66%;
    background: #0EB36D;
    float: left;
}
```

![](img/a4f165bc85f0823cde0404f2961ddceb.png)

您可以看到这两个 div 彼此相邻，中间没有任何空间。这是一个很好的例子，说明了如何使用 float 属性在 HTML 中创建列。

当然，您还需要通过将两个 div 包装在一个父元素中并应用`clearfix`技术来清除浮动([您可以在这里](https://medium.com/@kabir4691/how-to-clear-floats-in-css-269f05f411da)阅读更多关于该技术的内容)。