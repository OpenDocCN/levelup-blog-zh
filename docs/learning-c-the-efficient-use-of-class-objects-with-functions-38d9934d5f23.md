# 学习 C++:用函数有效地使用类对象

> 原文：<https://levelup.gitconnected.com/learning-c-the-efficient-use-of-class-objects-with-functions-38d9934d5f23>

![](img/7edda18f908ba6f72c06bc3713e3ecec.png)

JESHOOTS.COM 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[的照片](https://unsplash.com/@jeshoots?utm_source=medium&utm_medium=referral)

一些读者评论说，到目前为止，我的文章还没有以最有效的方式使用类对象。在我的大学 C++课程中，我尽量不同时引入太多的技术概念，以此来减少学生的认知负担。然而，我也不想让我的读者认为我不知道在将类对象作为参数传递给函数时如何正确使用它们，也不知道如何确保使用对象的函数不会无意中改变对象的状态。所以为了克服这个问题，我将从操作符重载中抽出一点时间来谈谈在你的 C++中类对象的有效使用。

本文将集中讨论两三个关键概念。首先，类对象应该作为引用对象传递给函数，以减少由于编译器在将对象传递给函数之前必须复制一个对象而导致的开销。第二个概念是将类对象作为常量传递，以确保函数定义不会无意中更改这些对象，第三个概念是出于同样的原因将函数定义标记为常量。让我们开始吧。

# 通过引用传递类对象参数

有时需要存储在类对象中的数据量会导致一些大型对象。当这样一个对象被传递给一个函数时，如果该对象是通过值传递的，编译器必须在将它传递给函数之前复制所有的数据。这会导致相当多的开销，从而降低程序的速度。

解决方案是通过引用将类对象传递给函数。这样，编译器就不会复制大量的数据，而只是在内存中传递对对象的引用，这样花费的时间更少。此外，通过引用传递对象，如果函数正在更改对象的数据，您不必从函数返回对象，从而节省更多时间。

让我们看一个例子。下面是一个简单的`Student`对象的类定义:

```
class Student {
private:
  string name;
  string id;
  vector<int> grades;public:
  Student(string sname, string sid) {
    name = sname;
    id = sid;
  } void addGrade(int grade) {
    grades.push_back(grade);
  }

  int gradeCnt() {
    return grades.size();
  } int getGradeTot() {
    int total = 0;
    for (int grade : grades) {
      total += grade;
    }
    return total;
  }
};
```

假设我想要一个通用函数来计算一个学生的考试成绩的平均值，而不是用一个成员函数来计算平均值。下面是函数定义:

```
double average(Student &stdnt) {
  int total = stdnt.getGradeTot();
  return static_cast<double>(total) / stdnt.gradeCnt();
}
```

通过引用传递`Student`对象，我很有效率，并且没有让编译器为那个学生复制所有存储的数据。

# 常量成员函数

我们需要编写一些不需要修改任何类数据的成员函数。对于这些成员函数，应该定义为`const`函数。当函数如此定义时，函数定义中任何试图修改对象状态的代码都将被标记为错误。这确保了使用该函数的任何人都知道该函数不会改变任何东西。

下面是一个`Point`对象的定义以及用`const`定义的 getter 成员函数:

```
class Point {
private:
  int x, y;public:
  Point(int x1, int y1) {
    x = x1;
    y = y1;
  } int getX() const { return x; }
  int getY() const { return y; }
};
```

下面是一个测试这个定义的简短程序:

```
int main ()
{
  Point p1(1,2);
  cout << "x: " << p1.getX() << ", y: " << p1.getY() << endl;
  return 0;
}
```

这个程序的输出是:

```
x: 1, y: 2
```

所有不对对象状态进行修改的成员函数都应该被定义为`const`函数。

# 将类对象参数作为常量引用传递

这就引出了第三个好的对象参数实践——将类对象参数作为`const`引用传递。这里我们结合了我们所学的关于通过引用传递对象和确保对象不能被修改的知识。

引用对象也可以作为常量传递，这样函数就不能在函数定义中修改对象。如果函数中有试图修改对象的代码，编译器将抛出错误。

下面是一个函数的例子，它将前面的一个`Point`对象作为`const` 引用传递:

```
void displayPt(const Point &p) {
  cout << "x: "  << p.getX() << ", y: " << p.getY();
}
```

下面是这个函数在程序中的用法:

```
int main ()
{
  Point p1(1,2);
  displayPt(p1);
  return 0;
}
```

这个程序的输出是:

```
x: 1, y: 2
```

现在让我们看看当我们在试图修改`Point`对象的函数定义中放入一些代码时会发生什么。首先，让我给`Point`定义添加一些 setter 代码:

```
void setX(int x1) {
  x = x1;
}void setY(int y1) {
  y = y1;
}
```

现在，我将添加任意一行代码，将 x 坐标更改为函数定义:

```
void displayPt(const Point p) {
  p.setX(2);
  cout << "x: "  << p.getX() << ", y: " << p.getY();
}
```

当我编译这个程序时，编译器在下面一行标记了一个错误:

```
p.setX(2);
```

因为我试图修改参数，但它是作为常量传递给函数的。

# 如何传递对象参数

当将类对象作为参数传递给函数时，最安全和最有效的方法是将它们作为`const`引用传递。如果函数被设计来修改对象，那么你不能把对象作为一个常量传递，但是你仍然可以把它作为一个引用参数传递。不修改成员变量的类成员函数也应该定义为`const`函数。

你能做的最糟糕的事情是把你的类对象作为值参数，并且遭受性能的打击，你最终会因为编译器在把你的对象的拷贝传递给一个函数之前制作你的对象的拷贝而受到影响。

感谢您的阅读，请发送电子邮件至[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)向我提出意见和建议。如果你对我的在线编程课程感兴趣，请访问 https://learningcpp.teachable.com。