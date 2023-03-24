# JavaScript 最佳实践—性能

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-performance-a549bc87ed20>

![](img/806fc6bbdcad5c5a0ac1a6f6b5e784e1.png)

[哈雷戴维森](https://unsplash.com/@harleydavidson?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

像任何其他编程语言一样，JavaScript 有自己的最佳实践列表，使程序更容易阅读和维护。JavaScript 有很多棘手的部分，所以我们应该避免降低代码质量的事情。通过遵循最佳实践，我们可以创建优雅且易于管理的代码，任何人都可以轻松使用。

在这篇文章中，我们将看看如何提高我们的应用程序的性能。动作包括在变量中缓存数据，使用最快的方式遍历变量，减少页面上的 DOM 访问和元素，以及推迟脚本加载。

# 减少对变量和属性的访问

我们应该减少在应用程序中访问变量和对象属性的次数。

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

# 遍历变量的最快方法

在 JavaScript 中，有多种方法可以遍历 iterable 对象。一个是传统的`for`循环。其他方法包括`for...of`循环，数组的`forEach`方法。`map`和`filter`也在映射和过滤操作完成时循环遍历数组。还有一个`while`循环。

在所有运行循环的方法中，`for`循环是最快的方法，不管有没有缓存`length`，就像我们上面做的那样。然而，缓存`length`有时会使循环执行得更好。

一些浏览器引擎已经优化了`for`循环，但没有缓存 length 属性。

步进递减的`while`循环大约比`for`循环慢 1.5 倍

使用`forEach`循环比`for`循环慢 10 倍，所以最好避免使用，尤其是对于大型数组。

我们可以在这里看到结果[。](https://stackoverflow.com/questions/5349425/whats-the-fastest-way-to-loop-through-an-array-in-javascript)

# 减少 DOM 访问

访问 DOM 是一项开销很大的操作，因为浏览器必须从网页中获取元素，然后从中创建一个对象并返回它。

为了减少 DOM 访问，如果我们需要多次操作 DOM 节点对象，我们应该将它设置为一个变量。

例如，如果我们有下面的 HTML，我们想在几秒钟后为它设置一些文本:

```
<p id='foo'></p>
```

为此，我们可以编写以下代码:

```
const setText = (element, textContent) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      element.textContent = textContent;
      resolve();
    }, 3000)
  })
}(async () => {
  const foo = document.querySelector('#foo');
  await setText(foo, 'foo');
  await setText(foo, 'bar');
  await setText(foo, 'baz');
})();
```

在上面的代码中，我们有一个函数来获取我们想要操作的 HTML 元素，以及我们想要设置的文本内容。

`setText`函数返回一个承诺，在 3 秒钟后将文本设置为给定的元素。

然后我们有一个`async`函数来设置文本 3 次。重要的部分是我们在每个调用中传递对元素的引用。这样我们就不必三次从网页中获取元素，这是一个开销很大的操作。

![](img/616854635d3c5a77f6e1ed2cbe7a7abe.png)

照片由 [Sawyer Bengtson](https://unsplash.com/@sawyerbengtson?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 减小 DOM 大小

渲染 DOM 树很慢。因此，我们必须缩小树的大小。

除了让我们的网页尽可能简单之外，别无他法。

较小的 DOM 使得用`querySelector`、`getElementById`或`getElementsByTagName`等方法搜索元素更快，因为要找的东西更少。

此外，页面渲染性能也会提高，因为加载的内容更少了。对于手机和平板电脑等速度较慢的设备来说尤其如此。

# 不要声明不必要的变量

每次我们声明变量时，浏览器都必须为变量保留内存空间。因此，为了减少内存的使用，我们应该声明太多的变量。

例如，如果我们有以下 HTML:

```
<div id='foo'>
  <p> </p>
</div>
```

并且我们想要设置`p`元素的文本内容，我们不应该这样写:

```
const foo = document.querySelector('#foo');
const p = foo.querySelector('p');
p.textContent = 'foo';
```

因为我们有两个变量。这意味着我们的计算机必须存储 2 个 JavaScript 变量的值。

相反，我们可以通过编写以下代码来减少变量声明:

```
document.querySelector('#foo p').textContent = 'foo';
```

正如我们所见，我们可以使用`querySelector`方法用 CSS 选择器选择任何东西。这意味着我们应该使用这个方法和相关的`querySelectorAll`方法来选择元素，因为它们都可以使用 CSS 选择器来选择任何 HTML 元素节点。

# 推迟加载脚本

加载 JavaScript 文件是一项开销很大的操作。浏览器必须下载文件，解析内容，然后将其转换为机器代码并运行。

浏览器会一行一行地下载一个文件，所以它会阻止任何其他操作的发生。

所以，要尽量拖延。我们可以通过将`script`标签放在末尾来做到这一点。同样，我们可以使用`script`标签上的`defer`属性来实现这一点。

此外，我们可以在页面加载后运行脚本，方法是动态创建`script`元素，并按如下方式添加:

```
window.onload = () => {
  const element = document.createElement("script");
  element.src = "[https://code.jquery.com/jquery-1.12.4.min.js](https://code.jquery.com/jquery-1.12.4.min.js)";
  document.body.appendChild(element);
};
```

任何可以在页面加载后加载的内容都可以使用这种脚本加载方法。

我们可以通过做一些事情来加速我们的页面。首先，我们可以在变量中缓存数据，这样我们就不必重复访问它们。然后我们可以用`for`循环更快地遍历条目。

此外，我们可以减小 DOM 的大小，以减少需要加载的项目。我们还可以通过将 DOM 对象赋给变量来缓存它们。

我们也不应该声明不必要的变量，我们应该尽可能推迟脚本的加载，这样就不会耽误我们的浏览器。