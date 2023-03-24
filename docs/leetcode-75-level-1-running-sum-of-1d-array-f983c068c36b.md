# leet code 75 Level 1—1D 阵列的运行总和

> 原文：<https://levelup.gitconnected.com/leetcode-75-level-1-running-sum-of-1d-array-f983c068c36b>

![](img/e3f8ecce1481e9c6177020be3fb9f054.png)

照片由[纳丁·沙巴纳](https://unsplash.com/@nadineshaabana?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 问题

给定一个数组`nums`。我们将数组的运行总和定义为`runningSum[i] = sum(nums[0]…nums[i])`。

归还`nums`的流水账。

## 例子

*   示例 1:

```
**Input:** nums = [1,2,3,4]
**Output:** [1,3,6,10]
**Explanation:** Running sum is obtained as follows: [1, 1+2, 1+2+3, 1+2+3+4].
```

*   示例 2:

```
**Input:** nums = [1,1,1,1,1]
**Output:** [1,2,3,4,5]
**Explanation:** Running sum is obtained as follows: [1, 1+1, 1+1+1, 1+1+1+1, 1+1+1+1+1].
```

*   示例 3:

```
**Input:** nums = [3,1,2,10,1]
**Output:** [3,4,6,16,17]
```

# 解决办法

```
class Solution:
    def runningSum(self, nums: List[int]) -> List[int]:
        if len(nums) == 1:
            return nums

        result = []
        for i in range(len(nums)):
            result.append(sum(nums[:i+1]))

        return result
```

## 时间和空间复杂性

*   **时间复杂度** : O(n)。给定 n 是`nums`中元素的个数。
*   **空间复杂度** : O(n)如果考虑输出的话。O(1)如果我们不考虑输出。

# 外卖食品

感谢您阅读这个简短的解题问题。如果有人知道更好或更快的时间复杂度来解决这个问题，请随意评论和反馈。和平！✌️