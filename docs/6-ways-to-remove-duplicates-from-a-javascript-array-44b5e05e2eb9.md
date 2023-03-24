# 从 JavaScript 数组中删除重复项的 6 种方法

> 原文：<https://levelup.gitconnected.com/6-ways-to-remove-duplicates-from-a-javascript-array-44b5e05e2eb9>

## 哪种方式更好？

![](img/0b037bb59e6f95d8701e61df3c731e45.png)

照片由 [Sangga Rima Roman Selia](https://unsplash.com/@sxy_selia?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在这个故事中，我将向您展示从 JavaScript 数组中删除重复项的 6 种方法。哪种方法比较好？来和我一起探索吧！

# 1.简单双 for 循环

使用双循环进行重复数据删除的逻辑非常简单，但是代码的时间复杂度很高。

# 2.(for 循环||减少)+ (indexOf ||包含)

我们可以使用`Array.prototype.reduce()`、`Array.prototype.filter()`甚至一个简单的 for 循环来遍历原始数组，然后使用`Array.prototype.indexOf()`或`Array.prototype.includes()`进行重复判断。更推荐`Array.prototype.includes()`，因为它认可`NaN`。

# 3.过滤器+索引

我们可以用`Array.prototype.filter()`和`Array.prototype.indexOf()`。如果当前元素的索引与`Array.prototype.indexOf()`找到的索引相同，则意味着该元素在数组中第一次出现，换句话说它是唯一的。

# 4.排序+(用于循环||归约||过滤器)

我们可以将数组从小到大排序得到升序排列的数组，这样就可以用`Array.prototype.reduce()`、`Array.prototype.filter()`甚至简单的`for loop`来判断当前元素是否等于前一个元素。如果相等，说明是重复元素。

这样可以达到去重的效果，但是这种方法打乱了原始数组元素的顺序。

# 5.Map +(用于循环||减少||过滤器)

我们可以用 Map 作为哈希表，记录数组中的元素是否出现过，然后进行循环判断，也可以达到去重的目的。

# 6.一组

我们可以使用 ES6 提供的`Set`对象，它可以存储任何类型的**唯一值**，无论是原始值还是对象引用。接下来，我们可以使用 spread 操作符或`Array.from()`将`Set`对象转换成一个数组。

# 给我看看代码

最后，我是李。我会继续输出前端技术相关的故事。如果你喜欢这样的故事，想支持我，请考虑成为 [*中等会员*](https://medium.com/@islizeqiang/membership) *。每月 5 美元，你可以无限制地访问媒体内容。如果你通过* [*我的链接*](https://medium.com/@islizeqiang/membership) *报名，我会得到一点佣金。*

你的支持对我来说非常重要——谢谢。