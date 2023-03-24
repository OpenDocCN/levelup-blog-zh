# jQuery 技巧——类名、列表元素和去抖动

> 原文：<https://levelup.gitconnected.com/jquery-tips-class-names-list-elements-and-debounce-cd3a32ebee39>

![](img/bec7eaca055ef6b0efe4683ee89fcf42.png)

[Bonnie Kittle](https://unsplash.com/@bonniekdesign?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

尽管年代久远，jQuery 仍然是操纵 DOM 的流行库。

在本文中，我们将了解一些使用 jQuery 的技巧。

# 使用 jQuery 获取元素的类列表

要用 jQuery 获取元素的类列表，我们可以获取元素的`class`属性，并用任何空格分割类列表字符串。

为此，我们可以写:

```
$(element).attr("class").split(/\s+/);
```

我们得到元素，用`attr`得到 class 属性。

然后我们用匹配任何空白的`/\s+/`调用`split`。

为了让我们的生活更轻松，我们可以使用`classList`属性。

我们可以通过获取 DOM 元素来做到这一点。

例如，我们可以写:

```
$(element)[0].classList
```

我们只需获取`classList`属性来获取类的列表。

DOM 元素通过索引来访问。

# 延迟 Keyup 处理程序，直到用户停止键入

我们可以创建自己的函数，使用回调函数调用`setTimeout`,并将回调函数作为回调函数传递，以延迟回调函数的运行。

例如，我们可以写:

```
const delay = (fn, ms) => {
  let timer = 0
  return function(...args) {
    clearTimeout(timer)
    timer = setTimeout(fn.bind(this, ...args), ms || 0)
  }
}
```

我们创建了`delay`函数，它接受我们想要运行的回调，也就是`fn`。

`ms`是我们希望在运行`fn`回调之前延迟的持续时间。

我们有返回一个函数的`timer`，这个函数接受一些参数，我们可以将这些参数传递给`fn`回调函数。

我们还通过传递`this`作为`bind`的第一个参数，将`this`的值设置为我们调用`keyup`的元素。

在我们返回的函数中，如果有一个集合，我们调用`timer`上的`clearTimeout`。

我们必须返回一个传统函数，以便获得正确的值`this`，这是我们正在监听的 keyup 事件的元素。

然后我们用`fn`调用`setTimeout`，用我们描述过的`this`的值和我们传入的参数。

要使用它，我们可以写:

```
<input id="input" type="text" placeholder="enter your name"/>
</label>
```

然后我们可以写:

```
$('#input').keyup(delay(function (e) {
  console.log(this.value);
}, 1500));
```

我们用 jQuery 获得 ID 为`input`的输入。

然后我们用传递给它的 keyup 事件监听器调用我们的`delay`函数来调用`keyup`。

然后我们可以用`this.value`记录输入的值。

这样，我们可以看到我们输入的值。

延迟设置为 1500 毫秒或 1.5 秒。

如果我们不想创建自己的函数，我们也可以使用下划线或洛达什的`debounce`方法。

例如，我们可以写:

```
const debouncedOnKeyup = _.debounce(function (e) {
  console.log(this.value);
}, 1500);$('#input').keyup(debouncedOnKeyup);
```

我们调用下划线或 Lodash `debounce`方法向事件处理函数添加延迟。

然后我们调用`keyup`来延迟运行 keyup 处理程序。

# 在使用 jQuery 执行某些操作之前，等待所有图像加载完毕

为了在 jQuery 上运行某些东西之前等待图像加载，我们可以等待文档准备好。

我们还可以观察窗口的 load 事件，并在它发出后运行代码。

为了等待文档准备好，我们可以写:

```
$(document).ready(() => {
  //...
});
```

我们用一个回调函数调用了 jQuery 对象上的 T7。

此外，我们可以通过编写以下内容来观察加载事件:

```
$(window).on("load", () => {
  //...
});
```

当窗口对象上的 load 事件被触发时，回调被运行。

# 用 jQuery 获取选定的元素标记名

我们可以用`prop`方法获取`tagName`属性来获取标签名。

例如，我们可以写:

```
$("<a>").prop("tagName");
```

我们用`'tagName'`字符串获得了`a`元素的标签名。

此外，我们可以把它做成一个插件。

例如，我们可以写:

```
$.fn.tagName = function() {
  return this.prop("tagName");
};
```

然后我们可以写:

```
$("<a>").tagName();
```

定义插件后。

![](img/5cb525178a0f741aabeb9491d5a33b74.png)

由 [Satyabrata sm](https://unsplash.com/@smpicturez?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以用`tagName`属性获得 jQuery 元素的标记名。

有各种方法可以得到班级名单。

此外，有各种方法可以延迟按键处理程序。

为了确保图像被加载到文档中，我们可以观察窗口或文档事件来检查它是否准备好了。