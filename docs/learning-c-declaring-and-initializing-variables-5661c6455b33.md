# 学习 C++:声明和初始化变量

> 原文：<https://levelup.gitconnected.com/learning-c-declaring-and-initializing-variables-5661c6455b33>

![](img/cb6fcdc8ddcafbc77137b3d3484a6f9b.png)

由[凯利·西克玛](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

学习如何使用变量是新 C++程序员应该学习的第一项技能。关于变量是如何声明(创建)和初始化(给定值)的，你需要知道几件事，我将在本文中涉及这些。

# 命名变量的规则

C++有一些命名变量的规则。第一条规则是变量名必须以字母字符开头。这个字符通常是小写的，因为以大写字母开头的名称是为类名保存的。这是惯例而不是规则，但无论如何你都应该遵守。

在最初的字母字符之后，变量可以包含更多的字母、数字和下划线字符。变量名不能包含符号或空格。

另一个 C++变量命名约定，但不是规则，是变量名应该有意义。变量名`salary`比变量名`s`有意义得多。遵循这一约定会使您的程序更容易阅读和调试。

# 声明没有值的变量

第一种变量声明类型是创建一个没有值的变量。通过指定变量将保存的数据类型并给变量命名来声明变量。下面是声明变量的语法模板:

*数据类型变量名；*

以下是一些例子:

```
int salary;
double ratio;
string firstName;
bool flag;
```

如果您想要声明同一类型的多个值，您可以将它们放入一个语句中，用逗号分隔变量。以下是在一条语句中声明多个变量的语法模板:

*数据类型变量名 1，变量名 2，变量名 3，…；*

省略号表示列表可以继续。

以下是一些例子:

```
int grade1, grade2, grade3;
double tax_rate1, tax_rate2;
string firstName, middleName, lastName;
```

# 声明和初始化变量

有时候你既想声明一个变量，又想给它一个初始值(初始化)。在 C++中，有几种方法可以做到这一点。

声明和初始化变量的第一种方法是在声明语句中给变量赋值。以下是这项技术的语法模板:

*数据类型变量名=表达式；*

赋值运算符右侧的表达式可以是文字或其他类型的表达式，如算术或字符串表达式。以下是一些例子:

```
int salary = 40000;
double ratio = 0.01 * .14;
string aBeatle = "Paul " + "McCartney";
bool flag = false;
```

您可以使用这种技术在同一个语句中声明和初始化变量。以下是一些例子:

```
int grade1 = 89+5, grade2 = 77+5, grade3 = 81;
string aBeatle = "John Lennon",
       anotherBeatle = "George Harrison";
bool flag1 = false, flag2 = true;
```

# 可选的声明和初始化方法

随着 C++发展到 C++11 和更高版本，声明和初始化变量的新方法已经出现。一种方法是使用函数运算符将变量初始化为一个值。例如:

```
int number(22);
string greeting("Hello.");
double ratio(0.25 * 1.1);
```

另一种初始化方法是使用花括号，就像你在初始化列表中使用的一样，但是用一个值或表达式代替，如下例所示:

```
int grade1{81};
string address{"1000 W. Scenic"};
double salary{25250.50 * 2};
```

当使用初始值设定项时，可以执行扩大转换，但不能执行收缩转换。所以，你可以把一个整数值赋给一个浮点变量，但是你不能把浮点值赋给一个整型变量。这里有两个例子:

```
double salary {25000}; // allowed
int salary {35250.50}; // not allowed
```

如果您选择，可以将赋值运算符用于初始值设定项:

```
double salary = {25000};
```

# 用初始值设定项分配默认值

如果你给一个变量赋值一个空的初始化器，这个变量将被赋予这个数据类型的默认值。以下是一些例子:

```
int number{}; // assigned 0
double ratio{}; // assigned 0.0
string name{}; // assigned the empty string ""
bool flag{}; // assigned 0 or false
```

从 C++11 开始，就可以使用初始化器进行变量赋值了。

# 使用 auto 分配数据类型

从 C++11 开始，您可以使用`auto`关键字让编译器自动为变量分配数据类型。它通过检查赋值右边的表达式的类型并将该类型赋给变量来实现这一点。

这种类型的变量初始化的语法模板是:

*汽车变量名=表达式；*

以下程序演示了`auto`关键字是如何工作的:

```
#include <iostream>
#include <typeinfo>
using namespace std;int main () {
  auto salary = 38500;
  auto flag = false;
  auto pi = 3.14159;
  cout << "The data type of salary is: "
       << typeid(salary).name() << endl;
  cout << "The data type of flag is: "
       << typeid(flag).name() << endl;
  cout << "The data type of pi is: "
       << typeid(pi).name() << endl;
  return 0;
}
```

这个程序的输出是:

```
The data type of salary is: i
The data type of flag is: b
The data type of pi is: d
```

其中“I”代表 int，“b”代表 bool，“d”代表 double。

然而，现在我已经向您展示了如何使用 auto，您不应该在我在这里展示的例子中使用它。更好地使用`auto`是因为当输入数据类型很麻烦，或者语句非常复杂，直到运行时才能知道数据类型。

什么时候应该使用`auto`的一个例子是当你声明一个迭代器变量，并且不想键入该类型的全名时。

# 变量声明选择

正如您所看到的，有几种选择可以用来声明变量。我让我所有的初学者使用需要赋值操作符的标准方法，尽管当他们进入更高级的课程时，他们可以选择他们最喜欢的方法。你很可能会选择你最习惯的类型，或者是你工作的标准。

像大多数 C++特性一样，选择是多种多样的，但是它们的使用通常取决于你正在编写的程序的类型。

感谢您的阅读，请给我发电子邮件提出意见和建议。