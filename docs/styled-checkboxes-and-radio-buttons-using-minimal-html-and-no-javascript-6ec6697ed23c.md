# 使用最少的 HTML 而不是 JavaScript 设计复选框和单选按钮

> 原文：<https://levelup.gitconnected.com/styled-checkboxes-and-radio-buttons-using-minimal-html-and-no-javascript-6ec6697ed23c>

*这是我上一篇文章* [*的后续文章，只使用 CSS*](/scalable-styled-checkboxes-using-just-css-its-called-em-use-em-db21bae5f216)

*![](img/a2892bf086e4e1d9acc44e90bdb8ce7d.png)*

****注*** [本文已被一篇利用外观的较新文章取代:无；出于历史的考虑，我不写这篇文章。](https://medium.com/p/e08a9ceda0e8)*

*我经常惊讶于人们会用燃烧的火圈来完成最简单的事情。当 HTML 标签是一个复选框或单选按钮时，它的样式绝对符合这一点。*

*对于那些到处乱说“组件”这个词的人来说尤其如此……你知道组件的类型；React/Vue 那些一开始连写 HTML 都没资格的粉丝们。就像他们开始把事物当成“组件”的那一刻，所有最后一点理智和理性的发展都被从记忆中抹去了。*

# *现有方法的问题是*

1.  *他们经常用大量不需要的额外的类或元素来加载标记。*
2.  *许多人仍然使用 JavaScript 来实现这一点，尽管十多年来这并不是你真正需要的脚本。*
3.  *像素度量的使用意味着它们很难缩放，并且不会随着用户的字体大小选择而自动放大。再次强调，EM 是可访问的字体大小，其他的都是垃圾……所以使用它们吧！*
4.  *他们经常依靠我们也不需要的花哨的绝对定位技巧。*
5.  *它们很难简单地一概而论。*
6.  *隐藏真正的`<input>`标签的方法要么通过`display:none;`或`visibility:hidden;`破解屏幕阅读器/盲文阅读器*
7.  *或者隐藏所述标签比在屏幕阅读器上使用所需的复杂很多很多倍。说真的，伙计们，你所要做的就是把它放到屏幕之外。*

# *那么我们如何避免这些问题呢？*

*难就难在这里，这太简单了，太荒谬了。让我们设置三种不同的风格。一个正常的检查，一个开关，和两个收音机。*

## *HTML，利用短命名的空元素*

```
*<label>
  <input type="checkbox"><i></i>
  Standard Checkbox<br>
</label><label>
  <input type="checkbox"><ins></ins>
  Alternate Checkbox<br>
</label><label>
  <input type="checkbox"><b></b>
  Switch<br>
</label><label>
  <input type="radio" name="testRadioSet" value="1"><i></i>
  Radio 1<br>
</label><label>
  <input type="radio" name="testRadioSet" value="2"><i></i>
  Radio 2<br>
</label>*
```

*使用包装格式`<label>`意味着我们不需要 ID 或 FOR 属性，尽管我们使用的技术实际上两种方式都可行。输入后的额外标记做了所有的繁重工作。*

*在每种情况下，我们使用一个空的`<i></i>`、`<ins></ins>`或`<b></b>`作为元素来提供我们的样式。如果像 italic 这样的空标签对 font-awesome 来说足够好，那对我来说就足够好了！*

## *组合子和属性选择器是你的朋友！*

****注意*** *，这个 CSS 假设一个重置正在使用中，特别是边距、填充和边框被清零，并且框尺寸:border-box 已经应用于所有内容。**

*首先，我们需要隐藏真正的输入。*

```
*input[type="checkbox"],
input[type="radio"] {
  position:absolute;
  left:-999em;
}*
```

*将东西滑离屏幕而不是使用`display:none;`或`visibility:hidden;`可以让它们支持键盘、屏幕阅读器、盲文阅读器和其他非视觉媒体或替代导航。这就是为什么你会经常在模态对话框、弹出菜单等等中看到相同的方法。*

*用`display:none;`或`visibility:hidden;`隐藏流内容或实际控件是破坏可访问性和让搜索引擎忽略你的内容的最快方法之一，所以要小心使用它们！*是的，搜索也受到影响，他们寻找它，以便给那些试图使用“内容伪装”方法的 SEO 垃圾一巴掌。**

**—更正，原来我有 top:-999 em；这使得页面在焦点上滚动。通常这样做是为了避免 flex 的问题，但在测试中，我试图避免的拐角情况没有出现。感谢推特上的*[*M Akhyar Rahman H*](https://twitter.com/akhyarrh)****染上了那场流感。*****

****我们还应该捕获焦点状态，这样键盘导航就不会被忽略。****

```
****input[type="checkbox"]:focus + *,
input[type="radio"]:focus + * {
  box-shadow:0 0 0.25em 0.25em #0484;
}****
```

****一个简单的盒子阴影处理得很好。我会在制作中使用所有的输入，调整阴影风格，以最适合你的布局/设计。不要关闭默认的轮廓或者使用缺乏焦点样式的元素，至少要提供某种形式的视觉替代。****

****现在，我们所有的兄弟元素将共享一些样式或有重叠，这比每次都声明更快，代码更少。****

```
****input[type="checkbox"] + *,
input[type="radio"] + * {
  display:inline-block;
  line-height:1em;
  height:1em;
  width:1em;
  margin-top:0.25em;
  vertical-align:top;
  font-style:normal;
  font-weight:normal;
  text-align:center;
  text-decoration:none;
  border:0.0625em solid #666;
  transition:all 0.5s;
  cursor:pointer;
}****
```

****内联块允许我们实际遵守任何垂直填充、边距或高度。其中大部分都有固定的高度和宽度，顶部边距调整用垂直对齐:顶部补偿垂直对齐:中间通常被打破/无用的跨浏览器。由于这些标签中的一些可能有重量、风格或其他类似的装饰，我们将它们剔除，然后设置它们中大多数的公共边界并启用过渡。将光标设置为指针也是一个好主意，这样当鼠标经过它时，人们就可以知道它是可交互的。****

****现在，有些人可能会大发雷霆，说你不应该使用“*”通用选择器，因为它“很慢”。像许多疯狂的毫无根据的说法一样，这充其量只是半真半假，最糟糕的是一个露骨的谎言。 因为它受到相邻兄弟选择器的限制，这使得它成为所有元素的自然 DOM 遍历，这意味着应用程序不会比任何其他方法慢。正如我在文章"[中揭露的那样，CSS 选择器/组合器很慢，类很快——他们在骗你！关于通用选择器“速度”的说法更多的是基于人们被告知的盲目鹦鹉学舌，而不是基于现实中的任何方式、形状或形式。现在这个行业中有很多这样的说法，这些人并不真正了解事情是如何运作的。](/css-selectors-combinators-are-slow-classes-are-fast-theyre-lying-to-you-827ff7d15203)****

****同样，对于我们的每一个兄弟元素，我们将需要——或者至少想要——一些生成的内容，所以让我们也默认这些内容。****

```
****input[type="checkbox"] + *:before,
input[type="radio"] + *:before {
  content:"";
  position:relative;
  display:block;
  transition:all 0.5s;
}****
```

****空的内容，所以这些将实际存在，相对位置，所以我们可以滑动它们进行调整或动画，阻止它服从任何我们想要的大小，以及相同的过渡速度作为他们的父母。****

****接下来，我们只需选择标准复选框和备选复选框。首先是两个:****

```
****input[type="checkbox"] + i,
input[type="checkbox"] + ins {
  border-radius:0.25em;
}****
```

****我们可能希望共享相同的边界半径，以便将它们嵌套在一起。****

****然后，对于我们的标准复选框:****

```
****input[type="checkbox"] + i:before {
  content:"\2714";
  top:-0.1em;
  left:0.1em;
  color:#0C0;
  transform-origin:50% 65%;
  transform:scale(0);
}input[type="checkbox"]:checked + i {
  background:#CFC;
}input[type="checkbox"]:checked + i:before {
  transform:scale(1.5);
}****
```

****一个 utf-8 的勾号，位置调整使它的中心更好的与原点调整，这样我们就可以在选中时放大它的大小。当缩放和使用 utf-8 字符时，很多时候你必须调整字距调整表中字形的大小和位置。这可能会导致 Linsux 或 OSuX 把事情搞砸，因为我是在 Winblows 上写的，这就是为什么在部署中我会建议使用类似 font-awesome 的东西来过滤掉这些不一致。现在，我们将使用 UTF-8 只是为了展示如何做到这一点。外观细节可以自己拨进去。****

****对于备选复选框样式，让我们在选中时将它标记为红色 X。****

```
****input[type="checkbox"] + ins:before {
  content:"\1F5D9";
  top:-0.1125em;
  left:-0.0625em;
  color:#F00;
  transform:scale(0);
}input[type="checkbox"]:checked + ins {
  background:#FCC;
}input[type="checkbox"]:checked + ins:before {
  transform:scale(0.8);
}****
```

****再次时髦的定位，以调整位置，但除此之外，只有着色和缩放时，该框被选中。****

****在这两种情况下，scale 都用于使复选标记或 X 动画显示在视图中。****

****对于我们的“开关”风格****

```
****input[type="checkbox"] + b {
  width:auto;
  height:auto;
  background:#FCC;
  padding-right:0.5em;
  border:0.125em solid #C00;
  border-radius:1em;
}input[type="checkbox"] + b:before {
  left:0;
  width:0.75em;
  height:0.75em;
  background:#C00;
  border:0.125em solid #FCC;
  border-radius:50%;
}input[type="checkbox"]:checked + b {
  background:#8F8;
  border-color:#0A0;
}input[type="checkbox"]:checked + b:before {
  left:0.5em;
  background:#0A0;
  border-color:#8F8;
}****
```

****我们关闭了宽度和高度，以便它可以缩放到它生成的内容子级。红色的外边框和粉红色的背景使它看起来很好，1EM 的边框半径使边缘变圆。正确的填充为我们的“开关”腾出了空间。我们必须手动说出边界半径，因为它不是正方形，所以 50%会给我们一个椭圆形。😢****

****生成的内容从左开始:0；这样动画才能工作。*(如果我们省略这一点，它将在类似 Chrome 中工作，但在 FF 中不工作。)*我们固定宽度和高度，给它一个小边框。请注意，这些颜色选择是相反的父母。****

****突出显示时，我们将颜色从红色调切换到绿色调，然后将内部内容向左滑动 0.5em。因为这是相对定位的，所以容器的大小仍然基于我们要移动的子元素，而不管我们将它移动到哪里。****

****关于风格开关的更多信息，请参见我之前的文章:****

****[](/scalable-styled-checkboxes-using-just-css-its-called-em-use-em-db21bae5f216) [## 仅使用 CSS 的可缩放的“样式化”复选框。它叫 EM，利用它们！

### 在回复 Omar Sharaki 的文章“从静态到动态 CSS 值”,关于从…

levelup.gitconnected.com](/scalable-styled-checkboxes-using-just-css-its-called-em-use-em-db21bae5f216) 

现在，对于单选按钮，让我们保持简单。我记得在 70 年代，当我还是个孩子的时候，我父亲有一台收音机，当你按下预设按钮时，它们会“眨眼”打开，因为它们内部的橡胶“护套”下面有一个球，当你按下按钮时，它会通过中间的狭缝向上推。让我们试着用`transform:scaleY`复制它。

```
input[type="radio"] + i {
 padding:0.125em;
 border-width:0.125em;
 border-radius:50%;
 background:#FFF;
}input[type="radio"] + i:before {
 width:100%;
 height:100%;
 border-radius:50%;
 background:#AAA;
 transform:scaleY(0);
}input[type="radio"]:checked + i:before {
 background:#000;
 transform:scaleY(1);
}
```

同样没有什么太复杂的。一些填充，使背景显示在我们的内部元素周围，增加这些的边框大小，同时保持我们在开始时选择的默认颜色，并使它成为一个具有边框半径的圆。

内部元素根据其父元素调整大小，圆化为圆形，设置为灰色，然后在 Y 轴上缩放为不可见。

当它应该被显示的时候，我们把它放大，使它“像眼睛一样睁开”并使背景变暗。我发现从灰色到黑色的过渡增强了“睁开眼睛”的外观。

# 现场演示

我把它放到我的标准演示板中，在一些 PRE/CODE 标签中交替出现，在输入之前显示每个输入部分的源代码。

 [## 样式化的复选框和单选按钮

### 这是一个用非常简单的标记设计单选按钮和复选框输入标签的演示，没有 JavaScript。CSS 完成所有繁重的工作…

cutcodedown.com](https://cutcodedown.com/for_others/medium_articles/styledChecksAndRadio/styledChecksAndRadio.html) 

同我所有的例子目录:
[https://cutcodedown . com/for _ others/medium _ articles/styledChecksAndRadio/](https://cutcodedown.com/for_others/medium_articles/styledChecksAndRadio/)

是完全开放的，除了整个事情的一个. rar 之外，还可以访问一些黏糊糊的片段。

对于那些喜欢用笔的人，给你:

# 利弊

我很难想到缺点。唯一真正的问题是，我将它应用于整个页面，因此任何缺少额外标签的输入都不会显示。说实话好吧。

最大的问题可能是某些操作系统上的某些浏览器不能正确显示 UTF 8 字符。正如我在解释中所说的，如果这对你来说真的是个问题，那就使用像字体一样的网络字体来显示图标。

优点有很多，它优雅地降级为非屏幕媒体 CSS 用户的默认行为，它是键盘可导航的，并且无论如何，形状或形式都不会违反可访问性规范。对于标记中的每个输入，只需要额外的 7 到 12 个字节*(取决于您为状态选择的标签)*，我们就可以在大约 2k 的 CSS 中应用多个漂亮的外观。这是我每天都要做的一笔交易。

# 结论

再一次，保持表示和内容的分离，利用选择器和组合器，等等，让 CSS 做我们所有的重活，把我们扛在肩上，带我们越过终点线。**脚本不是所有问题的终极答案**，也不是盲目地胡乱添加几十个 DIV、SPAN、内联图像和类。****