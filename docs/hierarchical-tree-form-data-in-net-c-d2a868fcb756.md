# 层次树形数据结构。NET C#

> 原文：<https://levelup.gitconnected.com/hierarchical-tree-form-data-in-net-c-d2a868fcb756>

## 提示和技巧

## 为分层树形数据及其相关操作设计一个数据结构。NET C#

![](img/60053a226e91a2a03f8a9efa18872785.png)

由 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [veeterzy](https://unsplash.com/@veeterzy?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄， [Ahmed Tarek](https://medium.com/@eng_ahmed.tarek) 调整

有时你会发现自己需要处理**层次树形**数据。简单地说，这是呈现在**父子**节点中的数据。

在这种情况下，您有时可能会与实现的复杂性作斗争，尤其是在处理大量数据时。

在本文中，我将为您提供这种情况下的一种可能的设计。然而，您需要记住，像往常一样，拥有一个通用的设计可以提供抽象的功能，但是它可能缺乏针对特定场景的特定增强。

因此，我建议您将本文中的设计作为一个开放思维的工具。你需要研究它，从中学习，从中获得一些想法，并最终使它适应你自己的需要。

![](img/dae42316c04548aad197e34b378e3bf1.png)

# 主要目标

这是本设计的主要目标:

1.  将分层数据表示成树形数据结构。
2.  具有独立创建节点的能力。
3.  具有连接和分离节点的能力。
4.  具有搜索具有适当性能的节点的能力。
5.  不要失去强类型数据的优势。

因此，在设计过程中，我们需要牢记这些要求。

![](img/dae42316c04548aad197e34b378e3bf1.png)[](https://medium.com/subscribe/@eng_ahmed.tarek) [## 🔥订阅艾哈迈德的时事通讯🔥

### 订阅艾哈迈德的时事通讯📰直接获得最佳实践、教程、提示、技巧和许多其他很酷的东西…

medium.com](https://medium.com/subscribe/@eng_ahmed.tarek) ![](img/dae42316c04548aad197e34b378e3bf1.png)

# 设计

首先，我们需要记住，数据本身可以是任何业务实体。然而，层级结构是另外一回事。这是控制节点之间关系的结构，如何添加，如何删除，如何搜索等等。

![](img/dae42316c04548aad197e34b378e3bf1.png)

## 有效载荷

我们在这里可以注意到:

1.  这是主要的实体，代表要包装到我们的层次结构中的业务实体。
2.  我们没有在这里添加任何成员，但可以肯定的是，如果你认为这是你所有业务实体之间的共同之处，你可以添加一些。

## 节点类型

我们在这里可以注意到:

1.  这是表示节点类型的枚举。
2.  我们只有两种节点类型；**结构**和**叶片**。
3.  **结构**节点是只提供层次结构信息的节点。简而言之，它就像一个文件夹或节点，其中会有子节点。
4.  **叶**节点是只提供业务数据的节点。简而言之，它是包装数据的节点，不会有任何子节点。

## 信息节点

我们在这里可以注意到:

1.  这是表示任何节点的公共成员的接口，无论它是**结构**还是**叶**。
2.  `Id`是节点的唯一标识符。这应该是绝对独特的。我把它实现为一个字符串，但是你可以根据自己的需要自由地修改它。
3.  `Name`是节点的名称。这应该在某个时候用于表示层。
4.  `PathPart`是用来表示节点路径的名称。
5.  `Parent`是父节点。它的类型是`IStructuralNode`，因为它不可能是**叶子**，因为**叶子永远不会有孩子**。

## 结构节点

我们在这里可以注意到:

1.  我们定义了委托`ChildNodeAddedEventHandler`来表示一个事件的处理程序，当一个节点作为子节点被添加到另一个节点下时，该事件将被触发。
2.  我们还定义了委托`ChildNodeRemovedEventHandler`来表示一个事件的处理程序，当一个节点从另一个节点的子节点中移除时，该事件将被触发。
3.  我们定义了扩展`INode`的`IStructuralNode`接口。
4.  它有两个事件；`ChildNodeAdded`和`ChildNodeRemoved`。
5.  它有一个只读的子节点集合。
6.  它的节点类型默认为**结构**。
7.  它有一个添加子元素的方法。
8.  它有一个移除子对象的方法。
9.  它还有一个返回所有嵌套子节点的平面集合的方法。

## ILeafNode 和 ILeafNode<tpayload></tpayload>

我们在这里可以注意到:

1.  `ILeafNode`是`INode`的延伸。
2.  它具有属性`Payload`，该属性返回类型`Payload`的包装数据。
3.  它隐藏父`Type`并默认为**叶**。
4.  `ILeafNode<out TPayload>`隐藏父`Payload`并定义一个类型化的。

## 结节

我们在这里可以注意到:

1.  它执行`INode`。
2.  实现很简单。
3.  如果你愿意，你可以扩展这个来实现`IEquatable<Node>`。
4.  我们还允许用户通过`Parent`属性直接设置**父**。然而，实现被委托给了`AddChild`和`RemoveChild`方法。

## 叶节点<tpayload></tpayload>

我们在这里可以注意到:

1.  它继承了`Node`并实现了`ILeafNode<TPayload>`。
2.  这里值得一提的是，在构造函数实现中，如果设置了父类，我们调用父类上的`AddChild`传入`this`。

## 结构节点

在分析这段代码之前，我们需要了解它是如何工作的。

正如我们所说的，节点应该能够被孤立地创建。这意味着最终用户应该能够在不设置任何父节点甚至子节点的情况下创建节点。

那么他应该能够将这个节点作为子节点附加到另一个节点。此外，他应该能够将其他节点作为子节点附加到该节点的子节点，…等等。

对于所有这些操作，我们的代码应该保持节点之间的结构和关系不变。

此外，我们希望使搜索节点变得容易。一种特殊情况是按 Id 搜索。这应该是我们最快的速度。

因此，记住这一点，让我们分析代码。

我们在这里可以注意到:

1.  在构造函数中，如果设置了一个子集合，我们调用`AddChild`方法传递子集合的每个子集合。
2.  同样，如果设置了父节点，我们调用父节点上的`AddChild`并传入`this`。
3.  为了便于搜索节点，我们定义了`Dictionary<string, INode> m_Catalog`。这是一个目录，其中保存了对当前节点下所有节点的引用，甚至是嵌套节点。
4.  为了保持该目录始终同步，当前节点在添加每个直接子节点时订阅它们的`ChildNodeAdded`和`ChildNodeRemoved`事件。
5.  遵循同样的规则，当前节点在每个直接子节点被移除的时刻取消订阅每个直接子节点的`ChildNodeAdded`和`ChildNodeRemoved`事件。
6.  此外，我们需要记住，无论何时将一个节点作为直接子节点添加到当前节点，该节点都可能有其他嵌套子树。在这种情况下，当前目录也必须用该子树的所有嵌套子树进行更新。
7.  那么，说到这里，我们来看看`AddChild(INode child)`的实现。
8.  在将新子节点附加到当前节点的新父节点之前，确保新子节点与其旧父节点分离。
9.  `m_Catalog.Add(child.Id, child);`将新的子项添加到目录中。
10.  `m_Children.Add(child);`将孩子添加到`Children`集合中。
11.  `OnDirectChildNodeAdded(child);`触发`ChildNodeAdded`事件，通知直接父节点添加了一个新的子节点，最终新的更新将应用到它的目录中。
12.  然后我们检查新的子节点是否是一个**结构**节点，因为在这种情况下我们需要监听可能应用于它的变化。
13.  因此，如果这是真的，我们循环新的子元素的平面子元素，将它们一个接一个地添加到当前目录中，并触发`ChildNodeAdded`事件，但是这次使用了`node`和`added`参数的正确值。这样做也是为了向直接父节点通知嵌套更新。请注意，直接父节点也会这样做，并通知其直接父节点，…等等。
14.  正如我们所说的，当前节点应该订阅新子节点的`ChildNodeAdded`和`ChildNodeRemoved`事件，以便它总是被更新。这是我们在`structuralNode.ChildNodeAdded += AddHandler;`和`structuralNode.ChildNodeRemoved += RemoveHandler;`上做的事情。
15.  `child.Parent = this;`将子节点的父节点设置为当前节点。
16.  现在，让我们来看看`RemoveChild(INode child)`的实现。
17.  `m_Catalog.Remove(child.Id);`从目录中删除子项。
18.  `m_Children.Remove(child);`从`Children`集合中删除子元素。
19.  `OnDirectChildNodeRemoved(child);`触发`ChildNodeRemoved`事件以通知直接父节点一个子节点被移除，最终新的更新将应用于其目录。
20.  然后，我们检查新的子节点是否是一个**结构**节点，因为在这种情况下，我们需要监听可能应用于它的更改。
21.  因此，如果这是真的，我们循环这个孩子的扁平孩子，从当前目录中一个接一个地删除他们，并且也触发`ChildNodeRemoved`事件，但是这一次使用`node`和`added`参数的适当值。这样做也是为了向直接父节点通知嵌套更新。请注意，直接父节点也会这样做，并通知其直接父节点，…等等。
22.  我们说过，当前节点应该取消订阅要删除的子节点的`ChildNodeAdded`和`ChildNodeRemoved`事件。这就是我们在`structuralNode.ChildNodeAdded -= AddHandler;`和`structuralNode.ChildNodeRemoved -= RemoveHandler;`做的事情。
23.  `child.Parent = null;`将父项重置为空。

我知道这部分很难，但是，如果你再看一遍，你会发现它并不复杂。

## 树形导航器

这就像一个 **Helper** 类，它提供了额外的功能，比如搜索节点、评估完整路径、查找节点深度等等。

在内部，它很好地利用了我们在节点类中定义的定义良好的结构。

我没有抽象这个类，但是你可以这样做，你可以根据你的需要扩展它的功能。

![](img/e0aced1bcec82fe33e92db5d56920ca2.png)

由 [Elisha Terada](https://unsplash.com/@elishaterada?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄，由 [Ahmed Tarek](https://medium.com/@eng_ahmed.tarek) 调整

# 最后的话

我本来打算编写一个示例应用程序来向您展示我们的设计是多么高效，但是，经过重新考虑之后，我决定将它留给您作为一个练习。

所以，我知道当你看到这个设计时，你可能会觉得它太难理解了。然而，你首先需要记住这项工作有多复杂。

正如我在开头所说的，你需要把这篇文章中的设计作为一个思想开放器，给它一些思考，从中学习，放弃你不需要的，适应它，增强它，最后开始使用它。这永远是学习和获得新技能的正确方法。

就这样，希望你觉得读这个故事和我写它一样有趣。

![](img/dae42316c04548aad197e34b378e3bf1.png)

## 希望这些内容对你有用。如果您想支持:

如果您还不是**中型**会员，您可以使用 [**我的推荐链接**](https://medium.com/@eng_ahmed.tarek/membership) ，这样我就可以从**中型**中获得您的一部分费用，您无需支付任何额外费用。订阅
[**我的简讯**](https://medium.com/subscribe/@eng_ahmed.tarek) 将最佳实践、教程、提示、技巧和许多其他很酷的东西直接发送到您的收件箱。

![](img/dae42316c04548aad197e34b378e3bf1.png)

# 其他资源

这些是你可能会发现有用的其他资源。

[](/when-implementations-affect-abstractions-1bb2adc808d1) [## 当实现影响抽象时

### 关于实现的知识会影响抽象设计吗？

levelup.gitconnected.com](/when-implementations-affect-abstractions-1bb2adc808d1) [](/web-scraping-in-net-c-934d6a85c32e) [## 网页抓取。NET C#

### 如何在 DotNet 中进行网页抓取的指南(。NET) CSharp (C#)，附带代码示例。

levelup.gitconnected.com](/web-scraping-in-net-c-934d6a85c32e) [](/builder-design-pattern-in-net-c-bbf11c891548) [## 中的生成器设计模式。NET C#

### 一步一步的指南，从零开始开发一个流畅的 API。NET C#使用生成器设计模式。

levelup.gitconnected.com](/builder-design-pattern-in-net-c-bbf11c891548) ![](img/dae42316c04548aad197e34b378e3bf1.png)