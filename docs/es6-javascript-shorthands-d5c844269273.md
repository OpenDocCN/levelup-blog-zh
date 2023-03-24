# ES6 Javascript 速记

> 原文：<https://levelup.gitconnected.com/es6-javascript-shorthands-d5c844269273>

## 用 Javascript 提高生产力

![](img/a8bd7b53934620b66f2acb0293fd56d9.png)

卢卡·斯拉普尼卡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

无论你打字有多快，或者你对快捷键有多熟悉，你的编码效率仍然岌岌可危！

编码是一项日常实践。一种从内部重塑和改造自己的技能。所以熟练使用速记肯定有助于提高生产率、时间效率和代码质量！

# 短手

速记是捷径。与键盘快捷键不同，这些快捷键简化了简单的逻辑，节省了代码行，从而节省了时间。

下面是一些简单的日常 javascript 任务，让我们有效地使用 shorthands 来改进代码的开发时间、性能、质量和效率。

## 声明变量

我们倾向于在多行代码中定义和声明变量，这限制了可写性和可读性。并且需要花费大量时间来单独定义它们。

```
// long hand
let a;
let b = 1;
let c = 3;
```

因此，最好在一行中轻松地声明和定义它们，这样可以节省时间和精力。

```
// short hand
let a, b = 1, c = 3;
```

## 为多个变量赋值

通常，给变量赋值会占用多行代码。从而阻碍了代码的可读性和可写性。

```
// long hand
let x,y,z;
x = 1;
y = 2;
z = 3;
```

但是，在 Javascript 中，我们也可以在一行中给多个变量赋值。这个概念叫做[数组分解](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)。

```
// short hand
let [x, y, z] = [1, 2, 3];
```

## 分配默认值

在现实世界的场景中，我们会遇到这样的情况，当没有其他的*值可以赋值时，我们想要给一个变量赋一个默认值。*

```
// long hand
let finalPermit;
let age= getAge();if (age>= 18){
    finalPermit= getAge();
}else{
    finalPermit= "You must be 18!";
}
```

您可以使用 OR 运算符简单地设置默认值。它的工作原理很简单，如果运算符左边的值为 falsey，它会将右边的值赋值。

```
// short hand 
let finalGrade= getScore() || "Failed!";
```

## 三元运算符

简单的 if-else 语句可能写起来很长，很耗时。占用了至少 5 行代码。

```
let points = 70;
let result;// long hand
if (marks >= 50){
    result = "pass";
}else{
    result = "fail";
}
```

然而，在 Javascript 中，您可以使用三元运算符在“单一”行中编写 if-else 语句。

```
//short hand
let result = marks>=50 ? "Pass" : "Fail";
```

## 短路评估

在现实世界的应用程序中，有些情况下您可能希望基于布尔变量执行任务，其中，如果条件为 ***真*** ， ***则*** 您可能希望执行一行代码。

```
// long hand
if (isLoggedIn){
    redirectToHomePage();
}
```

您可以简单地使用 AND 运算符。它的工作原理是，如果运算符左边的值为真，它将执行右边的函数。

```
// short hand
isLoggedIn && redirectToHomePage();
```

## 多条件检查

在一个 if 条件中检查多个值可能会令人厌烦。

```
// long hands
if (value===1 || value==="one" || value===2 || value==="two"){
    console.log("Number is either 1 or 2.");
}
```

因此，我们可以将所有值放在一个数组中，并在检查多个值时验证它们。这可以通过以下两种方式实现:

*   索引 Of()

```
// short hand using indexOf()
if ([1,"one",2,"two"].indexOf(value)>=0){
    console.log("Number is either 1 or 2.");
}
```

*   包括()

```
// short hand using includes()
if ([1,"one",2,"two"].includes(value)){
    console.log("Number is either 1 or 2.");
}
```

## 模板文字

一般来说，将字符串和变量连接起来会影响代码的可读性和可写性，大大降低速度。

```
// long hand
console.log('Hello' + name);
```

我们可以使用 ES6 模板文字，而不是使用+操作符来连接字符串。也就是说，您可以简单地用反斜杠````替换`“ ”`或`‘ ’`，并在`${ }`中提到变量。

