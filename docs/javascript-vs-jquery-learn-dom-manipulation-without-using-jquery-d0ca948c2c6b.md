# JavaScript 与 jQuery——不使用 jQuery 学习 DOM 操作

> 原文：<https://levelup.gitconnected.com/javascript-vs-jquery-learn-dom-manipulation-without-using-jquery-d0ca948c2c6b>

*原贴于*[*【https://faisalrashid.tech/blogs/JavaScript-vs-jQuery】*](https://faisalrashid.tech/blogs/JavaScript-vs-jQuery)

![](img/bda6a390611250fe14165a5fb0b1825d.png)

jQuery 已经成为许多新的和即将到来的 Web 开发人员的救星，包括我自己。如果你想学习以前的 Web 开发，学习 jQuery 是理所当然的。这主要是因为 jQuery 消除了许多跨浏览器兼容性问题，使开发人员能够编写代码，而不必担心他们实现的功能是否能在所有浏览器上工作。

但是随着浏览器标准的改进和 jQuery 的大部分 API 集成到 JavaScript 中，jQuery 已经变得有点多余了。此外，有了原生浏览器 API，调试代码就容易多了，而且由于是原生的，这些 API 中的大多数都比 jQuery 的 API 提供了更好的性能。此外，您将少一个要添加到脚本导入中的库。如果你仍然不同意放弃 jQuery，也许这个[答案](https://softwareengineering.stackexchange.com/questions/122191/what-benefits-are-there-to-native-javascript-development)会有所帮助。

因此，如果您正在考虑脱离 jQuery，我已经编译了一个人们使用的常见 jQuery 方法和 API 的列表，以及它们的 Vanilla JS 替代品(Vanilla JS 是不使用任何库的普通 JavaScript 代码的花哨名称)。让我们开始吧！

## 查询 DOM

jQuery 的主要特点是它查询 DOM 元素的惊人能力。下面演示了这一点:

```
jQuery('div.home')
```

但是，您可以使用 JavaScript 的 **document.querySelector()** 和**document . query selector all()**方法实现同样的事情。下面是它们的实现。

```
document.querySelector('div.home')
```

该方法总是返回与选择器匹配的第一个元素。如果您希望查询多个元素，您将不得不使用 **querySelectorAll()** 方法。

```
document.querySelectorAll('img').forEach((_el, index) => {
      //do stuff with **_el** here
})
```

**querySelectoryAll()** 将返回匹配元素的数组，因此使用 **forEach** 来迭代每个元素。

## 。html()

**。html()**jQuery 的方法用于获取或设置一个节点/元素的 HTML 内容。

要在 JavaScript 中做同样的事情，您可以使用**。任何 Html 元素的 innerHtml** 属性。

假设您需要用类 **main-div 获取 div 元素的 HTML 内容。你可以用 JavaScript 这样做。**

```
var htmlContent = document.querySelector('.main-div').innerHtml;
```

类似地，要设置同一个 div 的 HTML 内容，您可以这样做:

```
document.querySelector('.main-div').innerHtml = '<p>Hello World</p>';
```

记住，如果你想用同一个选择器改变多个元素的 Html，你可以像这样使用 **querySelectorAll()** 方法:

```
document.querySelectorAll('.main-div').forEach((*el, index) => {*
_el.innerHtml = '<p>Hello World</p>';
});
```

注意，我在这里将索引**传递给匿名函数**。在这种情况下，您并不真的需要它，但是将数组中每个元素的索引放在手边是一个好习惯。你永远不知道你什么时候可能需要它。

## 。瓦尔()

**。val()**jQuery 的方法用于获取或设置输入元素的值。

要在 JavaScript 中获取或设置输入元素的值，可以使用该元素的**值**属性。

用类别**输入框**将输入元素的值设置为**‘新’**

```
document.querySelector('.input-box').value = 'new';
```

获取同一元素的值

```
var inputValue = document.querySelector('.input-box').value;
```

## 。文本()

jQuery 优惠**。text()** 方法获取一个元素的所有内容，不包括标记。例如，使用 jQuery 获取< p > Hi < /p >元素的**文本**将返回字符串**‘Hi’。当你试图让网页上的内容真实可见时，这非常有用。**

也就是说，每个 HTML 元素都有一个属性“innerText ”,它本质上拥有相同的值。

```
let textValue = document.querySelector('.main-div').innerText;
```

## DOM 样式化

jQuery 最令人兴奋的特性之一是修改 DOM 样式的能力。当你试图获得用户交互的视觉反馈时，这非常有用。

可以通过以下两种方式之一来修改样式:

**添加/删除类:**
要添加或删除一个类，您肯定会使用**。addClass()** 或者**。jQuery 附带的 removeClass()** 方法。要在 JavaScript 中做同样的事情，你可以使用元素的属性。这可以如下所示完成。
将类 **testClass** 添加到 Id 为 **testElement** 的元素中

```
document.querySelector('#testElement').classList.add('testClass');
```

从 Id 为 **testElement** 的元素中删除类 **testClass**

```
document.querySelector('#testElement').classList.remove('testClass')
```

**添加/修改 CSS:**
要直接改变一个元素的 CSS，我们使用**。jQuery 提供的 css()** 方法。同样，这在 JavaScript 中很容易做到。
要设置一个元素的样式，我们只需设置该元素的 **style** 属性所公开的样式的值。

```
document.querySelector('#testElement').style.color = '#ffffff';
```

这将把 Id 为 **testElement** 的元素的颜色设置为白色。
注意:由于元素的样式被暴露为 JavaScript 对象属性，您将不能直接使用属性，如**背景色**，因为属性名称中有一个“-”。在这些情况下，您应该简单地使用这些属性的 **camelCased** 版本。例如，

```
document.querySelector('#testElement').style.backgroundColor = '#000000';
```

这将把 Id 为 **testElement** 的元素的背景色设置为黑色。

## 。属性()

在开发 web 应用程序时，设置和检索 HTML 元素的属性值再次变得非常有用。您可能需要获得一个 **data-** 属性的值，该属性在使用您的应用程序时存储重要的数据。jQuery 通过公开**来提供这种能力。attr()** 方法。您可以将它与一个参数一起使用来获取属性的值，或者使用带有两个参数的重载来设置 HTML 属性的值。

在 JavaScript 中，HTML 元素的每个属性都作为该元素的一个属性公开。这意味着要设置一个 **img** 元素的 **src** 属性的值，您可以简单地执行以下操作:

```
document.querySelector('img').src = '/images/image.png';
```

就这么简单。对于像 **data-** 这样的属性，您可以使用另一种方法，

```
document.querySelector('img').setAttribute('data-title', 'test');
```

为了获得相同元素的值，

```
document.querySelector('img').getAttribute('data-title');
```

**getAttribute()** 和 **setAttribute()** 与 jQuery 的 **attr()** 方法非常相似。所以，习惯这些应该没那么难。

## 。show() /。隐藏()

同样，两个最广泛使用的 jQuery 方法是**。显示()**和**。hide()** 方法。这些方法确实如它们所说的那样，隐藏或显示 HTML 元素。本质上，这些方法只是修改元素的显示样式属性。因此，我们可以使用简单的样式修改来实现同样的事情。

要隐藏元素:

```
document.querySelector('.test-element').style.display = 'none';
```

显示隐藏的元素

```
document.querySelector('.test-element').style.display = 'block';
```

## 创建交互式、快速动态网页应用的网页开发技术

我最喜欢和最常用的 jQuery 功能之一是使用 AJAX 发送 HTTP 请求。然而，我们必须明白 **jQuery.ajax()** 是现有 JavaScript 功能的包装器。毫无疑问，jQuery 使得使用 AJAX 变得轻而易举，但这不是我们在这里的原因，对吗？

要向 URL“home/sendmessage”发送包含数据{id: 23，name: 'faisal'}的 POST 请求，我们需要执行以下操作:

```
let request = new XMLHttpRequest();// Set an event handler when the status of the request changesrequest.onreadystatechange = function(response) {
    if (request.readystate == 4 && request.status == 200) {
        // do stuff with the response object
    }
}// Open a **Post** request to the url **home/sendmessage** asynchronously.request.open('POST', 'home/sendmessage', true);// Set the request header/srequest.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');//Send parameterized data as part of the Post requestrequest.send('id=23&name=faisal');
```

就是这样。您的发布请求将被发送到服务器。

如果您是 jQuery 开发人员，您会知道这些并不是 jQuery 附带的所有方法和特性。肯定还有更多，但我只是试图解决最常见的问题，只是为了让您了解在浏览器中没有 jQuery 也是可能的。

我希望这足以推动你使用越来越多的 JavaScript。