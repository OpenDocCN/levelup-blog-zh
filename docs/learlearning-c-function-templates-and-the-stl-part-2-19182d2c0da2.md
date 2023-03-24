# 学习 C++:函数模板和 STL 第 2 部分

> 原文：<https://levelup.gitconnected.com/learlearning-c-function-templates-and-the-stl-part-2-19182d2c0da2>

![](img/eccb249765b3caf032aeceda739e6a60.png)

[黄福生](https://unsplash.com/@killerfvith?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在我的上一篇文章中，我讨论了什么是函数模板以及如何使用它们。在第二篇文章中，我将继续讨论如何使用默认的模板参数，如何重载函数模板，以及一些其他的函数模板技术。本文中的一些例子摘自 Vandevoore、Josuittis 和 Gregor 的书 *C++模板:完全指南*。

# 默认模板参数

默认模板参数是模板参数的默认*值*。可以使用这种技术来声明和定义这样的变量(给定最大值函数的正确定义):

```
auto max_value = maximum(3.14159, 3);
```

实现默认模板参数的一种方法是使用`common_type`来定义返回类型参数。这个定义是这样的:

```
template <typename T1, typename T2,
  typename RT = common_type_t<T1, T2>>
RT maximum(T1 val1, T2 val2) {
  return val1 < val2 ? val2 : val1;
}
```

下面是一个测试程序及其输出，用于演示实际运行的功能:

```
int main()
{
  int num = 3;
  double dnum = 3.14159;
  auto maxVal = maximum(num, dnum);
  cout << "The largest value of 3 and 3.14159 is: "
       << maxVal << endl;
  return 0;
}The largest value of 3 and 3.14159 is 3.14159
```

当然，您总是可以显式指定返回类型:

```
int main()
{
  int num = 3;
  double dnum = 3.14159;
  auto maxVal = maximum<int, double, double>(num, dnum);
  cout << "The largest value of 3 and 3.14159 is: "
       << maxVal << endl;
  return 0;
}
```

这个版本显然要求显式声明三种数据类型，这在大多数情况下并不是首选。

获得默认模板参数的另一种方法是将默认值放在模板参数列表中，如下所示:

```
template <typename RT = double, typename T1, typename T2>
RT maximum(T1 val1, T2 val2) {
  return val1 < val2 ? val2 : val1;
}
```

当明确存在真正的“默认”数据类型时，此选项很有意义，例如当您将整数值与浮点值组合在一起时，将浮点值作为默认值是可以接受的，因为整数将被扩大为浮点值。情况不会总是这样。

不过，要完成我们在这里试图实现的目标，最好的方法是让编译器使用`decltype`来推断返回类型。我在上一篇关于函数模板的文章中已经提到了这一点，但为了完整起见，这里还是给出了定义:

```
template <typename T1, typename T2>
auto maximum(T1 val1, T2 val2) ->
  decltype(val1 < val2 ? val2 : val1) {
  return val1 < val2 ? val2 : val1;
}
```

# 重载函数模板

模板化函数可以像普通函数一样重载。事实上，一个常规函数可以用一个模板函数重载，就像这个交换函数的例子一样:

```
void my_swap(int &val1, int &val2) {
  int temp = val1;
  val1 = val2;
  val2 = temp;
}
```

这个函数可以用一个模板重载:

```
void my_swap(int &val1, int &val2) {
  int temp = val1;
  val1 = val2;
  val2 = temp;
}template <typename T1, typename T2>
  void my_swap(T1 &val1, T2 &val2) {
  T temp = val1;
  val1 = val2;  
  val2 = temp;
}
```

下面是一个演示这两个功能的程序:

```
int main()
{
  int inum1 = 2;
  int inum2 = 3;
  cout << "inum1: " << inum1 << ", inum2: " << inum2 << endl;
  my_swap(inum1, inum2);
  cout << "inum1: " << inum1 << ", inum2: " << inum2 << endl;
  double dnum1 = 2.11;
  double dnum2 = 3.33;
  cout << "dnum1: " << dnum1 << ", dnum2: " << dnum2 << endl;
  my_swap(dnum1, dnum2);
  cout << "dnum1: " << dnum1 << ", dnum2: " << dnum2 << endl;
  return 0;
}
```

这个程序的输出是:

```
inum1: 2, inum2: 3
inum1: 3, inum2: 2
dnum1: 2.11, dnum2: 3.33
dnum1: 3.33, dnum2: 2.11
```

当然，除了说明函数可以用函数模板重载之外，没有理由使用函数的 int 版本。

一个更复杂的例子是用另一个函数模板重载一个函数模板。在下面的例子中，我们重载了一个 add 函数，这样在一个版本中，可以在运行时提供数据类型，而在另一个版本中，编译器可以推导出数据类型。下面是代码、测试程序和该程序的输出:

```
template <typename T>
T add(T val1, T val2) {
  return val1 + val2;
}template <typename T1, typename T2>
auto add(T1 val1, T2 val2) -> decltype(val1 + val2) {
  return val1 + val2;
}int main()
{
  int num1 = 2, num2 = 3;
  cout << "Sum: " << add<int>(num1, num2) << endl;
  double dnum1 = 2.1, dnum2 = 3.2;
  cout << "Sum: " << add<double>(dnum1, dnum2) << endl;
  cout << "Sum: " << add(num1, dnum1) << endl;
  return 0;
}
```

这个程序的输出是:

```
Sum: 5
Sum: 5.3
Sum: 4.1
```

# 非类型函数模板参数

函数模板的参数不一定是类型的占位符，它们也可以是值。下面是一个函数示例，该函数根据通过模板参数传入的数量以及测试程序和输出绘制成绩曲线:

```
template <int val, typename T>
T curveGrade(T grade) {
  return grade + val;
}int main()
{
  int igrade = 77;
  cout << "Not curved: " << igrade << ", Curved: "
       << curveGrade<4, int>(igrade) << endl;
  double dgrade = 76.25;
  cout << "Not curved: " << dgerade << ", Curved: "
       << curveGrade<5, double>(dgrade) << endl;
  return 0;
}Not curved: 77, Curved: 81
Not curved: 76.25, Curved: 81.25
```

传递非类型模板函数参数的一个有趣的变化是，它们也可以用来派生函数的返回类型。下面是上面使用`curveGrade`函数的一个例子:

```
template <typename T, T val = T{}>
T curveGrade(T grade) {
  return grade + val;
}int main()
{
  int igrade = 77;
  cout << "Not curved: " << igrade << ", Curved: "
       << curveGrade<int, 4>(igrade) << endl;
  return 0;
}Not curved: 77, Curved: 81
```

代码片段`T val = T{` }获取该类型的默认值，并将其赋给`val`。

你会注意到我在测试程序中没有使用 `double` 。这是因为 C++不能从非整数值派生类型。

# 转到类模板

这就结束了我对函数模板的介绍性讨论。在我的下一篇文章中，我将介绍如何用模板定义类，以获得与函数模板相同的泛型。

感谢您的阅读，请发送电子邮件提出意见和建议。