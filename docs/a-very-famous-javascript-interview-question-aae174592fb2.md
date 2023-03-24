# 一个非常著名的 Javascript 面试问题

> 原文：<https://levelup.gitconnected.com/a-very-famous-javascript-interview-question-aae174592fb2>

![](img/fdf22066afd92084142478a9eaf34f96.png)

约翰娜·布格特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

今天我们将看到一个常见的 Javascript 面试问题。

问题是这样的:

> 实现一个`**product**`方法，当使用以下两种方法调用时，该方法将返回两个数的乘积

a) `product(4, 5)`

b) `product(4)(5)`

这是一个非常著名的 Javascript 面试问题，因为它测试你对函数式 Javascript、闭包和函数参数的知识。要解决这个，我们先来了解一些基本概念。

## **高阶函数**

高阶函数是接受函数、返回函数或两者兼有的函数。

**例如:**数组`map`、`filter`、`reduce`方法都接受一个函数作为自变量，并且是高阶函数。

当一个函数返回另一个函数时，它也被称为高阶函数。

```
function greet(message) {
 return function(name) {
  console.log(message, name);
 };
}const hello = greet('Hello');
console.log(hello('Mark')); // Hello Mark
```

在这里，我们不用创建一个单独的变量来存储`greet`函数的结果，而是可以像这样直接调用它:

```
console.log(greet('Hello')('Mark')); // Hello Mark
```

## **自变量对象**

我们编写的每个函数都可以访问`arguments`对象，该对象包含调用函数时传递的值列表:

```
function add() {
 let sum = 0;
 for(let i = 0; i < arguments.length; i++) {
  sum += arguments[i];
 }
 console.log(sum); // 15
}add(1,2,3,4,5);
```

**注意:箭头函数不能访问参数对象**

所以使用这个高阶函数和`arguments`对象，我们可以通过以下两种方式实现`product`方法。

1.  **通过计算参数长度:**

```
function product(a) {
 if (arguments.length == 2) {
  return arguments[0] * arguments[1];
 } else {
   return function(b) {
    return a * b;
   };
 }
}console.log(product(4, 5)); // 20
console.log(product(4)(5)); // 20
```

2.**通过检查第二个参数是否存在:**

```
function product(a, b) {
 if (b) { // if b exists
  return a * b;
 } else {
   return function(b) {
    return a * b;
  };
 }
}console.log(product(4, 5)); // 20
console.log(product(4)(5)); // 20
```

第二种方法有一个问题。在 javascript 中， **0** 被认为是 falsy 值。点击了解更多信息。因此，如果我们调用如下所示的函数，我们会得到不同的输出

```
console.log(product(4, 0)); // function (b) {}
console.log(product(4)(0)); // 0
```

为了解决这个问题，我们可以将上面的函数改写为

```
function product(a, b) {
 if (b || b === 0) { // if b exists or b is zero
  return a * b;
 } else {
   return function(b) {
    return a * b;
  };
 }
}
console.log(product(4, 0)); // 0
console.log(product(4)(0)); // 0console.log(product(4, 5)); // 20
console.log(product(4)(5)); // 20
```

您还可以在函数中添加额外的验证，以检查传递给函数的值是否为数字，然后返回 product，否则会返回一些一般性的错误消息，如“输入无效”

今天到此为止。希望你今天学到了新东西。

**别忘了直接在你的收件箱** [**这里**](https://yogeshchavan.dev) **订阅我的每周时事通讯，里面有惊人的技巧、诀窍和文章。**