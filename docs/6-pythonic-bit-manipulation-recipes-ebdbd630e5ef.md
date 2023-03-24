# 6 种 Pythonic 比特操作方法

> 原文：<https://levelup.gitconnected.com/6-pythonic-bit-manipulation-recipes-ebdbd630e5ef>

![](img/f9f89cae3db0c3881eae3665b56e59d3.png)

图片来源:[https://pxhere.com/en/photo/1458897](https://pxhere.com/en/photo/1458897)

一些可重用的 python 方法，用于操作位来执行快速运算。

## 1.使用位操作将整数加倍

使用`<<`运算符将位左移一位，从而使整数加倍。

## 2.使用位操作将整数减半

使用`>>`运算符将位向右移动一位，从而将整数减半。

## 3.使用位操作执行加法

这个比较复杂。伪代码算法是:

```
while a ≠ 0
    c ← b and a
    b ← b xor a
    left shift c by 1
    a ← c
return b
```

原则上，它使用一个循环，当第一个整数达到 0 时结束，所有的位都被加到第二个整数上。

这里我们使用了`& (AND)`和`^ (XOR)`操作符。

## 4.使用位操作执行乘法

这也很复杂，需要前面的加法。下面是伪代码算法:

```
c ← 0
while b ≠ 0
    if (b and 1) ≠ 0
        c ← c + a
    left shift a by 1
    right shift b by 1
return c
```

其再次循环直到达到零。

## 5.使用位操作执行减法

这是毛啊！首先，我们需要使用`~`运算符将被减数变为负数(找到它的补数)。然后我们找到被减数和减数的按位与。然后继续将减数减半，直到它变为 0。

很简单，对吧？

## 6.使用位操作执行除法

这也太纠结了！

就像乘法和加法的例子一样，除法就是反复的减法。这个实现返回一个元组，第一个元素包含商*和第二个元素包含余数*。**

*我希望你喜欢这个！*