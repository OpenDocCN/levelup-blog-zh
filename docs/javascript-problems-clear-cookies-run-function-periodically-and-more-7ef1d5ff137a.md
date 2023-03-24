# JavaScript 问题——清除 Cookies、定期运行函数等等

> 原文：<https://levelup.gitconnected.com/javascript-problems-clear-cookies-run-function-periodically-and-more-7ef1d5ff137a>

![](img/d8b1133c9eda417890904b4c0aabd628.png)

照片由 [Elisa Kerschbaumer](https://unsplash.com/@__elisa__?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# 清除 Cookies

我们可以通过将 cookie 的`Max-Age`设置为过去的时间戳来清除 cookie。

例如，我们可以写:

```
document.cookie = `${name}; Max-Age=-99999999;`;
```

将带有给定键的 cookie 设置为过去的时间戳以立即删除它。

# 异步和延迟脚本标记

`async`对于异步加载脚本很有用。具有`async`属性的脚本可以以任何顺序加载。

如果我们想异步加载脚本，并按照脚本标签列出的顺序加载，我们可以使用`defer`属性。

# 将基数添加到 parseInt

我们应该将基数传递给`parseInt`的第二个参数，这样它将总是按照我们想要的大小写将数字字符串解析成整数。

例如，如果我们有:

```
parseInt('100', 10);
```

那么字符串`'100'`将被解析为一个基数为 10 的数字，而不是它想要的任何数字。如果我们省略基数，数字以`'0x'`开始，那么基数自动设置为 16。如果字符串以`'0'`开头，那么在旧版本的浏览器中，该数字可能会被解析为八进制数。

如果一个字符串以任何其他值开始，那么它被解析为一个十进制数。

# 用
元素替换所有换行符

我们可以搜索所有换行符，并用`replace`替换`'<br>'`。

例如，我们可以写:

```
str = str.replace(/(?:\r\n|\r|\n)/g, '<br>');
```

其中`/(?:\r\n|\r|\n)/g`搜索字符串中的所有换行符。

然后我们传入`'<br>'`来替换它们。

# 从不使用 eval

使用`eval`通常不是个好主意。我们可能会向代码注入攻击开放我们的应用程序。

调试更具挑战性，因为运行的代码是在字符串中，所以没有语法高亮、自动完成、行号等。

用`eval`运行的代码也更慢，因为代码在字符串中，所以没有机会编译或缓存代码。

# 如何并行运行多个 NPM 脚本？

我们可以使用`concurrently`包来并行运行脚本。

我们只是写:

```
"dev": "concurrently --kill-others \"npm run start-watch\" \"npm run serve\""
```

我们只是使用带有`--kill-others`开关的包和它后面的脚本。

`--kill-others`死一个就杀所有命令。

# 初始化数组的长度

我们不需要将数组初始化为给定的长度，因为 JavaScript 数组是动态长度的。

因此，我们可以将事物放入任何索引中。

# 选中或取消选中 JavaScript 复选框

我们可以将`checked`属性设置为`true`或`false`，分别使复选框被选中或取消选中。

例如，我们可以写:

```
document.getElementById("checkbox").checked = true;
```

要选中复选框并:

```
document.getElementById("checkbox").checked = false;
```

取消选中它。

使用 jQuery，我们可以使用`prop`方法将`'checked'`属性设置为`true`或`false`来做同样的事情。

例如，我们可以写:

```
$("#checkbox").prop("checked", true);
```

要选中复选框并:

```
$("#checkbox").prop("checked", false);
```

取消选中它。

# 每 5 秒钟调用一次函数

要调用一个函数 everything 5 秒，我们可以使用`setInterval`函数。

例如，我们可以写:

```
setInterval(() => { 
  // ...
}, 5000);
```

然后在回调中，我们可以每隔 5 秒运行一段我们想运行的代码。

第二个参数以毫秒为单位，因此 5000 是 5 秒。

# 检查函数是否存在

为了检查函数是否存在，我们可以使用`typeof`操作符。

我们可以通过编写以下代码来检查函数是否存在:

```
if (typeof me.foo !== "undefined") { 
  // ...
}
```

使用前检查`foo`方法是否为`undefined`。

我们可以写一个更精确的检查:

```
if (typeof me.foo === "function") { 
  // ...
}
```

检查`foo`是否确实是一个函数值属性。

![](img/c706f17f2039ce0201b2bee558d296fe.png)

兹登尼克·马赫亚克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

我们可以用`typeof`操作符检查某个东西是否是一个函数。为了清除 cookies，我们可以将`Max-Age`设置为过去的时间戳。

让我们定期运行代码。我们永远不应该使用`eval`。

基数参数应传入`parseInt`。