# Python 优化—实习

> 原文：<https://levelup.gitconnected.com/python-optimizations-216205001b83>

![](img/a4b0ff6619ae82205110251476a7e02d.png)

你听说过 Python 中实习的概念吗？

实习是 Python 优化程序使用的内存的方式，通过引用与先前创建的变量具有相同数据的*数字*(有一些例外)或*字符串*变量的内存中的相同空间。我们稍后会更深入地探讨。

## 变量

在我们开始深入探讨实习之前，我想先回顾一下什么是变量。

变量只是一个指针，指向内存中存储某个对象的空间。例如，如果你声明一个变量并给它赋值一个整数，那么这个变量就只是一个指针，指向内存中保存这个整数的空间。

当你声明一个变量`x = 5`时，它实际上并不意味着`x`就是字面上的`5`，它所发生的是`x`的值是某个存储数字`5`的地址。

如果您编写以下代码:

```
x, y = 5, 500*# hex(id(x)) - returns the memory address where the variable is pointing to in hexadecimal*print(**"The id for x is: {}"**.format(hex(id(x))))
print(**"The id for y is: {}"**.format(hex(id(y))))
```

在我的情况下，它打印:

```
The id for x is: 0x106d7d060
The id for y is: 0x105897f90
```

如果你编译上面的代码，你会注意到变量`x`在你的计算机中有相同的地址，但是变量`y`没有。我将在下一节解释为什么会发生这种情况。

现在我们知道了变量是指向内存地址的指针，我们可以更深入地了解实习以及 Python 如何处理它。

## 整数内部化

基本上 Python 对*整数*所做的就是自动将最常见的*整数*保存在内存中，那些*整数*在`[-5, 256]`之间。每当你声明一个在这个范围内的变量时，Python 就会指向内存中预分配的空间，例如，如果你有这样的代码:

```
a = 5

b = 5

c = a

d = b

print(**"The id for a is: {}"**.format(hex(id(a))))
print(**"The id for b is: {}"**.format(hex(id(b))))
print(**"The id for c is: {}"**.format(hex(id(c))))
print(**"The id for d is: {}"**.format(hex(id(d))))
```

输出将是这样的:

```
The id for a is: 0x105e13060
The id for b is: 0x105e13060
The id for c is: 0x105e13060
The id for d is: 0x105e13060
```

如您所见，所有 4 个变量都指向同一个地址，但如果您像这样更改其中一个变量的值，会发生什么情况:

```
a = 5

b = a

print(**"The id for a is: {}"**.format(hex(id(a))))
print(**"The id for b is: {}"**.format(hex(id(b))))b = 340print(**"The id for b is: {}"**.format(hex(id(b))))
```

那么它将首先打印

```
The id for a is: 0x105e13060
The id for b is: 0x105e13060
```

然后它会在内存中分配空间来存储那个`340`，并让变量指向那个地址。在我的例子中，输出如下:

```
The id for b is: 0x105525fd0
```

如果我们有另一个具有相同值`340`的变量，那么 Python 会让这个新变量指向相同的地址，以重用内存中的空间。

## 字符串实习

*字符串* *内定*和*整数* *内定*几乎一样。Python 所做的是，每次声明一个新变量时，如果该字符串是与前一个完全相同的*字符串*，那么它会将之前创建的*字符串*的地址分配给新变量。

```
a = **'hello_world'** b = **'hello_world'** c = **'Hello_world'** d = **'hello world this is a long long string that python will intern to make the app work faster'** e = **'hello world this is a long long string that python will intern to make the app work faster'** print(**"The id for a is: {}"**.format(hex(id(a))))
print(**"The id for b is: {}"**.format(hex(id(b))))
print(**"The id for c is: {}"**.format(hex(id(c))))
print(**"The id for d is: {}"**.format(hex(id(d))))
print(**"The id for e is: {}"**.format(hex(id(e))))
```

在我的例子中，这产生了以下输出:

```
The id for a is: 0x107702930
The id for b is: 0x107702930
The id for c is: 0x1077029f0
The id for d is: 0x10764f930
The id for e is: 0x10764f930
```

如你所见，如果字符串完全匹配，内存地址将是相同的。

使用*字符串*的一个优点是，当比较两个*字符串*而不是用`==`操作符比较它们时，你可以使用关键字`is`。不同之处在于，`==`将逐个字符进行比较，如果第一个字符串的每个字符都与第二个*字符串*的字符相匹配，那么它将返回`true`，但是使用`is`关键字，它将只比较内存地址，这要快得多。也许在小文本中你甚至不会注意到差别，但是如果你必须处理一个非常大的文本，那么你会开始看到很大的差别。

```
**import** time

**def** compare_strings():
    d = **'hello world this is a long long string that python will intern to make the code work faster'***10000
    e = **'hello world this is a long long string that python will intern to make the code work faster'***10000
    **for** i **in** range(100000):
        **if** d == e:
            **pass** start = time.perf_counter()
compare_strings()
end = time.perf_counter()
print(**"Elapsed time {}"**.format(end-start))
```

在前面的代码中，我们使用`==`比较两个字符串，在我的电脑中用了 4.73 秒，但在这种情况下，知道字符串是相同的，并且知道 python 会对它们进行整型，我们可以使用`is`关键字进行比较，如下所示:

```
**import** time

**def** compare_strings():
    d = **'hello world this is a long long string'***10000
    e = **'hello world this is a long long string'***10000
    **for** i **in** range(100000):
        **if** d **is** e:
            **pass** start = time.perf_counter()
compare_strings()
end = time.perf_counter()
print(**"Elapsed time {}"**.format(end-start))
```

在这种情况下，我的计算机上的运行时间只有 0.004 秒，所以在比较真正长的*字符串*时，这确实有很大的不同。

## 结论

*实习*是一种通过引用相同的内存地址来优化程序的方法。特别是*string**interning*非常有用，因为你可以通过改变一个简单的语句来优化你的代码。

字符串实习的一个应用是自然语言处理，其中你必须对单词和短语进行大量评估。

如果你对 Python 优化感兴趣，你可以看看我写的关于 [Python 优化(窥视孔)](https://medium.com/@gmotzespina/python-optimizations-a822db1f6bf5)的文章。