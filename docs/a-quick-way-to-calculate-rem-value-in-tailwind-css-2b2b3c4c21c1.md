# 一种快速计算顺风 CSS 中 rem 值的方法

> 原文：<https://levelup.gitconnected.com/a-quick-way-to-calculate-rem-value-in-tailwind-css-2b2b3c4c21c1>

![](img/0df135b96a6c1128fcf62baf7eaab6a8.png)

由[布鲁诺·马丁斯](https://unsplash.com/@brunus?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

在 Tailwind 中，如果你想产生一个“1rem”的“padding ”,你可以使用下面的类名:“p-4”。我发现我需要一种快速的方法来计算在“4”处使用的值，当我试图产生更大数量的“填充”(或任何其他使用“rem”的属性)时。

顺风时，4 等于 1。因为 4 总是等于“1rem ”,你只需要用 4 乘以你想要的“rem”的数量。例如，如果你想在一个元素的两边添加一个“5rem”的“填充”，你可以使用“p-20 ”,因为“4 * 5 = 20”。

**公式:**

`4 * Amount of rem wnated = Value needed to use in place of '4' in the Tailwind class name`

```
<!-- padding: 1rem; -->
<header class="p-4"></header>

<!-- padding: 5rem; -->
<header class="p-20"></header>
```