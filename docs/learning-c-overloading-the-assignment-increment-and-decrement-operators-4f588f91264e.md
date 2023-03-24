# 学习 C++:重载赋值、递增和递减运算符

> 原文：<https://levelup.gitconnected.com/learning-c-overloading-the-assignment-increment-and-decrement-operators-4f588f91264e>

![](img/4727a22e8fccdc6e8c8417fb37a5036e.png)

照片由[贾瓦德·埃斯马埃利](https://unsplash.com/@javad_esmaeili?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在我的上一篇文章中，我向您展示了如何重载输入和输出操作符，以便您可以使用标准方法使用您的用户定义类型执行输入和输出。在本文中，我将介绍如何重载另外三个操作符:赋值、递增和递减。

# this 指针

在讨论如何重载赋值操作符之前，我需要讨论一下`this`指针。`this`指针是一个内置指针，当代码执行时，它总是指向当前有“焦点”的对象。

假设您有两个表示`Point` s: `p1`和`p2`的对象，并且您想要检索 x 和 y 坐标:

```
cout << p1.getX() << ", " << p1.getY();
```

在内部，C++编译器创建了一个名为 this 的编译器，它指向`p1`对象。如果我接着写:

```
cout << p2.getX() << ", " << p2.getY();
```

`this`指针现在指向`p2`。

我们将需要`this`指针来重载我在本文中讨论的操作符，因此您将很快看到 this 指针的运行。

# 重载赋值运算符

让我们从使用我在上一篇关于重载的文章中提供的`Point`类的定义开始:

```
class Point {
private:
  int x, y;public:
  Point(int, int);
  friend ostream &operator << (ostream &, const Point &);
  friend istream &operator >> (istream &, Point &);
};Point::Point(int newx, int newy) {
  x = newx;
  y = newy;
}ostream &operator << (ostream &strm, const Point &p) {
  strm << "x:" << p.x << ", y:" << p.y;
  return strm;
}istream &operator >> (istream &strm, Point &p) {
  cout << "x coordinate: ";
  strm >> p.x;
  cout << "y coordinate: ";
  strm >> p.y;
  return strm;
}
```

我们希望能够将一个`Point`对象的值分配给另一个`Point`对象，如下所示:

```
Point p1(1,2);
Point p2;
p2 = p1;
```

为了重载`Point`对象的赋值操作符，我们需要传入我们要赋值的`Point`对象作为参数。当前对象是我们通过赋值初始化的新的`Point`对象。在函数进行任何赋值之前，它确保当前对象和传入的对象不是同一个对象。如果不是，函数体将现有对象的`x`和`y`成员变量赋给当前对象，然后将`this`指针返回给当前对象。这个定义放在`Point`类定义中。

以下是`Point`对象的重载赋值运算符的定义:

```
const Point operator=(const Point &pnt) {
  if (this != &pnt) {
    x = pnt.x;
    y = pnt.y;
  }
  return *this;
}
```

下面是一个使用这个定义的测试程序:

```
int main ()
{
  Point p1(1,2);
  cout << p1 << endl;
  Point p2(0,0);
  cout << p2 << endl;
  p2 = p1;
  cout << p2 << endl;
  return 0;
}
```

这个程序的输出是:

```
x:1, y:2
x:0, y:0
x:1, y:2
```

我们在这个例子中看到的作业类型叫做*复制作业*。还有另一种类型的赋值，叫做*移动赋值*，我将在另一篇文章中讨论。

# 重载增量运算符

定义类时，通常需要重载增量运算符。这个操作符重载起来并不困难，但是你必须记住，增量操作符既可以作为前缀操作符，也可以作为后缀操作符。

为了演示如何重载增量运算符(和减量运算符)，我将为跟踪库存的类引入一个新的类定义。这个类将定义一个产品——产品名称和库存商品的数量。下面是类的定义:

```
class Item {
private:
  string itemName;
  int numStock;public:
  Item(string name, int stock) {
    itemName = name;
    numStock = stock;
  } void display() const { // for your Rud
    cout << itemName << ": " << numStock;
  }
};
```

要重载前缀增量操作符，我们所要做的就是将增量操作符应用于`numStock`成员变量并返回`this`指针。下面是运算符定义，它放在类定义中:

```
Item operator++() {
  ++numStock;
  return *this;
}
```

下面是一个测试程序:

```
int main ()
{
  Item item1("Widget", 0);
  item1.display();
  ++item1;
  cout << endl;
  item1.display();
  return 0;
}
```

以下是该程序的输出:

```
Widget: 0
Widget: 1
```

现在让我们看看如何重载后缀增量运算符。这一点有点棘手，因为我们必须认识到这样一个事实，即存储在变量中的值是在值递增之前被访问的。我们的函数需要在递增成员变量之前获取它的旧值。

这个函数定义的另一个奇怪之处是，我们必须为函数提供一个伪参数，这样编译器就会知道这是后缀增量运算符的定义，而不是前缀增量运算符的定义。

以下是重载后缀增量运算符的定义:

```
Item operator++(int) {
  Item temp(itemName, numStock);
  numStock++;
  return temp;
}
```

下面是一个测试程序:

```
int main ()
{
  Item item1("Widget", 0);
  item1.display();
  ++item1;
  cout << endl;
  item1.display();
  item1++;
  cout << endl;
  item1.display();
  return 0;
}
```

这个程序的输出是:

```
Widget: 0
Widget: 1
Widget: 2
```

# 重载减量运算符

前缀和后缀这两个减量运算符的定义与增量运算符类似，只是使用了减量运算符而不是增量运算符。以下是两种递减版本的定义:

```
Item operator--() {
  --numStock;
  return *this;
}Item operator--(int) {
  Item temp(itemName, numStock);
  numStock--;
  return temp;
}
```

下面是一个测试程序:

```
int main ()
{
  Item item1("Widget", 0);
  item1.display();
  ++item1;
  cout << endl;
  item1.display();
  item1++;
  cout << endl;
  item1.display();
  cout << endl;
  --item1;
  item1.display();
  item1--;
  cout << endl;
  item1.display();
  return 0;
}
```

以下是该程序的输出:

```
Widget: 0
Widget: 1
Widget: 2
Widget: 1
Widget: 0
```

# 超载继续

这就结束了关于重载赋值、递增和递减操作符的讨论。在我的下一篇 C++文章中，我将讨论重载关系操作符。

感谢您的阅读，请发电子邮件至[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)给我，并提出意见和建议。如果你对我的在线编程课程感兴趣，请访问[https://learningcpp.teachable.com](https://learningcpp.teachable.com)。