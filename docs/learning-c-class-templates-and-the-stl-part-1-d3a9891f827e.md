# 学习 C++:类模板和 STL 第 1 部分

> 原文：<https://levelup.gitconnected.com/learning-c-class-templates-and-the-stl-part-1-d3a9891f827e>

![](img/4fc25123208e663e094a4296521cf90f.png)

照片由[米米·蒂安](https://unsplash.com/@mimithian?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在前两篇文章中，我讨论了如何在 C++中创建和使用函数模板。在这篇文章和下一篇文章中，我将讨论如何创建和使用类模板。模板允许我们创建通用程序，这些程序可以处理不同的数据类型，而不必重载和/或复制编程代码。泛型编程是 C++标准模板库(STL)的核心，理解如何使用函数和类模板是有效使用 STL 的基础。

# 定义类模板—堆栈类

套用已故计算机科学家西蒙·派珀特的一句话，没有编程就很难谈论编程。对于我的类模板示例，我将首先定义一个`Stack`类，然后定义一个模仿 C++的`pair` 结构的`Pair`类。

正如许多人所知，堆栈是一种数据结构，其中数据被输入到堆栈的顶部(通过`push`成员函数)，而数据只从堆栈的顶部移除(通过`pop`成员函数)。唯一需要的另一个主成员函数是一个`top`函数，它检查并返回栈顶的数据元素。

栈可以用于所有数据类型，这种能力有助于通用实现。我们可能需要一个整数堆栈，或者字符串堆栈，或者浮点值堆栈。为每种数据类型创建单独的实现是不切实际的，因此模板定义是必要的。

我将通过展示如何将一个类定义为类模板来开始类定义。这看起来很像你第一次定义一个函数模板。下面是我如何开始这个`Stack`类的:

```
template <typename T>
class Stack {
  // declarations and definitions
};
```

从现在开始，每当我们需要引用一个数据类型，或者引用进入该类的数据或者从该类发送的数据时，我们将使用`T`占位符来键入数据。下面是`Stack`类的完整声明:

```
template <typename T>
class Stack {
  private:
    vector<T> data;
  public:
    void push(T element);
    bool pop();
    T top();
};
```

您可以看到，保存堆栈数据的向量是用`T`键入的，`push`成员函数的参数和`top`成员函数的返回类型也是如此。您还应该注意到，为了节省空间，我没有为该类定义构造函数，也没有定义任何实用函数，这并不是因为我认为它们对于一个完整的堆栈类定义来说是不必要的。

现在让我们看看`Stack`类的定义:

```
template <typename T>
void Stack<T>::push(T const& element) {
  data.push_back(element);
}template <typename T>
bool Stack<T>::pop() {
  if (!data.empty()) {
    data.pop_back();
    return true;
  }
  return false;
}template <typename T>
T const& Stack<T>::top() {
  return data.back();
}
```

请特别注意模板参数占位符在定义部分中的使用位置。每次提到类名时，以及每当它需要作为参数类型或返回类型的占位符时，都必须将它放在括号中。

下面是一个将`Stack`类用于整数的程序:

```
int main()
{
  Stack<int> numbers;
  numbers.push(1);
  numbers.push(2);
  cout << "The top of the stack" " << numbers.top() << endl;
  numbers.push(3);
  cout << "The top of the stack: " << numbers.top() << endl;
  numbers.pop();
  cout << "The top of the stack: " << numbers.top() << endl;
  return 0;
}
```

您需要注意的一件事是，如果您不提供模板参数，程序将无法编译。与函数模板不同，在函数模板中，编译器可以从参数中推导出数据类型，而在类模板中，编译器无法推导出类的类型，因为没有要推导的参数。

# 第二个例子:Pair 类

`Pair`是一个保存两个数据元素的类。这些数据元素可以是任何类型，也可以是不同的类型。我的定义将模仿 STL 中的`pair`结构，并用于 STL 容器，如 map。

该类的两个成员变量是`first`和`second`。为了更接近地模仿 STL 版本，这些将在`public`部分声明，这样它们可以直接从`pair`对象访问。(由于成员变量的公共访问，我也可以将这个类实现为一个结构。)

因为这是一个简单的类，所以声明也是对`Pair` 类的定义:

```
template <typename T1, typename T2>
class Pair {
  public:
    T1 first;
    T2 second;
};
```

`pair` struct 有一个帮助生成 pair 对象的函数，`make_pair`。这是我的版本的定义，叫做`makePair`:

```
template <typename T1, typename T2>|
Pair<T1, T2> makePair(T1 first, T2 second) {
  Pair<T1, T2> p;
  p.first = first;
  p.second = second;
  return p;
}
```

这需要一个函数模板，以便我们可以根据需要更改第一个和第二个类型。

下面是一个使用`Pair`类和`makePair`函数的示例程序:

```
template <typename T1, typename T2>
class Pair {
  public:
    T1 first;
    T2 second;
};template <typename T1, typename T2>
Pair<T1, T2> makePair(T1 first, T2 second) {
  Pair<T1, T2> p;
  p.first = first;
  p.second = second;
  return p;
}int main()
{
  Pair<string, int> nameAge;
  string name = "Mary";
  int age = 23;
  nameAge = makePair<string, int>(name, age);
  cout << "Name: " << nameAge.first << endl;
  cout << "Age: " << nameAge.second << endl;
  return 0;
}
```

以下是该程序的输出:

```
Name: Mary
Age: 23
```

为了说明类模板为什么有用，下面是另一个使用这个类和函数的程序:

```
int main()
{
  Pair<double, string> ratios;
  double r = 3.14159;
  string name = "pi";
  ratios = makePair(r, name);
  cout << "Value: " << ratios.first << endl;
  cout << "Name: " << ratios.second << endl;
  return 0;
}
```

以下是该程序的输出:

```
Value: 3.14159
Name: pi
```

# 类模板的价值

像函数模板一样，类模板使 C++中的泛型编程成为可能。泛型程序比其他编程范式更灵活，因为正如标准模板库所示，我们可以以各种方式混合和匹配容器(数据结构)和算法(函数)，这在其他范式中是不可能的。

还有更多关于类模板的主题，我将在下一篇文章中讨论其中的几个主题。

感谢您的阅读，请给我发电子邮件提出意见和建议。