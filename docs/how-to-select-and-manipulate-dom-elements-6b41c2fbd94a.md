# 如何选择和操作 DOM 元素

> 原文：<https://levelup.gitconnected.com/how-to-select-and-manipulate-dom-elements-6b41c2fbd94a>

![](img/fc6890a50d038397ccd085b286f25d8f.png)

[Toa Heftiba](https://unsplash.com/@heftiba?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了使我们的网页动态，我们必须操纵页面上的元素，这样我们就可以获得动态效果。网络浏览器的标准库允许我们这样做。在`document`对象中有多个方法，该对象代表页面上的项目。它有一个完整的 DOM 树，包含了我们可以获取并操作的所有元素。在本文中，我们将研究一些从页面中获取元素的方法。

# getElementsByClassName

`document.getElementsByClassName`方法让我们通过类名获得页面上 DOM 对象的列表。这个方法将返回任何具有给定类的对象。它接受一个参数，该参数是一个字符串，其中包含一个或多个由空格分隔的类名。它是任何 DOM 元素的一部分，这意味着它也可以在该方法返回的元素上调用。

返回一个`NodeList`对象，这是一个类似数组的对象。它有一个`length`属性，可以被一个`for`循环或`for...of`循环遍历，但是像`forEach`这样的数组方法不包含在这个对象中。

最简单的例子是从下面的 HTML 中获取元素:

```
<p class='foo'>
  foo
</p>
<p class='foo'>
  bar
</p>
```

然后在我们的 JavaScript 代码中，我们可以通过编写下面的代码用`foo`类获取元素:

```
const foo = document.getElementsByClassName('foo');
```

一旦我们有了它，我们就可以访问元素，所以我们可以通过给它的属性赋值来操纵它们。例如，如果我们想改变样式，我们可以在`style`属性中设置属性值。

我们可以编写如下代码，用`foo`类将两个元素的文本颜色都设置为红色:

```
const foo = document.getElementsByClassName('foo');
for (const f of foo) {
  setTimeout(() => {
    f.style.color = 'red';
  }, 2000)
}
```

此外，我们可以向该方法传入多个类名。它们必须用空格隔开。它将选择包含所有类的元素。例如，如果我们向第一个`p`元素添加一个`bar`，如下所示:

```
<p class='foo bar'>
  foo
</p>
<p class='foo'>
  bar
</p>
```

然后，我们可以编写以下代码，在 2 秒钟后将第一个`p`元素的文本颜色更改为红色:

```
const foo = document.getElementsByClassName('foo bar');
for (const f of foo) {
  setTimeout(() => {
    f.style.color = 'red';
  }, 2000)
}
```

`style`属性有很多属性。几乎任何 CSS 选项都可以通过给`style`属性赋值来设置。

# getElementsByTagName

我们可以通过使用`document.getElementsByTagName`方法选择具有给定标记名的所有元素。

它接受带有标记名的字符串，并返回一个 NodeList 对象，其中包含带有给定标记的所有元素。与任何 NodeList 对象一样，我们可以通过一个`for`循环或一个`for...of`循环来循环，但是像`forEach`这样的数组方法不包含在这个对象中。

例如，我们可以编写以下代码，在给定以下 HTML 代码的情况下，在 2 秒钟后将所有的`p`元素设置为红色:

```
<p>
  foo
</p>
<p>
  foo
</p>
<div>
  baz
</div>
```

我们可以写:

```
const pElements = document.getElementsByTagName('p');
for (const p of pElements) {
  setTimeout(() => {
    p.style.color = 'red';
  }, 2000)}
```

将它们设置为红色，因为我们将`p`传递给了该方法。我们应该看到前两个元素在 2 秒钟后变成红色。我们可以看到`div`元素保持黑色，因为它不是`p`元素。

# getElementsByTagNameNS

`getElementsByTagNameNS`与`getElementsByTagName`方法非常相似，但是它需要两个参数而不是一个。第一个是带有 HTML 文档的 XML 名称空间的字符串，第二个与`getElementsByTagName`的第一个参数相同，即标记名。

名称空间是 XML 名称空间，它是用 XHTML 而不是 HTML 定义网页的语法的一部分。

返回值与`getElementsByTagName`相同，它是一个 NodedeList 对象，包含所有具有给定标记和 XML 名称空间的元素。

例如，如果我们有以下 HTML:

```
<html ae kf" href="http://www.w3.org/1999/xhtml" rel="noopener ugc nofollow" target="_blank">http://www.w3.org/1999/xhtml">
  <head>
    <title>Foo</title>
  </head>
  <body>
    <p>
      foo
    </p>
    <p>
      foo
    </p>
    <div>
      baz
    </div>
    <script src="main.js"></script>
  </body>
</html>
```

然后，我们可以在`main.js`中编写以下 JavaScript 代码，使`p`元素的文本在 2 秒钟后变为红色:

```
const pElements = document.getElementsByTagNameNS('[http://www.w3.org/1999/xhtml'](http://www.w3.org/1999/xhtml'), 'p');for (const p of pElements) {
  setTimeout(() => {
    p.style.color = 'red';
  }, 2000)}
```

我们必须用扩展名`.xhtml`命名 HTML 文件，以使这个方法有效，因为它只用于 XHTML 文档。

![](img/98a1a0371258ecb24bc5452cf560a3b5.png)

照片由[威尔·史密斯](https://unsplash.com/@willsmuth?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# getElementById

`document.getElementById`方法让我们通过 ID 获取页面上的 DOM 对象。我们可以通过传入一个带有我们想要选择的元素的 ID 的字符串来使用它。因为每页只能有一个给定 ID 的元素，所以它将只获取一个元素并返回它。

例如，如果我们有以下 HTML:

```
<p id='foo'>
  foo
</p>
```

然后，我们可以通过编写以下代码，在 2 秒钟后将颜色设置为红色:

```
const foo = document.getElementById('foo');
setTimeout(() => {
  foo.style.color = 'red';
}, 2000)
```

# 查询选择器

用上面的方法选择要操作的元素很笨拙，因为我们只能通过 ID 或类名选择元素。使用`querySelector`方法，我们可以使用任何 CSS 选择器来选择一个元素。

它接受一个参数，这个参数是一个字符串，带有我们想要选择的元素的 CSS 选择器。它返回一个元素对象，该对象是 CSS 选择器选择的第一个元素。如果我们传入的选择器无效，它会抛出一个语法错误。

特殊字符必须用反斜杠进行转义。

例如，如果我们有以下输入:

```
<input type='text'>
```

然后，我们想通过编写以下代码为它设置一个值:

```
const input = document.querySelector('input[type="text"]');input.value = 'abc';
```

我们可以对 CSS 选择器中的特殊字符进行转义，如下例所示。如果我们有一个 ID 为`ab\c`的输入，它有一个特殊字符斜杠:

```
<input type='text' id="ab\c">
```

然后，我们可以像下面的代码一样更改它的值:

```
const input = document.querySelector('#ab\\\\c');input.value = 'abc';
```

请注意，我们有 4 个斜线来表示字符串中的斜线字符。

如果我们的元素在选择器中有一个冒号，比如:

```
<input type='text' id="ab:c">
```

然后我们可以写:

```
const input = document.querySelector('#ab\\:c');input.value = 'abc';
```

冒号通过在前面加两个斜线来转义。

# querySelectorAll

像`querySelector`方法一样，`querySelectorAll`为我们想要选择的元素获取一个 CSS 选择器字符串。但是，它返回一个 NodeList 对象，其中包含所有与 CSS 选择器匹配的元素。

例如，如果我们可以编写以下 HTML 代码:

```
<p class='foo'>
  foo
</p>
<p class='foo'>
  foo
</p>
<p class='baz'>
  baz
</p>
```

然后，我们可以选择所有带有`.foo`类的元素，并通过编写以下代码在 2 秒钟后将文本设置为红色:

```
const fooElements = document.querySelectorAll('.foo');for (const f of fooElements) {
  setTimeout(() => {
    f.style.color = 'red';
  }, 2000)
}
```

# 对非文档元素使用上述方法

我们可以在非文档 DOM 对象上使用所有这些方法。例如，如果我们有以下 HTML 代码:

```
<div id='bar'>
  <p class='foo'>
    foo
  </p>
  <p class='foo'>
    foo
  </p>
  <p class='baz'>
    baz
  </p>
</div>
```

然后我们可以用任何方法和选择`bar`元素，这将返回`bar`元素 DOM 对象。然后，我们可以使用任何方法，包括我们之前使用的方法，在返回的对象中选择另一个元素。

例如，使用上面的 HTML，我们可以编写下面的代码来选择所有带有类`foo`的`p`元素，并在 2 秒钟后将它们的文本设置为红色:

```
const fooElements = document.querySelector('#bar').querySelectorAll('.foo');for (const f of fooElements) {
  setTimeout(() => {
    f.style.color = 'red';
  }, 2000)
}
```

如果我们用返回 NodeList 对象的方法替换`querySelector`，那么我们必须用`for`或`for...of`循环遍历它们，或者通过索引获取主题元素。

上面的大多数方法对于获取 DOM 元素和操作它们都非常有用。然而，我们可能可以忽略`getElementByTagNameNS`，因为自从 HTML 5 发布以来，我们不再编写 XHTML 文件。