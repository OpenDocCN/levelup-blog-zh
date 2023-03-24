# 学习 C++:表格驱动的选择

> 原文：<https://levelup.gitconnected.com/learning-c-table-driven-selection-5064338a5446>

![](img/d05fc6b2b1dd2347730fce63834c5c80.png)

照片由 [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 C++中执行选择(或分支)的典型方式是使用 if 语句。然而，对于许多不断出现的场景，将逻辑放入表中是在代码中执行选择的更有效的方式。在这篇文章中，我将展示几种用表格代替复杂 if 语句的方法。

# 直接访问表

我想讨论的第一种表驱动选择是使用直接访问表。我将用来演示直接访问表的例子是确定一个月中的天数。

解决这个问题的典型方法，或者我称之为计算机科学 101 方法，是用一个`if-else if`语句对一个月中的日子进行编码，就像这样:

```
if (month == 1) {
  days = 31;
}
else if (month == 2) {
  days = 28;
}
else if (month == 3) {
  days = 31;
}
else if (month == 4) {
  days = 30;
}
else if (month == 5) {
  days = 31;
}
else if (month == 6) {
  days = 30;
}
else if (month == 7) {
  days = 31;
}
else if (month == 8) {
  days = 31;
}
else if (month == 9) {
  days = 30;
}
else if (month == 10) {
  days = 31;
}
else if (month == 11) {
  days = 30;
}
else if (month == 12) {
  days = 31;
}
```

这是一大堆代码，占据了程序中很大的空间，需要花很多时间来通读。

对这些数据进行编码的一种更简单的方法是在一个数组中，数组索引表示月份数，存储在该索引中的元素是该月的天数。

下面的程序演示了如何使用数组作为直接访问表来查找一个月中的日子:

```
#include <iostream>
using namespace std;int main ()
{
  int month = 0;
  int days = 0;
  const int numMonths = 12;
  int monthDays[numMonths] =
    {31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
  string another = "y";
  do {
    cout << "Month number to look up? ";
    cin >> month;
    days = monthDays[month-1];
    cout << "There are " << days << " days." << endl;
    cout << "Do another (y/n)? ";
    cin >> another;
  } while (another == "y");
  return 0;
}
```

以下是该程序的输出示例:

```
Month number to look up? 7
There are 31 days.
Do another (y/n)? y
Month number to look up? 2
There are 28 days.
Do another (y/n)? n
```

使用直接访问表查找的完整程序比上面使用的`if-else if` 语句要短。

# 阶梯式存取表

代替`if-else if` 语句的第二种类型的表查找是阶梯访问表，如史蒂夫·麦康奈尔在他的书*代码完成*中所描述的。

阶梯式表格背后的思想是，表格条目对一定范围的数据有效，而不是对特定值有效。一个经典的例子是给数字考试分数分配字母等级。在许多教师使用的标准 90-80-70-60 方案中,“A”级对应于 90 到 100 的数字分数；“B”级对应于从 80 到 89 的数字分数；“C”级对应于从 70 到 79 的数字分数；诸如此类。

下面是一个为字母等级实现阶梯过程的程序:

```
int main ()
{
  int gradeLimits[] = {60, 70, 80, 90, 100};
  string grades[] = {"F", "D", "C", "B", "A"};
  int gradeLevel = 0;
  int maxGradeLevel = 5;
  int score;
  cout << "What was the numeric score? ";
  cin >> score;
  string stuGrade = "A";
  while ((stuGrade == "A") && (gradeLevel < maxGradeLevel)) {
    if (score < gradeLimits[gradeLevel]) {
      stuGrade = grades[gradeLevel];
    }
    gradeLevel++;
  }
  cout << "A numeric score of " << score
       << " is the letter grade " << stuGrade << ".";
  return 0;
}
```

以下是该程序的几次运行:

```
What was the numeric score? 73
A numeric score of 73 is the letter grade C.
What was the numeric score? 91
A numeric score of 91 is the letter grade A.
```

这个程序比一个`if-else if`版本要短得多，更高效，也更容易阅读。

# 索引访问表

进行表驱动选择的另一种方法是使用索引访问表。在这里，表的表现很像哈希表。对于字母等级示例，数字分数是索引，与该分数相关联的字母等级存储在该索引的位置，就像散列表中的键被转换为数组索引值一样。

索引访问表可以手动创建并存储在库中，也可以在程序启动时由函数生成。下面的示例通过在程序开始时创建索引访问表并在创建后立即演示来简化这一过程。

程序如下:

```
int main ()
{
  // this step will be in another program or function
  const int numElements = 101;
  string letterGrades[numElements];
  for (int i = 0; i < numElements; i++) {
    if ((i >= 0) && (i <= 60)) {
      letterGrades[i] = "F";
    }
    else if ((i >= 60) && (i <= 69)) {
      letterGrades[i] = "D";
    }
    else if ((i >= 70) && (i <= 79)) {
      letterGrades[i] = "C";
    }
    else if ((i >= 80) && (i <= 89)) {
      letterGrades[i] = "B";
    }
    else {
      letterGrades[i] = "A";
    }
  }
  // table processing part
  int score = 77;
  cout << score << ": Letter grade: "
       << letterGrades[score] << endl;
  score = 62;
  cout << score << ": Letter grade: "
       << letterGrades[score] << endl;
  score = 82;
  cout << score << ": Letter grade: "
       << letterGrades[score] << endl;
  score = 98;
  cout << score << ": Letter grade: "
       << letterGrades[score] << endl;
  score = 54;
  cout << score << ": Letter grade: "
       << letterGrades[score] << endl;
  return 0;
}
```

下面是这个程序运行一次的输出:

```
77: Letter grade: C
62: Letter grade: D
82: Letter grade: B
98: Letter grade: A
54: Letter grade: F
```

# 考虑长 if-else if 语句上的表驱动选择

如果您发现自己在程序中编写了一长串`if-else if`语句，您应该考虑在程序中采用这些表格驱动的选择方法之一。表格更容易阅读，也不容易出错。它们也更有效，尤其是当它所替换的`if-else if` 语句很长并且使用复杂的逻辑时。

感谢您的阅读，请给我发电子邮件提出意见和建议。如果你对我的计算机编程课程感兴趣，请访问我的网站:【https://learningcpp.teachable.com】T2。