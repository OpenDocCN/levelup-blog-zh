# JavaScript 问题—选择元素、本地存储等等

> 原文：<https://levelup.gitconnected.com/javascript-problems-selecting-elements-local-storage-and-more-25d6d0c20f07>

![](img/80c6ef67938f84d12b565427ab96fabb.png)

弗兰克·麦肯纳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案。

# 自定义数据属性上的 jQuery 选择器

我们可以使用 jQuery 选择具有自定义数据属性的元素。

例如，我们可以写:

```
$("ul[data-group='companies']")
```

然后我们得到 ul 元素，它的`data-group`属性被设置为`companies`。

# 将 js onClick 传递值反应给方法

我们必须创建一个返回另一个函数的函数。

例如，我们可以写:

```
return (
  <div value={column} onClick={() => this.handleSort(column)}>{column}</div>
);
```

我们有一个带参数的方法。

用我们想要的`colunm`调用`handleSort`方法。

# 复选框选中状态已更改事件

我们可以附加一个事件处理程序来监听复选框的变化。

为此，我们可以使用 jQuery 中的`change`方法来实现。

例如，我们可以写:

```
$(".checkbox").change(function() {
  if (this.checked) {
    //Do stuff
  }
});
```

我们得到 checkbox 元素，然后对它调用`change`。

然后我们用`this.checked`得到值，其中`this`是复选框。

# 用本地存储存储数组

我们可以通过将数组转换成字符串来用本地存储来存储数组。

为此，我们可以使用`JSON.stringify`方法。

例如，我们可以写:

```
localStorage.setItem("names", JSON.stringify(names));
```

假设`names`是一个数组，我们把它串成一个 JSON 字符串，然后我们用第一个参数中指出的键`'name'`存储它。

然后我们可以通过使用`JSON.parse`来获得值:

```
const storedNames = JSON.parse(localStorage.getItem("names"));
```

# 呈现后在输入字段上设置焦点

在 React 组件中，我们可以在元素加载后调用 DOM 方法。

在类组件中，这应该在`componentDidMount`方法中。

例如，我们可以写:

```
class App extends React.Component{
  componentDidMount(){
    this.nameInput.focus();
  }
  ...
}
```

其中`nameInput`是输入元素的 ref。

在一个函数组件中，我们可以通过编写来使用`useEffect`钩子:

```
useEffect(() => {
  nameInputRef.current.focus();
}, [])
```

`nameInputRef`是输入 ref。

`current`是带有 DOM 元素的属性。

然后我们就可以在上面调用`focus`了。

# 在 HTML 文件中包含另一个 HTML 文件

我们用 jQuery `load`方法将另一个 HTML 文件包含在一个 HTML 文件中。

例如，我们可以写:

```
$("#includedContent").load("page.html");
```

这将把`page.html`的内容加载到 ID 为`includedContent`的元素中。

# document . getelementbyid vs jQuery $()

`getElementById`不同于 jQuery 的`$`函数。

`$`可以接受任何选择器字符串，如果找到给定的选择器，就返回它的一个或多个元素。

`getElementById`只能返回给定 ID 的元素。

# 编写一个返回对象的 ES6 箭头函数

要编写一个返回对象的 arrow 函数，我们可以用括号将对象括起来。

例如，我们可以写:

```
p => ({ foo: 'bar' });
```

如果我们想返回其他值，那么括号是可选的。

# 从 HTML 字符串创建一个 DOM 元素

我们可以使用`document.createElement`来创建元素。

然后我们可以将 HTML 字符串设置为新元素的`innerHTML`属性的值。

例如，我们可以写:

```
const div = document.createElement('div');
div.innerHTML = htmlString.trim();
```

其中`htmlString`是 HTML 字符串。

我们创建了一个 div 并将 HTML 字符串的内容放入其中。

# 从父页面运行 iframe 中的 JavaScript 代码

我们可以使用`contentWindow`属性来访问 iframe 的内容。

然后我们可以在其中调用一个函数:

```
document.getElementById('frame').contentWindow.foo();
```

我们用它在 iframe 中调用了`foo`，假设 iframe 的 ID 是`frame`。

# 窗口调整事件

为了在窗口调整大小时运行代码，我们可以将一个事件监听器函数附加到`window.onresie`属性。

例如，我们可以写:

```
window.onresize = (event) => {
  //...
};
```

# 从 JavaScript 对象中移除对象

`delete`操作符让我们从一个对象中删除一个属性。

例如，我们可以写:

```
const o = { bar: 'foo' }
delete o.bar;
```

然后我们从`o`中移除了`bar`属性。

![](img/86570e8d1b66a13d32a455de1d1fd82e.png)

[斯蒂夫·约翰森](https://unsplash.com/@steve_j?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

我们可以通过将事件处理程序附加到`onresize`属性来观察 resize 事件。

如果我们想运行带参数的函数，我们可以将函数传递给调用函数的`onClick`。

有多种方法可以从 DOM 中获取元素。