# JavaScript 最佳实践—浏览器差异、性能等

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-browser-differences-performance-and-more-25f704f1bb25>

![](img/eebde0a939f1ae46f169f8251df90909.png)

[澳门图片社](https://unsplash.com/@macauphotoagency?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

像任何其他编程语言一样，JavaScript 有自己的最佳实践列表，使程序更容易阅读和维护。JavaScript 有很多棘手的部分，所以我们应该避免降低代码质量的事情。通过遵循最佳实践，我们可以创建优雅且易于管理的代码，任何人都可以轻松使用。

在本文中，我们将看看如何优雅地支持旧浏览器，避免大量嵌套，各种优化，并检查我们的程序中的数据。

# 渐进增强

进步增强意味着当某些技术不可用时，我们应该优雅地降级我们的应用程序。这意味着我们应该检查某些技术是否在我们想要支持的所有浏览器中都得到支持，如果不支持，就让我们的应用程序以某种方式工作。

我们还可以添加 polyfills 来增加对新技术的支持，这些新技术是我们所支持的旧浏览器所不具备的。

例如，如果我们想使用 Internet Explorer 11 中没有的新数组方法，但我们的应用程序仍然支持浏览器，那么我们必须为它添加一个 polyfill 或检查它是否存在，并做一些不同的事情而不是崩溃。

# 避免大量嵌套

在代码中有大量的嵌套使得它们很难阅读。这是因为很难理解代码的逻辑。

嵌套条件语句和循环应该保持在最低限度。

例如，不要写:

```
const items = {
  foo: [1, 2, 3],
  bar: [1, 2, 3],
  baz: [1, 2, 3]
};const parentUl = document.createElement('ul');
for (const item of Object.keys(items)) {
  const parentLi = document.createElement('li');  
  const childUl = document.createElement('ul');
  for (const num of items[item]){
    const childLi = document.createElement('li');
    childLi.textContent = num;
    childUl.appendChild(childLi);    
  }
  parentLi.textContent = item;
  parentLi.appendChild(childUl);  
  parentUl.appendChild(parentLi);
}
document.body.appendChild(parentUl);
```

这就创建了一个嵌套列表，读和写都很混乱。我们应该通过将列表创建分离到一个函数中并调用该函数来减少嵌套:

```
const items = {
  foo: [1, 2, 3],
  bar: [1, 2, 3],
  baz: [1, 2, 3]
};const createUl = (items) => {
  const ul = document.createElement('ul');
  for (const item of items) {
    const li = document.createElement('li');
    li.textContent = item;
    ul.appendChild(li);
  }
  return ul;
}const parentUl = createUl(Object.keys(items));
const parentLis = parentUl.querySelectorAll('li');
for (const parentLi of parentLis) {
  const childUl = createUl(items[parentLi.textContent]);
  parentLi.appendChild(childUl);
}
document.body.appendChild(parentUl);
```

正如我们所看到的，上面的代码没有任何嵌套循环，这使得代码更容易阅读。此外，我们有一个`createUl`函数来创建包含条目的`ul`元素，并返回`ui`元素对象。

这意味着我们可以随后将它附加到文档或 HTML 元素中。

# 优化循环

我们应该在单个变量中缓存每个文献中使用的值。

这是因为每次我们这样做时，CPU 必须一次又一次地访问内存中的项目来计算结果。

因此，我们应该尽可能少地这样做。

例如，如果我们有一个循环，我们不应该写如下:

```
for (let i = 0; i < arr.length; i++) {}
```

相反，我们应该写:

```
let length = arr.length;
for (let i = 0; i < length; i++) {}
```

这样，`arr.length`在我们的循环中只被引用一次，而不是在每次迭代中访问它。

![](img/fd0a8ba788a8c8da217154788bd17c3e.png)

[Adrien céSARD](https://unsplash.com/@adriencesard?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 将 DOM 访问保持在最低限度

DOM 操作是一项 CPU 和内存密集型操作。因此，我们应该努力把它保持在最低限度。

这意味着我们必须保持页面尽可能简单，并且只在必要时才进行 DOM 操作。任何静态样式都应该在 CSS 中，而不是用 JavaScript 动态添加。

此外，我们应该在 HTML 中保留任何静态元素，而不是通过操纵 DOM 来创建它们。

此外，我们应该创建创建元素并在需要时调用它们的函数，而不是在代码的顶层不断地进行 DOM 操作。

# 为所有浏览器编写代码

我们的代码应该对所有浏览器一视同仁。我们不应该编写 hacks 来适应各种浏览器，因为当浏览器改变版本时，这些 hacks 会很快被破坏。

我们应该坚持被接受为标准的代码，或者使用像 Modernizr 这样的库来处理不同浏览器的问题。

此外，我们可以添加 polyfills 来添加各种浏览器中缺少的任何功能，因此我们可以让我们的应用程序在不同的浏览器上运行，即使它们可能不支持某些现成的功能。

# 不要相信任何数据

我们应该检查用户输入的任何数据。HTML5 有很多表单验证功能来检查有效输入。我们可以用 HTML5 和普通 JavaScript 来实现。

一旦我们检查了输入的数据，我们还需要检查变量中的数据和函数返回的数据。因为 JavaScript 是一种动态类型语言，我们必须检查这些东西。

对于原始值，我们可以使用`typeof`操作符来检查数据的数据类型。例如，如果我们有:

```
let x = 1;
```

然后`typeof x`会返回`'number'`。其他原始数据类型，如布尔型、字符串型、未定义型等。是一样的。

唯一的例外是`null`，它有 object 类型。

我们应该经常检查像`null`或`undefined`这样的值，因为它们可能会使我们的程序崩溃。我们可以通过分别写`x === null`和`typeof x === 'undefined'`来实现。

此外，我们应该小心 JavaScript 进行的类型强制，比如在条件语句和函数调用中。例如，`Math.min`方法在被调用时会将其参数转换成数字。在返回结果之前,`==`操作符将所有操作数转换为相同的类型。

对于对象，我们可以通过使用`instanceof`操作符来检查它们的类型，看看它们是从哪个构造函数创建的。例如，如果我们有一个数组:

```
let arr = [];
```

那么`[] instanceof Array`将会是`true`。数组还有一个静态的`isArray`方法来检查数据类型。

我们写道，在 JavaScript 代码中，我们应该意识到我们的应用程序支持的不同浏览器之间的差异。这意味着检查我们想要使用的方法是否存在，并添加 polyfills 来添加旧浏览器缺少的功能。

我们应该缓存在循环中重复访问的变量和属性，这样它们就不必在每次循环运行时都被访问。

还应该避免深度嵌套，以保持代码清晰易懂。

此外，由于 DOM 操作是一种开销很大的操作，所以应该尽量减少。静态样式和元素应该分别在 CSS 和 HTML 中。

最后，我们不应该相信应用程序中的数据。必须检查输入的格式和有效性，并且应该检查变量和值中的数据的类型，包括`null`或`undefined`。