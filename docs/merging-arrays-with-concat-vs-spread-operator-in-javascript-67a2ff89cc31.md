# JavaScript 中使用 concat 与 spread 运算符合并数组

> 原文：<https://levelup.gitconnected.com/merging-arrays-with-concat-vs-spread-operator-in-javascript-67a2ff89cc31>

![W](img/d891666cc9ebd9231a574f9c9cb6a45e.png)  W 谈到在 JavaScript 中合并数组，有几种方法可用，每种方法都有自己的优缺点。在我们之前的一篇文章中，我们讨论了合并两个数组的不同方法。在本文中，我们将比较两种最流行的方法:使用 concat 方法和使用 spread 运算符。concat 方法和 spread 运算符都允许您将两个或多个数组合并成一个数组，但是它们有一些关键的区别，使它们更适合某些任务。我们将探究这些差异，并查看一些实际例子，说明如何在 JavaScript 中使用 concat 方法和 spread 操作符来合并数组。

这只是众多关于 web 开发的文章中的一篇。请随时关注或订阅我们，获取更多关于 web 开发的精彩内容。我们尝试一周发布多次。请确保不要错过我们的任何精彩内容。

![](img/b46c79d47a55bdf2fdf7020d319a6b8d.png)

照片由 [Karoline Soares](https://unsplash.com/@karolinesfotografia?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

有不同的方法来合并多个数组。在上一篇关于合并数组的文章中，我们介绍了不同的方法。但是在这篇文章中，我们想集中讨论其中的两种，并逐一进行比较

当您合并多个数组时，concat 和 spread 的行为非常相似。然而，如果参数不是一个
可迭代的，它们的行为就会完全不同。

在 JavaScript 中，iterable 是一个实现 Symbol.iterator
方法的对象，该方法允许在
循环的 for…中或使用 spread 运算符(…)迭代(循环)对象。

下面是一个可迭代对象的例子:

```
const iterable = {
 [Symbol.iterator]: function* () {
 yield 1;
 yield 2;
 yield 3;
 }
};
for (const value of iterable) {
 console.log(value);
}
// Output: 1, 2, 3
```

可迭代对象是 JavaScript
语言设计中的一个核心概念，它们被用在各种上下文中，比如数组、
字符串和生成器函数。

数组和字符串是 JavaScript 中内置的可迭代对象，它们
可以使用 for…of 循环或 spread 操作符(…)循环。对于
示例:

```
const arr = [1, 2, 3];
for (const value of arr) {
 console.log(value);
}
// Output: 1, 2, 3
const str = 'abc';
for (const value of str) {
 console.log(value);
}
// Output: 'a', 'b', 'c'
```

生成器函数是在
执行期间可以暂停和恢复的函数，当被调用时，它们返回一个可迭代的对象。下面是一个返回 iterable 对象的生成器函数的
示例:

```
function* generator() {
 yield 1;
 yield 2;
 yield 3;
}
const iterable = generator();
for (const value of iterable) {
 console.log(value);
}
// Output: 1, 2, 3
```

让我们回到 concat()方法和 spread 操作符。当合并数组时，concat()将数组项作为一个整体添加，而 spread 操作符尝试迭代它，如果失败，将引发异常。例如:

```
const a = [1,2,3]
const y = 99;
console.log(a.concat(y)); 
// Output: [1,2,3,99]
console.log([…a, …y]); 
// Output: throws TypeError
```

如您所见，concat()方法将数字视为一个整体，而 spread 操作符试图迭代，但失败了，因此抛出了异常。

另一个例子:

```
const x = "hi";
console.log(a.concat(x)); 
// Output: [1,2,3, "hi"];
console.log([…a, …x]); 
// Output: [1,2,3, "h", "i"]
```

concat()方法将字符串视为一个整体，而 spread 则尝试遍历字符串中的每个字符。

下面是另一个说明 concat()方法和 spread 运算符之间差异的示例:

```
function* gen() {
 yield 'a';
 yield 'b';
 yield 'c';
}
console.log(a.concat(gen())); 
// Output: [1,2,3, [gen]]
console.log([...a, ...gen()]); 
// Output: [1,2,3, 'a', 'b', 'c']
```

concat()方法将生成器函数作为一个整体放入数组中。而 spread 操作符接受生成器函数并执行它，从而将生成的值添加到数组中。

总而言之，当参数可能不可迭代时，concat()和 spread 之间的
选择取决于您是否希望它们被迭代。除此之外，它们的行为基本相同。如果您希望它们被迭代，请使用 spread。如果你想把它当作一个整体，使用 concat。

话虽如此，ES6 还是提供了一种用 Symbol.isConcatSpreadable 覆盖
concat()默认行为的方法。

默认情况下，这对于数组是真的。对于其他一切，它是
假的。手动将其设置为 true，告知 concat()迭代
参数，就像 spread 一样，如下所示:

```
const x = new String("hi");
console.log(a.concat(x)); 
// Output: [1,2,3, "hi"];
x[Symbol.isConcatSpreadable] = true
console.log(a.concat(x)); 
// Output: [1,2,3, "h", "i"];
```

性能方面的 concat()对于小型和大型数组都更快
，这可能是因为它可以受益于特定于数组的优化，而
spread 必须符合通用迭代协议。然而，这只适用于较新的浏览器。在旧的浏览器上，spread 操作符对于小数组更快，而 concat()对于大数组更快。

![](img/5c7fdb823e2c7f4190f716ff6bed224c.png)

就在几个月前，我们认为 spread 对于小数组更快，而 concat()对于大数组更快。如你所见，事情发生了巨大而快速的变化。因此，保持最新状态是很重要的。您可以通过关注或订阅我们来做到这一点。我们尝试一周发布多次。请确保不要错过我们的任何精彩内容。再见。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)