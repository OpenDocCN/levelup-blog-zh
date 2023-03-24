# 学习 C++:指针第 1 部分

> 原文：<https://levelup.gitconnected.com/learning-c-pointers-part-1-aa2f394912d0>

![](img/118c7c79397168664bedeb784dd07e56.png)

[娜塔莉·斯佩纳](https://unsplash.com/@nathalie_spehner?utm_source=medium&utm_medium=referral)在[号宇宙飞船](https://unsplash.com?utm_source=medium&utm_medium=referral)上拍摄的照片

对于学习 C++的学生来说，最可怕和最令人困惑的话题之一是指针。在本文中，我将讨论什么是指针以及如何创建和使用它们。在我的下一篇文章中，我将讨论指针的一些更深奥的特性，并演示如何使用指针来创建动态数组。

# 内存地址

在讨论指针之前，我需要讨论变量和内存地址。变量是计算机内存位置的名称。内存位置用十六进制值命名，如 0x6dfeec。直接使用十六进制内存地址太难了，所以编程语言使用变量名来代替。

在 C++中，通过对变量名应用 address-of 运算符(`&`)可以找到内存地址的十六进制值。下面是一个例子:

```
int number = 1;
cout << &number; // displays 0x6dfeec on my computer
```

操作符的地址在使用指针时将发挥重要作用。

# 定义的指针

指针是存储内存位置的变量。使用指针，您可以访问变量的内存位置，也可以使用特殊语法访问存储在该内存位置的值。有些编程技术需要使用指针，而有些事情使用指针更容易或更有效。

# 创建指针和访问指针值

指针变量是一个保存内存地址的变量，可以访问存储在该地址的值。指针变量使用指针变量名前的星号声明。以下是用于声明新指针变量的语法模板:

*数据类型*指针名称；*

星号告诉编译器被声明的变量是一个指针。以下是一些示例:

```
int *ptr_integer;
double *ptr_double;
string *ptr_string;
```

一旦指针变量被声明，它必须用内存地址初始化。以下是指针初始化的语法模板:

*指针变量= &变量名；*

与变量组合的运算符的地址被分配给指针变量。以下是一些示例:

```
int number = 22;
ptr_integer = &number;
string name = "Jane";
ptr_string = &name;
```

一旦我们声明了一个指针并用内存地址初始化它，我们就可以访问它的值。这是第一次尝试，从上面使用指针变量`ptr_integer`:

```
cout << ptr_integer;
```

我电脑上这条线的输出是:

```
0x6dfee8
```

发生了什么事？当我们给一个指针变量分配一个内存地址时，这个地址就是指针变量的直接值。如果我们想要访问存储在地址中的值，我们必须使用解引用操作符(`*`)来访问值而不是地址。所以要得到存储在内存地址的值，我们必须写:

```
cout << *ptr_integer;
```

输出是:

```
22
```

我们可以通过使用赋值语句左侧的间接操作符为指针变量赋值:

```
*ptr_integer = 100;
cout << *ptr_integer << endl; // displays 100
```

当使用间接操作符访问一个指针变量时，它被称为解引用指针。有时你会听到间接操作符，也称为解引用操作符，我会交替使用这两个术语。

# 声明指针变量而不初始化它

你可以声明一个指针变量，而不是立即用内存地址初始化它。这种类型的指针称为*空指针*。当您这样做时，您应该将它初始化为内置常量`nullptr`。这里有一个例子:

```
int *ptr = nullptr;
```

从 C++11 开始，`nullptr`常量只出现在 C++中。以前的 C++版本允许您将空指针设置为 0，如下所示:

```
int *ptr = 0;
```

或者您可以使用一个特殊的 C++宏`NULL`，如下所示:

```
int *ptr = NULL;
```

这两种初始化空指针的方法都会导致程序中的二义性，所以从 C++11 开始，应该使用`nullptr`来初始化空指针。

# 指针和数组

指针和数组在 C++中有一种特殊的关系。数组名是指向数组第一个元素的指针，指针可以用作数组名。让我们更仔细地研究一下这些事实。

下面是一个数组，已声明并初始化:

```
const int numElements = 5;
int numbers[numElements] = {2,4,6,8,10};
```

我可以通过对数组名应用解引用操作符来访问数组的第一个元素，如下所示:

```
cout << "First element: " << *numbers << endl;
// displays First element: 2
```

我可以使用以下语法显示第二个元素:

```
*(numbers + 1)
```

我们可以对此进行归纳，以便可以使用以下语法访问任何元素:

**(数组名+ n)*

为了演示这一点，下面的代码使用数组名作为指针来显示数组的所有元素:

```
int main ()
{
  const int numElements = 5;
  int numbers[numElements] = {2,4,6,8,10};
  for (int i = 0; i < numElements; i++) {
    cout << *(numbers + i) << " ";
  }
  return 0;
}
```

与常规数组访问一样，注意不要让代码超出数组的界限。这就是为什么我总是用数组元素的数量来指定一个常量，并在数组访问循环中使用它。

# 指针算法

另一种遍历数组的方法是创建一个指向数组的指针，并对指针执行算术运算。这里有一个例子:

```
int main ()
{
  const int numElements = 3;
  string names[numElements] = {"Meredith", "Allison", "Mason"};
  string *names_ptr = names;
  cout << "First name: " << *names_ptr << endl;
  names_ptr++;
  cout << "Second name: " << *names_ptr << endl;
  return 0;
}
```

声明一个指针，并为其分配数组名。然后使用指针从第一个数组元素移动到第二个数组元素。您可以使用这种技术来遍历整个数组，如下所示:

```
for (int i = 0; i < numElements; i++) {
  cout << *names_ptr << " ";
  names_ptr++;
}
```

您可以使用指针算法在数组中向后移动，如下例所示，由于上例的延续，指针指向最后一个元素:

```
for (int i = 0; i < numElements; i++) {
  names_ptr--;
  cout << *names_ptr << " ";
}
```

指针在显示名称之前递减，因为当循环停止时，指针指向数组的末尾。

# 比较指针

可以使用关系操作符对指针进行比较。这不同于比较指针所指向的值。当比较“欠引用”指针时，内存地址就是被比较的值。比较两个不相关变量的内存地址没有多大意义，但是有理由比较数组元素的内存地址。

让我们首先创建一个包含一些数字的数组:

```
const int numElements = 5;
int numbers[numElements] = {23, 1, 45, 67, 13};
```

我们可以验证第一个数组元素的地址是否小于第二个数组元素的地址，如下所示:

```
if (numbers < numbers+1) {
  // do something
}
```

我们可以使用任何关系运算符来执行我们需要的任何类型的比较。

指针比较的一个用途是用它来控制遍历 while 循环，如下例所示:

```
int main ()
{
  const int numElements = 5;
  int numbers[numElements] = {23, 1, 45, 67, 13};
  int *ptr = numbers;
  while (ptr < &numbers[numElements]) {
    cout << *ptr << " ";
    ptr++;
  }
  return 0;
}
```

这个程序的输出是:

```
23 1 45 67 13
```

# 指向下一篇文章

在我下一篇关于指针的文章中，我将讨论常量指针，指向常量指针的指针作为函数参数，以及使用指针实现动态数组。

感谢您的阅读，请给我发电子邮件提出意见和建议。如果你对我的在线编程课程感兴趣，请访问[https://learningcpp.teachable.com](https://learningcpp.teachable.com)。