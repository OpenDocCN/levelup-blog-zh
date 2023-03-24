# JavaScript 的基本 DOM 操作

> 原文：<https://levelup.gitconnected.com/basic-dom-operations-in-javascript-1a9c910e33ae>

了解 JavaScript 中一些基本但重要的 DOM 方法

![](img/5f7faa1be9c62872d89a8d7ee97a844a.png)

**图像由** [**潘卡杰·帕特尔**](https://unsplash.com/@pankajpatel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) **由 Unsplash**

下面的代码是我们用来访问和操作 JavaScript DOM 方法的代码:

```
<div id="parent"> 
  <div class="child one"> < /div>
  <div class="child children second"> < /div>
  <div class="child third"> < /div>
</div>
```

1.  通过`id`选择 and 元素。它将总是返回 1 个元素，因为要求`id`是唯一的。

```
document.getElementById(element_id);Example:**document.getElementById('parent');** 
```

2.通过`class`名称选择所有元素。

```
document.getElementsByClassName(class_name); 
// return all elements with class name as an arrayExample: document.getElementsByClassName('child'); // return child elementdocument.getElementsByClassName('child children');
// the above returns element which has both **child & children class**
```

3.按标记名选择元素

```
document.getElementsByTagName(tag_name);Example: document.getElementsByTagName('**div**');
```

4.通过选择器选择第一个元素，这将返回文档中与指定的选择器组匹配的第一个元素。

```
document.querySelector(selector);Exampledocument.querySelector('.child'); 
```

5.通过选择器选择所有元素。

```
document.querySelectorAll(selector);Example document.querySelectorAll('.child');
```

6.获取元素的子元素。

```
var parent = document.getElementById('parent');parent.childNodes; returns NodeListparent.children; returns HTMLCollection
```

7.找出孩子的数量。

```
var parent = document.getElementById('parent');parent.childElementCount; // 2
```

8.找到父节点。

```
var child = document.querySelector('.child');child.parentElement;
```

9.创建新的 DOM 元素。

```
var div = *document*.*createElement*(*'div'*);
```

10.创建文本元素。

```
var *h1* = *document*.*createTextNode*(*"This is text element"*);
```

11.添加一个元素作为子节点，作为其相邻节点的最后一个同级。

```
var parent = document.getElementById('parent');var div = *document*.*createElement*(*'div'*);parent.append(div);
```

12.添加一个元素作为子节点，作为其相邻节点的第一个同级。

```
var parent = document.getElementById('parent');var div = *document*.*createElement*(*'div'*);parent.prepend(div);
```

谢谢😊 🙏为了阅读📖。

关注我 [JavaScript Jeep🚙💨](https://medium.com/u/f9ffc26e7e69?source=post_page-----98efbae5e8aa----------------------)。

请在这里捐款[。你捐款的 80%捐给了需要食物的人🥘。提前感谢。](https://www.paypal.com/paypalme2/jagathishSaravanan)