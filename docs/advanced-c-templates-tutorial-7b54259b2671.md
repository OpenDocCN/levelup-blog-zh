# 高级 C++模板教程

> 原文：<https://levelup.gitconnected.com/advanced-c-templates-tutorial-7b54259b2671>

## 高级 C++模板概念“SFINAE”的解释

![](img/54373cce917033b15702394b678fc41a.png)

众所周知，C++包含了一个强大的特性，叫做模板。使用模板，您可以编写可重复用于不同类型的函数和类。事情很简单，直到您需要为特定类型或特定约束使用模板函数的不同实现。

假设我们定义了一个叫做 *Print* 的普通效用函数模板，如下所示，

```
#include <iostream>
#include <string>
#include <chrono>
#include <ctime>template<class T>
void Print(const T& arg) {
auto time_point = std::chrono::system_clock::now();
std::time_t ttp = std::chrono::system_clock::to_time_t(time_point);std::cout << "["<< std::ctime(&ttp) <<"] " << arg << std::endl;
}
```

*Print* 函数接受一个模板参数，并将带有时间戳的值打印到控制台。

例如，如果使用字符串和整数类型调用，输出如下所示。

```
std::string str = "Hello World!";
int i = 1;Print(str);
Print(i);## console output ##
[Sun May 31 13:03:35 2020 ] Hello World!
[Sun May 31 13:03:35 2020 ] 1
```

此外，您可以将 *Print* 函数用于您的自定义类类型，只要它们支持`operator<<`。

```
class MyClass {
 public:
  MyClass() : m_i(0) {};

  friend std::ostream & operator << (std::ostream &out, const MyClass &m);
 private:
    int m_i;
};std::ostream &operator<<(std::ostream &os, const MyClass &m) { 
    return os << m.m_i;
}
```

(这里，它为 *MyClass* 类型重载`operator<<`，并将其声明为 MyClass 的友元，以便可以从重载的`operator<<`访问私有成员 *m_i* )。输出如下所示。

```
MyClass my_class;
Print(my_class);## console output ##
[Sun May 31 13:03:35 2020 ] 0
```

到目前为止一切顺利。假设现在您只想将 MyClass 的值输出到日志文件或其他输出通道。所以你在 MyClass 中实现了 *LogMessage* 函数，如下所示。

```
class MyClass {
 public:
  MyClass() : m_i(0) {};

  void **LogMessage**() const {
  // Output to file
  }friend std::ostream & operator << (std::ostream &out, const MyClass &m);
private:
    int m_i;
};
```

现在，您需要修改 *Print* 模板函数，以便它对 *MyClass* 类型使用 *LogMessage* 函数，同时对其他类型保持使用原来的实现。下面描述了新的要求。

```
std::string str = "Hello World!";
int i = 0;
MyClass my_class;Print(str) --> Use the original Print implementation. 
Print(i) --> Use the original Print implementation.
Print(my_class) --> Use MyClass::LogMessage() function.
```

模板功能 *Print* 需要的两个实现是

```
template<class T>
void Print(const T& arg) {
auto time_point = std::chrono::system_clock::now();
std::time_t ttp = std::chrono::system_clock::to_time_t(time_point);std::cout << "["<< std::ctime(&ttp) <<"] " << arg << std::endl;
}
```

和

```
template<class T>
void Print(const T& arg) {
  arg.LogMessage();
}
```

当然，如果只是简单的写两个，编译器会给出一个错误，说模板函数 *Print* 定义了两次。此外，即使编译器没有给出错误，编译器也无法判断为哪种类型选择哪个 *Print* 函数。

这里需要注意的一点是，即使没有调用 *Print* 函数，编译器也会给出错误，所以即使是模板函数 *Print* 也没有用具体类型实例化。这种行为与编译器完成的“两阶段查找”有关。

编译模板时，编译器分两个阶段完成。在第一阶段，它解析模板代码，然后检查是否有语法错误，并尝试查找变量名和不依赖于模板参数的函数名。

在第二阶段，它用给定的具体类型实例化模板，然后再次检查代码对于给定的类型是否有效。

回到我们关于模板函数 *Print* 的问题，我们需要一些机制来让编译器根据给定的具体类型选择 *Print* 的具体实现，而不违反编译器检查的规则。

