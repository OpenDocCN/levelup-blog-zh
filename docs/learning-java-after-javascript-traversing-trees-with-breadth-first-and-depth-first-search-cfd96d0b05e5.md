# 先学 Javascript 后学 Java:用广度优先和深度优先搜索遍历树

> 原文：<https://levelup.gitconnected.com/learning-java-after-javascript-traversing-trees-with-breadth-first-and-depth-first-search-cfd96d0b05e5>

![](img/d6ff29b1895406553b883743cdd280aa.png)

大多是因为 logo 才决定学 Java 的

> 在 Javascript 之后学习 Java 是我写的一个系列，记录我学习一门新语言的进展。

我的关注者和积极支持者都知道，我最近完成了一个软件工程训练营，在那里我学习了 Ruby、Rails、Javascript 和 React。为了在找工作的同时继续学习，我已经决定从**构建 Java 程序**([https://www . thrift books . com/w/Building-Java-Programs-a-back-to-basics-approach _ Stuart-reges/296941/# edition = 7929069&idiq = 7923614](https://www.thriftbooks.com/w/building-java-programs-a-back-to-basics-approach_stuart-reges/296941/#edition=7929069&idiq=7923614))这本书里拿起 Java。

在今天的帖子中，我将通过展示如何在树状数据结构中实现广度优先和深度优先搜索来说明 Java 和 Javascript 之间的语法差异。下面的例子使用了一个简单的节点类。这些节点放在一起时会创建一个无环的树状结构。以下是 Javascript 和 Java 中的节点类:

Javascript 中的节点类

Java 中的节点类

在声明“静态类节点”的下面是节点类的属性。名为 children 的数组列表是用名为 List 的接口创建的。这通常在 Java 中完成，因为有许多不同的数组结构，如果以后你决定使用不同的结构，你只需要改变一个数组列表<node>()。下面是这个类的构造方法。</node>

## 广度优先遍历

广度优先遍历首先从左到右，然后从上到下遍历树。我的解决方案使用队列。

Javascript 中的广度优先遍历函数

该函数接收一个空数组，并返回该数组，其中包含从每个节点推入的数据。我首先声明一个队列，并将根节点推入其中。当队列长度不为 0(或大于 0)时，将第一个节点移出队列，将数据存储到数组中，并将该节点的每个子节点推送到队列中。

Java 中的广度优先遍历函数

这个函数在 Java 中的工作方式是一样的，尽管 Java 有一个可以从 java.util 包中使用的队列接口。在这里，我们将队列创建为一个链表，并依赖于我之前提到的一些内置的队列方法。

## 深度优先遍历

深度优先遍历从根节点移动到最左边的叶子，然后垂直到达每个叶子节点。我的解决方案使用递归来遍历每个节点。

非常短的实现。我将数据从第一个节点推入数组，然后使用 for of 循环遍历节点的每个子节点，并对每个子节点调用深度优先搜索函数。这将在移动到右边的节点之前，将对每个最左边的节点的函数的调用添加到堆栈的叶节点。

在 Java 中也非常相似。一个显著的区别是，我们不能使用类似 for of 循环的东西，因为我们节点的 children 属性是一个 ArrayList，为此我们必须使用常规的 for 循环。

我并没有详细讨论这两种语言中的一些句法差异；我只是想为那些有兴趣在学习 Javascript 之后学习 Java 的人展示这个算法的解决方案的并排比较。我强烈建议解析 Java 解决方案，并查找任何您不清楚的关键字。