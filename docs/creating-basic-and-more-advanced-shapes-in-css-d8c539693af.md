# 在 CSS 中创建基本和高级形状

> 原文：<https://levelup.gitconnected.com/creating-basic-and-more-advanced-shapes-in-css-d8c539693af>

## CSS 给了我们创造多种形状的机会。

![](img/87c5b3637641ede0c4ae41215c8e35d8.png)

在 CSS 中制作各种形状很容易。正方形和长方形是 web 开发中最常见、最自然的形状。你需要加上宽度和高度，就这样。然后加上边界半径，你就有了圆形和椭圆形。

更复杂的形状需要使用`:before`和`:after`伪元素或更多的 HTML。这给了我们另外两种形状来创造复杂的东西。在创造不同的形状方面，你最好的朋友是`transform`和`position`。

设计师下面的大多数形状会导出为图像，但正如我们所知，我们应该用 CSS 替换简单的图像来加速我们的网站。

请记住:所有的形状都是由身体上的`box-sizing: border-box;`构成的！

让我们从基本形状开始。

# 正方形

```
<div class="square"></div>.square {
  width: 80px;
  height: 80px;
  background: orange;
}
```

![](img/c5d8f7b80204f63d640c9b225689e17f.png)

平方

# 矩形形状

```
<div class="rectangle"></div>.rectangle {
  width: 200px;
  height: 80px;
  background: orange;
}
```

![](img/f8ce2e9625ace23a967af6a8a3e17712.png)

矩形

# 圆形

```
<div class="circle"></div>Elipse shape.circle {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  background: orange;
}
```

![](img/0490cf3459942dc998b4267190c926a3.png)

圆

# 椭圆形状

```
<div class="elipse"></div>.elipse {
  width: 160px;
  height: 80px;
  border-radius: 50%;
  background: orange;
}
```

![](img/00f06fcf7c6d364be813f8e78d2772f5.png)

埃利普塞

# 等腰三角形

```
<div class="isosceles_triangle"></div>.isosceles_triangle {
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 0 100px 100px 100px;
  border-color: transparent transparent orange transparent;
}
```

![](img/da1e7e0bbc59e91071557c9f57975aec.png)

等腰三角形

# 三角形向上形状

```
<div class="triangle_up"></div>.triangle_up {
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 0 50px 86.6px 50px;
  border-color: transparent transparent orange transparent;
}
```

![](img/d8cb7dee392d4a053efde8636424cb99.png)

三角形向上

# 三角形向下形状

```
<div class="triangle_down"></div>.triangle_down {
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 86.6px 50px 0 50px;
  border-color: orange transparent transparent transparent;
}
```

![](img/10e419570d91a8a4df227824779a6227.png)

三角形向下

# 三角形左侧形状

```
<div class="triangle_left"></div>.triangle_left {
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 50px 86.6px 50px 0;
  border-color: transparent orange transparent transparent;
}
```

![](img/d282df166092ee84afbd011fb5c8b693.png)

向左三角形

# 三角形直角形状

```
<div class="triangle_right"></div>.triangle_right {
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 50px 0 50px 86.6px;
  border-color: transparent transparent transparent orange;
}
```

![](img/19e1e9fecf6572ae95058398dca64612.png)

三角形右

# 三角形左上形状

```
<div class="triangle_top_left"></div>.triangle_top_left {
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 100px 100px 0 0;
  border-color: orange transparent transparent transparent;
}
```

![](img/2ed0c585b77c8998f4e3778b9953baa5.png)

左上三角形

# 三角形右上形状

```
<div class="triangle_top_right"></div>.triangle_top_right {
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 0 100px 100px 0;
  border-color: transparent orange transparent transparent;
}
```

![](img/ae7b0497b83b428538e24c3b6c4f49a6.png)

右上三角形

# 三角形右下角形状

```
<div class="triangle_bottom_right"></div>.triangle_bottom_left {
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 100px 0 0 100px;
  border-color: transparent transparent transparent orange;
}
```

![](img/cb56ce8cd12190308706438982c6b6bf.png)

右下三角形

# 三角形左下角形状

```
<div class="triangle_bottom_left"></div>.triangle_bottom_left {
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 100px 0 0 100px;
  border-color: transparent transparent transparent orange;
}
```

![](img/b7abb6381925ce6809be16433b712bc3.png)

左下三角形

# 梯形形状

```
<div class="trapezoid"></div>.trapezoid {
  border-bottom: 100px solid orange;
  border-left: 25px solid transparent;
  border-right: 25px solid transparent;
  height: 0;
  width: 100px;
}
```

![](img/e4e60cb676743871abf0140f626307aa.png)

