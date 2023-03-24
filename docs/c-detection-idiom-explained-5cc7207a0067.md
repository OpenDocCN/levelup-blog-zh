# C++检测习语解释道

> 原文：<https://levelup.gitconnected.com/c-detection-idiom-explained-5cc7207a0067>

## 介绍流行的元编程模式

![](img/2b281de8de7ea9885321e714a3c1e4a4.png)

# 介绍

模板是一个有用的特性，通过它我们可以定义一个通用的函数或者一个通用的类，它可以用于不同的类型。在你可以用模板做的所有其他很酷的事情中，有一个流行的模板技术叫做“检测习语”。

检测习语是一种带有模板的模式，可以在编译时检测特定表达式对于给定类型是有效的还是格式不良的。人们经常用它来有选择地在模板函数的不同实现之间进行选择。例如，假设你正在开发一个库，你有一个函数可以创建一个给定参数的副本，并对它进行如下处理。

```
template <class T>
void Func(const T& arg) {
  // 1\. Create a copy of arg.
  T copy(arg); // 2\. Do some process with the copy.
}
```

作为库开发人员，您希望该函数的用户能够将它用于任何类型，因此您将它定义为模板函数。到目前为止一切看起来都很好。

然而，在你发布了这个库之后，你意识到有些类型定义了一个特殊的`Clone()`函数，在创建类型的副本时应该使用这个函数，而不是像函数中的`T copy(arg)`那样使用它的复制构造函数。下面的代码详细描述了它。

```
class Foo {
 public:
  Foo * Clone() const {   **// User defined special clone function.**
    return new Foo(*this);
  }
};int main() {
  Foo foo;
  Func(foo); **// When the template function is instantiated with  
             // type Foo, It should use Foo::Clone() to copy itself.** return 0;
}
```

所以我们需要让编译器选择使用`Clone()`函数的`Func(const T& arg)`的不同实现，如果给定类型有函数的话。下面是解释问题的伪代码。

```
**//// What we want to do in pseudocode ////****// For the type without Clone() function.**
template <class T>
void Func(const T& arg) {
  **T copy(arg);**// 2\. Do some process with the copy.
}**// For the type with Clone() function.**
template <class T>
void Func(const T& arg) {
  **T * copy = arg.Clone();**// 2\. Do some process with the copy.
}
```

这个问题的解决方案大体上可以分为两步。

1.  检查给定类型是否有`Clone()`功能。
2.  根据第一步的结果，让编译器选择合适的`Func(const T &arg)`实现。

# 第一步。检查给定类型是否具有`Clone()`功能。

为了实现第一步，我们可以使用“检测习语”。正如我之前所写的，检测习语是一种带有模板的模式，可以检测编译时特定表达式对于给定类型是有效的还是格式不良的。检测习语的实现经常利用 SFINAE 规则。

让我们看看如何将习语应用到我们的案例中。下面的代码是检测习语的实现。

```
template <class T1, class T2 = void>
struct HasClone : false_type {};template <class T1>
struct HasClone<T1, std::void_t<decltype(std::declval<T1>().Clone())>> : true_type {}; // Simplified definition!
struct false_type {
    static constexpr bool value = false;
};struct true_type {
    static constexpr bool value = true;
};
```

这里，我们定义了一个名为`HasClone`的模板类(struct)。第一个定义是`HasClone`的主模板定义。它需要 2 个模板参数 T1，T2 和 T2 有一个默认参数 *void* 。它继承自具有静态成员变量*值*的 false_type，其中类型 *bool* 被初始化为 *false* 。

第二种定义是部分专门化的定义，只把 T1 作为一个参数。它继承自 true_type⁴，具有静态成员变量*值*，类型 *bool* 初始化为 *true* 。

这里，我们想要实现的是让编译器选择

*   如果给定类型不具有`Clone()`功能，那么`HasClone`的第一个(主要)定义`HasClone::value`为*假*
*   第二个定义如果给定类型有`Clone()`功能。因此，`HasClone::value`是*真*

这样，我们可以稍后使用编译时间常数`HasClone::value`来选择性地选择不同的`Func()`实现(第二步，稍后描述)。

如果你看一下`HasClone`的第二个定义，第二个参数被指定为`std::void_t<decltype(std::declval<T1().Clone())>`。

让我们一步一步地调查它。首先，`std::void_t` ⁵是一个别名模板，它将任何给定的类型序列映射到一个类型 void。可以实现如下。

```
template< class... >
using void_t = void;
```

乍一看，它似乎很没用。对于任何给定的类型，`void_t<class...>`只是变成 void 类型(`class...`是可变 template⁶参数的表达式，允许任意数量的模板参数)。但是，当与 SFINAE 规则结合使用时，它会非常有用。

这里的关键点是，无论给定的类型是什么，都必须是格式良好的，才能使`void_t<class...>`本身成为格式良好的别名(在这种情况下，对于类型 *void* )。如果任何一个给定的类型是错误的，别名`void_t<class...>`本身也是错误的。

同时，SFINAE 规则允许编译器在用给定类型替换模板参数后，忽略包含不良表达式的模板类/函数。

在典型的检测习语实现中，它利用`void_t<class...>`的行为和 SFINAE 规则，让编译器在不满足某个条件时忽略某个模板。

在我们的例子中，如果给定的类型没有`Clone()`函数，我们需要让编译器忽略第二个`HasClone`定义，并让它选择主模板定义。

接下来，让我们看看`void_t<class...>.`的论证

