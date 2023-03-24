# 小心绳子。包含()

> 原文：<https://levelup.gitconnected.com/be-careful-with-string-contains-ffcf4067f3f3>

![](img/6e095678a26e57f966fed16f58df5880.png)

塞缪尔·拉莫斯在 [Unsplash](https://unsplash.com/s/photos/string) 上拍摄的照片

前几天，我的代码出现了一些意想不到的结果。

归结起来就是这个(简化版):

## 要求/提供的信息

1.  我必须在两个集合之间找到匹配的 id。
2.  id 是三位数长。
3.  `otherIds`集合中的 ID 以“ID”为前缀。
4.  对于`otherIds`集合，在`ids`集合中有零个或一个匹配。

## 问题是

我在消耗这些代码的代码中不断得到异常，我不知道为什么，我写的代码看起来如此简单。

**转出** `**"some string".Contains(“”)**` **将返回** `**true**` **。**

这意味着我每次都得到不止一个匹配，这让我很头疼。

## 解决方案

我不得不将我的`Where()`条款改为:

`otherIds.Where(x => !string.IsNullOrEmpty(id) && x.Contains(id));`

## 吸取的教训

1.  永远不要假设你知道标准方法是如何工作的！即使是你一直使用的那些，在给定的边缘情况输入下也会表现得很奇怪。举个很好的例子，你认为`(int)4.8`和`Convert.ToInt32(4.8)`返回值一样吗？
2.  **总是写单元测试！如果我没有忘记写一个单元测试的话，这个问题可能早就被发现了。**