梯形

# 平行四边形形状

```
<div class="parallelogram"></div>.parallelogram {
  width: 160px;
  height: 80px;
  transform: skew(20deg);
  background: orange;
}
```

![](img/8b54c96ee141108428182fdf664395f6.png)

平行四边形

# 星形— 5 分

```
<div class="star_5"></div>.star_5 {
  position: relative;
  width: 0px;
  height: 0px;
  display: block;
  border-right: 100px solid transparent;
  border-bottom: 70px solid orange;
  border-left: 100px solid transparent;
  transform: rotate(35deg);
}

.star_5:before {
  content: '';
  position: absolute;
  display: block;
  top: -45px;
  left: -65px;
  border-bottom: 80px solid orange;
  border-left: 30px solid transparent;
  border-right: 30px solid transparent;
  height: 0;
  width: 0;
  transform: rotate(-35deg);
}
.star_5:after {
  content: '';
  position: absolute;
  display: block;
  top: 3px;
  left: -105px;
  width: 0px;
  height: 0px;
  border-right: 100px solid transparent;
  border-bottom: 70px solid orange;
  border-left: 100px solid transparent;
  transform: rotate(-70deg);
}
```

![](img/f6cc1eddf74af3247aace0db43699fc2.png)

星级— 5 分

# 星形— 6 分

```
<div class="star_6"></div>.star_6 {
  width: 0;
  height: 0;
  border-left: 50px solid transparent;
  border-right: 50px solid transparent;
  border-bottom: 100px solid orange;
  position: relative;
}

.star_6:before {
  content: "";
  position: absolute;
  top: 30px;
  left: -50px;
  width: 0;
  height: 0;
  border-left: 50px solid transparent;
  border-right: 50px solid transparent;
  border-top: 100px solid orange;
}
```

![](img/8ad43068d37fede5763ffd27c010b1ad.png)

星级—6 分

# 星形— 8 分

```
<div class="star_8"></div>.star_8 {
  background: orange;
  width: 80px;
  height: 80px;
  position: relative;
  transform: rotate(20deg);
}

.star_8:before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  height: 80px;
  width: 80px;
  background: orange;
  transform: rotate(135deg);
}
```

![](img/7ea3a6746f096a6abdbda8e408e98c0d.png)

星级—8 分

# 星级 12 分

```
<div class="star_12"></div>.star_12 {
  background: orange;
  width: 80px;
  height: 80px;
  position: relative;
}

.star_12:before,
.star_12:after {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  height: 80px;
  width: 80px;
  background: orange;
}

.star_12:before {
  transform: rotate(60deg);
}
.star_12:after {
  transform: rotate(30deg);
}
```

![](img/d279e1c67da35b0b34c2e35becbf0f34.png)

星级—12 分

# 五边形

```
<div class="pentagon"></div>.pentagon {
  position: relative;
  width: 90px;
  border-width: 50px 18px 0;
  border-style: solid;
  border-color: orange transparent;
}

.pentagon:before {
  content: "";
  position: absolute;
  height: 0;
  width: 0;
  top: -85px;
  left: -18px;
  border-width: 0 45px 35px;
  border-style: solid;
  border-color: transparent transparent orange;
}
```

![](img/e7bb1b2157b8ffab70411de827949594.png)

五边形

# 六边形

```
<div class="hexagon"></div>.hexagon {
  width: 100px;
  height: 55px;
  background: orange;
  position: relative;
}

.hexagon:before {
  content: "";
  position: absolute;
  top: -25px;
  left: 0;
  width: 0;
  height: 0;
  border-left: 50px solid transparent;
  border-right: 50px solid transparent;
  border-bottom: 25px solid orange;
}
.hexagon:after {
  content: "";
  position: absolute;
  bottom: -25px; left: 0;
  width: 0;
  height: 0;
  border-left: 50px solid transparent;
  border-right: 50px solid transparent;
  border-top: 25px solid orange;
}
```

![](img/2131102dfcd0d50a3a267d18f1b20137.png)

六边形

# 八角形

```
<div class="octagon"></div>.octagon {
  width: 100px;
  height: 100px;
  background: orange;
  position: relative;
}

.octagon:before {
   content: "";
  position: absolute;
  top: 0;
  left: 0;
  border-bottom: 29px solid orange;
  border-left: 29px solid rgb(24,24,24);
  border-right: 29px solid rgb(24,24,24);
  width: 42px;
  height: 0;
}
.octagon:after {
  content: "";
  position: absolute;
  bottom: 0;
  left: 0;
  border-top: 29px solid orange;
  border-left: 29px solid rgb(24,24,24);
  border-right: 29px solid rgb(24,24,24);
  width: 42px;
  height: 0;
}
```

