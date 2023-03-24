# 为什么 Rust 中的迭代器很酷，以及如何更好地使用它们

> 原文：<https://levelup.gitconnected.com/iterators-in-rust-what-does-lazy-really-mean-6e33e06cf0fb>

![](img/a37b7af30cec323fcba476c7aae30740.png)

元素链接上生锈的迭代器…懂了吗？

在普通 JavaScript 中，对列表执行过滤，然后记录每个元素需要`O(2n)`。在 Rust 中，需要`O(n)`。下面是原因，以及 Rust 迭代器的其他一些很酷的东西。

我们经常听说 Rust 迭代器很懒，但我从来没有真正明白这是什么意思，直到我不得不用 Rust 和 JavaScript(技术上是 TypeScript，但仍然是)过滤列表。

在 JavaScript 中，列表过滤器如下所示:

```
myList.filter((t) => t !== "Filter me out")
```

然后要记录每个元素，您应该像这样链接它:

```
myList.filter((t) => t !== "Filter me out").forEach(console.log)
```

幕后发生的是`filter`函数将遍历列表，返回每个不符合条件的元素。在这种情况下，它会过滤掉所有没有说“过滤掉我”的字符串。然后，它将通过一个*新的*列表重复并记录它们中的每一个。

修复它使其成为`O(n)`是很简单的:

```
myList.forEach((t) {if (t!== "Filter me out") console.log(t)})
```

但那看起来不干净。

在 Rust 中，你已经使用类似于第一种方法将`O(n)`开箱。

```
myList
.into_iter()
.filter(|v| v != "Filter me out")
.for_each(|v| println!("{}", v))
```

那么，这里的魔力在哪里呢？

Rust 迭代器是*懒惰的，*这意味着它们直到被告知才执行。这意味着您可以在迭代器上添加任何一系列操作，直到您使用它，它才会“运行”。这意味着当我们在迭代器上调用`for_each`时，它消耗迭代器，但只获得通过过滤器的元素。

这很有用，因为现在，我们可以用迭代器以函数的方式做任何事情。

```
myList
.into_iter()
.filter(..) //one filter
.filter(..) // two filter
.map(..) //map 
.filter(..) //filter because why not
.map(..) //map again... who's writing this code???
.cloned(..) //for the hell of it, we clone ever element
```

上面的例子在现实世界中并不实用(因为你为什么需要映射 x 2 和过滤 x 3？)，但它是有效的 Rust 代码…除此之外，它没有做任何事情。

如果你在末尾加上分号，那么你除了销毁`myList`之外什么也没做(因为我们用了`into_iter.`如果我们用了`iter`我们什么也没做，就这样。).我们需要消耗迭代器，用消耗迭代器的东西。

在这种情况下，它可能是一个`for_each`，但是您可以做很多事情。也许你想要一个`.sum`，或者`.collect`把它转换成另一个向量，或者也许是`.sort`？这些都会消耗迭代器，一旦消耗完，就完成了。

这个设计非常聪明，因为它让我们的代码更具可读性。我们不需要像在 JS 中那样将每个操作放在同一个闭包中，如果我们想的话，我们可以在执行之前通过存储迭代器来有条件地添加过滤器。

这里有一个操作列表，你*可以*在迭代器中使用这些操作，以使你的代码更具功能性和可读性:

*   将迭代器缩减到它的第一个`n`元素
*   `.skip(n)`跳过第一个`n`元素
*   `.cloned()`克隆迭代器中的每个元素
*   `.enumerate`将元素`t`上的迭代器转换为元素`(t, i)`上的迭代器，其中`i`是元素的索引。这个有用，我用的很多:)
*   `.cycle`无限循环迭代器。
*   `.rev`反转一个迭代器。

在文档中还有很多很多。我只挑选了 6 个最常用/最有用的，但是如果你曾经在`map`或`for_each`中写了太多的逻辑，你可能想在我开始迭代之前后退一步，想想什么可以被应用。