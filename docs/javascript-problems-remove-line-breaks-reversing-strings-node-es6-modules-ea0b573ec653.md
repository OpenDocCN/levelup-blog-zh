# JavaScript 问题—删除换行符、反转字符串、节点 ES6 模块

> 原文：<https://levelup.gitconnected.com/javascript-problems-remove-line-breaks-reversing-strings-node-es6-modules-ea0b573ec653>

![](img/0152ecf3b63ce3f783ae81082ed38917.png)

罗伯特·里奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# 使用 JavaScript 向 JSON 对象添加新属性

我们可以用点或括号符号向 JSON 对象添加属性。

例如，我们可以写:

```
object["prop"] = value;
```

或者:

```
object.prop = value;
```

# 将消息打印到控制台

我们可以用`console.log`、`console.error`、`console.warn`或`console.info`方法将消息打印到控制台。

`console.log`向控制台记录消息。

`console.error`将错误信息记录到控制台。

`console.warn`记录警告。

`console.info`记录信息性消息。

我们可以用`%c`标签将 CSS 添加到控制台日志输出中:

```
console.log('%c hello', "background: blue; color: white");
```

# 禁用在引导模式区域外单击以关闭模式

通过使用`keyboard`和`backdrop`属性，我们可以禁止在引导模式区域之外点击来关闭模式:

```
$('#modal').modal({ backdrop: 'static', keyboard: false });
```

`backdrop`设置为`'static'`让我们通过点击模态外部来禁止关闭模态。

`keyboard`设置为`false`禁止按 Esc 键关闭模态。

# Javascript 向日期添加前导零

我们可以通过获取日期部分，然后将 0 添加到该部分，来将前导零添加到日期中。

例如，我们可以写:

```
const dateStr = `0${date.getDate().toString().slice(-2)}/0${(date.getMonth() + 1).toString().slice(-2)}/${date.getFullYear()}`;
```

在添加前导零之前，我们应该确保日期和月份是个位数。

# 移除字符串中的所有换行符

我们可以删除所有的换行符，方法是使用正则表达式来匹配所有的换行符:

```
str = str.replace(/(\r\n|\n|\r)/gm, "");
```

`\r\n`是 Windows 使用的 CRLF 换行符。

`\n`是一个 LF 换行符，被其他所有东西使用。

`\r`是回车。

`g`获取换行符的所有实例。

我们用空字符串替换它们来移除它们。

# Node.js 错误-语法错误:意外的标记导入

ES6 模块导入仅在节点 12 或更高版本中可用。

我们可以通过用`mjs`扩展名保存模块文件来使用它。

或者我们可以在最近的`package.json`里加上`{ "type": "module" }`。

在节点 9 和 12 之间，我们可以导入没有`— -experiemental-modules`标志的模块。

否则，我们可以使用`require`来导入模块。

同样，我们可以使用巴别塔节点。

为了使用它，我们运行:

```
npm install babel-register babel-preset-env --save-dev
```

那么在`.babelrc`中，我们可以写:

```
{
  "presets": ["env"]
}
```

然后在`package.json`中，我们可以添加:

```
"scripts": {
  "start": "babel-node src/index.js"
}
```

假设`src/index.js`是我们 app 的入口。

# 将 JavaScript 日期初始化为午夜的最佳方式

为了将日期设置为午夜，我们使用了`setHours`方法。

例如，我们可以写:

```
const d = new Date();
d.setHours(0, 0, 0, 0);
```

我们调用全零的`setHours`将小时、日、分钟、秒和毫秒都设置为零。

那我们的约会就定在午夜。

# 从数组中移除最后一项

我们可以用几种方法从数组中移除最后一项。

我们可以使用`splice`方法:

```
arr.splice(-1, 1)
```

`-1`是数组的最后一个索引，1 表示我们删除 1 项。

同样，我们可以调用`pop`:

```
arr.pop();
```

这将从`arr`中移除最后一个项目。

# 在 JavaScript 中原地反转字符串

我们可以通过使用`split`、`reverse`和`join`方法在 JavaScript 中将字符串反转:

```
const reverse = (s) => {
  return s.split("").reverse().join("");
}
```

我们将字符串分割成一个字符数组。

然后我们在数组上调用`reverse`，然后我们调用`join`将其回调。

我们可以用扩展操作符代替`split`:

```
const reverse = (s) => {
  return [...s].reverse().join("");
}
```

这做同样的事情。

![](img/b7de034a1c2fdc23750affb8b0710fd7.png)

照片由 [Dmitriy Serafin](https://unsplash.com/@daserafin?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以用`reverse`和`join`反串。

有多种方法可以将事情记录到控制台。

如果我们有一个最新版本的节点，我们可以使用节点应用程序导入。

可以用正则表达式和`replace`删除换行符。