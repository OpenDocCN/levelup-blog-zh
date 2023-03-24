# 学习 JavaScript:使用字符串

> 原文：<https://levelup.gitconnected.com/learning-javascript-working-with-strings-c5e1cba93a8a>

![](img/5f8988a0900fbc0da413c3ae8ec0449f.png)

[FOODISM360](https://unsplash.com/@foodism360?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

字符串是 JavaScript 中第二常用的数据类型，在许多情况下，由于 JavaScript 广泛用于 web 应用程序，所以它是主要的数据类型。在本文中，我将讨论字符串在 JavaScript 中是如何工作的，以及如何高效地使用它们。我还将讨论一些刚刚被发现和使用的新功能。

# 定义的字符串

字符串是用单引号或双引号括起来的 0 个或多个字符的任意集合。字符串中的字符可以是字母字符、数字、符号和空格。以下是 JavaScript 字符串文字的一些示例:

```
"hello world"
'good bye, world!'
"1600 Pennsylvania Avenue"
'$*&!@ it!'
```

如果您在字符串中使用单引号，并且您需要嵌入单引号来写出缩写，您可以使用反斜杠字符(`\`)作为*转义符*。要了解为什么需要这样做，让我们看看当您在 JavaScript shell 中写出这样一个字符串而没有转义单引号时会发生什么:

```
js> 'can't'
typein:1:5 SyntaxError: unexpected token: identifier:
typein:1:5 'can't'
typein:1:5 .....^
```

解释器不知道如何处理单引号后的 t。

现在看看当我们对单引号进行转义时会发生什么:

```
js> 'can\'t'
"can't"
```

转义字符告诉解释器将单引号视为撇号，而不是“字符串结束”字符。

您可以在字符串中嵌入其他字符，包括换行符(`\n`)和制表符(`\t`)。以下是使用 shell 的一些示例:

```
js> print("Hello, \n world!");
Hello,
world!
js> print("Hello, \tworld");
Hello,  world
```

如果您想知道，也可以对这些字符进行转义，如下例所示:

```
js> print("The newline character is \\n.");
The newline character is \n.
```

# 字符串操作

有一个处理字符串的内置操作符—连接操作符(`+`)。串联运算符用于将两个或多个字符串放在一起形成一个字符串。以下是使用 shell 的一些示例:

```
js> "Paul" + "McCartney"
"PaulMcCartney"
js> "John" + " " + "Lennon"
"John Lennon"
js> "John" + " Paul" + " George Ringo"
"John Paul George Ringo"
```

连接时，可以同时使用单引号字符串和双引号字符串:

```
js> 'now is the time' + " for all good people " + 'to come to the aid of their party'
"now is the time for all good people to come to the aid of their party"
```

在串联运算符之后，有一组函数可用于对字符串执行操作。关于一个字符串，你需要知道的第一件事就是它有多长。您可以使用`length`功能找到答案:

```
js> let words = "now is the time for all good people";
js> words.length;
35
```

如果您想将一个字符串重复设定的次数，您可以使用`repeat`功能:

```
js> let greet = "Hello";
js> greet.repeat(5);
"HelloHelloHelloHelloHello"
```

您可以使用`includes`函数在字符串中搜索一个字符串，该函数返回一个布尔结果:

```
js> let words = "the quick brown fox jumped over the lazy dog";
js> words.includes("fox");
true
```

您可以使用`substr`函数从字符串中提取字符串的一部分，称为子字符串:

```
js> let name = "James Earl Jones";
js> name.substr(6,4)
"Earl"
```

类似的函数`substring`返回起点和终点之间的子字符串:

```
js> let name = "James Earl Jones";
js> name.substring(6,10);
"Earl"
```

如果你需要替换字符串的一部分，但不想替换整个字符串，你可以使用`replace`功能。它有两个参数—要替换的字符串和替换字符串。这里有一个例子:

```
js> let word = "recieve";
js> word = word.replace("ie", "ei");
"receive"
```

JavaScript 中可以使用的字符串函数还有很多很多。更多信息，[这里有一个指向 JavaScript 字符串函数参考页面的链接。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)

# 模板文字

JavaScript 字符串最强大的新特性之一是模板文字。模板文字可以做很多你用常规字符串做不到的事情，或者你只能用更复杂的技术来做。

当您用反斜线(```)而不是单引号或双引号将字符串括起来时，字符串就是模板文字。例如:

```
js> let greeting = `Hello, world!`;
js> print(greeting);
Hello, world!
```

在这方面，模板文字``Hello, world!``的行为就像一个普通的字符串。模板文字发挥作用的地方是在特殊情况下和更复杂的字符串使用中。

这里有一个例子。在编写 JavaScript 程序时，有时会希望在一行的中间断开一个字符串，并在下一行继续，例如当字符串特别长时。通常，您必须用反斜杠结束该行。这里有一个例子:

```
js> let sentence = "Now is the time for all good people \
to come to the aid of their party.";
js> sentence
"Now is the time for all good people to come to the aid of their party."
```

使用模板文字，你不需要使用反斜杠:

```
js> let sentence = `Now is the time for all good people
to come to the aid of their party.`;
js> sentence
"Now is the time for all good people \nto come to the aid of their party."
```

模板文字的另一个用途是将数据代入字符串。在当前版本的 JavaScript 之前，如果您想将变量数据放在字符串中，您必须将字符串与变量连接起来，如下例所示:

```
js> let salary = 25000;
js> print("This year, your salary of $" + salary + " is going to increase by 10%.");
This year, your salary of $25000 is going to increase by 10%.
```

使用模板文字，您可以使用特殊的语法将变量直接放入字符串中。下面是上面例子的工作原理:

```
js> let salary = 25000;
js> print(`This year, your salary of $${salary} is going to increase by 10%.`);
This year, your salary of $25000 is going to increase by 10%.
```

特殊的语法是*$ {变量名}* 。将整个字符串写成模板文字，将变量放在`${}`中会告诉解释器用变量存储的值替换变量名。模板文字的这种使用非常强大，并且使得字符串编程更加容易。

模板文字还有更多用途，但这是所有 JavaScript 程序员应该立即开始使用的两个主要用途。

# 理解字符串很重要

JavaScript 中的字符串编程极其重要，尤其是在 web 编程中，大量数据都是字符串形式的。学习字符串是如何工作的，理解所有可用的字符串函数，会让你成为更好、更高效的 JavaScript 程序员。

感谢您的阅读，请给我发电子邮件提出意见和建议。