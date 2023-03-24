# 教或学 Python 的简单方块技巧

> 原文：<https://levelup.gitconnected.com/simple-square-trick-for-teaching-or-learning-python-da4959af9af6>

## 通过一个简单的实际项目了解 python 的基础知识

由作者嵌入

学习编码的最好方法之一是用获得的知识做实际的项目。

在不涉及任何项目或思考的情况下，用简单的代码练习编码是不可取的。最终，目标是创建现实生活中的项目。

编程就是思考。即使在非常早期的阶段，思维也应该在简单的初学者友好的技巧或挑战的帮助下被灌输。

这些技巧将编程语言的不同方面结合在一起，创造出令人惊叹的东西。这有助于理解如何进行编程。以及如何独立思考。

无论是学习者还是教师，这个特殊的技巧涵盖了基本 python 的一些方面。这包括:

*   句法
*   变量
*   用线串
*   条件循环
*   导入模块
*   功能等。

挑战结束时，学习者将能够使用基本的 python 技能创建一个微型程序。

# 入门指南

在这个微型项目中，我们将使用海龟模块。这使得学习 python 成为一个有趣的过程，因为与标准 python 代码相比，它提供了更好的视觉输出。

下面是我们将遵循的非常简单的步骤。

*   正在导入模块。
*   定义海龟函数。
*   画线和我们的第一个正方形。
*   画四个正方形并有一个无限循环。

## 导入模块

这非常简单。

```
import turtle
```

## 定义海龟函数

在这里你可以使用任何名称。当学习者根据自己的名字定制名字时会更有趣，例如

```
levelup_turtle = turtle.Turtle()
```

## 画直线和第一个正方形

和乌龟一起站在第一线。

```
levelup_turtle.forward(100)
```

与乌龟的第一个方块

```
levelup_turtle.forward(100)
levelup_turtle.right(90)
levelup_turtle.forward(100)
levelup_turtle.right(90)
levelup_turtle.forward(100)
levelup_turtle.right(90)
levelup_turtle.forward(100)
```

## 在条件循环上画四个正方形

这是通过定义一个函数然后添加一个条件来实现的。

```
def square():
  levelup_turtle.forward(100)
  levelup_turtle.right(90)
  levelup_turtle.forward(100)
  levelup_turtle.right(90)
  levelup_turtle.forward(100)
  levelup_turtle.right(90)
  levelup_turtle.forward(100)playstation = 10
xbox = 8while xbox < playstation:
  square()
```

# 最终代码

微型项目的完整代码。

# 结果呢

由作者嵌入

仅此而已。快乐编码😊

![](img/a3262ef16f4a859e903cd41525b02411.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[窗口](https://unsplash.com/@windows?utm_source=medium&utm_medium=referral)拍摄