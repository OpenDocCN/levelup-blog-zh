# 什么是递归数据结构？

> 原文：<https://levelup.gitconnected.com/what-is-a-recursive-data-structure-f5d910d96c0a>

在编程时，你可能听说过递归或递归函数。递归函数本质上是一个调用自身的函数。

但是什么是递归数据结构呢？在本文中，我将解释什么是递归数据结构，并给出例子。

![](img/3e0be02aea0cf2009c12a43b11dcd348.png)

[西蒙朱](https://unsplash.com/@smnzhu?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

如果你已经知道递归函数，你应该很容易理解递归数据结构。功能的本质是它被执行的能力。同样，数据结构的本质是它包含值的能力。

所以，递归函数是*执行自身*的函数。就像一个递归数据结构*包含它自己*。

递归数据结构的一个例子是**链表**。链表包含一个泛型类型的`value`变量和一个`Node`类型的`next`变量。

![](img/757e654e894770aff086641596b4e657.png)

照片由 [JJ 英](https://unsplash.com/@jjying?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

链表被认为是递归数据结构的原因是因为`next`变量包含一种类型的`Node`。在 Java 中，代码如下所示:

```
public class Node < T > {
    public T value;
    public Node < T > next;
}
```

如您所见，`Node`中的`next`变量本身就是类型`Node`。因此，链表是一种递归数据结构。

另一个例子是**树**。一棵树有`node`和`edges`。

![](img/3dd118fa7fa4e495004ce1e92a1e4eea.png)

Todd Quackenbush 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

与链表相反，树中的`node`包含一个通用值。`edges`包含树的子树也包含树。正因为如此，树也是一种递归的数据结构。

```
public class Tree < T > {
    public T node;
    public List < Tree < T >> edges;
}
```

在`Tree`中有一个列表，即`edges`包含另一个`Tree`。这使得树是递归的。

这个简单的解释对你来说清楚吗？如果没有，请在下面评论并告诉我。