如您所见，`decltype(std::declval<T1>().Clone())`被指定为`void_t<class...>`的模板参数。std::declval⁷是模板元编程中经常使用的函数模板。它返回一个给定类型的引用，所以不用实际创建一个对象，你可以假设你有一个给定类型的对象。在这种情况下，它用于形成调用给定类型的`Clone()`成员函数的表达式。decltype⁸是返回表达式或实体类型的关键字。在这种情况下，它试图返回给定类型的`Clone()`成员函数的返回类型。

现在，让我们看看当我们给`HasClone`模板一个具体类型时会发生什么。基本上有两种情况。(1)给定类型有`Clone()` , (2)给定类型没有`Clone()`。

让我们先看看情况(2 ),因为它更容易理解。

如果给定的类型没有`Clone()`函数，`std::declval<T1>().Clone()`就会变成病态。`decltype(std::declval<T1>().Clone())`也是如此，因此我们可以使`void_t<class...>`的模板参数格式不良。

```
template <class T1>
struct HasClone<T1, std::void_t<***Ill-formed***> : std::true_type {};
```

结果，`void_t`本身也变得不良，由于 SFINAE 规则，编译器忽略第二个`HasClone`定义。最终，它选择了唯一剩下的主定义。

```
class Bar {
// Without Clone() function.
}HasClone<Bar>::value **//** value is **false**
```

接下来，我们来看案例(1)。与情况(2)相反，`HasClone`的主模板定义和第二模板定义都被编译器认为是很好的候选。然而，由于 rule⁹的偏序，选择了比第一个定义更专业的第二个定义。

需要注意的一点是，要使这种机制工作，主模板中的默认模板参数必须是一个 *void* 类型。为了理解，我们必须看看当`Foo`被严格指定时会发生什么。

```
class Foo {
 public:
  Foo * Clone() const { 
    return new Foo(*this);
  }
};HasClone<Foo>::value // value is **true**
```

当给定`Foo`时，编译器试图用模板参数 T1 替换给定的参数`Foo`,用于两个模板。两个模板用`Foo`替换 T1 都没有问题。但不同的是，替换后，第二个模板定义中的`std::void_t<decltype(std::declval<Foo>().Clone())>`被翻译成了 *void* ，整个定义变成了如下所示:

```
template <class T1>
struct HasClone<T1, void> : std::true_type {};
```

这将第二个参数 T2 明确指定为 *void* 。

同时，作为一个单独的步骤，由于主模板定义有一个默认的模板参数 *void* ，编译器在实例化时将`HasClone<Foo>`翻译成`HasClone<Foo, void>`。然后，偏序规则开始生效。由于第二个模板明确地将第二个参数指定为 *void* ，因此它被认为比主定义更专业，也更受青睐。

# 第二步。让编译器选择合适的`Func(const T &arg)`实现

现在，我们已经实现了一种机制来检查给定的类型是否具有`Clone()`功能。最后，我们可以使用它让编译器选择合适的`Func(const T& arg)`实现。

基本上，我们在这里再次使用 SFINAE，就像我们在第一步中做的那样。

实现如下所示:

```
template<class T, class = std::enable_if_t<HasClone<T>::value>>
void Func(const T& arg) {
   T* copy = arg.Clone(); // 2\. Do some process with the copy.
}template<class T,  class = std::enable_if_t<!HasClone<T>::value>>
int Func(const T& arg) {
  T copy(arg);

  // 2\. Do some process with the copy.
}
```

如您所见，它使用步骤 1 中的`HasClone`将其值赋予 std::enable_if_t ⁰.如果给定值为 *true* ，则 std::enable_if_t 转换为良构类型 *void* 。另一方面，如果给定值为*假*，则该值是病态的。该实现利用了这种行为，让编译器忽略一个模板实现，同时保持另一个依赖于`HasClone<T>::value`，因此依赖于给定类型是否有`Clone()`。

就是这样。但是为了更进一步的考虑，我们可能想要使用相同的技术或者使用提供的特性 std::is_copy_constructible 来检查它是否是可复制构造的。

# 摘要

在这篇文章中，我们讨论了流行的模板元编程模式，“检测习语”。使用该模式，我们可以实现能够检测给定类型是否有特定成员的工具。然后，我们可以使用实现的工具进行进一步的模板元编程。沃尔特·e·布朗有一个关于这个话题的精彩演讲。“现代模板元编程:概要，第二部分”我推荐观看它以了解更多信息。

[1]:[https://en.wikipedia.org/wiki/Prototype_pattern](https://en.wikipedia.org/wiki/Prototype_pattern)

【2】:[https://en.cppreference.com/w/cpp/language/sfinae](https://en.cppreference.com/w/cpp/language/sfinae)

http://www.cplusplus.com/reference/type_traits/false_type/

[4]:[http://www.cplusplus.com/reference/type_traits/true_type/](http://www.cplusplus.com/reference/type_traits/true_type/)

【5】:【https://en.cppreference.com/w/cpp/types/void_t 

[6]:【https://en.cppreference.com/w/cpp/language/parameter_pack 

【7】:[https://en.cppreference.com/w/cpp/utility/declval](https://en.cppreference.com/w/cpp/utility/declval)

【8】:[https://en.cppreference.com/w/cpp/language/decltype](https://en.cppreference.com/w/cpp/language/decltype)

[9]:[https://en . CP preference . com/w/CPP/language/partial _ specialization](https://en.cppreference.com/w/cpp/language/partial_specialization)

[10]:[https://en.cppreference.com/w/cpp/types/enable_if](https://en.cppreference.com/w/cpp/types/enable_if)

[11]:[https://en . CP preference . com/w/CPP/types/is _ copy _ constructible](https://en.cppreference.com/w/cpp/types/is_copy_constructible)

[12]:[https://youtu.be/a0FliKwcwXE](https://youtu.be/a0FliKwcwXE)