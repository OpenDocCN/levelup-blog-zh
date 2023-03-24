# JavaScript 问题—选项卡焦点状态、UTC、字符串字符等等

> 原文：<https://levelup.gitconnected.com/javascript-problems-tab-focus-state-utc-string-characters-and-more-fbd1c4bf7950>

![](img/d23332cf7551bbc96f8185dc4e3b6d9e.png)

你好，我是尼克🎞上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# 检查是否选中了复选框

我们可以用 jQuery `attr`或`prop`方法检查复选框是否被选中。

例如，我们可以写:

```
$("#checkbox").attr('checked')
```

我们也可以写:

```
$("#checkbox").prop('checked')
```

获取复选框的选中值。

同样，我们可以使用`is`:

```
$("#checkbox").is(':checked')
```

# 将 JavaScript 日期转换为 UTC

要将 JavaScript 日期转换成 UTC，我们可以使用`toIsoSytring`方法来完成。

例如，我们可以写:

```
const isoDate = new Date(2020, 0, 1).toISOString();
```

然后我们得到`“2020–01–01T08:00:00.000Z”`作为`isoDate`的值。

`Z`表示时间为 UTC。

# 如何取消设置 JavaScript 变量

`delete`操作符让我们删除全局变量，因为它是`window`的属性。

例如，我们可以写:

```
delete foo;
```

如果声明`foo`时没有像`let`、`var`或`const`这样的关键字。

否则，无法移除。

我们只是把它设置为`undefined`。

# 获取字符串的第一个字符

我们可以用`charAt`或括号得到字符串的第一个字符。

例如，我们可以写:

```
const x = 'foo bar';
const letter = x.charAt(0);
```

那么`letter`就会是`'f'`。

我们也可以写:

```
const x = 'foo bar';
const letter = x[0];
```

得到同样的东西。

同样，我们可以使用`substring`方法。第一个参数是起始索引，它是包含性的，第二个参数是结束索引，它是排他性的。

例如，我们可以写:

```
const x = 'foo bar';
const letter = x.substring(0, 1);
```

为了得到`'f'`。

# 删除文本中的所有空白

从字符串中删除所有空格的更简单的方法是使用`replace`方法。

例如，我们可以写:

```
str.replace(/ /g,'')
```

这将删除`str`中的空格字符。`g`表示全局，将在整个字符串中搜索空格。

要删除所有空白字符，我们还可以写:

```
str.replace(/\s/g,'')
```

删除除空格字符之外的所有空格。

# 检测浏览器窗口当前是否处于活动状态

我们可以监听`visibilitychange`事件来检查窗口是否是活动的。

例如，我们可以写:

```
document.addEventListener("visibilitychange", onchange);
```

如果页面可见性 API 不可用，还有 jQuery `blur`和`focus`方法:

```
$(window).blur(() => {
  // ...
});$(window).focus(() => {
  // ...
});
```

如果窗口不在焦点上，则运行`blur`的回调。

如果窗口获得焦点，就会运行`focus`的回调函数。

# 在 jQuery 中使用正则表达式选择元素

我们可以使用来自[https://j11y.io/javascript/regex-selector-for-jquery/](https://j11y.io/javascript/regex-selector-for-jquery/)的 jQuery `:regex`过滤器来选择一个带有 regex 的元素。

例如，使用它，我们可以写:

```
$("div:regex(class, .*foo.*)")
```

选择包含子字符串`foo`的类的 div。

# 检查元素是否包含类

为了检查一个元素是否包含一个类，我们可以使用一个元素的`classList`属性来检查它是否包含这个类。

例如，我们可以写:

```
element.classList.contains('foo');
```

这将检查元素是否有`foo`类。

# 用 jQuery 区分鼠标左键和右键点击

使用 jQuery，我们可以通过用`mousedown`方法监听`mousedown` 事件来区分鼠标左键和右键点击。

例如，我们可以写:

```
$('#element').mousedown(function(event) {
  switch (event.which) {
    case 1:
      console.log('left button');
      break;
    case 2:
      console.log('middle button');
      break;
    case 3:
      console.log('right button.');
      break;
    default:
      console.log('not a  button');
    }
});
```

我们使用鼠标事件对象的`event.which`属性来获取被点击的按钮。

1 是左键。2 是中间的按钮。3 是右边的按钮。

# 使用 Node.js 选择元素

为了让我们在节点应用程序中选择 DOM 节点，我们可以使用`jsdom`包来完成。

要使用它，我们可以写:

```
const jsdom = require("jsdom");
const { JSDOM } = jsdom;
const { window } = new JSDOM();
const { document } = (new JSDOM('')).window;
```

我们有由`jsdom`包创建的`window`和`document`对象，而不是使用浏览器。

但是我们可以选择节点，就像在浏览器中一样。

![](img/f5cbeedc83ab63d6e0339afd1b44ad0a.png)

桑迪·米勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 结论

我们可以在节点应用中选择 DOM 节点。

变量不能被删除，除非它们是全局变量。

我们可以使用页面可见性 API 来获取浏览器标签或窗口的焦点状态。