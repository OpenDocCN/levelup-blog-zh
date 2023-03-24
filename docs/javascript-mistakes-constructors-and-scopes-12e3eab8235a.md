# JavaScript 错误— null vs undefined、构造函数和作用域

> 原文：<https://levelup.gitconnected.com/javascript-mistakes-constructors-and-scopes-12e3eab8235a>

![](img/a11193472828c58338c4238423bc4ae9.png)

安德烈·瓦瑟伯格在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

JavaScript 是一种比世界上许多其他编程语言更友好的语言。然而，在编写 JavaScript 代码时，由于误解或忽略我们已经知道的东西，仍然很容易犯错误。通过避免下面的一些错误，我们可以通过防止代码中的错误和错别字来使我们的生活变得更容易，这些错误和错别字会使我们陷入意想不到的结果。在本文中，我们将看看`null`和`undefined`之间的混淆，异步代码的作用域问题，以及内建对象的作用域和使用对象文字与构造函数。

# 混淆空和未定义

在 JavaScript 中，`null`和`undefined`都表示变量没有值。它们非常相似，但也有一些重要的区别。如果`null`的类型是`object`，那么当我们在`null`上使用`typeof`操作符时，如下所示:

```
typeof null
```

我们得到`'object'`。

另一方面，如果我们在`undefined`上使用`typeof`操作符，如下所示:

```
typeof undefined
```

我们得到`'undefined'`。

如果我们将它们与`===`操作符进行比较，如下所示:

```
undefined === null
```

我们得到`false`，因为它们是两种不同的类型。然而，当我们将它们与`==`操作符进行比较时:

```
undefined == null
```

然后我们得到`true`，因为它们都是假的。因为它们是两种不同的类型，所以不应该混淆。

需要注意的一点是，对于函数默认参数，只有当值为`undefined`而不是`null`时，它们才会被赋值。

```
const hello = (name = 'World') => console.log(`Hello, ${name}!`)
hello(null) // Hello, null!
hello(undefined) // Hello, World!
```

# 异步代码和范围

JavaScript 的一个令人困惑的方面是变量的可见性仍然在父范围内。这意味着当一个循环运行时，我们希望在`setTimeout`函数的回调中访问一个循环变量，在`setTimeout`函数的回调被调用之前，索引变量已经被递增以满足结束条件。这意味着我们只能在循环结束后获得循环变量的最终值，该值被传递到`setTimeout`函数的回调中。

例如，如果我们有以下代码:

```
for (var i = 0; i < 10; i++) {
  setTimeout(() => {
    console.log(i);
  }, 100);
}
```

我们得到 10 次记录。这是因为`setTimeout`的回调函数中的代码在循环结束后运行。`i`到时候早就更新到 10 了。如果我们想在每个`setTimeout`回调函数调用中保持循环变量的值。然后，我们必须通过将`setTimeout`包装在一个函数中，将`i`的值传递给回调函数。

如果我们希望看到 0 到 9 被记录，那么我们必须编写以下代码:

```
for (var i = 0; i < 10; i++) {
  ((i) => {
    setTimeout(() => {
      console.log(i);
    }, 100);
  })(i)
}
```

上面的代码将`setTimeout`调用包装在匿名函数中，每次循环运行时我们都会调用该函数，并且我们在每次迭代中将`i`的值传递给该函数。然后在循环完成后，`i`的值将被传递到`console.log`而不是`i`的值。

![](img/651a82f1a3208cb9b66957896e21ff34.png)

弗朗切斯科·德·托马索在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 在文本上使用构造函数

在 JavaScript 中，构造函数对于创建新的对象实例并不总是有用的。对于像数组、数字和字符串这样的内置对象来说尤其如此。我们用`new`操作符构造对象的新实例，就像 C#或 Java 等其他语言一样。然而，它并不总是如我们所愿。

例如，我们可以用`new`操作符创建一个新的`Array`对象。例如，我们可以写:

```
let arr = new Array('a', 'b', 'c', 'd');
```

然后我们得到了我们期望的`[“a”, “b”, “c”, “d”]`。然而，当我们只向`Array`构造函数传递一个参数时，正如我们在下面的代码中所做的:

```
let arr1 = new Array(23);
```

然后我们得到一个空数组，其属性设置为 23。

原来有两个`Array`构造函数。一个接受一个参数，即数组的长度，另一个接受由逗号分隔的无限对象列表，即新数组的元素列表。

但是，如果唯一的参数不是一个数字，那么它会创建一个数组，并将该参数作为数组的唯一条目。例如，如果我们写:

```
let arr = new Array('a');
```

我们得到`[“a”]`。

如果唯一的参数是负数，它也无法创建新数组。我们得到错误消息`Uncaught RangeError: Invalid array length`。

使用数组构造函数令人困惑，因为有两个构造函数接受不同类型的参数。

对于数字和字符串，我们在使用构造函数创建这些对象时会遇到不同的问题。当我们使用`new`操作符创建一个数字或字符串对象时，当我们将它们与`===`操作符比较时，我们会遇到问题。例如，如果我们尝试比较两个具有相同内容的字符串，其中一个是字符串文字，另一个是用构造函数创建的，如下面的代码所示:

```
'abc' === new String('abc');
```

我们得到`false`，即使它们的内容完全相同。这是因为字符串文字`'abc'`属于 string 类型，但是用`new String(‘abc’)`创建的对象属于 object 类型。同样，这是令人困惑的。

我们必须在 string 对象上调用`valueOf`方法，使其成为 string 类型的对象。

同样，如果我们有:

```
1 === new Number(1);
```

我们也因为同样的原因得到`false`。`1`属于数字类型，而`new Number(1)`属于对象类型。同样，我们必须调用`valueOf`方法将`new Number(1)`转换回数字类型的对象。

所有这些问题的解决方案都可以通过使用文字符号来解决。JavaScript 不需要预先知道数组的长度，因为它只是将`undefined`赋给没有赋值的位置。

# 结论

在 JavaScript 中，`null`和`undefined`都意味着被赋予这些值的变量没有值。它们非常相似，但是有一些重要的。一个如果`null`是类型`object`，而`undefined`是类型`undefined`。这意味着`null === undefined`将被评估为`false`。

每当我们有异步代码，并且我们想要将某个变量的值传入一个由类似`setTimeout`的异步函数调用的回调函数时，我们必须将外部变量传入用函数包装异步代码的回调函数，然后将变量从外部传入回调函数。

最后，我们应该避免使用构造函数来创建内置对象，如数组、数字和字符串。对于数字和字符串，类型将不同于文字类型，文字类型将具有数字或字符串的实际基本类型。对于数组来说，这是因为有两个构造函数接受不同的参数，我们会得到意想不到的结果，这取决于我们传入的参数的数量。