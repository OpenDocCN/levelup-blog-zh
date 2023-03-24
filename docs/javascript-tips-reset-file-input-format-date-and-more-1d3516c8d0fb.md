# JavaScript 提示—重置文件输入、格式化日期等

> 原文：<https://levelup.gitconnected.com/javascript-tips-reset-file-input-format-date-and-more-1d3516c8d0fb>

![](img/822061fd7dc4bc52a8c8fd90b732d3e8.png)

照片由[齐比克](https://unsplash.com/@zibik?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# 重置文件输入

我们可以通过将输入的`value`属性设置为空字符串来重置文件输入。

例如，我们可以写:

```
document.getElementById("file-input").value = "";
```

# JavaScript 中只比较日期部分而不比较时间

如果我们将日期的时间设置为相同的话，我们可以在不比较时间的情况下比较日期。

为此，我们可以将两者设置为相同的时间:

```
const date1 = new Date();
const date2 = new Date(2011,8,20);
date1.setHours(0, 0, 0, 0);
date2.setHours(0, 0, 0, 0);
```

我们将`date1`和`date2`都设置为午夜。

现在我们只能通过日期来比较两者。

# 手动触发 onchange 事件

我们可以通过使用`Event`构造函数来触发变更事件。

例如，我们可以写:

```
const event = new Event('change');
element.dispatchEvent(event);
```

我们用`Event`构造函数创建事件。

然后我们用`event`对象作为参数调用元素上的`dispatchEvent`。

# 从 JavaScript 日期对象中获取 YYYYMMDD 格式的字符串

要获得 YYYYMMDD 格式的字符串形式的日期，我们可以使用内置的 date 方法来实现。

例如，我们可以写:

```
const mm = date.getMonth() + 1; 
const dd = date.getDate();
const yyyymmdd = [date.getFullYear(), `${mm > 9 ? '' : '0'}mm`,
`${dd > 9 ? '' : '0'}dd`].join('');
```

我们称`getFullYear`为 4 位数年份。

`getMonth`大半夜的。我们必须加上 1，因为在 JavaScript 日期中，月份是从零开始的。

`getDate`返回当天。

然后我们就把它们都放在数组里，调用`join`把它们组合在一起。

# JavaScript 属性访问的点符号与括号

我们可以使用点或括号符号来访问 JavaScript 属性。

例如，我们可以写:

```
const foo = form["foo"];
```

或者:

```
const foo = form.foo;
```

它们对于字符串键也是一样的。

点符号更快更容易阅读。

但是方括号符号允许我们使用特殊字符访问属性，或者使用变量访问属性。

# 对象扩散与对象分配

我们可以使用扩展语法或`Object.assign`来复制或合并对象。

扩展语法更短，但不是动态的。

例如，我们可以写:

```
const obj = {...a, ...b};
```

我们将对象`a`和`b`的条目分散到一个新对象中，并将其分配给`obj`。

它也仅在 ES2018 或更高版本中可用。

如果我们需要更动态的东西，我们可以使用`Object.assign`。

它在旧版本的 JavaScript 中也可用。

例如，我们可以写:

```
const obj = Object.assign({}, a, b);
```

做和以前一样的事情。

但是，这比使用 spread 语法要长。

如果对象在一个数组中，我们还可以使用 spread 运算符组合动态数量的对象:

```
const obj = Object.assign({}, ...objs);
```

其中`objs`是一个对象数组。

# JSON 和 JSONP 的区别

JSON 和 JSONP 的区别在于，尽管有同源策略，我们还是可以使用 JSONP。

JSON 通过 JSONP 传递到回调中。

但 JSON 却不是这样。

例如，JSON 响应是:

```
{ "name": "james", "id": 5 }
```

但是 JSONP 被传递到一个回调函数中，我们用查询参数`callback`指定这个回调函数。

例如，如果我们有:

```
?callback=func
```

在 URL 的查询字符串中，我们写:

```
function func(json){
  console.log(json.name);
}
```

来获取 JSONP 响应。

`json`是也:

```
{ "name": "james", "id": 5 }
```

# Object.create()和 new 构造函数()之间的区别

`Object.create`让我们创建一个直接继承自另一个对象的对象。

如果我们使用构造函数来创建一个对象，那么我们就创建了一个从构造函数的原型继承的对象。

例如，如果我们有:

```
const foo = Object.create(bar);
```

然后`foo`继承`bar`的属性。

但是如果我们写:

```
const foo = new Foo();
```

然后`foo`继承了`Foo`的原型。

![](img/c82f425b1a5a0939352e3c0a932520eb.png)

照片由 [Sonnie Hiles](https://unsplash.com/@sonniehiles?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以通过将文件的`valuye`属性设置为空字符串来重置文件输入。

Spread 和`Object.assign`可以做类似的事情。

`Object.create`和构造函数也不同。

我们还可以调用 date 方法来获取日期的一部分。