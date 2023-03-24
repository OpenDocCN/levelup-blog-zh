# 路线依赖和拓扑排序

> 原文：<https://levelup.gitconnected.com/course-dependency-and-topological-sort-3a6c28c83996>

![](img/3dafaa0b73bfc9100ee4f1c70d7ab2ec.png)

这篇简单的短文说明了在编码面试中图形问题是如何出现在不同地方的。今天的问题涉及一个*有向无环图(DAG)，DAG 的*和*拓扑排序*。问题是 Airbnb 问的。

随意看看一些[老](https://cppcodingzen.com/?p=2333)图论[问题](https://cppcodingzen.com/?p=2059)及其解决方案。

# 问题:

我们得到了一个 hashmap，它将每个`courseId`键与一列`courseIds`值相关联，这表示`courseId`的先决条件是`courseIds`。返回课程的排序，以便我们可以完成所有课程。如果没有这样的排序，则返回空。

例如，给定{'CSC300': ['CSC100 '，' CSC200']，' CSC200': ['CSC100']，' CSC100': []}，应该返回['CSC100 '，' CSC200 '，' CSCS300']。

# 解决方案:

这是图中 [*拓扑排序*](https://en.wikipedia.org/wiki/Topological_sorting) 的超标准应用，如果你对算法了如指掌，你会做得非常好。

算法的步骤如下:

*   从课程散列表中构建一个*有向图*，其中
*   1)每个过程由一个节点表示，并且
*   *2) course_i* 对 *course_* k 有边，如果 *course_i* 依赖于 *course_* k(或者 *course_k* 是 *course_* i 的前提条件)。
*   在该图上执行*深度优先搜索(DFS)* 。假设我们正在从节点 *course_i* 运行 DFS
*   1)假设没有剩下 *course_i* 的*未探索的*邻居。这意味着要么 *course_i* 没有邻居，要么 *course_i* 的每个邻居都已经被访问过。在这种情况下，将*课程 _i* 追加到拓扑排序列表的末尾。
*   2)假设*课程 _i* 的邻居*课程 _j* 当前正在考虑中。这意味着我们从一条路径 *(course_j，…，course_i* )开始，找到了一条新的边 *(course_i，course_j)。*这清楚地表明了原图中的循环，循环意味着*没有拓扑排序是可能的。在这种情况下，返回一个空列表。*

下面是该算法的递归实现(我们添加了额外的块注释来解释实现中使用的每个结构和条件)。如果您习惯于实现标准的 DFS 算法，那么您应该对这个实现非常熟悉🙂

# 测试:

让我们写几个测试来测试，

*   空课程表
*   带循环的课程表
*   标准的课程表，就像上面问题陈述中的那样。

*原载于 2021 年 3 月 7 日*[*【https://cppcodingzen.com】*](https://cppcodingzen.com/?p=2816)*。*