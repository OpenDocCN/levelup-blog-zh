# 为什么不应该使用名称空间

> 原文：<https://levelup.gitconnected.com/why-you-shouldnt-use-namespaces-c136af9723d>

## 更重要的是，为什么不应该使用名称空间 STD

我们都做过。我们发现在 cpp 文件的顶部写一行代码可以节省一些时间。但是总有一天，就像我一样，当你意识到使用名称空间 STD 可能不是一个好主意的时候。

![](img/d396b73ce25645a58484f6f203ee1414.png)

潘卡杰·帕特尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

首先，我们需要理解名称空间到底是什么。名称空间是用于排序和组织代码的声明性区域，这在你使用多个库时特别有用。如果你想调用一个包含在命名空间中的函数，你必须告诉编译器这个函数在哪个命名空间中，例如标准库中的字符输出。

```
std::cout << "something" << std::endl;
```

显然，为了使事情变得更容易，自 1995 年以来，我们就包含在 C++标准中，我们有能力创建一个作用域，从本质上告诉编译器，当我们调用一个没有名称空间的函数时，要在名称空间和全局作用域中查找。对于单个名称空间来说，这很好，特别是如果您将它用于自己的自定义名称空间，但是假设您使用的是标准库，这可能会有点混乱。

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

以这个例子为例，它非常容易阅读，我们知道所有的函数来自哪里，但是假设我在顶部引入了一个名称空间 STD。

```
#include <iostream>
#include <vector>
#include <functional>using namespace std;void print_values(const vector<int>& values, const function<void(int)>& func)
{
    for (auto value : values)
    {
        func(value);
    }
}int main()
{
    vector<int> numbers = { 4,2,30,2,9 };
    vector<int> numbers_2 = { 4,3,30,5,9 };
    auto lambda = [&numbers_2](int a) mutable{
        auto iter = find(numbers_2.begin(), numbers_2.end(), a)
        if (iter != numbers_2.end())
        {
            cout << "Value: " << a << endl;
            numbers_2.erase(iter);
        }
    };
    print_values(numbers, lambda);
}
```

虽然这样看起来更干净，但它为调试你的程序增加了额外的一层，你看，因为我在 snake case 中编写我的函数，因为标准库在 snake case 中编写它的函数，如果不是因为函数在顶部的事实，你可能会误认为是标准库函数。

使用命名空间 std 时，可能会出现其他问题。假设你是一个疯子，决定写一个打印函数，并把它叫做 cout。

```
#include <iostream>
#include <string>void cout(const std::string& message)
{
    std::cout << message << std::endl;
}int main()
{
    using namespace std; cout((string)"Hello world");
}
```

对于大多数编译器，这应该会抛出一个错误，因为它发现了函数“cout”的两个实例，一个在标准库中，一个在全局环境中。

使用库时可能会出现一个不同但相似的错误。假设您创建了一个名为 functions 的库，还创建了一个名为 logging 的库。

```
function.hnamespace functions
{}logging.hnamespace logging
{
    std::string print(const std::string& message)
    {
        //use the message to work out how significant the error is
        std::cout << "ERROR" << std::endl;
    }
}
------------------------#include <iostream>
#include <string>#include "logging.h"
#include "functions.h"int main()
{
    using namespace logging;
    using namespace functions; print((std::string)"Hello world");
}
```

现在，您决定更新 functions 名称空间，以包含一个 print 函数，该函数恰好与 logging 名称空间中的函数同名，并采用相同的参数，但它有不同的输出。

```
function.hnamespace functions
{
    std::string print(const std::string& message)
    {
        std::cout << message << std::endl;
    }
}logging.hnamespace logging
{
    std::string print(const std::string& message)
    {
        //use the message to work out how significant the error is
        std::cout << "ERROR" << std::endl;
    }
}
------------------------#include <iostream>
#include <string>#include "logging.h"
#include "functions.h"int main()
{
    using namespace logging;
    using namespace functions; print((std::string)"Hello world");
}
```

现在我们有一个问题，因为我们将会得到未定义的行为，并且没有任何逻辑原因使调试程序成为一场噩梦。

回到为什么不应该特别使用“名称空间标准”的问题。在 YouTube 上有一个很棒的视频，由切诺制作，讲述了为什么你不应该使用“名称空间标准”。该视频使用了 EA 的开源 STL 库的例子，该库在 Frosbite 引擎中使用。EA STL 是 EA 的 std 库的等价物，所以通过使用“命名空间 STD”和“命名空间 STL ”,你会遇到许多问题(这些问题不容易解决，因为它们首先出现是没有意义的),因为许多函数名在两个库之间共享。

所以名称空间本身真的很好，它们有助于使代码更有组织，并且它们允许你知道函数来自哪里，而不是你看你的 cpp 文件中有什么头文件，然后猜测那个函数来自哪里。我将以一些该做和不该做的事情来结束这篇文章。

*   不要使用命名空间 std，但是如果一定要在小范围内使用 if。
*   在任何情况下都不要在头文件中使用名称空间 std，那会直接导致错误。
*   一定要使用名称空间，它们会让你的代码更有条理。
*   最后，如果你要使用名称空间 STD，不要写与标准库中的函数同名的函数。