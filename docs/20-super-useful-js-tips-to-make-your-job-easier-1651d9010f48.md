# 20 个超级有用的 JS 技巧让你的工作变得更简单

> 原文：<https://levelup.gitconnected.com/20-super-useful-js-tips-to-make-your-job-easier-1651d9010f48>

![](img/91c7f520653b3a456526b8f3233fc02e.png)

收藏这 21 个超级有用的 JS 技巧，并在你的发展中使用它们，让你的工作更容易！

## 1.具有多个条件的 If 语句

将多个值放在一个数组中，然后调用该数组的 includes 方法。

## 2.如果为真，则简化…否则使用条件表达式

## 3.假值(未定义、null、0、False、NaN、空字符串)检查

当我们创建一个新的变量时，有时我们想检查引用的变量是否不是一个假值，比如 null 或 undefined 或者一个空字符串。JavaScript 为这种检查提供了一个很好的快捷方式——逻辑 or 操作符(||)

||将在左操作数为假的情况下返回右操作数只有当左侧为:*空*或*南*或*空*或*未定义*或*假*时，逻辑 or 运算符(||)将返回右侧的值。

## 4.空/未定义检查和默认赋值

## 5.获取列表中的最后一项

在其他语言中，这种功能被制作成可以在数组上调用的方法或函数，但是在 JavaScript 中，您必须自己做一些工作。

## 6.比较后返回

## 7.使用可选的链接操作符-？。

？也称为链判断运算符。它允许开发人员读取深度嵌套在对象链中的属性值，而不必验证每个引用。当引用为空时，表达式停止求值并返回*未定义的*。

## 8.多个条件的&&运算符

要仅在变量为真时调用函数，请使用&&运算符。

当您希望在 React 中有条件地呈现组件时，这对于用(&&)进行短路很有用。例如:

> { this . state . is loading &<loading>}</loading>

## 9.简化开关

我们可以在键值对象中存储条件，并根据条件调用它们。

## 10.默认参数值

## 11.简化的条件查找

如果我们想基于不同的类型调用不同的方法，可以使用多个 else if 语句或者开关，但是有没有比这更好的简化技巧呢？其实和之前的开关简化是一样的。

## 12.对象属性分配

## 13.解构分配

## 14.模板字符串

如果您厌倦了使用+将多个变量连接成一个字符串，这个简化技巧会让您省心。

## 15.跨越字符串

![](img/ae4448c4e3c724aa03ec2be69711ff58.png)

## 16.indexOf 的按位简化

当在数组中查找某个值时，我们可以使用 indexOf()方法。但是有一个更好的方法，让我们看看这个例子。

## 17.将字符串转换为数字

parseInt 和 parseFloat 等内置方法可用于将字符串转换为数字。我们也可以简单地通过在字符串前面提供一元操作符(+)来做到这一点。

## 18.按顺序履行承诺

如果您有一堆异步或正常函数，它们都返回要求您逐个执行的承诺，那该怎么办？

**等待所有承诺完成**

*Promise.allSettled()* 方法接受一组 Promise 实例作为参数，包装成一个新的 Promise 实例。在所有这些参数实例都返回结果(满足或拒绝)之前，包装实例不会结束。

有时候，我们并不关心异步请求的结果，我们只关心是否所有的请求都已经结束。这时， *Promise.allSettled()* 方法就很有用了。

## 19.交换数组元素的位置

## 20.带范围的随机数发生器

有时候你需要生成随机数，但是你希望数字在一定的范围内，那么你可以使用这个工具。

**生成随机颜色**

好了，就这些，上面整理的 20 个 JavaScript 小技巧希望对你的开发工作有实际的帮助，感谢阅读。

[](/javascript-try-catch-error-and-exception-handling-guide-352526468245) [## JavaScript Try-Catch 错误和异常处理指南

### 在任何软件开发项目中，处理错误和异常对于应用程序的成功至关重要…

levelup.gitconnected.com](/javascript-try-catch-error-and-exception-handling-guide-352526468245) [](/12-lines-of-javascript-to-make-you-look-like-a-pro-f11437df6965) [## 12 行 JavaScript 让你看起来像个专家

### Javascript 可以做很多神奇的事情，而且有很多东西要学，今天我简单扼要的介绍几个…

levelup.gitconnected.com](/12-lines-of-javascript-to-make-you-look-like-a-pro-f11437df6965) [](/summarize-the-common-loop-traversal-methods-in-javascript-how-many-have-you-used-49abb0ae918f) [## 总结一下 JavaScript 中常见的循环遍历方法——你用过几个？

### 数组和对象作为最基本的数据结构，在各种编程语言中起着至关重要的作用。这很难…

levelup.gitconnected.com](/summarize-the-common-loop-traversal-methods-in-javascript-how-many-have-you-used-49abb0ae918f)