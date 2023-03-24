# 遍历 JavaScript 数组的五种方法

> 原文：<https://levelup.gitconnected.com/five-ways-to-loop-through-a-javascript-array-3325f4673334>

## 学习 JavaScript 中遍历数组的不同方法

![](img/108ec1e18b57a63727285a14c2b64d10.png)

照片由[赞](https://unsplash.com/@zanilic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/five-finger?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

[](https://gitconnected.com/portfolio-api) [## 组合 API -轻松发展您的编码事业| gitconnected

### 消除在每个单独位置手动更新您的详细信息的痛苦。只需在您的中更改一次数据…

gitconnected.com](https://gitconnected.com/portfolio-api) 

在 JavaScript 中，有很多方法可以遍历数组。

1.  可以`break`或跳过`continue`一次迭代的循环:

*   `for`
*   `while`
*   `do…while`
*   `for…in`

2.我们不能`break`或跳过迭代的循环:

*   `forEach`

提示:创建一个包含多个元素的数组

```
var array = **Array.from(new Array(10000).keys());** 
```

# 1.为

```
let arrayLength = array.length;for(let i = 0 ; i < arrayLength; i++) {

   let val = array[i];}
```

*   我们可以使用`break`语句来打破这个循环
*   我们可以使用`continue`语句跳过当前迭代。

# **2。而**

```
let i = 0;let arrayLength = array.length;while(i < arrayLength ) { let val = array[i]; i++;}
```

我们也可以在一个`while`循环中使用`break`和`continue`。但是当我们使用一个`while`循环时，我们需要为下一次迭代处理`increment`。如果我们忘记了`increment`部分，那么它可能会导致一个无限循环。

# 3.做…的同时

我不喜欢使用`do…while`循环来迭代一个数组。在检查`condition`之前，`do…while`将总是执行一次动作，这意味着即使我们的数组为空，它也会执行。这意味着它应该只用于特定的情况。

```
let i = 0;let arrayLength = array.length;do { let val = array[i]; i++; } while (i < arrayLength);
```

我们也可以在一个`do…while`循环中使用`break`和`continue`。

# **4。对于**中的……

```
for (let val in array) {
    // operation  
}
```

我们可以在`for…in`循环中使用`break`和`continue`。这些循环也可以用来遍历对象。

# 5.为每一个

如果我们需要对数组的每个元素执行操作，我们使用`forEach`。在这个循环中，我们不能中断或跳过迭代。使用`forEach`也是一种更具功能性和声明性的语法，这使得它成为许多开发人员的首选。

```
array.forEach(val => { // operation});
```

这里写了一个基准测试来寻找这些不同函数之间的时间差:[https://jsperf.com/for-vs-foreach/654](https://jsperf.com/for-vs-foreach/654)。基本的`for`循环实际上是测试用例中最快的。

除了以上方法，我们还可以使用`map`、`filter`、`reduce`这些更高级的 JavaScript 函数。这些用于具体案例，您可以在下面的文章中了解到:

[](/sneak-peak-of-map-filter-and-reduce-in-javascript-79d38181a48) [## JavaScript 中 map()、filter()和 reduce()的快速概述

levelup.gitconnected.com](/sneak-peak-of-map-filter-and-reduce-in-javascript-79d38181a48) [](https://medium.com/better-programming/you-dont-need-loops-in-javascript-1dc8139eab4b) [## JavaScript 中不需要循环

### 了解如何消除循环并使用高阶函数，如映射、减少和过滤

medium.com](https://medium.com/better-programming/you-dont-need-loops-in-javascript-1dc8139eab4b) 

跟随 [Javascript 吉普🚙💨](https://medium.com/u/f9ffc26e7e69?source=post_page-----3325f4673334--------------------------------)。

[](https://sitepoint.tapfiliate.com/p/payout-methods/new/) [## 登录|站点点

### 不支持的浏览器虽然我们的跟踪技术支持旧的浏览器，不幸的是我们的网站不支持…

sitepoint.tapfiliate.com](https://sitepoint.tapfiliate.com/p/payout-methods/new/)