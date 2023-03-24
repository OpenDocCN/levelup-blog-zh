# 用 Python 写的数据结构及其各自的 LeetCode 问题

> 原文：<https://levelup.gitconnected.com/the-most-common-data-structures-written-in-python-b63f864e71c>

## 包括完整的代码片段(GitHub Gists)

![](img/8e3ea3d4e6d13d28c0276e9d5c75a344.png)

由[罗曼·辛克维奇](https://unsplash.com/@synkevych?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

所有代码片段都是可运行的(也就是说，您可以复制并粘贴它，它将是可运行的)。如果你发现任何错误，请在评论中告诉我！

所有片段还描述了每个操作及其对应的渐近运行时(即大 O)。所有代码片段都是我自己创作的。

# 目录

1.  **堆栈**
2.  **队列**
3.  **堆**
4.  **链表**
5.  **树(BST)**
6.  **树(Trie)**

# 大量

```
**Operation:** push(add element to the stack)
**Time Complexity**: O(1)**Operation:** pop(remove from stack)
**Time Complexity**: O(1)**Operation:** peek(return first/topmost item in stack)
**Time Complexity**: O(1)**Operation:** clear(removes all elements in stack)
**Time Complexity**: O(1)
```

# 行列

区分单端队列和双端队列非常重要。

## **单端队列**

```
**Operation:** enqueue(add an element to the start of queue)
**Time Complexity**: O(n)**Operation:** dequeue(remove an element from the queue)
**Time Complexity**: O(1)
```

## 双头队列

```
**Operation:** append(add element to the right of queue)
**Time Complexity**: O(1)**Operation:** appendleft(add element to the left of queue)
**Time Complexity**: O(n)**Operation:** pop (removes rightmost element in queue)
**Time Complexity**: O(1)**Operation:** popleft(removes leftmost element in queue)
**Time Complexity**: O(n)**Operation:** clear(removes all elements in queue)
**Time Complexity**: O(1)
```

# 很

与最大堆相比，最小堆需要做很小的改动。请注意下面代码片段的不同之处。

## **最大堆数**

```
**Operation:** push(adds a new element to the heap)
**Time Complexity**: O(log n)**Operation:** peek(returns max element in heap)
**Time Complexity**: O(1)**Operation:** pop(removes max element from heap)
**Time Complexity**: O(log n)
```

## **最小堆数**

```
**Operation:** push(adds a new element to the heap)
**Time Complexity**: O(log n)**Operation:** peek(returns max element in heap)
**Time Complexity**: O(1)**Operation:** pop(removes max element from heap)
**Time Complexity**: O(log n)
```

# 链接列表

下面所有的链表数据结构都将使用下面代码片段中的`Node`类。一个`Node`跟踪单个元素。

## 与链表相关的 LeetCode 问题

*   [从排序列表中删除重复项](https://leetcode.com/problems/remove-duplicates-from-sorted-list/)
*   [两个链表的交集](https://leetcode.com/problems/intersection-of-two-linked-lists/)
*   [链表循环](https://leetcode.com/problems/linked-list-cycle/)
*   [反向链表](https://leetcode.com/problems/reverse-linked-list/)
*   [链表中间](https://leetcode.com/problems/middle-of-the-linked-list/)
*   [移除链表元素](https://leetcode.com/problems/remove-linked-list-elements/)
*   [回文链表](https://leetcode.com/problems/palindrome-linked-list/)
*   [合并两个排序后的链表](https://leetcode.com/problems/merge-two-sorted-lists/)

## **单链表**

```
**Operation:** add (insert element to Linked List)
**Time Complexity**: O(1)**Operation:** find(returns element from Linked List)
**Time Complexity**: O(n)**Operation:** remove(removes max element from Linked List)
**Time Complexity**: O(1)
```

## **循环链表**

**循环**和**单链表**的区别:

1.  链表末尾的节点没有`None`作为`next_node`。相反，它指回根节点。
2.  由于这种循环，我们修改:

*   `insert`:我们不是在根处插入，而是作为第二个**元素插入到链表中，即根的 next_node。**
*   `find`:我们需要知道什么时候我们已经循环了一次链表，否则我们将会陷入一个无限循环
*   `remove`:类似于单链表，除了在根节点中找到值的场景中，我们需要将最后一个节点的 next_node 更新为新的根节点

```
**Operation:** add (insert element toLinked List)
**Time Complexity**: O(1)**Operation:** find(returns element from Linked List)
**Time Complexity**: O(n)**Operation:** remove(removes element from Linked List)
**Time Complexity**: O(1)
```

## **双向链表**

**双**和**单**链表的区别:

1.  `Node`类具有附加的`prev_node`属性(双向)
2.  `DoubleLinkedList`类跟踪作为属性的`last`节点

```
**Operation:** add (insert element to Linked List)
**Time Complexity**: O(1)**Operation:** find(returns element from Linked List)
**Time Complexity**: O(n)**Operation:** remove(remove element from Linked List)
**Time Complexity**: O(1)
```

# 树

## 术语:

1.  **节点/顶点**——树中的每个元素被称为一个节点或顶点
2.  **边**—*边*连接两个节点
3.  **根**——最顶端的节点
4.  **祖先** —所述节点之上的所有节点
5.  **后代** —所述节点下的所有节点
6.  **父节点** —问题节点的正上方节点
7.  **子节点** —直接位于相关节点下的节点
8.  **同级** —具有相同父节点的节点
9.  **叶** —没有子节点的节点

## 树的类型

1.  二叉树—具有两个子节点的树
2.  二叉查找树—类似于(1)，除了每个节点大于其左子树**中的每个 ***节点和其右子树中的每个*** 节点和**节点小于 ***节点***

## 与树木相关的 LeetCode 问题

*   [二叉树的最大深度](https://leetcode.com/problems/maximum-depth-of-binary-tree/)
*   [二叉树的最小深度](https://leetcode.com/problems/minimum-depth-of-binary-tree/)
*   [对称树](https://leetcode.com/problems/symmetric-tree/)
*   [反转二叉树](https://leetcode.com/problems/invert-binary-tree/)
*   [在二叉查找树搜索](https://leetcode.com/problems/search-in-a-binary-search-tree/)
*   [合并两棵二叉树](https://leetcode.com/problems/merge-two-binary-trees/)
*   [另一棵树的子树](https://leetcode.com/problems/subtree-of-another-tree)
*   [二叉查找树的最低共同祖先](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

## 二分搜索法树

```
**Operation:** add (add an element to the BST)
**Time Complexity**: O(log n)**Operation:** find(find an element in the BST)
**Time Complexity**: O(log n)**Operation:** remove(removes an element from the BST)
**Time Complexity**: O(log n)
```

## 努力

```
**Operation:** add_word (add word to Trie)
**Time Complexity**: O(n) where n is length of word**Operation:** does_word_exist(boolean to check if word is in Trie)
**Time Complexity**: O(n) where n is length of word
```

## 与尝试相关的 LeetCode 问题

*   [实现 Trie(前缀树)](https://leetcode.com/problems/implement-trie-prefix-tree/)
*   [设计添加和查找单词的数据结构](https://leetcode.com/problems/design-add-and-search-words-data-structure/)

## 结束语

暂时就这样吧！让我知道你还想看什么样的数据结构。

如果你喜欢我的内容并且没有订阅 Medium，请考虑支持我并通过我的推荐链接订阅。