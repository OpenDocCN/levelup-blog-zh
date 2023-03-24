# 按值传递与按引用传递

> 原文：<https://levelup.gitconnected.com/pass-by-value-vs-pass-by-reference-f9dc67a39b66>

![](img/35644814e1e1961c810568a126207d17.png)

只是我对“通过引用传递”这个术语的一些想法:

## 只有传值！

通过引用传递导致混乱，它总是通过值传递。

当您将对象作为参数传递给方法时，对象的引用是通过值传递的，因此在方法中，您有一个引用的副本。

这就是为什么您可以在方法内将对象设置为 null，而在方法外它不会有任何影响，因为引用的副本被赋给 null，而不是实际的引用。

然而，当您访问该对象的属性时，您将查找该引用，获取实际的对象并更改将在方法之外保持的值。

即使使用了 ref 或 out 参数，您仍然要将引用的副本传递给该引用。

![](img/7669eed4c3b05df8801ca2ef6c87568d.png)

奥利弗·布赫曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片