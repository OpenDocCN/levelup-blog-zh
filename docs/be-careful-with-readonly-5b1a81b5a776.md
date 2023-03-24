# 小心只读

> 原文：<https://levelup.gitconnected.com/be-careful-with-readonly-5b1a81b5a776>

![](img/221cb0e5327ee5908b92d57246a60178.png)

弗兰克·辛斯利在 Unsplash[上的照片](https://unsplash.com/)

当引用类型字段被标记为 readonly 时，只有指针(内存地址)是只读的，而不是指针所引用的对象。

这意味着如果您将数组标记为 readonly，您仍然可以更改数组内容。

看看这段代码:

上面的代码编译和运行良好。

如果您希望数组内容也是只读的，那么您需要将数组包装在一个`ReadOnlyCollection<T>`中: