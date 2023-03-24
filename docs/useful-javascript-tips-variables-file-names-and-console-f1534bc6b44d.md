# 有用的 JavaScript 技巧—变量、文件名和控制台

> 原文：<https://levelup.gitconnected.com/useful-javascript-tips-variables-file-names-and-console-f1534bc6b44d>

![](img/75a635a63c15057f263a258b7b4628ca.png)

照片由[Slawek](https://unsplash.com/@s1awek?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何类型的应用程序一样，JavaScript 应用程序也必须写得很好。否则，我们以后会遇到各种各样的问题。

在本文中，我们将了解一些应该遵循的技巧，以便更快更好地编写 JavaScript 代码。

# var 和 let

`var`是函数范围的，`let`是块范围的。

`var`被吊起`let`未被吊起。

只提升了变量，但没有提升值。

例如，如果我们有:

```
console.log(x);
var x = 1;
```

那么`x`将是`undefined`，因为`var x`被提升而 1 没有被提升。

另一方面，我们不能用`let`做同样的事情:

```
console.log(x);
let x = 1;
```

上面的代码会出错，因为我们不能使用`let`变量，除非它们已经被声明。

为了减少混乱，我们应该使用`let`变量。

# 中断 forEach 迭代

我们可以在传递给`forEach`的回调函数中返回`true`来跳过一次迭代。

这样，我们可以拥有与`continue`相同的功能。

例如，我们可以写:

```
[0, 1, 2, 3, 4].forEach((val, i) => {
  if (val === 2) {
    return true;
  }
  console.log(val);
});
```

关于的代码将跳过 2，因为当`val`为 2 时，我们返回`true`。,

所以我们跳过了它下面的控制台日志。

# 使用切片

`slice`方法可用于从数组中获取数组条目。

我们可以从负索引开始，从右边而不是左边返回数组条目。

所以如果我们有:

```
const arr = [1, 2, 3];
```

然后:

```
arr.slice(-1)
```

返回`[3]`。

```
arr.slice(-2))
```

返回`[2, 3]`。

并且:

```
arr.slice(-3)
```

返回原始数组。

这是因为`-1`是数组最后一个元素的索引。

`-2`是倒数第二等等。

# 短路条件

短路条件很方便，因为我们可以用`&&`和`||`做一些我们可能想不到的事情。

例如，不写:

```
if(condition){
    dosomething();
}
```

我们可以写:

```
condition && dosomething();
```

因为`doSomething`仅在`condition`与`&&`操作器一致时运行。

同样，我们可以利用`||`操作符的短路。

例如，我们可以写:

```
a = a || 'default';
```

因为如果`a`为真，则`a`被分配给`a`。

否则，`'default'`被分配给`a`。

# 使用可选参数

我们可以用默认的参数语法使参数可选。

例如，我们可以写:

```
const divide = (a, b = 1) => a / b;
```

如果我们跳过参数，那么`b`将被赋值为 1。

所以`divide(2)`应该是 2。

# 将内容复制到剪贴板

我们可以从剪贴板中获取元素，并通过编写以下内容来复制其内容:

```
document.querySelector('#foo').select();
document.execCommand('copy');
```

第一行选择 ID 为`foo`的元素。

然后第二行进行复制。

# 从文件名中提取文件扩展名

有几种方法可以从文件名中提取文件扩展名

## 正则表达式

例如，我们可以写:

```
const getExt = (filename) => {
  return (/[.]/.exec(filename)) ? /[^.]+$/.exec(filename)[0] : undefined;
}
```

我们有一个正则表达式来按点分割文件名。然后我们得到带`/[^.]+$/.exec(filename)[0]`的点之后的部分，如果没有点，则得到`undefined`的部分。

## 使分离

此外，我们可以使用`split`方法按点分割文件名。

例如，我们可以写:

```
const getExt = (filename) => {
  return filename.split('.').pop();
}
```

一个字符串有一个`split`方法，它可以用分隔符分割一个字符串，并将其作为一个字符串数组返回。

然后我们调用`pop`移除拆分字符串的最后一部分并返回。

因此，我们得到了扩展。

# 控制台日志技巧

我们可以使用`&&`在计算控制台日志之后的表达式之前运行控制台日志。

因为`console.log`返回`undefined`，所以右操作数总是被求值。

所以我们可以写:

```
console.log(foo) && false
```

我们在一个表达式中记录了`foo`的值并返回了`false`。

# 将功能打印到控制台

我们可以将一个函数转换成一个字符串来打印出函数的代码。

例如，我们可以写:

```
console.log(func.toString());
```

这样，我们可以在控制台日志中看到该函数的代码。

![](img/80ab425b2cb6d9e262abb370155d85f4.png)

[拍摄的照片](https://unsplash.com/@j_b_foto?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上

# 结论

我们可以用`toString`和`console.log`方法记录一个函数的代码。

此外，我们可以使用`&&`操作符在计算其他表达式之前记录一个值。

从文件名中获取文件扩展名也有多种方法。

最后，要尽量使用`let`来声明变量。