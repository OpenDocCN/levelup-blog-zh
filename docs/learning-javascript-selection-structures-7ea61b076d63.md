# 学习 JavaScript:选择结构

> 原文：<https://levelup.gitconnected.com/learning-javascript-selection-structures-7ea61b076d63>

![](img/7f755b78d4280db31c0f8a262fd0cc18.png)

由 [Vladislav Babienko](https://unsplash.com/@garri?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 程序并不总是以直线、顺序的方式编写的。使用计算机解决的大多数问题都要求程序能够选择执行哪一行代码。例如，一个工资单程序必须根据员工在一周或一个工资周期内工作了多少小时来决定他们的工作是应该得到正常工资还是加班工资。

JavaScript 提供了两种在代码中进行选择的主要结构。这些结构中的第一个是`if`语句。另一个构造是条件运算符。本文将讨论如何在 JavaScript 中形成各种类型的`if`语句，然后以讨论条件操作符结束。

# 关系运算符

在我谈论选择结构之前，我需要了解 JavaScript 程序如何进行比较。JavaScript 中的比较是使用关系运算符完成的。关系运算符比较不同关系的两个值。关系运算符有:

*   `>`大于
*   `>=`大于或等于
*   `<`不到
*   `<=`小于或等于
*   `!=`不等于
*   `!==`严格不等于
*   `==`等于
*   `===`严格等于

现在，让我们通过定义几个变量并使用 Spidermonkey shell 使用这些操作符进行比较来看看这些操作符是如何工作的:

```
js> let word = "hello";
js> number > 50
true
js> number >= 101
false
js> word < "Hello"
false
js> word > "garbage"
true
js> number <= 100
true
```

数字比较应该是可以理解的，但是您可能对字符串比较感到好奇。通过比较每个字母的 ASCII 值来比较字符串，从每个单词的第一个字母开始。如果第一个单词的第一个字母比第二个单词的第一个字母具有更高的 ASCII 值，则第一个单词更大。这就是为什么上例中“hello”不小于“Hello”的原因。

如果你不熟悉 ASCII，请到这里寻求解释。

可能需要解释的两个关系操作符是严格操作符，`===`和`!==`。由于 JavaScript 设计中的一些古怪之处，这些操作符是必要的。我可以通过使用 shell 的演示来最好地解释这些怪癖:

```
js> let number = 100;
js> let strNumber = "100";
js> number == strNumber
true
```

JavaScript 将数字变量评估为等于字符串变量，因为它们具有相同的表面值，尽管它们并不真正相等，因为它们不能互换使用。但是，如果我们使用严格的等式运算符来比较这两个变量，我们会得到不同的结果:

```
js> number === strNumber
false
```

我们可以用不等于运算符来演示同样的古怪之处:

```
js> let number = 100;
js> let strNumber = "100";
js> number != strNumber
false
js> number !== strNumber
true
```

# 简单的 if 语句

JavaScript 中第一种类型的`if`语句是简单的`if`。在这种形式中，测试一个关系表达式，如果表达式的计算结果为真，则执行一条或一组语句。

下面是简单 if 的语法模板:

*if(关系表达式){
语句；
}*

该模板指示对关系表达式进行求值，如果结果为真值，则执行一条或一组语句。

如果你对编程有所了解，你可能会注意到我没有提到任何关于语句的花括号。如果代码块中只有一条语句，JavaScript 和大多数其他语言都允许花括号是可选的。但是，无论如何都要包括大括号，这已经成为一种标准的编程实践，目的是为了防止当您希望在一个块中执行多个语句，但却忽略了大括号时出现的逻辑错误。这种实践实际上是由一位著名的 JavaScript 程序员道格拉斯·克洛克福特开始的，他在他的书*JavaScript:The Good Parts*中描述了这种实践。

下面是一个演示简单 if 如何工作的示例程序:

```
let grade = 0;
putstr("Enter your grade: ");
grade = parseInt(readline());
if (grade >= 70) {
  print("You passed!");
}
```

这个程序运行了两次:

```
Enter your grade: 78
You passed!Enter your grade: 65
```

您会注意到，当输入 65 分时，什么也没有发生。这是因为当关系表达式为假时，简单的`if`并没有提供要发生的事情。为此，您需要第二种形式的`if` 语句，即`if-else`语句。

# if-else 语句

这种形式的`if`语句允许在关系表达式为假时执行一组语句，因此它比简单的`if`语句有用得多。下面是`if-else`的语法模板:

*if(关系表达式){
语句；
}
else {
语句；
}*

现在，我们可以重写上面的示例，以便在用户没有获得及格分数时向他们发出一条消息:

```
let grade = 0;
putstr("Enter your grade: ");
grade = parseInt(readline());
if (grade >= 70) {
  print("You passed!");
}
else {
  print("Sorry. You did not pass.");
}
```

这个程序运行了两次:

```
C:\js>js test.js
Enter your grade: 78
You passed!C:\js>js test.js
Enter your grade: 68
Sorry. You did not pass.
```

编程中有很多问题只需要一个`if-else`语句。然而，还有许多其他问题需要评估一个以上的关系表达式来做出决定，这些类型的问题需要 if 语句的第三种形式，即`if-else if`语句。

# if-else if 语句

`if-else if` 语句允许将一长串关系表达式捆绑在一起，形成一个逻辑块。这种决策的一个典型例子是将字母等级分配给数字分数，其中可能的字母等级和分数范围是:

90–100 A
80–89 B
70–79 C
60–69D
0–59 F

下面是`if-else if`语句的语法模板:

*if(关系表达式){
语句；
}
else if(关系表达式){
语句；
}
else if(关系表达式){
语句；
}
……
else {
语句；
}*

省略号表示语句中可以有更多的`else if`块。

第一个关系表达式之后的每个关系表达式都是可选的，就像语句末尾的`else`一样。

让我们看看如何使用`if-else if`来解决我上面介绍的字母等级问题:

```
let letterGrade = "";
let grade = 0;
putstr("Enter the grade: ");
grade = parseInt(readline());
if (grade >= 90) {
  letterGrade = "A";
}
else if (grade >= 80) {
  letterGrade = "B";
}
else if (grade >= 70) {
  letterGrade = "C";
}
else if (grade >= 60) {
  letterGrade = "D";
}
else if (grade >= 0) {
  letterGrade = "F";
}
print(`A numeric grade of ${grade} earns a letter grade of
      ${letterGrade}.`);
```

以下是该程序的几次运行:

```
C:\js>js test.js
Enter the grade: 88
A numeric grade of 88 earns a letter grade of B.C:\js>js test.js
Enter the grade: 71
A numeric grade of 71 earns a letter grade of C.
```

# 条件运算符

有一种编写 if-else 语句的快捷方法，称为条件运算符。该运算符由两个符号 `$:`组成。以下是条件运算符的语法模板:

*(关系表达式)？如果为真则评估语句:如果为假则评估语句；*

下面是使用条件运算符编写的通过/失败程序:

```
let grade = 0;
putstr("Enter the grade: ");
grade = parseInt(readline());
(grade >= 70) ? print("You passed!") : print("Sorry. You didn't 
   pass.");
```

下面是该程序的两次运行:

```
C:\js>js test.js
Enter the grade: 74
You passed!C:\js>js test.js
Enter the grade: 65
Sorry. You didn't pass.
```

条件操作符通常比`if-else`语句要短，但是不一定容易阅读和理解，所以如果你担心其他人阅读和理解你的程序做什么，请小心使用这个操作符。

感谢您的阅读，请给我发电子邮件提出意见和建议。如果你对我的在线编程课程感兴趣，请访问 https://learncpp.teachable.com。