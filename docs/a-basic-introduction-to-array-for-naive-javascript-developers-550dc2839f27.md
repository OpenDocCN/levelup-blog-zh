# JavaScript 中数组方法概述

> 原文：<https://levelup.gitconnected.com/a-basic-introduction-to-array-for-naive-javascript-developers-550dc2839f27>

## 了解如何在 JavaScript 中操作数组

![](img/9baa7ad7450d9a9e447edecffc5f5deb.png)

图片由来自 Unsplash 的[paweczerwi ski](https://unsplash.com/@pawel_czerwinski?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)提供

让我们创建一个数组:

```
var numbers = [1,2,3,4,5]
```

使用索引来访问数组元素。索引从`0`开始。因此，如果我们想要访问第一个元素，那么我们需要使用索引`0`来访问。

```
numbers[0] // 1numbers[3] // 4numbers[9] // undefined
```

## **求数组的长度**

```
numbers**.length**
```

## **获取数组的最后一个元素**

```
let len = numbers.length;let index = len - 1;let lastElement = numbers[index]
```

## **在数组末尾添加一个元素**

```
numbers.push(6);numbers; // [1,2,3,4,5,6]
```

## **将元素添加到数组的开头**

```
numbers.unshift(0);numbers; // [0,1,2,3,4,5,6]
```

## **删除数组的最后一个元素**

```
numbers.pop()
```

## **删除数组的第一个元素**

```
numbers.shift()
```

## **删除特定索引处的元素**

```
let numbers = [1,2,3,4,5];let index = 2;numbers.splice(index,1);numbers; [1,2,4,5]
```

## **在特定索引处添加元素**

```
let numbers = [1,2,5];let index = 2;let elements = [3,4]numbers.splice(index,0, ...elements);numbers; [1, 2, 3, 4, 5]
```

## **更新元素**

```
var numbers = [1,2,2,4,5]numbers[2] = 3;numbers; // [1,2,3,4,5]
```

## **查找数组中是否有元素**

```
var numbers = [1,2,3,4,5]numbers.includes(1);// truenumbers.includes(10);// false
```

## **找到元素的索引**

```
var numbers = [1,2,3,4,5]numbers.indexOf(1); // 0numbers.indexOf(10); // -1(if element not present )
```

## **迭代一个数组**

```
var numbers = [1,2,3,4,5]for(let i = 0; i < numbers.length; i++) {
    console.log(**numbers[i]**);
}// example 1numbers.forEach(elem => console.log(elem));
```

## **反转一个数组**

```
var numbers = [1,2,3,4,5]numbers.reverse(); // [5, 4, 3, 2, 1]
```

## **过滤数组元素**

```
var numbers = [1,2,3,4,5];numbers.filter((elem) => elem > 3);// [4,5]
```

## **用函数映射所有元素并创建一个新数组**

```
var numbers = [1,2,3,4,5]; var doubledNum =  numbers.map(item => item *2 );doubledNum; // [2,4,6,8,10]
```

## **对数组进行排序**

```
let num= [1, 2, 3, 4, 5, 6];// sort in descending order
let descOrder = num.sort( **(a, b) => a > b ? -1 : 1** );console.log(descOrder); // output: [6, 5, 4, 3, 2, 1]

// sort in ascending order
let ascOrder = num.sort(**(a, b) => a > b ? 1 : -1**);console.log(ascOrder); // output: [1,2,3,4,5,6]
```

## 从数组中创建一个字符串

```
var test = ["This", "is", "a", "test."]test.join(" "); // **"This is a test."**
```

谢谢😊 🙏为了阅读📖。

关注我 [JavaScript Jeep🚙💨](https://medium.com/u/f9ffc26e7e69?source=post_page-----98efbae5e8aa----------------------)。

请在这里捐款[。你捐款的 80%捐给了需要食物的人🥘。提前感谢。](https://www.paypal.com/paypalme2/jagathishSaravanan)