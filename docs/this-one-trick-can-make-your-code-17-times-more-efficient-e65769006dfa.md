# 这一招可以让你的代码效率提高 17 倍

> 原文：<https://levelup.gitconnected.com/this-one-trick-can-make-your-code-17-times-more-efficient-e65769006dfa>

![](img/02a9cf61cde0d23ecf49d589ffd48846.png)

[去飞溅](https://unsplash.com/photos/FCHlYvR5gJI)

## 用最少的努力提高性能

每个人都希望自己的代码更高效——下面是一个简单技巧的实现及其原因，这个技巧只需最少的努力，却能显著提高性能。

想想 Mandelbrot 集，这是一个迷人的数学现象，涉及位运算、递归和虚数。因为这是一个如此复杂和计算多样化的函数，所以这是一个关于如何提高效率的很好的案例研究。深入的代码解释和产生的结果的漂亮图像可以在[这种刚性意味着它可以避免 Python 的动态类型所需的巨大内存空间开销。因为一个变量可以有很多可能的值，所以需要分配更多的内存。另一方面，对静态类型变量范围的严格限制意味着内存空间和执行过程要高效得多。函数参数也能够被声明为静态类型的 C 变量，如上所述。](https://medium.com/faun/visualize-the-beauty-of-recursion-by-programming-a-fractal-generator-714dbd94d6d1#中，静态类型变量被设置为只有一种数据类型。</p><p id=)

[这就是为什么简单地声明变量类型会导致处理时间加速的原因。尽管有许多其他方法可以让 Cython 的 Python 代码更高效，但最简单、最不需要技术、也许回报最高的方法是写出变量类型。](https://medium.com/faun/visualize-the-beauty-of-recursion-by-programming-a-fractal-generator-714dbd94d6d1#中，静态类型变量被设置为只有一种数据类型。</p><p id=)

[在你的剧本中有无数 Cython 的应用。](https://medium.com/faun/visualize-the-beauty-of-recursion-by-programming-a-fractal-generator-714dbd94d6d1#中，静态类型变量被设置为只有一种数据类型。</p><p id=)

*   [下一次，当您编写一个行方式的函数来应用于具有几千行的数据帧进行复杂的数据操作时，使用 Cython 可以大大加快遍历所有行所需的时间。](https://medium.com/faun/visualize-the-beauty-of-recursion-by-programming-a-fractal-generator-714dbd94d6d1#中，静态类型变量被设置为只有一种数据类型。</p><p id=)
*   [如果您正在手工编写一个数据转换管道来应用复杂和/或有条件的扩充，请考虑在 Cython 中实现它。](https://medium.com/faun/visualize-the-beauty-of-recursion-by-programming-a-fractal-generator-714dbd94d6d1#中，静态类型变量被设置为只有一种数据类型。</p><p id=)
*   [为神经网络编写自定义优化器或损失函数(或自行实现现有函数)时，使用 Cython 可以加快训练过程。](https://medium.com/faun/visualize-the-beauty-of-recursion-by-programming-a-fractal-generator-714dbd94d6d1#中，静态类型变量被设置为只有一种数据类型。</p><p id=)

[](https://medium.com/faun/visualize-the-beauty-of-recursion-by-programming-a-fractal-generator-714dbd94d6d1#中，静态类型变量被设置为只有一种数据类型。</p><p id=)[](https://towardsdatascience.com/5-quick-easy-hacks-to-write-more-computationally-efficient-code-b1168208b8df) [## 5 个快速简单的技巧来编写计算效率更高的代码

### 转变您低效的编码风格

towardsdatascience.com](https://towardsdatascience.com/5-quick-easy-hacks-to-write-more-computationally-efficient-code-b1168208b8df) [](https://towardsdatascience.com/jupyter-notebook-essential-productivity-hacks-9b7d69073769) [## Jupyter 笔记本基本生产力黑客

### 更高效地创建和编码

towardsdatascience.com](https://towardsdatascience.com/jupyter-notebook-essential-productivity-hacks-9b7d69073769)