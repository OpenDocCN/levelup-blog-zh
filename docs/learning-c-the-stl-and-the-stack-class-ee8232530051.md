# 学习 c++:STL 和堆栈类

> 原文：<https://levelup.gitconnected.com/learning-c-the-stl-and-the-stack-class-ee8232530051>

![](img/9767fc46326a1b3d0d472a4b707945ad.png)

照片由[布鲁克·拉克](https://unsplash.com/@brookelark?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

stack 类提供了一个非常专门化的容器接口。堆栈是一种后进先出(LIFO)容器，它具有最少数量的成员函数，您可以使用这些函数来操作存储在堆栈中的数据。在计算机科学和计算机编程的几个领域中，堆栈扮演着重要的角色。本文将讨论如何在 C++编程中使用堆栈，并演示一些使用堆栈的应用程序。

# 堆栈概述

堆栈是一个容器，所有数据从容器的顶部进出。您可以在堆栈上执行的三个主要操作是:1) *将新数据推送到堆栈上*；2) *弹出*栈顶数据元素；3)查看堆栈顶部的*和*。

如果你想想一个现实生活中的推论，想想自助餐厅的托盘。当顾客走进自助餐厅，拿到一个托盘时，他们会把托盘从堆叠的顶部拿走。当洗碗机清洗托盘并将其放回原处时，托盘会放在托盘堆的顶部。当您查看托盘堆栈时，您可以完全看到的唯一托盘是顶部托盘。

正如我前面提到的，堆栈被认为是一个后进先出的容器，因为推到堆栈顶部的最后一个元素是从堆栈顶部弹出的第一个元素。

# 声明堆栈

stack 类的头文件是:

```
#include <stack>
```

`stack`类是一个模板类，因此您必须在声明中提供一个数据类型，如下例所示:

```
stack<int> numbers;
stack<string> people;
stack<double> ratios;
stack<char> operators;
```

你不能用初始化列表来初始化一个栈，也不能像其他容器一样指定一个初始容量作为构造函数的参数。

# 向堆栈添加数据并查看堆栈顶部

只有一种方法可以将数据添加到堆栈中——`push`函数。`push`函数获取一个元素并将其存储在堆栈的顶部。栈顶也是栈中唯一可以访问的元素。这个类有一个检查栈顶的函数——`top`函数。

下面是一个简短的程序，它将一些数据推送到堆栈上，然后检查堆栈的顶部:

```
#include <iostream>
#include <stack>
using namespace std;int main()
{
  stack<int> numbers;
  numbers.push(2);
  numbers.push(4);
  numbers.push(8);
  cout << "top of stack: " << numbers.top() << endl;  // 8
  return 0;
}
```

这个程序的输出是:

```
top of stack: 8
```

# 从堆栈中删除数据

使用`pop`函数从堆栈中移除数据。这个函数只能移除栈顶的元素。正如我前面提到的，除了顶部的元素，栈上没有其他元素是可访问的。

下面是一个从堆栈中弹出数据的示例，在每次弹出后检查堆栈的顶部:

```
int main()
{
  stack<int> numbers;
  numbers.push(2);
  numbers.push(4);
  numbers.push(8);
  cout << "top of stack: " << numbers.top() << endl;
  // top of stack: 8
  numbers.pop();
  cout << "top of stack: " << numbers.top() << endl;
  // top of stack: 4
  numbers.pop();
  cout << "top of stack: " << numbers.top() << endl;
  // top of stack: 2
  numbers.pop();
  cout << "top of stack: " << numbers.top() << endl;
  // this line does not execute
  return 0;
}
```

最后一个`cout`语句没有执行，因为我试图弹出一个空堆栈。您可以使用一个函数`empty`，它可以让您知道堆栈是空的还是仍然有数据。让我们修改上面的程序，利用`empty`功能:

```
int main()
{
  stack<int> numbers;
  numbers.push(2);
  numbers.push(4);
  numbers.push(8);
  while (!numbers.empty()) {
    cout << "top of stack: " << numbers.top() << endl;
    numbers.pop();
  }
  return 0;
}
```

另一个可以用来帮助确定堆栈何时为空的函数是`size`函数。这个函数返回堆栈中元素的数量。下面是如何使用它来防止弹出一个空堆栈:

```
int main()
{
  stack<int> numbers;
  numbers.push(2);
  numbers.push(4);
  numbers.push(8);
  while (numbers.size() > 0) {
    cout << "top of stack: " << numbers.top() << endl;
    numbers.pop();
  }
  return 0;
}
```

# 一些堆栈应用程序

堆栈可以用于一些有趣的算法和应用程序。以下是其中的几个。

# 将十进制数转换为二进制数

