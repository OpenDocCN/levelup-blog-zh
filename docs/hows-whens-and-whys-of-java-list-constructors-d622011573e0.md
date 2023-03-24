# 今天你应该知道的 Java 列表构造器技巧

> 原文：<https://levelup.gitconnected.com/hows-whens-and-whys-of-java-list-constructors-d622011573e0>

## 应如何使用 List::of、Arrays::asList、Collections::unmodifieablelist、Collections::singletonList 和 new ArrayList

![](img/6ada1844fcab539de7f0efd92895e65c.png)

列表 <person>—来自[像素](https://www.pexels.com/photo/group-of-people-sitting-on-concrete-bench-4880395/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的 [cottonbro](https://www.pexels.com/@cottonbro?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄的照片</person>

创建列表有几种方法。

*   `*Arrays.asList(…)*`
*   `*List.of(…)*`
*   `*Collections.unmodifiableList(…)*`
*   `*Collections.singletonList(..)*`
*   `*new ArrayList(…)*`

*挑哪个？为什么你应该选择* `*List.of*` *而不是* `*Arrays.asList*` *？各带来什么好处？*让我们来回答这些问题。

为了完成整篇文章，我们需要运行代码。测试将显示有效的操作，以及抛出异常的操作。代码本身并不干净，但能完成工作。

## Arrays::asList

> 返回由指定数组支持的固定大小的列表。(对返回列表的更改“直写”到数组。)这个方法与`[Collection.toArray()](https://docs.oracle.com/javase/7/docs/api/java/util/Collection.html#toArray())`相结合，充当基于数组和基于集合的 API 之间的桥梁。返回的列表是可序列化的，并实现了`[RandomAccess](https://docs.oracle.com/javase/7/docs/api/java/util/RandomAccess.html)`。— [asList docs](https://docs.oracle.com/javase/7/docs/api/java/util/Arrays.html#asList(T...))

先说`asList`操作。这是一个固定数组，值为“A”和“V”。在下一个例子中，我们还将看到*“直写”*选项。

运行`asList`操作的结果，用 JDK 11:

从日志中我们能看到什么？我们看到字符的数量没有变化。然而，我们可以看到一些方法没有抛出异常。*为什么？他们没有在列表中添加或删除字符。*

靠近点看。不会导致异常的方法有`retainAll, removeAll, sort`。保留所有元素不会导致大小变化，因此不会发生异常。

*什么原因导致异常？*用空列表调用`retainAll`。用同样的名单打电话给`removeAll`。此行为会改变列表的大小。

*`*set*`*`*replaceAll*`*为什么不抛出异常？*因为*“直写”*属性。`asList`中使用的阵列可以改变。`set`是做改变的操作。**

**`asList`创建固定大小的集合。价值观会变。你不能减少实际的数组。**

***`*asList*`*还支持什么？*将`nulls`添加到列表中。其他集合方法中的无效行为。*为什么？* `nulls`在集合中制造歧义。假设我们在地图中得到一个键`x`的`null`。这是否意味着我们在地图上没有 `*x*` *的钥匙？这是否意味着键* `*x*` *的值丢失？****

***避开* `*null*` *检查会带来什么？更好的性能。* `asList`，正如这个[研究探索的](https://dzone.com/articles/singleton-list-showdown-collectionssingletonlist-v)，比不可变的同行表现更好。**

**什么时候可以使用这种创建列表的方法？当尺寸不变，但数值会变。我会把它用在固定大小的游戏板上。每个元素都可能改变，但大小不应该改变。**

## **列表::共个**

**`List::of` 是完全不可改变的。无论是大小还是实际价值。不管参数是什么，每个方法都会抛出`UnsupportedOperationException`。**

**Stuart Marks [声明](https://www.youtube.com/watch?v=ogRVWXuuAU4&ab_channel=Java)说`List::of`提供了不可变的集合。元素本身并不是不可变的。原始值被包装，因此是不可变的。**

**使用`List::of`创建配置列表。当你需要在石头上设定价值时。**

## **Collections::singletonList**

**这种行为类似于`List::of`。我们不能改变大小，价值观必须保持不变。**

**例外情况是`retainAll, removeAll, sort`。它们不改变包装列表，因此不会抛出异常。**

**`singletonList`的一个怪异行为。可以把`null`放到`singletonList`里。*为什么很奇怪？* `null`避免用于所有收藏。即便如此，`singletonList`也能顶得住`null`。**

**根据[到](https://dzone.com/articles/singleton-list-showdown-collectionssingletonlist-v)的实验，`singletonList`比`List::of`快。这并不意味着我们不对单例列表使用`List::of`。*我们什么时候需要* `*List::of*` *进行单体列表？*创建动态列表时。当你需要确保单例列表包含`nonNull`元素时，应用`List::of`。**

***什么时候可以用* `*null*` *的* `*singletonList*` *？*从列表中删除`nulls`。这里有一个例子:**

```
**List<String> list = Arrays.asList("abc", null, "def", null);
list.removeAll(Collections.singletonList(null));**
```

**也可以是`retainAll` `nulls`，尽管我没有看到这种行为的用例。**

## **使用新数组列表**

**你看我们最后得到了一个空列表。当您需要改变列表或从不可变列表创建可变列表时，请使用 new ArrayList。**

## **集合::不可修改列表**

**使用`Collections::unmodifiableList`或`List::of`，因为它们有相同的行为。当您需要更多详细信息时，使用`Collections::unmodifiableList`。**

**`Collections::unmodifiableList`创建**不可修改的视图**。*什么是不可修改视图？*列表内容可以更改。名单不能变。包装原始值，使它们不可变。这里有一个**不可修改视图**的例子。**

```
**### TESTING unmodifiableList with mutable content ###
a123**
```

***那* `*List::of*` *呢？***

**我们得到了同样的经历，就像对待`Collections::unmodifiableList`一样。**

```
**### TESTING listOF with mutable content ###
a123**
```

# **摘要**

**不要得出永恒不变的结论。你可能错了。我得出了错误的结论，这浪费了开发人员的时间。**

**`unmodifiableList`，表示内容可以改变，收藏不能。做出这种改变，远离讨厌的虫子。**

**`singletonList`允许`nulls`。不想要`nulls`的时候用`List::of`。`List::of`性能更差，但不允许`List`中的`nulls`。**

# **你可能会喜欢这些文章**

**[](https://medium.com/dev-genius/9-bad-java-snippets-to-make-you-laugh-82f5018e06e5) [## 让你发笑的 9 个糟糕的 Java 片段

### 糟糕的 Java 代码让你感觉更好

medium.com](https://medium.com/dev-genius/9-bad-java-snippets-to-make-you-laugh-82f5018e06e5) [](https://medium.com/javarevisited/linked-list-analysis-java-developers-should-know-121ed803bee1) [## Java 开发人员应该知道的链表分析

### LinkedList 如何击败 ArrayList 和 ArrayDeque

medium.com](https://medium.com/javarevisited/linked-list-analysis-java-developers-should-know-121ed803bee1) [](https://medium.com/javarevisited/7-practical-java-tips-you-should-know-92654313eb4) [## 你应该知道的 7 个实用 Java 技巧

### Java 的注意事项可选

medium.com](https://medium.com/javarevisited/7-practical-java-tips-you-should-know-92654313eb4)**