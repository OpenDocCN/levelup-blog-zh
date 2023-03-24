# JavaScript 闭包如何让你恶心

> 原文：<https://levelup.gitconnected.com/javascript-closure-77275e2115a9>

## [网页开发](https://medium.com/@rakiabensassi/list/software-engineering-7a179a23ebfd)

## 如果您读过一篇关于 JavaScript 中闭包和作用域的文章，请阅读这篇文章

![](img/5fdb1cdacfe9a02c20e4123ddd7e0f26.png)

[来源 pexels.com](https://www.pexels.com/photo/a-person-in-black-hakama-dress-7792245/)(作者编辑)

我第一次听到“关闭”这个词是在十多年前。然而，对我来说，它仍然代表着一种黑洞。

2012 年，我参加了一次工作面试，面试中我被要求定义什么是“JavaScript 闭包”。我忍不住说，他们是一类函数。当然，我的回答不足以让我得到这份工作。

我后来读到了这个概念，但每次我都发现它很复杂和令人困惑，我想知道在 JavaScript 中存在这样一个特性背后的原因。

在过去的几个月里，一家出版物找到了我，让我写一份关于 JavaScript 教学的难题清单。我想也许是命运让我再次结束。所以这一次，我决定自己的战斗，并解决这个问题一劳永逸。我做到了。

在今天的文章中，我将分析 JavaScript 闭包是如何难以理解的，尤其是当它们与错误的作用域混合在一起时，我将澄清关于它们的混淆。但是在我们开始之前，让我们先来看看闭包是什么的简单定义:

> "闭包是一个即使在父函数关闭后仍能访问父作用域的函数."

# 问题是

您能猜出以下代码片段的输出吗:

JavaScript 闭包内的循环

该代码将打印:

```
item number: 4
item number: 4
item number: 4
item number: 4
```

为什么？

`item number: 4`是我每次调用`shoppingCardItems[j]`的输出，因为日志消息`console.log("item number: " + i)`中使用的变量`i`被绑定到匿名函数`for (var i=0; i<4; i++)`之外的同一个变量，当循环结束时，最后一个变量将是`4`。

我的例子中的匿名内部(或嵌套)函数在 JavaScript 中被称为**闭包**。

# 改变范围

如果我想要以下输出:

```
item number: 0
item number: 1
item number: 2
item number: 3
```

我需要修改代码来确定变量`i`的绑定范围，如下所示:

JavaScript:在闭包内正确使用循环

如你所见，我在第一个循环中使用了`let`而不是`var`。`let`和`var`的主要区别在于范围规则:

*   由`var`关键字声明的变量的作用域是直接函数体(函数作用域)。
*   由`let`关键字声明的变量的作用域是由`{}`表示的直接封闭块(块作用域)。

因为函数作用域令人困惑，并导致 JavaScript 中的错误，ECMAScript 6 在语言中引入了`let`语句。

# 现实生活中的用例

在循环中使用 JavaScript 闭包的另一个真实例子是:

JavaScript:向数组中的每一项添加事件监听器

每当用户单击产品时，上面的代码都会添加一条日志消息。如果您希望在应用程序中添加跟踪逻辑，或者如果您需要在项目上发出单击时触发其他事件，这将非常有用。

我希望这篇文章能帮助你减少对 JavaScript 闭包的困惑，并且更好地理解它们的用途以及如何正确地划分它们的作用域。

# 想要更多吗？

加入 **13K+学员**查看我的 Udemy 视频课程: [**内存泄漏 101:检测并修复 Web Apps 中内存泄漏的必备指南**](https://www.udemy.com/course/identify-and-fix-javascript-memory-leaks) 。

我为一群聪明、好奇的🧠人写关于工程、技术和领导力的文章💡。 [**加入我的免费电子邮件简讯独家访问**](https://rakiabensassi.substack.com/) 或注册媒体[在这里](https://rakiabensassi.medium.com/membership)如果你还没有这样做。

[](https://betterprogramming.pub/how-to-create-a-fancy-mat-snackbar-with-tailwind-css-a9fa0cf0b0c9) [## 如何用 CSS 创建一个漂亮的 mat-snackbar

### 在 Angular 项目中实际使用 tailwind CSS

better 编程. pub](https://betterprogramming.pub/how-to-create-a-fancy-mat-snackbar-with-tailwind-css-a9fa0cf0b0c9) [](https://betterprogramming.pub/microservices-cloud-architecture-9d15be866ce6) [## 如何验证微服务架构中的字段

### 探索现代软件开发的实践模式，如特性切换和金丝雀发布

better 编程. pub](https://betterprogramming.pub/microservices-cloud-architecture-9d15be866ce6)