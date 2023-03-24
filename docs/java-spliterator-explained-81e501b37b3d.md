# Java Spliterator 解释

> 原文：<https://levelup.gitconnected.com/java-spliterator-explained-81e501b37b3d>

## *迭代器+拆分= >拆分器*

![](img/548c0d98b2c965507a0ce39231167f94.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=820000) 的 [Republica](https://pixabay.com/users/republica-24347/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=820000)

Java 有多种类型的*遍历源的*元素。我的上一篇文章展示了如何使用`[java.util.Iterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html)`和`[java.util.ListIterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/ListIterator.html)`来遍历像集合这样的数据结构。

从 Java 1.2 开始就支持*迭代器*的概念，但是在 Java 8 中有了一个新的亲戚`[java.util.Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`。

# 分割器

除了*遍历*序列的数据外，像`[Iterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html)`、`[Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`也可以*分割*它:

> *迭代器+拆分= >拆分器*

`trySplit()`方法允许它将序列中的一些元素划分为另一个`[Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`。

与迭代器相比，这个特别的优势使它成为 Stream API 的核心组件。通过将数据分割成 apt 子序列，它允许并行处理。

## 特征

不是每个数据序列都可以被*分割成多个部分。*

作为并行处理的*使能器*，实际的*处理器*必须知道更多关于它从`[Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`获得的数据。例如，如果一个数据序列*必须*被排序，那么流需要以不同于无序集合的方式处理它。

为了提供尽可能多的关于“分裂”数据序列的信息，一个`[Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`可以具有某些*特征*:

*   `[CONCURRENT](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#CONCURRENT)` 底层元素源代码在没有外部同步的情况下是线程安全的吗？
*   `[DISTINCT](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#DISTINCT)` 所有元素对于任何一对都是唯一的和`x.equals(y) == false`的吗？
*   `[IMMUTABLE](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#IMMUTABLE)` 在遍历过程中，是否不允许对源文件进行任何更改，如添加/删除/替换？
*   `[NONNULL](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#NONNULL)` 所有元素都有保障`!= null`？
*   `[ORDERED](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#ORDERED)` 元素来源是否有序？
*   `[SIZED](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#SIZED)` 遍历前的预估尺寸是实际尺寸吗？
*   `[SORTED](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#SORTED)` 元素排序了吗？
*   `[SUBSIZED](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#SUBSIZED)` 源的拆分部分还是`[SIZED](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#SIZED)`？

特征由`int`表示，所以要提供多个值，我们需要使用 [*按位 or*](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/op3.html) `|`。

以下是由常用集合类型创建的拆分器的特征:

`[HashSet<T>](https://docs.oracle.com/javase/8/docs/api/java/util/HashSet.html)`的例子也表明，这些特征不必固定不变。他们可以利用周围的环境做出明智的决定。

# 界面

`[java.util.Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`只有 8 种方法。如果我们不计算`default`方法，这个数字会下降到 4:

```
**// CHARACTERISTICS**int [characteristics](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#characteristics--)();default boolean [hasCharacteristics](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#hasCharacteristics-int-)(int characteristics)**// SIZE**long [estimateSize](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#estimateSize--)();default long [getExactSizeIfKnown](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#getExactSizeIfKnown--)();**// COMPARATOR**default Comparator<? super T> [getComparator](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#getComparator--)();**// TRAVERSING**boolean [tryAdvance](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#tryAdvance-java.util.function.Consumer-)(Consumer<? super T> action);default void [forEachRemaining](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#forEachRemaining-java.util.function.Consumer-)(Consumer<? super T> action);**// SPLITTING**Spliterator<T> [trySplit](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html#trySplit--)();
```

由于这篇文章更多的是一个高层次的介绍，我就不赘述了。这些方法有很好的描述性名称，并且有很好的文档记录。

# 原始类型

Valhalla 项目还在遥远的未来，用泛型处理基本类型是行不通的。所以原语的专用类型是我们目前唯一的选择，就像专用流一样。

`[Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)` 3 种专业类型:

*   `[Spliterator.OfInt](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.OfInt.html)`
*   `[Spliterator.OfLong](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.OfLong.html)`
*   `[Spliterator.OfDouble](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.OfDouble.html)`

不直接支持其他基本类型，因为除了这三种类型之外，没有专门的`Consumer`类型存在。我们可以利用`Spliterator.OfPrimitive`来创建我们自己的，但是它需要额外的功能接口，这些接口在 JDK 并不容易获得。

# 创建拆分器

最简单的方法是使用`[java.util.Collection](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html)`的复古`[spliterator()](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html#spliterator--)`方法，它创造了一个没有任何特色的基本`[Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`。但是我在 JDK 遇到的所有收藏类型都提供了一个更加专业化的`[Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`和优化的特性。

如果我们需要为一个定制的数据序列创建一个`[Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`，我们不一定要自己实现这个接口。相反，我们可以使用`[java.util.Spliterators](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterators.html)`的便利方法。就像其他只有`static`便利方法的类型一样，它提供了广泛的选项:

特别有意思的是`[<T> Spliterator<T> spliterator(Iterator<? extends T> iterator, long size, int characteristics)](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterators.html#spliterator-java.util.Iterator-long-int-)`法。在它的帮助下，每个可迭代类型都可以用作`[Stream<T>](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)`，即使它没有提供自己的`[Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`:

实用程序类`[java.util.Arrays](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html)`也提供了`static`创建`[Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`的便利方法，但它们大多只是包装了对`[Spliterators](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterators.html)`的调用:

# 平行

并行数据处理也是一项复杂的任务。Java 集合框架类型要么不是线程安全的，要么它们提供了`synchronized`包装器来缓解。这种方法的问题是*线程争用:*线程之间对共享数据结构的访问发生冲突，导致一个线程等待另一个线程完成工作时速度变慢。

与一次只遍历一个项目不同的是，`[Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`可以分割它的一部分元素以允许并行处理，而不是提供并行行为本身。

我们可以通过调用`[Collection#parallelStream()](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html#parallelStream--)`或中间流操作`[parallel()](https://docs.oracle.com/javase/8/docs/api/java/util/stream/BaseStream.html#parallel--)`在流中利用这一点。

如果我们看实际的代码，关于`[Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`没有太大的区别:

`[Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`促进了并行性，但是它是必须做实际工作的流。

何时使用并行流超出了本文的范围。

精髓在于使用并行流不会自动带来更好的性能。这在很大程度上取决于您处理的数据的类型和数量、您的执行环境等等。这里有一篇关于并行流的可能陷阱的文章:

[](/be-careful-with-java-parallel-streams-3ed0fd70c3d0) [## 小心 Java 并行流

### Parallels streams 不仅可以提高性能，还会产生意想不到的错误

levelup.gitconnected.com](/be-careful-with-java-parallel-streams-3ed0fd70c3d0) 

# 迭代器与拆分器

尽管`[Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`和`Iterator<T>`都有*迭代*的概念，但它们的应用范围不同。

`[Iterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html)`是从 Java 1.2 开始可用的一种通用的、非并行的迭代方式。它深深植根于 [Java 集合框架](https://docs.oracle.com/javase/8/docs/technotes/guides/collections/overview.html)中。最有可能的是，我们中的许多人都有某种形式的关于`[Iterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html)`或相关`[java.lang.Iterable<T>](https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html)`的*实践*经验，用于并发修改，或者由于 [*增强 for-loop*](https://betterprogramming.pub/how-to-iterate-with-java-56b0fd83bbfc#a9ce) 。

`[Spliterator<T>](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)`可用于迭代 [Java 集合框架](https://docs.oracle.com/javase/8/docs/technotes/guides/collections/overview.html)类型，是[流 API](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html) 的核心。如果底层数据允许，它还支持并行遍历。尽管它是 Stream API 如此重要的一部分，但它更多的是一种“后台”类型。我们只通过方便的方法使用它，或者使用 JDK 中已经存在的类型。

```
**You like my ramblings about Java?
Check out my upcoming book!**
[https://belief-driven-design.com/book/](https://belief-driven-design.com/book/)
```

# 资源

*   [分割器](http://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html)(甲骨文)
*   [Java 教程并行](https://docs.oracle.com/javase/tutorial/collections/streams/parallelism.html)(甲骨文)
*   [Java 流 API](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html) (甲骨文)
*   [Java 集合框架](https://docs.oracle.com/javase/8/docs/technotes/guides/collections/overview.html)(甲骨文)
*   [迭代器](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html)(甲骨文)

[](https://betterprogramming.pub/how-to-iterate-with-java-56b0fd83bbfc) [## 如何用 Java 正确迭代

### 不仅仅是“for”和“while”

better 编程. pub](https://betterprogramming.pub/how-to-iterate-with-java-56b0fd83bbfc)