# C++中模板类型演绎的陷阱

> 原文：<https://levelup.gitconnected.com/pitfalls-of-templates-type-deduction-in-c-272d66bc75aa>

![](img/b2beab597c1c8d34bee759394ba486ba.png)

[法托斯 Bytyqi](https://unsplash.com/@fatosi?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

C++是一种严格类型化的语言。我们必须使用特定的类型声明每个变量、函数参数和函数返回值。如果预期的类型和实际使用的类型不兼容，它就不会编译。这带来了一个好处，即语言本身确保了“类型安全”,以防止开发人员犯将错误的对象、参数分配给错误的函数、运算符的错误。此外，这种检查是在编译时静态完成的，因此在运行时不会增加额外的性能成本。

然而，这种严格类型化的方面也使得 c++不太灵活。假设你喜欢提供一个函数作为库，函数带两个输入参数，计算总和并返回。因为您无法预测库的用户在其上下文中需要的特定类型，所以您无法实现与可能需要的类型兼容的函数。此外，即使您确切地知道您的库需要支持哪些类型，您也需要准备多个采用不同类型(int、void、string、custom class 等)的函数。在大多数情况下，该函数具有适用于所有类型的通用行为。因此，也会有很多重复。

为了解决这个问题，C++编程语言提供了一个强大的特性，叫做模板。使用模板，我们可以实现一个通用函数或一个可以用于许多不同类型的类。事实上，模板在库开发中非常流行，许多 C++标准库都在内部使用模板。

在这篇文章中，我将列举一些你在使用 C++模板时可能会遇到的陷阱或意外。

# 简单的例子

简单的模板函数定义和用法如下图所示，

```
template <class T>
void function(T arg) {
// do something with arg
}int main() {
  **int** a = 1;
  function(a);  
}# # # Instantiated function # # #
# void function(**int** arg) {
#  // do something with arg
# }
```

当*函数(a)* 为编译器读取的时，编译器从传递给*函数的变量 *a* 中推导出 *T* 的类型。*在这种情况下， *T* 推导为 *int。*结果， *void function(int arg)* 被实例化。

推演结果一点都不意外。现在，让我们看看下面的下一个案例，

```
template <class T>
void function(T arg) {
// do something with arg
}int main() {
  int a = 1; 
  **const int &** ref_to_const_int = a;
  function(ref_to_const_int);
}# # # Instantiated function # # #
# void function(**int** arg) {
#  // do something with arg
# }
```

在这种情况下，变量 *ref_to_const_int* 被传递给了*函数。ref_to_const_int* 是对 *const int* 的引用(即 *const int &* )。给定第一个例子的结果，很自然的猜测 T 会推导为 *const int &* ，与 *ref_to_const_int 相同。*但是， **T 会推导为“ *int”，*而不是*“const int&”。*** 所以实例化的函数看起来和之前一模一样: *void function(int arg)。*

# 自动类型“衰变”

发生这种意外是因为编译器在实例化*函数*时对传递的类型进行了某种转换。当我们将 *const int &* 参数传递给*函数*时，在推导 *T* 的类型时，编译器将原参数中的“ *const* ”和“ *&* ”去掉，将 *T* 推导为“ *int* ”。编译器的这种自动类型转换称为“衰减”。只有在将函数参数声明为按值传递时，才应用此规则。在我们的例子中，我们的*函数*声明采用 *T arg* ，也就是像下面这样通过值传递，所以编译器应用了衰减规则。

```
template <class T>
void function(T arg) {  // **pass-by-value **        
// do something with arg
}
```

衰减模式及其条件是，

当将函数参数声明为按值传递时，

1.  任何顶级引用都被删除:*int&*->*int*。
2.  数组类型转换为指针类型:*char*T46【10】->*char**。
3.  函数转换成函数指针类型:*int(int)*->-int(*)(int)。
4.  (如果 2，3 不适用)任何顶级 cv 限定符( *const* ， *volatile* )都被删除:*const int*->*int，volatile int - > int。*

在上面的案例中，应用了规则 1 和 4。因此，编译器在实例化模板时将 *const int &* 转换为 *int* 。

让我们看另一个衰变模式的例子。这次我们关注“数组类型到指针类型”衰减模式，

```
template <class T>
void function(T arg) {  // **pass-by-value**
// do something with arg
}int main() {
  **const char const_array[7]** = "Hello!"; 
  function(str);
}# # # Instantiated function # # #
# void function(**const** **char*** arg) {  // Not const char[7]
#  // do something with arg
# }
```

在这种情况下， *const_array* 即 *const char [7]* 类型被传递给了*函数*。因为没有参考限定词，衰变纲型 1 不适用。然后，由于它是一个数组类型，衰减模式 2 被应用，该模式将 *const char [7]* 转换为 *const char** 。并且因为它不是函数，所以衰减模式 3 被跳过。最后，由于衰减模式 2 适用，衰减模式 4 也被跳过。因此，编译器将 T 的类型推导为 *const char** 。这样，实例化的函数就是 *void 函数(****const*******char *****arg)*。*

# *防止自动类型衰减*

*为了避免编译器的这种自动类型衰减，可以将函数参数声明为按引用传递，如下所示:*

```
*template <class T>
void function(T& arg) {  // **pass-by-reference**
// do something with arg
}int main() {
  int a = 1; 
  const int & ref_to_const_int = a;
  function(ref_to_const_int);
}# # # Instantiated function # # #
# void function(**const int&** arg) {
#  // do something with arg
# }*
```

*由于函数参数被声明为 *T &* (按引用传递)，因此不会应用衰减模式(记住，衰减仅在函数将其参数声明为按值传递时发生)。推导出的 T 的类型为 *const int* 。因此编译器实例化的函数是 *void 函数(const int & arg)。**

*这也能防止其他的腐烂模式。你可以在下面看到一个数组类型的例子。*

```
*template <class T>
void function(T& arg) {  // **pass-by-reference**
// do something with arg
}int main() {
  **const char const_char_array[7]** = "Hello!"; 
  function(**const_char_array**);
}# # # Instantiated function # # #
# void function(**const char(&)[7]** arg) {
#  // do something with arg
# }*
```

*推导出的 T 的类型是 *const char[7]，*不是 *const char*。*因此编译器实例化的函数是 *void 函数(const char( & )[7] arg)。**

*虽然这种类型的衰退行为看起来像是必须避免的负面事情，但它也可能是一种期望的行为，这取决于具体情况。例如，假设您想实现一个函数，它接受两个相同类型的输入，将它们打包成一个 std::pair <t t="">并返回它的一个副本。这个模板如下所示。如您所见，它要求两个输入具有相同的 t 类型。</t>*

```
*template <class T>
std::pair<T, T> make_pair_function(T arg1, T arg2) {
  return std::pair<T, T>(arg1, arg2);
}*
```

*假设你喜欢传递两个数组给 *make_pair_function。*如果您将函数参数声明为按值传递，即使您传递两个长度不同的数组(因此，它们是不同的类型)，由于编译器的自动衰减，这两种类型都被推导为 *const char** 。这就是为什么这是有效的，编译没有错误。*

```
*template <class T>
std::pair<T, T> make_pair_function(T arg1, T arg2) {
  return std::pair<T, T>(arg1, arg2);
}int main() {
  **const char const_array1[7]** = "Hello!"; **const char const_array2[8]** = "World!!";make_pair_function(const_array1**,** const_array2);
}# # # Instantiated function # # #
#  std::pair<**const char***, **const char***>
#   function(**const char*** arg1, **const char*** arg2) {
#  return std::pair<**const char***, **const char***>(a, b);
# }*
```

*但是，如果您将函数参数声明为按引用传递，并将两个数组传递给 *make_pair_function* ，它将不会编译。这是因为编译器将 arg1 和 arg2 的类型推断为不同的类型( *const char[7]* ， *const char[8]* )，而没有模板 *make_pair_function* 可以让可以接受两种不同的类型作为输入参数，或者没有非模板 *make_pair_function* 可以接受( *const char[7]* ，*

**因此，这就是为什么自动类型衰减可以是有用的功能取决于情况。实际上，在标准库中定义了一个名为 *std::decay* 的实用函数，这个函数通过编译器有意地强制类型 decay 的行为。重要的是要意识到模板类型演绎下发生了什么，并根据需要进行调整。**

# **摘要**

**在这篇文章中，我解释了 C++中模板类型演绎的陷阱。我解释了编译器自动类型衰减转换的概念，以及如何防止它。**

**[1]:模板，[https://en.cppreference.com/w/cpp/language/templates](https://en.cppreference.com/w/cpp/language/templates)**

**[2]:简历限定词，[https://en.cppreference.com/w/cpp/language/cv](https://en.cppreference.com/w/cpp/language/cv)**

**[3]: *std::衰变，*https://en.cppreference.com/w/cpp/types/decay**