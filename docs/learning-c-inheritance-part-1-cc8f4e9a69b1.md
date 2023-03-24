# 学习 C++:继承第 1 部分

> 原文：<https://levelup.gitconnected.com/learning-c-inheritance-part-1-cc8f4e9a69b1>

![](img/51a711e20ddbc56cccacc14ca4c6c32b.png)

照片由[奥斯卡·诺德](https://unsplash.com/@furbee?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

在这篇文章中，我将介绍如何在 C++中执行对象继承。当我们在 C++程序中使用继承时，我们是在建模 *is-a* 关系。比如狗*是——一种*哺乳动物；一辆大众甲壳虫*是一辆*汽车；正方形*是一个*形状。这意味着，以大众甲壳虫为例，甲壳虫具有许多典型汽车的属性，尽管它可能具有一些其他汽车没有的属性，并且它与汽车共有的属性可能与其他汽车不相同。例如，甲壳虫的形状完全不同于其他任何汽车的形状。但是一般来说，一个从另一个对象继承的对象与它所继承的对象有许多共同的特征。

# 一些术语

我们将要继承的类叫做*基类*。从另一个类继承的类被称为*派生类*。包括一个基类和一个或多个派生类的一组类被称为*类层次*。当一个派生类只继承一个基类时，这就是所谓的单一继承。当一个派生类从多个类继承时，这被称为多重继承。在这组文章中，我将使用单一继承。

认识到一个类可以包含其他类作为其定义的一部分是很重要的。这不是一个继承的例子，而是一个*组合的例子。合成的一个例子可能是一个正方形类，它包含一个点类作为正方形的原点。*

与我上面提到的 is-a 关系相反，组合用于 has-a 关系。一个人有-一个名字；一辆汽车有一个发动机；一个员工有工资。我会在这个继承系列的最后多讲讲构图。

# 继承基础

为了演示继承，我将使用一个创建不同形状的经典示例。我将从一个基本的`Shape`类开始，它没有定义一个真实的形状，但是提供了一些我们可以用于特定形状的基本属性和过程。

下面是我的`Shape`类的定义:

```
class Shape {
private:
  int x, y;
public:
  Shape(int newx, int newy) {
    x = newx;
    y = newy;
  }

  int getX() { return x; }
  int getY() { return y; } void setX(int newx) { x = newx; }
  void setY(int newy) { y = newy; } void draw() {
    cout << "Drawing a shape with origin point, x: "
         << x << ", y: " << y;
  }
};
```

当创建一个类的目的仅仅是作为一个类层次结构中的基类时，这个类被称为抽象类。`Shape`类是一个经典的抽象类，因为形状只是某种特定形状类型的抽象，比如正方形或三角形。

要使一个类成为派生类，可以这样开始定义:

```
class Square : public Shape {};
```

这告诉编译器`Shape`类将从`Square`类派生而来。

继续讨论`Square`类，我们需要识别该类的成员变量。一个`Square`对象将会有一个`Shape`的 x 和 y 坐标，但是它也有成员变量来表示其边的长度和宽度。我们把这些放在私人区域:

```
class Square : public Shape {
private:
  int length, width;
};
```

第一行:

```
class Square : public Shape
```

指示基类具有公共规范，这意味着继承的类可以直接访问基类的公共成员。在基类中声明为私有的成员变量和成员函数对于继承的类也是私有的。这意味着，例如，我们很快就会看到，如果我们想让一个继承的类访问基类的私有成员变量，我们就必须使用公共成员函数，比如 getters，来获得这种访问。

现在让我们给类定义添加一个构造函数。派生类构造函数的形式与基类的构造函数略有不同，所以让我先给你演示一下，然后我会解释为什么它是这样形成的:

```
class Square : public Shape {
private:
  int length, width;
public:
  Square(int newx, int newy, int len, int wdth)
    : Shape(newx, newy) {
    length = len;
    width = wdth;
  }
};
```

为了正确设置`x`和`y`成员变量，构造函数需要调用设置这些值的`Shape`(基类)构造函数。这是必要的，因为在基类构造函数中可能有一些专门的代码需要被调用。另外，我们不想重复调用已经被调用的代码，所以我们只调用基类构造函数，让它来完成这项工作。

请记住，基类构造函数总是在派生类构造函数之前被调用，这样就可以在移动到派生类之前设置基类成员。从语法上看，这是显而易见的，但我想让它更清楚，以防万一。

现在让我们写一个简短的测试程序，看看我们到目前为止有什么。我们可以创建一个`Square`对象，并通过调用从`Shape`继承的 draw 成员函数来“绘制”它。下面是测试的代码:

```
int main () {
  Square sq1(1,2,3,3);
  sq1.draw();
  return 0;
}
```

以下是该程序的输出:

```
Drawing a shape with origin point, x: 1, y: 2
```

这不是我们想要的。程序运行，但是 `sq1`正在为一个形状使用`draw`成员函数，它输出它正在绘制一个形状。我们希望函数输出它正在画一个正方形。我们可以通过为`Square`类定义一个新的`draw`成员函数来实现这一点，该函数将覆盖或隐藏该类从 `Shape`继承的`draw`函数。

定义如下:

```
void draw() {
  cout << "Drawing a square with origin point, x: " << x
       << ", y: " << y;
  cout << ", with length: " << length << " and width: "
       << height;
}
```

只是这个成员函数不太管用。该函数不能直接访问`x`和`y`，因为它们是`Shape`的私有函数。相反，我们需要使用 Shape 类定义中的 getters 来访问`x`和`y`。(另一种解决方案是在`Shape`中进行`x`和`y`和`protected` 的访问。我将在另一篇文章中讨论这种选择。)

下面是`Square`的`draw`成员函数的新定义:

```
void draw() {
  cout << "Drawing a square with origin point, x: " << getX()
       << ", y: " << getY() << endl;
  cout << "With length: " << length << " and width: "
       << width;
}
```

下面是使用新版本`draw`的输出:

```
Drawing a square with origin point, x: 1, y: 2
With length: 3 and width: 3
```

# 关于析构函数的快速说明

我没有花太多时间讨论析构函数，因为它们对我介绍的简单类没什么用。然而，我需要讨论派生类中使用的析构函数的一个方面。

我前面提到过，基类构造函数总是在派生类构造函数之前被调用。然而，对于析构函数，顺序是相反的。在调用基类析构函数之前调用派生类析构函数。

这里有一个简单的例子来说明这一点:

```
class Base {
private:
  string value;
public:
  Base(string val) {
    value = val;
  }

  ~Base() {
    cout << "Calling base class destructor." << endl;
  }
};class Derived : public Base {
public:
  Derived(string val) : Base(val) {
    // nothing to do here
  } ~Derived() {
    cout << "Calling derived class destructor." << endl;
  }
};int main () {
  Derived d("Test");
  return 0;
}
```

该程序的输出是:

```
Calling derived class destructor.
Calling base class destructor.
```

# 继承继续，并解释了受保护的访问

这就结束了我对 C++中对象继承的介绍。我的下一篇文章将继续这个讨论，解释受保护的访问是如何工作的，什么时候应该使用它，什么时候应该避免使用它。

感谢您的阅读，请给我发电子邮件提出意见和建议。如果你对我的在线编程课程感兴趣，请访问[https://learningcpp.teachable.com](https://learningcpp.teachable.com)。