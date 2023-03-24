# JavaScript 中的函数式编程概念

> 原文：<https://levelup.gitconnected.com/functional-programming-concepts-in-javascript-87a2d28bd2dd>

![](img/c399a4adcd0fb1f0bc4ec93bbdeb9ff7.png)

照片由 [Mysaell Armendariz](https://unsplash.com/@mysa21?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

函数式编程是一种编程范式，它声明我们创建计算作为函数的评估，并避免改变状态和可变数据。

在 JavaScript 中，我们可以应用这些原则来使我们的程序更健壮，产生更少的错误。

在本文中，我们将看看如何在 JavaScript 程序中应用一些函数式编程原则，包括纯函数和创建不可变对象。

# 纯函数

纯函数是在给定相同输入集的情况下总是返回相同输出的函数。

我们应该尽可能地使用纯函数，因为它们更容易测试，并且给定一组输入，输出总是可预测的。

此外，纯函数不会产生任何副作用，这意味着它确实会改变函数之外的任何东西。

纯函数的一个例子是下面的`add`函数:

```
const add = (a, b) => a + b;
```

这个函数是一个纯函数，因为如果我们传入两个数字，我们将返回这两个数字相加的结果。

它在函数之外不做任何事情，也没有改变函数内部返回值的值。

非纯函数的例子包括:

```
let x;
const setX = val => x = val;
```

`setX`不是一个纯函数，因为它设置了函数之外的`x`的值。这叫副作用。

副作用是在被调用函数之外观察到的状态变化，而不是它的返回值。`setX`在函数之外设置一个状态，所以会产生副作用。

另一个例子如下:

```
const getCurrentDatePlusMs = (milliseconds) => +new Date() + milliseconds;
```

`getCurrentDatePlusMs`返回一个依赖于`+new Date()`的值，这个值总是变化的，所以我们可以很容易地用测试来检查它的值。给定输入也很难预测返回值，因为它总是在变化。

不是一个纯粹的函数，而且由于其不断变化的返回值，维护和测试也很困难。

为了使它更纯粹，我们应该把`Date`对象放在外面，如下所示:

```
const getCurrentDatePlusMs = (date, milliseconds) => +date + milliseconds;
```

这样，给定相同的日期和毫秒，我们将总是得到相同的结果。

![](img/c80c62f7f4e3af8c2891eb84e93ad8c2.png)

Lauren McConachie 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 不变

不变性意味着数据一旦被定义就不能被更改。

在 JavaScript 中，原始值是不可变的，包括数字、布尔值、字符串等。

然而，没有办法在 JavaScript 中定义一个不可变的对象。这意味着我们必须小心对待它们。

我们有`const`关键字，从名字判断应该会创建一个常量，但是没有。我们不能给它重新赋值，也不能用相同的名字重新定义一个常数。

然而，分配给`const`的对象仍然是可变的。我们可以更改它的属性值，也可以删除现有的属性。可以添加或删除数组值。

这意味着我们需要一些其他的方法来使对象不可变。

幸运的是，JavaScript 拥有使对象不可变的`Object.freeze`方法。它防止对象的属性描述符被修改。此外，它还可以防止使用`delete`关键字删除属性。

一旦对象被冻结，就不能撤销。为了使它再次可变，我们必须将对象复制到另一个变量。

它将这些更改应用到对象的顶层。因此，如果我们的对象有嵌套，那么它不会被应用到嵌套的对象。

我们可以定义一个简单的递归函数来冻结一个对象的级别:

```
const deepFreeze = (obj) => {
  for (let prop of Object.keys(obj)) {
    if (typeof obj[prop] === 'object') {
      Object.freeze(obj[prop]);
      deepFreeze(obj[prop]);
    }
  }
}
```

上面的函数遍历对象的每一层，并在其上调用`Object.freeze`。这意味着它使整个嵌套对象不可变。

我们也可以在操纵一个对象之前复制它。为此，我们可以使用 spread 运算符，该运算符适用于 ES2018 之后的对象。

例如，我们可以编写以下函数来制作对象的深层副本:

```
const deepCopy = (obj, copiedObj) => {
  if (!copiedObj) {
    copiedObj = {};
  } for (let prop of Object.keys(obj)) {
    copiedObj = {
      ...copiedObj,
      ...obj
    };
    if (typeof obj[prop] === 'object' && !copiedObj[prop]) {
      copiedObj = {
        ...copiedObj,
        ...obj[prop]
      };
      deepCopy(obj[prop], copiedObj);
    }
  }
  return copiedObj;
}
```

在上面的代码中，我们创建了一个新的`copiedObj`对象来保存复制对象的属性，如果它还不存在的话，这在第一次调用中是不应该的。

然后我们遍历`obj`对象的每个属性，然后对`copiedObj`和`obj`应用 spread 操作符来复制给定级别的值。然后我们在每一层递归地这样做，直到我们通过每一层，然后我们返回最后的`copiedObj`。

我们只在属性在那个级别还不存在时应用 spread 操作符。

我们怎么知道这行得通？首先，我们可以检查每个值的属性是否相同，如果我们对它运行`console.log`，结果就是相同的。然后我们也可以用`===`检查复制的对象是否与原始对象具有相同的引用。

例如，如果我们有:

```
const obj = {
  foo: {
    bar: {
      bar: 1
    },
    a: 2
  }
};
```

然后我们可以通过书写来检查:

```
const result = deepCopy(obj);
console.log(result);
```

检查`result`的内容。

我们应该得到:

```
{
  "foo": {
    "bar": {
      "bar": 1
    },
    "a": 2
  }
}
```

这个和`obj`一样。如果我们跑:

```
console.log(result === obj);
```

我们得到`false`，这意味着这两个没有引用同一个对象。这意味着它们是彼此的复制品。

纯函数和不变性是函数式编程的重要组成部分。这些概念流行是有原因的。它们使输出变得可预测，并使意外的状态变化变得更加困难。

纯函数是可预测的，因为它们对同一组输入返回相同的输出。

不可变对象很好，因为它防止了意外的变化。JavaScript 中的原始值是不可变的，但对象不是。我们可以用`Object.freeze`方法冻结它以防止对它的修改，我们也可以用 spread 操作符递归地复制它。