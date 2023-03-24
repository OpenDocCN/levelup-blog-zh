# JavaScript 基础:三元运算符

> 原文：<https://levelup.gitconnected.com/javascript-basics-ternary-operator-c5e1510c6d2>

![](img/d9c83f75184f19b1ad57da9420ad50e3.png)

在编写 if 语句时，也有一种使用三元运算符来缩短它的方法。

这是一个常规的 if 语句

```
if(3 > 4){
    console.log("true");
}else{
    console.log("false");
}
```

下面是使用三元运算符的相同 if 语句:

```
3 > 4 ? console.log("true") : console.log("false");
```

如果我们把这个分解开来:

`3 > 4 ?`

`if (3 > 4)`

你在括号里写的东西在问号前面。

问号后面是什么，你可以把它看做花括号里面的项目。冒号的行为类似于 if 语句中的`else`。

```
? console.log("true") : console.log("false");
```

```
{
  console.log("true");
}else{
  console.log("false");
}
```

你也可以这样理解:

```
If condition ? print out this : or else print out that
```

如果您有一个基本的 if-else 语句，三元运算符就很有用。一旦引入 if-else if-else if-else 语句，坚持使用通常的方式编写 if-else 语句可能更好(也更容易阅读)。

![](img/ccabb49250dd5f6d9dc711ff2404bf3f.png)

但是说到三元运算符，这也适用于真真假假的求值。

如果您想根据变量是否已经有值来给变量赋值，您可以像这样编写标准的 If 语句:

```
let name;if(animal){
    name = animal;
}else{
    name = "space alien";
}
```

或者，您可以使用短路评估，并像这样编写上面的相同语句:

```
let name = animal || "space alien";
```

JavaScript 将根据右边的条件返回什么来分配变量`name`的值。

下面是正在发生的事情的伪代码:

```
let name = (if animal has a value, assign that value to name) **||** (if animal has no value or is a falsy, assign "space alien" to name);
```

[](/javascript-basics-truthy-and-falsy-d6971bdc8650) [## JavaScript 基础知识:真理和谬误

### 当使用条件时，放在括号内的必须是返回 true 或 false 的值。的…

levelup.gitconnected.com](/javascript-basics-truthy-and-falsy-d6971bdc8650) [](/javascript-basics-comparison-and-logical-operators-7489f3a16b3f) [## JavaScript 基础:比较和逻辑运算符

### 今天我们要学习比较和逻辑运算符。在我们上一篇 JavaScript 基础文章中，我们学习了…

levelup.gitconnected.com](/javascript-basics-comparison-and-logical-operators-7489f3a16b3f)