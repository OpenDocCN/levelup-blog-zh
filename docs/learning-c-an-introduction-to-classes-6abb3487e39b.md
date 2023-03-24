# 学习 C++:类的介绍

> 原文：<https://levelup.gitconnected.com/learning-c-an-introduction-to-classes-6abb3487e39b>

![](img/0df4fe2bb25eb6052c0c36655d62fd82.png)

照片由[埃米尔·佩伦](https://unsplash.com/@emilep?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在 C++中，创建对象以进行面向对象编程的主要方法是类。类是一组变量(称为成员变量)和函数(称为成员函数)，它们定义了现实世界(或抽象)对象的属性和行为。在这篇文章中，我将介绍在 C++中创建类对象的基础知识。

# 对象概述

面向对象编程是作为更好地模拟现实世界的一种手段而开发的。在现实世界中，我们希望通过计算机程序解决的问题涉及到被称为名词的事物。例如，一个工资单程序将有雇员、工资单、薪水册等。所有这些都是我们可以识别为物体的东西。

这些对象有定义它们的属性和描述它们做什么的行为。例如，雇员有姓名、工作职位、年龄等。工资单程序的员工行为可以是他们在一个支付周期内工作的小时数。

C++类背后的主要概念是，我们可以将一个事物的属性和该事物的行为组合成一个单一的实体，即*类*。另一个基本概念是可以隐藏对象的属性，以便可以控制对这些属性的访问，从而管理存储在对象中的数据。

员工的年龄就是一个例子。通常，我们会将年龄存储为整数。然而，在大多数 C++编译器中，整数的范围可以从大约-20 亿到+20 亿。所以我们可以把 1723 分配给一个人的年龄，根据编译器的规则，这是一个有效的年龄。但当然不是，所以我们可以控制程序如何与年龄属性交互，这样就可以只输入一个人的合法年龄。

以这种方式隐藏数据的能力是 OOP 数据封装概念的一部分。

除了数据，我们还可以使用数据封装来隐藏对象的行为(成员函数)。例如，我们的工资程序将需要计算一个雇员的净工资，但是我们不想将这种行为暴露给用户来利用。所以我们可以隐藏成员函数，这样它们就不是对象用户可访问的接口的一部分。我们可以有一个名为 `calculatePayCheck`的成员函数，但是它只调用另一个成员函数，这个成员函数不属于可从对象调用的成员函数集。

这是足够高层次的解释了。让我们认真研究一下具体细节，学习如何用 C++创建类。

# 创建一个类

我将演示如何通过定义一个跟踪日期的类来创建一个类。当创建一个类时，你要做的第一件事就是在编译器的全局空间中给这个类起一个名字:

```
#include <iostream>
#include <vector>using namespace std;class Date {};int main ()
{ return 0;
}
```

注意，类定义的右花括号后面有一个分号。这是必需的，如果忽略，将导致错误消息。

下一步是定义存储类数据的成员变量。对于日期，我们希望存储月、日和年，因此这些成为我们的成员变量。我们希望对用户隐藏这些数据，所以我们指定将成员变量声明放在一个`private` 访问节中，如下所示:

```
class Date {
private:
  int month, day, year;};
```

一旦确定了成员变量，下一步就是创建组成类接口的成员函数。我们可以为一个`Date`类定义许多不同的成员函数，但是现在让我们只定义一个以标准月/日/年格式显示日期的函数。

作为我们类的用户可以访问的接口的一部分的成员函数被放在类定义的`public` 部分。函数也可以放在`private`部分，但是类的用户不能访问这些函数，这些函数通常被称为帮助函数，因为它们帮助其他的`public`成员函数完成它们的工作。

下面是一个`display`成员函数的定义:

```
class Date {
private:
  int month, day, year;public:
  void display() {
    cout << month << "/" << day << "/" << year;
  }
};
```

这些是非常简化的定义，我将在以后的文章中改进它们。

让我们看看此时完整的`Date`类定义是什么样子的:

```
class Date {
private:
  int month, day, year;public:
  void display() {
    cout << month << "/" << day << "/" << year;
  } void setMonth(int m) {
    month = m;
  } void setDay(int d) {
    day = d;
  }

  void setYear(int y) {
    year = y;
  }
};
```

我可以用这个定义做更多的事情，但是现在让我们看看如何在程序中使用这个`Date`类。

# 创建类对象

一旦定义了类，就可以在程序中使用它了。使用与创建变量相同的语法来创建类对象。下面是创建类对象的语法模板:

*类名对象名(参数列表)；*

当您为您的类定义了一个或多个构造函数时，将使用参数列表。我将在下一篇文章中讨论构造函数。

下面是如何创建一个`Date`类对象:

```
Date today;
```

today 对象现在是`Date`类的*实例*，创建新类对象的行为被称为*实例化*。

下一步是使用成员函数向对象添加一些数据。下面是实现这一点的代码片段:

```
today.setMonth(8);
today.setDay(23);
today.setYear(2020);
```

现在我们可以显示刚刚设置的日期:

```
today.display(); // displays 8/23/2020
```

下面是完整的程序和类定义:

```
class Date {
private:
  int month, day, year;public:
  void display() {
    cout << month << "/" << day << "/" << year;
  } void setMonth(int m) {
    month = m;
  } void setDay(int d) {
    day = d;
  } void setYear(int y) {
    year = y;
  }
};int main ()
{
  Date today;
  today.setMonth(8);
  today.setDay(23);
  today.setYear(2020);
  today.display();
  return 0;
}
```

以下是该程序的输出:

```
8/23/2020
```

我对输出不满意，因为我们希望格式为 mm/dd/yyyy，而月份显示的只是一个位数，因为月份数小于 10。解决这个问题的一个方法是编写一个私有函数，检查它的参数有多少位，并相应地调整日期位数。

下面是这种函数的一个定义:

```
string adjustDate(int d) {
  string date;
  if (d < 10) {
    date = "0" + to_string(d);
  }
  else {
    date = to_string(d);
  }
  return date;
}
```

下面是新的完整的类定义:

```
class Date {
private:
  int month, day, year; string adjustDate(int d) {
    string date;
    if (d < 10) {
      date = "0" + to_string(d);
    }
    else {
      date = to_string(d);
    }
    return date;
  }public:
  void display() {
    string date = adjustDate(month) + "/" + adjustDate(day)
                  + "/" + adjustDate(year);
    cout << date;
  } void setMonth(int m) {
    month = m;
  } void setDay(int d) {
    day = d;
  } void setYear(int y) {
    year = y;
  }
};
```

以下是该程序的输出:

```
08/23/2020
```

# 课堂上还有很多东西

本文只是对 C++面向对象编程的一个基本介绍。在我的下一篇文章中，我将讨论如何创建和使用构造函数，这使得实例化新的类对象更加容易和有效，因为在实例化新的类对象时，可以将数据分配给成员变量。

感谢您的阅读，请发送电子邮件至[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)向我提出意见和建议。如果你对我的在线编程课程感兴趣，请访问 https://learningcpp.teachable.com。