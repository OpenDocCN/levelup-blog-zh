# 哈希表是如何工作的？

> 原文：<https://levelup.gitconnected.com/implementing-a-hash-table-in-javascript-29aca1edfe2b>

![](img/19db0af98301bed54e12008ec55074ce.png)

照片由[皮斯特亨](https://unsplash.com/@pisitheng?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/dictionary?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

通过自己设计和建造来学习。教程

# 什么是哈希表？

数据结构用于存储可以在固定时间内访问的键值对(大多数情况下)。

## 为什么要使用哈希表？

哈希表可以用来解决需要跟踪不同变量而不需要显式编写它们的问题。

我们来举个例子。假设您有以下字符串，“abgcddcccfmlk”

现在假设要求您确保字符串中没有任何重复超过 3 次的字母。

## 你会怎么做？

天真的答案是使用**两个嵌套循环**将每个字母与其余的**逐个进行比较**，并确保该字母不会重复超过 3 次。

尽管这种解决方案可行，但它需要 **n** 次操作才能完成，其中 **n 等于数组的大小。**我们可以看到这种解决方案会变得非常低效。

现在假设我们有一个散列表来记录每个字母重复的次数。

## 我们的哈希表应该是这样的

> [答:1 ]

当我们遍历字符串时，我们将更新找到的所有值，并在遍历过程中给它们各自的计数加 1。在字符串的一次迭代之后，我们的哈希表在遍历数组中的最后一个“c”之前应该是这样的。

> [甲:1，乙:1，庚:1，丙:3，丁:2]

我们可以看到，如果我们给 ***c*** 加 1，它将变成 **4** ，从而使我们的字符串“无效”。我们可以简单地在这里停止并返回 false，而不查看数组的其余部分，因为我们知道我们的字符串已经无效，因为**“c”重复超过 3 次**。这最多需要 **n 次操作**，这比我们之前的解决方案好得多。

希望您现在能明白为什么散列表对这类问题如此有用，以及它所有可能的用例。

既然我们对哈希表在实践中是如何工作的有了更好的理解，让我们开始用 JavaScript/TypeScript 实现它。

> 注意:TypeScript 和 JavaScript 之间唯一的区别是变量类型的声明。在下面的代码片段中，代码是用 TypeScript 编写的，但是通过删除类型定义，可以很容易地将其转换成 javascript。

例如

```
**//in typescript**
var addOne(num: number): number {
     return num++;
}**//in javascript**
var addOne(num) {
     return num++;
}
```

不是说我们已经得到了我们的方式，让我们挖掘！

# 为我们的哈希表创建一个类

这里我们可以看到我们的类有两个属性。尺寸和桌子。我们的哈希表只是一个常规数组，大小是它的长度。稍后会详细介绍

# 决定哈希表的大小

对于特定的输入，散列函数应该总是返回相同的输出。我们的结果不应该是随机的。

这里我们遍历字符串，将每个字符转换成它的 ASCII 码* 100。之后，我们使用*模数运算符(%)* 使生成的 id 适合我们的范围(数组的大小)；

> 这意味着我们生成的任何 id，都将是一个从 0 到我们指定的大小值的数；

# 在哈希表中插入和检索值

为了插入一个值，我们简单地散列我们的键，并使用 **id** 作为索引将它的值保存在表中。

为了检索键值，我们可以简单地散列我们想要找到的键，并使用 **id** 作为索引来访问数组。

## 等等，如果两个不同的键产生相同的 id 会发生什么？

比如说我们的尺寸= 2；

很有可能我们的哈希函数会返回 1，或者 2。

因此，如果我们用键“cat”保存一个值，用键“dog”保存另一个值，我们的值可能会被覆盖，因为我们的数组中只有两个空格。

> 当散列两个不同的密钥产生相同的 id 时。我们称之为碰撞。

# 那么，我们该如何解决这个问题呢？

有各种方法可以解决这个问题，但是我们将使用一个**链表**来解决我们的“小”问题。

为了实现这一点，我们需要在构造函数中的数组的每个索引中压缩一个链表。我们还必须更新我们的 *insert()* 和 *get()* 方法，所以

[](https://medium.com/javascript-in-plain-english/implementing-a-linked-list-in-javascript-717d2ab5d9a9) [## 用 Javascript 实现一个链表

### 今天我们将创建我们自己的二叉查找树实现，但是在我们写一行代码之前，它…

medium.com](https://medium.com/javascript-in-plain-english/implementing-a-linked-list-in-javascript-717d2ab5d9a9) 

我们现在已经成功地实现了一个哈希表，它使用一个链表来处理冲突。

[](https://medium.com/everything-javascript/binary-search-tree-in-javascript-635cc8a3eecf) [## 了解二分搜索法树(第一部分)

### 到目前为止，我们已经介绍了链表和散列表，但是是时候通过学习二叉树来更进一步了…

medium.com](https://medium.com/everything-javascript/binary-search-tree-in-javascript-635cc8a3eecf) 

**这是最终结果**

## **以及用于该实现的链表**

注意:如果我们的哈希表太小或者哈希函数不够好，我们的哈希表可能会降低两个非常大的链表的性能，这将降低从一开始使用一个链表所获得的整体性能优势。

这意味着 O(1)的存取时间可以降低到 0(n)；

如果你喜欢这个教程，不要忘了关注我，将来会有更多关于数据结构和算法的内容。

今天到此为止。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)