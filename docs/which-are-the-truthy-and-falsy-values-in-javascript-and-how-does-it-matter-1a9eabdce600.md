# Javascript 中的真值和假值是什么，这意味着什么

> 原文：<https://levelup.gitconnected.com/which-are-the-truthy-and-falsy-values-in-javascript-and-how-does-it-matter-1a9eabdce600>

## 理解这个概念将有助于您编写更好、更简洁的代码

![](img/6a72c2d1762f61ed6ceb3d863cbf166f.png)

照片由 [Goran Ivos](https://unsplash.com/@goran_ivos?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

你知道 Javascript 有一组预定义的 falsy 值吗？

**Truthy 和 falsy 值是在执行特定操作时强制为真或假的非布尔值。**

以下是在 Javascript 中被视为假值的**仅有的六个**值。

*   `false`
*   `undefined`
*   `null`
*   `""`
*   `NaN`
*   `0`

任何不在上面列表中的东西都被认为是真实的。

因此，如果你正在编写一个`if`条件，你不需要检查某个东西是否为空、未定义或 null。会自动认为是`false`。

```
const value = "";
// this condition is enough and no need to check value == ""
if(value) {
  console.log(value); // This code will not be executed
}const nullValue = null;
// this condition is enough and no need to check value === null
if(nullValue) { 
   console.log(nullValue); // This code will not be executed
}let sum;
// this condition is enough and no need to check value === undefined
if(sum) { 
   console.log(sum); // This code will not be executed
}
```

检查一个值是真还是假的简单方法是将它传递给`Boolean`函数

```
Boolean(NaN) // false
Boolean([]) // true
Boolean({}) // true
```

通过翻转任意值两次，可以将其转换为 true 或 falsy 布尔值:

```
let number1;
console.log(!!number1); // falseconst number2 = 10;
console.log(!!number2); // trueconst name1 = 'Tim';
console.log(!!name1); // trueconst name2 = '';
console.log(!!name2); // falseconst nullValue = null;
console.log(!!nullValue); // false
```

这有一些实际应用。

## 应用真值和假值

假设您正在解析一个 CSV 文件，并且每一列都包含逗号分隔的名称、数字和一些空值。有一些空格，所以当您解析每一行时，您可能会得到如下内容:

```
const parsedData = 'David,Mike,Tim,,John, 10,, Jonathan';
```

你想只打印有效的值，所以在这种情况下你可以这样做

```
const parsedData = 'David,Mike,Tim,,John, 10,, Jonathan';console.log(
 parsedData
  .split(',')
  .map(value => value.trim())
  .filter(value => !!value)
  .join(' ')
); // David Mike Tim John 10 Jonathan
```

这可以进一步简化为

```
const parsedData = 'David,Mike,Tim,,John, 10,, Jonathan';console.log(
 parsedData
  .split(',')
  .map(value => value.trim())
  .filter(**Boolean**)
  .join(' ')
); // David Mike Tim John 10 Jonathan
```

如果你有一组不同的值，比如

```
const arr = [10, 20, "raj", 0, [], '', NaN, 3, undefined, 50, null, 89];
```

您可以通过以下方式获得有效值

```
const arr = [10, 20, 'raj', 0, [], '', NaN, 3, undefined, 50, null, 
89];console.log(arr.filter(Boolean)); //[ 10, 20, 'raj', [], 3, 50, 89 ]
```

今天到此为止。希望你今天学到了新东西。

**别忘了直接在你的收件箱** [**这里**](https://yogeshchavan.dev) **订阅我的每周时事通讯，里面有惊人的技巧、诀窍和文章。**