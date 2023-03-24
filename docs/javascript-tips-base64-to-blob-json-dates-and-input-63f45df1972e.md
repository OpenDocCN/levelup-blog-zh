# JavaScript 技巧— Base64 到 Blob、JSON、日期和输入

> 原文：<https://levelup.gitconnected.com/javascript-tips-base64-to-blob-json-dates-and-input-63f45df1972e>

![](img/57fa537442fb2e41c7cb6ba67d78705a.png)

由[托德·夸肯布什](https://unsplash.com/@toddquackenbush?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# JSON.stringify 和 JSON.parse 之间的区别

`JSON.strignify`和`JSON.parse`做不同的事情。

`JSON.stringify`将一个对象转换成一个 JSON 字符串。

`JSON.parse`将一个 JSON 字符串解析成一个对象。

例如，我们可以通过书写使用`JSON.stringify`;

```
const obj = { foo: 1, bar: 2 };
const str = JSON.stringify(obj);
```

`str`是`obj`的字符串版本。

我们可以通过写来使用`JSON.parse`:

```
const obj = JSON.parse(str);
```

如果`str`是一个有效的 JSON 字符串，那么它将被解析成一个对象。

# 删除字符串中的重音符号或音调符号

如果我们的字符串中有重音符号或音调符号，我们可以使用`normalize`方法将其删除。

例如，我们可以写:

```
const str = "garçon"
const newStr = str.normalize("NFD").replace(/[\u0300-\u036f]/g, "")
```

那么`newStr`就是`“garcon”`。

`NFD`表示 Unicode 范式。

它将组合的字素分解成更简单的字素的组合。

因此`ç`被分解成字母和音调符号。

然后我们用`replace`去掉所有的重音字符。

# 解析 JSON 时出现“意外标记 o”错误

如果我们试图解析的东西已经是一个对象，我们将得到“意外的标记 o”错误。

因此，我们应该确保我们没有解析 JSON 对象。

# 检测浏览器语言首选项

我们可以通过使用`window.navigator.userLanguage`或`window.navigator.language`来检测浏览器语言偏好。

`window.navigator.userLanguage`仅在 IE 中可用。

`window.navigator.language`在其他浏览器中可用。

它返回在 Windows 区域设置中设置的语言。

`window.navigator.language`返回浏览器语言，而不是首选语言。

# 用 JavaScript 从 Base64 字符串创建 Blob

要从 base64 字符串创建 blob，我们可以使用`atob`函数。

然后我们必须从字符串的每个字符创建一个字节值数组。

然后我们将字节数数组转换成一个`Uint8Array`。

然后我们可以将它传递给`Blob`构造函数。

因此，我们写道:

```
const byteCharacters = atob(b64Data);
const byteNumbers = new Array(byteCharacters.length);
for (let i = 0; i < byteCharacters.length; i++) {
    byteNumbers[i] = byteCharacters.charCodeAt(i);
}
const byteArray = new Uint8Array(byteNumbers);
const blob = new Blob([byteArray], {type: contentType});
```

去做这一切。

# 获取当前日期和时间

我们可以用 JavaScript 中的`Date`实例的各种方法来获取当前的日期和时间。

`getDate`获取一个月中的某一天。

`getDay`获取星期几，0 表示星期日，1 表示星期一，依此类推。

`getFullYear`返回整年的数字。

获取一天中的小时数。

`getMinutes`返回一小时中的分钟数。

`getSeconds`获得秒。

我们可以通过书写来使用它:

```
const newDate = new Date();
const day = newDate.getDay();
```

我们还可以使用`toLocaleString`方法以一种对地区敏感的方式格式化我们的日期字符串:

```
new Date().toLocaleString();
```

# 在我们输入内容时跟踪变化的最佳方式

为了跟踪我们在输入框中输入的变化，我们可以监听`input`事件。

例如，我们可以写:

```
const input = document.getElementById('input');
const result = document.getElementById('result');const inputHandler = (e) => {
  result.innerHTML = e.target.value;
}

source.addEventListener('input', inputHandler);
```

我们用`e.target.input`在`inputHandler`中获得输入值。

同样，我们在`result`元素中设置输入值。

然后我们调用`addEventListener`来监听输入事件。

# JavaScript 中“= >”的含义

`=>`让我们创建一个箭头函数，它是一个不绑定到`this`的更短的函数。

因此，我们不能使用箭头函数作为构造函数。

它从外部获取`this`的值。

它也没有绑定到`arguments`对象，所以我们不能用它来获取传递给函数的参数。

![](img/9e4fa03c29902769484c7aaa6dc6e9af.png)

[David Vig](https://unsplash.com/@davidvig?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

`JSON.stringify`和`JSON.parse`是彼此相反的。

`=>`让我们创建箭头函数。

我们可以监听`input`事件来获取用户在输入框中输入的值。

获取当前日期和时间可以用 date 方法来完成。

我们可以将 base64 字符串转换为 blob。