```
// short hand 
console.log(`Hello ${name}`);
```

## 多行字符串

使用+操作符和`\n`编写多行字符串可能会很麻烦，而且阅读起来也很困难。

```
// long hand 
console.log('La la la la la la\n' +
'Sing a happy song');
```

因此，您可以直接使用反斜线````，而不是使用带`\n`的+运算符。

```
// short hand
console.log(`La la la la la la
Sing a happy song`);
```

## 交换 2 个变量

交换两个变量是一种常见的做法，我们在许多现实应用中都会遇到这种情况。当值被交换时，我们通常需要第三个“临时”变量来临时保存值。

```
let x = 1, y = 2;// long hand 
const temp = x; 
x = y; 
y = temp;
```

然而，使用 Javascript 数组分解，您可以交换两个变量，而不需要第三个变量。

```
let x = 1, y = 2; // short hand
[x,y] = [y,x];
```

## 箭头功能

在开发 Javascript 应用程序时，函数是最常用的。

```
// long hand
function add(a,b){
    return a+b;
}
```

您可以使用箭头函数语法来缩短函数，如下所示。

```
// short hand
const add = (a,b) => a + b;
```

## For 循环

当必须定义“计数”变量、范围和条件时，编写 for 循环可能会很长。

```
let arr = [1,2,3,4];// long hand
for (let i = 0; i< arr.length; i++){
    console.log(arr[i]);
}
```

因此，有两种更简单的方法可以在更高的层次上处理这个问题。因此减少了编写额外代码的需要并提高了可读性。

*   对...来说

```
// short hand using for-of
for (const val of arr){
    console.log(val);
}
```

*   对于-在

```
//short hand using for-in
for (const index in arr){
    console.log(arr[index]);
}
```

## 对象属性分配

为对象的属性赋值主要是由开发人员来完成的。

```
let firstName = "Adam";
let lastName = "naggy";// long hand
let obj = {firstName: firstName, lastName: lastName};
```

但是，如果变量名和对象键名相同，我们可以只在对象文本中提到变量名，而不是键和值。

```
// short hand
let obj = {fistName, lastName};
```

## 将字符串转换为数字

将字符串转换为数字的常用方法是使用 parseInt()方法。

```
// long hand
let total = parseInt('45');
```

但是，在 ES6 中，您可以通过在字符串前写一个+运算符来将字符串转换为数字。

```
// short hand 
let total = +'45';
```

## 从字符串中获取字符

通常，我们会使用`string.charAt()`将所需字符的索引传递给它，以获得字符串中的一个字符。

```
// long hand
let string = "Hello World!"
str.charAt(1);
```

但是，您可以使用[]运算符并传递索引来从字符串中获取字符。

```
// short hand
str[1];
```

## 合并数组

您可以使用`Array.concat()`合并数组，并将子数组作为参数传递给`concat()`。

```
let arr1 = [2,3];//long hand
let arr2 = arr1.concat([4,5]);
```

代替`Array.concat()`，我们可以使用 rest/spread 操作符来合并数组。

```
// short hand
let arr2 = [...arr1, 4, 5];
```

# 优化您的代码

使用短指针确实可以提高代码的速度和效率。

更少的代码行意味着编译代码的时间更少。因此，性能更好。

它提高了可读性和可写性。此外，它有助于编写更清晰的测试用例，并更容易地执行测试。

人手不足并不总是好的。知道何时使用它很重要。请记住，在考虑代码质量时，最佳实践总是第一位的。

# 结论

[Javascript](https://www.javascript.com/) 是一个大型社区，有来自世界各地的支持。有许多工具、语言和框架都是用 Javascript 构建的。即使是普通的 javascript 仍然被用来开发应用程序。因此，它是 IT 世界中最主要和广泛使用的语言。

我希望这个故事已经帮助你加快了开发时间，并且改进了你的代码。

分享知识是获得知识的唯一途径。对于那些可能从这个故事中受益的人，请随意添加并通过评论做出贡献。

玩得开心！享受编码！