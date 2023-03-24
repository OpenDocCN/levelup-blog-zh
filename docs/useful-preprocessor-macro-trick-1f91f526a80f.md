# 有用的预处理宏技巧

> 原文：<https://levelup.gitconnected.com/useful-preprocessor-macro-trick-1f91f526a80f>

有效的预处理器元编程技巧

![](img/8127bb8bb605994424c8b4dc15c74682.png)

C/C++预处理器是用于在编译过程之前处理源代码的工具。预处理器处理源代码中以散列(`#`)符号开始的某些指令。有几个预处理指令。最著名的可能是用于包含特定文件的`#include`指令。

```
#include <iostream> // Includes standard library *iostream* header
```

此外，还有`#if`、`#ifdef`、`#ifndef`、`#elif`、`#endif`指令，它们可以作为预处理程序指令的其他示例用于条件编译。在这篇文章中，我决定谈谈`#define`指令及其用法，因为在所有的指令中，`#define`指令的某些用法特别有争议，在某些情况下，使用它被认为是一种不好的做法，但在实践中，它仍然被广泛使用，我喜欢提出一些它的用法变得有利的情况。有关其他类型的预处理程序指令的详细信息，请参考文档。

# 预处理宏

`#define`指令用于定义*宏*。宏是已经命名的代码片段。无论何时使用宏，它都会被宏的内容替换。简单的例子如下所示。

```
#define PI 3.14double radius = 2.0;
double area = PI * radius * radius;
```

`PI`是一个被归类为类对象宏的宏。它将被预处理器在代码中使用的标记 *3.14* 所取代。

另一种类型的宏称为类似函数的宏，如下所示

```
#define max(a, b) ( ((a) > (b)) ? (a) : (b) )

int a = 5;
int b = 4;

int c = max(++a, b); // c becomes 7 not 6!
```

实际上，这个例子经常被用来解释为什么类似函数的宏会带来混乱的效果并使代码容易出错。在上面的例子中，在最后一次给 *c.* 赋值后，你会期望 *a* 和 *c* 的值是 6，然而，这些值是 7 而不是 6。这是因为宏展开后的代码如下所示，因此

```
int c = ( ((++a) > (b)) ? (++a) : (b) );
```

*a* 的值增加两次，变成 7 而不是 6。当宏的用户希望它像正常的函数调用一样工作时，这可能会有问题。

此外，还有其他原因可能使宏不受欢迎:对于人和调试器来说，用宏调试代码的难度增加，宏没有作用域的概念，它不是类型安全的，等等。

然而，尽管使用宏有一些缺点，但只要小心使用，在某些情况下宏会是一个有用的工具。

# 为 RAII 对象创建唯一的名称

有效使用宏的一个例子是创建从不被直接引用的唯一命名的变量，特别是当这些变量与 RAII⁴技术一起使用时。现在，我用一个来自 Andrei Alexandrescu 和 Petru Marginean 的 scopeguard⁵·习语的例子来解释这个用例。ScopeGuard 习语的思想是让 Guard 对象的析构函数在作用域的末尾调用用户指定的清理动作。注意:为了简化，我使用了 ScopeGuard 习语的一个非常粗糙的实现，以便我们可以专注于宏的使用。实现如下所示，

```
#define CONCATENATE_IMPL(s1, s2) s1##s2
#define CONCATENATE(s1, s2) CONCATENATE_IMPL(s1, s2)#ifdef __COUNTER__
#define ANONYMOUS_VARIABLE(str) \
CONCATENATE(str, __COUNTER__)
#else
#define ANONYMOUS_VARIABLE(str) \
CONCATENATE(str, __LINE__)
#endifnamespace scope_guard {template <class Func>
class ScopeGuard {
 public:
  ScopeGuard( Func const& cleanup ) 
           : cleanup_( cleanup ) {}

  ~ScopeGuard() { cleanup_(); } private:
  Func cleanup_;
};enum class ScopeGuardOnExit {};template <typename Fun>
ScopeGuard<Fun> operator+(ScopeGuardOnExit, Fun&& fn) {
return ScopeGuard<Fun>(std::forward<Fun>(fn));
}}  // end of namespace scope_guard#define SCOPE_EXIT \
auto ANONYMOUS_VARIABLE(scope_exit_var) \
= ::scope_guard::ScopeGuardOnExit() + [&]()int main() {

  std::ofstream myfile;
  SCOPE_EXIT{ myfile.close(); std::cout << "Finished cleanup"; };

  myfile.open ("example.txt");
  myfile << "Writing this to a file.\n";
}
```

我们先来看看 *main()* 函数中`SCOPE_EXIT`宏的用法。在我们创建了`myfile`对象之后，我们可以声明性地指定退出作用域(在本例中，退出 main 函数)时需要执行的清理步骤。在这种情况下，它将关闭打开的文件，并输出消息以指示清理已经完成。使用这个宏，描述退出作用域时要执行的必要步骤(例如，释放资源、记录日志等)变得非常清晰和直观。

现在，让我们看看`SCOPE_EXIT`宏是如何实现的以及它是如何工作的。

`SCOPE_EXIT`宏扩展成如下代码，

```
auto ANONYMOUS_VARIABLE(scope_exit_var) = ::scope_guard::ScopeGuardOnExit() + [&]()
```

