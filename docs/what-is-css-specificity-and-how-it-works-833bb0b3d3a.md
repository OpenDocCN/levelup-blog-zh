# 什么是 CSS 特异性，它是如何工作的？

> 原文：<https://levelup.gitconnected.com/what-is-css-specificity-and-how-it-works-833bb0b3d3a>

## 了解如何根据元素的类和 id 将 CSS 样式应用于元素

![](img/cad5117ba8f26f895b707a71209485bd.png)

照片由 [Goran Ivos](https://unsplash.com/@goran_ivos?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

CSS 代表级联样式表。这意味着样式流过整个应用程序，并应用于具有匹配标识符的元素。由于 CSS 是一种全局样式机制，我们必须了解当有冲突时浏览器如何决定应用什么样式。

*“级联”是指当一个特定元素有多个 CSS 声明和多个样式表的组合时，解决应用于该元素的样式之间的冲突。*

在本文中，我们将使用一个叫做**特异性**的概念来看看如何应用冲突的样式规则。

如果你使用过像 bootstrap 这样的 CSS 框架，你可能会注意到你不能够轻易地为一些像按钮这样的元素添加额外的样式。这是因为 CSS 的特殊性。所以你需要添加一个更“具体”的选择器来改变按钮的 CSS。

看看下面的代码:

```
// HTML
<p class="para" id="para_id">This is a paragraph</p>// CSS
p {color: red;}#para_id {color: green;}.para {color: blue;}
```

你认为，段落的颜色会是什么？你困惑吗？

**回答:**颜色会是绿色。

我知道，在你理解什么是 CSS 特异性之前，预测它真的很令人困惑。

所以让我们深入研究一下。

将应用的 CSS 由所有浏览器中标准化的特性优先级决定。下面的列表是按照特定性优先顺序递增的。这意味着连接到最高优先级的样式将应用于该元素。

1.`!important`规则具有最高优先级
2。内嵌样式是第二高的优先级
3。`id`是第三优先级
4。类、伪类和属性是第四优先级
5。元素和伪元素是第五优先级

所以在上面的代码中，没有`!important`或者内联样式，所以它们退出了游戏。但是，有一个`id`、`class`和元素选择器。所以由于`id`的优先级最高，所以它获胜，绿色将成为该段落的颜色。

你可以在这里玩代码:[https://codepen.io/myogeshchavan97/pen/WNbxqMO?编辑=1100#0](https://codepen.io/myogeshchavan97/pen/WNbxqMO?editors=1100#0)

试着移除`id`或`class`，看看会发生什么。

现在，看一下下面的代码

```
// HTML
<div id="list">
  <p class="favorite" id="must-buy"><span>Butter</span></p>
</div>// CSS
div#list #must-buy { color: red; }
.favorite span { color: blue !important; }
```

你能辨认出文字“黄油”的颜色吗？

**回答:**会是蓝色的

为什么？因为尽管`id`比`class`优先级高，但是`!important`具有最高优先级，所以它总是赢。

这里玩玩吧:[https://codepen.io/myogeshchavan97/pen/VwYKZmX?编辑=1100#0](https://codepen.io/myogeshchavan97/pen/VwYKZmX?editors=1100#0)

看看下面的代码:

```
// HTML
 <div class="list">
    <p class="favorite">
      <span class="highlight">Butter</span>
    </p>
 </div>// CSS
div.list p .highlight { color: red; }
div.list p .highlight:nth-of-type(odd) { color: black; }
```

看起来很复杂，对吧？

我们来简化一下。

我们将记录选择器出现的次数。因此，考虑一下第一条 CSS 规则

```
div.list p .highlight { color: red; }
```

1.！重要:没有！重要所以计数是**0**2。内嵌样式:没有内嵌样式，所以计数为**0**3。Id:没有 id，所以计数是**0**4。类、伪类和属性:这里有两个类，**。列出**和**。突出显示**所以计数是**2**5。元素和伪元素:有 2 个元素， **div** 和 **p** ，所以计数为 **2** 。

所以如果我们把它们写成一行，就变成了

```
 0 0 0 2 2
```

现在考虑下一个 CSS 规则

```
div.list p .highlight:nth-of-type(odd) { color: black; }
```

1.！重要:没有！重要所以计数是**0**2。内嵌样式:没有内嵌样式，所以计数是**0**3。Id:没有 id，所以计数是**0**4。类、伪类和属性:共有 2 个类**。列出**和**。突出显示**和 1 个伪类**第 n 个类型**，因此计数为**3**5。元素和伪元素:有 2 个元素 **div** 和 **p** ，所以计数为 **2**

所以如果我们把它们写成一行，就变成了

```
 **0 0 0 3 2**
```

为了确定应用了哪一个，我们需要比较每个列的值

**0 0 2 2->div . list p . highlight {颜色:红色；} **0 0 3 2->**div . list p . highlight:n-of-type(奇数){ color:黑色；}**

第一列是 0。
第二列为 0。
第三列是 0
第四列是第一条规则的 2，第二条规则的 3，所以第二条规则是赢家，颜色是黑色。

在这里玩一下:[https://codepen.io/myogeshchavan97/pen/KKwgPmp?editors=1100](https://codepen.io/myogeshchavan97/pen/KKwgPmp?editors=1100)

现在你明白了…

如果所有列都具有如下所示的相同值，会发生什么情况:

0 0 0 2 2
0 0 0 2 2

在这种情况下，最后编写的规则将优先，CSS 将被应用。因此，如果您导入多个样式表，当特异性相等时，最后导入的样式表将应用其样式。

## **要点:**

1.  `!important`具有最高优先级，不能被覆盖。这意味着我们不应该用它来应用样式，因为很难管理冲突和调试 CSS。只有在没有其他选择的情况下才应该使用它。
2.  使用`id`比`class`少。`id`优先于类，所以你必须小心使用它，以确保如果你在你的应用程序中共享组件，你不会创建一个太具体而不能改变的样式。
3.  解决样式冲突的最好方法是添加更多的`class`来提高特异性。这使得你的 CSS 代码更有目的性，并防止你遇到这样的情况:你有一个用`id`或`!important`样式的元素，但你不能改变它。

**别忘了直接在你的收件箱** [**这里**](https://yogeshchavan.dev) **订阅我的每周简讯，里面有惊人的技巧、窍门和文章。**