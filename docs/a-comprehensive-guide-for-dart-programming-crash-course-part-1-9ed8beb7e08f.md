# Dart 编程综合指南:速成班(第一部分)

> 原文：<https://levelup.gitconnected.com/a-comprehensive-guide-for-dart-programming-crash-course-part-1-9ed8beb7e08f>

![](img/9ef714703b70bedf0412401cd2ecf65b.png)

由[阿里安·达尔维什](https://unsplash.com/@arianismmm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 开始使用 Dart 编程语言所需要知道的一切。

大多数时候，我们在 youtube 或其他网站上花费数小时观看内容，而我们想要的只是开始学习该语言。如果你想成为一名更好的开发者，那么你需要学习所有的基本概念，但是如果你想尽可能快地构建东西，那么你需要的只是一个速成班。

**为什么要学习 Dart 编程？**

Dart 是一种相对简单、现代且高效的编程语言。Dart 是一种客户端优化的编程语言，适用于多种平台上的应用程序。Dart 由 Google 开发，用于构建移动、桌面、服务器和 web 应用程序。Dart 是一种面向对象、基于类、垃圾收集的语言，具有 C 风格的语法。Dart 可以编译成本机代码或 JavaScript。

# 我们将在所有部分涵盖的主题:

1.  Hello World(学习编程语言需要的一切😅)
2.  评论
3.  接受输入
4.  数据类型
5.  经营者
6.  条件语句
7.  环
8.  功能
9.  异常处理
10.  收集
11.  面向对象编程

# 我们将在这一部分讨论的主题:

1.  你好世界
2.  评论
3.  接受输入
4.  数据类型
5.  经营者

# 1.你好世界:

Main 是一个特殊的函数， *required* ，是应用程序开始执行的顶级函数。每个 app 都必须有一个顶级的`main()`函数，作为 app 的入口点。

所以，不浪费时间，让我们写我们的 hello world 程序。

```
void main() {
  print(“Hello World”);
}
```

# 2.评论

程序中的注释就像是所有将要阅读该程序的开发人员的笔记。

注释只对编译时的人有用，编译器会忽略它。

```
void main() {
  //it's single line comment
  /* Multiple
     Line
     Comment
  */
  print(“Hello World”);
}
```

# 3.接受输入

您可以使用 readLineSync()函数通过控制台从**用户**进行标准的**输入**，为此，您必须从 dart 导入一个库 **dart** :io。

```
import ‘dart:io’;void main(List<String> args) {
  stdout.write(“What’s your name: “);
  String name = stdin.readLineSync();
  print(name);
}
```

# 4.数据类型

在深入研究数据类型之前，我们先来谈谈变量。

## 变量

变量是对计算机内存位置的引用或符号名。简单来说，就像你小学的数学方程式，比如 x = 5，我们在 x 中存储了 5，我们可以根据需要改变它的值。例如，我们可以将它的值替换为 x = 'name '或任何我们想要的值。

**声明变量的一些规则:**

*   变量可以是小写字母(A 到 Z)、大写字母(A 到 Z)、数字(0 到 9)或下划线(_)的组合。
*   变量不能以数字开头。1 变量是无效的，但是变量 1 是完全正确的。
*   关键字不能用作变量名。

## 数字

Dart 中有两种类型的数字:

1.  **int:**

在 Dart 中，int 的值可以从-263 到 263–1。

2.**双精度:**

IEEE 754 标准规定的 64 位(双精度)浮点数。

```
*void main() {
  // Numbers: int and double* int age = 22;
  double num = 10.5;
  print(age + num);
}
```

## 线

Dart 字符串是一系列 UTF-16 代码单元。您可以使用单引号或双引号来创建字符串:

您可以使用`${*expression*}`将表达式的值放入字符串中。如果表达式是标识符，可以跳过{}。

```
void main() {
  *// String and String iterpolation* String name = “Rajesh Berwal”;
  print(‘My nname is $name and the length of the name with space is ${name.length}’);
}
```

## 布尔代数学体系的

为了表示布尔值，Dart 有一个名为`bool`的类型。只有两个对象具有 bool 类型:布尔文字`true`和`false`

```
void main() {
  *// Boolean* bool isWorking = true;
  print(“Are you working: ${isWorking}”);
  bool notWorking = false;
  print(“Are you working: ${isWorking}”);
}
```

## 常量和最终值

如果你不想改变变量，使用`final`或`const.`

```
void main() {
  *// Creating Constants
  /*
   -> final variable is only initialized when it’s accessed
   -> const variable is initialized at the compile time
   -> main difference const keyword get memory location at the compile time
   -> but final variable only when it is accessed
  */* const daysInLeapYear = 366;
  print(‘Days in leap year: $daysInLeapYear’);final pi = 3.14;
  print(pi);
}
```

## 动态声明变量

没有静态类型声明的变量被隐式声明为动态的。

```
*void main() {
  // We can also declare variable dynamically* var something = “Somewhere”;
  print(something);
  var num = 5;
  print(num);
}
```

# 5.操作员:

## 算术运算符

Dart 支持常用的算术运算符，如下例所示:

```
void main() {
  print(2 + 3 == 5);
  print(2 - 3 == -1);
  print(2 * 3 == 6);
  print(5 / 2 == 2.5); // Result is a double
  print(5 ~/ 2 == 2); // Result is an int
  print(5 % 2 == 1); // Remainder print('5/2 = ${5 ~/ 2} r ${5 % 2}' == '5/2 = 2 r 1');
}
```

## 比较(关系)运算符

下面是使用每个等式和关系运算符的示例:

```
void main() {
  print(2 == 2);
  print(2 != 3);
  print(3 > 2);
  print(2 < 3);
  print(3 >= 3);
  print(2 <= 3);
}
```

## 逻辑(布尔)运算符

您可以使用逻辑运算符反转或组合布尔表达式。

```
!*expr* inverts the following expression (changes false to true, and vice versa)||               logical OR&&               logical AND
```

下面是一个使用逻辑运算符的示例:

```
if (!done && (col == 0 || col == 3)) {
  // ...Do something...
}
```

## 按位运算符

您可以在 Dart 中操作数字的各个位。通常，您会对整数使用这些按位和移位运算符。

```
&      AND
|      OR
^      XOR
~      *expr*Unary bitwise complement (0s become 1s; 1s become 0s)
<<     Shift left
>>     Shift right
```

下面是一个使用按位和移位运算符的示例:

```
void main() {
  final value = 0x22;
  final bitmask = 0x0f;print((value & bitmask) == 0x02); // AND
  print((value & ~bitmask) == 0x20); // AND NOT
  print((value | bitmask) == 0x2f); // OR
  print((value ^ bitmask) == 0x2d); // XOR
  print((value << 4) == 0x220); // Shift left
  print((value >> 4) == 0x02); // Shift right
}
```

## 赋值运算符

正如您已经看到的，您可以使用`=`操作符赋值。如果只在被赋值变量为空时赋值，使用`??=`操作符。

```
// Assign value to a
a = value;
// Assign value to b if b is null; otherwise, b stays the same
b ??= value;
```

像`+=`这样的复合赋值操作符把操作和赋值结合起来。

```
=    –=   /=    %=    >>=   ^=   +=    *=   ~/=     <<=   &=      |=
```

以下示例使用赋值运算符和复合赋值运算符:

```
void main() {
  var a = 2; // Assign using =
  a *= 3; // Assign and multiply: a = a * 3
  print(a == 6);
}
```

接受我的建议，如果你想成为一名更好的开发人员，那么就从编程的初始阶段开始，因为不同编程语言的语法会发生变化，但概念是相同的。速成课程旨在帮助您开始使用 dart 编程语言，以便您可以更快地创建应用程序。为了更好的理解，学习那种语言的内在工作。

为了更好地理解程序的时间复杂性，您可以查看我们的文章:

[](https://medium.com/swlh/a-comprehensive-guide-for-time-complexity-and-big-o-notation-4ebef201dfc8) [## 时间复杂性和大 O 符号的综合指南

### 大 O 是一种语言，我们用它来描述一个算法的复杂性，意味着它可以有多快或多慢。不是…

medium.com](https://medium.com/swlh/a-comprehensive-guide-for-time-complexity-and-big-o-notation-4ebef201dfc8)