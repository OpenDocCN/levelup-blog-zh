# 解决 Leetcode 问题，获得你梦想中的公司的报价

> 原文：<https://levelup.gitconnected.com/solve-leetcode-problems-and-get-offers-from-your-dream-companies-a35f331fd4d5>

Leetcode 387。字符串中的第一个唯一字符

在这个系列中，我将和我的朋友一起解决 Leetcode 媒体问题，你可以在我们的 youtube 频道上看到，今天我们将解决问题 Leetcode 387。**字符串中第一个唯一的字符**

![](img/2d51e85faabf452b12a7f1d1bd2132ce.png)

稍微介绍一下我，我过去有来自**优步**印度和**亚马逊**印度的 offers，目前在阿姆斯特丹**Booking.com**工作。

# 学习算法的动机

[](https://medium.com/leetcode-simplified/solve-leetcode-problems-and-get-offers-from-your-dream-companies-2786415be0b7) [## 解决 Leetcode 问题，获得你梦想中的公司的报价

### 练习 Leetcode 背后的介绍和动机

medium.com](https://medium.com/leetcode-simplified/solve-leetcode-problems-and-get-offers-from-your-dream-companies-2786415be0b7) 

# **问题陈述**

给定一个字符串，找出其中第一个不重复的字符并返回其索引。如果不存在，返回-1。

**示例:**

```
s = "leetcode"
return 0.s = "loveleetcode"
return 2.
```

**注意:**您可以假设字符串只包含小写英文字母。

# **Youtube 讨论**

Leetcode 387。字符串中的第一个唯一字符

# **解决方案**

根据问题，我们要返回第一个不重复字符的索引。

**天真的解决方案**

我们来谈谈解决问题的幼稚方法。我们可以从索引`i`处的字符串中选择一个字符，并检查它在左侧子串(0 到 i-1)和右侧子串(i+1 到字符串末尾)中的出现。

如果这个字符没有出现在任何一个列表中，我们返回索引`i`。这将给我们一个`O(n^2)`的时间复杂度。

**优化解决方案**

现在让我们试着改进它。

在处理涉及字符频率的问题时，要尽量使用一个 [**哈希表**](https://www.educative.io/edpresso/what-is-a-hash-table) 。

对于这个问题，我们必须统计每个字符的出现次数，并返回计数为 1 的第一个字符的索引。因此，我们可以首先存储所有字符的出现，然后检查只出现一次的字符。

下面也给出了 C++代码。

时间复杂度: **O(n)** ，n 是字符串的长度

空间复杂度:O(1) ，哈希表最多有 26 个键。

# **类似问题**

1.  [Leetcode 442:查找数组中的所有重复项](https://medium.com/leetcode-simplified/solve-leetcode-problems-and-get-offers-from-your-dream-companies-9d123345c23a)
2.  [Leetcode 1429:第一个唯一编号](https://medium.com/leetcode-simplified/solve-leetcode-problems-and-get-offers-from-your-dream-companies-4f5e49af2fec)

这个问题的代码可以在下面的库中找到。

[](https://github.com/webtutsplus/LeetCode/tree/main/src/LC387_First_Unique_Character) [## webtutsplus/LeetCode

### 在 GitHub 上创建一个帐户，为 webtutsplus/LeetCode 的开发做出贡献。

github.com](https://github.com/webtutsplus/LeetCode/tree/main/src/LC387_First_Unique_Character) 

# 感谢您的阅读，并关注此出版物以了解更多 Leetcode 问题！😃

[](https://medium.com/leetcode-simplified) [## LeetCode 简体

### 我们将现场解决 Leetcode 问题，您可以在我们的 youtube 频道上观看

medium.com](https://medium.com/leetcode-simplified)