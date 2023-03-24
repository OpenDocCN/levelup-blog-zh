# JavaScript 通过类名获取元素

> 原文：<https://levelup.gitconnected.com/javascript-get-element-s-by-class-name-fd35b1ed7d2>

了解如何使用 JavaScript 中的 **getElementsByClassName()** 和 **querySelectorAll()** 方法通过**类名**获取**一个或多个 HTML 元素对象**。

1.  [使用**getElementsByClassName()**](https://softauthor.com/javascript-get-elements-by-class-name/#get-element-by-class-name-using-getelementsbyclassname)通过类名获取元素
2.  [使用**query selectorall()**](https://softauthor.com/javascript-get-elements-by-class-name/#get-element-by-class-name-using-queryselectorall)通过类名获取元素
3.  [用**多个类**](https://softauthor.com/javascript-get-elements-by-class-name/#get-element-by-multiple-class-names) 按类名获取元素
4.  [从**父元素**](https://softauthor.com/javascript-get-elements-by-class-name/#get-elements-by-class-name-from-parent-element) 中按类名获取元素

# 1.使用 getElementsByClassName()通过类名获取元素

创建五个具有相同类名 **box** 的 div 元素。

> 与 ID 名称不同，一个 HTML 文档可以有多个类名。

```
<div class="box">box 1</div>
<div class="box">box 2</div>
<div class="box">box 3</div>
<div class="box">box 4</div>
<div class="box">box 5</div>
```

**通过对**文档**对象运行**getElementsByClassName()**方法，获取所有包含 **box** 类的** HTML 元素对象。

```
const boxes = document.getElementsByClassName("box");
console.log(boxes); // HTMLCollection
```

**getElementsByClassName()**方法接受一个参数，该参数是**类**属性的值。

在这种情况下**框**。

> **getElementsByClassName()**方法的参数中的**字符串**必须与 HTML 标记中指定的 class 属性值相匹配。

一旦**getElementsByClassName()**方法找到一个或多个元素，它将把它们作为 **HTMLCollection** 返回。

[HTMLCollection](https://softauthor.com/javascript-htmlcollection-vs-nodelist/) 是一个类似数组的对象，它具有 **length** 属性，该属性返回其中的许多项。

```
boxes.length; // 5
```

使用 **for** … **of** 遍历 **HTMLCollection** 以获取其中的每个元素。

```
for(box of boxes) {
  console.log(box); // div.box, div.box, div.box, div.box, div.box
}
```

# 2.使用 querySelectorAll()通过类名获取元素

或者， **querySelectorAll()** 方法也**通过类名获取**一个或多个元素。

在**文档**对象上调用 **querySelectorAll()** 方法。

与**getElementsByClassName()**方法类似， **querySelectorAll** ()方法接受一个参数，即**类名**。

与 **getElementsByClassName** ()中的参数不同， **querySelectAll()** 的**参数**中的**类名**必须在**前面加上点(.)**

```
const boxes = document.querySelectorAll(".box");
console.log(boxes); // NodeList
```

它返回 [NodeList](https://softauthor.com/javascript-htmlcollection-vs-nodelist/) ，这是文档节点的集合。

NodeList 可以通过 **forEach()** 方法或者循环的**for…进行迭代。**

```
boxes.forEach(box => {
   console.log(box);
});
```

# 3.通过多个类名获取元素

让我们看看如何**获得一个或多个元素**，这些元素有**多个类名**。

```
<div class="box">box 1</div>
<div class="box">box 2</div>
<div class="box red">box 3</div>
<div class="box red">box 4</div>
<div class="box">box 5</div>
```

**获取 HTML 元素**，它有两个类，分别是 **box** 和 **red** 。

为此，**调用**文档**对象上的**getElementsByClassName()**方法。**

它需要**一个参数**，即类名**方框**和**红色**，中间用一个**空格**隔开**引号**。

```
const redBoxes = document.getElementsByClassName("box red");
redBoxes.length; // 2
```

# 4.通过类名从父元素获取元素

了解如何通过**类名**从**父**元素中**获取元素**。

```
<section id="box-container">
  <div class="box">box 1</div>
  <div class="box">box 2</div>
  <div class="box">box 3</div>
  <div class="box">box 4</div>
  <div class="box">box 5</div>
</section>
```

通过调用**文档**对象上的 **getElementById()** 方法获取**父元素**。

将其分配给一个常量 **boxContainer** 。

然后，**调用 **boxContainer** 上的**getElementsByClassName()，它是**父元素**。

> getElementsByClassName()可以在任何父元素对象上调用，其中 as[getElementById()](https://softauthor.com/get-element-by-id-in-javascript/)方法只能在全局文档对象上调用。

```
const boxContainer = document.getElementById("box-container");
const boxes = boxContainer.getElementsByClassName("box"); 
console.log(boxes); // HTMLCollection
```

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)