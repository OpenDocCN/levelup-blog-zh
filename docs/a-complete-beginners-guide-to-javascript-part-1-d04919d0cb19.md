# Javascript 初学者完全指南—第 1 部分

> 原文：<https://levelup.gitconnected.com/a-complete-beginners-guide-to-javascript-part-1-d04919d0cb19>

![](img/f2c1132f120d6dd330bd27a340b04e19.png)

这是两个故事中的第一个，在这里你将学到开始使用 Javascript 编程所需要知道的一切。

> 要了解用于 DOM 和 web 的 Javascript，请查看[第 2 部分](/a-complete-beginners-guide-to-javascript-part-2-501ec89af76c?source=friends_link&sk=33ec19c8db7d53646246e9104b873281)

那么这个故事包括什么呢？我将它分成了以下几个部分，涵盖了 Javascript 的基础知识:

*   介绍
*   变量
*   分配
*   数据类型
*   功能
*   用线串
*   数字
*   数组
*   目标
*   日期
*   算术和数学
*   条件和比较
*   环
*   错误
*   班级
*   异步编程

我建议使用类似于 [PlayCode.io](http://playcode.io) 的工具来跟踪这个故事，这样你就可以真正了解我们经历的每一件事。

# 介绍

我们先来说说 Javascript 是什么，为什么它如此重要。Javascript 创建于 1995 年，是一种为万维网设计的编程语言，现在被绝大多数网站使用。它使我们能够创建交互式 web 应用程序，如果操作得当，可以给最终用户带来更加高效、用户友好和愉快的体验。

多年来，Javascript 已经成为一种非常强大的语言，并且有了许多可用的框架，您可以做几乎任何您能想到的事情。现在，你甚至可以使用 NodeJS 将 Javascript 用于后端，使用 ReactNative 等框架构建 iOS 和 Android 应用，使用 Electron 等框架构建跨平台桌面应用。

现在我们已经讨论了 Javascript 是什么以及它为什么有用，让我们来看看一些基本的语法。

我们将从注释开始，这些注释非常有用，不仅有助于组织你的代码，也有助于你和其他开发者理解正在发生的事情。

我们可以使用两个正斜杠来编写单行注释:

```
// This is a single line comment
```

我们也可以写多行注释:

```
/*
This is a
multi-line
comment
*/
```

接下来，我们有变量，这些用来保存数据。变量可以使用三个关键字声明，var、let 和 const:

```
var variableName;
let variableName;
const variableName;
```

`var`、`let`和`const`都是关键字，这意味着它们用来定义要执行什么动作。通过`var`、`let`和`const`，我们告诉浏览器创建变量。

变量名不能以数字开头，通常你会看到它们以小写字母或下划线开头。在 Javascript 中，我们对几乎所有东西都使用所谓的 Camel Case，包括变量名。这是第一个单词以小写字母开始，然后后面的每个单词都以大写字母开始。

这意味着代替:

```
variable_name
variable-name
VariableName
```

我们使用:

```
variableName
```

Javascript 也是区分大小写的，所以`variableName`和`VariableName`不一样。

接下来，我们有文字，它们只是简单的固定值。例如:

```
20
"Hello World"
```

现在让我们快速看一下操作符，这些符号允许你做赋值，算术和比较。例如，如果我们想给一个变量赋值 20，那么我们可以使用等号运算符。

```
let variable = 20;
```

或者我们想用 20 乘以 5，为此，我们会使用乘法运算符，它是一个星号。

```
20 * 5
```

用我们的乘法，我们刚刚写的叫做表达式。表达式是文字、变量、函数和运算符的混合体，当它们组合在一起时用于计算单个值。用 20 乘以 5，我们用乘法运算符将文字 20 和 5 结合起来计算值 100。

这是 Javascript 的基本语法，现在让我们更详细地看看变量。

# 变量

正如我前面提到的，在 Javascript 中我们有三种声明变量的方法，`var`，`let`和`const`。那么它们之间的区别是什么呢？

`var`和`let`都是可变的，这基本上意味着我们可以改变它们的值。`const`另一方面，是不可变的，认为它是只读的，所以值不能被改变。例如:

```
// This will work
var animal = "dog";
animal = "cat";// This will work
let animal = "dog";
animal = "cat";// This will not work
const animal = "dog";
animal = "cat";
```

那么如果`var`和`let`都是易变的，那么两者有什么区别呢？嗯，他们基本上有不同的范围。这意味着访问它们的能力根据您在脚本中的位置而不同，`var`是函数范围的，`let`和`const`都是块范围的。这意味着`var`可以在整个函数中被访问，而`let`和`const`只能在声明它们的块中使用。块是大括号内的任何东西。

让我们来看一个例子:

```
// Var
for (let i=0; i<1; i++) {

  var animal = "dog";    
  let country = "England";  
  const planet = "Earth";}// This will log "dog"
console.log(animal);// These will both throw errors
console.log(country);
console.log(planet);
```

当试图记录`country`和`planet`变量时，您会看到一个错误，这是因为我们试图从声明它们的块之外访问它们。

你现在可能想知道为什么会这样，看着代码你可能会认为`var`也只能在块内使用，因为那是它被声明的地方。它的工作原理是一种叫做提升的东西。Javascript 所做的是，在创建阶段，它在函数的开始用值`undefined`初始化`var`。当那行代码运行时，您想要的值就会被赋值。因为我们仍然在同一个函数中，所以我们可以从块的外部访问`animal`，因为它实际上已经在块之前被定义了。一开始这可能有点混乱，但是你写的 Javascript 越多，你就会越明白。

在 Javascript 中，我们不需要在声明变量时赋值，例如，我们可以这样声明`animal`:

```
let animal;
```

现在即使我们没有给它赋值，它仍然有一个值，这个值就是`undefined`。我们甚至可以一次声明多个变量:

```
let animal, country, planet;
```

现在我们对变量有了更多的了解，让我们继续学习赋值。

# 分配

赋值基本上就是它所说的，它是给一个变量赋值的过程。我们已经看过了给变量赋值:

```
let animal = "dog";
```

我们也可以将变量赋给其他变量:

```
let animalOne = "dog";
let animalTwo = animalOne;
```

这里`animalOne`和`animalTwo`都等于`"dog"`。

在 Javascript 中，我们有一些快速更新变量值的方法。例如，假设我们有一个值为`"Hello"`的变量，我们想给这个变量加上`"World"`。要做到这一点，您只需:

```
let myVar = "Hello";
myVar += " World";
```

我们在这里所做的被称为串联，这是两个或更多的值连接在一起成为一个值。在我们的例子中`"Hello"`和`" World"`变成了`"Hello World"`。

如果我们尝试连接两个数字会发生什么？嗯，它们是加在一起的而不是连在一起的。例如:

```
let myVar = 1;
myVar += 1;
```

这里的`myVar`现在等于`2`，而不是`11`。在处理数字时，我们也可以像刚才做加法一样，快速地进行加减运算。

```
let myVar = 1;// Increment
myVar++; // myVar now equals 2// Multiple
myVar *= 5; // myVar now equals 10// Divide
myVar /= 2; // myVar now equals 5// Subtract
myVar -= 2; // myVar now equals 3// Decrement
myVar--; // myVar now equals 2
```

这就是你需要知道的关于赋值的全部内容，现在让我们来看看数据类型。

# 数据类型

我们现在已经很好地理解了变量和给这些变量赋值，所以让我们更深入地了解变量可以包含的数据类型。

我们已经知道了字符串和数字。关于字符串，有一点需要注意，它们可以用单引号或双引号括起来，例如:

```
'my string'
"my string"
```

下一个最简单的数据类型是布尔型，基本上不是真就是假。

```
let loading = false;
```

对于布尔变量，我们可以将相反的值赋给变量，例如:

```
let loading = false;
loading = !loading; // Loading now equals true
```

我们也可以根据条件分配布尔值，例如:

```
let first = 4;
let second = 7;
let isEqual = (first === second);
```

这里`isEqual`等于`false`，因为我们的`first`变量不等于我们的`second`变量。

现在让我们来看看数组。数组允许我们在一个变量中存储多个值，并且是用方括号定义的。

```
let colours = ["red", "green", "blue"];
```

我们不必在声明数组时填充它，我们可以声明一个空数组，稍后再填充它。例如:

```
let colours = [];colours.push("red");
colours.push("green");
colours.push("blue");
```

这里我们仍然以数组结束，就像我们创建的第一个数组一样。为了访问变量保存的值，我们使用数组的索引。数组是从零开始的，这意味着索引从 0 开始。例如:

```
console.log(colours[0]); // This logs red
console.log(colours[1]); // This logs green
console.log(colours[2]); // This logs blue
```

我们的索引不一定是数字，也可以是字符串，这叫做关联数组。例如:

```
let user = [];
user["firstName"] = "Bob";
user["lastName"] = "Smith";console.log(user["firstName"]); // This logs Bob
console.log(user["lastName"]); // This logs Smith
```

到目前为止，我们创建的所有数组都是一维的，这意味着只有一个值列表。数组也可以是多维的，换句话说，是列表中的列表。

```
let sequences = [];let sequence1 = [1,2,3,4,5,6];
let sequence2 = [1,1,2,3,5,8];sequences.push(sequence1);
sequences.push(sequence2);
```

我们现在已经创建了一个二维数组，其中有一个包含不同序列的列表。我们访问值的方式和以前一样，唯一的不同是现在我们有两个索引，而不是一个。例如，假设我们想得到第一个序列中的第二个数字。

```
console.log(sequences[0][1]); // This logs 2
```

对于多维阵列，需要注意的一点是，添加的维度越多，阵列所需的总存储空间就会急剧增加。正因为如此，你应该明智地使用它们。

让我们暂时回到关联数组。尽管可以创建这种类型的数组，但您很少会在 Javascript 中看到它们。这是因为有一种更好的方法，那就是使用对象。

对象是 Javascript 的支柱，几乎 Javascript 中的所有东西都是对象的核心。对象是一组键值对，让我们创建一个用户对象。

```
let user = {
  firstName: "Bob",
  lastName: "Smith"
};
```

这里我们有两个键，`firstName`和`lastName`，以及两个值，`"Bob"`和`"Smith"`。这些值可以是任何数据类型，甚至可以是其他对象，例如:

```
let user = {
  name: {
    first: "Bob",
    last: "Smith",
  },
  age: 30,
  isActive: true,
  favouriteColours: [
    "red",
    "green",
    "blue"
  ]
};
```

我们访问对象就像访问数组一样，除了我们使用键而不是索引。

```
console.log(user.age); // This logs 30
console.log(user.name.first); // This logs Bob
console.log(user.favouriteColours[0]) // This logs red
```

就这么简单，你很可能会发现自己在编写 Javascript 时会大量使用对象。对象的一个便利特性是能够将函数作为值。例如，我们可以编写一个函数来返回用户的名字和姓氏。

```
let user = {
   firstName: "Bob",
   lastName: "Smith",
   fullName: function() {
      return `${this.firstName} ${this.lastName}`;
   }
};
```

现在，如果我们记录`user.fullName()`，我们将得到`"Bob Smith"`。在这里你会看到我们使用了一些我们还没有涉及到的东西，这些函数我们将在后面的故事中介绍。另外两件事是`this`关键字和奇怪的`${}`语法。首先，`this`是一个用于从自身访问值的关键字，所以这里它是从我们的`user`对象访问值。奇怪的语法叫做模板文字，这基本上允许我们在字符串中使用变量。模板文字是我最喜欢的 Javascript 特性之一，我们曾经不得不使用`+`操作符来实现模板文字的功能。例如:

```
let first = "Hello";
let second = "World";
let third = "!";// Old Way
let oldString = first + " " + second + third;// New Way
let newString = `${first} ${second}${third}`;
```

`oldString`和`newString`都拥有完全相同的值，但`newString`是以一种更简单、更易读的方式创建的。模板文字的主要好处之一是能够创建多行字符串。

我们要看的最后一个数据类型是`typeof()`函数，它允许你找出值是什么数据类型，并且非常有用。例如:

```
console.log(typeof("Hello")); // This logs string
console.log(typeof(1)); // This logs number
console.log(typeof(true)); // This logs boolean
console.log(typeof(function() {})); // This logs function
```

现在我们已经很好地掌握了数据类型，让我们来看看函数。

# 功能

函数是专门为执行单一任务而编写的代码块。它们可以很好地分割你的代码，使其更容易阅读，它们还允许代码的可重用性，而不需要在每次你需要执行特定任务时进行复制和粘贴。我们来看看怎么写一个。

```
function sayHello() {
  console.log("Hello World");
}
```

在 Javascript 中，我们使用`function`关键字来声明一个函数，后跟函数名，函数名后面有括号。要调用一个函数，只需键入函数名，后跟括号。

```
sayHello(); // This will log "Hello World"
```

如果我们愿意，我们可以把函数赋给一个变量。

```
let sayHello = function() {
  console.log("Hello World");
}
```

如果我们将函数赋给一个变量，那么我们不需要`function`关键字，相反我们可以使用箭头函数。

```
let sayHello = () => {
  console.log("Hello World");
}
```

所有这些函数都以相同的方式执行，并以相同的方式调用。有时，我们可能希望函数在不调用它的情况下执行，为此，我们使用一个可立即调用的函数表达式，简称 IIFE。这些是这样写的:

```
(function () {
  console.log("Hello World");
})();
```

就像把一个函数赋给一个变量一样，如果我们愿意，我们也可以去掉这里的关键字`function`。

```
(() => {
  console.log("Hello World");
})();
```

这两者的工作方式相同，只是没有关键字`function`。

你想对一个函数做的一件主要的事情是传递数据给它，我们使用参数来做这件事。函数接受的参数直接在函数名后面的括号中定义。例如，让我们创建一个将两个字符串组合在一起的函数。

```
function combineStrings(first, second) {
  let result = `${first} ${second}`;
}combineStrings("Hello", "World");
```

这个函数使用我们传入的两个字符串来创建一个新的字符串。但是有一个问题，目前我们不能得到新的字符串。为了获得新的字符串，我们需要从函数中返回它，这是使用`return`关键字完成的。

```
function combineStrings(first, second) {
  return `${first} ${second}`;
}let newString = combineStrings("Hello", "World");
```

现在，如果我们要记录`newString`，那么我们将得到`"Hello World"`。这是一个最基本的函数，函数可以像你希望的那样复杂。让我们看看参数的其他选项，例如，如果我们不知道有多少个字符串将被传递到函数中。这不是问题，我们可以使用`...`语法将所有传递的参数转换成一个数组，例如:

```
function combineStrings(...strings) {
   // This logs ["Hello","World","!"]
   console.log(strings);
}combineStrings("Hello", "World", "!");
```

也许我们甚至不确定参数是否会被传递，为此我们可以使用默认值。这些是这样写的:

```
function doSomething(param1 = "Default", param2 = 34, param3 = false) {}// doSomething will use "Hello", 34, false
doSomething("Hello");// doSomething will use "Hello", 100, false
doSomething("Hello", 100);// doSomething will use "Hello", 100, true
doSomething("Hello", 100, true);
```

关于函数，我们要看的最后一件事是它们可以调用自己，让我们来看看这是如何工作的:

```
function increment(count = 0) { // Log count
  console.log(count); // Increment
  count++; // If count equals 1then increment again
  if (count === 1) {
    increment(count);
  }}increment();
```

如果您运行这个函数，您将得到两个日志，`0`和`1`。你需要非常小心函数调用它们自己，如果你不小心，那么你可能会陷入一个无限循环，函数一遍又一遍地调用自己。

好了，现在你对函数有了很好的理解。接下来，我们将看一看我们可以用字符串做的一些很酷的事情。

# 用线串

Javascript 有许多方便的方法来处理字符串。我现在不会一一介绍，但我会介绍我认为你会用得最多的几个。

让我们从`length`属性开始，它执行 tin 上的内容。

```
let ourString = "Hello World!";// This logs 12
console.log(ourString.length);
```

现在假设我们想知道我们的字符串是否包含感叹号，为此我们使用`includes`方法。

```
let withMark = "Hello World!";
let withoutMark = "Hello World";// This logs true
console.log(withMark.includes("!"));// This logs false
console.log(withoutMark.includes("!"));
```

所以我们现在知道，如果我们的字符串包含感叹号，也许我们不希望它。为了去掉感叹号，我们可以使用`replace`方法，这需要两个参数。第一个参数是我们要搜索的内容，第二个参数是我们要替换的内容。

```
let ourString = "Hello World!!";// This logs "Hello World!"
console.log(ourString.replace("!", ""));
```

这里我们搜索感叹号，并用一个空字符串替换它。现在您会注意到,`replace`方法只删除了一个感叹号，这是因为`replace`只搜索子子串的第一个实例。如果我们想要替换感叹号的所有实例，那么我们需要同时使用两种方法，即`split`和`join`方法。`split`方法使用给定的参数将一个字符串分割成一个数组，这个参数从字符串中移除。例如:

```
let ourString = "Hello World!!";// This logs ["Hello World", "", ""]
console.log(ourString.split("!"));
```

太好了，我们去掉了感叹号。现在唯一的问题是，这是一个数组，而不是一个字符串，要把它变回一个字符串，我们使用`join`方法。需要注意的是，`join`是一个`array`方法，而不是一个`string`方法，在一个字符串上使用它会给你一个错误。

```
let ourString = "Hello World!!";// This logs "Hello World"
console.log(ourString.split("!").join(""));
```

让我们再看一个例子，假设我们有一个字符串，其中多次出现单词 single，我们想用 world double 替换所有 single 的实例。

```
let ourString = "single single single";// Split - this logs ["", " ", " ", ""]
console.log(ourString.split("single"));// Join - this logs "double double double"
console.log(ourString.split("single").join("double"));
```

现在我们来看看如何替换子字符串，假设我们想让所有的内容都大写。为此，我们使用了`toUpperCase`方法，还有一个以相反方式工作的`toLowerCase`方法。

```
let ourString = "hello world";
let upperCase = ourString.toUpperCase();
let lowerCase = upperCase.toLowerCase();// This logs "HELLO WORLD"
console.log(upperCase);// This logs "hello world"
console.log(lowerCase);
```

还有最后一个方法我想给你看，那就是`trim`方法。假设我们有一个用户输入，我们想处理它的值，这个值可能有不必要的空白。`trim`方法允许我们删除所有的空白。

```
let badString = "     hello world     ";// This logs "     hello world     "
console.log(badString);// This logs "hello world"
console.log(badString.trim());
```

好了，这些是我认为最有用和最常用的处理字符串的便捷方法。现在让我们来看看数字。

# 数字

让我们先来看看用 Javascript 写数字的一些不同方法。首先，我们有明显的两个，整数和浮点数。整数没有小数点，浮点有小数点。

```
// Integer
8// Float
8.4
```

接下来我们有指数符号。假设你想用 7620 万这个数字，而不需要输入大量的零，你可以直接输入它，它是指数。

```
// This
762e5// Instead of
76200000
```

你也可以使用负指数，例如，`0.00762`就是`762e-5`。

我们也可以把数字写成十六进制，例如，`161`就是`0xA1`。我不打算详细讨论十六进制和指数，这些是另一个更基于数学的故事的主题。

现在我们知道了一些写数字的方法，假设我们想要检查一个值是否是一个数字，为此，我们使用`isNaN`函数。`NaN`代表不是一个数字。

```
// This logs false
console.log(isNaN(1));// This logs false
console.log(isNaN(1.6));// This logs false
console.log(isNaN(762e5));// This logs false
console.log(isNaN(0xA1));// This logs false
console.log(isNaN(true));// This logs false
console.log(isNaN("1"));// This logs true
console.log(isNaN("1A"));
```

大多数结果你都可以预料到，但是为什么`true`和`"1"`也会记录错误。`true`记录 false，因为 boolean 基本上是一个`1`或一个`0`，所以 true 实际上是`1`。`isNaN`函数首先将参数转换成一个数字，然后计算出它是否是一个实际的数字。这个功能解释了为什么`"1"`返回 false，因为如果你把`"1"`转换成一个数字，那么它等于`1`。

接下来，让我们看看改变数字格式的方法:

```
let ourNumber = 1376.89;// This logs "1376.89"
console.log(ourNumber.toString());// This logs "1.37689e+3"
console.log(ourNumber.toExponential());// This logs "1.4e+3"
console.log(ourNumber.toExponential(1));// This logs "1377"
console.log(ourNumber.toFixed());// This logs "1376.890000"
console.log(ourNumber.toFixed(6));// This logs "1376.89"
console.log(ourNumber.toPrecision());// This logs "1376.9"
console.log(ourNumber.toPrecision(5));
```

我们也可以反过来，用`Number()`把这些格式化的字符串变回一个数字。

```
let ourNumber = 1376.89;// This logs 1376.89
console.log(Number(ourNumber.toString()));// This logs 1376.89
console.log(Number(ourNumber.toExponential()));// This logs 1400
console.log(Number(ourNumber.toExponential(1)));// This logs 1377
console.log(Number(ourNumber.toFixed()));// This logs 1376.89
console.log(Number(ourNumber.toFixed(6)));// This logs 1376.89
console.log(Number(ourNumber.toPrecision()));// This logs 1376.9
console.log(Number(ourNumber.toPrecision(5)));
```

好了，这就是作为 Javascript 初学者需要了解的关于数字的全部内容。现在让我们继续学习更多关于数组的知识。

# 数组

我们已经看了如何声明和访问数组，现在让我们来看看你对数组做的最常见的事情之一，迭代它。在 Javascript 中，有三种简单的方法可以遍历整个数组。我们将从`for`循环开始。

我们可以用两种不同的方式编写我们的`for`循环，我们将首先看一下编写它们的老方法。我们首先声明一个计数变量，然后定义循环将运行多少次，在本例中是数组的长度，最后，我们在每次迭代中增加计数。

```
let colours = ["red","green","blue","purple","yellow"];for (let i=0; i<colours.length; i++) {
  console.log(colours[i]);
}
```

我们刚刚编写的代码将遍历数组中的每一项，并将其记录到控制台。旧的语法有点麻烦，可能会导致令人讨厌的错误，例如，如果您不小心告诉它循环错误的次数。现在，我们能够以更简单的方式编写相同的循环，如下所示:

```
let colours = ["red","green","blue","purple","yellow"];for (colour of colours) {
  console.log(colour);
}
```

这做完全相同的事情，但它看起来干净得多。虽然`for`循环确实有它的用处，例如，如果你想在循环中运行异步代码，我们有另外两种很好的方法来迭代数组。`forEach`和`map`是两个数组方法，两者写法相同。

```
let colours = ["red","green","blue","purple","yellow"];colours.forEach((colour) => {
  console.log(colour);
});colours.map((colour) => {
  console.log(colour);
});
```

这两种方法都将函数作为参数，并且该函数将数组项作为参数。我们还可以向我们的函数添加两个其他参数，一个`index`参数和一个`array`参数，但我现在不会深入讨论这些。

在这一点上，你可能想知道拥有`forEach`和`map`方法的意义何在，因为它们看起来几乎完全一样，似乎做着完全相同的事情。没错，他们非常相似，`forEach`能做的事情，`forEach`能做的事情，`map`能做的事情，`map`都能做。区别在于`forEach`不返回任何东西，而`map`返回一个值。

正因为如此，`forEach`用于当你不想修改数组时，例如，你只想显示其中的值，或者把每一项保存到存储器中。`map`用于当你想修改数组时，例如替换数组中的值。让我们通过给颜色本身添加颜色这个词来看看它的作用。

```
let colours = ["red","green","blue","purple","yellow"];let mapColours = colours.map((colour) => {
  return `${colour} colour`;
});let forEachColours = colours.forEach((colour) => {
  return `${colour} colour`;
});// This logs ["red colour", "green colour", "blue colour", "purple colour", "yellow colour"]
console.log(mapColours);// This logs undefined
console.log(forEachColours);
```

我说过`map`可以做`forEach`能做的所有事情，但是你应该只在修改数组的时候使用它。你可能会想，为什么我只能用它来修改数组。原因是`map`将创建一个新的数组，即使你没有返回任何东西，作为程序员，这是我们不希望的，因为我们的代码应该尽可能快和高效。

让我们看看如何向数组中添加项，第一种方法是直接向索引中添加值。

```
let numbers = [1,2,3,4];
numbers[4] = 5;// This logs [1,2,3,4,5]
console.log(numbers);
```

虽然这样做是可能的，但我不推荐这样做。如果索引错误，那么您可能会覆盖一个值或在数组中创建间隙，这两种情况很可能都不是我们想要的。让我们来看看这些场景:

```
let numbers = [1,2,3,4];// Overwrite
numbers[2] = 5;// This logs [1,2,5,4]
console.log(numbers);// Gap
numbers[9] = 5;// This logs [1,2,3,4,undefined,undefined,undefined,undefined,undefined,5]
console.log(numbers);
```

向数组中添加条目的更好的方法是使用`push`方法，就像我们在前面的故事中使用的一样。使用`push`，您想要添加的值将始终被添加到数组的末尾。

```
let numbers = [1,2,3,4];
numbers.push(5);// This logs [1,2,3,4,5]
console.log(numbers);
```

我们也可以在数组的开头添加值，为此我们使用了`unshift`方法。

```
let numbers = [1,2,3,4];
numbers.unshift(5);// This logs [5,1,2,3,4];
console.log(numbers);
```

但是如果我们想在数组中间的某个地方添加一个值，或者多个值，该怎么办呢？我们可以通过使用`splice`方法做到这一点，这需要几个参数。第一个参数是应该插入新项目的索引。第二是应该删除多少项。最后，其余的参数是您想要添加的值。

```
let numbers = [1,2,3,4];numbers.splice(2, 0, 5);// This logs [1,2,5,3,4];
console.log(numbers);numbers.splice(2, 1, 8, 9);// This logs [1,2,8,9,3,4];
console.log(numbers);
```

很好，我们可以在数组中任意位置添加值。如果我们想删除值呢。我们有三种方法可以做到这一点，`pop`、`shift`和`splice`。让我们从`pop`开始，这个方法移除数组的最后一个索引。

```
let numbers = [1,2,3,4];
numbers.pop();// This logs [1,2,3]
console.log(numbers);
```

`shift`方法移除数组的第一个索引。

```
let numbers = [1,2,3,4];
numbers.shift();// This logs [2,3,4]
console.log(numbers);
```

最后，`splice`的工作方式和以前一样，只是这次我们没有添加任何值。

```
let numbers = [1,2,3,4,5];numbers.splice(2, 1);// This logs [1,2,4,5]
console.log(numbers);numbers.splice(1,2);// This logs [1,5]
console.log(numbers);
```

现在，我们可以声明、添加、移除和迭代数组了。在我们结束数组之前，让我们快速浏览一些非常有用的数组方法。

首先，我们有`toString`方法，它只是返回一个逗号分隔的数组字符串。

```
let numbers = [1,2,3,4];// This logs "1,2,3,4"
console.log(numbers.toString());
```

接下来，我们有`reverse`方法，它做了你所期望的，它反转了数组。

```
let numbers = [1,2,3,4];// This logs [4,3,2,1]
console.log(numbers.reverse());
```

在那之后，我们有了`includes`方法，这就像它处理字符串一样，它允许我们查看一个数组是否有值。

```
let numbers = [1,2,3,4];// This logs true
console.log(numbers.includes(2));// This logs false
console.log(numbers.includes(5));
```

现在我们有了一个非常方便的方法将数组连接在一起，这就是`concat`方法。

```
let numbers1 = [1,2,3,4,5];
let numbers2 = [6,7,8,9,10];
let numbers = numbers1.concat(numbers2);// This logs [1,2,3,4,5,6,7,8,9,10]
console.log(numbers);
```

假设我们有一个数字数组，我们想计算出所有数字的和，例如把它们全部相减。为了进行求和，我们可以使用`reduce`和`reduceRight`方法。这些方法的区别在于`reduce`从第一个索引开始，`reduceRight`从最后一个索引开始。

```
let numbers = [23,32,54,79];let reduce = numbers.reduce((total, number) => {
  return total - number;
});let reduceRight = numbers.reduceRight((total, number) => {
  return total - number;
});// This logs -142
console.log(reduce);// This logs -30
console.log(reduceRight);
```

我选择减去所有的数字，因为这样可以让你清楚地看到`reduce`产生的结果与`reduceRight`不同，因为它是从第一个索引开始的，而不是从最后一个索引开始的。可以看到，这两种方法都将一个函数作为参数，并且这个函数有两个参数，总和以及数组的当前值。

现在，假设我们想对一个数组进行升序排序，为此我们使用了`sort`方法。与前面的方法类似，`sort`也将一个函数作为参数，这个函数包含了排序的逻辑。我们传递的函数有两个参数，`a`和`b`，它们是当前值和下一个值。

```
let numbers = [12, 97, 32, 56, 63];let sortedNumbers = numbers.sort((a, b) => {
  return a - b;
});// This logs [12, 32, 56, 63, 97]
console.log(sortedNumbers);
```

很好，我们现在有了一个排序的数组，但是它是如何工作的呢？为什么我们要从`b`中减去`a`？

我们从`b`中减去`a`，因为如果我们返回一个负数，那么`a`将排在`b`之前。如果我们想对数字进行降序排序，那么我们应该做`b — a`，因为我们希望`b`排在`a`之前。这可能需要一点时间来理解，我知道这花了我一点时间，所以让我们更详细地了解一下。

假设我们有一个包含两个值的数组:

```
[value1, value2]
```

同比较`a — b`:

```
a(value1) - b(value2)
```

通过这种比较，我们有三种可能的结果，正数、负数和零。正数表示`b`将排在`a`之前:

```
value2, value1
```

负数表示`a`将排在`b`之前:

```
value1, value2
```

零表示不需要改变:

```
value1, value2
```

同样，如果我们有`3`和`4`，那么它将排序为`[3, 4]`:

```
[3, 4] -> (3 - 4 = -1) -> [3, 4]
```

如果我们有`4`和`3`，那么它将排序为`[3, 4]`:

```
[4, 3] -> (4 - 3 = 1) -> [3, 4]
```

最后，如果我们有`4`和`4`，那么它将排序为`[4, 4]`:

```
[4, 4] -> (4 - 4 = 1) -> [4, 4]
```

最后一次，这次用`[3,1,1,5]`:

```
// First pass
[3, 1, 1, 5] -> (3 - 1 = 2) -> [1, 3, 1, 5] // 3 & 1 swap
[1, 3, 1, 5] -> (3 - 1 = 2) -> [1, 1, 3, 5] // 3 & 1 swap/*
At this point the array is sorted, but the algorithm requires one full pass with no swaps to know that it is sorted.
*/[1, 1, 3, 5] -> (3 - 5 = -2) -> [1, 1, 3, 5] // No swap// Second pass
[1, 1, 3, 5] -> (1 - 1 = 0) -> [1, 1, 3, 5] // No swap
[1, 1, 3, 5] -> (1 - 3 = -2) -> [1, 1, 3, 5] // No swap
[1, 1, 3, 5] -> (3 - 5 = -2) -> [1, 1, 3, 5] // No swap
```

该算法现在已经完成了一次没有交换的完整传递，因此它知道阵列已经完全排序。我已经对此进行了相当详细的介绍，因为在对数组进行排序时，了解它是如何工作的是很重要的，否则你就不能使用`sort`方法来发挥它的全部能力。我们刚刚使用的排序类型被称为冒泡排序，如果你想更多地了解不同类型的排序并直观地看到它们是如何工作的，那么我强烈推荐你尝试一下[排序。](https://cs.stanford.edu/people/jcjohns/sorting.js/)

好的，最后我们要看看`filter`方法。这个方法允许我们根据条件从数组中过滤出值。例如，假设我们需要所有大于 50 的数字。

```
let numbers = [12, 32, 43, 56, 72, 98];let filteredNumbers = numbers.filter((number) => {
  return number > 50;
});// This logs [56, 72, 98]
console.log(filteredNumbers);
```

过滤数组就是这么简单。好的，现在让我们更详细地看看这些物体。

# 目标

我们已经介绍了您需要了解的关于对象的大部分内容。我们已经了解了如何声明一个对象，如何给它们赋值，以及如何使用函数作为对象值。对象的值也可以使用我们刚刚看到的同样方便的方法，例如，如果你的对象有一个字符串值，你可以使用`toUpperCase()`来格式化它。关于对象，我还想给你们展示三个东西，它们是 getters，setters，和 constructors。

让我们从 getters 开始，这些是你的对象内部的函数，允许你获取一个值。通常情况下，如果您想对所获得的值进行一些格式化，您会使用这些方法。例如，假设我们有一个包含购物篮总数的对象。

```
let basket = {
  total: 0
};// Displays £0.00
console.log(`£${basket.total.toFixed(2)}`);
```

如果不使用 getter，我们将不得不编写类似这样的代码，以用户习惯的方式显示总数。我敢肯定，你可以想象，这将是一个痛苦，必须写在你想显示总数的地方，所以我们将使用一个 getter。

```
let basket = {
  total: 0,
  get totalPrice() {
    return `£${basket.total.toFixed(2)}`;
  }
};// Displays £0.00
console.log(basket.totalPrice);
```

现在我们所要做的就是每当我们想要得到格式化的总数时就写`basket.totalPrice`。另一方面，Setters 允许我们很容易地在一个对象中设置一个值，假设我们有一个对象，当它被设置时，我们希望该值被格式化，例如，一个用户名，我们希望它是小写的。

```
let user = {
  username: "",
  set setUsername(value) {
    this.username = value.toLowerCase();
  }
}user.setUsername = "MYUSERNAME";// Logs "myusername"
console.log(user.username);
```

这两个例子都是 setters 和 getters 的基本用法，通常你会用它们做比我们做的更多的事情，但是它们以简单的方式向你展示了它们做什么以及如何使用它们。

现在让我们看看构造函数，它们非常方便。在开发时，很可能会出现需要多个对象使用同一个结构的情况。当然，你可以像我们一直做的那样为每个对象手动编写结构，或者你可以使用构造函数。构造函数基本上是创建对象的函数，例如，假设我们想要一个用户对象供多个用户使用。

```
// Constructor
function User(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
  this.fullName = function () {
    return `${this.firstName} ${this.lastName}`
  };
};// First user
let firstUser = new User("Bob", "Smith");// Second user
let secondUser = new User("Jill", "Summers");// Logs "Bob Smith"
console.log(firstUser.fullName());// Logs "Jill Summers"
console.log(secondUser.fullName());
```

这里你可以看到我们有两个用户变量，它们都使用相同的对象结构。就像常规函数一样，如果我们不想在创建对象时传入值，我们的构造函数可以有默认值，例如:

```
function User(name, online = true) {
  this.name = name;
  this.online = online;
};let myUser = new User("Bob Smith");// This logs {name: "Bob Smith", online: true}
console.log(myUser);
```

一旦创建了对象，就可以像使用任何其他对象一样使用它。这就是作为 Javascript 初学者需要了解的关于对象的一切。现在让我们来看看如何处理日期。

# 日期

Javascript 有一个处理日期的内置对象，即`Date`对象。让我们从获取当前日期和时间开始。

```
// This logs Sat Jun 29 2019 15:40:17 GMT+0100 (BST)
console.log(new Date());
```

显然，如果您运行此命令，您将获得不同的日志，但格式相同:

```
DayName Month Day Year Hour:Minute:Second Timezone (TimezoneName)
```

大多数时候我们可能不需要所有这些信息，我们可以使用`Date` objects 内置方法来获取我们想要的任何信息。

```
// This logs 2019
console.log(new Date().getFullYear());// This logs 5
console.log(new Date().getMonth());// This logs 29
console.log(new Date().getDate());// This logs 6
console.log(new Date().getDay());// This logs 15
console.log(new Date().getHours());// This logs 45
console.log(new Date().getMinutes());// This logs 56
console.log(new Date().getSeconds());// This logs 129
console.log(new Date().getMilliseconds());
```

默认情况下，这些方法将使用浏览器的时区返回日期，我们可能不希望这样。相反，我们可以使用 UTC 日期和时间，这就像在方法名的`get`后面加上`UTC`一样简单。

```
new Date().getUTCFullYear();
new Date().getUTCMonth();
new Date().getUTCDate();
new Date().getUTCDay();
new Date().getUTCHours();
new Date().getUTCMinutes();
new Date().getUTCSeconds();
new Date().getUTCMilliseconds();
```

另一个常见的任务是，你可能需要在某个时间点获取自纪元以来的毫秒数，这是 1970 年 1 月 1 日午夜 UTC。自从纪元以来，我们有两种不同的方法来获得时间。

```
// This logs 1561820006430
console.log(new Date().getTime());// This logs 1561820006430
console.log(Date.now());
```

你可以看到它们都返回相同的值，我个人使用的是`Date.now()`，但是你选择哪种方法取决于你自己。这是目前你需要知道的全部内容。在 Javascript 中处理日期会变得非常复杂和混乱，我认识的大多数开发人员都使用第三方包来格式化和使用日期。

# 算术和数学

我们已经学习了基本的加法、减法、乘法和除法，还学习了变量的递增和递减。现在让我们来看看模数运算符，它用于计算总和的余数，例如:

```
// This logs 3.7
console.log(8 % 4.3);
```

例如，如果您想知道一个数字是否是偶数，这可能很方便。

```
// This logs true because 4 is even
console.log((4 % 2 === 0));// This logs false because 3 is odd
console.log((3 % 2 === 0));
```

接下来，我们有指数运算符，用于将一个数乘以另一个数的幂。

```
// This logs 4 - 2 squared equals 4
console.log(2 ** 2);// This logs 64 - 4 cubed equals 64
console.log(4 ** 3);
```

就像在学校一样，Javascript 先做乘除再做加减，比如:

```
// This logs 12
console.log(4 + 4 * 2);// This logs 16
console.log((4 + 4) * 2);
```

在 Javascript 中，我们有一个很棒的对象叫做`Math`对象，这允许我们做更高级的数学运算。我将快速介绍一些你最有可能使用的方法。我不打算一一介绍或详述，因为那是另一个更数学化的故事。

```
// Calculate the square root of a given value
// This logs 4
console.log(Math.sqrt(16));// Calculate a given value to the power of another
// e.g. 6 to the power of 8, or 6 * 6 * 6 * 6 * 6 * 6 * 6 * 6
// This logs 1679616
console.log(Math.pow(6, 8));// Round a given value to the nearest integer
// This logs 8
console.log(Math.round(7.6));// This logs 7
console.log(Math.round(7.3));// Round a given value up to the nearest integer
// This logs 8
console.log(Math.ceil(7.3));// Round a given value down to the nearest integer
// This logs 7
console.log(Math.floor(7.6));// Generate a random float between 0 and 1
console.log(Math.random());// Get the highest value in a given sequence
// This logs 7
console.log(Math.max(5,2,6,7,5,3));// Get the lowest value in a given sequence
// This logs 2
console.log(Math.min(5,2,6,7,5,3));
```

`Math`对象也有几个常量供我们使用，就像之前一样，我会快速列出它们，但不会涉及太多细节。

```
// This logs PI
console.log(Math.PI);// This logs E (Eulers number)
console.log(Math.E);// This logs the natural logarithm of 2
console.log(Math.LN2);// This logs the natural logarithm of 10
console.log(Math.LN10)// This logs the base 2 logarithm of E
console.log(Math.LOG2E);// This logs the base 10 logarithm of E
console.log(Math.LOG10E);// This logs the square root of 2
console.log(Math.SQRT2);// This logs the square root of 0.5
console.log(Math.SQRT1_2);
```

好了，你现在知道如何用 Javascript 做数学了。接下来，我们将看看条件和比较。

# 条件和比较

在 Javascript 中，我们有两种方法进行比较，即`if`和`switch`语句。让我们从`if`的声明开始，这是这样写的:

```
if (condition) {
  code to be executed if the condition is true
} else {
  code to be executed if the condition is false
}
```

一条`if`语句可以有多个`if`条件，例如:

```
if (condition) {} else if (condition) {} else if (condition) {} else {}
```

现在我们知道了如何写一个`if`语句，让我们看看我们是如何写条件的。在 Javascript 中我们有 9 个比较操作符，让我们一个接一个地看看。

首先，我们有等于运算符:

```
// This logs true because 3 does equal 3
console.log((3 == 3));// This logs false because 3 does not equal 4
console.log((3 == 4));// This logs true because 3 does equal '3'
console.log((3 == '3'));// this logs true because "1" does equal true
console.log(("1" == true));
```

前两个条件有道理，但为什么后两个也通？这是因为 equals 运算符不关心数据类型，对它来说，字符串`"1"`与数字`1`相同，数字`1`也与`true`相同。如果我们希望数据类型很重要，那么我们可以使用`===`而不是`==`，例如:

```
// This logs true
console.log(("1" == true));// This logs false
console.log(("1" === true));// This logs false
console.log((1 === true));// This logs true
console.log((true === true));
```

接下来，我们有不等于运算符:

```
// This logs true
console.log((1 != 2));// This logs false
console.log(("1" != 1));// This logs true
console.log(("1" !== 1));
```

在这里，您可以看到我们还可以向不等于运算符添加一个额外的`=`,这样我们的数据类型就很重要。

接下来，我们有小于、大于、小于或等于和大于或等于运算符。

```
// This logs true
console.log((10 > 5));// This logs false
console.log((10 < 5));// This logs true
console.log((5 >= 5));// This logs false
console.log((6 <= 5));
```

最后，我们有 not 运算符，我们用它来检查布尔值是真还是假，例如:

```
let exists = false;// This logs true because exists does equal false
console.log((!exists));exists = true;// This logs false because exists doe not equal false
console.log((!exists));
```

这是所有 9 个比较操作符，现在让我们在一个`if`语句中使用它们。

```
let exists = false;if (!exists) {
  // Condition passed
} else {
  // Condition failed
}
```

我们还可以在我们的条件中添加多个比较，如果两个比较都为真或者其中一个为真，我们的条件就可以通过。

```
let exists = false;
let number = 5;if (exists && number === 5) {
  // This will not be executed
} else {
  // This will be executed because neither of our comparisons passed
}if (exists || number === 5) {
 // This will be executed because our number does equal 5
} else {
  // This will not be executed
}
```

你甚至可以在条件中包含条件，例如，如果一个数字在 0 到 10 之间或者在 20 到 30 之间，我们想做一些事情。

```
let number = 9;if ((number > 0 && number < 10) || (number > 20 && number < 30)) {
  // This will execute because 9 is between 0 and 10
}number = 23if ((number > 0 && number < 10) || (number > 20 && number < 30)) {
  // This will execute because 23 is between 20 and 30
}number = 15if ((number > 0 && number < 10) || (number > 20 && number < 30)) {
  // This will not execute because 15 isn't between 0 and 10 or 20 and 20
}
```

你可以看到这很混乱，这是一个你可能想要使用`else if`的例子，例如:

```
let number = 23;if (number > 0 && number < 10) {
  // This will not execute
} else if (number > 20 && number < 30) {
  // This will execute
} else {
  // This will not execute
}
```

现在使用`else if`代码更容易阅读和理解。

好了，现在你应该对`if`语句有了很好的理解，让我们来看看`switch`语句。`switch`语句将一个值与一系列不同的案例进行比较，如果没有一个案例匹配，则执行默认案例。例如，假设我们要将一个数字转换成星期几。

```
let day = 5;switch(day) { case 1:
    console.log("Monday");
    break; case 2:
    console.log("Tuesday");
    break; case 3:
    console.log("Wednesday");
    break; case 4:
    console.log("Thursday");
    break; case 5:
    console.log("Friday");
    break; case 6:
    console.log("Saturday");
    break; case 7:
    console.log("Sunday");
    break; default:
    console.log("Invalid Day");};
```

在本例中，`"Friday"`将被记录，如果我们将日期变量改为`12`，那么`"Invalid Day"`将被记录。您会注意到，在每个日志之后，我们都编写了`break`关键字，这样一旦案例与`switch`语句匹配，就会退出。

最后，使用`switch`语句，我们可以让多个案例运行相同的代码。假设我们想知道我们的一天是工作日还是周末。

```
let day = 5;switch(day) { case 1:
  case 2:
  case 3:
  case 4:
  case 5:
    console.log("Weekday");
    break;
  case 6:
  case 7:
    console.log("Weekend");
    break; default:
    console.log("Invalid Day");};
```

这里我们将看到如果`day`等于 1、2、3、4 或 5，则记录`"Weekday"`，如果`day`等于 6 或 7，则记录`"Weekend"`。关于条件和比较，这就是你所需要知道的，我们现在来看看循环。

# 环

我们已经非常详细地介绍了循环，我们已经查看了`for`、`forEach`和`map`。我们还有两个循环要看，这是`while`和`do while`循环。

首先，`while`循环运行，直到满足一个条件。例如，假设我们想要运行循环，直到一个数等于 10。

```
let fingers = 1;while (fingers < 11) {
  console.log(fingers);
  fingers++;
}
```

这里我们的循环运行了 10 次，每次都给`finger`加 1。你必须小心使用`while`和`do while`循环，因为如果你的条件从未被满足，那么你将会以一个无限循环结束，这将会使你的脚本崩溃。

`do while`循环的工作方式与 while 循环类似，只是它们执行一次块，而不管条件是否满足。例如:

```
let fingers = 1;while (fingers < 1) {
  console.log(`Fingers: ${fingers}`);
  fingers++;
}let toes = 1;do {
  console.log(`Toes: ${toes}`);
  toes++;
} while (toes < 1);
```

如果您运行这段代码，您会看到我们没有从 fingers 循环中获得日志，而从 toes 循环中获得了一个日志。这是因为即使 1 不小于 1，`do while`循环已经执行了一次该块。

# 错误

在 Javascript 中，错误可能非常令人困惑，很难理解到底哪里出错了。例如，如果您运行以下命令:

```
let date = Date().parse();
```

您将得到一个错误，内容如下:

```
error: TypeError: Date().parse is not a function. (In 'Date().parse()', 'Date().parse' is undefined)
```

这实际上是比较容易破译的错误之一，它基本上只是说在`Date()`中没有名为`parse`的函数。这里的主要问题是这会使你的脚本崩溃，这一行可能只是你脚本的一小部分，所以让整个脚本崩溃是不理想的。我们可以做的是将这一行放在一个`try catch`块中，这样脚本就不会崩溃，它可以继续做它应该做的一切。

```
try {
  let date = Date().parse();
} catch(err) {
  console.log('Error message');
}
```

现在，如果您运行这个脚本，它不会崩溃，并且会显示一条错误消息。这可能仍然是你想要的，如果你的代码依赖于这一行，那么你可能希望脚本退出。要强制脚本退出，我们可以使用`throw`关键字。

```
try {
  let date = Date().parse();
} catch(err) {
  console.log('Error message');
  throw err;
}
```

现在，我们的错误消息将被记录，然后脚本将退出并记录详细的错误。这个方法允许你在退出脚本之前向用户显示一个用户友好的错误信息，这比仅仅显示原始的 Javascript 错误更加用户友好。

调试 Javascript 时，你会遇到 6 种错误类型，让我们来看一看，这样你就能理解它们的意思了。

**语法错误**

这是罐头上写的，你的语法有错误。

**参考错误**

这意味着发生了非法引用，例如，从变量的作用域之外访问变量。

**类型错误**

这意味着您正在做一些您不应该用特定数据类型做的事情。例如，如果你试图在一个对象上调用`forEach`，这会给你一个类型错误，因为`forEach`对对象来说不是一个有效的方法。

**范围错误**

这意味着数字超出了有效范围。例如，如果您试图使用`toPrecision`方法给出一个 30 位小数的浮点数，这会给你一个 RangeError，因为`toPrecision`只允许数字最多有 21 位小数。

**URIError**

这意味着在一个 URI 处理函数中发生了错误，这是由畸形的 URI 引起的。

**EvalError**

最后，这意味着在`eval`功能中出现了错误。EvalError 不是您真正需要担心的问题，因为较新版本的 Javascript 会抛出 SyntaxErrors 而不是 EvalErrors。

现在我们已经知道了更多关于错误和如何处理它们的知识，让我们继续学习类。

# 班级

类类似于对象，但更高级。我将编写一个类，然后我们将一点一点地学习它。

```
class ScoreBoard { constructor(...players) { this.players = [];
    players.forEach((player) => { this.players.push({
        name: player,
        score: 0
      }); }); } displayScores() { this.players.forEach((player) => {
      console.log(`${player.name}: ${player.score}`);
    }); } updateScore(playerName) { this.players.map((player) => { if (player.name === playerName) {
        player.score++;
      } return player; }); }}let scoreboard = new ScoreBoard("John", "Lucy", "Steve");scoreboard.updateScore("Lucy");
scoreboard.updateScore("John");
scoreboard.updateScore("Steve");
scoreboard.updateScore("Steve");
scoreboard.updateScore("Steve");
scoreboard.updateScore("Lucy");
scoreboard.updateScore("John");
scoreboard.updateScore("Lucy");scoreboard.displayScores();
```

这里我们写了一个处理简单游戏得分的类。首先，我们有`constructor`，这是一个每当类的新实例被创建时都会被调用的函数。在我们的`constructor`中，我们将所有的玩家添加到一个数组中，并给他们一个零的初始分数。接下来，我们有一个显示分数的函数，它所做的就是遍历玩家并记录分数。最后，我们有一个更新特定玩家分数的函数，为此我们使用`map`来遍历所有玩家。如果玩家的名字与我们传入的名字相同，那么分数就会更新。

在这个类下面，我们创建了这个类的一个新实例，并将其存储在一个名为`scoreboard`的变量中，然后我们给玩家添加一些分数，最后显示这些分数。希望这个例子显示了类对于模块化你的代码是多么的有用。我认为对你来说，学习更多关于类和它们如何工作的最好方法是利用我在上面写的东西，试着给它增加更多的功能。

# 异步编程

假设我们想执行一个函数，但是我们不知道这个函数要执行多长时间，我们如何得到这个函数的返回值呢？我们过去使用所谓的回调，这些函数被传递给另一个函数，然后在完成时被调用。回调有点像噩梦，事情很快变得非常混乱。现在我们有了处理异步任务的更好方法，让我们从承诺开始。

让我们创建一个返回承诺的函数。

```
function exists(value) {

  return new Promise((resolve, reject) => {

    setTimeout(() => {

      if (value) {
        resolve('Value is true');
      } else {
        reject('Value is false')
      }

    }, 2000);

  });

}exists(true).then((response) => {
  console.log(response);
}).catch((err) => {
  console.log(err);
});
```

这里，我们的函数`exists`返回一个承诺，这个承诺根据我们传递给它的值决定是接受还是拒绝。如果我们传入 true，那么承诺解决，换句话说，一切顺利，如果我们传入 false，那么承诺拒绝，换句话说，这意味着错误。当我们调用我们的函数时，我们有两个其他的函数链接到它，`then`和`catch`。如果承诺成功，将调用`then`函数，如果承诺被拒绝，将调用`catch`函数。如果您运行这段代码，您会看到我们的日志直到两秒钟后才出现，这是因为我们使用了`setTimeout`来模拟发生的事情。如果您在函数中有一个网络请求，那么在请求完成之前，日志不会出现。

我们也可以连锁承诺，例如，让我们再次呼唤存在。

```
function exists(value) {

  return new Promise((resolve, reject) => {

    setTimeout(() => {

      if (value) {
        resolve('Value is true');
      } else {
        reject('Value is false')
      }

    }, 2000);

  });

}
exists(true).then((response) => {
  console.log(response);
  return exists(true);
}).then((response) => {
  console.log(response);
}).catch((err) => {
  console.log(err);
});
```

现在，如果我们运行这段代码，我们将在两秒钟后看到日志`"Value is true"`，然后两秒钟后我们将看到另一个日志说同样的话。

起初，承诺可能会令人迷惑，我知道我花了一段时间才理解它们，但是你用得越多，你就越能理解它们。你也可能看到链接多个承诺会变得非常混乱，我们实际上有一个更简单的方法来处理这个问题，那就是异步等待。

使用 async-await，我们能够定义异步函数，这些函数可以等待来自其他异步函数的响应。例如，让我们用 async-await 写同样的东西。

```
function exists(value) {

  return new Promise((resolve, reject) => {

    setTimeout(() => {

      if (value) {
        resolve('Value is true');
      } else {
        reject('Value is false')
      }

    }, 2000);

  });

}async function getExists() {

  let firstResponse = await exists(true);
  console.log(firstResponse);

  let secondResponse = await exists(true);
  console.log(secondResponse);

}getExists();
```

我们仍然得到我们的两个日志，但是这段代码更加清晰易读。这里我们唯一没有的是捕捉任何错误的能力，让我们把它加进去。

```
function exists(value) {

  return new Promise((resolve, reject) => {

    setTimeout(() => {

      if (value) {
        resolve('Value is true');
      } else {
        reject('Value is false')
      }

    }, 2000);

  });

}async function getExists() {

  let firstResponse = await exists(true);
  console.log(firstResponse);

  let secondResponse = await exists(true);
  console.log(secondResponse);

  let errorResponse = await exists(false);

}getExists().catch((err) => {
  console.log(err);
});
```

我们现在已经用 false 添加了对`exists()`的调用，以便承诺被拒绝，我们还在调用`getExists`函数时添加了`catch()`。在这里添加`catch`将捕获`getExists`函数中的每个错误，这很好，因为我们所有的错误都集中在一个地方。

我想我会把异步编程留到这里，现在你应该对承诺和异步等待有了一个基本的了解。现在最好的办法是练习使用它们，这很可能会比阅读更多细节更快地帮助你掌握它们。

# 结论

如果我没有做错，那么您现在应该对 Javascript 基础有了很好的理解。像任何新技能一样，熟能生巧，你写的 Javascript 越多，你就会变得越好，越博学。如果你有任何问题，请提出来，我会尽我所能回答。

在[的下一个故事](https://medium.com/@willptswan/a-complete-beginners-guide-to-javascript-part-2-501ec89af76c)中，我们将看看如何使用 DOM 以及如何使用 Ajax 异步发出网络请求。

第二部分:[https://medium . com/@ willptswan/a-complete-初学者-javascript 指南-part-2-501ec89af76c](https://medium.com/@willptswan/a-complete-beginners-guide-to-javascript-part-2-501ec89af76c)