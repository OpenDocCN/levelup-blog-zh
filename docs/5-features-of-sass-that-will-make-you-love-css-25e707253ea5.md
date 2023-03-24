# Sass 的 5 个特性会让你爱上 CSS

> 原文：<https://levelup.gitconnected.com/5-features-of-sass-that-will-make-you-love-css-25e707253ea5>

![](img/9650d26a772638d46a18ad704cffc45d.png)

我喜欢做一名全栈工程师。我平等地欣赏后端和前端代码，并且出于完全不同的原因。后端应用程序的坚韧令我惊讶，它们操作、查询、操作并从大量数据中准确返回所需的内容。

前端在我心中有一个特殊的位置，因为它融入了一个漂亮的 UI/UX 设计的艺术性。给它们呼吸生命纯粹是一种乐趣。不过，我对美学方面的感觉并不总是那么热情洋溢。在我发现 JavaScript(更确切地说是 React)之前，HTML 让我有些抓狂，在我发现 Sass 之前，CSS 也是如此。

Sass，或语法上令人敬畏的样式表，谦逊地称自己为拥有超能力的 CSS 我同意这种说法。Sass 是一种扩展语言，用于设计比基本爱好应用程序更大的东西。它提供了作用域，这反过来又创建了一致性、简单的变量声明、流控制等等。

![](img/f1cffbaf64262abada26a037befe5018.png)

# 基础知识

Sass 是一种预处理器脚本语言，它被编译成经典的 CSS 脚本。以下内容来自官方文档，准确总结了 Sass 为何是现代前端工程中的必要工具:

> CSS 本身很有趣，但是样式表变得越来越大，越来越复杂，越来越难维护。这就是预处理器可以提供帮助的地方。Sass 允许您使用 CSS 中不存在的功能，如变量、嵌套、混合、继承和其他漂亮的好东西，使编写 CSS 再次变得有趣。

简单地说，Sass 提供了更简洁、更易于管理的样式工具，以及标准 CSS 尚不具备的急需功能。

Sass 文件应该使用扩展名。scss，实际上包含在其中的脚本看起来和 css 没有太大的不同。正如我上面所说的，您需要的是*扩展*语言，这意味着虽然它提供了额外的功能，但所有 CSS 脚本在 Sass 文件中都是有效的。设置背景颜色、字体样式和容器宽度对你来说并不陌生。

现在，对于 Sass 中最好的 5 个特性…

![](img/282cf0028e86ac6bf9b4f1731492d168.png)

