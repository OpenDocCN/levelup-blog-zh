# JavaScript 语言入门指南

> 原文：<https://levelup.gitconnected.com/a-beginners-guide-to-in-javascript-15bebffce2c>

![](img/3617be31e92223bbacdcb7b33d236fd9.png)

# 什么是…？

‘…’或 spread 操作符是 JavaScript 中一个有用的语法工具。它可用于:

*   函数调用
*   数组/字符串
*   休息参数

让我们来看一下如何在提到的每一种用途中使用它。

# 函数调用

## 1.使用数组的“新建”对象

传统上，您不能使用“new”关键字直接使用数组创建对象。我说的是类似于`new Date(array)`(一个新的日期对象)的东西。在构造函数中使用数组是无效的，但要与“...”一起使用，就有可能:

```
const date = [2020, 0, 1];  // 1 Jan 2020
const dateObj = new Date(...date);console.log(dateObj);
// VM60:1 Wed Jan 01 2020 00:00:00 GMT-0500 (Eastern Standard Time)
```

## 2.“apply()”方法

“…”可以像 JavaScript 中的`apply()`方法一样使用。

例如，不用`apply()`:

```
const array = ['a', 'b'];
const elements = [0, 1, 2];
array.push.apply(array, elements);
console.info(array); // ["a", "b", 0, 1, 2]
```

您可以使用“…”来获得更简洁的语法，如下所示:

```
const array = ['a', 'b'];
const elements = [0, 1, 2];
array.push(...elements);
console.info(array); // ["a", "b", 0, 1, 2]
```

> *关于* `*apply()*` *如何工作的更多细节，你可以在*[*w3schools.com/js/js_function_apply.asp*](https://www.w3schools.com/js/js_function_apply.asp)*查阅。*

## 3.作为函数参数的数组

您还可以使用 spread 运算符提供一个数组作为函数参数。

```
function sum(x, y, z) {
  return x + y + z;
}
const num = [1, 2, 3];
console.log(sum(...num)); //6
```

# 数组/字符串

## 1.复制数组

这是使用 spread 运算符最有用的方法之一。使用以下工具轻松复制阵列:

```
const arr = [1, 2, 3];
const arr2 = [...arr];
```

而现在，arr2 瞬间变成了 arr 的副本！您对其中一个数组所做的任何更改都不会影响到另一个数组！

> *重要提示:“…”仅复制一级深度的数组。如果多维数组中的任何一个发生了变化，它们都会受到影响！*

## 2.插入新元素

不使用像`splice()`和`concat()`这样的方法，spread 操作符提供了一种更简单的方法，可以在数组中的现有元素之间轻松插入新元素。

这里有一个简短的例子:

```
const middle = [3, 4]; 
const numList = [1, 2, ...middle, 5]; 
console.log(numList); //  [1,2,3,4,5]
```

> 关于 `*splice()*` *如何工作的更多细节，你可以在 w3schools.com/jsref/jsref_splice.asp*[*阅读。*](https://www.w3schools.com/jsref/jsref_splice.asp)

## 3.组合两个数组

不用`concat()`，可以用“...”要像这样连接数组:

```
let numList = [1, 2];
let arr2 = [3, 4, 5];numList = [...numList, ...arr2]; 
console.log(numList); //  [1,2,3,4,5]
```

> *关于* `*concat()*` *如何工作的更多细节，你可以在*[*w3schools.com/jsref/jsref_concat_array.asp*](https://www.w3schools.com/jsref/jsref_concat_array.asp)*查阅。*

# 休息参数

rest 参数将不定数量的参数表示为一个数组。在函数中，只有最后一个参数可以是 rest 参数。说明这一点的一个很好的例子是:

```
function myFun(a, b, ...manyMoreArgs) {
  console.log("a", a)
  console.log("b", b)
  console.log("manyMoreArgs", manyMoreArgs)
}myFun("one", "two", "three", "four", "five", "six")// Console Output:
// a, one
// b, two
// manyMoreArgs, [three, four, five, six]
```

【developer.mozilla.org/en-US/docs/Web/JavaSc..】[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)**)**

*如上所示，`...manyMoreArgs`具有用户将提供的未知且不确定的长度。在这种情况下，用户提供 4，因此，这些参数被表示为一个包含 4 个元素的数组。*

# *这就是…的力量*

*我希望现在您理解了 spread 操作符的强大功能，以及它在 JavaScript 中是多么有用！将它整合到您的项目中可以帮助您学习如何将它用于各种用途！像删除数组中的重复项一样，替换`unshift()`和各种常见的数组方法等等！*

*请在下面分享你的问题或想法。感谢阅读！干杯！*