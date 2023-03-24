# 遍历 JavaScript 对象键的多种方法

> 原文：<https://levelup.gitconnected.com/multiple-ways-to-traverse-javascript-objects-keys-5a00a2773267>

![](img/f83ede951106fdc518df7608a832aed5.png)

蒂埃拉·马洛卡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 对象是 JavaScript 中的基本构件。在 ES6 或更高版本中，有很多方法可以遍历对象。

在本文中，我们将研究如何在 JavaScript 中遍历对象键。

# 遍历属性

有几种方法可以遍历 JavaScript 对象的属性。它们是:

*   `Object.keys(obj)` —获取所有非继承(自有)属性的所有字符串键
*   `Object.getOwnPropertyNames(obj)` —获取所有自身属性的所有字符串键
*   `Object.getOwnPropertySymbols(obj)` —获取所有自有属性的所有符号键
*   `Reflect.ownKeys(obj)` —获取所有自有属性的所有密钥
*   `for...in`循环——循环所有自己的和继承的可枚举属性

# 属性的遍历顺序

遍历顺序没有定义。因此，`for...in`循环的遍历顺序因浏览器而异。

我们不应该依赖于代码中遍历的顺序。

# `Object.keys(obj)`

`Object.keys`接受一个对象作为参数，并返回一个非继承的、可枚举的字符串键数组。

例如，我们可以如下使用它:

```
const obj = {
  a: 1,
  b: 'foo',
  c: false
};for (const key of Object.keys(obj)) {
  console.log(key);
}
```

上面的代码遍历由`Object.keys`返回的`obj`的键。然后我们记录`obj`的键，因为它们被循环通过。

然后我们得到:

```
a
b
c
```

从控制台日志输出。

# object . getownpropertymanames(obj)

我们可以使用`Object.hetOwnPropertyNames`方法来检索所有的字符串键，包括不可枚举的。

例如，我们可以如下使用它:

```
const obj = {
  a: 1,
  b: 'foo',
};Object.defineProperty(obj, 'c', {
  value: false,
  enumerable: false
})for (const key of Object.getOwnPropertyNames(obj)) {
  console.log(key);
}
```

在上面的代码中，我们通过用一个指定属性设置为`false`的对象调用`Object.defineProperty`，在`obj`中定义了一个不可枚举属性`c`。

然后当我们循环通过由`getOwnPropertyNames`返回的键时，我们得到:

```
a
b
c
```

从控制台日志输出，因为可枚举和不可枚举属性都由`getOwnPropertyNames`返回。

# object . getownpropertymodals(obj)

`Object.getOwnPropertySymbols`方法接受一个对象并返回该对象的所有符号键。只返回自己的密钥。

例如，我们可以如下使用它:

```
const obj = {
  [Symbol('a')]: 1,
  [Symbol('b')]: 'foo',
  [Symbol('c')]: false
};for (const key of Object.getOwnPropertySymbols(obj)) {
  console.log(key);
}
```

然后我们得到:

```
Symbol(a)
Symbol(b)
Symbol(c)
```

从控制台日志输出。

# Reflect.ownKeys(obj)

`Reflect.ownKeys`在返回的对象中返回字符串和符号非继承键。它接受一个我们想从中获取密钥的对象。

例如，我们可以如下使用它:

```
const obj = {
  [Symbol('a')]: 1,
  [Symbol('b')]: 'foo',
  c: false
};for (const key of Reflect.ownKeys(obj)) {
  console.log(key);
}
```

然后我们得到:

```
c
Symbol(a)
Symbol(b)
```

记录在控制台日志输出中。

![](img/d32bf266ac5a9bf13240e29e5bd601a6.png)

Erik Mclean 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# for(obj 中的常量键)

我们可以使用`for...in`循环遍历继承的和非继承的可枚举属性键。

例如，给定以下类结构和对象:

```
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }
}class Student extends Person {
  constructor(firstName, lastName, studentId) {
    super(firstName, lastName);
    this.studentId = studentId;
  }
}const student = new Student('Jane', 'Smith', '123');
```

然后我们得到`student`对象的所有继承的和非继承的键。

因此，当我们对`student`对象运行`for...in`循环时，如下所示:

```
for (const key in student) {
  console.log(key);
}
```

我们得到:

```
firstName
lastName
studentId
```

从控制台日志中。

如果我们想遍历继承的和非继承的键，这是唯一的选择。

除了`for...in`之外的选项都返回自己的键，所以没有多少选项可以循环所有的键。

然而，如果我们不想遍历继承的键，那么其他选择会好得多，因为我们可以确定顺序，因为它们都返回数组。

# 结论

有很多方法可以循环对象的键。

它们是:

*   `Object.keys(obj)` —返回所有非继承(自有)属性的所有字符串键
*   `Object.getOwnPropertyNames(obj)` —返回所有自身属性的所有字符串键
*   `Object.getOwnPropertySymbols(obj)` —返回所有自有属性的所有符号键
*   `Reflect.ownKeys(obj)` —返回所有自有属性的所有键
*   `for...in`循环—循环所有自己的和继承的可枚举属性

这些涵盖了循环遍历键的所有可能性，比如循环遍历符号键、继承键和不可枚举键。