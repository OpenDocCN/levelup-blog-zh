# 自举 5 —垂直对齐和复位

> 原文：<https://levelup.gitconnected.com/bootstrap-5-vertical-alignment-and-resets-f211db812a77>

![](img/73396e56bd71ee5f5e017726315dc0fc.png)

凯瑟琳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

写这篇文章时，Bootstrap 5 处于 alpha 状态，可能会更改。

Bootstrap 是任何 JavaScript 应用程序的流行 UI 库。

在本文中，我们将了解如何通过 Bootstrap 5 的重置来垂直调整内容和最佳实践。

# 竖向定线

我们可以将内容与各种类垂直对齐。

为此，我们可以应用`.align-baseline`、`.align-top`、`.align-middle`、`.align-bottom`、`.align-text-bottom`和`.align-text-top`类中的一个。

然后我们可以写:

```
<span class="align-baseline">baseline</span>
<span class="align-top">top</span>
<span class="align-middle">middle</span>
<span class="align-bottom">bottom</span>
<span class="align-text-top">text-top</span>
<span class="align-text-bottom">text-bottom</span>
```

我们可以将项目与所有这些类对齐。

`align-baseline`将它们与中间的基线对齐。

`align-top`将内容顶端对齐。

`align-middle`将事物居中对齐。

`align-bottom`将内容底部对齐。

`align-text-top`将文本顶部对齐。

`align-text-bottom`将文本底部对齐。

这些类也可以在表格单元格中使用。

例如，我们可以写:

```
<table style="height: 100px;">
  <tbody>
    <tr>
      <td class="align-baseline">baseline</td>
      <td class="align-top">top</td>
      <td class="align-middle">middle</td>
      <td class="align-bottom">bottom</td>
      <td class="align-text-top">text-top</td>
      <td class="align-text-bottom">text-bottom</td>
    </tr>
  </tbody>
</table>
```

然后我们得到不同的结果。

`align-baseline`的工作原理与`align-top`相同。

`align-text-top`和`align-text-bottom`的工作方式相同。

# 能见度

Bootstrap 5 提供了在不修改元素显示的情况下控制内容可见性的类。

我们可以使用`visible`类使事物可见，使用`invisible`使事物不可见。

例如，我们可以写:

```
<div class="visible">...</div>
<div class="invisible">...</div>
```

那么第一个 div 是可见的，第二个是不可见的。

# 重新启动

Reboot 提供了一组基线 CSS，我们可以在其上构建我们的样式。

它建立在 Normalize 的基础上，Normalize 只使用元素选择器，为我们提供了许多风格固执己见的 HTML 元素。

额外的样式仅由类完成。

它改变了一些浏览器的默认设置，使用`rems`而不是`em`来调整组件间距。

`margin-top`避免了。垂直边距可能会折叠，从而产生意想不到的结果。

对于`margin`来说，单向更简单。

使用`rem` s 代替`margin` s，以便于缩放。

它还将与`font-`相关的属性声明保持在最低限度，并尽可能使用 inherit。

# 页面默认值

`box-sizing`在每个元素上全局设置。

这确保了元素的声明宽度不会因为填充或边框而超出，

没有为`html`元素声明基`font-size`。

但是，假设为 16px。

这可以用`$font-size-root`变量覆盖。

`body`亦见全局`font-family`、`font-wwight`、`line-height`和`color.`

`body`有一个`background-color`设置为`#fff`。

# 原生字体堆栈

Bootstrap 5 将字体设置为给定的样式。

它是用以下代码设置的:

```
$font-family-sans-serif:
  -apple-system,
  BlinkMacSystemFont,
  "Segoe UI",
  Roboto,
  "Helvetica Neue", Arial,
  "Noto Sans",
  sans-serif,
  "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji" !default;
```

我们有所有流行平台的字体，如 macOS 和 iOS、Windows、Android、Linux 和表情符号字体。

如果没有其他字体可用，默认字体为 Helvetica 新字体和 Arial 字体。

# 标题和段落

Bootstrap 5 为标题和段落提供了自己的样式。

它们移除了`margin-top`，并将标题的`margin-bottom`设置为`.5rem`，段落的`1rem`。

# 列表

所有列表的`margin-top`都被移除，并且`margin-bottom`被设置为 1。

嵌套列表没有`margin-bottom`。`padding-left`为`ul`和`ol`元件复位。

`dd`将`margin-left`设置为 0，将`margin-bottom`设置为`.5rem`。

s 是加粗的。

# 内嵌代码

如果我们用`code`标签包装 cod 片段，那么我们需要对 HTML 尖括号进行转义。

# 代码块

我们可以对多行代码使用`pre` s。

Bootstrap 移除`pre`元素的`margin-top`，使用`rem`元素的`margin-bottom`。

# 变量

如果我们使用 Bootstrap 5，那么变量应该写在`var`标签里面。

例如，我们可以写:

```
<var>y</var> = <var>m</var><var>x</var> + <var>b</var>
```

# 用户输入

`kbd`表示通常用键盘输入的输入。

比如，我们可以写；

```
<kbd><kbd>ctrl</kbd> + <kbd>,</kbd></kbd>
```

添加键盘按键的说明。

![](img/42aaaaa3b92caa7dc9fb8efa7dc4fc1d.png)

照片由[麦蒂·贝克](https://unsplash.com/@maddybakes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 结论

Bootstrap 为我们提供了一个可以构建的 CSS 基线。

内容可以与各种类保持一致。