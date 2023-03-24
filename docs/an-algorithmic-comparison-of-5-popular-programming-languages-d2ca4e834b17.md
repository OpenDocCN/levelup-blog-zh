# 5 种流行编程语言的算法比较

> 原文：<https://levelup.gitconnected.com/an-algorithmic-comparison-of-5-popular-programming-languages-d2ca4e834b17>

![](img/1ee651f694f3a257655ebdc6db950214.png)

[拉多万·纳基夫·雷汉](https://unsplash.com/@radowanrehan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在本文中，我将使用唐纳德·克努特(Donald Knuth)首次为一篇论文开发的算法来比较五种现代计算机编程语言(C、C++、Java、JavaScript 和 Python)，这篇论文是他和路易斯·特拉布·帕尔多(Luis Trabb Pardo)一起写的，内容是关于编程语言的早期发展。

Knuth 将该算法命名为 TPK (Trabb Pardo Knuth)算法。下面是算法的伪代码:

*从用户处获取 11 个数字
对于从最后一个元素开始的序列中的每个项目:
以该项目为参数调用函数 F
显示函数返回值*

函数 F 的定义如下:

*函数 F(number):
返回 number +的绝对值的平方根(5 次
数的 3 次幂)*

最初的 TPK 算法也检查了接收函数返回值的变量的溢出，但是我把它去掉了，因为它不像克努特在 1977 年写文章时那样相关。此外，检查溢出并没有真正增加我对各种编程语言的比较。

该算法为程序员提供了演示编程语言以下特征的机会:

*   数据类型
*   数据存储
*   函数定义
*   重复

这些都是学习任何编程语言的学生在课程的第一个学期要学习的特征。

我将使用[的语言系统 https://repl.it](https://repl.it) 来执行我的编程语言比较。使用这个网站可以让我保持编程环境尽可能的恒定，同时在不同的语言之间切换进行比较。

让我们从在 c 语言中实现 TPK 算法开始。

# C 语言中的 TPK 算法

下面是 TPK 算法的一个实现，以及一个测试程序，用 C:

```
#include <stdio.h>
#include <math.h>double f(double num) {
  return sqrt(fabs(num) + (5 * pow(num, 3)));
}int main(void) {
  const int N = 11;
  double numbers[N];
  for (int i = 0; i < N; i++) {
    printf("Enter a number: ");
    scanf("%d", &numbers[i]);
  }
  for (int j = N-1; j >= 0; j--) {
    double result = f(numbers[j]);
    printf("%d\n", result);
  }
  return 0;
}
```

当然，任何学过使用 C 风格语法的语言(如 Java、C#和 JavaScript)的程序员都很容易读懂这个程序。这个程序中唯一不熟悉的部分是使用`scanf`函数的输入行。

有些程序员可能不熟悉`printf` 函数，所以你可能也需要对它做一些研究。

一般来说，我要比较的许多语言主要在它们如何执行输入和输出以及如何处理数据结构方面有所不同，尤其是类似 C 的语法语言。

现在让我们看看这个程序在 C 的姐姐语言 C++中是什么样子的。

# C++中的 TPK 算法

下面是 TPK 算法的一个实现和一个 C++测试程序:

```
#include <iostream>
#include <cmath>
using namespace std;double f(double num) {
  return sqrt(fabs(num) + (5 * pow(num, 3)));
}int main() {
  const int N = 11;
  double numbers[N];
  for (int i = 0; i < N; i++) {
    cout << "Enter a number: ";
    cin >> numbers[i];
  }
  for (int i = N-1; i >= 0; i--) {
    double result = f(numbers[i]);
    cout << result << endl;
  }
}
```

这个程序和我之前写的 C 程序之间的主要区别在于输入和输出是如何处理的。但还有另一个更微妙的区别。在上面的 C 程序中，第二个`for`循环必须用与第一个`for`循环不同的索引变量来编写，而在 C++版本中，我可以对两个循环使用相同的索引变量。这是因为 C++有块级作用域，而 C 没有。

现在让我们用 Java 运行 TPK 算法。

# Java 中的 TPK 算法

在 Java 中运行 TPK 算法和在 C++中运行该算法之间有一些不同，尽管这些不同主要是语法上的不同。你可以看看 Java 程序，看它的轮廓和 C++程序相似。下面是 Java 代码:

```
import java.util.Scanner;
import java.lang.Math;class Main {
  static double f(double number) {
    return Math.sqrt(Math.abs(number) + (5 * Math.pow(number, 3)));
  } public static void main(String[] args) {
    final int N = 11;
    double [] numbers = new double[N];
    Scanner userInput = new Scanner(System.in);
    for (int i = 0; i < N; i++) {
      System.out.print("Enter a number: ");
      numbers[i] = userInput.nextDouble();
    }
    userInput.close();
    for (int i = N-1; i >= 0; i--) {
      double result = f(numbers[i]);
      System.out.println(result);
    }
  }
}
```

最明显的区别在于主程序是一个类。Java 中的一切都是一个对象，所以即使是一个命令式程序也必须作为一个类来编写。这意味着`f`函数必须写成静态方法，因为它是从一个类中调用的。我使用术语*方法*，尽管`f`实际上只是一个函数，因为它并不真正附属于一个类对象。

Java 中没有关键字`const`；常数用`final`声明。数组在 Java 中是一个对象，所以它必须用构造函数调用来实例化。

Java 程序比前两个程序长几行。一个原因是`Scanner`类比 C++中的`iostream`库需要使用更多的编码。例如，我必须实例化一个`Scanner`对象，并在完成后关闭它，但是在 C++中，我只需要使用`cout`而不需要所有的实例化和关闭。

这些都是主要的区别，但是如果你以前从未见过 Java 程序，但是知道 C 或 C++，你应该可以理解这个程序的功能。

现在让我们从静态类型的编译语言转换到两种动态类型的解释语言，JavaScript 和 Python，看看 TPK 程序在这些语言中与 C、C++和 Java 有什么不同。我将从 JavaScript 开始。

# JavaScript 中的 TPK 算法

对于这个测试，我没有使用 repl.it 中的 JavaScript 语言，因为该站点使用 Node.js，并且 Node.js 中的用户输入比我在本文中想要的更复杂。我使用的是 Mozilla 的 Spidermonkey 版本的 JavaScript，它提供了一种更简单的用户输入方式。

乍一看，用 JavaScript 实现并测试 TPK 算法看起来与我们的 C、C++和 Java 版本没有太大区别，因为 JavaScript 借用了太多 C 语法。以下是 JavaScript 代码:

```
function f(number) {
  return Math.sqrt(Math.abs(number) + (5 * Math.pow(number, 3)));
}let numbers = []; // let numbers = new Array();
const N = 11;
for (let i = 0; i < N; i++) {
  putstr("Enter a number: ");
  numbers[i] = parseFloat(readline());
}
for (let i = N-1; i >= 0; i--) {
  let result = f(numbers[i]);
  print(result);
}
```

首先要注意的是没有显式类型。JavaScript 在幕后处理所有的输入。空数组可以用空方括号声明，尽管我也可以这样写:

`let numbers = new Array();`

我可能会在更正式的程序中这样做。

还要注意，我不必导入数学库。我可以在需要的地方引用 JavaScript 的`Math`库，这使得程序更短一些。事实上，这个程序的 Java 版本有 21 行代码，不包括空格，而 JavaScript 版本只有 13 行代码。我并不是说简洁是最重要的因素，但是这个例子突出了使用解释型语言比编译型语言的优势之一。

现在让我们转到最后一种编程语言——Python。

# Python 中的 TPK 算法

Python 将是所有这些语言中最不同的，因为 Python 不使用 C 语言语法。许多人吹捧这是将 Python 作为第一语言学习的一个优势，我不会在这里争论这一点，除了说如果你计划成为一门专业语言，你最终将不得不学习一门使用 C 语法的语言，你可以争辩说，当你转向 C++或 Java 时，在诸如 JavaScript 之类的语言中早期学习它会给你带来优势。

不过，让我们继续，看看 TPK 算法及其测试程序是如何用 Python 实现的。代码如下:

```
import mathdef f(number):
  return math.sqrt(math.fabs(number) + (5 * math.pow(number, 3)))numbers = []
N = 11
for i in range(0, N):
  numbers.append(float(input("Enter a number: ")))
for i in range(N-1, -1,-1):
  print(f(numbers[i]))
```

由于语法上的差异，Python 版本看起来与其他版本有些不同。语法上的差异也会导致程序更短。与 Java 版本的 21 行代码相比，Python 程序只使用了 9 行代码。

在 Python 中，数组被称为列表，虽然 Python 列表允许对列表元素进行索引访问，但它不允许索引赋值，所以 append 方法用于向列表添加数字。

Python 中没有常量，所以`N`只是被赋了一个值。Python 通常将常用的常量(如`PI`)放在模块中，这样它们可以被使用但不能被更改。

`for`Python 中的循环看起来有点不同，因为它们使用`range`函数来指定循环控制变量的起始值和终止值。

Python 对循环块使用缩进而不是花括号，冒号用于标记块的开始。当人们说 Python 没有太多语法时，我反驳说 Python 只是有不同的语法。编程语言必须有语法，否则就无法解析。

# 结论

我写这篇文章的目的并不是要将这些语言中的一种提升到另一种之上，而是使用一种可以在每种语言中表达的算法来指出它们之间的相似之处和不同之处。

我将让您选择一种最喜欢的语言，或者决定哪种语言最适合这种类型的编程。在以后的文章中，我将着眼于其他算法进行比较，比如字符串算法和最好用对象解决的问题。

感谢您的阅读，请回复这篇文章或发邮件给我，告诉我您的意见和建议。