# 学习 C++:在容器中存储类对象

> 原文：<https://levelup.gitconnected.com/learning-c-storing-class-objects-in-containers-ca12546f1a89>

![](img/dbcba20ae1b23b24deb5054533060f6a.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

从类实例化的对象可以存储在各种 C++容器中，就像容器存储各种内置数据类型的变量一样。在这篇文章中，我将向你展示如何在数组和向量中存储类对象，以及如何在这些对象被存储后访问它们。

# 类别定义

我将用来演示如何在容器中存储类对象的类是一个`Student`类，它包含学生的姓名、学号和一个向量，向量中包含他们的学期成绩。尤其重要的是，我有一个成员变量，它本身就是一个容器，这样我就可以向你展示如何从另一个容器中访问成绩(这只是有点混乱)。

下面是`Student` (名义)等级定义:

```
class Student {
private:
  string name;
  string id;
  vector<int> grades;public:
  Student(string n, string i) {
    name = n;
    id = i;
  } Student() {
    name = "";
    id = "";
  } string getName() { return name; }
  string getId() { return id; } void addGrade(int grade) {
    grades.push_back(grade);
  }
};
```

# 在数组中存储和访问类对象

现在让我们创建几个对象，并把它们放在一个数组中。数组是通过使用`Student`类作为数据类型，后跟名称和要存储的元素数量来创建的。这是一行代码:

```
const int numStudents = 3;
Student students[numStudents];
```

下面是使用`Student`类定义的完整程序:

```
int main ()
{
  Student stu1("Jane Smith", "1234");
  stu1.addGrade(77);
  stu1.addGrade(81);
  stu1.addGrade(88);
  Student stu2("John Jones", "2345");
  stu2.addGrade(91);
  stu2.addGrade(81);
  stu2.addGrade(66);
  Student stu3("Barb Green", "4321");
  stu3.addGrade(81);
  stu3.addGrade(82);
  stu3.addGrade(83);
  const int numStudents = 3;
  Student students[numStudents];
  students[0] = stu1;
  students[1] = stu2;
  students[2] = stu3;
  return 0;
}
```

现在让我们看看如何访问数组中的对象来显示它们的名称和 id。如果一个`Student`对象不在容器中，我们可以访问对象名，后跟点操作符和我们想要的成员函数名。对于数组中的对象，对象名被替换为数组名和我们正在访问的索引位置。

下面的代码片段演示了如何做到这一点:

```
for (int i = 0; i < numStudents; i++) {
  cout << students[i].getName() << ", "
       << students[i].getId() << endl;
}
```

以下是我们运行程序时的输出:

```
Jane Smith, 1234
John Jones, 2345
Barb Green, 4321
```

那还不算太糟。现在让我们更深入一层，访问存储在向量中的分数。由于`grades`向量是私有的，我们需要一个 getter 成员函数来检索分数:

```
void showGrades() {
  for (int grade : grades) {
    cout << grade << " ";
  }
}
```

现在我们可以将对这个成员函数的调用合并到我们的程序中:

```
for (int i = 0; i < numStudents; i++) {
  cout << students[i].getName() << ", "
       << students[i].getId() << endl;
  cout << "Grades: ";
  students[1].showGrades();
  cout << endl;
}
```

现在的输出是:

```
Jane Smith, 1234
Grades: 91 81 66
John Jones, 2345
Grades: 91 81 66
Barb Green, 4321
Grades: 91 81 66
```

类似地，我们可以编写成员函数来计算平均分数，找出最低和最高分数，等等。但是现在对于数组来说已经足够了。让我们继续学习矢量。

# 在向量中存储和访问类对象

除了向量和数组之间的所有区别之外，使用向量存储类对象和使用数组的主要区别在于如何声明向量来存储类对象。

我们可以使用前面所示的相同的`Student` 类定义。我们可以使用与前面相同的对象实例化代码来创建三个学生。我们必须做的第一个更改是用创建向量的语句替换数组声明语句:

```
vector<Student> students;
```

我们不需要一个常数来表示学生的数量，因为如果我们需要知道向量中有多少元素，我们可以使用`size`成员函数，并且因为我们可以使用一个范围`for` 循环来遍历向量，所以我们不需要这个循环的常数。

我们程序的其余部分可以像我们之前写的那样使用。我们将使用相同的点符号来访问对象的成员函数，将数组符号(`students[n]`)替换为向量名称，例如当我们将学生添加到向量时:

```
students.push_back(stu1);
students.push_back(stu2);
students.push_back(stu3);
```

记住这些，下面是在 vector 中存储和访问`Student`对象的程序:

```
#include <iostream>
#include <vector>
using namespace std;// class definition goes hereint main ()
{
  Student stu1("Jane Smith", "1234");
  stu1.addGrade(77);
  stu1.addGrade(81);
  stu1.addGrade(88);
  Student stu2("John Jones", "2345");
  stu2.addGrade(91);
  stu2.addGrade(81);
  stu2.addGrade(66);
  Student stu3("Barb Green", "4321");
  stu3.addGrade(81);
  stu3.addGrade(82);
  stu3.addGrade(83);
  vector<Student> students;
  students.push_back(stu1);
  students.push_back(stu2);
  students.push_back(stu3);
  for (Student stdnt : students) {
    cout << stdnt.getName() << ", " << stdnt.getId() << endl;
    cout << "Grades: ";
    stdnt.showGrades();
    cout << endl;
  }
  return 0;
}
```

以下是该程序的输出:

```
Jane Smith, 1234
Grades: 77 81 88
John Jones, 2345
Grades: 91 81 66
Barb Green, 4321
Grades: 81 82 83
```

# 下课了

这就是关于在数组和向量容器中存储类对象的概述。在我的下一篇文章中，我将转移到一个更技术性但更有用的话题——操作符重载。

感谢您的阅读，请在[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)给我发电子邮件，提出意见和建议。如果你对我的在线编程课程感兴趣，请访问[https://learningcpp.teachable.com](https://learningcpp.teachable.com)。