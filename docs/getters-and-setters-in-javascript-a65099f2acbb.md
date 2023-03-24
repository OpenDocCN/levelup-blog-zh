# JavaScript 中的 Getters 和 Setters

> 原文：<https://levelup.gitconnected.com/getters-and-setters-in-javascript-a65099f2acbb>

![](img/cc958ac1f007cfdc942b2ef3b4fa0380.png)

JavaScript 中的 Getters 和 Setters 最早出现在 2009 年 ECMAScript 5 的发布中。我最初只是在 Jonas Schmedtmann 的《2020 年完整 JavaScript 课程》中短暂地遇到过它们。有一个简短的章节是关于 Getters 和 Setters 的，老实说，我并没有把这个主题放在太重要的位置上。然而，今年我参加了 Codecademy Pro 路径，后端软件工程师。本课程涵盖了全栈 JavaScript，并着重强调了 Getters 和 Setters 的使用。我很难解决重点上的差异，所以我回去查看文档以便更好地理解它。

W3Schools 的 JavaScript 教程将 Getters 和 Setters 称为“JavaScript 访问器”，允许您定义对象访问器。

MDN JavaScript 文档给出了更具描述性的定义。

**Getter** —将一个对象属性绑定到一个函数，当查找该属性时将调用该函数。

**Setter** —将对象属性绑定到试图设置该属性时要调用的函数。

它们为您提供了一种定义属性的方法，但是在访问该属性之前不会计算其值。这将计算价值的成本推迟到需要的时候。因此，它们允许动态计算值。(MDN)

```
Const person = { firstName: ‘John’, lastNamen: ‘Doe’, language: ‘en’, //Getter //
   get lang() { Return this.language; }, //Setter // set lang(lang){ this.language = lang; }}console.log(person.lang); // getter returns “en”person.lang = “esp”;  // sets language to “esp”
```

**为什么我们需要使用 Getters 和 Setters？**(根据 W3Schools)

*   使用它们时确保更好的数据质量
*   它为对象的属性和方法提供了更简单的语法。
*   对幕后做事有用。

**使用 Getter 修改属性**

```
const person = { home: “California “, get homeTown() { return ‘San Francisco, ${this.home}’;
   }}console.log(person.hometown) // San Francisco, California
```

**两种不同的视角**

乔纳斯·施梅德曼:“Getters 和 Setters 有时用起来很好，但不需要使用，许多程序员根本不使用它们。”

另一方面，Codecademy 在私有化对象属性的上下文中使用它。然而，他们的课程暗示这是必须的或经常使用的。

```
const person = { _firstName: 'John', //underscore used by convention to mean property should not be altered.// Means property not intended to be manipulated directly _lastName: 'Doe', _age: 37,// getter allows property to be manipulated upon retrieval get fullName() { if (this._firstName && this._lastName) return `${this._firstName} ${this._lastName}`; } else { return 'Missing a first name or a last name.'; } },// setter allows manipulation of property prior to storage. set age(newAge){ if (typeof newAge === 'number'){ this._age = newAge; } else { console.log('You must assign a number to age'); } }};// To call the getter method:person.fullName; // 'John Doe'//to use setterperson.age = 40;console.log(person._age); // Logs: 40person.age = '40'; // Logs: You must assign a number to age
```

**getter 和 Setters 也用于类**

```
class Dog { constructor(name) { this._name = name; this._behavior = 0; }, get name() { return this._name; }, get behavior() { return this._behavior; }, set name(newName){ this._name = newName; }, incrementBehavior() { this._behavior++; }}
```

我可以理解在这种情况下如何使用 Getters 和 Setters。但是它们是绝对必要的吗？在其他情况下，你会用到它们吗？或者它们只是代码中的另一个冗余？

我很想听听更有经验的 JavaScript 程序员对 Getters 和 Setters 有什么看法。