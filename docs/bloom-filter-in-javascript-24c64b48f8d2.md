# 如何在 JavaScript 中实现布隆过滤器

> 原文：<https://levelup.gitconnected.com/bloom-filter-in-javascript-24c64b48f8d2>

布隆过滤器是用于确定元素是否是集合成员的概率数据结构。它允许您在使用相对较少的空间的同时，以较高的准确度测试集合中某个元素的成员资格。在本文中，我们将探索 bloom filters 是如何工作的，以及如何用 JavaScript 实现它。我们还将深入探讨一下位运算。如果你想了解更多关于位运算的知识，我们也在这里发表了一篇关于 JavaScript 中位运算基础的[文章。我们还将看看布鲁姆过滤器的一些实际应用，以及它们如何用于解决现实世界中的问题。对于我们当中不耐烦的人:我们在最后链接了 GitHub 存储库中的示例项目。](https://medium.com/gitconnected/basics-of-bitwise-operations-in-javascript-with-practical-examples-6197b4d391b3)

这只是许多关于 JavaScript 和软件开发的文章中的一篇。欢迎关注我们，获取更多关于 JavaScript 和软件开发的精彩内容。我们尝试一周发布多次。请确保不要错过我们的任何精彩内容。

![](img/bec947c2fea6f75f920fbe91ca93d8eb.png)

由[泰勒·尼克斯](https://unsplash.com/@nixcreative?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

1970 年，伯顿·霍华德·布鲁姆构思了布鲁姆过滤器。它是一种概率数据结构，用于测试一个元素是否是一个集合的成员。布隆过滤器允许非常快速的成员资格查询，但是有可能产生假阳性(错误地指示元素在集合中),但是它永远不会返回假阴性结果。误报的概率使其成为一种概率数据结构。

布隆过滤器被实现为比特阵列，其中每个比特最初被设置为 0。为了向过滤器添加一个元素，几个散列函数
被应用于该元素，并且产生的散列值被用于将数组中的相应位设置为 1。为了检查元素是否在过滤器中，将相同的散列函数应用于该元素，并检查数组中的相应位。如果所有位都已设置，则该元素可能在过滤器中。如果没有设置任何位，则该元素肯定不在过滤器中。

可以通过调整数组的大小和使用的哈希函数的数量来控制误报的概率。较大的数组和较大数量的散列函数将导致较低的误报概率，但也会导致较慢的成员资格查询。

布隆过滤器通常用于快速执行成员资格查询很重要，并且误报的代价可以接受的情况。它们通常用于网络和安全应用中，以过滤掉不需要的流量或检测恶意活动。

以下是布隆过滤器的一些使用案例:

*   **网络流量过滤**:布隆过滤器可用于在网络层过滤掉不需要的流量，只允许授权的流量通过。这在防火墙或入侵检测
    系统中很有用。
*   **拼写检查**:布隆过滤器可以用来快速检查单词拼写是否正确。这在文本编辑器或文字处理器中可能很有用。
*   **集合成员关系**:布隆过滤器可以用来测试一个元素是否是集合的成员。在快速执行成员资格查询很重要并且误报的代价可以接受的情况下，这可能是有用的。
*   **缓存失效**:布隆过滤器可用于跟踪哪些项目在缓存中，并快速确定项目是否在缓存中。这在分布式缓存系统中非常有用，在向源服务器发出请求之前，快速检查缓存中是否存在某个项目非常重要。

以下是使用布隆过滤器的一些优点:

*   **快速成员资格查询** : Bloom filters 允许非常快速的成员资格查询，因为它们只需要少量的哈希函数计算和位运算。
*   **低内存使用率** : Bloom filters 使用固定数量的内存，与集合中的项目数量无关。这使得它们适合在内存有限的情况下使用。
*   **无假阴性** : Bloom 过滤器从不产生假阴性，这意味着如果一个元素在过滤器中，成员查询将总是返回“可能在集合中”。
*   **可调整的误报率**:通过调整位数组的大小和使用的哈希函数的数量，可以控制误报的概率。这允许用户在假阳性率和成员资格查询速度之间进行权衡。
*   **简单实现**:布隆过滤器实现起来相对简单，只需要少量代码。

以下是使用布隆过滤器的一些缺点:

*   **误报的可能性**:布隆过滤器有可能产生误报，这意味着它们可能错误地指示元素在集合中。这可以通过使用更大的位数组和更多的哈希函数来缓解，但这也会导致成员资格查询更慢。
*   **容量有限** : Bloom filters 只能存储有限数量的项目，之后它们的误报率迅速增加。这意味着它们不适合在项目集
    预计会随着时间显著增长的情况下使用。
*   **不可移除元素**:一旦一个元素被添加到布隆过滤器，它就不能被移除。这意味着过滤器总是有可能对该元素产生误报。
*   **功能受限** : Bloom filters 只能执行成员查询，不支持插入、删除、交集等其他集合操作。

让我们实现一个使用布隆过滤器的小应用程序。让我们写一个密码检查器:当你注册一个新账户时，建议不要重复使用现有的密码，而是创建一个新的。为了检查您是否已经使用了密码，我们将使用 bloom filter 编写一个应用程序。如果不使用您的密码，它将返回 false。如果您的密码可能(也可能没有)已经被使用，那么它将返回 true，并要求您创建一个新密码。如您所见，bloom filter 并不完全准确，因为它可能会返回误报。但是在我们的例子中，这无关紧要，因为您只需创建一个新密码。

让我们开始实现我们的密码检查器:

```
class BloomFilter {
 constructor(size, hashFunctions) {
   this.size = size;
   this.hashFunctions = hashFunctions;
   this.bits = new Array(size).fill(0);
 }
 add(item) {
  for (let i = 0; i < this.hashFunctions.length; i++) {
   const hashValue = this.hashFunctions[i](item);
   const index = hashValue % this.size;
   this.bits[index] |= 1 << hashValue;
  }
 }
 contains(item) {
  for (let i = 0; i < this.hashFunctions.length; i++) {
   const hashValue = this.hashFunctions[i](item);
   const index = hashValue % this.size;
   if ((this.bits[index] & 1 << hashValue) === 0) {
    return false;
   }
  }
  return true;
 }
}
```

如果你一开始不明白，它是做什么的，不要着急。接下来，我们将仔细阅读每一行代码，并向您解释:

```
class BloomFilter {
 constructor(size, hashFunctions) {
  this.size = size;
  this.hashFunctions = hashFunctions;
  this.bits = new Array(size).fill(0);
 }
```

这定义了一个名为 BloomFilter 的类，它的构造函数有两个参数:size 和 hashFunctions。size 参数是用于在过滤器中存储项目的位数组中的位数。hashFunctions 参数是一个函数数组，用于对添加到过滤器中的项目进行哈希运算。我们将在本文后面更详细地讨论 this 函数的实现。

构造函数初始化三个实例变量:size、hashFunctions 和 bits。size 和 hashFunctions 被设置为传递给构造函数的参数值，bits 是一个 size 元素的数组，都被初始化为 0。

```
add(item) {
 for (let i = 0; i < this.hashFunctions.length; i++) {
  const hashValue = this.hashFunctions[i](item);
  const index = hashValue % this.size;
  this.bits[index] |= 1 << hashValue;
 }
}
```

这是 add 方法，用于向过滤器添加一个项目。它
遍历散列函数数组，将每个函数应用于该项，并使用得到的散列值在 bits 数组中相应的索引处设置一个位。它通过使用|=操作符将 1 < <的值与位数组
中索引处的位的当前值进行 or 运算来实现这一点。

```
contains(item) {
 for (let i = 0; i < this.hashFunctions.length; i++) {
  const hashValue = this.hashFunctions[i](item);
  const index = hashValue % this.size;
  if ((this.bits[index] & 1 << hashValue) === 0) {
   return false;
  }
 }
 return true;
}
```

这是 contains 方法，用于检查一个项目是否在
过滤器中。它的工作方式类似于 add 方法，但是它不是设置 bits 数组中的位
，而是检查这些位是否被设置。如果任何位
没有被设置，它立即返回 false。如果所有的位都已设置，则返回 true。这意味着该项目可能在过滤器中，但有可能出现误报。它使用&运算符将 1 < <的值与 bits 数组中索引处的位的当前值进行哈希运算，并检查结果是否等于 0。

可能的哈希函数如下所示:

```
function hash(item) {
 let hash = 0;
 for (let i = 0; i < item.length; i++) {
  hash = (hash << 5) + hash + item.charCodeAt(i);
  hash = hash & hash;
  hash = Math.abs(hash);
 }
 return hash;
}
```

这只是哈希函数的一个基本实现。这对于生产来说还不够，但是对于我们的例子来说已经足够了。下面我们将逐行解释 has 函数的作用:

```
function hash(item) {
 let hash = 0;
```

这定义了一个名为 hash 的函数，它将一个项作为参数。它将名为 hash 的局部变量初始化为 0。

```
for (let i = 0; i < item.length; i++) {
 hash = (hash << 5) + hash + item.charCodeAt(i);
```

这是一个遍历项目中每个字符的循环。对于每个字符，它将 hash 的值左移 5 位并加到
本身，然后将字符的 ASCII 值加到 hash 中。这有助于将散列的比特混合在一起。

```
hash = hash & hash;
```

这将 hash 值与其自身进行 and 运算，有助于进一步混合 hash 位并减少冲突的机会。

```
hash = Math.abs(hash);
```

这取 hash 的绝对值，保证它是正数。

```
}
 return hash;
}
```

最后，函数返回 hash 的值。请注意，这只是一个哈希函数的示例实现。您不希望在您的产品代码中使用这个函数。

要在 Bloom filter 中使用这个散列函数，您可以将它作为散列函数数组中的一个元素传递给 Bloom filter 的构造函数。例如:

```
const bloomFilter = new BloomFilter(1000, [hash]);

bloomFilter.add("hello");
bloomFilter.add("world");
bloomFilter.add("hi");
bloomFilter.add("aasdf");
bloomFilter.add("test123");
bloomFilter.add("test1234");
bloomFilter.add("1234");
bloomFilter.add("password");

console.log(bloomFilter.contains("hello"));    // true
console.log(bloomFilter.contains("world"));    // true
console.log(bloomFilter.contains("hi"));       // true
console.log(bloomFilter.contains("aasdf"));    // true
console.log(bloomFilter.contains("test123"));  // true
console.log(bloomFilter.contains("test1234")); // true
console.log(bloomFilter.contains("1234"));     // true
console.log(bloomFilter.contains("password")); // true
console.log(bloomFilter.contains("password1"));// false
```

整个项目可以在我们的 [GitHub 资源库](https://github.com/pandaquests/passwordChecker/blob/main/passwordChecker.js)中找到:

![](img/5c7fdb823e2c7f4190f716ff6bed224c.png)

这就是你如何使用布鲁姆过滤器。你以前实现过布隆过滤器吗？在什么背景下？或者你有什么问题吗？请在下方留言评论。也别忘了跟着我们。我们每周发表多篇文章。所以，不要错过任何一个。再见