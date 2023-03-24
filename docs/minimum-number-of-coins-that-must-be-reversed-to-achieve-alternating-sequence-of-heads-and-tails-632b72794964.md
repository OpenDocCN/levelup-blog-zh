# 为获得交替的正面和反面顺序而必须翻转的最小硬币数

> 原文：<https://levelup.gitconnected.com/minimum-number-of-coins-that-must-be-reversed-to-achieve-alternating-sequence-of-heads-and-tails-632b72794964>

![](img/8fad8cc3433b048b0813f353cdf1c9e6.png)

乔希·阿佩尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 问题

有 N 枚硬币，每一枚都有正面或反面。我们希望所有的硬币形成一个首尾交替的序列。要实现这一点，最少需要反转多少硬币？

给定由 N 个表示硬币的整数组成的数组 A，返回必须反转的硬币的最小数量。数组 A 的连续元素代表连续的硬币，包含 0(正面)或 1(反面)。

## 例子

*   示例 01

```
**Input**: [1,0,1,0,1,1]
**Output**: 1
**Explanation**: After reversing the sixth coin, we achieve an alternating sequence of coins [1,0,1,0,1,0].
```

*   示例 02

```
**Input**: [1,1,0,1,1]
**Output**: 2
**Explanation**: After reversing the first and fifth coins, we achieve an alternating sequence of coins [0,1,0,1,0].
```

*   示例 03

```
**Input**: [0,1,0]
**Output**: 0
**Explanation**: The sequence of coins is already alternating.
```

*   示例 04

```
**Input**: [0,1,1,0]
**Output**: 2
**Explanation**: After reversing the first and second coins, we achieve an alternating sequence of coins [1,0,1,0].
```

## 限制

*   n 是范围[1]内的整数..100];
*   数组 A 的每个元素都是一个整数，可以有下列值之一:0，1

# 解决办法

```
def solution(A):
    if len(A) > 0:
        count1 = 0
        count2 = 0
        for index in range(len(A)):
            if index % 2 == 0:
                if A[index] == 1:
                    count1 += 1
                if A[index] == 0:
                    count2 += 1
            else:
                if A[index] == 0:
                    count1 += 1
                if A[index] == 1:
                    count2 += 1

        return min(count1, count2)
```

## 时间和空间复杂性

*   **时间复杂度** : O(n)。给定的`n`是数组中元素的个数。
*   **空间复杂度** : O(1)。没有使用辅助存储器。

# 算法解释

下面是一个演示该算法的示例。给定这个输入数组，`[1,0,1,0,1,1]`。

*   **迭代 1**:0 模 2 等于 0 吗？是的。`1`等于 1 吗？是的。`count1`增量为 1。现在`count1`是 1。
*   **迭代 2**:1 模 2 等于 0 吗？不，`0`等于 0 吗？是的。`count1`增量为 1。现在`count1`是 2。
*   **迭代 3**:2 模 2 等于 0 吗？是的。`1`等于 1 吗？是的。`count1`增量为 1。现在`count1`是 3。
*   **迭代 4**:3 模 2 等于 0 吗？否。`0`是否等于 0？是的。`count1`增量为 1。现在`count1`是 4。
*   **迭代 5**:4 模 2 等于 0 吗？是的。`1`等于 1 吗？是的。`count1`增量为 1。现在`count1`是 5。
*   **迭代 6**:5 模 2 等于 0 吗？编号`1`等于 1 吗？是的。`count2`增量为 1。现在`count2`是 1。
*   `count1`和`count2`的最小值是多少？答案是 1。

这是另一个演示算法的例子。给定这个输入数组，`[0,1,1,0]`。

*   **迭代 1**:0 模 2 等于 0 吗？是的。`0`等于 0 吗？是的。`count2`增量为 1。现在`count2`是 1。
*   **迭代 2**:1 模 2 等于 0 吗？号`1`等于 1 吗？是的。`count2`增量为 1。现在`count2`是 2。
*   **迭代 3**:2 模 2 等于 0 吗？是的。`1`等于 1 吗？是的。`count1`增量为 1。现在`count1`是 1。
*   **迭代 4**:3 模 2 等于 0 吗？否。`0`是否等于 0？是的。`count1`增量为 1。现在`count1`是 2。
*   `count1`和`count2`的最小值是多少？答案是 2。

# 外卖食品

感谢您阅读这个简短的解题问题。如果有人知道更好或更快的时间复杂度来解决这个问题，请随意评论和反馈。和平！✌️