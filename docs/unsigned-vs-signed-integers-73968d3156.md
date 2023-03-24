# 无符号整数与有符号整数

> 原文：<https://levelup.gitconnected.com/unsigned-vs-signed-integers-73968d3156>

![](img/00f2d603384ce6224c5875a2718ba1b7.png)

罗伯特 f .在 [Unsplash](https://unsplash.com/) 上的照片

下面的 C 代码会输出什么？

## **输出:x 大于**

## 为什么？

因为 x 是有符号整数，所以 y 是无符号整数。当计算机比较两者时，x 被提升为无符号整数类型。
当 x 提升为无符号整数时，-2 变成 65534，肯定大于 10。
为什么会转换成 65534？去[这里](http://reinventingthewheel.azurewebsites.net/TwosComplementTut.aspx#top)了解一下。
这种有符号/无符号数的互换会导致安全漏洞，如这里的[所述](http://stackoverflow.com/questions/3259413/should-you-always-use-int-for-numbers-in-c-even-if-they-are-non-negative#3261019)。

请注意，C#中的等效代码会产生“y 更大”的直观结果:

这是因为 uint 和 int 在比较之前很久就被提升为。

你可以在 [MSDN](http://msdn.microsoft.com/en-us/library/aa691330%28v=vs.71%29.aspx) 网站上了解更多关于这一点以及其他二进制数字促销的规则。