# JavaScript 问题—元素、字符串和循环

> 原文：<https://levelup.gitconnected.com/javascript-problems-elements-strings-and-loops-f38ca0b82a7c>

![](img/8d32dc13aa0dd1d9e880391754d37755.png)

[rafarudol](https://unsplash.com/@rudol?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

像任何类型的应用程序一样，当我们编写 JavaScript 应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些常见 JavaScript 问题的解决方案

# 按 ID 删除元素

我们可以通过 ID 删除一个元素。

有几种方法可以做到这一点。例如，我们可以将元素的`outerHTML`属性设置为空字符串:

```
document.getElementById("element-id").outerHTML = "";
```

我们还可以获得元素的父节点。

当我们可以在父元素上调用`removeChild`并把我们想要移除的元素作为参数传入时:

```
const element = document.getElementById("element-id");
element.parentNode.removeChild(element);
```

# 偏移 HTML 锚点以调整另一个元素

我们可以让一个元素的`position`属性成为相对属性，然后我们可以把它移动到我们喜欢的任何地方。

例如，如果我们有一个`a`元素:

```
<a class="anchor"></a>
```

然后我们可以通过书写来移动它:

```
a.anchor {
  display: block;
  position: relative;
  top: -250px;
  visibility: hidden;
}
```

通过将`position`设置为`relative`，我们可以将锚元素移动到我们喜欢的任何地方。

# 比较 JavaScript 对象

我们可以用几种方法比较物体。

但是，我们不能使用比较运算符来比较它们，因为只有当两个对象引用内存中的同一个对象时，它们才被认为是相等的。

`Object.is`也是如此。

所以，两个都不能用。

相反，比较两个对象的一种方法是使用`JSON.stringify`方法。

如果我们的对象中只有原始值或其他对象，那么我们可以使用它。

我们可以写:

```
JSON.stringify(obj1) === JSON.stringify(obj2)
```

属性的顺序对此很重要。

如果它们的顺序不同，那么它们被认为是不同的。

所以如果我们有:

```
const obj1 = { a: 1, b: 2 };
const obj2 = { b: 2, a: 1 };
```

然后它们会按照这个顺序被字符串化，所以它们会是不同的字符串。

我们也可以使用 Lodash 来比较它们。它有`isEqual`方法来比较它们。

例如，我们可以写:

```
_.isEqual(obj1 , obj2)
```

来比较它们。

# 从下拉列表中获取选定的选项

我们可以使用 jQuery 的`text`方法来获取选中的值。

例如，我们可以写:

```
const selected = $('#dropdown').find(":selected").text();
```

假设 select 元素的 ID 为`dropdown`，我们可以使用`:selected`属性来获取选中的项目。

然后我们可以使用`text`方法来获得实际选择的值。

`val()`不起作用，因为点击一个选项不会改变下拉列表的值。

只添加了`:selected`属性。

# Async 和 Await 和 forEach

我们不能将`forEach`与`async`和`await`一起用于并行或顺序处理事情。

如果我们想按顺序运行多个承诺，我们可以使用 for-of 循环:

```
const printFiles = async () => {
  const files = await getFilePaths(); for (const file of files) {
    const contents = await fs.readFile(file, 'utf8');
    console.log(contents);
  }
}
```

for-of 循环将按顺序运行承诺。

自 ES2018 年以来，还推出了“等待”。

例如，我们可以写:

```
const printFiles = async () {
  const files = await getFilePaths() for await (const file of fs.readFile(file, 'utf8')) {
    console.log(contents)
  }
}
```

他们都做同样的事情。

如果我们想并行运行承诺，我们可以使用`Promise.all`。

例如，我们可以写:

```
const printFiles = async () => {
  const files = await getFilePaths();
  const contents = await Promise.all(files.map((file) => fs.readFile(file, 'utf8')));
  console.log(contents);
}
```

我们将`files`条目映射到承诺，以便我们可以与`Promise.all`并行运行它们。

# 检查字符串是否以给定的子字符串结尾

string 有`endsWith`方法来检查一个字符串是否以给定的子字符串结尾。

例如，我们可以写:

```
const endWithWord = str.endsWith("world");
```

我们只需用想要检查的子字符串调用`endsWith`。

![](img/7e5798c1158a1636c85043aa25b5b9ee.png)

由 [Behzad Ghaffarian](https://unsplash.com/@behz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 结论

异步函数不能与`forEach`一起工作。相反，我们可以使用 for-of、for-await-of 或`Promise.all`来运行多个承诺。

我们可以使用`endsWith`来检查一个字符串是否以给定的字符串结尾。

要从下拉列表中获取选中的项目，我们可以使用`:selected` CSS 属性。

我们可以通过 ID 移除一个元素，方法是获取它的父元素，然后在我们想要移除的元素上调用`removeChild`。