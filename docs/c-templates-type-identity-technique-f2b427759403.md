# C++模板类型标识技术

> 原文：<https://levelup.gitconnected.com/c-templates-type-identity-technique-f2b427759403>

## 引入“类型标识”技巧并引入 std::common_type

![](img/2b281de8de7ea9885321e714a3c1e4a4.png)

模板是一个有用的特性，通过它我们可以定义一个通用的函数或者一个通用的类，它可以用于不同的类型。要使用 templates 函数，我们需要在每次调用该函数时将具体类型指定为模板参数。实际上，显式地指定模板函数的所有类型会很快变得乏味，因此我们通常让编译器自动确定预期的类型，而不是显式地指定它们。这被称为模板参数演绎。

基本参数推导过程将传递的参数类型与相应的模板参数 T 进行比较，并尝试得出正确的替换。

假设有一个如下定义的模板函数，它接受两个参数并返回两个值的和。

```
template<class T>
T add(T a, T b) {
  return a + b;
}
```

我们可以用具体类型的参数调用函数，而不是显式指定模板参数。

```
auto sum = add<int>(1, 2);  // Explicit specification
auto sum = add(1, 2);  // Implicit specification
```

在上面的例子中，对于显式和隐式规格说明情况，模板参数 T 都被替换为 *int* 。

到目前为止，这些看起来都很简单，但是模板功能的用户可能会很快发现一些意想不到的错误，

```
auto sum = add(1, 2.5);  // Passing int(1) and double(2.5) as 
                         // templates arguments##### Error #####
templates argument deduction/substitution failed. 
```

如您所见，将 1 (int)和 2.5 (double)作为 templates 函数的参数会导致编译器错误。在推导过程中对每个参数进行独立分析，最终得出不同的结论(从第一个参数将 *T* 推导为 *int* ，从第二个参数将 *T* 推导为 *double* )，因此推导过程失败，编译器给出错误。

# 类型标识习语

上述函数的模板参数推导失败的原因是因为对第一个和第二个参数都进行了推导，并且最终结果有冲突。消除该错误的一种方法是防止第一或第二变元的自动变元推导，并且仅使用单个推导结果来推导模板参数 t 的类型

为了实现这一点，我们可以使用一种叫做“类型标识习语”的技术。可以描述如下，

```
template <class T>
struct type_identity {
    using type = T;
};
```

使用 *type_identity，*我们修改 *add* 函数如下:

```
template<class T>
T add(T a, typename type_identity<T>::type b) {
  return a + b;
}
```

现在，用两种不同的类型(int，double)调用函数不会产生编译错误。

```
auto sum = add(1, 2.5) // No errors.// The templates type T is deduced as int. 
```

如果您看到结果类型，模板类型 T 被推断为 int，第二个参数没有被编译器用于类型推断。

为了了解更多，让我们更深入地看看新的 *add* 函数定义。第二个参数的表达式替换为

```
typename type_identity<T>::type b
```

typename 是一个特殊的关键字，用来告诉编译器下面的名称是一个类型。没有这些信息，编译器就无法知道*类型*是在*类型标识*结构中定义的类型还是静态成员。从 *type_identity* 的定义可以看出， *type* 只是模板参数 t 的别名。

同时，如果你看到 C++文档中的非演绎上下文一节，

> 在下面的情况中，用于组成`**P**`的类型、模板和非类型值不参与模板参数推导，而是*使用*在其他地方推导或显式指定的模板参数。如果模板参数只在非推导的上下文中使用，并且没有明确指定，则模板参数推导将失败。
> 
> 1)使用 [qualified-id](https://en.cppreference.com/w/cpp/language/identifiers#Qualified_identifiers) 指定的类型的*嵌套名称说明符*(范围解析运算符::)左边的所有内容:

所以，换个更简单的说法，如果模板参数 T 出现在模板函数参数表达式中*:*作用域解析运算符的左边，编译器不会试图通过演绎过程来演绎类型。相反，它使用来自其他地方的演绎过程的结果。在我们的 *add* 函数的情况下，第一个自变量的演绎结果(即 *int* )用于替换第二个自变量中的 *T* 。由于*类型*只是*type _ identity*struct*，*中 *T* 的别名，因此第二个参数的类型也归结为 *int* 。

从 C++20 开始， *type_identity* 包含在语言中，并作为 *std::type_identity* 提供。同样为了避免每次都写*:*类型，别名 *type_identity_t* 也是可用的。 *type_identity_t* 的定义如下。

```
template< class T >
using type_identity_t = typename type_identity<T>::type;
```

# std::common_type

现在*添加*函数的用户可以在不明确指定类型的情况下调用该函数，并且不会得到编译错误。但是，不是很方便。函数的用户需要知道哪个函数参数用于类型推导。行为也不是很直观。例如，当我们给定 *int* 和 *double* 值作为参数时，更自然地期望每个模板参数被替换为 *double* 。否则，将 2.5 转换为 *int* 会失去其准确性。

```
auto sum = add(1, 2.5) // 2.5 is converted to int, truncated to 2!
```

为了实现更直观的行为，我们可以使用一个名为 *std::common_type* ⁴.的特性 *std::common_type* 是一个模板工具，它给出了给定类型的一个公共类型，所有这些类型都可以隐式地转换成这个公共类型。使用 *std::common_type，*我们最终的 *add* 函数如下所示，

```
template<class T>
T add_impl(T a, T b) {
  return a + b;
}template<class T1, class T2>
typename std::common_type_t<T1, T2>
add(T1 t1, T2 t2) {
   return add_impl<std::common_type_t<T1,T2>>(t1, t2);
}
```

新的 *add* 函数有两个参数。每个 *T1* 、 *T2* 都会发生模板类型扣款。然后，通过显式指定模板类型 *T* 来调用 *add_impl* 函数，其中 *std::common_type_t < T1，T2>被替换为给定 *T1* ， *T2* 的通用类型。上例中， *T1* 推导为 *int* ， *T2* 推导为 *double* ，那么 *std::common_type_t < int，double >* 确定为 *double* 。因此，模板参数 *T* 替换为 *double* 用于 *add_impl* 功能。*

# 摘要

在这篇文章中，我解释了称为“类型标识习语”的通用 C++模板技术和称为 *std::common_type* 的实用程序。

[1]:[https://en.cppreference.com/w/cpp/keyword/typename](https://en.cppreference.com/w/cpp/keyword/typename)

[2]:[https://en . CP preference . com/w/CPP/language/template _ argument _ deduction # Non-deducted _ contexts](https://en.cppreference.com/w/cpp/language/template_argument_deduction#Non-deduced_contexts)

[3]:[https://en.cppreference.com/w/cpp/types/type_identity](https://en.cppreference.com/w/cpp/types/type_identity)

【4】:[https://en.cppreference.com/w/cpp/types/common_type](https://en.cppreference.com/w/cpp/types/common_type)

1)使用 [qualified-id](https://en.cppreference.com/w/cpp/language/identifiers#Qualified_identifiers) 指定的类型的*嵌套名称说明符*(范围解析运算符::)左边的所有内容: