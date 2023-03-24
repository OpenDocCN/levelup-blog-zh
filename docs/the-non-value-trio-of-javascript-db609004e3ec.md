# JavaScript 的无价值三重奏

> 原文：<https://levelup.gitconnected.com/the-non-value-trio-of-javascript-db609004e3ec>

## 揭开未定义、Null 和 NaN 的神秘面纱

![](img/b0f3a8cd9811c0c570368de8c1cceaf0.png)

照片由[克里斯原子](https://unsplash.com/@krisatomic)在 [Unsplash](https://unsplash.com/photos/m5RYZ2s2ZCA) 上拍摄

Undefined、Null 和 NaN 是三个关键字，称为非值或空值，对于许多初学 JavaScript 的工程师来说，它们常常是错误代码、压力和困惑的来源。依我拙见，部分原因是它们在大多数课程或书籍中很少得到应有的重视。在这里，我们将试图给这个三重奏以应有的重视，并揭开它的神秘面纱。

## 不明确的

这个关键字在 JavaScript 中既是数据类型又是值。它是六种原始数据类型之一，其他的有:`string`、`number`、`boolean`、`null`和`symbol` (ES5+)。默认情况下，*被分配给任何尚未定义值的变量*。在更深的层次上，这意味着变量存在，但其不存在的值已被自动赋值为`*undefined*`。JavaScript 是一种动态类型语言，这意味着 JS 引擎根据分配给它的值的数据类型来确定变量的类型。在这个上下文中，`undefined`实际上意味着 *JavaScript 不知道所声明变量的数据类型，因为它还没有用值*初始化。

> 任何未赋值的变量都会被自动赋予 undefined 的值和类型

我们可以用下面的代码来说明这一点。

```
let myVar;  //declared but left unassigned
console.log(myVar)
```

输出:`undefined`

`undefined`也是一种数据类型，因此有如下说法:

```
console.log(typeof myVar)
```

输出:`"undefined"`

注意到`undefined`的值和类型之间有一点点不同；后者在引号中。你可能在面试问题中发现的一个问题是:

**`**typeof typeof undefined**`**的值是多少？****

**答案是:`string`**

**这意味着我们可以键入以下内容:**

**`console.log(typeof myvar + “ variable”)`**

**输出:`undefined variable`**

**我们因此可以说，在 JavaScript 中，*的数据类型* `*undefined*` *的数据类型是* `*string*`！**

**对于函数，如果返回值未赋值，则返回`undefined`。更重要的是，如果没有返回值，函数也会返回`undefined`。**

```
function f(){
   return;
}console.log(f())
```

**输出:`undefined`**

## **空**

**`Null`也是 JavaScript 中的原始数据类型，也可以赋值。然而，与`undefined`不同的是，它的数据类型是`Object`。**

```
let myVar = null;
console.log(myVar)
```

**输出:`null`**

```
Console.log(typeof myVar);
```

**输出:`Object`**

> ****null** 是一个有意的值，表示缺少预期的对象值**

**`null`用来故意给某物一个空值。不满足条件时让函数返回 null 是常见的做法。例如，JavaScript 的内置`match()`函数返回匹配给定字符串模式的`object`，否则返回`null`。**

```
console.log( "Abe".match(/[aeiou]/gi). )
```

**输出:`[ ‘A’, ‘e’ ]`**

```
console.log( "PS4".match(/[aeiou]/gi). )
```

**输出:`null`**

**如果找到匹配，则`match()`的返回值是一个数组(一个对象),否则`null`返回。因此，`null`是一个预期的 ***对象*** 值的有意缺失。理解 null 与对象一起使用是很重要的。**

**`undefined`与`null`的主要区别在于:**

*   **`undefined`表示变量已知，但其值未知，因此其数据类型未知**
*   **`null`表示变量的数据类型是已知的(`Object`)，但是它的值*故意留为未知(空)，因为没有对象与给定的环境相关。***
*   **当数值未知时，`undefined`是否被自动分配给**
*   ***`null`是*故意分配的*，当没有相关对象时***

***Null 代表*虚无*或*不存在*，可以认为等同于 0。事实上，在一些语言如 C++和 Java 中，是这样的。***

***我们来看几个与`null`和`undefined`相关的怪癖。***

```
*null === undefined   // false
null  == undefined   // true*
```

***第一条语句检查左边的值是否与右边的值完全一样。这就是' *three* quals' (===)运算符的作用。对于 double-equals，因为 JavaScript 是动态类型的，所以当它看到两个值时，它会试图找出它们的数据类型。如果两个值不属于同一类型，它会将其中一个值转换或*强制*(或*强制*)为另一个值的数据类型，*才会进行比较。例如，如果比较一个`string`和一个`number`，JavaScript 会将前者强制转换为后者。这就是为什么`0 == ‘0’`和`‘0’ == false`是真的。将`0`转换为类型`number`，然后进行比较，返回 true。类似地，由于类型强制，`null`双倍等于`undefined`。请记住，这是 JavaScript 特有的怪癖；在大多数语言中，这样一个令人困惑的概念并不存在。****

## ***圆盘烤饼***

***它代表“不是一个数字”，代表*没有有效数字*。这是无效计算的结果，例如对`string`而不是`number`进行算术运算。我们来看几个例子。***

```
*1 * 'five'
'ten' / 2*
```

***输出:`NaN`***

```
*let myVar = 'four'
console.log( Math.sqrt(myVar) )*
```

***输出:`NaN`***

***注意，由于类型强制，像`‘5’ * 5`或`Math.sqrt(‘4’)`这样的操作在 JavaScript 中都是有效的。然而，在类型强制不适用的情况下，那些操作将总是产生`NaN`。***

> ***`NaN`是由无效数学运算引起的错误的关键字***

***让我们来看看这个关键字的一些奇怪之处。***

```
*NaN == NaN*
```

***输出:`false`***

***有时候发现一个操作是否有效是很有用的。***

```
*function checkIfValidOperation(operation){
     if (operation === NaN)
         console.log("Invalid Operation!");
     else
         console.log("Valid Operation!");
}let myOp = Math.sqrt('four');    //NaNcheckIfValidOperation(myOp)      //???*
```

***输出:`Valid Operation!`***

***出乎意料！`myOp`不等于`NaN`吗！？是的，但是比较是`false`。这是因为在 JavaScript 中:***

***`NaN` === `NaN`***

***输出:`false`***

***这是这种语言的一个怪癖。为了测试一个操作是否产生`NaN`，我们需要使用内置函数`isNaN()`。***

```
*isNaN(myOp);*
```

***输出:`true`***

## ***结合这一切***

***结合我们的非价值三要素，我们可以提取额外的怪癖，例如:***

```
*1 + null + 1*
```

***输出:`2`***

***`null + null`***

***输出:`0`***

***那是因为`null`受到了和`0`一样的待遇。***

```
*undefined + 1
null + undefined*
```

***输出:`NaN`***

***那是预期行为。***

## *****外卖*****

***我们可以将上述总结如下:***

*   ***`undefined`表示变量存在(已声明)，但其数据类型还不存在，因为它未赋值***
*   ***`undefined`在 JavaScript 中既是值又是数据类型***
*   ***`undefined`的数据类型是`‘undefined’`，是一个`string`***
*   ***`null`表示变量的数据类型存在，但其值故意留空***
*   ***在 JavaScript 中，null 既是值也是数据类型***
*   ***`null`的数据类型为`Object`***
*   ***`null`与物体结合使用***
*   ***`null`象征*无*并被视为`0`***
*   ***`undefined`是自动的，`null`是故意的***
*   ***`NaN`意味着一个数学运算无效***
*   ***在比较值时使用 *threequals* 运算符(===)来避免类型强制被认为是一种好的做法***

***感谢您的阅读！编码快乐！:)***