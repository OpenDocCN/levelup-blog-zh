# 有用的 JavaScript 技巧——黑暗模式和切片

> 原文：<https://levelup.gitconnected.com/useful-javascript-tips-dark-mode-and-slices-16d8a71ef60c>

![](img/dfc8b291cd8d8970cf65089f89cb761d.png)

照片由[水貂在](https://unsplash.com/@minkmingle?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上

像任何类型的应用程序一样，JavaScript 应用程序也必须写得很好。

否则，我们以后会遇到各种各样的问题。

在本文中，我们将了解一些应该遵循的技巧，以便更快更好地编写 JavaScript 代码。

# 检查浏览器中的黑暗模式

我们可以使用`matchMedia`方法在浏览器中检查黑暗模式。

例如，我们可以写:

```
window.matchMedia('(prefers-color-scheme: dark)').matches
```

我们还可以附加一个监听器来监听模式变化。

例如，我们可以写:

```
window
  .matchMedia('(prefers-color-scheme: dark)')
  .addEventListener('change', event => {
    if (event.matches) {
      //dark mode
    } else {
      //light mode
    }
  })
```

`event.matches`在运行`if`模块之前匹配黑暗模式。

# 检查一个元素是否是另一个元素的后代

我们可以通过使用`parentNode`属性来检查一个元素是否是另一个元素的后代。

例如，我们可以创建一个函数来遍历 DOM 树，检查一个元素是否有给定的节点。

我们可以写:

```
const isDescendant = (el, parent) => {
  let child = el;
  while (child = child.parentNode) {
    if (child === parent) {
      return true
    }
  }
  return false
}
```

我们有一个函数接受`el`和`parent`这两个 DOM 元素。

然后我们使用一个`while`循环从`child`开始遍历，从`el`开始，通过将`parentNode`属性值赋给`child`来遍历树。

然后在循环内部，我们用`===`检查它们是否是同一个元素。

如果树上没有等于`parent`的元素，那么我们返回`false`。

因此，如果我们有下面的 HTML:

```
<div>
  <p>
    foo
  </p>
</div>
```

然后是下面的日志`true`:

```
const p = document.querySelector('p');
const div = document.querySelector('div');
console.log(isDescendant(p, div))
```

另一方面，如果我们有:

```
<div>

</div><p>
  foo
</p>
```

那么相同的代码将记录`false`。

# 删除 JavaScript 中字符串的第一个字符

要返回一个去掉了原字符串第一个字符的新字符串，我们可以使用`slice`方法。

例如，我们可以写:

```
const text = 'foo'
const shortened = text.slice(1);
```

那么`shortened`就是`'oo'`。

# 删除 JavaScript 字符串的最后一个字符

删除 JavaScript 字符串的最后一个字符有点困难。

但是我们可以用`slice`的方法来做。

例如，我们可以写:

```
const text = 'foo'
const shortened = text.slice(0, -1);
```

我们传入第二个参数，带有切片的结束索引。

最后一个索引不包括在返回的字符串中。

`-1`表示字符串的最后一个索引。负索引从右到左排列。

所以-2 是倒数第二个索引，-3 是倒数第三个索引，依此类推。

因此，`shortened`就会是`'fo'`。

# 向 HTML 画布添加文本

我们可以通过使用画布上下文的`fillText`方法向 HTML 画布添加文本。

假设我们有以下画布:

```
<canvas width="300" height="300"></canvas>
```

我们可以编写以下内容来添加一些文本:

```
const canvas = document.querySelector('canvas');
const context = canvas.getContext('2d');
context.fillText('hello world', 100, 100);
```

`fillText`第一个论点是文本本身。

第二个和第三个参数是以像素为单位开始绘制文本的点的 x 和 y 坐标。

# 将数组分成两半

我们可以使用`Math.ceil`和`slice`方法将一个数组分成两半。

例如，我们可以编写以下内容:

```
const list = [1, 2, 3, 4, 5, 6, 7, 8];
const half = Math.ceil(list.length / 2);    
const firstHalf = list.slice(0, half);
const secondHalf = list.slice(-half);
```

返回一个新的数组，所以我们可以用它根据给定的开始和结束索引对它进行切片。

`half`是中点的指标。

如果`half`被用作切片的结束索引，它将被排除。如果它被用作起始索引，那么它被包括在内。

因此`firstHalf`具有从第一个到索引为`half`的前一个的条目。

并且`secondHalf`具有从索引`half`到结尾的条目。

因此，`firstHalf`就是`[1, 2, 3, 4]`。

而`secondHalf`就是`[5, 6, 7, 8]`。

![](img/57410a96594d65da037a8e25cb7bfaa2.png)

照片由 [Kouji 鹤](https://unsplash.com/@pafuxu?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以用`matchMedia`检查浏览器中的黑暗模式。

同样，我们可以用`slice`提取字符串或数组的一部分。

我们可以使用`parentNode`属性来获取 DOM 元素的父元素。