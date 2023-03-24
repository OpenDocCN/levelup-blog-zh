# JavaScript 基础知识:真理和谬误

> 原文：<https://levelup.gitconnected.com/javascript-basics-truthy-and-falsy-d6971bdc8650>

![](img/3dd24bfcafaeb47be0d3fc594bf81d99.png)

当使用条件时，放在括号内的必须是返回 true 或 false 的值。括号内的值可以是布尔值`true`或`false`，或者您可以使用比较或逻辑运算符，如`<, >, or !==`。但是如果你想检查一个变量是否存在呢？你可以试试这个:

```
let variable = "over here";
if (variable === ""){
    // something something
}
```

或者更好，你也可以试试这个:

```
let variable = "over here";
if (variable){
    // something something
}
```

如果你想知道一个变量是否有任何类型的值，你可以在条件中的括号内只包含变量的名字。如果它有一个值，那么条件将评估该变量为真。如果变量没有赋值，它将返回 false，但是某些值被认为是 false。这些值包括:

*   `0`
*   空字符串
*   `null`
*   `undefined`
*   `NaN`

如果一个变量被赋予了一个数字，并且这个数字是一个`0`，那么条件将会把这个变量评估为 false。

如果您想看我以前的涉及 If 语句和比较值的文章，请查看这两篇文章:

[](/javascript-basics-comparison-and-logical-operators-7489f3a16b3f) [## JavaScript 基础:比较和逻辑运算符

### 今天我们要学习比较和逻辑运算符。在我们上一篇 JavaScript 基础文章中，我们学习了…

levelup.gitconnected.com](/javascript-basics-comparison-and-logical-operators-7489f3a16b3f) [](/javascript-basics-conditional-statements-95d1ed403039) [## JavaScript 基础:条件语句

### 今天我们将学习 JavaScript 中的条件句。你可以把条件句想象成这种情况是…

levelup.gitconnected.com](/javascript-basics-conditional-statements-95d1ed403039)