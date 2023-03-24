# C++中的 Lambda 表达式

> 原文：<https://levelup.gitconnected.com/lambda-expressions-in-c-c1462e6a7803>

在 C++中使用 lambda 表达式是一种快速定义匿名函数的方法，在调用它的地方或者作为一个更大函数的参数。Lambda 表达式往往只有 1-2 行代码，可能只使用一次或两次。

![](img/febdfc5beeef7d790d4fc10fa3126b15.png)

潘卡杰·帕特尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Lambda 表达式基于 lambda 演算的概念，这是一种基于 20 世纪 30 年代纯抽象的语言。虽然 lambda 演算是图灵完备的，但它不同于图灵机，因为它不保留任何状态。

C++是一种命令式语言，这意味着与 Haskell 等函数式语言不同，它基于基于状态的计算模型，而不是数学逻辑和 lambda 演算。直到 2011 年，随着 C++ 11 的加入，C++才采用了匿名函数。

那么我们如何开始写一个 lambda，对于最基本的 lambda 来说，真的很简单。首先，我们需要设置我们的 C++环境，然后我们可以开始编写 lambda。

我们需要了解 lambda 的 6 个部分。

*   捕获条款
*   参数列表(可选)
*   可变规范(可选)
*   异常规范(可选)
*   尾随返回类型(可选)
*   lambda 的主体

```
#include <iostream>int main()
{
    auto lambda = [](int a, int b) {return (abs(a) > abs(b)); };
    if (lambda(5, 4))
    {
        std::cout << "True" << std::endl;
    }
}
```

现在你可能会看着它，说“你不能把 lambda 的主体放在 if 语句中吗”，你可能是对的，在这种情况下，你可能应该这么做。这比 lambda 的例子要干净得多。

```
if (5 > 4)
{
    std::cout << "True" << std::endl;
}
```

那么我们在哪里使用λ呢？一个常见的例子是在排序函数中。假设你有一个包含对的向量，你想对它排序，C++没有内置到 stander 库中，所以我们必须为它写一个 lambda。

```
#include <iostream>
#include <algorithm>
#include <vector>int main()
{
    auto pairs_sort = [](std::pair<int, float> a, std::pair<int, float> b) {return (a.second > b.second);  };
    std::vector<std::pair<int, float>> test_vector = { {0,0.3}, {1, 0.2}, {3, 0.9}, {4, 1.5}, {5, 0.15} };
    std::sort(test_vector.begin(), test_vector.end(), pairs_sort);
}
```

但那是针对标准库中包含的函数，我们如何在自己的函数中包含 lambda 表达式呢？我们有两个解，你选择哪一个取决于你是否捕捉了一个变量。

## **使用函数指针**

当使用函数指针时，我们不能捕获 lambda 中的任何东西，因为它不能被转换成函数指针，正如在 [C++ 11 草案](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2012/n3337.pdf)中所述。

> 没有 lambda-capture 的 lambda-expression **的闭包类型具有指向函数**的指针的公共非虚拟非显式 const **转换函数，该函数具有与闭包类型的函数调用运算符相同的参数和返回类型。这个转换函数返回的值应该是一个函数的地址，当这个函数被调用时，与调用闭包类型的函数调用操作符具有相同的效果。**

这是因为通过使用捕获括号，该范围内的变量被视为对象的成员变量，函数指针只传输 lambda 的逻辑而不是成员变量，要用捕获来传输它，您需要使用函数对象。

```
#include <iostream>
#include <vector>
void print_values(const std::vector<int>& values, void(*func)(int))
{
    for (auto value : values)
    {
        func(value);
    }
}int main()
{
    auto lambda = [](int a) {std::cout << "Value: " << a << std::endl; };
    std::vector<int> numbers = { 4,2,30,2,9 };
    print_values(numbers, lambda);
}
```

但是假设我们只想打印这些值，如果它们也在不同的向量中，现在你可以把它传递给函数。但是你可能不想这样做，因为那需要你每次传递两个向量，那么我们该怎么做呢？我们将其包括在捕获范围内。

## 使用固定支架

当使用捕获括号时，我们必须使用函数对象，而不是函数指针。为此，我们需要添加函数库。

```
#include <iostream>
#include <vector>
#include <functional>void print_values(const std::vector<int>& values, const std::function<void(int)>& func)
{
    for (auto value : values)
    {
        func(value);
    }
}int main()
{
    std::vector<int> numbers = { 4,2,30,2,9 };
    std::vector<int> numbers_2 = { 4,3,30,5,9 };
    auto lambda = [&numbers_2](int a) {
        if (std::find(numbers_2.begin(), numbers_2.end(), a) != numbers_2.end())
        {
            std::cout << "Value: " << a << std::endl;
        }
    };
    print_values(numbers, lambda);
}
```

在捕捉括号中，你可以看到我通过引用传递了这个向量，但是如果你想的话，你也可以通过值来复制它。

如果你想编辑你在 lambda 中捕获的变量，你必须使用 mutable 关键字，因为这告诉编译器忽略捕获变量的 CONST 关键字。例如，你可能想在第二个向量被打印出来后删除它。

```
#include <iostream>
#include <vector>
#include <functional>void print_values(const std::vector<int>& values, const std::function<void(int)>& func)
{
    for (auto value : values)
    {
        func(value);
    }
}int main()
{
    std::vector<int> numbers = { 4,2,30,2,9 };
    std::vector<int> numbers_2 = { 4,3,30,5,9 };
    auto lambda = [&numbers_2](int a) mutable{
        auto iter = std::find(numbers_2.begin(), numbers_2.end(), a)
        if (iter != numbers_2.end())
        {
            std::cout << "Value: " << a << std::endl;
            numbers_2.erase(iter);
        }
    };
    print_values(numbers, lambda);
}
```

如果没有可变关键字，编译器会向你抛出一个错误。

Lambdas 已经是 C++中一个强大的工具，随着 C++新版本中更多功能的加入，它们将变得更加有用。

希望这篇深入 C++中 lambdas 世界的文章是有见地的，虽然我没有涵盖所有内容，但我已经涵盖了足够多的内容，您将能够在您的代码中毫无问题地使用它。要了解更多信息，我建议查看由微软和 T2 提供的文档。