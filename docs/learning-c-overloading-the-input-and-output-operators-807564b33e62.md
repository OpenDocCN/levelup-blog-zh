# 学习 C++:重载输入和输出操作符

> 原文：<https://levelup.gitconnected.com/learning-c-overloading-the-input-and-output-operators-807564b33e62>

![](img/8c73e693f1329286202446ea4362a2bc.png)

[维多利亚博物馆](https://unsplash.com/@museumsvictoria?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当你在设计和实现一个类的时候，你要做的第一件事就是确定将数据放入你的类对象的方法和将数据从你的对象中取出的方法。有时成员函数很适合这种情况，但在其他时候，您可能希望使用输入和输出操作符，就像您对内置数据类型使用的操作符一样。

在这篇文章中，我将向你展示如何重载输入操作符(`>>`)和输出操作符(`<<`)，这样你就可以在你的类对象中使用它们。

# 关于运算符重载的几点说明

C++是少数允许运算符重载的语言之一。重载操作符对类很有用，因为有时候你想用一个操作符来处理一个类对象，而这个操作符不能识别这个对象。这对于所有用户定义的类型(类)都是如此。

举个例子，考虑一个叫做`IntData`的类。这个类是整数的包装类，包括(假设)一些 int 类型本身没有的功能。如果我想把两个`IntData`对象加在一起，在没有操作符重载的情况下，我必须提供一个成员函数来做这件事。下面是一个代码片段示例:

```
IntData d1(1);
IntData d2(2);
IntData d3 = d1.add(d2);
```

我更想做的是:

```
IntData d1(1);
IntData s2(2);
IntData d3 = d1 + d2;
```

如果没有操作符重载，我无法做到这一点，因为`+`操作符没有被定义为与我的`IntData`对象一起工作。另一方面，通过操作符重载，我可以让`+`操作符识别我的`IntData`对象并执行结果加法。这使得运算符重载成为许多类定义的重要部分。

这也适用于输入和输出等操作。我不能简单地写这个来显示一个`IntData`对象的内容:

```
cout << d1 << endl;
```

操作符不知道我的类定义。我也不能使用输入操作符将数据放入`IntData`对象:

```
cin >> d2;
```

为了让这些操作能够工作，以便我的类的用户可以像使用原始数据类型一样使用这些对象，我需要重载输入和输出操作符来识别`IntData`对象。

# 重载初步:友元函数

为了正确地重载操作符来处理类，必须将重载操作符的函数定义为一个*友元函数*。友元函数是可以访问类的私有成员变量的函数。该函数不被视为类的一部分；它只是访问类的私有成员变量来执行它的功能。

这里有一个简单的例子，使用了我上面讨论的`IntData` 类的定义。为了更真实地演示友元函数是如何工作的，我需要将类的声明从类的定义中分离出来，尽管为了可读性起见，我将它们放在同一个文件中。下面是`IntData`类的声明和定义:

```
class IntData {
private:
  int data;public:
  IntData(int);
  friend void print(const IntData &);
};IntData::IntData(int d) {
  data = d;
}void print(const IntData &d) {
  cout << d.data;
}
```

你可以看到`print`函数是在类定义中定义的，但它并不是类的一部分。我不能使用点操作符从一个`IntData`对象中访问它。如果我尝试，我会得到一个错误。相反，我像使用常规函数一样使用它，并向它传递一个`IntData`对象。下面是一个演示如何使用 friend 函数的示例程序:

```
int main ()
{
  IntData d1(12);
  print(d1); // displays 12
  return 0;
}
```

当然，友元函数比我在这里展示的要多，但是这足以让你看完这篇关于操作符重载的文章。我将在另一篇文章中详细介绍友元函数和友元类。

# 重载输出运算符

我将通过重载输出操作符(`<<`)来使用我定义的`Point`类，从而开始我的重载演示。下面是`Point`的简单类定义:

```
class Point {
private:
  int x, y;
public:
  Point(int, int);
};
```

首先，我们来讨论输出操作符是如何重载的。重载运算符涉及到创建一个函数定义，该函数定义扩展了运算符可以处理的数据类型的定义。该函数看起来像一个常规函数。它有一个返回类型、一个名称(操作符本身)、一个参数列表、一个函数体和一个返回值。

重载函数被定义为接受数据的类的友元函数。这是为了让我们可以将所需的类的私有成员变量直接传递给函数，即使重载的操作符函数在类定义之外。

下面是重载输出操作符以输出`Point`对象成员变量的函数定义签名行:

```
ostream &operator << (ostream &strm, const Point &p)
```

函数的返回类型是`ostream`，表示输出流。接下来是输出操作符名称，它被定义为一个引用对象，这样您就可以将多个输出操作符链接在一起。参数列表由一个`ostream`对象引用和一个常量`Point`对象引用组成，其中`ostream`对象引用是`Point`数据将要流向的对象，因此数据不会在函数中被修改。为了提高效率，这些参数也通过引用传递。

现在让我们看看函数体:

```
{
  strm << "x:" << x << ", y:" << y;
  return strm;
}
```

该函数将 x 和 y 坐标的标签以及成员变量本身传输到`strm`对象，该对象由函数返回。

下面是如何在类声明部分声明该函数:

```
friend ostream &operator << (ostream &, const Point &);
```

下面是完整的类声明和定义代码，以及一个测试程序:

```
class Point {
private:
  int x, y;
public:
  Point(int, int);
  friend ostream &operator << (ostream &, const Point &);
};Point::Point(int newx, int newy) {
  x = newx;
  y = newy;
}ostream &operator << (ostream &strm, const Point &p) {
  strm << "x:" << p.x << ", y:" << p.y;
  return strm;
}int main ()
{
  Point p1(1,2);
  cout << p1 << endl;
  return 0;
}
```

这个程序的输出是:

```
x:1, y:2
```

# 重载输入运算符

重载输入操作符所需的代码类似于输出操作符的代码，除了明显地使用了一个`istream`对象而不是一个`ostream`对象。重载函数需要在类声明部分声明为友元，并在类外定义，就像我们对重载输出操作符所做的那样。

下面是`Point`类的重载输入操作符的函数声明和函数定义:

```
friend istream &operator >> (istream &, Point &);istream &operator >> (istream &strm, Point &p) {
  cout << "x coordinate: ";
  strm >> p.x;
  cout << "y coordinate: ";
  strm >> p.y;
  return strm;
}
```

注意，`Point`对象是一个常量参数，因为我们希望函数修改对象的成员变量。我还在函数定义中提供了一些提示信息，这些信息并不是绝对必要的，但是可以使操作者更加用户友好。

下面是一个测试输入操作符和输出操作符的程序:

```
int main ()
{
  Point p1;
  cout << "Enter a point: " << endl;
  cin >> p1;
  cout << p1 << endl;
  return 0;
}
```

这个程序的输出是:

```
Enter a point:
x coordinate: 5
y coordinate: 8
x:5, y:8
```

# 前方有更多超载

这两个操作符只是我们在类定义服务中可以重载的许多操作符中的第一个。在我的下一篇文章中，我将讨论如何重载赋值操作符以及递增和递减操作符。

感谢您的阅读，请给我发电子邮件，地址是[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)，并提出意见和建议。如果你对我的在线编程课程感兴趣，请访问[https://learningcpp.teachable.com](https://learningcpp.teachable.com)。