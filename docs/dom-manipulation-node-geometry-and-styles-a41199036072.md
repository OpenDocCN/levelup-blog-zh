# DOM 操作—节点几何图形和样式

> 原文：<https://levelup.gitconnected.com/dom-manipulation-node-geometry-and-styles-a41199036072>

![](img/61f92a6956b78df504d975e28fe06394.png)

照片由 [niko photos](https://unsplash.com/@niko_photos?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是世界上最流行的编程语言之一。为了有效地使用它，我们必须了解它的基本知识。

在本文中，我们将研究如何获得节点的各种维度和样式。

# 获取元素 offsetTop 和 offsetLeft 值

`offsetTop`和`offsetLeft`从`offsetParent`中获取一个元素节点的像素值。

元素节点属性给出了从左上边框之外和`offserParent`左上边框之内的元素的像素距离。

`offsetParent`是通过搜索具有非静态 CSS 位置的最近祖先来确定的。

如果没有找到，则`body`位置用于`offsetParent`的值。

例如，如果我们在主体中有一个 div:

```
<!DOCTYPE html>
<html> <head> </head> <body>
    <div></div>
  </body></html>
```

然后我们可以用下面的方法得到 div 的位置:

```
const div = document.querySelector('div');console.log(div.offsetLeft);
console.log(div.offsetTop); 
console.log(div.offsetParent);
```

我们为`offsetparent`获取`body`，为其他属性获取数字。

# 获取元素的上、右、下和左边框边缘

`getBoundingClientRect()`方法获取一个元素的外部边界边缘相对于视窗顶部和左侧边缘的位置。

例如，如果我们有:

```
<!DOCTYPE html>
<html><head></head><body>
    <div></div>
  </body></html>
```

我们写道:

```
const rect= document.querySelector('div').getBoundingClientRect();console.log(rect);
```

然后我们得到`top`、`left`、`right`和`bottom`属性，其尺寸以像素为单位，相对于视口。

# 获取视口中元素的大小

`getBoundingClientRect()`方法也返回`height`和`width`属性。

例如，如果我们有:

```
<!DOCTYPE html>
<html> <head> </head> <body>
    <div></div>
  </body></html>
```

我们写道:

```
const rect = document.querySelector('div').getBoundingClientRect();console.log(rect);
```

我们得到的`height`和`width`属性的总高度和宽度包括填充和边框。

# 获取不包括边框的视口中元素的大小

`clientHeight`和`clientWidth`具有不包括边框的元素尺寸。

但是包括填充和内容。

如果我们有:

```
<!DOCTYPE html>
<html> <head> </head> <body>
    <div></div>
  </body></html>
```

我们可以写:

```
const div = document.querySelector('div');console.log(div.clientHeight, div.clientWidth)
```

# 获取视口中特定点的最顶端元素

获取对 HTML 文档中特定位置的最顶层元素的引用。

例如，如果我们有:

```
<!DOCTYPE html>
<html> <head> </head> <body>
    <div></div>
  </body></html>
```

然后我们可以通过写来使用它:

```
console.log(document.elementFromPoint(10, 12));
```

我们得到了`html`元素。

# 使用 scrollHeight 和 scrollWidth 获取被滚动元素的大小

`scrollHeight`和`scrolLWidtgh`给出了被滚动节点的高度和宽度。

例如，如果我们有一个滚动`html`元素的 HTML，那么我们得到被滚动的`html`元素的大小。

如果我们有:

```
<p></p>
```

并且:

```
p {
  height: 1000px;
  width: 1000px;
  background-color: green;
}
```

然后，我们通过写入获得正在滚动的 p 元素的宽度:

```
const p = document.querySelector('p');console.log(p.scrollHeight, p.scrollWidth);
```

每处房产我们得到 1000 英镑。

# 获取和设置从顶部和左侧滚动的像素

`scrollTop`和`scrollLeft`是读写属性，返回由于滚动而当前在可滚动视区中不可见的左侧或顶部的像素。

例如，如果我们有:

```
<p></p>
```

并且:

```
p {
  height: 1000px;
  width: 1000px;
  background-color: green;
}
```

然后，我们可以通过编写以下内容来设置它们:

```
const p = document.querySelector('p');
p.scrollTop = 750;
p.scrollLeft = 750;
```

然后，当我们记录这些值时，我们应该会看到它们。

# 将元素滚动到视图中

我们可以调用`scrollIntoView`方法将一个条目滚动到视图中。

例如，如果我们有:

```
for (let i = 0; i < 50; i++) {
  const p = document.createElement('p');
  p.innerText = i;
  document.body.appendChild(p);
}
```

为了生成 50 个 p 元素并将它们附加到正文，我们将第 41 个元素滚动到视图中，方法是:

```
document.querySelectorAll('p')[40].scrollIntoView(true);
```

![](img/1e74a9bc10c3860d7af382481940791c.png)

菲尔·古德温在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 样式属性

DOM 节点对象有`style`属性，用于样式化我们选择的元素。

它还返回元素的样式。

例如，如果我们有:

```
<div></div>
```

然后我们可以通过书写得到样式:

```
const styles = document.querySelector('div').style;
```

然后`styles`会有所有款式。

# 结论

我们可以用 DOM 对象的不同属性得到不同的维度，

要获取样式，我们可以使用`style`属性。