来自 [Pexels](https://www.pexels.com/photo/colorful-poster-with-numbers-and-letters-on-white-surface-3964566/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的[现成](https://www.pexels.com/@readymade?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)照片

# 1.变量

普通 CSS 确实支持变量的使用，但是 Sass 极大地简化了它们。使用 Sass，你声明变量名前面加$然后赋一个值(不是*属性，*而是一个值，即#F5F2D0)。我倾向于使用在 a _variables.scss 文件中全局分配所有变量的做法，这使得在保持一致性的同时在应用程序中使用不同的颜色变得非常容易。

```
$offWhite: #F5F2D0;
$black: #1B1E23;
```

这是我的偏好，但是 Sass 也支持局部变量。在样式表开头声明的任何变量都是全局变量，而在一个块(花括号之间)中声明的任何变量都是该元素的局部变量。考虑到这一点，我在每个单独的样式表的顶部导入了全局变量表，以使相同的变量集始终可用。

Sass 还提供了一个！默认标志，允许用户自定义 Sass 库，同时仍然保持库的稳定性，以及一些高级功能，帮助确定变量是否存在。

*了解更多关于* [*萨斯变量*](https://sass-lang.com/documentation/variables) *。*

![](img/13523eeebbfbe57f1eccd0fd9bddffe7.png)

照片由 [Shopify 合作伙伴](https://burst.shopify.com/@shopifypartners?utm_campaign=photo_credit&utm_content=Free+Math+Cards+%26+Colored+Pencils+Image%3A+Browse+1000s+of+Pics&utm_medium=referral&utm_source=credit)从[爆](https://burst.shopify.com/school?utm_campaign=photo_credit&utm_content=Free+Math+Cards+%26+Colored+Pencils+Image%3A+Browse+1000s+of+Pics&utm_medium=referral&utm_source=credit)

# 2.经营者

Sass 支持您在编程中习惯使用的大多数标准运算符:

*   相等:==，！=
*   数学:+，-，*，/，%
*   比较:≤、、≥
*   布尔型:与、或、非
*   串联:+，-，/

我发现 Sass 的特别之处不在于它本身的特性，而在于它们的合力。例如，您可以看到操作符和变量如何使 CSS 中的一些数学变得更简单。与元素大小相关的属性，如高度、宽度、边距和填充，通常是相互依赖的，我们都曾为试图计算这个和那个以使页面看起来如此而头疼。您需要某些元素来对齐，或者一个元素拉伸另外两个元素的宽度。通过设置变量，然后使用这些变量而不是硬编码值，调整大小和布局成为一个更易于管理和动态的过程。

例如，在需要一个元素拉伸其他两个元素的宽度的情况下，可以使用变量设置两个元素的宽度，然后将这两个变量相加来设置被拉伸元素的宽度。

*了解更多关于* [*萨斯运算符*](https://sass-lang.com/documentation/operators) *。*

![](img/a5762482afb62e9bb4079c1cc661cae8.png)

照片由[高光 ID](https://unsplash.com/@highlightid?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/rules?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 3.At 规则

这是事情变得真正有趣的地方。由于 at-rules 的可用性，我上面所说的大部分内容都非常有用。at-rules*牛逼*。这里有太多的内容需要一一列举(完整的列表可以在[文档](https://sass-lang.com/documentation/at-rules)中找到),但是我们将涵盖一些最重要的内容。CSS 已经支持许多非常有用的 at-rules，比如@import 和@font-face，如果你还没有熟悉的话，我建议你先熟悉一下。Sass at-rules 在语法上的工作方式是一样的。

## @使用

使用规则可以用于将其他样式表作为模块导入，然后可以以模块化的方式访问其组件。模块的命名空间只是路径的最后一部分，但可以使用“as”作为别名。

这个变量:

```
*// src/_container.scss*$width: 500px;
```

可以在另一个文件中使用，如下所示:

```
*// style.scss* **@use** 'src/container';.container {
   width: container.width;
}
```

或者:

```
*// style.scss* **@use** 'src/container' as con;.container {
   width: con.width;
}
```

## @mixin 和@include

Sass 文档很好地解释了这些规则:

> Mixins 允许您定义可以在整个样式表中重用的样式。它们使得避免使用像`.float-left`这样的非语义类，以及在库中分发样式集合变得容易。

mixin 规则将定义样式，include 规则将触发 mixin 块的使用。Mixins 可以接受参数，使它们在重用时完全可定制。当您有多个具有相似但不完全相同属性的组件时，这个规则非常有用，并且相似性不是任意的(即，它们以某种方式相关——如果一个组件发生变化，另一个组件也需要发生变化)。

例如，假设您想要创建一系列相同但具有不同配色方案的容器。您可以像这样使用 mixins:

```
**@mixin** **change-color** ($bg, $c) {
  **width**: 200px;
  **height**: 100px;
  **font-family**: 'Raleway';

  **background-color:** $bg;
  **color:** $c;
}

**.blue-container** {
  **@include** **change-color**(#00008B**,** #ADD8E6);
}**.green-container** {
  **@include** **change-color**(#228B22**,** #90EE90);
}
```

## @功能 `and **@return**`

Sass 函数的外观和感觉将与您在代码中编写的任何其他基本函数一样。它们接受参数，可以封装迭代、循环、数学运算，最后返回值。

这些在计算尺寸、定义配色方案(反转、变亮或变暗现有颜色)或任何需要基本数学或变量使用之外的动态样式时非常有用。

## `@if and @else, @each, @for, @while`

这些 at 规则支持样式表中的流量控制。同样，它们看起来和感觉上与你正在写的其他 if/else、each、for 和 while 语句非常相似。

流控制规则通常在函数内部使用，以有条件地或迭代地返回值。变量可以表示对象或数组，然后可以对其进行迭代。

## @错误

错误 at-rule 通常与其他 at-rule 一起使用，比如条件或函数，当出现错误时，它会抛出一个错误。作为曾经因为我的普通 CSS 中的语法错误(不，我没有夸张)而花了 4 个多小时调试 Heroku 部署的人，我是这个 at-rule 的忠实粉丝。

![](img/23ace373e30e6a8d3e6941ce37cdfffd.png)

照片由[拉胡尔·潘迪特](https://burst.shopify.com/@rahulpandit98?utm_campaign=photo_credit&utm_content=Browse+Free+HD+Images+of+Pages+Of+A+Book+Curled+Into+A+Heart+Shape&utm_medium=referral&utm_source=credit)从[爆发](https://burst.shopify.com/books?utm_campaign=photo_credit&utm_content=Browse+Free+HD+Images+of+Pages+Of+A+Book+Curled+Into+A+Heart+Shape&utm_medium=referral&utm_source=credit)

# 4.插入文字

正如我前面所说的，Sass 的伟大之处在于其功能的合力。在插值的情况下肯定是这样。Sass 样式表的任何部分都支持插值，并且可以与函数和变量结合使用。您可以使用插值来动态引用属性、类名、值、元素等等。

例如，您可以创建一个函数来确定是否要对给定的类使用边距或填充，并按如下方式内插表达式的结果，以便动态设置属性。

```
.#{component-name} {
   #{margin-or-padding}: 10px;
}
```

![](img/30b05a82f90964e9262cf8055750ceb5.png)

[凯蒂·史密斯](https://unsplash.com/@kate5oh3?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/cooking-prep?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

# 5.预处理

这可能有点显而易见，但它确实是这里的标题。Sass 是一个预处理器，它将所有这些特殊的变量、函数、混合、样式模块和规则完美地编译成日常的 CSS。你做准备，他们做重活。

您可以在这里找到 Sass 的完整文档，我建议您花些时间浏览一下，以便全面了解它的功能。