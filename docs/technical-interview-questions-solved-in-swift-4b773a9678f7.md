# Swift 解决的技术面试问题

> 原文：<https://levelup.gitconnected.com/technical-interview-questions-solved-in-swift-4b773a9678f7>

## 两个 sum，回文，装水最多的容器，最长的常用前缀。

![](img/8689746ace0beb8b27a4e5ef79cb5a15.png)

照片由[安德鲁·尼尔](https://unsplash.com/@andrewtneel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

是的，许多科技公司会有一些不需要你在界面上添加标签和文本字段的技术问题。如果你想为大公司工作，那么你的技术面试问题会挑战你的算法知识。

有一些网站提供编码面试实践，比如 hackerRank 和 geeksforgeeks。你将在下面找到的所有技术面试问题都来自 LeetCode，因为我曾经在上面练习编码。

# 两个总和

## [问题](https://leetcode.com/problems/two-sum/)

给定一个整数数组`nums`和一个整数`target`，返回这两个数的索引*，使它们相加为* `*target*`。

你可以假设每个输入都有 ***恰好*一个解决方案**，你不能两次使用*相同的*元素。

可以任意顺序返回答案。

## 解决办法

# 回文数

## [问题](https://leetcode.com/problems/palindrome-number/)

判断一个整数是否是回文。当一个整数向后读和向前读一样时，它就是一个回文。

**跟进:**你能不用把整数转换成字符串就能解出吗？

**例 1:**

```
**Input:** x = 121
**Output:** true
```

**例 2:**

```
**Input:** x = -121
**Output:** false
**Explanation:** From left to right, it reads -121\. From right to left, it becomes 121-. Therefore it is not a palindrome.
```

## 解决办法

# 盛水最多的容器

## [问题](https://leetcode.com/problems/container-with-most-water/)

给定`n`非负整数`a1, a2, ..., an`，其中每个代表坐标`(i, ai)`上的一个点。`n`画垂直线，使线`i`的两个端点在`(i, ai)`和`(i, 0)`。找出两条线，它们和 x 轴一起形成一个容器，这样容器中的水最多。

**例 1:**

```
**Input:** height = [1,8,6,2,5,4,8,3,7]
**Output:** 49
**Explanation:** The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49.
```

## 解决办法

# 最长公共前缀

## [问题](https://leetcode.com/problems/longest-common-prefix/)

写一个函数在字符串数组中寻找最长的公共前缀字符串。

如果没有公共前缀，则返回空字符串`""`。

**例 1:**

```
**Input:** strs = ["flower","flow","flight"]
**Output:** "fl"
```

**例二:**

```
**Input:** strs = ["dog","racecar","car"]
**Output:** ""
**Explanation:** There is no common prefix among the input strings.
```

## 解决办法

以后我会为你提供更多技术问题的解答。感谢阅读！