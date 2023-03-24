# 用于处理数组、列表或向量的三个程序模板

> 原文：<https://levelup.gitconnected.com/three-program-templates-for-working-with-arrays-lists-or-vectors-89749a41bd23>

## 这些模板可以自动处理有序数据

![](img/ffdcfa5659fb26e8034e02c7bdf205b7.png)

Patrick Amoy 在 [Unsplash](https://unsplash.com/s/photos/programmers?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

所有编程语言都提供了一些顺序存储数据的方法。一些语言为此使用数组。一些语言使用术语列表而不是数组。有些语言同时提供数组和另一种有序数据集合类型——vector(c++)或 ArrayList (Java 和 C#)。为了简化，我将把用于存储顺序数据的数据结构称为*集合*。

本文将演示六个程序模板，初学者应该学习这些模板，以便更自动地决定如何使用这些集合。我将向您展示模板的伪代码，然后提供两到三个不同语言的示例，以便您可以了解模板是如何使用的。有时当有多种使用模板的方法时，会有多个伪代码示例，例如当将数据添加到一个数组与将数据添加到一个向量时。

# 填充一个数组(或列表或向量等。)

第一个程序模板用于从用户处获取数据并将其存储在一个集合中，以使数据更容易处理。例如，如果我想收集一次考试的成绩，并从最高分到最低分对它们进行排序，当数据在一个集合中时，这比我试图对自变量进行排序要容易得多。

下面是用数据填充数组的伪代码:

*声明数组大小
当有更多输入时，将数组索引变量设置为 0
:
读取值
将值存储在数组的索引位置
将索引位置增加 1*

下面是用数据填充 C++向量的伪代码:

*声明数据类型为
的向量，同时有更多输入:
读取一个值
将该值推回到向量上*

以下是用数据填充 Python 列表的伪代码:

*声明列表
当有更多输入时:
读取一个值
将该值追加到列表上*

下面是一个用 JavaScript 填充数组的例子:

```
let grades = new Array();
const NUM_GRADES = 5;
for (let i = 0; i < NUM_GRADES; i++) {
  putstr("Enter a grade: ");
  let grade = parseInt(readline())
  grades[i] = grade;
}
```

这是一个用 C++填充向量的例子:

```
#include <iostream>
#include <vector>
using namespace std;int main()
{
  vector<int> grades;
  const int NUM_GRADES = 5;
  int grade;
  for (int i = 0; i < NUM_GRADES; i++) {
    cout << "Enter a grade: ";
    cin >> grade;
    grades.push_back(grade);
  }
return 0;
}
```

下面是 Python 中的另一个示例，使用 sentinel 值来控制输入:

```
grades = []
grade = int(input(Enter a grade: "))
while grade != -1:
  grades.append(grade)
  grade = int(input("Enter a grade: "))
```

当您希望用交互式输入填充数据结构时，可以使用该模板。当然，您可以在不涉及用户的情况下用数据初始化大多数数据结构，但是这种技术是用语法模板而不是程序模板来描述的。

# 处理每个元素

一旦用数据填充了一个集合，您将希望对该数据执行某种类型的计算。通常，您会希望访问集合中的每个元素，例如将其添加到总数中以确定平均值，或者对数据进行排序，甚至只是在屏幕上显示数据。“处理每个元素”模板用于这些目的。

根据您使用的语言和您想要访问数据的方式，此模板有不同的伪代码示例。大多数语言允许您使用索引来访问集合，但是许多语言也允许您使用一种更简单的技术，让语言来做索引。我将演示所有这些方法，并讨论何时使用它们。

首先，下面是使用索引处理每个元素的伪代码:

*对于索引= 0 到集合长度减 1:
处理索引处的元素*

下面是一个在 JavaScript 中使用此伪代码的示例，其中数组在声明时初始化:

```
let grades = new Array(71, 88, 91);
for (let i = 0; i < grades.length; i++) {
  putstr(grades[i] + " ");
}
```

这在其他使用 C 风格语法的语言中看起来很相似。在 Python 中，for 循环看起来有点不同:

```
grades = [71, 88, 91]
for i in range(0, len(grades)):
  print(grades[i], end=" ")
```

如果您不小心试图访问集合边界之外的集合元素，对集合的索引访问可能会导致错误。为了补救这一点，大多数语言现在都有一个构造，允许您在不使用索引的情况下访问集合中的每个元素。一般来说，这被称为 *for-each* 循环，每种语言都以不同的方式实现这种循环类型。

不过，我们可以用一般的方式编写伪代码，并演示它在不同语言中的用法。这是:

*对于集合中的每个元素:
处理元素*

下面是 for-each 循环在 C++中的用法:

```
int main()
{
  const int NUM_GRADES = 3;
  int grades[NUM_GRADES] = {71, 88, 91};
  for (int grade : grades) { // range for loop
    cout << grade << " ";
  }
  return 0;
}
```

如果数据存储在一个向量中，这同样有效。

在 JavaScript 中，用于数组的正确 for 循环是 for-of 循环。下面是一个使用 for-of 循环计算数组中成绩平均值的程序:

```
let grades = new Array(71, 88, 91);
let total = 0;
for (const grade of grades) {
  total += grade;
}
let average = total / grades.length;
print("Average:", average);
```

编程大师们一致认为，无论何时访问数组或向量等集合中的每个元素，都应该使用 for-each 类型的循环，前提是这种类型的循环在您的编程语言中是可用的。在允许访问集合边界之外的内存的语言中，使用 for-each 类型循环有助于避免语法错误(大多数语言)或逻辑错误(C++)。

# 搜索收藏

此程序模板用于线性或顺序搜索，其中搜索从集合的开头开始，当搜索找到要搜索的元素时结束，或者搜索到达集合的结尾而没有找到要搜索的元素。

与 *Process Every Element* 模板一样，该模板可用于索引循环或 for-each 类型循环。下面是索引模板伪代码:

*获取值以搜索
将索引设置为 0
用于索引<集合长度
如果集合[索引]等于值以搜索
返回成功
如果在集合结束
报告失败*

下面是 Python 中使用该模板的一个示例，其中定义了一个函数来搜索列表中的值:

```
def search(coll, value):
  for i in range(0, len(coll)):
    if coll[i] == value:
      return True
  return False from random import seed
from random import randint
seed(1)
grades = []
for i in range(10):
  grades.append(randint(50,100))
print(grades)
value = int(input("Enter search value: "))
if search(grades, value):
  print(value,"is in grades.")
else:
  print(value,"is not in grades.")
```

该程序使用随机数生成来构建成绩列表并显示该列表，因此我们可以通过输入列表中的值和列表外的值来验证搜索功能是否有效。

执行 for-each 搜索的程序模板类似于索引模板:

*如果当前元素等于为
返回成功
搜索的值，如果在集合
返回失败*结束，则获取在集合
开始搜索
的值

下面是一个在 C++中使用的模板示例，使用了一个向量:

```
#include <iostream>
#include <vector>
#include <cstdlib>
#include <ctime>
using namespace std;bool search(vector<int> vec, int value){
  for (int number : vec) {
    if (number == value) {
      return true;
    }
  }
  return false;
} int main()
{
  srand(time(0));
  vector<int> numbers;
  for (int i = 1; i <= 10; i++) {
    numbers.push_back(rand() % 100 + 1);
  }
  for (int number : numbers) {
    cout << number << " ";
  }
  cout << endl;
  int value;
  cout << "Enter a value to search for: ";
  cin >> value;
  if (search(numbers, value)) {
    cout << value << " is in numbers." << endl;
  }
  else {
    cout << value << " is not in numbers." << endl;
  }
  return 0;
}
```

这里有一个关于 C++的提示。如果集合参数是数组，则不能在函数中使用 range for 循环。在这种情况下，你只能使用矢量。你可以在 main 函数中用一个 range for 循环来遍历一个数组，但是你不能在返回值函数中使用这种类型的循环。

# 程序模板和编程专业知识

成为专业编程专家的一个关键是学习用于解决问题的不同程序模板。就像国际象棋大师那样，通过研究著名的国际象棋比赛棋盘位置，学习快速识别何时应该使用程序模板，并知道如何有效地使用它，将使你朝着成为编程大师的方向前进。