![](img/f5509261ec8c977da9a184bb5277685e.png)

八角形

# 菱形

```
<div class="diamond"></div>.diamond {
 width: 0;
  height: 0;
  border: 50px solid transparent;
  border-bottom: 70px solid orange;
  position: relative;
  top: -50px;
}

.diamond:before {
  content: '';
  position: absolute;
  left: -50px;
  top: 70px;
  width: 0;
  height: 0;
  border: 50px solid transparent;
  border-top: 70px solid orange;
}
```

![](img/8ceb95099a3912399efa7664cd94d9e6.png)

钻石

好的，上面所有的形状都非常简单和普通。让我们创造一些不常见但又简单的东西。

# 切割钻石形状

```
<div class="diamond"></div>.diamond {
  border-style: solid;
  border-color: transparent transparent orange transparent;
  border-width: 0 25px 25px 25px;
  height: 0;
  width: 50px;
  position: relative;
}

.diamond:before {
  content: "";
  position: absolute;
  top: 25px;
  left: -25px;
  width: 0;
  height: 0;
  border-style: solid;
  border-color: orange transparent transparent transparent;
  border-width: 70px 50px 0 50px;
}
```

![](img/674dae6cb6ad7120ef729caff6632555.png)

切割钻石

# 加号形状

```
<div class="plus"></div>.plus {
  width: 30px;
  height: 100px;
  background: orange;
  position: relative;
}

.plus:before {
  content: '';
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  left: 0;
  width: 100px;
  height: 30px;
  background: orange;
}
```

![](img/306a1f927fbd44e869291b1844d0ceb4.png)

加

# 三叶草形状

```
<div class="clover">
  <span class="t_left"></span>
  <span class="t_right"></span>
  <span class="b_left"></span>
  <span class="b_right"></span>
</div>.clover {
  position: relative;
}

.clover span {
   width: 90px;
  height: 90px;
  background: orange;
  position: absolute;
}
.t_left {
  border-radius: 50% 50% 0 50%;
  left: -90px;
  top: 0px;
}

.t_right {
  border-radius: 50% 50% 50% 0%;
  right: -90px;
  top: 0px;
}

.b_left {
  border-radius: 50% 0% 50% 50%;
  left: -90px;
  top: 90px;
}

.b_right {
  border-radius: 0% 50% 50% 50%;
  right: -90px;
  top: 90px;
}
```

![](img/0a4ba4e6be57a96b763c0e70207db70c.png)

三叶草

# 心形

```
<div class="heart"></div>.heart {
  background: orange;
  width: 100px;
  height: 100px;
  transform: rotate(-45deg);
  position: relative;
}
.heart:before,
.heart:after {
  content: '';
  position: absolute;
  background: orange;
  width: 100px;
  height: 100px;
  border-radius: 50%;

}
.heart:after {
  top: -50px;
  left: 0;
}
.heart:before {
  left: 50px;
  top: 0;
}
```

![](img/f26d66b5783dd89d9a19a9015d770beb.png)

心

# 新月形

```
<div class="crescent"></div>.crescent {
  position: relative;
  width: 100px;
  height: 100px;
  background-color: transparent;
  box-shadow: inset -12px 5px 0 3px orange;
  border-radius: 50%;
}
```

![](img/fa8e876d28aa07ae650e394133a02cfd.png)

新月

# 半圆形状

```
<div class="half_circle"></div>.half_circle {
  background: orange;
  height: 90px;
  width: 45px;
  border-bottom-right-radius: 90px;
  border-top-right-radius: 90px;
}
```

![](img/3c22d6f69645dc15d7f05899934d755e.png)

半圆

# 水滴形状

```
<div class="drop"></div>.drop {
  background: orange;
  width: 100px;
  height: 100px;
  border-radius: 50%;
  position: relative;
}

.drop:before {
  content: "";
  position: absolute;
  top: -10%;
  left: 50%;
  border: 42px solid transparent;
  border-bottom: 62px solid orange;
  transform: translateX(-50%);
}
```

![](img/5324a8b185c7cdf32af6c27adadd26cc.png)

滴

# 欢迎来到 CSS-图像世界！

我希望你能熟悉 CSS 形状。如你所见，CSS 形状既有趣又简单。为了测试你自己，试着不看 CSS 代码自己画出来。

在下一篇文章中，我们将讨论如何创造泡沫演讲😎。感谢阅读！

*最初发表于*[*https://www.albertwalicki.com*](https://www.albertwalicki.com/creating-shapes-in-css)*。*