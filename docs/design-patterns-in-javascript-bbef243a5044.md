# JavaScript 中的设计模式

> 原文：<https://levelup.gitconnected.com/design-patterns-in-javascript-bbef243a5044>

![](img/25fce550f68ba623a90401a44f838ec6.png)

由[凯特·奥斯伯恩](https://unsplash.com/@kateausburn?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

设计模式有助于使代码更加整洁、易读，并且*不枯燥*。*干*是首字母缩略词，意思是“不要重复自己”。以下是 JavaScript 中最常用的设计模式。

## **工厂模式/功能**

工厂函数只是一个返回对象的函数，这种模式不需要像在构造函数中那样使用`new`关键字来初始化对象。这种模式是在 JavaScript 中使用函数式编程的好方法，因为它们只是简单的函数。像 jQuery 这样库使用工厂函数。

私有属性和方法可以在工厂中定义。只要将它们从返回的对象中排除，它们仍然可以被访问，因为形成了一个闭包。

工厂模式示例

**工厂组成**

这种模式用于将行为分配给对象，而不是继承许多通常不需要的行为。

**工厂构成示例**

**模块模式**

模块模式是一种创造性和结构化的设计模式，它提供了一种在生成公共 API 的同时封装私有成员的方法。

**原型图案**

原型模式侧重于创建一个对象，该对象可以通过原型继承用作其他对象的蓝图。由于 JS 中对原型继承的本机支持，这种模式在 JavaScript 中本来就很容易使用。

**单例模式**

Singleton 模式是一种设计模式，它将一个类的实例化限制为一个对象。创建第一个对象后，每当调用对象时，它将返回对同一对象的引用。

**抽象工厂模式**

抽象工厂模式是一种创造性的设计模式，可用于定义特定的实例或类，而不必指定正在创建的确切对象。

[](https://gitconnected.com/learn/javascript) [## 学习 JavaScript -最佳 JavaScript 教程(2018) | gitconnected

### 排名前 64 的 JavaScript 教程。课程由开发者提交并投票，使您能够找到最好的…

gitconnected.com](https://gitconnected.com/learn/javascript) [![](img/439094b9a664ef0239afbc4565c6ca49.png)](https://levelup.gitconnected.com/)