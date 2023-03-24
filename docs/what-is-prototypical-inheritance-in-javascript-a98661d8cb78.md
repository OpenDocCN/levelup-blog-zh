# JavaScript 中的原型继承是什么

> 原文：<https://levelup.gitconnected.com/what-is-prototypical-inheritance-in-javascript-a98661d8cb78>

![A](img/e335c4dacc10fa5bf9454d1eacca89b2.png)  A   re 你准备好了解这门语言中最酷、最简单的概念之一了吗？原型继承是 JavaScript 的核心概念之一，也是它不同于 Java 等其他面向对象编程语言的地方。掌握它会让你的编码生活变得轻而易举。此外，每个 JavaScript 开发人员都应该知道这个概念，因为它也是 JavaScript 中其他[设计模式的构建块。相信我，在这篇文章的结尾，你会说‘原型继承？小菜一碟！](https://pandaquests.medium.com/overview-of-design-patterns-in-javascript-27d14530397a)

![](img/727b5c2f4487624e93b6fa6504b6f8a0.png)

Joel Moysuh 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

原型继承是 JavaScript 对象从其他对象继承属性和方法的一种方式。这是 JavaScript 中的一个基本概念，可以帮助您编写更高效和可重用的代码。

这里有一个例子来说明原型继承是如何工作的:

假设您有一个“Person”对象，它具有姓名、年龄和职业等属性。您可以创建一个新的“Student”对象，该对象继承了“Person”对象的所有属性和方法，但还具有特定于学生的附加属性和方法，如“enrollmentStatus”和“study()”。

代码可能是这样的:

```
// Define the Person object
let Person = {
 name: 'John',
 age: 30,
 occupation: 'teacher',
 greet: function() {
 console.log(`Hi, my name is ${this.name} and I'm a ${this.occupation}.`);
 }
};
// Define the Student object
let Student = Object.create(Person);
Student.enrollmentStatus = 'full-time';
Student.study = function() {
 console.log(`I'm currently a ${this.enrollmentStatus} student. Time to hit the books!`);
}
// Test the Student object
console.log(Student.name); // prints 'John'
console.log(Student.age); // prints 30
console.log(Student.occupation); // prints 'teacher'
console.log(Student.enrollmentStatus); // prints 'full-time'
Student.greet(); // prints 'Hi, my name is John and I'm a teacher.'
Student.study(); // prints 'I'm currently a full-time student. Time to hit the books!'
```

在这个例子中,“学生”对象继承了“人”对象的所有属性和方法，并且能够添加自己独特的属性和方法。

JavaScript 中的原型继承有几个优点，包括:

## 复用性

您可以使用继承创建具有通用属性和方法的“父”对象，然后创建从父对象继承的“子”对象。这允许您重用代码并避免重复功能。

## 代码维护

通过允许您对父对象进行更改，继承可以使您的代码更易于维护，这些更改将反映在所有子对象中。

## 代码组织

通过允许您将相关功能分组到单个对象中，然后根据需要使用继承来扩展该功能，继承可以帮助您组织代码。

## 表演

通过允许重用代码和避免重复功能，继承可以提高代码的性能。

## 多态性

继承允许您创建与其父对象具有相同接口的子对象，但是具有自己独特的实现。这对于创建灵活且能处理不同场景的代码非常有用。

但是，在 JavaScript 中使用原型继承有一些潜在的缺点，特别是在 ReactJS 这个最受欢迎的 JavaScript 库的时候:

## 复杂性

原型继承可能很难理解，特别是对于不熟悉 JavaScript 或 ReactJS 的开发人员。这使得有效地学习和使用继承变得更加困难。

## 混乱

在理解对象之间的关系以及属性和方法如何被继承时，继承有时会导致混乱。

## 表演

如果使用不当，继承可能会对性能产生负面影响。例如，如果您有大量嵌套在继承层次结构中的对象，那么访问属性和方法可能需要更长的时间。

## 排除故障

调试使用继承的代码可能更加困难，因为您必须跟踪父对象和子对象中的问题。

下面是几个在 JavaScript 中如何使用继承的例子:

*   创建一个基本对象:你可以使用继承来创建一个具有公共属性和方法的基本对象，这些属性和方法可以被多个子对象共享。这允许您重用代码并避免重复功能。
*   扩展功能:通过创建从父对象继承并添加附加属性和方法的子对象，可以使用继承来扩展现有对象的功能。
*   组织代码:通过允许您将相关功能分组到单个对象中，然后使用继承根据需要扩展该功能，继承可以帮助您组织代码。
*   实现多态性:继承允许您创建子对象，这些子对象具有与其父对象相同的接口，但是具有自己独特的实现。这对于创建灵活且能处理不同场景的代码非常有用。

总之，原型继承是 JavaScript 中一个有用的工具，可以在各种情况下使用，帮助您编写更高效、更有组织、更可重用的代码。虽然原型继承在 JavaScript 中是一个有用的工具，但是考虑这些潜在的缺点并明智地使用它来避免对代码的任何负面影响是很重要的。

![](img/5c7fdb823e2c7f4190f716ff6bed224c.png)

好了，你有它的人！JavaScript 原型继承速成班。我希望你喜欢这个熊猫批准的关于这个强大概念的入门书。

在你离开之前，别忘了留下评论，让我们知道你对这篇文章的看法。你觉得它有帮助吗？你对未来的博客主题有什么问题或建议吗？我们希望收到您的来信！

如果你喜欢这篇文章，一定要关注并订阅更多熊猫口味的内容。我们为未来计划了很多好东西，我们不想让你错过任何东西。所以点击下面按钮，继续关注更多！

直到下一次，继续编码吧，朋友们！

你真诚的