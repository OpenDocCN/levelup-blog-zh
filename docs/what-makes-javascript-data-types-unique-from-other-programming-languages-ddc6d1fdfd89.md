# 是什么让 JavaScript 数据类型不同于其他编程语言

> 原文：<https://levelup.gitconnected.com/what-makes-javascript-data-types-unique-from-other-programming-languages-ddc6d1fdfd89>

![O](img/709408cfc9acccb3f7e693966c5f5992.png)  O 任何编程语言的一个关键方面就是它的数据类型，数据类型用来表示代码中不同种类的值。事实上，JavaScript 数据类型在许多方面都与其他编程语言中的数据类型不同。如果您是一名开发人员，您应该知道是什么使 JavaScript 中的数据类型不同于其他编程语言中的数据类型，例如为了评估 JavaScript 是否适合某个应用程序，或者为了与其他开发人员讨论一种语言的优缺点。在下面几节中，我们将探索 JavaScript 数据类型的独特特征和用途。

*这只是众多关于 JavaScript 和软件开发文章中的一篇。欢迎关注我们，获取更多关于 JavaScript 和软件开发的精彩内容。我们喜欢在 JavaScript 中涵盖基本概念，并使它们易于为更广泛的受众所理解。我们尝试一周发布多次。请确保不要错过我们的任何精彩内容。*

![](img/08b126088479d5af253c7ee3a5bf9ddb.png)

照片由 [Adrian N](https://unsplash.com/@anewevisual?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

首先，让我们先来看看 JavaScript 中的原语。与许多其他编程语言类似，原语是任何应用程序的构建块。它们就像原子:它们是你的代码中最小但不可摧毁的元素，所有其他的结构都建立在它们之上。在 JavaScript 中，有七种被认为是基本的数据类型:

*   Boolean:表示真或假值。
*   Null:表示故意缺少值。
*   Undefined:表示未初始化的值或缺少值。
*   Number:表示数值。
*   String:表示字符序列。
*   符号:表示唯一的、不可变的标识符。
*   BigInt:表示一个大的整数值。

JavaScript 还有一种称为 Object 的复合数据类型，它是键值对的集合，可以保存任何类型的值。

然而，有许多方面使得 JavaScript 数据类型不同于其他编程语言:

*   在 JavaScript 中，所有数据类型(除了上面列出的原始数据类型)都被认为是对象。这意味着它们具有可以被访问和调用的属性和方法，即使它们不是传统意义上的对象。例如，可以使用 length 属性获取字符串的长度，并可以使用 toUpperCase()方法将字符串转换为大写:

```
let str = 'Hello World';
console.log(str.length); // 11
console.log(str.toUpperCase()); // 'HELLO WORLD'
```

*   如上所述，JavaScript 有一个称为 Object 的复合数据类型，它是键值对的集合，可以保存任何类型的值。这为在 JavaScript 中存储和操作数据提供了极大的灵活性。例如，您可以创建一个对象来表示具有各种属性(如姓名、年龄和职业)的人:

```
let person = {
 name: 'John',
 age: 30,
 occupation: 'software developer'
};
console.log(person.name); // 'John'
console.log(person.age); // 30
console.log(person.occupation); // 'software developer'
```

*   JavaScript 有一个符号数据类型，它代表一个惟一的、不可变的标识符。这对于创建对象或其他值的唯一键或标识符非常有用。例如，您可以使用符号来定义对象的唯一属性:

```
let obj = {};
let key = Symbol('key');
obj[key] = 'value';
console.log(obj[key]); // 'value'
```

*   JavaScript 有 BigInt。它是 JavaScript 中表示大整数值的数据类型。这允许表示大于 Number 数据类型所能表示的最大安全整数值的整数值。例如，BigInt 可用于表示非常大的整数值:

```
let bigInt = 999999999999999999999999999999n;
console.log(bigInt); // 999999999999999999999999999999n
```

*   在 JavaScript 中，变量没有严格的类型，这意味着在声明变量时不需要指定数据类型。这为变量可以保存的值的类型提供了更大的灵活性，但如果处理不当，也可能导致潜在的类型强制问题。例如，您可以将一个字符串值赋给一个变量，然后将一个数字值赋给同一个变量，而不会出现任何错误:

```
let x = 'hello';
console.log(typeof x); // 'string'
x = 123;
console.log(typeof x); // 'number'
```

*   JavaScript 中存在一种类型的运算符。该运算符可用于确定值的类型。该运算符返回一个指示值的类型的字符串，这对于调试和处理代码中不同类型的值非常有用。举个例子:

```
let x = 'hello';
console.log(typeof x); // 'string'
x = 123;
console.log(typeof x); // 'number'
x = true;
console.log(typeof x); // 'boolean'
x = {};
console.log(typeof x); // 'object'
```

*   JavaScript 有一个空值，这表示故意没有值。这对于在代码中表示空值或缺失值非常有用。例如，可以使用 null 表示对象中缺少的值:

```
let obj = {
 name: 'John',
 age: 30,
 occupation: null
};
console.log(obj.occupation); // null
```

*   JavaScript 有一个 NaN 值，代表“非数字”，代表无效或不可表示的数值。这对于在执行数字运算时处理错误或无效输入非常有用。例如，如果您尝试对非数值执行数值运算，结果将是 NaN:

```
console.log(Number('hello')); // NaN
console.log(1 / 'hello'); // NaN
```

![](img/5c7fdb823e2c7f4190f716ff6bed224c.png)

我们希望您能够很好地理解 JavaScript 数据类型的独特之处。与其他语言相比，你知道 JavaScript 中的数据类型有哪些突出的方面吗？请在下面评论并告诉我们。我们喜欢关于技术细节的谈话。如果你不喜欢公开评论，你可以随时私下联系我们，比如留下私人信息。

如果你喜欢这篇文章，别忘了分享、鼓掌或关注。这将极大地帮助我们。提前感谢。再见。