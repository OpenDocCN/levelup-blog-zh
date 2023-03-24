# 分两步创建带有固定标题的 HTML 表格

> 原文：<https://levelup.gitconnected.com/creating-an-html-table-with-fixed-headers-in-2-steps-adcac409e4d3>

## 开发一个带有固定标题的纯 HTML 表格很容易。

![](img/994af397df0c22da253474ef6966f1cb.png)

由 [Abel Y Costa](https://unsplash.com/@abelycosta?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们将使用`position: sticky` CSS 属性来允许表格的标题给人一种固定标题的错觉。

根据 [MDN web 文档](https://developer.mozilla.org/en-US/docs/Web/CSS/position):

> **粘性定位元素**是计算出的`*position*`值为`*sticky*`的元素。它被视为相对定位的，直到它的包含块在其流根(或它在其中滚动的容器)内越过指定的阈值(例如将`*top*`设置为除 auto 之外的值)，此时它被视为“被卡住”,直到遇到它的包含块的相对边缘。

# 要遵循的步骤

我们将使用 **React** 进行演示。

创建具有固定标题的 HTML 表格有两个主要步骤:

## **#1 创建一个 HTML 表格**

创建一个 HTML 表格并将其包装在父表格中`div.`

父级`div`将用于调整表格高度及其溢出属性。

索引. js

## **#2 定义 CSS 属性，将** `position: sticky`添加到表格的`th`中

`position: sticky`将有助于提供固定标题的假象。

样式. css

# 决赛成绩

当滚动**时，HTML 表格**的标题将**粘贴到**父 div** 的顶部。**

祝你 HTML 表愉快。