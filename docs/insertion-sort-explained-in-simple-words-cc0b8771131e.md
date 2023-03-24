# 用简单的话解释插入排序！

> 原文：<https://levelup.gitconnected.com/insertion-sort-explained-in-simple-words-cc0b8771131e>

![](img/0cd73aff8c39b8c3c1fdd3639ff70b2b.png)

由[凯利·西克玛](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

想象一下，你是一名教师，正在看纸上的 1000 多名学生的名单。你想在列表中找到一个学生的名字。你必须查看名单上的每个人的名字。

现在，想象一下，如果这个列表是按字母顺序排列的。你只需要看列表中以你需要的字母开头的部分。电脑也是如此。遍历排序列表比普通列表更容易。

# 排序算法

对一个列表(或者一个数组)进行排序有很多技巧。一种方法是比较每对元素，并相应地交换它们。这是幼稚的做法。

为了优化排序过程，可以使用插入排序、快速排序、冒泡排序等算法。在这篇文章中，我们将看看插入排序。

# 插入排序的概念

![](img/c5f5fd3e1a1d03cd91752c6044a25f81.png)

照片由[伊内斯·费雷拉](https://unsplash.com/@inesrochaferreira?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

假设您要对一副牌进行排序。你可以从拿起最小的卡片插入正确的位置开始(假设是第一个索引)。直到该牌被认为是分类的牌。

再一次，从一叠卡片中找出除了你已经挑选的卡片之外最小的卡片，并把它插入正确的位置。重复这样做，直到整包卡片都被分类。

这是插入排序背后的主要概念。您可以为每个元素找到它们在最终排序列表中的位置。

# 伪代码

现在，我将解释函数 insertionSort (arr)的伪代码。它将未排序的数组作为唯一的参数。注意，这不是实际的代码。阅读[本](https://www.geeksforgeeks.org/how-to-write-a-pseudo-code/)获取伪代码指南。

```
for j = 1 to arr.length - 1
    key = arr[j]
    i = j-1
    while i >= 0 and arr[i] > key
        arr[i+1] = arr[i]
        i = i-1
    arr[i+1] = key
```

我举个例子解释一下。考虑下面的数组。

```
arr = [12, 14, 16, 13, 15]
```

从 1 到 4 运行`for`循环。最初，

```
j = 1
key = arr[1] = 14
i = 0
```

直到索引`i — 1`，数组被排序。自从`i=0`以来，数组的任何部分都没有被排序。

`for`循环的每次迭代都试图找到`arr[j]`的正确位置，即`key`。内部`while`循环将每个元素的`key`与之前的元素进行比较，并找到此时`key`的正确位置。

第一次迭代后，数组看起来与`key`相同，即 14 > 12。直到 12，所有元素都被排序。

在第二次迭代中，

```
j = 2
key = arr[1] = 16
i = 1
```

同样，没有变化，因为 16 大于所有早期的元素。

第三次迭代

```
j = 3
key = arr[1] = 13
i = 2
```

现在，当条件为`true`时，内部`while`循环将运行。在每次迭代结束时，数组以下列方式变化。

```
1st itr: [12, 14, 13, 16, 15] 
2nd itr: [12, 13, 14, 16, 15]Final array after while loop ends:
arr=[12, 13, 14, 16, 15]
```

第四次迭代。

```
j = 4
key = arr[1] = 15
i = 3
```

再次运行内部`while`循环。

```
1st itr: [12, 13, 14, 15, 16]Final array after while loop ends:arr=[12, 13, 14, 15, 16]
```

`for`循环结束。您现在可以看到数组已经排序。

# 时间复杂度

知道一个算法需要多少时间是很重要的。我们无法计算准确的时间，因为输入大小可能会有所不同。算法的时间复杂度作为输入大小的函数来度量。如果你对时间复杂度不熟悉，就跟着[这个](https://www.youtube.com/watch?v=0IAPZzGSbME&list=PLDN4rrl48XKpZkf03iYFl-O29szjTrs_O) YouTube 播放列表。

我们首先计算每个语句被执行的次数。

由于`for`循环从`j= 2 to n`开始运行，所以里面的每条语句都执行`n-1`次。这里，条件语句执行`n`次，因为它还检查循环不运行的退出条件。

让`tⱼ`表示每个`j`执行 while 语句的次数。

```
 **Statement**                                  **No. of times**for j = 1 to arr.length - 1                        n
    key = arr[j]                                  n-1
    i = j-1                                       n-1
    while i >= 0 and arr[i] > key             (n-1) * tj
        arr[i+1] = arr[i]                    (n-1) * (tj-1)
        i = i-1                              (n-1) * (tⱼ-1)
    arr[i+1] = key                                n-1
```

我们还将假设每个语句都需要一定的时间 cᵢ.所有语句的运行时间为

```
T(n) = c1 * n + c2 * (n-1) + c3 * (n-1) + c4 * (n-1) * tⱼ 
       + c5 * (n-1) * (tⱼ - 1) + c6 * (n-1) * (tⱼ - 1) + c7 * (n-1)
```

现在，我们来看两个场景。

## 最好的情况

插入排序的最佳情况发生在数组已经排序的时候。在这种情况下，内部 while 语句每次迭代只执行一次，即(tⱼ=1).上述函数变为:

```
T(n) = (c1 + c2 + c3 + c4 + c7)n - (c2 + c3 + c4 + c7)
```

这变成了形式`an + b`的线性函数。

## 最坏情况

最坏的情况发生在数组按降序排序时。这是插入排序花费最长时间的时候。在这种情况下，我们将`key`与目前为止排序的数组中的每个元素进行比较。因此，内部 while 循环运行到目前为止排序的数组的整个长度。

于是，`tⱼ = j`为`j = 2, 3,… n`。代入这些值，等式变为:

```
T(n) = (c4/2 + c5/2 + c6/2) * n² + (c1 + c2 + c3 + c4/2 - c5/2 - c6/2 + c7)n - (c2 + c3 + c4 + c7)
```

因此`T(n)`的形式为`an²+bn+c`，它是一个二次函数。

但是“一般情况”呢？数组是随机的会怎么样？一般情况被认为和最坏情况一样糟糕。所以，时间复杂度仍然是二次函数。

现在，我们不写完整的函数，而表示时间复杂度。我们忽略每个语句的恒定时间，只考虑增长的*阶*，即最差情况下的`an²`的最高阶函数。

同样，忽略常数，我们将最坏情况下的时间复杂度记为`O(n²)`，即`n²`的阶数。同样，最好的情况时间复杂度是`O(n)`。

有不同类型的符号用于表示时间复杂度。了解更多关于他们的[在这里](https://www.programiz.com/dsa/asymptotic-notations)。

# 结论

排序是计算机科学中非常重要的算法。根据您的情况，您可以尝试不同的排序算法，以找到最适合您的算法。

现在，想象你是同一个老师。不需要比较每一个元素，你可以使用这些算法中的一种以更快的方式对列表进行排序。

在这篇文章中，我详细解释了插入排序背后的主要概念，算法的每一步，以及如何计算它的时间复杂度。我希望我能让你明白。

如果您无法理解内容或对解释不满意，请在下面评论您的想法。新想法总是受欢迎的！如果你喜欢这篇文章，请鼓掌。**订阅**和**关注**我的每周内容。如果你想讨论什么，请随时在 LinkedIn 上联系我。到那时，再见！！

# 参考

这篇文章引用了《算法导论》一书的内容。它对插入排序有非常详细的解释。在这篇文章中，我试图总结相同的观点。

这是一本关于数据结构和算法入门的好书。你可以在[亚马逊](https://www.amazon.in/Introduction-Algorithms-3Ed-International-Press/dp/0262533057/ref=sr_1_3?crid=1KI28MW62BS3L&keywords=introduction+to+algorithms&qid=1665069500&qu=eyJxc2MiOiIyLjkxIiwicXNhIjoiMS42NyIsInFzcCI6IjEuNDYifQ%3D%3D&sprefix=introduction+to+algorithms%2Caps%2C253&sr=8-3) ( [链接](https://www.amazon.com/Introduction-Algorithms-Leiserson-Charles-Clifford-dp-B0839JW93F/dp/B0839JW93F/ref=mt_other?_encoding=UTF8&me=&qid=1665069547)美国网站)上买到这本书。