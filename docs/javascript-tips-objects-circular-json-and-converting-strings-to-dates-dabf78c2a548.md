# JavaScript 技巧——对象、循环 JSON 和将字符串转换为日期

> 原文：<https://levelup.gitconnected.com/javascript-tips-objects-circular-json-and-converting-strings-to-dates-dabf78c2a548>

![](img/fefb9d60121d1013663f22dc0ba8f29e.png)

[百合银行](https://unsplash.com/@lvnatikk?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# 以类似 JSON 的格式打印一个循环结构

我们可以使用`util`库来打印圆形结构。

它有一个`inspect`方法，让我们打印这些结构。

所以我们可以写:

```
console.log(util.inspect(obj));
```

为了做到这一点。

还有做同样事情的`circular-json`包。

我们通过运行以下命令来安装它:

```
npm i --save circular-json
```

并写下:

```
const CircularJSON = require('circular-json');
const json = CircularJSON.stringify(obj);
```

还有`flatted`套餐。

我们通过运行以下命令来安装它:

```
npm i flatted
```

然后我们可以写:

```
import {parse, stringify} from 'flatted';

const foo = [{}];
foo[0].foo = foo;
foo.push(foo);stringify(foo);
```

# 检查对象属性是否存在，并带有保存该属性的变量

我们可以使用`hasOwnProperty`方法或者`in`操作符来检查一个属性是否存在。

例如，我们可以写:

```
const prop = 'prop';
if (obj.hasOwnProperty(prop)) {
  //...
}
```

或者我们可以写:

```
const prop = 'prop';
if (prop in obj) {
  //...
}
```

# 检查用户是否已经滚动到底部

我们可以用普通的 JavaScript 来检查。

我们写道:

```
el.onscroll = () => {
  if(el.scrollTop + el.clientHeight === el.scrollHeight) {
    // ...
  }
}
```

我们在 scroll 上运行一个事件处理程序。

为了检查用户是否滚动到底部，我们检查了`scrollTop`和`clientHeight`的和是否与`scrollHeight`相同。

`scrollTop`是元素垂直滚动的像素数。

`clientHeight`是以像素为单位的元素高度。

包括填充，但不包括边框、边距和水平滚动条。

# 确定两个 JavaScript 对象是否相等

我们使用 Lodash `isEqual`方法来检查深度相等。

例如，我们可以写:

```
_.isEqual(object, other);
```

# 枚举 JavaScript 对象的属性

为了枚举 JavaScript 对象的属性，我们可以使用 for-in 循环或`Object.keys`。

例如，我们可以写:

```
for (const p in obj) {
  if (obj.hasOwnProperty(p)){
    //...
  }
}
```

我们调用`hasOwnProperty`来检查一个属性是否在一个对象中。

这是因为 for-in 枚举了原型链中的所有属性。

同样，我们可以使用`Object.keys`:

```
for (const p of Object.keys(obj)) {
  //... 
}
```

我们使用 for-of 循环代替 for-in 循环，因为`Object.keys`返回一个自己的键数组。

# 在 JavaScript 中将字符串转换为日期

我们可以使用`Date`构造函数将日期字符串解析成`Date`实例:

```
const parts = '2014-04-03'.split('-');
const date = new Date(parts[0], parts[1] - 1, parts[2]); 
console.log(date.toDateString());
```

我们提取日期字符串的年、月和日期。

然后我们将它们放在`Date`构造函数中。

我们必须从月份中减去 1，因为月份 0 是一月，1 是二月，等等。

然后我们得到正确的日期。

# 检测浏览器窗口当前是否不活动

为了检查浏览器窗口是否处于活动状态，我们可以在代码中观察`visibilitychange`事件。

例如，我们可以写:

```
document.addEventListener("visibilitychange", onchange);
```

`onchange`是哪里:

```
const onchange = () => {
  if (document.hidden) {
    //...
  } else {
    //...
  }
}
```

我们检查`document.hidden`属性来检查页面是否被隐藏。

# 在 Node.js 中一次读取一行文件

我们可以使用`readline`模块一次读取一行文件。

例如，我们可以写:

```
const lineReader = require('readline').createInterface({
  input: require('fs').createReadStream('file')
});lineReader.on('line', (line) => {
  console.log(line);
});
```

我们使用`readline`模块并传入一个带有文件读取流的对象。

然后监听`line`事件获取台词。

# 相当于 PHP isset()的 JavaScript

PHP 的`isset`函数在 JavaScript 中有几个等价的函数。

我们可以使用`typeof`操作符、`hasOwnProperty`或`in`操作符。

例如，我们可以写:

```
if (typeof obj.foo !== 'undefined') {
  // ...
}
```

检查`obj.foo`是否定义。

我们也可以写:

```
if (obj.hasOwnProperty('foo')) {
  // ...
}
```

或者:

```
if ('foo' in obj) {
  // ...
}
```

然后`in`操作符检查属性是否存在于`obj`的原型中，所以我们应该注意到这一点。

![](img/cc047c86277a84586ad05c15493e4875.png)

照片由[莎拉·奥利芙](https://unsplash.com/@taffy_apple?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

有很多方法可以检查一个属性是否存在。

我们可以使用库来打印循环 JSON。

可以用 Lodash 检查对象的深度相等。