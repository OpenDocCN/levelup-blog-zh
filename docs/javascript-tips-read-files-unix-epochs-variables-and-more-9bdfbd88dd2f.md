# JavaScript 技巧—读取文件、UNIX 纪元、变量等等

> 原文：<https://levelup.gitconnected.com/javascript-tips-read-files-unix-epochs-variables-and-more-9bdfbd88dd2f>

![](img/cbd93d70388e3aa554475ccde0d33d77.png)

马丁·莫雷诺在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# 如何获得自 UNIX 纪元以来的毫秒时间

要获得自 UNIX 纪元以来的毫秒时间，即自 1970 年 1 月 1 日 12 AM UTC 以来的时间，我们可以使用`Date.now()`。

例如，我们可以写:

```
const now = Date.now();
```

而`now`是以毫秒为单位的 UNIX 时间戳。

我们也可以写:

```
const now = new Date().valueOf()
```

得到同样的东西。

# 在 JavaScript 中动态创建 CSS 类并应用它

要创建一个动态 CSS 类并将该类应用于一个元素，我们可以用 JavaScript 创建一个样式元素。

然后我们可以设置元素的`className`属性来应用这个类。

例如，我们可以写:

```
const style = document.createElement('style');
style.type = 'text/css';
style.innerHTML = '.cssClass { color: green; }';
document.head.appendChild(style);
document.getElementById('foo').className = 'cssClass';
```

我们用设置为`'text/css'`的类型属性创建样式元素。

然后我们用`innerHTML`设置样式元素中的文本。

然后，我们获得带有`document.head`的头部，并向其添加样式元素。

最后，我们将`className`设置为我们刚刚创建的那个。

# 重命名 JavaScript 对象的键

要重命名 JavaScript 对象的键，我们可以用旧键的值添加新键。

然后我们可以删除旧的密钥。

我们可以使用`Object.assign`来做到这一点。

例如，我们可以写:

```
delete Object.assign(o, { [newKey]: o[oldKey] })[oldKey];
```

我们用括号符号填充`newKey`。

然后我们删除带括号的`oldKey`。

# 从 fs.readFile 获取数据

我们可以通过回调从`data`获取数据来从`fs.readFile`获取数据。

例如，我们可以写:

```
const fs = require('fs');

fs.readFile('./foo.txt', (err, data) => {
  if (err) {
    throw err;
  }
  console.log(data);
});
```

我们也可以与`readFileSync`同步进行:

```
const text = fs.readFileSync('foo.txt','utf8')
console.log (text)
```

在这两个例子中，我们都传入了文件的路径。

在第二个例子中，我们传入文件的编码。

# 检测 HTTP 或 HTTPS，然后在 JavaScript 中强制 HTTPS

我们可以检查一个 URL 是否以`https`开头，然后我们可以调用`location.replace`来替换这个 URL，用 URL 的`https`版本来替换它。

例如，我们可以写:

```
if (location.protocol !== 'https:') {
     location.replace(`https:${location.href.substring(location.protocol.length)}`);
}
```

我们调用`location.replace`并用`https`版本替换当前 URL。

我们调用`substring`从原始 URL 中删除`http`。

# 获取 JavaScript 对象键列表

我们可以用`Object.keys`方法得到对象键列表。

例如，我们可以写:

```
const obj = {
  foo: 1,
  bar: 2, 
  baz: 3
}
const keys = Object.keys(obj);
```

现在`keys`有了`obj`的钥匙。

# 使用 onClick 侦听器获取被单击按钮的 ID

我们可以通过使用`this`获得被点击元素的 ID。

例如，我们可以写:

```
<button id="foo" onClick="onClick(this.id)">button</button>
```

然后我们可以写:

```
const onClick = (clickedId) =>  {
  console.log(clickedId);
}
```

此外，我们可以通过编写以下内容来获取事件对象的`target`属性:

```
const onClick = (event) =>  {
  console.log(event.target.id);
}
```

# Javascript 中变量声明语法的区别

有多种方法可以声明变量。

一种方法是使用`var`，如果它在顶层，它会创建一个全局对象的全局变量。

否则，它是函数范围的。

例如，我们可以写:

```
var x = 1;
```

`let`在块范围内创建一个变量。

它仅在块和嵌套块中可用。

例如，我们可以写:

```
let foo = 1;
```

然后我们创建一个块范围的变量。

`const`也是块范围的，但它是常量。所以我们只能在声明的时候给它赋值一次。

例如，我们可以写:

```
const bar = 1;
```

我们不能在这一行之后给它赋值。

为了创建全局变量，我们可以在`window`对象上创建一个新的属性。

例如，我们可以写:

```
window.a = 0;
```

然后我们将`a`属性附加到`window`对象上。

我们可以用`a`来访问它。

为了避免混淆，我们应该尽可能声明块范围的变量。

![](img/9071e7c70944cf651572729758b127a7.png)

约翰·麦肯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

有许多方法可以声明 JavaScript 变量。

我们应该尽可能使用块范围的变量。

可以动态添加样式元素。

有几种方法可以在节点应用程序中读取文件。