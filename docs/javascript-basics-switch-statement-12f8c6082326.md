# JavaScript 基础:Switch 语句

> 原文：<https://levelup.gitconnected.com/javascript-basics-switch-statement-12f8c6082326>

![](img/bfccbf0110a4a638ae3c91a7eda1e961.png)

当编写 if 语句时，您可能想要包含几个`else if`语句，但是如果您想要包含一串`else if`语句，如下所示:

```
let color = "blue";if (color === "red"){
    console.log("The color is red.");
} else if (color === "orange"){
    console.log("The color is orange.");
} else if (color === "blue"){
    console.log("The color is blue.");
} else if (color === "grey"){
    console.log("The color is grey.");
} else if (color === "black"){
    console.log("The color is black.");
}else if ....} else {
    console.log("Invalid Color")
}
```

如你所见，如果火车失控。另一种更容易读写的方法是 switch 语句。

使用 switch 语句，上面的示例被改写为:

```
let color = "blue";switch (color){
    case: "red":
        console.log("The color is red.");
        break;
    case: "orange":
        console.log("The color is orange.");
        break;
    case: "blue":
        console.log("The color is blue.");
        break;
    case: "grey":
        console.log("The color is grey.");
        break;
    case: "black":
        console.log("The color is black.");
        break;
    default:
        console.log("Invalid Color");
        break;
}
```

与我们的 else-if 训练相比，switch 语句有更少的花括号和圆括号，这意味着更少的输入和更多的关注值。让我们来分析一下 switch 语句是如何工作的。

要使用 switch 语句，您可以使用`switch`关键字，括号内是您想要评估和比较的变量。

在花括号内，您想要比较的值后面跟有`case`关键字。在上面的例子中，我们想要与当前的`color`变量进行比较的值是`red`。这将检查当前`color`变量的值是否等于`red`。如果为真，那么特定情况的代码将打印出`The color is red.`。在打印出消息后，`break`语句将告诉程序退出 switch 块，并且不执行任何其他情况。

switch 语句将继续查看每种情况，直到没有其他要评估的情况，当这种情况发生时，将运行`default`关键字下的代码。当上面的所有条件都不满足时，这类似于 if 语句中的关键字`else`。

注意:如果省略 break 关键字，程序将继续运行并遍历所有情况，直到到达默认关键字并结束。

这里有一些其他的 JavaScript 基础文章，你可以看看:

[](/javascript-basics-ternary-operator-c5e1510c6d2) [## JavaScript 基础:三元运算符

### 在编写 if 语句时，也有一种使用三元运算符来缩短它的方法。

levelup.gitconnected.com](/javascript-basics-ternary-operator-c5e1510c6d2) [](/javascript-basics-truthy-and-falsy-d6971bdc8650) [## JavaScript 基础知识:真理和谬误

### 当使用条件时，放在括号内的必须是返回 true 或 false 的值。的…

levelup.gitconnected.com](/javascript-basics-truthy-and-falsy-d6971bdc8650)