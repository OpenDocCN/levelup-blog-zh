# 学习 C++:使用 if 语句进行分支

> 原文：<https://levelup.gitconnected.com/learning-c-branching-using-if-statements-916f6a80a8e1>

![](img/cd326dc6a9da0a3973c716d8648d2455.png)

照片由[弗拉季斯拉夫·巴比延科](https://unsplash.com/@garri?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

每种编程语言都必须具备两个主要的构造，才能计算所有可计算的内容。第一个结构是循环。在以前的文章中，我已经介绍了三种主要的循环类型— `for`、`while`、`do while`。

在本文中，我将介绍每种编程语言都需要的另一个主要结构——`if` 语句。`if`语句允许程序代码从严格的顺序执行中分支或偏离。这允许程序根据在处理过程中遇到的数据来决定计算什么。

# 关系运算符

C++通过评估一个关系表达式(我也称之为条件)来执行`if`语句，以确定该表达式是真还是假。这是通过使用关系运算符比较数据来完成的。关系运算符是二元运算符，返回一个值，可以是 true 或 false。以下是关系运算符:

`>`:大于

`>=`:大于等于

`<`:小于

`<=`:小于或等于

`==`:等于

`!=`:不等于

关系运算符用于比较变量、表达式或文字。这里有几个例子:

```
100 == 100
95 >= 90
65 <= 60
"Joe" > "Joseph"
"Michael" == "Micheal"
```

当关系运算符用于比较数据值时，该表达式称为关系表达式，有时也称为条件。正如我前面提到的，关系表达式产生一个布尔值——真或假。该值可以赋给一个布尔变量，但更可能的是，一个关系表达式仅用于在`if`语句中的值。

# 简单的 if 语句

既然我们已经了解了 C++是如何进行比较的，我们就可以探索如何在各种形式的`if`语句中使用它们。第一个是简单的`if`语句。

简单的`if`语句评估一个关系表达式，如果关系表达式为真，则执行`if`语句体中的一组语句。下面是简单的`if`语句的语法模板:

*if(条件){
语句；
}*

*s* 在括号中，因为在`if`语句的主体中可以有一个或多个语句。

下面是一个完整程序中简单的`if`语句的例子:

```
int main()
{
  string passFail = "Fail";
  int grade;
  cout << "Enter grade: ";
  cin >> grade;
  if (grade > 59) {
    passFail = "Pass";
  }
  return 0;
}
```

如果`grade` 的值为 60 或更大，则值`“Pass”`存储在`passFail`中。如果`grade`的值小于 60，则什么都不会发生。

这个版本的`if`语句的效用有限。更常用的`if`语句形式是`if-else`语句。

# if-else 语句

当程序需要做出决策时，如果条件为真，通常需要执行一组语句，如果条件为假，则需要执行另一组语句。这需要在`if` 之后有一个`else`子句来处理这种“否则”的情况。

以下是`if-else`语句的语法模板:

*if(条件){
语句；
}
else {
语句；
}*

如果条件为真，则执行`if`中的一组语句。如果条件为假，则执行`else`中的一组语句。

下面是我们之前看到的用`if-else`语句修改的例子:

```
int main()
{
  string passFail = "";
  int grade;
  cout << "Enter grade: ";
  cin >> grade;
  if (grade > 59) {
    passFail = "Pass";
  }
  else {
    passFail = "Fail";
  }
  cout << "Test results: " << passFail << endl;
  return 0;
}
```

以下是该程序两次运行的输出:

```
Enter grade: 77
Test results: PassEnter grade: 55
Test results: Fail
```

下面是演示如何使用`if-else`语句的另一个示例:

```
int main()
{
  string password = "", pwd;
  cout << "Enter your password (8 character minimum): ";
  cin >> pwd;
  if (pwd.length() < 8) {
    cout << "Password must be at least 8 characters." << endl;
    cout << "Please re-enter your password: ";
    cin >> pwd;
  }
  else {
    password = pwd;
  }
  cout << "Password saved." << endl;
  return 0;
}
```

这个例子演示了创建`if`语句时使用的逻辑的重要一点。编写这个程序时，假设用户没有输入所需的最少字符数，从而输入了错误的密码。假设用户第一次输入的密码是正确的，也可以编写这样的程序:

```
int main()
{
  string password = "", pwd;
  cout << "Enter your password (8 character minimum): ";
  cin >> pwd;
  if (pwd.length() >= 8) {
    password = pwd;
  }
  else {
    cout << "Password must be at least 8 characters." << endl;
    cout << "Please re-enter your password: ";
    cin >> pwd;
  }
  cout << "Password saved." << endl;
  return 0;
}
```

这些例子指出，`if`语句中使用的逻辑可以根据用户如何看待问题而变化，并且在许多情况下，设计逻辑如何工作没有对错之分，只要逻辑确实工作并且问题得到正确解决。

每当一个编程问题需要“做这个或做那个”逻辑时，就使用`if-else`语句。有些情况下，你需要解决的问题更像是“做这个，或做那个，或做那个，或做那个……”对于这些问题，你需要使用第三种形式的`if`陈述，即`if-else if` 陈述。

# if-else if 语句

`if-else if`语句允许你测试几个相关的条件，让你解决比其他两个`if`语句形式更复杂的问题。下面是`if-else if`语句的语法模板:

*if(条件){
语句；
}
else if(condition){
语句；
}
else if(condition){
语句；
}
……
else {
语句；
}*

省略号表示如果需要，可以有更多的`else if` 子句，没有数量限制。

语句末尾的`else`子句是可选的，这样，如果没有一个条件评估为真，您可以提供另一个选项。

让我们看一个如何使用`if-else if`语句的经典例子。您是一名教师，您的工作是按照标准评分标准将字母分数分配给数字分数:

90–100:A
80–89:B
70–79:C
60–69 :D
0–59:F

下面是一个程序，它将为考试分数指定一个字母等级:

```
int main()
{
  int score;
  string letterGrade;
  cout << "Enter a numeric score: ";
  cin >> score;
  if (score >= 90) {
    letterGrade = "A";
  }
  else if (score >= 80) {
    letterGrade = "B";
  }
  else if (score >= 70) {
    letterGrade = "C";
  }
  else if (score >= 60) {
    letterGrade = "D";
  }
  else if (score >= 0) {
    letterGrade = "F";
  }
  else {
    letterGrade = "Unknown";
  }
  cout << "A score of " << score << " equals a letter grade of "
       << letterGrade << "." << endl;
  return 0;
}
```

下面是该程序几次运行的输出:

```
Enter a numeric score: 90
A score of 90 equals a letter grade of A.Enter a numeric score: 71
A score of 71 equals a letter grade of C.Enter a numeric score: -10
A score of -10 equals a letter grade of Unknown.
```

最后一次程序运行演示了当用户输入的等级不在条件测试的限制范围内时会发生什么情况。

# 对于这些如果，没有“但是”或“但是”

这是 C++中 if 语句的三种形式。这些将会处理你所有的分支问题，尽管也有其他方法不使用`if`语句来实现分支。在以后的文章中，我将讨论如何使用表驱动的处理来代替一长串的`if-else if`语句。

我还将使用另一篇文章来介绍做出更复杂的决策，这些决策需要更多的操作符，而不仅仅是关系操作符。例如，如果你是一名信贷员，你有一条规定，如果某人的年薪超过 20000 美元，并且他们已经做同样的工作超过 3 年，你将向他提供一份工作。您需要一个布尔操作符来完成这项工作，我将在以后的文章中介绍这些操作符。

感谢您的阅读，请给我发电子邮件提出意见和建议。