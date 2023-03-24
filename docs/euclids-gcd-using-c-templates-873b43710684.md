# 欧几里德的 GCD 使用 C++模板

> 原文：<https://levelup.gitconnected.com/euclids-gcd-using-c-templates-873b43710684>

![](img/0ecd1d96e62b7f665b17442b245181fe.png)

# 介绍

求 2 个数的最大公约数的一种方法是欧几里德算法。可汗学院有个很棒的描述[这里](https://www.khanacademy.org/computing/computer-science/cryptography/modarithmetic/a/the-euclidean-algorithm)。基本算法是:

1.  从 2 个非零数字 A 和 B 开始，其中 A ≥ B。
2.  使用模运算，求余数 r。
3.  如果 R == 0，那么我们就完成了，答案是 b。
4.  如果 R！= 0，然后重复分配 B -> A 和 R->B 的算法。

这种算法的递归性质非常适合于 [C++模板元编程](https://en.wikipedia.org/wiki/Template_metaprogramming)。使用这种技术，我们可以让 C++编译器为我们做这种计算。我不确定这在实践中有多大用处，但是它给了我们一个关于 C++模板的快速教程，以及使用它们在编译时进行计算的能力。

# 克隆存储库

为了检查这段代码并亲自试用这个程序，你可以在这里克隆这个回购:【https://github.com/danmcleran/euclid.git

# GCD 父结构

我们首先定义希望用户实例化和使用的顶级结构:

你会注意到我们正在使用 [C++11 constexpr 说明符](https://en.cppreference.com/w/cpp/language/constexpr)。这确保了我们所有的计算都发生在编译时。这些计算没有运行时开销。稍后我们将通过检查汇编语言输出来证明这一点。

结构 gcd 中的前两行:

确保我们选择两个模板参数(M，N)中较大的一个作为值 1。然后，我们将值 2 设置为值≤值 1。这是一个小助手，在我们调用 gcd 实现之前，确保我们的 lhs 我们的 rhs，这发生在下一行:

这是启动递归模板实例化来执行 GCD 计算的那一行。

# GCD 实施

为了使这个模板更加用户友好，我们在一个名为:

为了方便起见，提供了这个结构及其专门化来构造 gcd。父结构处理除法余数的计算以及递归模板实例化。

# 模板专业化

我们有一个[局部模板专门化](https://en.cppreference.com/w/cpp/language/partial_specialization)来防止被 0:

如果递归模板实例化试图实例化一个 N == 0 的模板，这个专门化将介入并返回答案 GCD == M。

我们有另一个[部分模板专门化](https://en.cppreference.com/w/cpp/language/partial_specialization)，它在 M == 0 时截取:

当 M == 0 时，GCD 结果将== 0。

需要最后一个[显式模板专门化](https://en.cppreference.com/w/cpp/language/template_specialization)来停止递归模板实例化。当 M 和 N 都== 0 时，我们需要一个特殊化:

有了上面的专门化，我们可以使用 [C++模板元编程](https://en.wikipedia.org/wiki/Template_metaprogramming)来计算欧几里德的 GCD 算法。

# 示例程序

现在我们已经整理好了模板，我们可以用它们来计算使用欧几里德算法的 GCD。

# 构建示例

我们将使用 g++来构建示例程序:

```
g++ -o euclid -std=c++11 euclid.cpp
```

我们指定 C++ 11 来确保 constexpr 的行为符合预期。

# 运行示例

当我们运行该示例时，应该会看到以下输出:

```
GCD of 0, 0 = 0
GCD of 6, 4 = 2
GCD of 36, 24 = 12
GCD of 270, 192 = 6
GCD of 4, 6 = 2
GCD of 24, 36 = 12
GCD of 192, 270 = 6
```

你可以看到，无论模板参数的顺序如何，我们得到的结果都是一样的。

# 检查组件

为了确保我们不会招致运行时开销，我们可以生成程序集输出以供检查。在 Linux 上，我们这样做:

```
objdump -S --disassemble euclid > euclid.lst
```

您将看到，在程序执行期间，这些值被直接加载到寄存器中:

```
...
 9f5:   be 02 00 00 00          mov    $0x2,%esi
 9fa:   48 89 c7                mov    %rax,%rdi
 9fd:   e8 9e fd ff ff          callq  7a0 <_ZNSolsEm@plt>
 a02:   48 89 c2                mov    %rax,%rdx
 a05:   48 8b 05 c4 15 20 00    mov    0x2015c4(%rip),%rax
...
 a2a:   be 0c 00 00 00          mov    $0xc,%esi
 a2f:   48 89 c7                mov    %rax,%rdi
 a32:   e8 69 fd ff ff          callq  7a0 <_ZNSolsEm@plt>
 a37:   48 89 c2                mov    %rax,%rdx
...
```

注意，我们将 0x2 和 0xc 的值直接载入微处理器寄存器:

```
...
 9f5:   be 02 00 00 00          mov    $0x2,%esi
...
a2a:   be 0c 00 00 00          mov    $0xc,%esi
```

这是我们模板元编程的结果。

# 结论

C++模板可以通过 [C++模板元编程](https://en.wikipedia.org/wiki/Template_metaprogramming)做一些非常酷的事情。虽然这可能是一个玩具程序，但它展示了大多数模板元程序使用的机制。