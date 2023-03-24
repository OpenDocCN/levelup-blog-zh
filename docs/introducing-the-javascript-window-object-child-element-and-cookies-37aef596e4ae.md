# 探索 JavaScript 窗口对象:记录子元素和 Cookies

> 原文：<https://levelup.gitconnected.com/introducing-the-javascript-window-object-child-element-and-cookies-37aef596e4ae>

![](img/7b6e00f57b41780e7f6e5d451044525a.png)

照片由 [Will Esayenko](https://unsplash.com/@willhime?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

`window`对象是一个全局对象，具有与当前 DOM 文档相关的属性，即浏览器选项卡中的内容。

在本文中，我们将查看`window.document`对象的属性，包括`lastElementChild`和`cookies`属性。

# window . document . lastelementschild

`lastElementChild`属性是一个只读属性，它获取文档的最后一个子元素。因为文档对象只有一个子元素，即`html`元素。这是将要返回的元素。该属性返回一个`Element`对象，该对象具有以下属性:

*   `attributes` —一个只读属性，具有包含 HTML 元素的已分配属性的`NamedNodeMap`对象
*   `classList`-具有类属性列表的只读属性。
*   `className` —只读字符串属性，具有元素的类
*   `cilentHeight` —只读数字属性，具有元素的内部高度
*   `clientLeft` —只读数字属性，具有元素左边框的宽度
*   `clientTop` —只读数字属性，具有元素上边框的宽度
*   `cilentWidth` —具有元素内部宽度的只读数字属性
*   `computedName` —只读字符串属性，其标签对可访问性公开。
*   `computedRole` —只读字符串属性，具有应用于特定元素的 ARIA 角色。
*   `id` —具有元素 ID 的字符串
*   `innerHTML` —包含元素内容的 HTML 标记的字符串
*   `localName` —只读字符串，包含元素限定名的本地部分
*   `namespaceURI` —一个只读属性，具有元素的名称空间 URL，如果没有名称空间，则为`null`
*   `nextElementSibling` —一个只读属性，具有另一个`Element`对象，该对象表示紧跟在当前元素之后的元素，如果没有同级节点，则为`null`。
*   `outerHTML` —表示包括其内容的元素标记的字符串。也可以设置它，使它用我们分配给这个属性的 HTML 替换元素的内容
*   `part` —具有使用`part`属性设置的零件标识符。
*   `prefix` —只读字符串属性，具有元素的名称空间前缀，如果没有指定前缀，则为`null`
*   `previousElementSibling` —一个只读属性，它有另一个`Element`对象，表示当前元素之前的元素，如果没有这样的节点，则为`null`。
*   `scrollHeight` —只读数字属性，具有元素的滚动视图高度
*   `scrollLeft` —具有元素左滚动偏移量的数字属性。这可以是一个 getter 或 setter
*   `scroolLeftMax` —只读数字属性，具有元素可能的最大向左滚动偏移量
*   `scrollTop` —一个数字属性，具有垂直滚动的文档顶部的像素数
*   `scrollTopMax` —一个只读属性，具有该元素可能的最大顶部滚动偏移量
*   `scrollWidth` —只读数字属性，具有元素的滚动视图宽度
*   `shadowRoot` —一个只读属性，它具有由元素托管的开放阴影根，如果不存在开放阴影根，则为`null`
*   `openOrClosedShadowRoot` —只读属性，仅适用于 WebExtensions，其影子根由元素托管，与状态无关。
*   `slot` —元素插入的阴影 DOM 槽的名称
*   `tabStop` —一个布尔属性，指示元素是否可以通过按 tab 键接收输入焦点
*   `tagName` —一个只读的，包含具有给定元素的标记名的字符串

例如，我们可以在下面的代码中使用它:

```
console.log(document.lastElementChild);
```

那么我们应该得到这样的结果:

```
<body>
    <div>
  A
</div>
<div>
  B
</div>
<div>
  C
  <div>
    D
  </div>
</div><script>
    // tell the embed parent frame the height of the content
    if (window.parent && window.parent.parent){
      window.parent.parent.postMessage(["resultsFrame", {
        height: document.body.getBoundingClientRect().height,
        slug: ""
      }], "*")
    }// always overwrite window.name, in case users try to set it manually
    window.name = "result"
  </script></body>
```

假设我们在 HTML 代码中有如下内容:

```
<div>
  A
</div>
<div>
  B
</div>
<div>
  C
  <div>
    D
  </div>
</div>
```

我们还可以获得`html` `Element`对象的单个属性，如以下代码所示:

```
const {
  clientLeft,
  innerHTML,
  outerHTML
} = document.lastElementChild;console.log(clientLeft);
console.log(innerHTML);
console.log(outerHTML);
```

然后我们应该得到`html`元素左边界的宽度为 0，并得到最后 2 个控制台日志记录的文档的 HTML 内容。

![](img/dd4b31e70675a6c524eb1a4aa29351cf.png)

照片由[达里奥·明加雷利](https://unsplash.com/@dariomingarelli?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# window.document.cookie

为了读写客户端 cookie，我们可以使用`cookies`属性来获取和设置 cookie。客户端 cookie 是键-值对，其中键和值由等号分隔，每个键-值对条目由分号分隔。每个键值对可以用空格分隔。RFC 6265 标准要求每个分号后都有一个空格。有一些特殊的键，浏览器可以选择跟在键-值对后面，指定要设置或更新的 cookie，并在前面加上分号运算符:

*   `;path=path` —例如:`/`或`/dir`，如果没有指定，则默认为当前文档位置的当前路径。path 的值必须是绝对路径。路径表示对 cookie 有效的 URL。
*   `;domain=domain` —示例包括`'example.com'`或'`subdomain.example.com`。如果没有指定值，默认值是当前文档的宿主部分。域名中的前导点将被忽略。一些浏览器可能拒绝设置包含此类点的 cookie。如果指定了域，则子域总是包含在内。域表示对 cookie 有效的 URL。
*   `;max-age=max-age-in-seconds`—cookie 持续的时间(以秒为单位)
*   `;expires=date-in-GMTString-format` —如果既未指定`expires`也未指定`max-age`，cookie 将在会话结束时过期。如果担心隐私，最好明确设置超时时间，而不是让浏览器来设置。让浏览器在会话过期后清除 cookie 是不可靠的。
*   `;secure` — Cookie 只能通过 HTTPS 传输。在 Chrome 52 之前，这个标志可以与来自 HTTP 域的 cookies 一起出现。
*   `;samesite` —如果设置了此选项，则浏览器不会发送带有跨站点请求的 cookie。标志的可能值是`lax`或`strict`。`strict`值将阻止目标站点的浏览器在所有跨站点浏览上下文中发送 cookie，即使是在跟随常规链接时。`lax`值将只为顶级导航 GET 请求发送 cookies。这对于用户跟踪来说是足够的，但它将防止许多 CSRF 攻击。

一些用户代理支持以下 cookie 前缀:

*   `__Secure-` —通知浏览器应该通过安全通道传输 cookies
*   `__Host-` —通知浏览器应该通过安全通道传输 cookie，并且 cookie 的范围仅限于服务器传递的指定主机路径。域属性也不能出现在 cookie 中，因为它与此前缀冲突。如果服务器省略了 path 属性，那么所有包含请求 URI 的路径都是 cookie 的有效路径

我们可以如下设置客户端 cookies:

```
document.cookie = "first_name=joe";
document.cookie = "last_name=smith";
console.log(document.cookie);
```

在上面的`console.log`输出中，我们应该得到类似这样的结果:

```
first_name=joe; last_name=smith;
```

我们也可以在 Chrome 的应用标签的 cookies 部分查看 Cookies。此外，我们可以设置特殊的键值对，如下面的代码中的`path`和`max-age`属性:

```
document.cookie = "first_name=joe; path=/;  max-age=5";
document.cookie = "last_name=smith";
console.log(document.cookie);
```

然后我们还可以在 Chrome 的应用程序标签的 cookies 部分查看 Cookies。它应该显示密钥名称、值、路径和过期属性值。

注意，对于标准的 JavaScript 库，没有简单的方法来操作 cookies。我们必须自己操作`document.cookie`管柱。因此，如果我们想找到一个键值对，我们可以做如下的事情:

```
const firstName = document.cookie.split(';').find(c => c.includes('first_name'));
console.log(firstName);
```

上面的代码将找到带有关键字`first_name`的 cookie 条目，这应该会让我们得到`first_name=joe`。

然后我们可以通过写下来分别得到键和值:

```
const [
  key,
  value
] = firstName.split('=');
console.log(key, value);
```

那么我们应该得到:

```
first_name joe
```

从最后一个`console.log`语句开始。

`lastElementChild`属性获取文档对象的最后一个子元素，它应该是`html`元素，因为它是唯一的子元素。`cookie`属性让我们用自己的属性和相应的值来获取和设置 cookies。此外，我们可以设置特殊的属性和值，如`path`和`max-age`，分别控制对 cookie 有效的 URL 和 cookie 的到期时间。使用 JavaScript 标准库无法轻松操作 cookie，所以我们必须自己操作 cookie 字符串。