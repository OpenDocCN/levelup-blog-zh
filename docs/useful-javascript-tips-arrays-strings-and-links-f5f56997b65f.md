# 有用的 JavaScript 技巧——数组、字符串和链接

> 原文：<https://levelup.gitconnected.com/useful-javascript-tips-arrays-strings-and-links-f5f56997b65f>

![](img/333540a5fd8a5b110c3b31a60a9ccdca.png)

照片由[范吉利斯·高孚](https://unsplash.com/@ekovu?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

像任何类型的应用程序一样，JavaScript 应用程序也必须写得很好。否则，我们以后会遇到各种各样的问题。

在本文中，我们将了解一些应该遵循的技巧，以便更快更好地编写 JavaScript 代码。

# 检查一个值是否是数组

我们可以使用`Array.isArray`方法来检查一个值是否是一个数组。

例如，我们可以写:

```
const arr = [1, 2, 3];
Array.isArray(arr);
```

然后，`isArray`方法返回`true`,因为`arr`是一个数组。

# 连接两个数组

有多种方法可以连接多个阵列。

一种方法是扩展运算符。例如，我们可以写:

```
const arr = [...arr1, ...arr2];
```

假设`arr1`和`arr2`是数组，我们使用 spread 操作符将数组条目一个接一个地展开。

我们也可以使用`concat`方法连接两个数组。

例如，我们可以写:

```
const arr = arr1.concat(arr2);
```

`concat`方法也返回一个数组。

因此，`arr`具有与第一个示例相同的值。

# 连接两根弦

有几种方法可以将两根弦连接在一起。

我们可以使用`+`操作符来连接两个字符串。

例如，我们可以写:

```
const fullName = firstName + lastName;
```

然后用`+`操作符将这两个字符串组合在一起。

`+=`操作符向第一个字符串添加第二个字符串。

例如，我们可以写:

```
name += lastName;
```

然后我们将`lastName`连接到`name`。

和数组一样，字符串也有`concat`方法。

例如，我们可以写:

```
const fullname = firstName.concat(lastName);
```

`firstName`与`lastName`串联，然后返回一个新的字符串。

所以`fullName`会有串联的字符串。

# 从链接运行 JavaScript 代码

如果我们想从一个链接运行 JavaScript 代码，那么我们可以通过使用`javascript:void(0)`来设置`href`属性的值。

然后在处理程序中，我们可以编写 JavaScript 代码。

例如，我们可以编写以下 HTML:

```
<a href="javascript:void(0)" onclick="handleClick()">click me</a>
```

然后在我们的 JavaScript 文件中，我们可以写:

```
const handleClick = () => {
  alert('clicked');
  return false;
}
```

我们在`handleClick`函数中返回了`false`，这样当函数运行时浏览器就不会滚回顶部。

# 字母和变量

`let`和`var`变量差别很大。

`let`创建块范围的变量。

`var`创建一个函数范围的变量。它们也被吊起来了。

块范围的变量只在块内可用，这是我们大多数时候想要的。

块范围不会有任何混淆。

`var`变量的提升特性令人困惑，它们的值有时不是我们所期望的。

# 不要修改内置的原型

我们不应该修改对象原型。

如果我们修改它们，那么我们会得到意想不到的结果，因为人们不希望我们改变原型。

如果我们想增加功能，我们应该创建和修改我们自己的代码。

如果我们修改内置原型，可能会有冲突。

# 向特定索引添加项目

要向特定的索引添加一个条目，我们可以使用`splice`方法向具有给定索引的数组添加一个条目。

例如，如果我们有:

```
const colors = ['green', 'red'];
```

我们想将`'orange'`插入到索引 1 中，我们可以写:

```
colors.splice(1, 0, 'orange');
```

第一个参数是我们想要更改的起始索引。

0 表示我们不想删除任何内容。

`'orange'`是我们要插入到`'green'`和`'red'`之间的项目。

`splice`原地改变一个数组，所以我们得到`colors`就是`[“green”, “orange”, “red”]`。

如果我们想在`'green'`和`'red'`之间添加多个条目，我们可以写:

```
colors.splice(1, 0, 'orange', 'blue');
```

我们只是在第二个参数之后传入尽可能多的参数，把它们都插入到我们想要的位置。

所以`colors`就是`[“green”, “orange”, “blue”, “red”]`。

![](img/5982e7f1b9587d13e5e7e35683972789.png)

马丁·莫雷诺在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

我们可以通过将`href`的值设置为`javascript:void(0)`来运行链接中的代码，这样我们就可以通过将处理程序附加到事件处理属性来运行我们想要的代码。

使用`splice`，我们可以从特定索引开始添加项目。

有多种方法将两个字符串或数组连接在一起。