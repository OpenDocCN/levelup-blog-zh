# 你好世界！

> 原文：<https://levelup.gitconnected.com/hello-world-70a51f449d9f>

## 程序输出“Hello World！”不使用打印方法

![](img/b45c36010691bb990cd4199363441563.png)

照片由[弗拉季斯拉夫·克拉平](https://unsplash.com/@lemonvlad?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

你好世界！我相信这是一个普通的编程初学者第一次尝试用某种编程语言编码时会看到的。

因为这也是我在 Medium 的第一篇文章，我也想说`Hello world!`！

作为一个 C/C++程序员，一个简单的程序如下:

```
#include <iostream>

int main(int argc, char **argv) { 
  std::cout << "Hello world!" << std::endl; 
  return 0;
}
```

会提供输出

```
Hello world!
```

然而，如果我们只能通过使用上面的示例来产生输出，那就太无聊了，尤其是在 C/C++的世界中，我们应该知道代码背后的所有细节。

为此，我们将使用内联汇编语言在裸机版本中编译代码。

## 内嵌汇编语言

其中一个方法是有如下的`helloworld.c`

```
int main(void) {
  register int    syscall_no  asm("rax") = 1;
  register int    arg1        asm("rdi") = 1;
  register char*  arg2        asm("rsi") = "Hello world!\n";
  register int    arg3        asm("rdx") = 13;
  asm("syscall");
  return 0;
}
```

*   系统调用号被存入寄存器`rax`
*   其余的自变量被放入寄存器`rdi`、`rsi`和`rdx`
*   %rdi、%rsi、%rdx、%rcx、%r8 和%r9 用于将前六个整数或指针参数传递给被调用的函数

换句话说，上面的代码正在执行

```
write(STDOUT_FILENO,
   "Hello world!\n",
   sizeof("Hello world!\n")
);
```

## 在 Linux x86-x64 下运行代码

```
$ gcc -o helloworld helloworld.c
$ ./helloworld
```

## 无主程序

为了更有趣，我们将编写没有`main`的代码，姑且称之为`nomain`程序

该计划的切入点将是

```
void nomain() {
  print();
  exit();
}
```

`print`和`exit`功能的位置

打印功能与刚才描述的示例类似。对于`exit()`函数，它正在执行的系统调用

```
exit(EXIT_SUCCESS);
```

返回代码为 **0** ，而 **60** 是`exit`的系统调用号

要检查返回代码，我们可以键入

```
$ echo $?
```

执行完可执行文件后。

## Linux x86-x64 下正常代码的运行

```
$ gcc -c -fno-builtin helloworld.c
$ ld -static -e nomain -o helloworld helloworld.o
$ ./helloworld
```

*   **GCC** 已经提供了很多内置函数，通常为了优化的目的，它会用编译器的内部函数来代替 C 函数。例如，如果在`printf`函数中只涉及字符串，它将被替换为`puts`函数，以节省格式解释的时间。`exit()`函数是 **GCC** 的内部函数之一，因此我们必须设置`no-builtin`标志来禁用内置函数
*   然后通过`-e nomain`配置入口点

感谢阅读我的第一篇文章，希望你喜欢阅读！