至于模板类，部分模板专门化可用于根据特定类型或约束在不同的类模板实现之间进行选择。然而，在函数模板的情况下，不允许部分模板专门化。因此，我们必须使用另一种方式来实现预期的行为。

好消息是，通过一些利用编译器 SFINAE⁴原理的技术，我们可以解决这个问题。SFINAE 原理与上述两阶段查找编译的第二阶段相关。您可以参考本文底部给出的链接了解详细信息，但总的来说，当编译器实例化具有具体类型的模板时，如果结果代码无效，它会简单地忽略结果而不给出任何错误，如果有任何更好的匹配，则继续尝试其他候选模板。

让我们看看使用 SFINAE 原理的解决方案。第一个简单的解决方案是将 *Print* 函数修改如下，

```
template <class T>
typename std::enable_if<!std::is_same<T, MyClass>::value>::type
Print(const T& arg) {
  auto time_point = std::chrono::system_clock::now();
  std::time_t ttp =  std::chrono::system_clock::to_time_t(time_point); std::cout << "["<< std::ctime(&ttp) <<"] " << arg << std::endl;
}template <class T>
typename std::enable_if<std::is_same<T, MyClass>::value>::type
Print(const T& arg) {
  arg.LogMessage();
}
```

让我们一个一个来看，

```
typename std::enable_if<!std::is_same<T, MyClass>::value>::type
```

上面一整行是为 *Print* 函数声明指定一个返回类型。 *std::is_same* ⁵检查两个类型是否相同，如果相同， *std::is_same::value* 设置为 *true* 。如果不同， *std::is_same::value* 设置为 *false* 。

至于 *std::enable_if* ，如果 *true* 传递给 *std::enable_if* ，那么它将一个类型(如果第二个模板参数中没有指定，则默认为 *void* )生成 *std::enable_if::type* 。如果*假*通过， *std::enable_if::type* 将**而非** **定义为**。在上面 *Print* 函数的声明中，根据 SFINAE 原理，如果 *std::enable_if::type* 没有定义，就意味着代码无效，因此模板实例化将被忽略。

*开头的 typename* 关键字只是告诉编译器 *std::enable_if::type* 是类型，而不是变量(从 C++14 开始，它增加了一个别名，这样更容易写成 *std::enable_if_t⁶* )。

第二个打印函数声明除了！在 *std::is_same* 之前。

```
typename std::enable_if<std::is_same<T, MyClass>::value>::type
```

即使差别很小，由于它们声明中的语法不同，编译器也不会抱怨模板 *Print* 函数被定义了两次。

使用这种方法，如果首先将 *MyClass* 作为模板参数传递，编译器会尝试用 *MyClass* 实例化第一个 *Print* 模板函数实现。由于 *std::is_same < T，my class>:value*将 *true* ，*STD::enable _ if<****！****STD::is _ same<T，my class>:::value>:*类型**将不会被定义**(注意“！”在表达式前面)，所以第一个*打印*功能实现将被忽略。

接下来，编译器尝试用 *MyClass 实例化第二个 *Print* 函数实现。*由于*STD::enable _ if<STD::is _ same<T，my class>:::value>:::type***将被定义为 *void type*** ，因此实例化成功完成。因此，第二个 *Print* 函数实现被选择用于模板参数 MyClass 类型。

另一方面，对于其他类型，适用相反的逻辑，选择第一个*打印*功能实现。实例化结果描述如下。

```
Print(my_class) instantiates tovoid Print(const MyClass& arg) {
  arg.LogMessage();
}Print(i) instantiates tovoid Print(const int& arg) {
  auto time_point = std::chrono::system_clock::now();
  std::time_t ttp =  std::chrono::system_clock::to_time_t(time_point);std::cout << "["<< std::ctime(&ttp) <<"] " << arg << std::endl;
}
```

这个天真的解决方案解决了确切的问题。然而，假设您想要将 *LogMessage* 函数添加到其他自定义类中。突然，第一个解决方案变得没有吸引力，因为您必须用上面的技术为所有其他实现 *LogMessage* 函数的定制类重复 *Print* 函数。

更好的方法可能是根据给定的模板参数类型是否有 *LogMessage()* 成员函数来选择不同的 *Print* 函数实现。第二个解决方案如下

