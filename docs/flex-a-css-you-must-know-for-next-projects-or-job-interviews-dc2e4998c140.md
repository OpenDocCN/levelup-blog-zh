# flex——下一个项目或工作面试中你必须知道的 CSS

> 原文：<https://levelup.gitconnected.com/flex-a-css-you-must-know-for-next-projects-or-job-interviews-dc2e4998c140>

本文的目的是让你直观快速地理解 flex 并在你的项目或面试中使用它！让我们开始吧…

# 第 1/2 部分。父容器的属性(flex 容器)

![](img/1a42c844eae35d9ad967533081b84593.png)

```
.container {   
  display: flex | inline-flex;flex-direction: row | row-reverse | column | column-reverse; flex-wrap: nowrap | wrap | wrap-reverse; justify-content: flex-start | flex-end | center | space-between | space-around | space-evenly | start | end | left | right; align-items: stretch | flex-start | flex-end | center | baseline | first baseline | last baseline | start | end | self-start | self-end; align-content: flex-start | flex-end | center | space-between | space-around | space-evenly | stretch | start | end | baseline | first baseline | last baseline; gap: 10px;
  gap: 10px 20px; /* row-gap column gap */
  row-gap: 10px;
  column-gap: 20px;
}
```

![](img/c2027af053f08ea54a396e1687a7db86.png)

弯曲方向

![](img/6129ddf573277a01270bcb562dff6a2d.png)

柔性包装

![](img/7f4319a54ab203c58e00ac8d80e38247.png)

调整内容

![](img/82a89523061160078c5e6d878f977b1c.png)

对齐-项目

![](img/75a9848204189e6f251d29044de8bebc.png)

对齐内容

![](img/ddda759c3cb6d843cd114d5cb5f23fbc.png)

间隙、行间隙、列间隙

# 第二部分。子对象的属性(弹性项目)

![](img/3aaa69765fdbe6a03091186e2c973d6d.png)

```
.item {   
  order: 5; */* default is 0 */* flex-grow: 4; */* default 0 */* align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```

![](img/ed7f8c5b5cc06444d6db509fafaa7eba.png)

命令

![](img/68ddde35fbee0785b9e2971fa27764df.png)

灵活增长

![](img/da7c05553675b323ee85c1417aa8aae6.png)

自我对齐

更多详细深入的 CSSing 请参考 [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS) ，真正力量的源泉！

**呼吁行动**

如果你觉得这个指南有帮助，请鼓掌并跟我来。通过[这个链接](https://medium.com/@caopengau/membership)加入 medium，你可以在 medium 上看到我和所有其他优秀作家的优质文章。