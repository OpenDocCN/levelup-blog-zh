# 一步到位地反转链表

> 原文：<https://levelup.gitconnected.com/reversing-a-linked-list-in-one-pass-step-by-step-4a6113d8b3bb>

![](img/904e6b44463458ceb3af885f4491a89c.png)

约书亚·阿拉贡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

链表是软件工程面试中非常常见的话题。尽管它们可能会令人生畏，并且有一点学习曲线，但是你越是熟悉它们，它们很快就会成为你的第二天性。

链表中最常见的问题是反转一个单链表。让我们把这个问题一步步分解。我将在本文中讨论“一次通过”迭代解决方案。这些都将在 JavaScript 中显示，但是跨语言的概念保持不变。

# 概观

对于一个单链表，为了反转它，我们需要把所有的指针都翻转过来，指向相反的方向

示例:

1 → 2 → 3 → 4 → 5

现在变成了:

1 ← 2 ← 3 ← 4 ← 5

这与以下内容相同:

5 → 4 → 3 → 2→ 1

就是这样！

# 解决办法

首先，我们需要定义我们将在链表的遍历中使用的指针。

我们的“当前”节点将是列表的头。这个会给你。

当我们遍历列表时，我们需要跟踪当前节点之前的节点(这样我们就可以在反转过程中改变指向它的指针)，我们称之为‘prev’。头之前什么都没有，所以我们现在将它设置为 null。

我们还希望存储下一个节点，这样我们就可以轻松地访问它。我们将在遍历中设置“下一个”指针。现在，将其设置为 null。

```
let current = head
let prev = null
let next = null
```

现在我们已经为遍历做好了准备。我们将使用一个简单的 while 循环。一旦我们的“当前”指针位于列表的末尾，我们将停止 while 循环。我们知道，当“当前”指针试图将自己设置为一个不存在的节点，也就是“空”时，我们到达了列表的末尾。

```
let prev = null
let current = head
let next = nullwhile (current !== null) { }
```

我们需要做的下一件事是将下一个节点保存在我们之前定义的“next”变量中，以便我们可以在以后访问它。这允许我们在进入下一个循环时继续遍历列表中的下一个节点。我们通过定位“当前”节点上的“下一个”属性来访问列表中的下一个节点。

```
let prev = null
let current = head
let next = nullwhile (current !== null) {
    next = current.next}
```

我们现在要改变指针来查看前一个节点，而不是下一个节点。为此，我们将 current.next 设置为指向“prev”节点。这就是反转发生的地方！

```
let prev = null
let current = head
let next = nullwhile (current !== null) {
    next = current.next
    current.next = prev}
```

现在已经完成了，我们需要向前移动“当前”和“上一个”节点指针，为下一次迭代做准备。“prev”后面的节点是“current ”,因此我们可以将 prev 设置为 current。

```
let prev = null
let current = head
let next = nullwhile (current !== null) {
    next = current.next
    current.next = prev
    prev = current}
```

还好我们早一点保存了下一个节点！因为我们不能仅仅做一个简单的“current = current.next ”,因为我们做了反转。那只会把我们指向前一个节点。我们会被困住了！好了，让我们将当前节点设置为我们保存的“下一个”节点。

```
let prev = null
let current = head
let next = nullwhile (current !== null) {
    next = current.next
    current.next = prev
    prev = current
    current = next
}
```

厉害！我们完成了循环！现在只需返回“当前”对吗？不，记住' current '在循环结束时是 null，所以我们需要返回它之前的内容，也就是' prev '。

```
let prev = null
let current = head
let next = nullwhile (current !== null) {
    next = current.next
    current.next = prev
    prev = current
    current = next
}
return prev
```

现在我们完成了！

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)