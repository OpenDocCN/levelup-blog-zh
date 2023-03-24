# 让您的日常 JavaScript 编码任务变得更简单的 10 种简单方法

> 原文：<https://levelup.gitconnected.com/10-simple-ways-to-make-your-daily-javascript-coding-tasks-easier-98b2e575a42>

## 使用下划线. js

![](img/ae2ea339f3ddb5cd3a79348ff3efe5f1.png)

克里斯托夫·高尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 下划线. js 是什么？

js 是一个 JavaScript 库，具有一些用于常见编程任务的实用函数。在过去的一年里，我一直在使用这个库，它已经成为我最喜欢的 JS 库之一。下划线. js 提供了大约 100 个不同的实用函数，但是今天，我们将讨论 10 个最常用的函数。

![](img/5b23421e092796cb0a54d10f017158bd.png)

下划线. js —一个 JavaScript 库，为常见编程任务提供了实用函数。

在我们深入了解如何使用这个库之前，让我们先来学习如何安装这个库。您可以通过三种不同的方式导入下划线. js，使用 Node.js、使用 Meteor.js 和使用 Bower。

```
npm install underscore // Node.js
meteor add underscore // Meteor.js 
bower install underscore // Bower
```

# 效用函数-

# 1.每个

这个函数类似于 JavaScript 中默认的 **forEach** 函数。它用于迭代元素数组，并对数组中的每个元素执行操作。

示例-

```
const _ = require('underscore');let nums = [1, 2, 3, 4, 5];
_.each(nums, function(num) {
    // perform operation on each num
    console.log(num);
});
```

# 2.发现

该函数遍历数组中的每个元素，并传递第一个满足特定真值条件的元素。一旦满足真值条件并且找到了元素，其余的元素就不会被迭代。

示例-

```
const _ = require('underscore');let nums = [1, 2, 3, 4, 5, 6];
_.find(nums, function(num) {
    return num % 3 == 0;
});// It will return 3
```

# 3.过滤器

该函数遍历数组中的每个元素，并返回一个包含所有通过真值条件的元素的数组。它类似于 find 函数，但是它不是返回通过真值测试的第一个元素，而是返回所有通过条件的元素。

```
const _ = require('underscore');let nums = [1, 2, 3, 4, 5, 6];
_.filter(nums, function(num) {
    return num % 3 == 0;
});// It will return [3,6]
```

# 4.地图

该功能类似于 each 功能。它遍历整个数组，并通过对原始列表中的每个元素执行操作来返回一个新数组。

它主要是操纵原始数组并返回一个新数组。

示例-

```
const _ = require('underscore');let nums = [1, 2, 3, 4, 5];
_.map(nums, function(num) {
    return num * 10;
});// it will return [10, 20, 30, 40, 50]
```

# 5.减少

该函数用于将整个数组“缩减”为一个元素。它用于通过对每个元素连续执行操作，将整个数组转换为单个值。

示例-

```
const _ = require('underscore');let nums = [1, 2, 3, 4, 5];
_.reduce(nums, function(memo, num) {
    return memo + num;
}, 0);// This will return the sum of all elements in the array which is 15
```

这里 memo 是缩减的初始状态，每个后续状态都由 iteratee 返回。

# 6.每个

如果数组中的所有元素都通过了特定的真值测试，则该函数返回 true。如果有任何元素没有通过真值测试，它将停止遍历数组。

示例-

```
const _ = require('underscore');let nums = [1, 2, 3, 4, 5, 6];
_.every(nums, function(num) {
    return num % 2 == 0;
});// It will return false
```

# 7.一些

如果原始数组中至少有一个值通过了真值测试，则该函数返回 true。如果所有值都没有通过真值测试，则返回 false。它不断迭代数组中的所有元素，直到找到一个通过真值测试的元素。

示例-

```
const _ = require('underscore');let nums = [1, 2, 3, 4, 5, 6];
_.some(nums, function(num) {
		return num % 2 == 0;
});// It will return true
```

# 8.勇气

这个函数用于从一个对象数组中提取某些属性。它遍历数组中的每个对象，提取所需的属性，并返回它们的组合数组。

示例-

```
const _ = require('underscore');let castOfSiliconValley = [
{name: 'Richard', age: 31}, 
{name: 'Gilfoyle', age: 32}, 
{name: 'Dinesh', age: 29}
];
_.pluck(castOfSiliconValley , 'name');// This return => ["Richard", "Gilfoyle", "Dinesh"]
```

# 9.排序比

这个函数返回原始数组的排序副本，按升序排序。

示例-

```
const _ = require('underscore');let nums = [5, 2, 4, 1, 6, 3];
_.sortBy(nums, function(num) {
    return num;
});// It will return [1, 2, 3, 4, 5, 6]
```

# 10.分组依据

这个函数是根据一些常见的操作对数组中的原始元素进行分组。它通常在处理对象数组时使用，并且需要基于特定属性进行分组。

示例-

```
const _ = require('underscore');let cars = [
  {
    make: "Ferrari",
    color: "red"
  },
  {
    make: "Porche",
    color: "black"
  },
  {
    make: "mercedes",
    color: "red"
  },
  {
    make: "BMW",
    color: "white"
  }
];_.groupBy(cars, 'color');/*
It will return{
  red: [
    { make: 'Ferrari', color: 'red' },
    { make: 'mercedes', color: 'red' }
  ],
  black: [ { make: 'Porche', color: 'black' } ],
  white: [ { make: 'BMW', color: 'white' } ]    
}/*
```

# 结论

我希望这篇文章对你有所帮助。下划线. js 提供了更多的实用函数。您可以从他们的[文档](https://underscorejs.org)中查看完整的列表。

如果你喜欢这篇文章，也想阅读类似的作品，一定要在[推特](https://twitter.com/RahilSarvaiya)上关注我，我会在那里发布我的最新文章。请在评论区分享你对这篇文章的想法。如果你们对这篇文章或任何与下划线. js 相关的内容有任何建议，我很乐意知道。