# 排序和哈希表 Python

> 原文：<https://levelup.gitconnected.com/sorting-and-hash-tables-python-12c12dd9c8fb>

理解如何在 Python 中使用哈希表的指南。

![](img/34d314e3cff68f010cd2816c410d6549.png)

凯利·西克玛在 [Unsplash](https://unsplash.com/s/photos/sorting?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 整理

*   它是数组中项目的重新排列。
*   排序后的[13，5，2，9]-->[2，5，9，13]。
*   最坏情况下的大多数排序算法的时间复杂度为 O(n log n)。

# 例子

*   我们举个例子来理解排序的时间复杂度。
*   **问:查找数组[4，6，1，4，8]中是否有重复的。**

## 方法 1:强力方法

在使用强力方法时，对于数组的每个元素，它会遍历所有其他元素来检查是否存在重复。这方面的伪代码可以编写如下:

```
for i in arr:
    for j in arr!=i:
        check if i=j
```

使用了嵌套循环，因此时间复杂度为 O(n)。但是这不是时间有效的，因此需要更好的方法，这将进一步讨论。

## 方法 2:排序方法

在这种方法中，数组在找到重复之前被排序。排序完成后，我们遍历数组，检查每个元素是否相等。伪代码可以写成:

```
for i=0 to len(arr)-1:
    check if arr[i+1]=arr[i]
```

这里只使用了一个循环，所以我们有 O(n)。但是考虑到排序算法，我们有 O(n log n)。所以总的时间复杂度是 O(n) + O(n log n) = O(n log n)。但是时间复杂度仍然可以改进，我们将看到散列表如何帮助它。

# 哈希表

*   哈希表是一种键值存储。
*   当一些东西需要存储在哈希表中时，它应该有一个键和值。
*   密钥可以是字符串、数字或对象。
*   这对于搜索非常有用，因为查找需要 O(1)时间。
*   缺点之一就是占用了大量的空间和内存。
*   需要注意的一点是，这些键不是按任何顺序存储的。

## 方法 3:哈希表方法

可以创建一个空哈希表。然后我们遍历数组，检查元素是否在哈希表中。如果不是，那么我们将元素作为键添加到哈希表中，同时添加一个占位符作为它的值。如果我们遇到哈希表中的某个元素，就意味着我们找到了重复的元素。

在这种情况下，时间复杂度是 O(n ),因为我们最多迭代到数组的末尾。当使用哈希表时，时间复杂度降低了，但是需要权衡存储哈希表的空间。

# 问题:给定一个整数数组，找出一对总和为数字“X”的整数。

A = [4，6，1，7，9]

目标= 5

结果= [4，1]

输出应该是什么形式——返回一对数字

在多对的情况下做什么—返回任何一对

如果没有要返回的对，该怎么办—返回 null

## 方法一:蛮力

我们遍历数组中的每一对。伪代码如下:

```
for i=0 to len(arr)-1:
    for j=i+1 to len(arr):
        if arr[i]+arr[j] == target:
            return pair
no pair found, return null
```

时间复杂度为 O(n ),因为使用了两个循环，而空间复杂度为 O(1)。Python 代码如下:

```
def two_sum_brute(a,target):
    for i in range(len(a)-1):
        for j in range(i+1,len(a)):
            if a[i]+a[j]==target:
                return([a[i],a[j]])
    return None
```

## 方法 2:哈希表

我们将遍历数组，如果我们在 a[i]处，我们将检查 target-a[i]是否在哈希表中，如果是，我们返回该对。伪代码如下:

```
initialize empty hash table
for i=0 to len(arr):
    if target-arr[i] in hash table:
        return pair
    else:
        add arr[i] to hash table
no pair found, return null
```

时间复杂度为 O(n)，空间复杂度为 O(n)。Python 代码如下:

```
def two_sum_hash(a,target):
    d = {}
    for i in range(len(a)):
        if target-a[i] in d.keys():
            return([a[i],target-a[i]])
        else:
            d[a[i]] = 'placeholder'
    return None
```

> *这里指代号*[](https://github.com/jayashree8/Technical_Interview_Prep/blob/main/Sorting%20and%20hash%20table/TwoSum.py)**。**
> 
> *点击查看 GitHub 回购[。](https://github.com/jayashree8/Technical_Interview_Prep)*

> **联系我:*[*LinkedIn*](https://www.linkedin.com/in/jayashree-domala8/)*
> 
> **查看我的其他作品:* [*GitHub*](https://github.com/jayashree8)*