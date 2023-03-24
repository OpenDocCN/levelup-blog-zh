# 学习 C++:类定义中的 Getters 和 Setters

> 原文：<https://levelup.gitconnected.com/learning-c-getters-and-setters-in-class-definitions-c7c0469fb63c>

![](img/725d128b376c7b95066c915a91eef056.png)

[法托斯 Bytyqi](https://unsplash.com/@fatosi?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在 C++中对对象使用类的一个好处是你可以通过私有访问隐藏成员变量。但是，这样做就不可能从类定义之外访问这些变量或更改它们的值。这个问题的一个解决方案是在类接口中提供一组成员函数，称为 getters 和 setters。我将在本文中介绍如何创建和使用 getters 和 setters。

# 为什么类需要 Getters 和 Setters

设计 C++类时的惯例是将成员变量设为私有，以控制对它们的访问。使用数据隐藏，您可以编写代码来检查进入类的数据，以确保在将数据赋给成员变量之前数据是有效的。

例如，如果一个类存储一个人的年龄，通过将成员变量标记为 private，您可以通过一个函数提供对该变量的访问，该函数首先检查以确保传入的数据是有效的年龄。如果没有，您可以指定一个默认值，或者要求用户再次输入数据。

如果没有这种检查，为年龄输入的数据可以是编译器允许的该类型的任何合法值。因此，传递给整数变量的年龄可以是 34，也可以是 123，345，因为这两个值都是有效的整数值。数据验证函数可以阻止非法值进入对象并保持数据的完整性。

因为标记为 private 的成员变量无论如何都不能被访问，所以类还必须提供从对象中检索成员变量中存储的数据的方法，如果这是设计的一部分的话。这个功能对于维护数据完整性来说并不重要，但是它通常是一个实用的必需品。

通过提供 getter 和 setter 成员函数作为类接口的一部分，我们的面向对象程序可以满足这些数据设置和数据检索的需要。

# 创建 Setter 函数

让我们从定义一个用于本文的简单的`Person`类开始。这个类将有两个成员变量— `name`和`age`。`age`成员变量将需要一些数据验证，因为年龄不能小于 0，应该有一个大约 120 的上限。

让我们从这个类的基本定义开始:

```
class Person {
private:
  string name;
  int age;public:
  Person(string n, int a) {
    name = n;
    age = a;
  } void display() {
    cout << name << ", " << age << endl;
  }
};
```

现在让我们为这个类设计一个 setter 函数。该函数需要检查作为参数传入的年龄，以确保它大于或等于 0 且小于或等于 120。我还将任意决定，如果输入的年龄无效，该函数将把年龄设置为 0。

以下是`setAge`函数的定义:

```
void setAge(int a) {
  if ((a >= 0) && (a <= 120)) {
    age = a;
  }
  else {
    age = 0;
  }
}
```

只需将这个定义添加到类定义中。下面是一个测试我们新的 setter 函数的简短程序:

```
int main ()
{
  Person me("Jane Doe", 34);
  me.display();
  cout << endl;
  me.setAge(121);
  me.display();
  return 0;
}
```

这个程序的输出是:

```
Jane Doe, 34
Jane Doe, 0
```

既然我们已经编写了这个 setter 函数，我们可以在其他地方重用它，比如在构造函数中:

```
Person(string n, int a) {
  name = n;
  setAge(a);
}
```

我们还需要在构造函数中验证这个函数的数据。下面是检查新构造函数定义的测试程序:

```
int main ()
{
  Person you("Bobby McGee", -1);
  you.display();
  return 0;
}
```

这个程序的输出是:

```
Bobby McGee, 0
```

让我们通过为 name 成员变量定义一个 setter 函数来总结一下`Person`类的 setter 函数:

```
void setName(string n) {
  name = n;
}
```

这就是全部了。出于一致性目的，我们也应该将该函数添加到我们的构造函数中，如下所示:

```
Person(string n, int a) {
  setName(n);
  setAge(a);
}
```

在我讨论完 getter 函数之后，我将展示完整的类定义。

# 创建 Getter 函数

Getter 函数更容易定义，因为它们只需要检索存储在类对象中的数据。下面是`Person`类的 getter 函数:

```
string getName() {
  return name;
}int getAge() {
  return age;
}
```

下面是完整的`Person`类定义，包括所有的 getter 和 setter 函数:

```
class Person {
private:
  string name;
  int age;public:
  Person(string n, int a) {
    setName(n);
    setAge(a);
  } void display() {
    cout << name << ", " << age;
  } void setAge(int a) {
    if ((a >= 0) && (a <= 120)) {
      age = a;
    }
    else {
      age = 0;
    }
  } void setName(string n) {
    name = n;
  }

  string getName() {
    return name;
  } int getAge() {
    return age;
  }
};
```

# 包扎

Getter 和 setter 函数是对类接口的重要补充。因为一个类的成员变量将被标记为私有的，你的类的用户将需要一些方法来检索和设置它们的值。Getter 和 setter 函数以安全的方式提供了这种访问，因为 setter 函数可以用包含的数据验证代码来编写，以确保成员变量被设置为有效值。

当然，有些情况下，您不想为类中的所有成员变量提供 getter 或 setter 函数，或者两者都提供。可以检索但不能设置的成员变量称为只读成员变量。这种成员变量的一个例子可能是在程序运行期间跟踪对象实例数量的静态类变量。该值应该是只读的，这样用户就不会意外地更改该值。

感谢您阅读本文，请发邮件至 mmmcmillan1@att.net[给我，并提出意见和建议。如果你对我的在线编程课程感兴趣，请访问](mailto:mmmcmillan1@att.net)[https://learningcpp.teachable.com](https://learningcpp.teachable.com)。