```
template <class, class = void>
struct has_log_message : std::false_type { };

template <class T>
struct has_log_message<T, decltype(std::declval<T&>().LogMessage())> : std::true_type { };template <class T>
typename std::enable_if<has_log_message<T>::value>::type
Print(const T& arg) {
  arg.LogMessage();
}template <class T>
typename std::enable_if<!has_log_message<T>::value>::type
Print(const T& arg) {
  auto time_point = std::chrono::system_clock::now();
  std::time_t ttp = std::chrono::system_clock::to_time_t(time_point);std::cout << "["<< std::ctime(&ttp) <<"] " << arg << std::endl;
}
```

第二种解决方案在逻辑上可以分为两个部分。第一块在下面，

```
template <class, class = void>
struct has_log_message : std::false_type { };

template <class T>
struct has_log_message<T, decltype(std::declval<T>().LogMessage())> : std::true_type { };
```

这里，我们定义了一个结构模板 *has_log_message* 。它需要两个模板参数。第二个模板参数有一个默认类型 *void* 。它还指定了部分模板专门化

```
decltype(std::declval<T>().LogMessage())
```

作为第二个模板参数。 *std::declval⁷* 允许我们访问类型 *T* 的成员，就好像我们从 *T* 创建了一个对象。 *decltype* ⁷返回给定表达式的类型，在本例中，它返回 *T::LogMessage()* 函数的返回类型。如果 *T* 没有 *LogMessage()* 函数，则替换失败，模板将被忽略。

*std::true_type* ， *std::false_type* 各自定义其成员*值*为 *true* ， *false* ⁸.

因此，如果一个模板参数 *T* 被传递给 *has_log_message* 模板结构，首先，编译器试图为部分专门化的 *has_log_message* 实现实例化 *T* 。如果 *T* 有 *LogMessage()* 成员函数，则实例化成功完成。所以实例化的 *has_log_message* 结构是继承了 *std::true_type* 的结构，该结构将 *value* 成员设置为 *true* 。

如果 *T* 没有 *LogMessage()* 函数，则部分专门化的 *has_log_message* 实现的实例化失败(被忽略)，然后编译器继续使用继承 *std::false_type* 的主定义进行实例化，该主定义将*值*成员设置为 *false* 。

剩下的解决方案就是*打印*模板功能的实现。就像第一个天真的解决方案。下面一行指定了 *Print* 函数的返回类型，但是这次使用了 *has_log_member* struct 模板。

```
typename std::enable_if<has_log_message<T>::value>::type
```

与第一种解决方案一样，如果*has _ log _ message<T>:value*为 *false* ，则跳过实例化，如果*为 true* ，则产生一个类型，实例化成功完成。

综上所述，如果我们将一个具有 *LogMessage()* 函数的类型传递给 *Print* ，它将使用 *LogMessage()* ，但是如果我们将一个没有 *LogMessage()* 函数的类型传递给 *Print* ，它将使用原来的 *Print* 实现！

实例化结果描述如下。

```
std::string str = "Hello World!";
int i = 0;
MyClass my_class;
OtherClass other_class; // OtherClass has LogMessage() member.Print(str) --> Use the original Print implementation. 
Print(i) --> Use the original Print implementation.
Print(my_class) --> Use MyClass::LogMessage() function.
Print(other_class) --> Use OtherClass::LogMessage() function.
```

# 摘要

在这篇文章中，我解释了高级 C++模板概念“SFINAE ”,并举例说明了如何根据给定的模板参数来区分模板函数的实现。

[1]:模板，[https://en.cppreference.com/w/cpp/language/templates](https://en.cppreference.com/w/cpp/language/templates)

[2]:《c++模板完全指南第二版》

[3]:部分模板专门化，[https://en . CP preference . com/w/CPP/language/partial _ specialization](https://en.cppreference.com/w/cpp/language/partial_specialization)

[4]: SFINAE，[https://en.cppreference.com/w/cpp/language/sfinae](https://en.cppreference.com/w/cpp/language/sfinae)

[5]: std::is_same，[https://en.cppreference.com/w/cpp/types/is_same](https://en.cppreference.com/w/cpp/types/is_same)

[6]: std::enable_if，[https://en.cppreference.com/w/cpp/types/enable_if](https://en.cppreference.com/w/cpp/types/enable_if)

[7]: std::declval，【https://en.cppreference.com/w/cpp/utility/declval 

[8]: std::integral_constant，【https://en.cppreference.com/w/cpp/types/integral_constant】T4