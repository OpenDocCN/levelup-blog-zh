# CSS 网格—全面概述

> 原文：<https://levelup.gitconnected.com/css-grid-a-comprehensive-overview-64c7b60bb0d6>

在 CSS 中定位页面上的元素经常会让人头疼。我最近花时间深入研究了 CSS Flexbox 和 Grid，觉得只要 15 分钟左右的研究，任何人都可以轻松学会在页面上放置项目。CSS Grid 的伟大之处还在于，它是一个很好的媒体查询工具，可以让你的网站响应迅速。您可以轻松决定在大屏幕上并排显示四个元素，在较小的屏幕或平板电脑上减少到两个，然后在移动设备上减少到一个。指定列号非常简单，每个媒体查询一行，可以真正提高应用程序部分的响应能力。

![](img/0bbe0a8fc33026424a404a0c0ea84f51.png)

潘卡杰·帕特尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

CSS 网格布局的强大之处在于它是一个二维系统。与 flexbox 不同，flexbox 主要是一维的，通常需要几层嵌套来创建类似网格的布局，在 CSS Grid 中，您只需将样式应用于一个父元素(通常称为网格容器)，然后应用于所有容器的子元素(网格项目)。

首先，创建一个 div，并为该 div 提供一个容器或网格容器类，以便您可以轻松地对其进行样式化:

```
<div class="grid-container"></div>
```

你的 CSS 的第一步是给这个 div 一个网格的显示样式，你可以在 grid(块级网格)和 inline-grid(内联)之间选择:

```
.grid-container {
    display: grid;
}
```

这本身不会改变页面上的任何内容，但是，它为应用样式打开了大门，样式将组织您选择嵌套在 div 中的元素。

为了便于讨论，我们在容器 div 中添加了几个网格条目 div，现在我们想将它们以特定大小的列并排排列。我们有相当多的选择。为此，我们可以将 grid-template-columns CSS 属性添加到网格容器 div 的样式中:

```
.grid-container {
    display: grid;
    // Option 1 
    grid-template-columns: 100px 200px auto 300px;
    // Option 2 
    grid-template-columns: 1fr 2fr;
    // Option 3
    grid-template-columns: repeat(4, 100px);
}
```

从上面我们可以看到，在定义列的大小和数量时，我们有几个选项。选项 1 让我们根据像素(可以是任何其他单位，如 rem，以使其更具响应性)来指定，并添加 auto，以说明第三列应该适合剩余的空间。在选项 2 中，我们使用 fr 单元将列的大小设置为网格容器中空间的一部分(在上面的例子中，列 1 将获得 1/3 的空间，列 2 将获得 2/3 的空间。我个人倾向于选择选项 3，在该选项中，我们指定列的数量和它们的大小(同样，您可以使用其他单位，如 rem，这是我们建议的)。自然也有一个 grid-template-rows 属性用于处理行。

如果您希望设置所有未来行的大小，当您添加元素并将它们移动到第二行时，这些行可能会出现，您可以使用 grid-auto-rows 属性，以便在创建它们时指定它们的高度，您甚至可以给它们一个 minmax(min，max)值。

虽然还有其他几个属性，如 grid-row-gap、grid-column-gap 和 grid-gap，它们都是不言自明的，但我现在想重点介绍一下您可以应用于网格项的单独样式，以便轻松地说明您希望它们跨越多少列/行。首先，您显然需要向主容器添加一些元素:

```
<div class="grid-container">
    <div class="grid-item-1></div> 
    // More div items here
</div>
```

然后我们可以开始设计它们:

```
.grid-item-1 { 
    // Option 1 
    grid-column-start : 1;
    grid-column-end : 3;
    // Option 2 
    grid-column: 1/-1;
    // Option 3 
    grid-column: span 2;
}
```

在选项 1 中，我们为我们特定的网格项目的跨度规定了明确的开始和结束值。我们声明，我们希望它从第 1 列的最开头开始，在第 2 列的结尾结束。值得注意的是，1 是第 1 列的开始，2 是第 1 列的结束，然后每个额外的数字表示一个额外列的结束。因此，要跨越两列，我们将结束编号设置为 3，三列将为 4，依此类推。在选项 2 中，我们通过在同一个属性中添加开始值和结束值来稍微清理一下。开始值在正斜杠前面，结束号在正斜杠后面。这里我们使用-1，它表示应该在列的末尾结束。最后，选项 3 告诉我们，我们可以选择只声明我们的项目应该跨越多少列，这可能比我们用于 grid-column-end 的数字值稍微容易一些。和往常一样，所有这些属性都有基于行的等价属性。

此外，在设计整个网格的样式或为单个网格项目对齐项目和对齐项目时，可以通过使用 align-content 和 justify-content 轻松对齐项目和网格容器。如果需要覆盖任何内容，还可以在单个网格项目中使用 align-self 和 justify-self。

网格系统还有其他一些特性，比如网格模板，我强烈建议你去看看，因为人们在使用网格系统时肯定有自己的偏好，但是在这篇博文中，我已经介绍了我发现的自己在使用 CSS 网格时的偏好。如果你想学习更多关于网格系统的知识，我鼓励你看看这篇文章，这篇文章是关于这个不可思议的 CSS 技巧网站的——https://css-tricks.com/snippets/css/complete-guide-grid/。

我希望你喜欢这个最强大的 CSS 布局工具的全面介绍，并在未来关注更多的编码技巧、教程和概述！