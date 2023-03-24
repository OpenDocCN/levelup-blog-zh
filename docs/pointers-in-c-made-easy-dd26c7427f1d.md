# 在 C 语言中使指针变得容易

> 原文：<https://levelup.gitconnected.com/pointers-in-c-made-easy-dd26c7427f1d>

先从学习电脑内存开始吧。

# 记忆基础

当程序运行时，计算机需要保留一块内存。

计算机的操作系统完成这项任务，它在 RAM 中为程序分配一些临时内存。

1.  内存的基本单位是一个**字节**。
2.  每个字节都有一个与之相关的地址。
    这用以 0x 开始的十六进制表示，即 0xacfe24
3.  不同类型的变量占用不同的内存大小。

*   字符:1 字节
*   int: 4 个字节
*   浮点型:4 字节
*   double: 8 字节

![](img/f54c66425e6219d56369a3c5349667b5.png)

杰里米·贝赞格在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 程序存储器的结构

程序存储器分为 3 个部分:

1.  栈存储器
2.  堆内存
3.  存储器的代码部分

## 栈存储器

*   它遵循**费罗原则**(先进后出；首先存储的元素将在最后被访问)
*   它是高度有序的
*   它临时存储由函数创建的变量。
*   当一个变量被创建时，它被存储在堆栈中。一旦函数执行结束，这个内存空间就会被释放。
*   这是由编译器在编译期间(即运行时之前)执行的。这叫做**编译期/** **静态内存分配**。

## 堆内存

*   它是比堆栈更少的有序内存空间
*   它可以由程序员在程序执行时分配。这叫做**运行时/动态堆分配**。
*   堆中分配的内存不会自动释放，程序员必须显式地这样做，以避免**内存泄漏**。
*   C 语言中的`stdlib`库提供了以下函数来与堆内存交互:

1.  `malloc()`:用于在堆内存中分配特定的字节数
2.  `calloc()`:根据不同数据类型的变量数量来分配堆内存
3.  `realloc()`:用于收缩或扩展之前用`malloc()`或`calloc()`分配的内存
4.  `free()`:用于释放之前分配的内存

## 代码部分

程序执行指令存储在存储器的这一部分。

![](img/2a4bf0f140fd621006fdd5b9eb845b12.png)

照片由[阿曼达·琼斯](https://unsplash.com/@amandagraphc?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 什么是指针？

指针是一种特殊类型的整数变量。它存储一个变量的内存地址(指针所指向的)。

在 C 语言中，指针可以帮助你直接操作内存。

这在 Python 和 Javascript 这样的高级语言中是不可能的。

# 定义指针

当一个变量被定义时，一个连续的内存块被保留给它。

一个指针可以指向这个内存块的第一个字节。

可以使用以下语法定义指针:

> 数据类型*指针名称

或者

> 数据类型*指针名称

举个例子，

```
char* myPointer;
```

这将创建一个名为`myPointer`(初始化为`null`)的指针，指向一个字符。

![](img/311ef9a53417fed604524f1d774e05bf.png)

丹尼尔·塔纳塞[在](https://unsplash.com/@danielvtanase?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 给指针分配地址

为了给指针分配一个内存地址，我们使用了**引用操作符(&)。**

```
char favoriteLetter = 'a';char* myPointer = **&**favoriteLetter; //Assigns address of 'favoriteLetter' to 'myPointer'
```

`**&**favoriteLetter`告知变量`favoriteLetter`的内存地址

## 注意事项

1.  指针中分配的地址不是常数。
2.  只要变量的数据类型相同，指针就可以被重新分配给另一个变量地址。

# 打印指针的值

指针的地址可以打印如下:

```
printf("%p", myPointer);//This will print a hexadecimal address
```

# 访问指针指向的数据

这可以通过使用以下语法使用**解引用操作符(*)** 来完成:

> *指针名称

```
printf("%p", *myPointer);//Prints the data pointed by 'myPointer'
```

# 重新分配指针地址

我们将创建另一个名为`firstLetter`的变量，并使用**引用操作符(& )** 重新分配我们的指针指向这个变量的内存地址。

```
char firstLetter = 'x';myPointer = &firstLetter;printf("%p", myPointer); //Prints the memory address to variable 'firstLetter'
```

# 重新分配指针指向的值

这可以通过以下方式完成:

```
*myPointer = 'c';//Reassigns the memory address's value to 'c'
```

这将使`firstLetter`的值变为`'c'`。

![](img/c8377e50b712dfa2875cc87dfc9d8466.png)

照片由 [Jantine Doornbos](https://unsplash.com/@jantined?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 指针上的算术运算

整数的加减可以在指针上完成。

> 注意:两个指针不能相加或相减。

```
myPointer += 1; //Valid
myPointer -= 4; //Valid
myPointer *= 3; //Invalid
myPointer /= 2; //InvalidmyPointer += myPointer //Invalid
myPointer -= myPointer //Invalid
myPointer *= myPointer //Invalid
myPointer /= myPointer //Invalid
```

## 指向注释

将整数`n`加到指针上会将指针的地址值移动一段距离:`n` *(指针所指向的数据类型的大小，以字节为单位)

例如，如果一个整数`2`被加到一个指向双精度的指针上，在地址 **100** ，指针的新地址将是:`2` *字节大小`double`，即 8。

相加后的新值将是 **116** 。

# 对数组使用指针

指针可以指向数组中的值。

```
int my_array[3] = {0,1,2};
```

为了初始化一个指向索引`1`的指针，我们可以使用下面的语法:

```
int* array_pointer = &my_array[1];
```

# 在循环中使用指针

数组的值可以用指针更新，如下所示:

```
int my_array[5] = {0,1,2,3,4};int* array_pointer = &my_array[0]; //Setting the pointer to index 0 of 'my_array'for(int i=0; i <= 5; i++){
    *array_pointer = 0;
    array_pointer++;
}; //Setting all values in the array to be 0
```

![](img/bc329565d8b49060029ee80ab3409797.png)

照片由[安德·奥尔特尔](https://unsplash.com/@onderortel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 在函数中使用指针

指针可以作为参数传递给函数，如下所示:

```
void doubleNumber(int* numPointer){
    *num = *num * 2;
    printf("The value of a is %i", a);
};int main(void){    
    int num = 2;
    int* numPointer = &num; //Assigns a pointer to 'num' memory location
    doubleNumber(numPointer);
}; //Prints : The value of a is 4
```

## 指向注释

如果将整数参数作为参数传递给函数，原始变量的值不会改变。

这是因为函数会复制参数值并将其存储在参数变量中，而不是改变原始变量。

```
void doubleNumber(int num){
    num = num * 2;
    printf("The value of a is %i", a);
};int main(void){    
    int num = 2;

    doubleNumber(num); //Prints: The value of a is 4 printf("The value of a is %i", a); //Prints: The value of a is 2
};
```

![](img/ad57562b1c9ba3d3e30fe1c4d5cf8228.png)

照片由[替代代码](https://unsplash.com/@altumcode?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 对结构使用指针

“Struct”是 C 中的一种特殊数据类型，它可以包含不同数据类型的不同变量(称为**成员**)。

让我们定义一个结构`Car`:

```
struct Car{
    char name[10];
    char brand[10];
    double topSpeed;
};
```

让我们使用上面的定义初始化一个结构`car1`。

```
struct Car car1 = {
    .name = "Model A",
    .brand = "Tesla",
    .topSpeed = 32.1
};
```

我们可以为这个结构创建一个指针`car1_pointer`。

```
struct Car* car1_pointer = &car1;
```

要访问结构中的变量，我们可以使用以下语法:

```
(*car1_pointer).name;
(*car1_pointer).brand;
(*car1_pointer).topSpeed;
```

或者

```
car1_pointer->name;
car1_pointer->brand;
car1_pointer->topSpeed;
```

![](img/5a102eae032a8e932235882948883a26.png)

照片由 [DISRUPTIVO](https://unsplash.com/@sejadisruptivo?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

感谢您阅读这篇文章！:)

为了支持我在 Medium 上的工作，请鼓掌，关注我并订阅我的邮件列表。干杯！