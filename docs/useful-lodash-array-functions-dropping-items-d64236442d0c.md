# 有用的 Lodash 数组函数来删除项目

> 原文：<https://levelup.gitconnected.com/useful-lodash-array-functions-dropping-items-d64236442d0c>

![](img/c44e26e52797de9e6508f341c37d0b87.png)

[吉米·张](https://unsplash.com/@photohunter?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Lodash 是一个实用程序库，它有很多操作数组的方法。在本文中，我们将看看有用的 Lodash 数组方法，包括`drop`、`dropRight`、`dropRightWhile`和`dropWhile`。

# 滴

`drop`方法创建一个数组切片，从开始处删除第一个元素。

它有两个参数，这两个参数是要从中取值的数组，以及第一个参数中要从数组开始处删除的元素数。

它返回一个新数组，从开始处删除指定数量的元素。

例如，我们可以如下使用它:

```
import * as _ from "lodash";const arr = [1, 2, 3, 4, 5];
const result = _.drop(arr, 2);
```

然后我们将`[3, 4, 5]`分配给`result`。

如果数字大于数组的长度，那么我们得到一个空数组。例如，我们可以写:

```
import * as _ from "lodash";const arr = [1, 2, 3, 4, 5];
const result = _.drop(arr, 10);
```

然后我们将`[]`分配给`result`。

# `dropRight`

`dropRight`方法类似于`drop`方法，但是返回一个从右边而不是从左边放置项目的数组。

它有两个参数，第一个参数是一个从中取值的数组，第二个参数是要从数组末尾删除的元素数。

例如，我们可以这样写:

```
import * as _ from "lodash";const arr = [1, 2, 3, 4, 5];
const result = _.dropRight(arr, 2);
```

然后我们得到`result`的`[1, 2, 3]`。

如果数字大于数组的长度，那么我们得到一个空数组。例如，我们可以写:

```
import * as _ from "lodash";const arr = [1, 2, 3, 4, 5];
const result = _.dropRight(arr, 10);
```

然后我们将`[]`分配给`result`。

# `dropRightWhile`

`dropRightWhile`方法返回一个数组，从末尾开始的项被删除，直到我们传入的`predicate`函数返回 falsy。

它需要两个参数。第一个是我们要从中取值并从右边移除元素的数组。第二个参数是`predicate`函数，它将数组条目作为参数，并在我们希望停止移除项目时返回我们希望为 falsy 的条件。

例如，给定以下数组:

```
const people = [
  { name: "Mary", age: 10 },
  { name: "Jane", age: 15 },
  { name: "John", age: 20 }
];
```

那么我们可以使用`dropRightWhile`如下:

```
import * as _ from "lodash";
const people = [
  { name: "Mary", age: 10 },
  { name: "Jane", age: 15 },
  { name: "John", age: 20 }
];
const result = _.dropRightWhile(people, p => p.age > 15);
console.log(result);
```

上面的代码将复制一个`people`数组，从末尾开始删除大于 15 的条目，并返回结果数组。

然后我们得到:

```
[
  {
    "name": "Mary",
    "age": 10
  },
  {
    "name": "Jane",
    "age": 15
  }
]
```

![](img/b2121c8579cc74cc2f7a1ca6a9d7901d.png)

照片由 [Aaron Burden](https://unsplash.com/@aaronburden?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# `dropWhile`

`dropWhile`类似于`dropRightWhile`。唯一的区别是，它从开始处移除满足给定条件的项，而不是从右边移除，并返回。

采取的论据同`dropRightWhile`。

例如，给定以下数组:

```
const people = [
  { name: "Mary", age: 10 },
  { name: "Jane", age: 15 },
  { name: "John", age: 20 }
];
```

那么我们可以如下使用`dropRightWhile`:

```
import * as _ from "lodash";
const people = [
  { name: "Mary", age: 10 },
  { name: "Jane", age: 15 },
  { name: "John", age: 20 }
];
const result = _.dropWhile(people, p => p.age < 15);
console.log(result);
```

上面的代码将复制一个`people`数组，从开始处删除`age`小于 15 的条目，并返回结果数组。

然后我们得到:

```
[
  {
    "name": "Jane",
    "age": 15
  },
  {
    "name": "John",
    "age": 20
  }
]
```

作为分配给`result`的值。

`drop`创建一个数组的切片，从开始处删除第一个元素。如果从原始数组中删除的数字大于数组的长度，那么它将返回一个空数组。

`dropRight`与`drop`相同，除了切片是通过移除从末端开始的项目创建的。

`dropRightWhile`返回一个数组，从末尾开始的项被删除，直到我们传入的谓词函数返回 falsy。`dropWhile`与`dropRightWhile`相似，只是谓词是从左边开始计算的。