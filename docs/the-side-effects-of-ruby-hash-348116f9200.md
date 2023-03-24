# 在 Ruby Hash 中避免无声的失败和混乱

> 原文：<https://levelup.gitconnected.com/the-side-effects-of-ruby-hash-348116f9200>

## 最佳实践

## 如何享受 Ruby Hash 的强大功能而不损害可读性和可维护性

![](img/3802cc78a2aa82a6600dea1812b84af1.png)

照片由 [NeONBRAND](https://unsplash.com/@neonbrand?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/confusion?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

你有没有在`options[:name]`(符号键)和`options['name']`(字符串键)之间弄错，而你的代码因为错误的键类型而以`nil`值默默失效？

你是否曾经看到一个**散列**并且想知道那个散列中可能有什么属性？

**Ruby Hash** 是 Ruby 编程中最常用的元素之一。它因其强大、方便和灵活而广受欢迎。然而，如果没有谨慎的用法和纪律，带有散列的 Ruby 代码可能成为维护的噩梦。

# Ruby Hash 的用法

Ruby Hash 有两种常见的用法:

*   作为数据结构— ***好人***
*   作为数据对象— ***潜在的坏人***

## 作为数据结构的 Ruby Hash 好人

使用 Ruby Hash 作为**哈希映射**。

例如，提供了用户对象的阵列。**重复**高效检索用户 id 的一个简单方法是先将数组转换成一个 **Hash Map** ，键为 ***user_id*** ，值为 ***用户对象*** 。

这种用法是**最安全的**，因为这样的散列通常在同一个文件中创建和使用。即使这些散列是在不同的文件中创建和使用的，记录键和值也相对简单。

**键值文档**可以通过以下方式完成

*   变量和方法命名约定

```
id_user_map # indicates id is key, and user is value
```

*   或者使用代码注释作为文档

```
# Hash<String, User>
user_map
```

## Ruby Hash 作为数据对象——坏人

当你想用一种便捷的方式来"*"暂时保存你的数据而不创建显式的类时，也可以使用 Ruby Hash。*

*Ruby Hash 的键可以是多种数据类型:字符串、符号、数字、散列(是的，散列)、…*

*其中，**字符串**和**符号**最常用。*

***从 JSON** 解析**时，通常使用带字符串键**的 Hash。*

*当直接初始化时，通常使用带有符号键的**散列，因为符号键**比类型**短，而**更节省内存**。***

*这些原因使得这两种类型的散列键遍布整个代码库。*

# *Ruby Hash 作为数据对象的问题*

## *1.令人困惑的密钥类型*

*![](img/44225991f6ec6d4b10d0e3e24ce462cc.png)*

*罗伯特·鲁杰罗在 [Unsplash](https://unsplash.com/s/photos/confusion?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片*

*`options[:name]`还是`options['name']`？*

*如果一个现有的散列正在使用哪种类型的键(**字符串**对**符号**)的话，这很容易混淆和出错*

*   *你不是作家*
*   *你是一个月前的作家*

## *2.由于检索丢失的键返回 nil 而导致的静默失败*

*如果 ***键不存在*** ，则检索将返回 ***nil*** ，这看起来与检索具有 ***nil*** 值的 ***有效键*** 相同。这会导致**无声故障**。*

*这可能发生在以下情况*

*   *使用了**错误的键类型**(字符串对符号)*
*   ***错别字***

## *3.很难知道哈希实例中可能的键*

*你有没有见过这种代码，不需要阅读数百行代码就想知道“***video _ config***”hash 里面可能有什么？*

# *如何避免/解决麻烦*

## *1.使用 HashWithIndifferentAccess*

*[active support::hashwithindifferential access](https://api.rubyonrails.org/v4.2/classes/ActiveSupport/HashWithIndifferentAccess.html)是众所周知的解决这个键类型问题的方法。最常用的用法是[action controller::Parameters](https://api.rubyonrails.org/classes/ActionController/Parameters.html)。*

*但是，请注意，在代码方面**和性能方面**保持将 Hash 转换为多个层中的***active support::hashwithindifferential access***可能会变得**昂贵*******

## *****2.避免跨文件传递哈希实例*****

*****所有上述副作用都可以通过在相同的文件中构造和使用散列数据对象来避免(假定文件大小是合理的)。*****

## *****3.尽可能记录*****

*****这种方法为关键的期望问题提供了一个快速解决方案。这在大多数情况下是可行的。*****

*****然而，请注意，当散列中的键的数量增加时，这将很快成为一个噩梦。*****

*****当散列通过多层抽象时，这将很快成为**更糟糕的噩梦**。*****

## *****4.请改用 Ruby 对象*****

*****如果散列的所有属性总是在一起(即紧密耦合)，建议创建一个定义良好的对象类。这将有助于*****

*   *****允许解释器在检索过程中捕捉错别字*****

*   *****使文档更容易*****

## *****5.编写测试*****

*****编写测试总是选项之一。这也是确保代码工作的首要选择。*****

*****规范中使用的散列的例子也可以作为文档。*****

# *****结论*****

*****Ruby Hash 功能强大，使用方便。选择正确的使用位置将允许您在不牺牲可读性和可维护性的情况下利用这种能力。*****

*****感谢阅读。你对这篇文章中描述的问题和方法有什么看法？我错过了什么吗？请在评论区告诉我。*****