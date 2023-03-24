# CSS 中何时使用 Flexbox 与网格

> 原文：<https://levelup.gitconnected.com/when-to-use-flexbox-vs-grid-in-css-bf53e7b83477>

![W](img/d891666cc9ebd9231a574f9c9cb6a45e.png)  W 当涉及到在 web 上创建复杂且响应迅速的布局时，CSS 提供了两个强大的工具:Flexbox 和 Grid。你是否曾经困惑于什么时候网格比 Flexbox 更好，反之亦然？Flexbox 和 Grid 都提供了在容器内排列和对齐元素的解决方案，但是它们有一些关键的区别，使它们更适合某些任务。在本文中，我们将探讨在 CSS 中何时使用 Flex，何时使用 Grid，以及如何决定哪一个适合您的项目。我们还将看一些如何在您自己的 web 开发项目中使用 Flex 和 Grid 的实际例子。无论您是初学者还是有经验的开发人员，本文都将为您在工作中有效使用 Flex 和 Grid 提供有价值的见解和技巧。

这只是众多关于 web 开发的文章中的一篇。欢迎关注我们，获取更多关于 web 开发的精彩内容。我们尝试一周发布多次。请确保不要错过我们的任何精彩内容。

![](img/5b8e58ee7f7fa8c47fe038e63701fc2d.png)

照片由[哈尔·盖特伍德](https://unsplash.com/@halacious?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 网格

网格布局是一个二维布局系统，允许您
在网页上创建行和列。它提供了一种在容器中精确定位元素的方法，并可用于创建复杂且响应迅速的布局。

使用网格布局，您可以定义单个
网格项目的大小和位置，以及
组成网格的行和列的大小和位置。您还可以指定当容器的大小改变时，例如当用户调整浏览器窗口大小时，网格应该如何表现。

要在 CSS 中使用网格布局，您需要将 display: grid 属性
应用于容器元素，然后使用 grid-template-rows 和 grid-template-columns 属性定义网格的行和列
。然后，您可以使用 grid-row 和
grid-column 属性在网格中放置单个项目。

以下是如何使用网格布局创建简单的
两列布局的示例:

```
.container {
 display: grid;
 grid-template-columns: 1fr 1fr; /* Two equal-sized columns */
}
.item1 {
 grid-row: 1;
 grid-column: 1;
}
.item2 {
 grid-row: 1;
 grid-column: 2;
}
```

在此示例中。容器元素被设置为使用网格布局，并且
被分成两个大小相等的列。然后，将. item1 和. item2 元素放在网格的第一行中，其中. item1 放在第一列
，而. item2 放在第二列。

## Flexbox

Flex 布局也称为灵活框布局，是一种一维布局系统，允许您在容器内的元素之间对齐和分配空间。它提供了一种创建响应式布局的方法，这种布局可以自动适应容器的大小和其中元素的大小。

使用 Flex 布局，您可以指定元素
的布局方向(水平或垂直)，以及
元素在容器中的对齐方式和可用空间的分配方式。

要在 CSS 中使用 Flex 布局，您需要将 display: flex 属性
应用于容器元素。然后，您可以使用各种特定于 flex 的属性
来控制容器中元素的布局。

下面是一个如何使用 Flex 布局创建简单的
水平布局的例子:

```
.container {
 display: flex;
 flex-direction: row; /* Elements laid out horizontally */
}
.item {
 flex: 1; /* Equal space distributed among items */
}
```

在此示例中。容器元素被设置为使用 Flex 布局，并且
其中的元素被水平布局。
上的 flex 属性。item elements 指定它们应该占据等量的
可用空间。这导致物品均匀地分布在容器中。

值得注意的是，Flex 布局非常灵活，可以用于
多种场景。这对于创建自动调整容器
大小和其中元素大小的
响应布局特别有用。

## 网格与 Flexbox 的比较

如您所见，网格布局和 Flex 布局都是为网页中的元素创建布局的方式。它们都有自己的优势和使用案例，了解它们之间的差异对于根据您的特定需求选择正确的布局非常重要。在本文中，我们将介绍一些何时使用 Grid 以及何时使用 Flex 的通用指南。

在以下情况下使用网格:

*   您需要创建一个包含行和列的二维布局。
*   您需要以更精确的方式对齐项目。
*   您希望创建一个固定大小的项目网格。

在以下情况下使用 Flex:

*   您希望创建水平或垂直流动的一维布局。
*   您希望在项目之间平均分配空间。
*   您希望根据项目容器的大小来调整项目的大小。

总而言之，Flexbox 和 Grid 都是在 web 上创建灵活且响应迅速的布局的强大工具。Flex 最适合在单个方向上布局元素，而 Grid 更适合在二维网格中排列元素。虽然 Flex 和 Grid 都有各自的优势和局限性，但根据项目的具体需求选择合适的工具非常重要。通过理解 Flex 和 Grid 之间的区别，并知道何时使用它们，您可以创建美观有效的布局来满足用户的需求。

![](img/5c7fdb823e2c7f4190f716ff6bed224c.png)

这篇文章改变了你对他们的看法吗？这篇文章有帮助吗？如果是这样，请务必关注并订阅我们。我们每周多次发布关于 CSS、JavaScript 和软件开发的有趣文章——甚至在节假日也是如此。我们为你将复杂的话题分解成小而易理解的部分。所以，你不想错过任何一个。回头见！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)