由于`SANONYMOUS_VARIABLE(scope_exit_var)`也是一个宏，它也将被扩展。让我们来看看代码开头定义的相应宏。

```
#define CONCATENATE_IMPL(s1, s2) s1##s2
#define CONCATENATE(s1, s2) CONCATENATE_IMPL(s1, s2)#ifdef __COUNTER__
#define ANONYMOUS_VARIABLE(str) \
CONCATENATE(str, __COUNTER__)
#else
#define ANONYMOUS_VARIABLE(str) \
CONCATENATE(str, __LINE__)
#endif
```

`_COUNTER_`和`_LINE_`是特殊的预定义宏。在本例中，它们用于在代码中展开的每个地方提供唯一的编号。从 Gcc 发布 notes⁶:

> 添加了一个新的预定义宏`__COUNTER__`。它扩展为从 0 开始的连续整数值。结合`##`操作符，这提供了一种生成唯一标识符的便捷方法。

但是并不是所有的编译器都支持`_COUNTER_`，所以它使用标准的预定义`_LINE_`宏作为备份。该宏以十进制整数常量的形式扩展到当前输入行号。

`CONCATENATE`宏被定义为连接给定的两个输入。要使它与预定义的宏一起工作，需要两级宏扩展。这就是为什么我们也有`CONCATENATE_IMPL`宏。`CONCATENATE_IMPL`宏使用`*##*`宏操作符，该操作符获取两个单独的令牌并将它们粘贴在一起形成一个令牌。得到的令牌可以是变量名、类名或任何其他标识符。

这样一来，`ANONYMOUS_VARIABLE(scope_exit_var)`的扩展就可以像`scope_exit_var1`(末尾的数字取决于它在源代码`_LINE_`中被调用的地方，被调用了多少次`*_COUNTER_*`)。

因此，例子中对`SCOPE_EXIT`宏的调用扩展如下，现在，我们只剩下普通的 C++代码。

```
auto scope_exit_var1 = ::scope_guard::ScopeGuardOnExit() + [&]() { myfile.close(); std::cout << "Finished cleanup"; };
```

现在，让我们再来看看最初的 l 示例代码。它定义了一个模板操作符函数，该函数将枚举类`ScopeGuardOnExit`和模板参数`Fun&& fn`作为参数。

```
template <typename Fun>
ScopeGuard<Fun> operator+(ScopeGuardOnExit, Fun&& fn) {
  return ScopeGuard<Fun>(std::forward<Fun>(fn));
}
```

这是在扩展代码右侧调用的操作符。

它将模板参数`fun`转发给`ScopeGuard`类的构造函数，并返回构造的对象。在这种情况下，模板参数`fun`是λfunction⁷

```
[&]() { myfile.close(); std::cout << "Finished cleanup"; };
```

`ScopeGuard`是一个接受模板参数`Func`的类。它有一个`Func`类型的`cleanup_`成员，该成员是用构造函数中给定的参数初始化的。在`ScopeGuard`的析构函数中调用`cleanup_`，在本例中，它是给定的 lambda 函数。因此，当`scope_exit_var1`变量超出范围并且对象的析构函数被析构时，包含用户指定的清理步骤的 lambda 函数被执行！

正如我上面提到的，我将 ScopeGuard 的实现限制到最小，以便更简单地解释宏用法的思想。例如，在实践中，我们需要使它不可复制，并且它可以像 ScopeGuardOnFailure 一样被扩展，以指定在异常等情况下要执行的步骤，正如你可以从 Andrei Alexandrescu 的演讲“声明式控制 Flow"⁸”中看到的那样。

# 摘要

在这篇文章中，我解释了预处理器指令和宏的基础。然后我谈到了在代码中使用宏的缺点，以及为什么在社区中使用宏会有争议。即使宏有潜在的问题，它仍然在实践中被广泛使用，如果我们小心使用它，它会很有用。作为一个例子，我用 ScopeGuard 习语用例的例子介绍了使用宏创建唯一变量名的技术。

[1]:[https://en.wikipedia.org/wiki/C_preprocessor](https://en.wikipedia.org/wiki/C_preprocessor)

[2]:[https://en . Wikipedia . org/wiki/Conditional _ compilation #:~:text = In % 20 computer % 20 programming % 2C % 20 Conditional % 20 compilation，that % 20 are % 20 provided % 20 during % 20 compilation。](https://en.wikipedia.org/wiki/Conditional_compilation#:~:text=In%20computer%20programming%2C%20conditional%20compilation,that%20are%20provided%20during%20compilation.)

【3】:【https://en.cppreference.com/w/cpp/preprocessor 

【4】:【https://en.cppreference.com/w/cpp/language/raii 

[5]:[https://www . drdobbs . com/CPP/generic-change-the-way-you-write-excepti/184403758](https://www.drdobbs.com/cpp/generic-change-the-way-you-write-excepti/184403758)

【6】:[https://gcc.gnu.org/gcc-4.3/changes.html](https://gcc.gnu.org/gcc-4.3/changes.html)

[7]:[https://en.cppreference.com/w/cpp/language/lambda](https://en.cppreference.com/w/cpp/language/lambda)

[8]:[https://youtu.be/WjTrfoiB0MQ](https://youtu.be/WjTrfoiB0MQ)