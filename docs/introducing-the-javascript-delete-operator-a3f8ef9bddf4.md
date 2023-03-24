# JavaScript 删除操作符简介

> 原文：<https://levelup.gitconnected.com/introducing-the-javascript-delete-operator-a3f8ef9bddf4>

![](img/268e3171950da3eca24c90994ef04a5f.png)

由 [Borna Bevanda](https://unsplash.com/@bbevanda?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript `delete`操作符从对象中移除属性。它将释放被删除的属性持有的对对象的所有引用。

在本文中，我们将看看如何使用`delete`操作符，以及它在不同情况下的行为。

# 基本用法

要使用`delete`操作符，我们只需在任何想要删除的属性表达式前写下`delete`。

例如，如果我们有对象:

```
const obj = {
  a: 1,
  b: 2
};
```

然后，我们可以通过写以下内容来删除属性`a`:

```
delete obj.a;
```

或者:

```
delete obj['a'];
```

# 返回值

除了当被删除的属性是自己的不可配置属性时，`delete`操作符返回`true`。

如果我们试图删除一个自身不可配置的属性，将以非严格模式返回。

# 特征

如果我们试图删除的属性不存在，那么`delete`什么都不做并返回`true`。

如果对象的原型链中存在同名的属性，则对象将在删除后使用原型链中的属性。

任何用`var`声明的属性不能从全局作用域或函数作用域中删除。

`delete`不能删除全局范围内的任何函数。

同样，任何带有`let`和`const`的属性都不能从定义它们的作用域中删除。

无法删除不可配置的属性。因此，来自`Math`、`Array`或`Object`等内置对象的属性，或任何被创建为不可配置的属性都不能被删除。

不可配置的属性意味着我们不能改变它们的属性描述符。

`var`、`let`和`const`都创建不可配置的属性。

# 严格模式

如果我们试图在严格模式下删除一个变量，我们会得到一个语法错误。

# 非严格模式

如果我们试图删除一个变量，非严格模式将返回`false`。

![](img/39781bcfce1b632fa4feba98d7445194.png)

Cyrus Chew 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 删除数组元素

我们可以在数组元素上使用`delete`。该元素将从数组中移除，但`length`不会改变。

例如，如果我们有:

```
const arr = [1, 2, 3];
delete arr[1];
```

然后我们得到:

```
[1, empty, 3]
```

当我们记录长度时，我们会得到 3。

如果我们使用`in`操作符来检查索引是否存在，如下所示:

```
const arr = [1, 2, 3];
delete arr[1];
console.log(1 in arr);
```

我们得到`false`。

如果一个数组元素存在，但是有一个未定义的值，我们应该将它设置为`undefined`，而不是使用`delete`操作符。

正如我们所见，在数组上使用`delete`很奇怪。因此，如果我们想通过索引删除一个数组条目，我们应该使用`splice`:

```
const arr = [1, 2, 3];
arr.splice(1, 1);
```

`splice`的第一个参数指定了我们想要移除的数组元素的索引。第二个参数是我们希望从第一个参数指示的索引开始移除的元素的数量。

然后，当我们再次记录`arr`时，我们得到:

```
[1, 3]
```

# 结论

`delete`操作符用于从对象中删除属性。如果属性是自己的且可配置的，则它有效。

否则，它在非严格模式下返回`false`，在严格模式下抛出一个 SyntaxError。

它不应该与数组一起使用，因为它的行为不一致。