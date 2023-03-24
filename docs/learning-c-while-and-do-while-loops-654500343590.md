# 学习 C++: while 和 do while 循环

> 原文：<https://levelup.gitconnected.com/learning-c-while-and-do-while-loops-654500343590>

![](img/eb2434612f552ec2e3f28aa2fabd25ab.png)

照片由[艾蒂安·吉拉代](https://unsplash.com/@etiennegirardet?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在之前的[文章](/learning-c-for-loops-229e5a803002)中，我讨论了如何在 C++中使用`for`循环。在本文中，我将讨论 C++中的另外两种基本循环类型——`while`循环和`do while`循环。当事先不知道要处理循环体的次数时，可以使用这些循环类型。

例如，当处理一个文件的内容时，您不知道文件中有多少条记录，所以您不能使用`for`循环来执行循环体一定的次数。另一个例子是，当您处理用户输入时，用户可以决定何时停止处理。

这些类型的编程场景需要一个开放式循环，该循环将在达到特定条件时结束，而不是在特定次数的迭代之后结束。

# while 循环

对于大多数迭代次数不确定的编程问题，可以选择`while`循环。下面是`while`循环的语法模板:

*while(条件){
循环体；
}*

模板中的条件是一个关系表达式，必须计算为 true，循环体才能执行。条件通常包含在条件中检查的变量(循环控制变量)。

我要展示的第一个例子是使用一个带有 sentinel 值的`while`循环。*标记*是一个值，当遇到该值时，会导致循环停止。第一个例子是一个程序，它让用户输入测试分数来计算测试平均分。当用户输入值-99(标记值)时，程序将停止。

程序如下:

```
#include <iostream>
using namespace std;int main () {
  int grade = 0, total = 0, count = 0;
  cout << "Enter a grade: ";
  cin >> grade;
  while (grade != -99) {
    total += grade;
    count++;
    cout << "Enter a grade: ";
    cin >> grade;
  }
  double average = static_cast<double>(total) / count;
  cout << "The average grade is: " << average << endl;
  return 0;
}
```

下面是这个程序运行一次的输出:

```
Enter a grade: 77
Enter a grade: 81
Enter a grade: 92
Enter a grade: 83
Enter a grade: 76
Enter a grade: -99
The average grade is: 81.8
```

当我教我的学生`while`循环时，我告诉他们在设计使用 while 循环的代码时必须考虑四件事。它们是:

*1。条件必须明确，并反映出你要解决的问题。*

*2。条件中的循环控制变量必须为 true 才能进入条件。*

*3。知道你想在循环体中完成什么。*

*4。确保修改循环控制变量，以便循环最终停止。*

所有这些都很重要，但最后一个尤其重要。如果不修改循环内部的循环控制变量，循环就会一直运行下去，这叫做*无限循环*。

虽然您通常会选择使用 for 循环，但是您可以创建一个`while`循环来执行某个任务一定的次数。这被称为*计数控制*循环。这种循环的一个例子是当你想用用户的数据初始化一个数组时。以下是一个计数控制循环的示例:

```
int main () {
  const int numElements = 5;
  int numbers[numElements];
  int count = 0;
  int number;
  while (count < numElements) {
    cout << "Enter an integer: ";
    cin >> number;
    numbers[count] = number;
    count++;
  }
  for (int num : numbers) {
    cout << num << " ";
  }
  return 0;
}
```

我在最后添加了 range `for`循环，这样你就可以确保所有的数据都被输入到数组中。

如果省略 count++语句；，循环将永远运行，数组将不会正确填充。

# do while 循环

`do while`循环的执行与 while 循环非常相似，但有一个重要的区别——条件是在循环体的末尾，而不是在循环体之前。这使得一些程序更容易编写，因为条件测试是在第一次执行循环体之后执行的。

以下是 do while 循环的语法模板:

*do {
循环体；
} while(条件)；*

下面的程序是一个`do while`循环的例子。该程序要求用户输入他们的密码，只有当输入的密码和存储的密码匹配时才中断循环。

程序是这样的:

```
#include <iostream>
#include <string>
using namespace std;int main ()
{
  string password = "letmein";
  string loggedPW;
  do {
    cout << "Enter your password: ";
    cin >> loggedPW;
  } while (loggedPW != password);
  cout << "Password accepted!" << endl;
  return 0;
}
```

下面是这个程序运行一次的输出:

```
Enter your password: password
Enter your password: 123456
Enter your password: mypassword
Enter your password: letmein
Password accepted!
```

选择`do while`循环而不是`while`循环的关键是，当循环体必须至少执行一次时，选择`do while` 循环。一个例子是当你向用户显示一个菜单选项时(这个例子只在基于字符的应用程序中有意义)。下面是这样一个菜单的例子:

```
int main ()
{
  int choice = 0;
  do {
    cout << "1\. Do this." << endl;
    cout << "2\. Do that." << endl;
    cout << "3\. Quit." << endl;
    cout << "Enter choice: ";
    cin >> choice;
  } while (choice < 1 || choice > 3);
  return 0;
}
```

下面是这个程序的一次运行:

```
1\. Do this.
2\. Do that.
3\. Quit.
Enter choice: 0
1\. Do this.
2\. Do that.
3\. Quit.
Enter choice: 4
1\. Do this.
2\. Do that.
3\. Quit.
Enter choice: 3
```

# 关于 while 循环选择的总结

当循环要求不确定时，应使用`while`循环或`do while`循环。换句话说，如果你不知道你希望循环体执行多少次，你应该使用这两个版本的`while`循环中的一个。如果你想保证循环体至少运行一次，你应该选择一个`do while`循环，否则，总是选择 while 循环，因为它更容易阅读和调试。

感谢您的阅读，请给我发电子邮件提出意见和建议。