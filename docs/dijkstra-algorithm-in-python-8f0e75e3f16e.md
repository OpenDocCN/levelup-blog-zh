# Python 中的 Dijkstra 算法

> 原文：<https://levelup.gitconnected.com/dijkstra-algorithm-in-python-8f0e75e3f16e>

![](img/fe55eef6daf8fab385e06e86391ee4b2.png)

艾莉娜·格鲁布尼亚克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这是一种众所周知的算法，用于查找从单个源节点到图中所有其他节点的最短距离和最短路径。这个算法只适用于没有负循环的图(即一个[循环](https://en.wikipedia.org/wiki/Cycle_(graph_theory))，它的边总和为负值)。

# 该算法

1.  创建一个“seen”集合来跟踪访问过的节点。
2.  创建一个字典" *parentsMap* "用于 parentsMap 在算法执行后重构路径。
3.  创建一个字典“ *nodeCosts* ”，用于记录从源节点到达不同节点的最小开销，并将源节点的开销初始化为 0。
4.  创建一个优先级队列数据结构(在 python 中使用 *heapq* 带 list 的模块)并在优先级队列(heap)中推送一个 tuple ( *0，源节点* ) ，表示(从源节点到节点的开销，节点本身)。
5.  在 while 循环中循环，直到优先级队列中没有任何内容。循环时，弹出开销最小的节点。
6.  遍历 pop 节点的所有相邻节点。如果它们以前没有被浏览过，并且总开销比"*节点开销*中的开销小，则将它们也添加到优先级队列中，并更新" *parentsMap* "。
7.  从" *parentsMap* "重新构建路径。

# 代码

使用 parentsMap 重建路径是微不足道的。

# **临时演员**

*   如果我们只想从某个源节点到某个其他节点的最短路径，比如说“结束节点”，我们可以提前退出 while 循环(当我们从堆中取出最小成本节点时，该节点恰好是结束节点)。
*   上面的实现是 Dijkstra 算法的一个懒惰实现(我们正在向堆中添加节点，但是在那个堆中可能有相同的节点具有更高的开销，它来自不同的路径)还有一个急切的实现，你可以在 [my GitHub](https://github.com/abecus/DS-and-Algorithms/blob/master/graph/dijkstra.py) 找到它。

如果你在博客的任何地方发现了任何错误，或者想提出一些建议，可以考虑同样的评论。

感谢阅读。