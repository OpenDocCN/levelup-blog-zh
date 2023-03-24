# LeetCode 75 级别 1 —是子序列

> 原文：<https://levelup.gitconnected.com/leetcode-75-level-1-is-subsequence-fe5183c02ce9>

![](img/a09c75afe6d065d21a7f0f0b429d017e.png)

[Bradyn Trollip](https://unsplash.com/es/@bradyn?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 问题

给定两个字符串`s`和`t`，如果 `s` *是* ***的子序列****`t`*，返回*，否则返回*`false`*。***

**一个字符串的**子序列**是一个新的字符串，它是通过删除一些(可以是没有)字符而不打乱其余字符的相对位置而从原始字符串形成的。(即`"ace"`是`"abcde"`的子序列，而`"aec"`不是)。**

## **例子**

*   ****例 1:****

```
****Input:** s = "abc", t = "ahbgdc"
**Output:** true**
```

*   ****例 2:****

```
****Input:** s = "axc", t = "ahbgdc"
**Output:** false**
```

## ****约束****

*   **`0 <= s.length <= 100`**
*   **`0 <= t.length <= 104`**
*   **`s`和`t`仅由小写英文字母组成。**

# **解决办法**

```
**class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        if not s:
            return True
        if not t:
            return False

        sleft, sright = 0, len(s)
        tleft, tright = 0, len(t)

        while sleft < sright and tleft < tright:
            if s[sleft] == t[tleft]:
                sleft += 1
            tleft += 1

        return sleft == len(s)**
```

## **时间和空间复杂性**

*   ****时间复杂度** : O(t)。给定 t 是字符串`t`中元素的数量。**
*   ****空间复杂度** : O(1)。没有使用辅助存储空间。**

# **外卖食品**

**感谢您阅读这个简短的解题问题。如果有人知道更好或更快的时间复杂度来解决这个问题，请随意评论和反馈。和平！✌️**

# **分级编码**

**感谢您成为我们社区的一员！更多内容见[升级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在改造理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)**