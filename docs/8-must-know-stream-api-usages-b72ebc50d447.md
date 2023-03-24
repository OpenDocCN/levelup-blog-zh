# 8 个必须知道的流 API 用法

> 原文：<https://levelup.gitconnected.com/8-must-know-stream-api-usages-b72ebc50d447>

跟我一起用 stream API 写更优雅的代码吧。

![](img/e2f22bad4f735011596b4f0204c1ce91.png)

作为一名 java 开发工程师，数组和集合在我的代码中随处可见。我经常使用集合或数组来处理数据，如过滤和对象转换。相信大家对数组的数据处理已经非常熟悉了。但是当数据处理比较复杂的时候，我们的代码就会很长，很难理解。这个时候我一般会把代码拆分成方法，给有意义的方法名，可以解决我的一部分问题。不过在 java8 之后，有了官方更高效便捷的解决方案:Stream API。

流是 Java8 中处理集合的关键抽象。它可以指定要对集合执行的操作，并可以执行非常复杂的操作，如搜索、过滤和映射数据。使用流 API 操作集合数据类似于使用 SQL 执行数据库查询。也可以使用流 API 并行执行操作。简而言之，流 API 提供了一种高效且易于使用的数据处理方式。

该流具有以下特征。

*   它不是数据结构，也不保存数据。
*   懒求值，在流的中间处理过程中，只记录操作，不立即执行，直到执行终止操作，才会进行实际的计算。

今天我就分享一些工作中常用的，非常有用的 API。

## 要映射的列表

在我们的工作中，经常会遇到列表或数组转换成映射的场景。`Collectors.toMap`可以将列表转换成地图。代码显示如下:

类似的还有`Collectors.toList()`、`Collectors.toSet()`，意思是把对应的流转换成一个列表或者集合。

## 使用分组方式分组

执行聚合数据操作时经常使用分组，例如按国籍对用户进行分组。说到分组，相信大家都会想到 SQL group by。在 Java8 之前，我们可能需要这样做:

如果使用 groupingBy，只需要一行代码。

## 使用限制和跳过的内存分页

有时候我们在内存中聚合数据后，数据量可能太大，无法一次性将数据返回前端。这时候就需要分页了。Stream api 提供了 limit 和 skip 来完美地支持它。

## 使用 filter()过滤元素

从集合中筛选出不符合条件的元素。

## 使用排序+比较器进行排序

排序绝对是集合操作中的高频操作。虽然有现成的集合工具进行排序，但是 Stream api 集成了这个功能，形成了自己的功能生态，进一步提高了 api 的便利性。

## 使用 reduce 的聚合评估

stream api 还有一个非常有用的方法`reduce()`。使用 reduce 可以聚合集合中的所有元素以获得单个值。最典型的场景是数组求和。

当然，也可以使用 reduce 来执行其他的聚合操作，比如求一个数组的平均值。

## 使用映射转换类型

映射操作是重新处理流中的元素以形成新的流。这在开发中很有用，例如，我有一个用户集合，但我只需要使用用户的姓名和年龄，使用 map 方法可以做到这一点:

## 平面地图

flatMap 操作可以将多个相同类型的流组合成一个流。

## 最后

Stream API 以声明的方式处理集合数据，可以极大地提高 Java 程序员的生产率，让我们能够编写高效、干净、简洁的代码。希望大家都能掌握 stream api，写出优雅的代码。感谢阅读。

[](/how-to-draw-a-technical-architecture-diagram-2d2c3b4a1d07) [## 如何绘制技术架构图

### 什么是架构图？为什么要画架构图？怎样才能画出一个简单易懂的…

levelup.gitconnected.com](/how-to-draw-a-technical-architecture-diagram-2d2c3b4a1d07) [](/seven-intellij-debug-tricks-that-every-java-developer-must-know-de26aaac736a) [## 每个 Java 开发人员都必须知道的七个 Intellij 调试技巧

### 你知道这些把戏吗？这些一定会提高你的开发效率。

levelup.gitconnected.com](/seven-intellij-debug-tricks-that-every-java-developer-must-know-de26aaac736a) [](/dont-use-fileinputstream-to-transfer-files-5990805de28e) [## 不要使用 FileInputStream 传输文件

### 为了提高效率，请使用 FileChannel。

levelup.gitconnected.com](/dont-use-fileinputstream-to-transfer-files-5990805de28e) [](https://medium.com/javarevisited/five-api-performance-optimization-tricks-that-every-java-developer-must-know-75324ee1d244) [## 每个 Java 开发人员都必须知道的五个 API 性能优化技巧

### 为什么你的 API 响应这么慢？也许你需要解决这些问题。

medium.com](https://medium.com/javarevisited/five-api-performance-optimization-tricks-that-every-java-developer-must-know-75324ee1d244)