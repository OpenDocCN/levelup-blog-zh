# 模板类型演绎

> 原文：<https://levelup.gitconnected.com/template-type-deduction-69330052f085>

## C++中的模板类型演绎

![](img/a9e1e182f9e8a96ccb0e99644a0b105f.png)

照片由[摄影师](https://unsplash.com/@ffstop?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

当我们谈到 C++，`auto`应该是一个引人注目的特性。尽管如此，要理解`auto`的类型演绎，我们应该先看看`template type deduction`。本帖内容借用了[有效现代 C++](https://www.oreilly.com/library/view/effective-modern-c/9781491908419/) 中的*“第 1 项:理解模板类型演绎*”。

当没有明确指定的模板参数时，会发生模板类型推导。下面是一个显示明确指定的模板参数的简单例子。

```
template<typename T>
void f(const T& param)int x = 0; 
f<double>(x);
```

为了解释更多关于模板类型推断的内容，我们将使用两个关键字，分别是`T`和`ParameterType`，如下所示

```
template<typename T>
void f(ParameterType param)
```

*   `T`是模板参数的类型，而`ParameterType`是函数参数的类型`param`
*   `T`和`ParameterType`可以是但不一定是相同的。

用于调用下面的函数`f(expr)`

```
template<typename T>
void f(const T& param)int x = 0;
f(x);
```

`T`推导为`int`，而`ParameterType`推导为`const int &`。

为`T`推导类型，不仅取决于`expr`的类型，还取决于`ParameterType`的形式。

有三种情况

*   `ParameterType`是指针或引用类型，但不是通用引用
*   `ParameterType`是通用参考
*   `ParameterType`既不是指针也不是参照物

# ParameterType 是指针或引用类型，但不是通用引用

```
template<typename T>
void f1(T&) {
  std::cout << "T is ";
  if (std::is_const_v<T>) {
    std::cout << "const ";
  }
  std::cout << typeid(T).name() << std::endl;
}
```

如果现在我们运行

```
int main() {
  int x = 27;
  const int cx = x;
  const int& rx = x;
  f1(x);
  f1(cx);
  f1(rx);
}
```

输出将是

```
T is i
T is const i
T is const i
```

在类型推导过程中，rx 的引用性被忽略，这对于指针也是一样的。

如果我们为模板函数的参数显式地写下`const`，让我们看看什么会被推导为

```
template <typename T>
void f2(const T&) {
 std::cout << "T is ";
 if (std::is_const_v<T>) {
   std::cout << "const ";
 } 
 std::cout << typeid(T).name() << std::endl;
}
```

如果我们使用`f2`再次运行代码，输出将是

```
T is i
T is i
T is i
```

对于这三种情况，`T`被推导为`int`，换句话说，被调用的函数实际上是`void f2(const int &)`

# ParameterType 是一个通用引用

*   为了通用引用，我们可以使用完美转发来检查调用的是哪种类型的函数。[完美转发](https://pinloon.medium.com/perfect-forwarding-647e1caaf879)请参考我之前的帖子
*   现在，让我们使用完美转发和一些函数重载来检查调用的是哪种类型的函数。

```
void f(int&) { std::cout << "calling f(int &)" << std::endl; }void f(const int&) { 
 std::cout << "calling f(const int &)" << std::endl;
}void f(int&&) { std::cout << "calling f(int &&)" << std::endl; }template <typename T>
void f3(T&& param) {
  f(std::forward<T>(param));
}
```

如果我们执行

```
int main() {
  int x = 27; 
  const int cx = x; 
  const int& rx = x; 
  f3(x);
  f3(cx);
  f3(rx);
  f3(27);
}
```

我们将获得以下输出

```
calling f(int &)
calling f(const int &)
calling f(const int &)
calling f(int &&)
```

保持左流和右流。

# ParameterType 既不是指针也不是引用

```
template <typename T>
void f4(T) {
  std::cout << "T is ";
  if (std::is_const_v<T>) {
   std::cout << "const "; 
  }
  std::cout << typeid(T).name() << std::endl;
}
```

当通过值传递时，`const`，`volatile`和引用将被忽略。

如果我们使用`f4`再次运行代码，输出将是

```
T is i
T is i
T is i
```

感谢阅读！代码可在[这里](https://github.com/pllee4/blog-public/tree/master/posts/005/files)获得。希望你现在能更好地理解模板类型演绎。