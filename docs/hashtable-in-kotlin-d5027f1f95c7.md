# Kotlin 中的哈希表

> 原文：<https://levelup.gitconnected.com/hashtable-in-kotlin-d5027f1f95c7>

![](img/78e6d69fe7c56ccf9ce992ef10330bac.png)

数据结构在任何类型的软件开发中都是重要的主题，但是围绕它们的大多数解释都是基于 Java 的。Android 开发的优势在于它最初是用 Java 完成的，但在最近几年，Kotlin 已经成为开发的首选语言。由于这个原因，理解并把经典和广泛的 Java 知识应用到现代编程语言中是很重要的。这一点很重要，原因有很多，其中我们可以提一下:了解数据结构为我们提供了强大的编程基础，使编码更容易，帮助我们优化执行时间和内存，而且它们通常会在技术面试中被问到！

# 基础

如上所述，Kotlin 基于 Java 的数据结构。Java 本身的大量版本中有一个对[散列表](https://developer.android.com/reference/kotlin/java/util/Hashtable)的实现，还有一个对[散列表](https://developer.android.com/reference/kotlin/java/util/HashMap)的实现。另一方面，科特林决定使用[散列表](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-hash-map/index.html)，因为它们大致相当。

> HashMap 类大致相当于 Hashtable，除了它是不同步的并且允许空值。
> 
> [*Android HashMap 文档*](https://developer.android.com/reference/kotlin/java/util/HashMap)

需要注意的重要一点是，这个 HashMap 类有一个恒定的时间性能，在 O-notation 中称为 O(1)，但在最坏的情况下，它可能达到 O(N)。尽管值得注意的是，一些哈希映射的实现使用了自平衡二分搜索法树，并且在最坏的情况下是 O(log N ),但是 Kotlin 实现不是这种情况。

# KVP:键值对

术语“键-值对”经常在软件开发中使用，但是由于有如此多的首字母缩略词，知道 KVP 在上下文中提到这个术语的大多数时间是很重要的。简而言之，它表示您有两个关联的对象，一个表示键，另一个表示关联的值。这给出了这种数据结构的一个非常重要的优点:当你有一个键和一个值时，没有必要在内存中的特定位置存储条目，因为只要你引用这个键，你就会得到这个值。从最后一点得出，同样重要的是要知道项目永远不会按顺序排列。

为了使上面的内容更清楚，比较 HashMap 数据结构和数组是很重要的。如果您了解这种数据结构，您将知道项目是基于零索引存储的，这意味着每个项目将由一个从零开始的数值表示。在数组的情况下，索引号就是键，它们将按顺序存储在内存中，否则，键每次都会给你一个不同的值。

# 表现

Kotlin 表示 HashMap 的方式非常简单:

```
*HashMap<key, value>* or *HashMap<K, V>*
```

# 构造器

在 Kotlin HashMap 中有 4 个构造函数:

```
HashMap() 
HashMap(initialCapacity: Int, loadFactor: Float) HashMap(initialCapacity: Int) 
HashMap(original: Map <out K, V> )
```

对于第一个，不需要添加任何东西。只要一个空的构造函数，你就有了你的地图，kvp 可以在以后添加。

第二个和第三个有两个参数:

1.  initialCapacity:如果你知道从一开始你需要多少物品，你可以指定。
2.  loadFactor:它定义了您希望散列表增长的速率。如果您从 4 个项目开始，加载因子 0.50 将意味着一旦这 4 个项目中的 2 个被填满，大小将增加。

对于最后一个，您从地图创建了一个散列表，因此您可以对它进行操作。

# HashMap 示例

假设你拥有一家黑胶店(我知道！老歌！)，而客户很难找到书名，所以你给每个书名分配一个编号，从编号最低到最高排序。然后创建一个散列表来搜索它们。代码将如下所示:

```
package com.evanamargain.hashmap fun main() { 
    val records = HashMap<String, Int>()     records["Michael Jackson"] = 30 
    records["The Beatles"] = 20       
    records["Rolling Stones"] = 70     for ((k, v) in items) { 
        println("$k = $v") 
    } 
}
```

# 用 HashMaps 可以解决什么样的算法问题？

1.  知道一个数组是否是另一个数组的子集
2.  寻找具有某种特征的最大子阵列
3.  对数字对求和

正如您所看到的，HashMaps 是 Kotlin 中简单而有用的开发工具，如果您已经是一名开发人员，您很可能会想到过去曾经使用过几次。否则，你现在就可以开始做了。

如果你喜欢这个帖子的内容，请跟我来。访问我的网页[evanamargain.com](http://evanamargain.com)或者更具体地访问我的[博客](http://evanamargain.com/blog)。你也可以[给我买杯咖啡](https://www.buymeacoffee.com/evana)支持我！

![](img/82745c463c9f3fc381e9a13cdee82722.png)

[https://www.buymeacoffee.com/evana](https://www.buymeacoffee.com/evana)

下次见！

埃娃娜·马尔甘·普伊格

如果您需要任何有关移动应用程序的服务，请访问我的网页， [evisoft.mx](http://evisoft.mx) 或给我留言 contacto@evisoft.mx。

*原载于 2020 年 1 月 31 日 http://www.evanamargain.com*[](http://www.evanamargain.com/blog/android/hashtable-in-kotlin/)**。**