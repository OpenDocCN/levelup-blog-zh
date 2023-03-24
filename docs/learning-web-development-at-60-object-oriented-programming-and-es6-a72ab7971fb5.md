# 60 岁学习 Web 开发:面向对象编程和 ES6

> 原文：<https://levelup.gitconnected.com/learning-web-development-at-60-object-oriented-programming-and-es6-a72ab7971fb5>

![](img/db9d99462dd56e9fda22582599b68bd3.png)

[https://omycotton.com/?ref=pexels](https://omycotton.com/?ref=pexels)

当我用 Odin 项目完成了 [Web 开发 101 课程中的最后几个编码项目时，我对我学习 JavaScript 的进展感觉非常好。我可以浏览基本的 JavaScript 循环、数组、函数，甚至对对象有了基本的了解。我可以使用文档对象模型(DOM)很好地操作 HTML 和 CSS 元素。虽然我远不是专家，但我开始看到我努力的一些成果，我觉得我已经准备好学习高级 JavaScript 了。](https://www.theodinproject.com/courses/web-development-101)

**高级 JavaScript —考验与磨难**

我开始学习 Jonas Schmedtmann 的[完整 JavaScript 课程](https://www.udemy.com/course/the-complete-javascript-course/)的高级 JavaScript 部分。课程包括面向对象编程(OOP)和 ES6 或 ES2015 JavaScript。我在前面的课程中已经学习了什么是对象，但是我不知道如何在编程中使用对象。ES6 是 JavaScript 的新版本，引入了新的语法。

**高级 JavaScript 课程**

*   对象继承和原型链
*   函数构造函数
*   对象.创建
*   原语与对象
*   一级函数
*   将函数作为参数传递
*   函数返回函数
*   立即调用函数表达式(IIFE)
*   关闭
*   绑定、调用和应用

在第一次通过面向对象的 JavaScript 之后，我的进步完全停止了。我仍然不知道如何使用对象。本节末尾的编码挑战看起来是不可能的。即使在第二次复习这些课程后，我也没有很好地掌握这些概念。到目前为止，我很喜欢 Jonas 的课程，但是，我需要查看其他资源来学习。

**面向对象编程**

面向对象的编程似乎与我刚刚学习的基础 JavaScript 非常不同。对象是各种项目的集合；变量，数组，布尔，函数。说“一切都是对象”是老生常谈。然而，这似乎是真的。如何在编程中使用对象；创建和使用对象来完成任务？我先看了一下 [W3 Schools 网站](https://www.w3schools.com/js/default.asp)上的教程。我发现这些非常适合初学者，因为他们用简单的英语解释事情。我开始学习如何制作一个物体。

**声明一个对象**

使用对象文本是声明对象的最简单方式。到目前为止，一切顺利。

```
var person = {name : “name”,yearOfBirth: “year”};
```

我在 Udemy 上找到了托尼·艾丽西娅写的 [JavaScript:理解奇怪的部分](https://www.udemy.com/course/understand-javascript/)。几个学习 JavaScript 的网站极力推荐这门课。浏览了一下，我发现它正好涵盖了我正在努力解决的问题。

托尼·艾丽西娅(Tony Alicia)的课程是比较老的课程，最后一次更新是在 2016 年，而且已经不支持导师了，太糟糕了。我发现托尼可能是我所知道的最好的编程老师。他从容不迫的教学方式显得冷静而清晰。他重点解释了 JavaScript 如何“在幕后”工作。详细解释了继承、范围链和原型。我花了一个月的时间来学习这门课程，对这些概念有了更好的理解。

随后，我在 YouTube 上看了一小段 Brad Traversy 的视频，[面向对象编程速成班](https://www.youtube.com/watch?v=vDJpGenyHaA&list=PLillGF-RfqbbnEGy3ROiLWk7JMCuSyQtX&index=11&t=0s)。特拉弗斯有一种严肃务实的教学风格。他展示了更多关于面向对象 JavaScript 的最新信息，包括 ES6。公平地说， [Jonas Schmedtmann](https://www.udemy.com/course/the-complete-javascript-course/) 在他的课程中也非常完整地介绍了 ES6。现在继续讨论构造函数和继承的概念。

**函数构造器和继承**

函数构造器是一个制造新对象的函数。这些新对象继承了构造函数的属性。

一个小问题是函数名中第一个字母的大写。在从一开始学习在声明变量时使用小写字母之后，突然不得不记住这样做就变得违背直觉了。

下面是一个基本的函数构造器:

```
var Person = function(name, yearOfBirth){this.name = name;this.yearOfBirth = yearOfBirth;}
```

对象继承:用从构造函数对象继承的属性创建一个新对象。

```
Var joe = new Person (‘joe’, 1990);
```

名为“joe”的新对象从 Person 方法继承了姓名和出生年份属性。

这是多余的术语；方法和函数本质上是一样的，都被认为是对象。

好吧，我明白了。但后来我发现有不止一种方法可以做到这一点。事实证明，在 JavaScript 中有几种方法可以让对象存在。

**Create.object 方法:**什么……这做同样的事情。

```
var joe = object.create(personProto),{Name: {value: ‘joe’},yearOfBirth: {value: 1990}};
```

回到函数构造函数，方法也可以添加到对象中。

```
var Person = function(name, yearOfBirth){this.name = name;this.yearOfBirth = yearOfBirth;this.calculateAge() = function() {Var age = Date.getFullYear() - this.yearOfBirth;}}
```

但是有另一种方法可以添加方法。原型方法用于产生具有继承属性的函数(方法)。这将函数排除在构造函数之外，但允许它继承其属性。

**原型法**

```
Person.prototype.calculateAge = function(){var age = Date.getFullYear() - this.yearOfBirth;}
```

当我开始学习 ES6 时，我发现了另一种制作对象的方法，使用类。

**ES6 类**

```
class Person {constructor( name, yearOfBirth) {this.name= name;this.yearOfBirth= yearOfBirth;}}let joe =  new Person(“joe”, 1990);
```

哇，算上对象字面量，就可以用四种方式做本质上相同的事情。我的第一个想法是，“为什么我必须学习所有这些不同的语法？”它们只是略有不同，但对于一个初学编码的人来说，这是令人恼火的。事实证明，这并不重要。真正重要的是理解继承和原型。

**语法糖** **和面向对象的 JavaScript**

Traversy 和 Alicia 都提到了术语“句法糖”,将其定义为做同样事情的不同句法。两者都暗示了使用其中一个并不会带来编程优势。两位老师都强调学习原型和继承的概念，并建议在使用 JavaScript ES6 类之前学习。

这当然是我的情况。一旦我对原型和继承有了更好的理解，我可以看到所有制造一个对象的方法只是做同一件事的不同方法，这样就不会那么混乱了。概念上的理解起了作用。

那么如何在应用程序中使用面向对象编程呢？

我刚刚开始理解应用程序如何使用对象。我只使用面向对象的 JavaScript 编写过两个程序，但是我发现它主要避免了重复劳动，允许您对属性进行分组，并高效地存储和检索数据。这将使处理大量对象或数据变得更加容易。构造函数使得创建许多具有相似属性的对象变得很容易。制作物品的多种方式会让初学者感到困惑。我想随着我在编程和大型项目上获得更多的经验，我会更容易使用它们。

如果你对学习面向对象 JavaScript 有任何建议或见解，请在下面添加评论。

查看我的学习笔记，我在 30 天内完成了 Wes Bos 的 30 个项目。

[](https://jeffrey-amen.medium.com/javascript-30-01-drum-kit-2987437d32c5) [## JavaScript 30–01 架子鼓

### 2020 年 11 月 1 日——我报名参加了韦斯·博斯的免费课程 JavaScript 30，为期 30 天的普通 JavaScript 编码挑战…

jeffrey-amen.medium.com](https://jeffrey-amen.medium.com/javascript-30-01-drum-kit-2987437d32c5) [](https://jeffrey-amen.medium.com/javascript30-day-2-study-notes-3ad65d210e3f) [## JavaScript30 —第 2 天学习笔记

### Clock 项目涉及处理 CSS 转换和使用 JavaScript Date 对象。

jeffrey-amen.medium.com](https://jeffrey-amen.medium.com/javascript30-day-2-study-notes-3ad65d210e3f)