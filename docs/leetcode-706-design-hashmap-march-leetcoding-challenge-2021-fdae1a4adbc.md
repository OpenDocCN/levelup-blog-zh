# Leetcode 706。设计散列表-2021 年 3 月电子编码挑战

> 原文：<https://levelup.gitconnected.com/leetcode-706-design-hashmap-march-leetcoding-challenge-2021-fdae1a4adbc>

今天，我们将解决三月电码挑战的第七个问题。

![](img/169c0146b29203ab528cff22c588172c.png)

# 问题陈述

不使用任何内置哈希表库来设计哈希表。

具体来说，您的设计应该包括以下功能:

*   `put(key, value)`:将一个(键，值)对插入到散列表中。如果值已经存在于 HashMap 中，则更新该值。
*   `get(key)`:返回指定键映射到的值，如果该映射不包含键的映射，则返回-1。
*   `remove(key)`:如果此映射包含键的映射，则删除值键的映射。

**示例:**

```
MyHashMap hashMap = new MyHashMap();
hashMap.put(1, 1);          
hashMap.put(2, 2);         
hashMap.get(1);            // returns 1
hashMap.get(3);            // returns -1 (not found)
hashMap.put(2, 1);          // update the existing value
hashMap.get(2);            // returns 1 
hashMap.remove(2);          // remove the mapping for 2
hashMap.get(2);            // returns -1 (not found)
```

**注:**

*   所有的键和值都在`[0, 1000000]`的范围内。
*   操作次数将在`[1, 10000]`的范围内。
*   请不要使用内置的 HashMap 库。

# 解决办法

所以在这个问题中，我们被要求在不使用任何库函数的情况下设计一个 HashMap。这里我们将讨论解决这个问题的两种方法。

**方法 1 —数组:**通过这种方法，我们将用-1 初始化一个特定大小的数组。我们将使用给定的`key`作为数组的索引，并将给定的`value`存储在具有该索引的数组中。我们可以使用这个方法，因为`key` 总是正的。这是一个非常简单的代码。

时间复杂度:O(1)，对于 get、put、remove 方法

空间复杂度:O(1)，数组将占用 1000001 个空间。

**方法 2 — LinkedList:** 之前的解决方案被接受，因为键值不是负数。但总的来说，事实并非如此。所以我们将尝试改进解决方案，找到散列索引值，并将其存储在数组中。要理解 HashMap 背后的核心概念及其内部实现，请查看这个令人惊叹的 youtube 播放列表。( [Youtube](https://www.youtube.com/watch?v=wWgIAphfn2U&list=PLqM7alHXFySGwXaessYMemAnITqlZdZVE&ab_channel=GeeksforGeeks) )。

因此，通过检查视频，我们可以理解，我们可能会为不同的键获得相同的哈希索引(称为冲突)。为了解决这个问题，我们将使用 LinkedList。

让我们把整个算法过一遍。

1.  创建一个`ListNode`类。它是 LinkedList 的节点。它存储`key`和`value`对。
2.  创建一个名为`nodes`的`ListNode`类型的数组。它基本上像`HashMap`一样工作。
3.  在构造函数中初始化数组。
4.  `put`方法:首先，找到给定`key`的哈希值。检查是否有其他值存储在该位置。如果没有，用`(-1,-1)`键-值对创建一个`ListNode`,并将其存储在`nodes`数组中。如果存在，则获取对节点`(prev)`的引用。如果`prev.next`为空，那么创建一个新的`ListNode`和`key-value`对。否则替换`prev.next`节点的值。
5.  `get`方法:重复查找哈希值并获取对`ListNode(prev)`的引用的过程。如果`prev.next`为`null`，则返回-1，否则返回`prev.next.value`。
6.  `remove`方法:重复查找哈希值并获取对`ListNode(prev)`的引用的过程。要从数组中移除`key-value`对，请执行`prev.next=prev.next.next`。(从 LinkedList 中删除节点)

下面给出了详细的代码和注释。

时间复杂度:平均为 O(1)，最坏情况下为 O(n)

空间复杂度:O(1)，条目数固定。

代码可以在这里找到。

[](https://github.com/sksaikia/LeetCode/tree/main/src/MarchLeetcodeChallenge2021) [## sksaikia/LeetCode

### 在 GitHub 上创建一个帐户，为 sksaikia/LeetCode 开发做贡献。

github.com](https://github.com/sksaikia/LeetCode/tree/main/src/MarchLeetcodeChallenge2021) 

查看 2021 年 3 月 LeetCoding 挑战赛之前的问题。

1.  [三月吃糖挑战——第一天——分发糖果](https://medium.com/dev-genius/march-leetcoding-challenge-2021-problem-1-distribute-candies-f37f66ea7ee9)
2.  [三月 LeetCoding 挑战赛—第二天—设置不匹配](https://sourav-saikia.medium.com/march-leetcoding-challenge-2021-day-2-set-mismatch-4abd5ee491c9)
3.  [三月电码挑战—第三天—缺少号码](https://sourav-saikia.medium.com/march-leetcoding-challenge-2021-day-3-missing-number-ae8ee45a58cb)
4.  [3 月 LeetCoding 挑战赛—第 4 天—两个链表的交集](https://sourav-saikia.medium.com/march-leetcoding-challenge-2021-day-4-intersection-of-two-linked-lists-a775449b5563)
5.  [三月 LeetCoding 挑战赛—第 5 天—二叉树平均水平](https://link.medium.com/sC9L595opeb)
6.  [三月电子编码挑战—第 6 天—单词的短编码](https://medium.com/leetcode-simplified/march-leetcoding-challenge-2021-day-6-short-encoding-of-words-7fed4bfae557)

# 分级编码

感谢您成为我们社区的一员！ [**订阅我们的 YouTube 频道**](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1) 或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)