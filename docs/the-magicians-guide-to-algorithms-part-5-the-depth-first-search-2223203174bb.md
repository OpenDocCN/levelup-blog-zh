# 魔术师算法指南，第 6 部分:深度优先搜索

> 原文：<https://levelup.gitconnected.com/the-magicians-guide-to-algorithms-part-5-the-depth-first-search-2223203174bb>

![](img/65857213ab389dbbffd62113a4db59b0.png)

所以上周你向麦格教授展示了你的[广度优先搜索](https://medium.com/gitconnected/the-magicians-guide-to-algorithms-part-4-the-breadth-first-search-b800aec8ccf8) (BFS)法术。她说这很好，但想给你看另一种方法来做同样的事情。这叫做深度优先搜索(DFS)。

**深度优先搜索**

咒语是这样的:

对于此问题，您将使用上周文章中的同一数组:

```
var tree = [
    {value: 6, left: 1, right: 2},
    {value: 5, left: 3, right: 4},
    {value: 7, left: null, right: 5},
    {value: 3, left: 6, right: null},
    {value: 4, left: null, right: null},
    {value: 9, left: 7, right: 8},
    {value: 2, left: 9, right: null},
    {value: 8, left: null, right: null},
    {value: 10, left: null, right: null},
    {value: 1, left: null, right: null}
    ]
```

当用图表表示时，它看起来像这样:

```
 6
                                  / \
                                 5   7
                                / \   \
                               3   4   9
                              /       / \
                             2       8   10
                            /
                           1
```

如果你仔细看上面的咒语，它和 BFS 版本是一样的，只有一个小的不同。您可以使用堆栈，而不是使用队列来存储您想要浏览的节点。

**BFS vs DFS**

解释本周的咒语只是简单地重复上周的相同帖子，所以我们将通过比较两者并涵盖它们的用法来改变事情。

从它们的名字就可以猜到，BFS 算法从浅层开始，逐行深入。另一方面，DFS 可以快速地深入到你正在搜索的结构中，快速到达底部，然后返回到顶部。

那么你应该什么时候使用这些呢？如果解决方案离树的根不远，那么使用 BFS 是显而易见的，因为它首先快速搜索数据结构的顶部。但是，如果您的解决方案能够深入到数据结构内部，DFS 算法将会快速地向下搜索它。此外，在宽树的情况下，DFS 搜索更有效，而窄树容易被 BFS 搜索遍历。

同样值得注意的是:就我所发现的，在移动到右边之前，先穿过树的左边似乎是一种惯例。这涵盖了该算法的第二个细微变化。

**结论**

这就结束了这一点神奇。我还不确定下周我会讲什么，但我有一种感觉，它可能与斐波那契数列有关。我们会看到的。干杯！