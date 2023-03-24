# 纯 CSS 汉堡菜单

> 原文：<https://levelup.gitconnected.com/pure-css-hamburger-menu-c5eafc8d941d>

![](img/ba07581685e4f4533f3a2d095e4c7d8e.png)

# 介绍

你有没有遇到过这样的情况，你的网站需要一个汉堡菜单图标，但又不想导入整个库？或者你有没有想过一个汉堡图标是怎么建成的？如果你有，那你就来对地方了！在本教程中，我们将讲述如何使用纯 CSS 从头开始构建自己的汉堡菜单。不需要额外的 Javascript 或外部库。在本教程结束时，你至少能够用不同的动画制作三个汉堡菜单。

汉堡菜单动画

# HTML 结构

我们将对所有三个菜单使用下面的 HTML 结构。

```
<i class="hamburger --one"></i>
<i class="hamburger --two"></i>
<i class="hamburger --three"></i>
```

注意，我们每个菜单只需要一个*元素。事实上，你可以只用一个 HTML 元素来构建大多数图标。只有在极少数情况下，您会需要第二个 HTML 元素。这就是用 CSS 构建你自己的图标的好处之一。因为我们只有一个 HTML 元素，所以文件非常小，性能比使用 gif 文件或使用外部库好得多。我们选择*元素而不是**

**或元素，因为从语义上讲，使用*元素更有意义。它允许搜索引擎理解该元素只不过是一个图标，而和元素可以有更多的用途。***

# 设置三个酒吧

我们首先为整个*元素建立一个 200px 的基本宽度和 120px 的高度，这样我们的选择器将能够捕捉 onHover 事件，即使用户停留在三个条之间的空白区域。*

```
.hamburger {
  width: 200px;
  height: 120px;
  position: relative;
  margin: 0 20px;
}
```

对于 before 和 after 元素，我们赋予它们等于其父元素(*元素)的宽度和高度，并将背景色属性指定为 **currentColor** ，最后将颜色设置为#333。*

注意。我们将 background-color 设置为 currentColor，而不是直接指定我们想要的颜色，这样，如果以后我们希望更改两个元素的颜色，我们可以简单地更新 **color** 属性，这将同时影响两个元素。

```
.hamburger {
  &::before,
  &::after {
    content: '';
    position: absolute;
    width: 100%;
    height: 20px;
    color: #333;
    background-color: currentColor;
    transition: all .45s ease-in-out;
  }
  &::before {
    top: 0;
    transform: rotate(0);
  }
  &::after {
    bottom: 0;
    box-shadow: 0 -50px currentColor;
  }
}
```

# 动画制作第一份汉堡菜单

对于第一个汉堡菜单，我们将在悬停时执行以下操作:

1.  将第一个条(before 元素)移动到中心，并将其顺时针旋转 45 度
2.  通过将其颜色设置为透明来隐藏第二个栏(after 元素的方框阴影),并将第三个栏(after 元素)移动到中间。然后逆时针旋转第三根杆 45 度。

```
.hamburger.--one:hover {
  &::before {
    top: 50px;
    transform: rotate(45deg);
  }
  &::after {
    box-shadow: 0 0 transparent;
    bottom: 50px;
    transform: rotate(-45deg);
  }
}
```

汉堡菜单 1

# 动画制作第二份汉堡菜单

对于第二份汉堡菜单，我们想增加一点变化。在 before 和 after 元素的动画结束时，我们希望将 Y 轴上的整个*元素逆时针旋转 180 度。*

1.  将第一个条(before 元素)移动到中心，并将其顺时针旋转 45 度
2.  通过将其颜色设置为透明来隐藏第二个栏(after 元素的方框阴影),并将第三个栏(after 元素)移动到中间。然后逆时针旋转第三根杆 45 度。
3.  逆时针旋转 Y 轴上的整个*元件 180 度。*

```
.hamburger.--two {
  transform: rotateY(0);
  transition: all .45s .15s ease-in-out;
  &:before, &:after {
    transition: all .2s ease-in-out;
  }
  &:hover {
    transform: rotateY(180deg);
    &::before {
      top: 50px;
      transform: rotate(45deg);
    }
    &::after {
      box-shadow: 0 0 transparent;
      bottom: 50px;
      transform: rotate(-45deg);
    }
  }
}
```

汉堡菜单 2

# 动画制作第三份汉堡菜单

让我们为第三份汉堡菜单尝试一种完全不同的方法。我们将 before 元素的初始位置设置在中间，作为第二个条。将顶部 after 元素的初始位置设置为第一条，最后将 box-shadow 属性设置为底部的第三条。

当用户悬停在第三个汉堡菜单上时，我们将执行以下操作:

1.  通过将宽度收缩到其原始高度并将高度增加到其原始宽度，反转第二个条形(before 元素)的宽度和高度。
2.  将第一个条(after 元素)移动到中间，并隐藏第三个条(after 元素的 box-shadow 属性)。
3.  顺时针旋转整个*元件 45 度。*

```
.hamburger.--three {
  transition: transform .55s .3s ease-in-out;
  &:before,
  &:after {
    transition: all .3s ease-in-out;
  }
  &:before {
    width: unset;
    height: unset;
    left: 0;
    right: 0;
    top: 50px;
    bottom: 50px;
  }
  &:after {
    top: 0;
    bottom: unset;
    box-shadow: 0 100px currentColor;
  }
  &:hover {
    transform: rotate(45deg);
    &:before {
      left: calc(50% - 10px);
      right: calc(50% - 10px);
      top: -40px;
      bottom: -40px;
    }
    &:after {
      top: 50px;
      box-shadow: 0 0px transparent;
    }
  }
}
```

汉堡菜单 3

# 结论

你有它！常用汉堡菜单的三种不同方法和动画。通过调整悬停时 before/after 元素的变化，您可以随意尝试自己的动画。

完整的 Codepen 项目可以在这里找到:【https://codepen.io/chen1223/pen/NWpMrpb】T3

原帖:
[https://simpleweblearning.com/pure-css-hamburger-menu](https://simpleweblearning.com/pure-css-hamburger-menu)