我将讨论的第一个算法是将基数为 10(十进制)的数字转换为基数为 2(二进制)的数字。以下是算法 w，其中 n 是十进制数，B 是基数(2):

*0。* *从空栈开始。*

*1。* *取 n 乘 n % B 的最右位，将结果压入堆栈。*

*2。* *将 n 替换为 n/b*

*3。* *重复步骤 1 和 2，直到 n = 0。*

*4。* *从堆栈中弹出并打印数字，产生二进制数。*

下面是实现该算法的程序:

```
int main()
{
  stack<int> binDigits;
  int decNumber;
  cout << "Enter a decimal number: ";
  cin >> decNumber;
  int number = decNumber;
  const int BASE = 2;
  int digit;
  while (decNumber > 0) {
    digit = decNumber % BASE;
    binDigits.push(digit);
    decNumber /= BASE;
  }
  string binNumber = "";
  while (!binDigits.empty()) {
    binNumber += to_string(binDigits.top());
    binDigits.pop();
  }
  cout << number << " is " << binNumber
       << " in binary." << endl;
  return 0;
}
```

下面是这个程序的一次运行:

```
Enter a decimal number: 53
53 is 110101 in binary.
```

# 寻找回文

如果一个单词正反拼写相同，那么这个单词就是回文。雷达和赛车这两个词就是回文的例子。我们可以使用堆栈来确定一个单词是否是回文，方法是将一个单词的每个字母推到堆栈上。然后，我们可以通过将堆栈弹出到另一个单词中，直到堆栈为空，来形成该单词的反转。如果原词和由栈形成的词相等，那么这个词就是回文。

下面是一个检查单词是否是回文的程序:

```
int main()
{
  stack<char> letters;
  string word;
  cout << "Enter a word to check: ";
  cin >> word;
  for (unsigned i = 0; i < word.size(); i++) {
    letters.push(word[i]);
  }
  string revWord = "";
  while (!letters.empty()) {
    revWord += letters.top();
    letters.pop();
  }
  if (revWord == word) {
    cout << word << " is a palindrome." << endl;
  }
  else {
    cout << word << " is not a palindrome." << endl;
  }
  return 0;
}
```

以下是该程序的一些运行情况:

```
Enter a word to check: radar
radar is a palindrome.Enter a word to check: racecar
racecar is a palindrome.Enter a word to check: railcar
railcar is not a palindrome.
```

我们习惯写的算术语句都是用中缀写的，比如: *(9.0/5.0) * 100 + 32.0* 。但是，如果不能直接依赖正常的操作顺序，就必须用括号来表示正确的操作顺序。另一方面，后缀表达式可以不用括号，并且保持正确的操作顺序。

让我们构建一个简单的中缀到后缀的转换器，它适用于不包含括号的算术语句，比如 *a+b*c* 。对于这个转换器，我们将根据优先级将算术运算符推到堆栈上，并将标识符放到后缀语句上。

程序如下:

```
#include <iostream>
#include <stack>
using namespace std;int getPrecedence(char op) {
  if (op == '+' || op == '-') {
    return 1;
  }
  else if (op == '*' || op == '/') {
    return 2;
  }
  else if (op == '^') {
    return 3;
  }
  else {
    return 0;
  }
}bool isOp(char ch) {
  if (ch == '+' || ch == '-' || ch == '*' ||
      ch == '/' || ch == '^') {
    return true;
  }
  return false;
}int main()
{
  stack<char> s;
  string infix = "a+b*c";
  string postfix = "";
  char ch;
  for (unsigned i = 0; i < infix.size(); i++) {
    ch = infix[i];
    if (isOp(ch)) {
      if (s.empty()) {
        s.push(ch);
      }
      else if (getPrecedence(ch) >= getPrecedence(s.top())) {
        s.push(ch);
      }
    }
    else {
      postfix += ch;
    }
  }
  if (!s.empty()) {
    while (!s.empty()) {
      postfix += s.top();
      s.pop();
    }
  }
  cout << infix << endl;
  cout << postfix << endl;
  return 0;
}
```

对这个程序的一个很好的扩展是添加括号检查，这样你就可以转换更复杂的表达式。如果你做这个扩展，我很想看你的节目。

# 使用堆栈

我刚刚展示了三个使用堆栈解决编程问题的例子。在更技术性的计算机科学中，堆栈用于管理函数(参见[调用堆栈](https://en.wikipedia.org/wiki/Call_stack))、管理内存(参见[基于堆栈的内存分配](https://en.wikipedia.org/wiki/Stack-based_memory_allocation))以及回溯算法(参见[回溯](https://en.wikipedia.org/wiki/Backtracking))并使许多其他算法更加高效。

感谢您阅读这篇文章，请发送电子邮件提出您的意见和建议。