# JavaScript 数组方法

> 原文：<https://levelup.gitconnected.com/javascript-array-methods-d7adeab5c294>

![](img/11ac991fe9b6f8a5507feb9a2b50d2ad.png)

格伦·卡斯滕斯-彼得斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我把常见的 JavaScript 数组方法和一些小的描述和例子放在一起，作为快速指南和简单的参考。

在讨论数组方法之前，我们先声明一个数组。

```
const items = [
  {
    id: 1,
    name: 'Cereal',
    price: 4
  },
  {
    id: 2,
    name: 'Action Figure',
    price: 7
  },
  {
    id: 3,
    name: 'Video Game',
    price: 50
  },
  {
    id: 4,
    name: 'DVD',
    price: 20
  },
  {
    id: 5,
    name: 'Tablet',
    price: 500
  },
  {
    id: 6,
    name: 'Laptop',
    price: 1000
  },
  {
    id: 7,
    name: 'Vacuum Cleaner',
    price: 200
  },
  {
    id: 8,
    name: 'Microwave',
    price: 200
  },
  {
    id: 9,
    name: 'Sunglasses',
    price: 25
  },
  {
    id: 10,
    name: 'TV',
    price: 750
  },
];
```

很好，让我们来看看方法。

## 地图方法

返回从提供的函数返回的值的数组。`map`方法对数组中的每个元素执行一次函数。它不会改变原始数组

```
const itemNames = items.map(item => item.name);console.log(itemNames);/*
[
  'Cereal',
  'Action Figure',
  'Video Game',
  'DVD',
  'Tablet',
  'Laptop',
  'Vacuum Cleaner',
  'Microwave',
  'Sunglasses',
  'TV'
]
*/
```

## 过滤方法

返回一个值数组，该数组传递来自所提供函数的条件语句。`filter`方法对数组中的每个元素执行一次函数。它不会改变原始数组。

```
const affordableItems = items.filter(item => item.price < 100);console.log(affordableItems);/*
[
  { id: 1, name: 'Cereal', price: 4 },
  { id: 2, name: 'Action Figure', price: 7 },
  { id: 3, name: 'Video Game', price: 50 },
  { id: 4, name: 'DVD', price: 20 },
  { id: 9, name: 'Sunglasses', price: 25 }
]
*/
```

## 查找方法

返回从提供的函数传递条件语句的数组中第一项的值。一旦函数返回 true，`find`方法返回`true`，并且不检查剩余的元素。它不会改变数组。

```
const foundItem = items.find(item => item.id === 3);console.log(foundItem);// { id: 3, name: 'Video Game', price: 50 }
```

## 一些方法

通过检查数组中的任何元素是否传递了所提供函数的条件语句，返回一个布尔值。一旦函数返回`true`，方法`some`返回`true`并且不检查剩余的元素。它不会改变数组。

```
const hasExpensiveItems = items.some(item => item.price >= 500);console.log(hasExpensiveItems);// true
```

## 每一种方法

通过检查数组中的所有元素是否都通过了所提供函数的条件语句，返回一个布尔值。一旦函数返回`false`，`every`方法返回`false`并且不检查剩余的元素。它不会改变数组。

```
const allItemsExpensive = items.every(item => item.price >= 500);console.log(allItemsExpensive);// false
```

## forEach 方法

对数组中的每个元素执行一个函数。它不会改变数组。

```
items.forEach(item => console.log(item.name));/*
Cereal
Action Figure
Video Game
DVD
Tablet
Laptop
Vacuum Cleaner
Microwave
Sunglasses
TV
*/
```

## 还原方法

将数组缩减为单个值。为数组中的每个元素执行一个提供的函数，它是`reduce`方法中的第一个参数。第二个参数是函数中使用的累加器变量的初始值。所提供的函数还需要两个参数—累加器变量和当前元素。随着每次迭代，累加器变成返回值，当循环完成时，累加器作为单个值返回。

```
const total = items.reduce((accumulatedTotal, item) => item.price + accumulatedTotal, 0);console.log(total);// 2756
```

在这个例子中，`reduce`函数遍历`items`数组，对每个元素执行所提供的函数。由于在`reduce method`中传递了第二个参数，`accumulatedTotal`的初始值为`0`。在每次迭代中，当前商品的价格被添加到`accumulatedTotal`中，然后返回`accumulatedTotal`。一旦循环完成，函数`reduce`将返回`accumulatedTotal`。

## 包括方法

通过检查数组是否包含其参数来返回布尔值。它不会改变数组。

```
const pets = ['dog', 'cat', 'rabbit', 'hamster', 'parrot', 'fish'];const isFishIncluded = pets.includes('fish');console.log(isFishIncluded);// true
```

## 排序方法

对数组中的项目进行排序。默认情况下，`sort`方法按照字母和升序将值排序为字符串。它改变了原来的数组。

我将在前面的`pets`数组中添加“doggy”来展示`sort`如何按照字符串的升序对`'dog'`和`'doggy'`进行排序。

```
const pets2 = ['doggy', 'dog', 'cat', 'rabbit', 'hamster', 'parrot', 'fish'];pets2.sort();console.log(pets2);// ['cat', 'dog', 'doggy', 'fish', 'hamster', 'parrot', 'rabbit']
```

注意`'dog'`排在`'doggy'`之前。两个字符串都以相同的三个字母开头，但是由于`'dog'`的长度较短，所以它首先按升序排序。

这对于字符串很有效，然而，`sort`方法的默认行为是将数字作为字符串排序，而不是按数字排序。例如，“20”比“100”大，因为`sort`方法将“2”与“1”进行比较。为了解决这个问题，`sort`方法接受一个函数来比较元素。

```
const numbers = [20, 100, 55, 8, 13];// sort in ascending order
numbers.sort((a, b) => a - b);console.log(numbers);// [ 8, 13, 20, 55, 100 ]// sort in descending order
numbers.sort((a, b) => b - a);console.log(numbers);// [ 100, 55, 20, 13, 8 ]
```

## 连接方法

以字符串形式返回数组。它接受一个用作分隔符的可选参数。如果没有给定，则使用默认分隔符`,`——逗号后面没有空格。如果不需要分隔符，可以传入一个空字符串。它不会改变数组。

```
const drinks = ['Juice', 'Milk', 'Water', 'Coffee', 'Tea'];console.log(drinks.join());// Juice,Milk,Water,Coffee,Teaconsole.log(drinks.join(' and '));// Juice and Milk and Water and Coffee and Teaconsole.log(drinks.join(''));// JuiceMilkWaterCoffeeTea
```

## 反向方法

反转数组中元素的顺序。它改变了原来的数组。

```
const drinks = ['Juice', 'Milk', 'Water', 'Coffee', 'Tea'];console.log(drinks.reverse());// [ 'Tea', 'Coffee', 'Water', 'Milk', 'Juice' ]
```

就是这样！希望你学到了新的东西，或者对你已经知道的东西有了更深的理解。如果需要，请随意参考。感谢阅读！做好人。