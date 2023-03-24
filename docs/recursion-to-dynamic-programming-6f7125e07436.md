# 动态规划的递归

> 原文：<https://levelup.gitconnected.com/recursion-to-dynamic-programming-6f7125e07436>

## 解决任何动态规划问题的逐步方法

![](img/38fb2d2adb9e2eac0b7c5d16274a5f16.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [veeterzy](https://unsplash.com/@veeterzy?utm_source=medium&utm_medium=referral) 拍摄的照片

有很多文章解释了动态编程的概念以及使用自顶向下和自底向上的动态编程方法解决问题的方法。如果你没有读过，我建议你读一读我的文章，它涵盖了[动态编程](https://medium.com/swlh/dynamic-programming-made-easy-32b2ec0d018e)的基础知识。

没有多少人知道递归是动态编程解决方案的父。除非他/她知道如何解决递归问题，否则他/她无法解决动态编程解决方案。

找到递归关系就是导出动态规划解决方案。在本文中，我们将从 [LeetCode](https://leetcode.com/problems/longest-common-subsequence/) 中选取一个名为**最长公共子序列**的示例问题，然后通过递归、自顶向下的方法(记忆化)以及自底向上的方法来解决它。

# 问题陈述:

```
**Given two strings str****1** **and** **str2****, return the length of their longest common subsequence.****Input:** str1 = "abcde", str2 = "ace" 
**Output:** 3  
**Explanation:** The longest common subsequence is "ace" and its length is 3.
```

# 递归解决方案:

递归问题有两个重要元素:

1.  寻找基本案例
2.  寻找递归关系

在最长公共子序列问题中，基本情况或最小可能解是当任意一个字符串为空时。

```
**if** m **==** 0 **or** n **==** 0:**return** 0;
```

递归关系很容易找到。如果 m 和 n 的值相等，那么我们加 1，找到字符串剩余部分的最长公共子序列。

```
if str1[m-1] == str2[n-1]:return 1 + lcs(str1, str2, m-1, n-1);else:return max(lcs(str1, str2, m, n-1), lcs(str1, str2, m-1, n))
```

但是如果它们不相等，我们必须调用如上所示的两个函数调用，并将最大值作为结果。

# 自上而下的方法(记忆化):

如果我们为递归解画一个递归树，我们会发现许多函数调用被重复调用。这是被称为重叠子问题的动态规划问题的特性之一。

这个重叠的问题通过内存化来解决，在内存化中，一旦函数调用的结果被处理，我们就将结果存储在内存中，当用相同的输入再次调用函数时，我们返回缓存的结果。

在上面的解决方案中，结果存储在一个名为 l 的二维表中。

# 自下而上的方法:

在自底向上方法中，我们使用迭代方法从底部解决动态规划问题。我们从初始化表中的基本案例开始，并使用先前计算的结果向前推进。

创建一个字符串长度为行和列的二维表

```
L **=** [[None]*****(n**+**1) **for** i **in** range(m**+**1)]
```

使用两个 for 循环，方法类似于递归，当两个字符相似和不相似时，我们有两个条件

**时间复杂度分析:**

1.  **递归解:**最坏情况下的递归解具有 O(2^N).的指数时间复杂度
2.  **自顶向下法(Memoization ):** 自顶向下法在 O(mn)时间内解决问题，其中 m 是字符串 1 的长度，n 是字符串 2 的长度。
3.  **自底向上的方法:**它需要两个循环，因此它的时间复杂度为 O(mn ),类似于自顶向下的方法。

自顶向下和自底向上方法具有相同的时间复杂度，但是由于自顶向下方法是使用递归实现的，因此有可能由于过多的递归调用而导致堆栈溢出。

***感谢您把文章看完，希望这篇文章对您的编码准备有所帮助。请关注我，获取更多此类文章。***

# 资源:

[](https://medium.com/swlh/dynamic-programming-made-easy-32b2ec0d018e) [## 动态编程变得简单

### 通过解决一个流行的 LeetCode 问题来理解动态编程

medium.com](https://medium.com/swlh/dynamic-programming-made-easy-32b2ec0d018e) [](https://leetcode.com/problems/longest-common-subsequence/) [## 最长公共子序列- LeetCode

### 给定两个字符串 text1 和 text2，返回它们的最长公共子序列的长度。一个字符串的子序列是…

leetcode.com](https://leetcode.com/problems/longest-common-subsequence/) [](https://www.geeksforgeeks.org/longest-common-subsequence-dp-4/) [## 最长公共子序列| DP-4 - GeeksforGeeks

### 我们已经分别在集合 1 和集合 2 中讨论了重叠子问题和最优子结构性质。我们也…

www.geeksforgeeks.org](https://www.geeksforgeeks.org/longest-common-subsequence-dp-4/)