# 反应式编程只是类固醇上的函数式编程吗？

> 原文：<https://levelup.gitconnected.com/is-reactive-programming-just-functional-programming-on-steroids-a34d9fbe0328>

动手重构以命令式风格编写的代码片段，逐步添加功能性和反应性特性。

![](img/a2c61b9ca256329fe87ceb7551abc92b.png)

西蒙·佩莱格里尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在本文中，我们将查看一段以命令式风格编写的简单代码，并尽最大努力以函数方式重构它。

之后，我们将使用 Java 的 Reactive Streams 规范再次尝试重构它。

## 1.命令式风格

出于演示的目的，我们将保持一切简单。我们的目标是验证一个*订单*请求的各个方面，并返回一个*失败*的列表。

我们将使用 *ProductRepository* 和 *CustomerService* 组件来检索这些验证所需的数据。我们可以想象这些信息来自不同的服务或数据库。

## **2。将结果包装到一个单独的类中**

首先，让我们将失败列表包装成一个类。因为我们试图遵循函数式编程风格，所以让它成为不可变的。

![](img/e47dd04e06dbf3a455b1456ece7c23a0.png)

此外，我们将添加一个 *merge* 方法，将两个不同的验证合并在一起。因为这个类是不可变的，所以这个方法将返回一个新的对象，而不会改变最初的对象:

此外，我们添加了工厂方法来创建一个成功的验证*(没有失败)*和一个失败的验证*(具有动态数量的原因)*。

## 3.提取验证器

现在，我们可以将*产品*和*客户*验证提取到单独的方法或类中，这将返回新的*或*验证结果:

让我们对产品列表做同样的事情——如果至少有一个产品不可用，我们应该返回 *OrderValidationFailure。产品 _ 不可用:*

## 4.功能风格

现在，让我们使用 Java 的功能特性和新的验证器来重构初始代码片段。

我们可以注意到 *customerService* 正在返回一个*可选值:*

*   如果值存在，我们验证它并返回验证器响应
*   否则，我们返回失败的验证结果，原因为 *CUSTOMER_NOT_FOUND*

最后，我们合并两个验证的结果，收集所有可能的失败。

## 5.反应流

现在，让我们将[反应流](http://www.reactive-streams.org/)加入混合。

我们有各种方法来获取产品清单作为*通量< >* 和客户作为*单声道< >* 对象。

*   如果我们正在进行 HTTP 调用，我们可以简单地开始使用 Spring 5 的 WebClient。
*   对于数据库操作，我们可以尝试使用反应式存储库
*   对于其他操作，我们可以简单地从一个 *CompletableFuture:* 创建一个*单声道*

## 6.反应式风格

最后，让我们使用这些反应流来并行执行两个验证并优化流程:

## 7.摘要

我们可以注意到 Java 的功能特性之间的相似性，比如流和可选的和反应式的流。

他们都鼓励不变性，并提供懒惰的评估。

此外，将各种验证失败视为流程的一部分，而不是异常情况，这允许我们将方法链接起来并继续流程。

这些都会让我们产生疑问……*反应式编程只是类固醇上的函数式编程吗？*

![](img/ae729d2368ae43ce23fdfcba6c4d4451.png)

## 谢谢大家！

感谢你阅读这篇文章，请让我知道你的想法！欢迎任何反馈。

如果你想阅读更多关于干净的代码、设计、单元测试、函数式编程以及许多其他内容，请务必查看我的其他文章。

如果你喜欢我的内容，可以考虑[关注或订阅](https://medium.com/@emanueltrandafir)邮件列表。

最后，如果你考虑成为一个中等会员，支持我的博客，这里是我的[推荐人](https://medium.com/@emanueltrandafir/membership)。

编码快乐！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**将像你这样的开发人员安置在顶级创业公司和科技公司**](https://jobs.levelup.dev/talent/welcome?referral=true)