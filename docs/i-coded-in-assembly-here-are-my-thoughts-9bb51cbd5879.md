# 我用汇编语言编码。以下是我的想法。

> 原文：<https://levelup.gitconnected.com/i-coded-in-assembly-here-are-my-thoughts-9bb51cbd5879>

## 长话短说，我感谢 Python

编程很容易下兔子洞。我陷入兔子洞的问题让我选择了一门大学课程，在这门课程中，我认为兔子洞是编程的极限；汇编语言。除非你用 0 和 1 编码，否则它不会比汇编语言更低级。(我对用 0 和 1 编码不感兴趣……那听起来不好玩。)

![](img/9fd3d11c687dac5a0f741c23ae0ccaa4.png)

引用 Carl Sagon 或任何汇编程序员的话

这门课程，计算机组织和汇编语言触及了核心计算机语言。在这学期中，我们谈到了将二进制转换成十进制。我们使用二进制补码转换数字以获得负数，我们转换 IEEE 754 浮点单精度点和双精度点中的数字。我们也在 k 线图上做了大量的改动。

我认为了解幕后发生的事情是很好的练习。对转换数字和创建 K 图的课程概念进行实践是很重要的，即使你在当今时代编码时不会用到它。我认为这些基本原则是开发人员职业生涯的重要基础。

除了学习核心概念，我们有一个项目，我们必须在火星 MIPS 编码。

对于我们的项目，我们必须创建一个递归循环，将所有的数字从 1 加起来，直到用户输入的数字。如果用户输入一个十进制数，程序必须通知用户他们输入了一个浮点数，程序会将其截断。

您可以通过以下方式运行该程序:

首先下载 JAR 文件:[https://docs . Oracle . com/javase/8/docs/technotes/guides/JAR/index . html](https://docs.oracle.com/javase/8/docs/technotes/guides/jar/index.html)

然后克隆代码所在的 [Github](https://github.com/ilknureren/assembly-project/blob/main/index.txt) 项目。

## 项目代码:

```
.data
Prompt: .asciiz        "\n Please input value for N = "
Result: .asciiz        "\n The sum of integers from 1 to N is "
FB_num: .asciiz        "\n.You entered a Floating point number. It  has been truncated and we will be using:"
Bye:    .asciiz        "\n *** Adios Amigo - Have a good day***"     .globl                  main
.text

main:
 li $v0, 4       # Syscall print string
 la $a0, Prompt  # load address of prompt into $a0
 syscall         # 
 li $v0, 6       # read n into $v0 Outline 18 - number goes to f0
 syscall         # reads the value of N into $v0
 li $t0, 0       # Initialize $t0 to 0#Move the FB number into an integer register, No conversion #t1 is now the ieee - integer representation
mfc1 $t1, $f0srl $t2, $t1, 23#Subtract 127 to get the exponent
addi $s3, $t2, -127 #this is the exponent#Get the fraction
sll $t4, $t1, 9 #Shift out exponent and sign bit
srl $t5, $t4, 9 #Shift back to original position. This will make all previous bits 0
add $t6, $t5, 8388608 #add the implied bit to bit nnumber 8 (2^23)
addi $t7, $s3, 9 #add 9 to the exponent
sllv $s4, $t6, $t7 #by shifting to the left 9 + exponent. if this number NE 0 then there is a fraction. Shifted out the ine
#now get the integer
rol $t4, $t6, $t7 #rotate the bits left by $t7 to get the integer portion in the right most bits
#shift left 31 -exp to zero out the other bits
li $t5, 31
sub $t2, $t5, $s3 #shift the value
sllv $t5, $t4, $t2 #zero out the fraction part
srlv $s5, $t5, $t2 #reset integer bits. integer is in $s5
move $v0, $s5 #move the integer into V0li $t0, 0 #zero out T0
blez  $t1, End Loop:
 add $t0, $t0, $v0 # sum of integers in register &t0
 addi  $v0, $v0, -1 # summing integers in reverse order
 bnez $v0, Loop # branch to loop if $v0 is != 0

 beq $s4, $0, ENDIF
 li $v0, 4  # system call code for Print String
 la $a0, FB_num # load message if its a floating number
 syscall   # Print the string 

 li $v0, 1  # system call code for Print Integer
 move $a0, $s5 # load message if its a floating number (s5)
 syscall     # Print sum of integersENDIF: li $v0, 4  # system call code for Print String
 la $a0, Result # Print the result
 syscall   # Print the string 

 li $v0, 1  # system call code for Print Integer
 move $a0, $t0 # move value to be printed to $a0
 syscall   # Print sum of integers
 b       main  # branch to main

End:
 li $v0, 4  #system call code for Print String
 la $a0, Bye # load address of msg. into $a0
 syscall   # print the string
 li $v0, 10  # terminate program run and
 syscall   #return control to system
```

如果你看汇编代码，有些概念类似于 Python。

比如在 Python 中，如果你想打印什么东西，可以使用，`print`命令，而在汇编语言中，你可以通过键入`syscall`来打印。这是 Python 和更高级编程语言更容易读写的一个例子。

`if/else`Python 和汇编语言中的结构非常相似。在我的代码中，我有一个例子，如果用户输入了一个浮点数，代码将进入 if 语句，如果没有，代码将进入`else`。

在汇编语言中，你必须非常注意你的值存储在哪个寄存器中。你还必须非常注意你存储的变量的类型。在 python 中，我们不需要非常注意确保我们存储的是字符串还是整数，但是在其他低级语言中，比如 C#，我们需要这样做。

## 摘要

总之，我对汇编语言的基本结构和思维过程与 Python 等其他编程语言如此相似感到惊讶。我们有`if/else`语句，我们可以存储变量，我们可以打印它们。基本概念和结构是一样的。

然而，我非常感谢今天的标准编程语言。例如，Python 使得汇编编程很难做到的事情变得更容易。存储变量更容易，不用担心值存储在哪个寄存器中。也比较好读。

这让我想知道，未来的编程语言会有多简单。