# JavaScript 中的警告、提示和确认

> 原文：<https://levelup.gitconnected.com/alert-prompt-confirm-in-javascript-e3f847393de>

## 学习 JavaScript 中的三种基本用户交互。

在本文中，我们将了解浏览器功能`alert`、`prompt`和`confirm`。

![](img/e08a1d92a1418c7b7c3a86bc49272e38.png)

[Artem Sapegin](https://unsplash.com/@sapegin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

# 警报🚨

`alert` →用`ok`按钮在警告框中显示信息。显示警告框时，脚本执行暂停，直到用户按下“OK”或“ESC”键。

句法

```
alert(message)
```

例子

```
**alert("This is a alert 🚨");**
```

也可以传递任何 javascript 语句。

```
alert(2+4);
```

调用函数

```
function getMessage() { return "Hi 👋 from Javascript Jeep ";
}alert(getMessage())
```

要显示换行符，您可以使用

```
alert("Hello from /n JavaScript Jeep");
```

# 提示

`prompt`方法通过显示带有`ok`和`cancel`按钮的对话框，要求用户输入一个值。脚本执行暂停，直到用户按下“确定”或“取消”

句法

```
**let returnValue = prompt(message, [default_value]);**
```

`message` →对话框中显示的标题。

`default_value` →输入框中设置的默认(初始)值。在除 Internet Explorer 之外的所有浏览器中，它都是可选参数。在 IE 中如果我们没有通过默认，它会在提示中插入文本`"undefined"`。

调用`prompt`从输入字段返回文本，如果输入被取消，则调用`null`。

```
const YOB = prompt("What is your Year of Birth?", '');
```

如果用户输入一个值并按下“`OK`”，那么存储在`age`中的值就是用户输入。如果用户点击“取消”按钮或“Esc”键，则`age`的值为`null`

```
let YOB = prompt('What is your Year of Birth?', 1900);const currentYear = new Date().getFullYear();if(YOB === null) {
    YOB = 1900
}alert(`You are ${currentYear - YOB} years old!`); // You are 100 years old!
```

# 确认

`confirm`方法将显示一个确认框，显示一条带有`ok`和`cancel`按钮的消息(通常是一个问题)。

句法

```
let isConfirmed = confirm(question);
```

如果 OK 被按下，则`confirm`方法返回`true`，否则返回`false`。

例子

```
let canDelete = confirm("Do you want to delete the file ? ");
```

# 结论

所有这些方法都会暂停脚本的执行，并且不允许用户与页面的其余部分进行交互，直到窗口关闭。

对话框的位置和样式是不可定制的。

请把我捐赠给这里。即使是很小的一便士对我来说也是巨大的。请跟随我 [Javascript Jeep🚙💨](https://medium.com/u/f9ffc26e7e69?source=post_page-----e3f847393de--------------------------------)。和我一起加入[推特](https://twitter.com/jagathish1123)。

[](https://www.buymeacoffee.com/Jagathish) [## Jagathish Saravanan

### 你好👋。我是 Jagathish。爱写关于 JavaScript 的文章。你的支持就像夏天吃冰淇淋一样。我…

www.buymeacoffee.com](https://www.buymeacoffee.com/Jagathish) 

> [游览 JavascriptJeep.com](http://javascriptjeep.co,)