# 用指针回到基础

> 原文：<https://levelup.gitconnected.com/back-to-basics-with-pointers-58859c9c326b>

## 基础，编程

## 关于指针你需要知道的一切

![](img/37714b5ebddbd2584afc32b457c3ce46.png)

[阿克尔](https://unsplash.com/@aaker?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

指针是低级编程中需要理解的最重要的概念之一。我记得我试图不去想它。所以，我为初学者写了这篇文章。

> 万事开头难——歌德。

所以让我们接受挑战，进入下一个阶段。

**文章概述—**

*   **挖入变量**
*   **现在让我们瞄准指针**
*   **为什么我们需要指针**
*   让我们打破困惑
*   **最终提示和要点**

# 挖掘变量

每当我们创建一个变量时，我们必须给它分配一个*名称*、一个*数据类型*和一个*内存地址*。

**名称**用于调用或使用变量。一个**数据** **类型**定义了一个变量可以取什么样的值，ex — `int`用来存储整数，`char`用来存储字符。**内存地址**通常是变量的十六进制物理地址，它是变量在内存中的存储位置。

# 现在让我们瞄准指针

指针是一个变量，用来存储另一个变量的内存地址。

指针存储变量的第一个字节的地址，因为我们知道变量的大小，其余的字节很容易计算。

## 在 C #中声明指针

Astrix `*****`符号用于声明指针。宣言看起来像这样-

`int *p;`我们像这样声明变量— `int v;`

我们现在可以说`p`是一个'*指向 int '，*是因为类型`int` *。*它将存储整数变量的地址。

所有的指针都有相同的大小，因为它们只存储一个内存地址，这个地址对于机器来说是不变的。然而，不同的数据类型需要不同的指针声明。

```
char * character;
double * number;
float * balance;
```

这只是宣言。现在他们不储存任何价值。为此，我们必须为它们提供另一个要存储的变量的地址。在 C 语言中,“与”符号`&`又名“*地址——运算符“*”用于获取变量的地址。

```
int a = 1000; // a stores the value 1000
int *p = &a; // p stores the memory address of a. NOT 1000 // Another Wayint a = 1000;
int *p;
p = &a; // Notice * is not used again. We only use it while declaring.
```

## 取消引用指针

一旦指针被声明，下一步就是使用它。我们需要引用它所指向的值，称为指针的 ***目标*** 。这被称为 ***解引用指针。*** 为此，我们使用了 Astrix 符号`*`。

```
int * ptr;  // declaring a pointer to int
int a = 6; // a stores the value 6
ptr = &a; // p stores the memory address of a. NOT 6
printf("%d", *ptr); //prints 6\. Astrix is used to dereference the pointer
```

## 空指针

空指针是一种值为 0 的特殊指针。0 是唯一可以赋给指针的整数值，任意值都会出错。空指针是一个占位符，可以在以后用来初始化指针。下面是如何设置空指针—

```
int *p = 0; //Works fine
int *z = 100; //Error
int a = 420;p = &a; //Works fine. Now P stores the address of a
```

# 为什么我们需要指针

指针对于动态内存分配至关重要。指针提供了对变量的间接访问，这使得将变量传递给函数更加有效。

当我们把一个变量传递给一个函数时，它会作为一个函数参数被再次复制。从；外；离；除；超过；以前的

```
int a = 10;int func(int b){
//Some operation
}func(a);
```

在上面的例子中，`**a**`的值被复制到`**b**` ***。*** 如果我们假设这个程序运行的是 32 位机器，`**a**`将被分配 4 个字节，`**b**` 也将被分配 4 个字节。

在这种情况下，不存在任何问题，因为变量没有占用太多空间。但是如果我们对一个存储很多值的数组做同样的事情，我们可能会遇到障碍。

指点来了。指针使我们能够间接传递变量，因为变量的地址只有 1 个字节，所以效率更高。这是怎么做的-

```
int sum(int *a, int *b){
    int ans = *a + *b;
    return ans;
}void main(){
int a = 100;
int b = 200;
printf("%d", sum(&a,&b));  //prints 300
}
```

# 让我们打破困惑

![](img/6d4d1b5b3f4d444a9fd467bf755ce84d.png)

照片由[艾米丽·莫特](https://unsplash.com/@emilymorter?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 申报混乱

1.  **三种方法**

```
int *p;
int * p;
int* p;
```

哪个是正确的？

他们都是正确的。不同的教科书/程序员使用不同的符号。

2.**在同一条线上**

当你试图在同一行中声明 ***多个指针*** *时，另一个混淆就出现了。这是声明变量的常见做法，下面是单行变量声明的样子-*

```
int a, b, c, d; // Works fine. All of them are variables of type int
```

所以我们可能认为这是正确的-

```
int * x, y, z, g; // X is the pointer to int, but y, z, and g are variables of type int.
```

但正确的做法是这样的-

```
int *x, *y, *z, *g; // All of them are pointer to int
```

3.**指向数组的指针**

我们可能认为指向一个数组和指向任何其他变量是一样的。但事实并非如此。让我们看一看。

```
// Pointer to int
int a = 1000;     // variable of type int
int *p;          // declaring a pointer to int
p = &a;         // initialing pointer, assigning value to it// Pointer to array int* pta;
int a[] = {1,2,3,4}; //Creating an array
pta = a; //Notice we do not need '&'
```

这是因为`**a**` 本身就是指向数组第一个元素的指针。类似地，在处理 string 时不使用`&`，因为 string 只是一个字符数组。

## 解引用指针时的混乱和错误

1.  **与 Astrix 的混淆**

当我们声明一个指针时，我们使用`*****`,但是之后无论何时使用它，我们使用它**只是为了解引用。**

> 如果您没有看到' *int* '与`*`一起使用，那么指针是取消引用的。一直都是。

让我们看一个例子。假设 a 位于存储器中的位置 8999。

```
int a = 100; //creating an integer variable
int *p = &a; // creating a pointer to intprintf('%p',p); //prints 8999
printf('%d',*p); // prints 100
```

2.**分段故障**

指针的作用是引用**内存空间**中的某个地址，内存空间被分为**段**用于不同的操作。每个部分都有自己的目的，有些是*禁止的，*我们的程序不能访问。

如果一个指针指向一个极限内存位置，那么它将没有一个有效的目标。它会用一个*分段错误*错误使程序崩溃。

> 当一个程序试图访问一个不允许它访问的内存位置，或者试图以一种不允许的方式访问一个内存位置时，就会出现**分段故障**

这是如何发生的

```
int *p; //declaring a pointer
printf('%d',*p); //dereferenging a pointer with no value. Segmentation fault.
```

3.**传递一个指向函数的指针**

在函数中使用指针来引用变量是非常诱人的，为什么不呢，它带来了很多好处。但是如果没有正确实现，它可能是您的程序的噩梦。看看这个例子——

```
void sum(int *a, int *b){
 *a = *a + *b;
}void main(){
int a = 100;
int b = 200;
sum(&a,&b);printf(“%d”, a); //Prints 300
}
```

当我们传递一个指向函数的指针时，我们传递的是一个变量的内存位置。如上述示例所示，内存地址可用于永久更改变量的值。

4.**取消引用空指针**

解引用空指针会导致 ***分段错误*** 错误。为了防止错误，测试有效的目标通常是好的。

```
int* p = 0;if (p != 0 ) {
printf("%p",*p) //Dereferencing only if the condition is met
}
```

# 最后的提示和要点

指针令人困惑，但它们是你武器库中最重要的武器之一，所以要明智地使用它们。

如果你感到困惑或者不明白发生了什么，试着画一些图表，指向变量的指针。记下指针的值和目标的值。

如果你仍然不明白，不要担心，你总是有上面的部分来清除你的困惑。下面是一个小抄，复制它，忘记担心指针。

## 外卖食品

*   指针提供了间接访问变量的方法
*   指针允许你管理你的记忆
*   使用`**int ***` 声明一个指针，使用`*****`取消对它的引用。
*   要在同一行声明多个指针，在每个指针前使用`*`
*   使用`**&**`获取变量的地址
*   数组、字符串和其他多值类型不需要`**&**`来获取地址
*   取消引用没有值的指针或空指针会导致分段错误
*   内存地址可以用来永久改变变量的值，所以要小心

我希望它有帮助。对于问题、疑问或请求，请使用个人资料中的链接通过任何社交媒体平台联系我。