# 如何使用 SectionList 组件—反应本机基础

> 原文：<https://levelup.gitconnected.com/how-to-use-the-sectionlist-component-react-native-basics-b7ffcf809be5>

![](img/2511cdb108d04a4ee9e7970fb0261262.png)

自从 React Native 的 v0.43 发布以来，我们有两个新的组件来呈现数据列表，即`FlatList` 和`SectionList` *。今天我们将学习`SectionList`组件。*

## **介绍章节列表组件**

`SectionList`组件允许我们创建一个分成可滚动部分的内容列表。SectionLists 类似于 FlatList，但是它们进一步扩展了 flat list 的功能。

## **使用 SectionList 组件**

在本例中，我们将创建一个简单的硬编码数据节列表。数据道具中的每一项都被渲染成一个`Text`组件。

首先，让我们确保我们已经导入了`SectionList` 以及本例中其他必要的导入。

接下来让我们在类中实现一个`render`方法，并返回一个由根`View`组件包装的`SectionListComponent`。

但是我们必须通过一些关键的支持。首先，我们应该能够将数据放到 SectionList 中，我们将通过`sections` prop 来实现这一点。`sections` 属性保存了一个表示每个部分的数据数组，数组中的每一项都是一个对象。

现在让我们传递一些数据给它。

如上所示，`title` 是我们将用于我们的部分标题，它将作为我们各个部分的标题可见。`data` 表示用于创建列表的数组，通常是对象数组。

下一个需要的道具是`renderItem` *，*，它将负责呈现我们的数据数组中的每一项。`renderItem` prop 是一个从数据数组中取出一个`item` 并返回一个 React 元素的函数。

如上面的代码所示，`item`表示数据数组中的每一项。

接下来，我们给万添加了`renderSectionHeader` 道具。`renderSectionHeader` 显示列表视图的页眉部分。

我们不应该忘记添加`keyExtractor` 功能，允许跟踪项目。或者如 React 本地文档所述:

> 用于提取指定索引处给定项的唯一键。Key 用于缓存，并作为 react 键来跟踪项目重新排序。默认提取器检查`*item.key*`，然后像 React 一样使用索引。

这是我们有的

![](img/e6d2899486fd52185917ed8fcaed7aa1.png)

最后，为了让你的看起来像上面的截图，你可以添加下面的样式。

## **完整源代码**

帮助我们演示这一概念的完整源代码如下: