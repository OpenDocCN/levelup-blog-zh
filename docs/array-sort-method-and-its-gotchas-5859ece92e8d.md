# JavaScript 数组排序方法及其陷阱

> 原文：<https://levelup.gitconnected.com/array-sort-method-and-its-gotchas-5859ece92e8d>

## 理解数组排序方法及其令人惊讶的行为

![](img/46eaf2499e8108ad92b17d05a4de2689.png)

照片由[hello queue](https://unsplash.com/@helloquence?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

Array sort 方法对数组进行排序，而不考虑其数据类型，无论是编号数组、字符串数组还是复杂的对象数组。

排序方法具有以下语法:

```
array.sort([compareFunction])
```

在这里，`compareFunction`是可选的。

**需要注意的一点是，数组排序方法并不返回一个新的排序后的数组，而是改变原来的数组。**

```
const months = ["Nov", "Feb", "Jan", "Dec" ];
const sorted = months.sort(); // sort the original array and return reference to it

console.log(months); // ["Dec", "Feb", "Jan", "Nov"]
console.log(sorted); // ["Dec", "Feb", "Jan", "Nov"]
```

演示:[https://codepen.io/myogeshchavan97/pen/yLyQbMJ?editors=0011](https://codepen.io/myogeshchavan97/pen/yLyQbMJ?editors=0011)

如果您使用`===`比较月份和排序变量，您可以看到它们指向同一个数组，即原始排序数组。

*这是一个很有名的面试问题，用来检验应聘者是否知道数组排序方法。*

如果不想改变原来的数组，可以在 sort 方法前加一个 slice 方法。

slice 方法返回数组的一个新的浅表副本。

```
const months = ["Nov", "Feb", "Jan", "Dec" ];
const sorted = months.slice().sort(); // returns a new sorted array

console.log(months); // ["Nov", "Feb", "Jan", "Dec"]
console.log(sorted); // ["Dec", "Feb", "Jan", "Nov"]
console.log(months === sorted); // false
```

演示:[https://codepen.io/myogeshchavan97/pen/zYxMwPE?editors=0011](https://codepen.io/myogeshchavan97/pen/zYxMwPE?editors=0011)

**默认情况下，sort 方法将数组排序为字符串。**

这对于按字母顺序排序的字符串来说没问题，但是当我们对数字进行排序时会产生不正确的结果。

```
const numbers = [100, 25, 1, 5 ];
const sorted = numbers.slice().sort(); // returns a new sorted arrayconsole.log(numbers); // [100, 25, 1, 5 ]
console.log(sorted); // [1, 100, 25, 5]
```

演示:[https://codepen.io/myogeshchavan97/pen/MWYzmQp?editors=0011](https://codepen.io/myogeshchavan97/pen/MWYzmQp?editors=0011)

正如您在排序后的数组中看到的，5 排在最后，100 排在 25 之前，尽管 100 大于 25 和 5。

这是因为在对数字进行排序时，每个数字都被转换为一个字符串。排序方法逐个字符进行比较，因此“100”中的“1”比“25”中的“2”小，因此在按升序排序时，100 在 25 之前。

这是程序员经常忘记的常见问题。

我们可以通过将比较函数传递给排序方法来解决这个问题

```
const numbers = [100, 25, 1, 5];
const sorted = numbers.slice().sort(function(a, b) { 
 return a - b; 
}); // returns a new sorted arrayconsole.log(numbers); // [100, 25, 1, 5]
console.log(sorted); // [1, 5, 25, 100]
```

演示:[https://codepen.io/myogeshchavan97/pen/MWYzmQp?editors=0011](https://codepen.io/myogeshchavan97/pen/MWYzmQp?editors=0011)

当比较 100 和 25 时，`sort()`方法将 100 - 25 计算为 75，这是一个正值，所以它将 25 放在 100 之前。

这将按升序对数组进行排序。

为了按降序排序，我们需要对 compare 函数做一点小小的修改。

```
const numbers = [100, 25, 1, 5];
const sorted = numbers.slice().sort(function(a, b) { 
 return **b - a;** 
}); // returns a new sorted arrayconsole.log(numbers); // [100, 25, 1, 5]
console.log(sorted); // [100, 25, 5, 1]
```

演示:[https://codepen.io/myogeshchavan97/pen/bGNQRdB?editors=0011](https://codepen.io/myogeshchavan97/pen/bGNQRdB?editors=0011)

**所以在对数字进行排序时，一定要传递 compare 函数，否则你会得到一个不想要的结果。**

## **排序函数排序时使用的规则:**

1.如果第一个值按字母顺序排在第二个值之前，则返回负值。
2。如果第一个值按字母顺序排在第二个值之后，则返回正值。
3。如果第一个和第二个值相等，则返回零。

让我们编写一个代码，按照用户的姓氏对他们进行排序

```
const users = ["David Jonhnson", "Tim Lindholm", "David Beckham", "Johnny Depp" ];
const sorted = users.slice().sort(function(firstUser, secondUser) { 
 const firstLastName = firstUser.split(" ")[1];
 const secondLastName = secondUser.split(" ")[1];
 if(firstLastName < secondLastName) return -1;
 if(firstLastName > secondLastName) return 1;
 return 0;
});

console.log(users); // ["David Jonhnson", "Tim Lindholm", "David Beckham", "Johnny Depp"]
console.log(sorted); // ["David Beckham", "Johnny Depp", "David Jonhnson", "Tim Lindholm"]
```

演示:[https://codepen.io/myogeshchavan97/pen/XWJyRoZ?editors=0011](https://codepen.io/myogeshchavan97/pen/XWJyRoZ?editors=0011)

要对数组进行随机排序，我们可以使用 Math.random 方法

```
const numbers = [100, 25, 1, 5 ];
const sorted = numbers.slice().sort(function(a, b) { 
 return 0.5 - Math.random() 
}); // returns a new sorted arrayconsole.log(numbers); // [100, 25, 1, 5]
console.log(sorted); // array in random order
```

演示:[https://codepen.io/myogeshchavan97/pen/MWYzoKQ?editors=0011](https://codepen.io/myogeshchavan97/pen/MWYzoKQ?editors=0011)

**对对象数组进行排序:** 假设我们有一个用户数组，其中每个用户对象都包含一个姓名和年龄属性，我们希望根据用户的年龄对数组进行升序排序。我们可以这样做:

```
const users = [{
  name: 'David',
  age: 30
},
{
  name: 'Mike',
  age: 40
},
{
  name: 'John',
  age: 25
}];
const sorted = users.slice().sort(function(firstUser, secondUser) { 
 if(firstUser.age < secondUser.age) return -1;
 if(firstUser.age > secondUser.age) return 1;
 return 0;
});

console.log(users); // [{'name':'David','age':30},{'name':'Mike','age':40},{'name':'John','age':25}]
console.log(sorted); // [{'name':'John','age':25},{'name':'David','age':30},{'name':'Mike','age':40}]
```

演示:[https://codepen.io/myogeshchavan97/pen/bGNQRwg?editors=0011](https://codepen.io/myogeshchavan97/pen/bGNQRwg?editors=0011)

要按年龄降序对数组进行排序:

```
const users = [{
  name: 'David',
  age: 30
},
{
  name: 'Mike',
  age: 40
},
{
  name: 'John',
  age: 25
}];const sorted = users.slice().sort(function(firstUser, secondUser) { 
 if(firstUser.age < secondUser.age) **return 1;**
 if(firstUser.age > secondUser.age) **return -1;**
 return 0;
});console.log(users); // [{'name':'David','age':30},{'name':'Mike','age':40},{'name':'John','age':25}]
console.log(sorted); // [{'name':'Mike','age':40},{'name':'David','age':30},{'name':'John','age':25}]
```

演示:[https://codepen.io/myogeshchavan97/pen/bGNQrGv?editors=0011](https://codepen.io/myogeshchavan97/pen/bGNQrGv?editors=0011)

今天到此为止。希望你今天学到了新东西。

**别忘了直接在你的收件箱** [**这里**](https://yogeshchavan.dev) **订阅我的每周时事通讯，里面有惊人的技巧、诀窍和文章。**