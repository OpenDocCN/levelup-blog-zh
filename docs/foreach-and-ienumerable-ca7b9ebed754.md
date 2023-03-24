# Foreach 和 IEnumerable

> 原文：<https://levelup.gitconnected.com/foreach-and-ienumerable-ca7b9ebed754>

![](img/af5213239ae39f8c1d25d37b4bce54ca.png)

照片由[乔·格林](https://unsplash.com/@jg?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

最近我读了很多关于`foreach`和迭代器模式的文章。

在我的研究中，我想到了这些问题:

*   foreach 迭代变量如何只读？
*   为什么在直接使用迭代器模式时，编译器允许迭代变量的赋值？
*   为什么`IEnumerator.Current`是只读的？
*   为什么我不能在`IEnumerable<T>`中添加/删除元素？
*   为什么运行时允许你在`IEnumerable<T>`中更新一个元素的属性？
*   为什么有`IEnumerator<T>`还需要`IEnumerable<T>`？
*   `Array.GetEnumerator()`什么时候用？

本页将深入探讨这些问题以及我从 StackOverflow 得到的答案。

# foreach 迭代变量如何只读？

## 为什么？

回答“为什么”很容易，因为它让您不会想到您会更新集合中的元素，而您不会，因为您处理的是一个局部变量，它只引用了集合中的元素。

更新局部变量中的引用不会对集合中的对象做任何事情。

## 怎么会？

“如何做”有点难以弄清楚。

在 C#规范的 8.8.4 中，它提供了这个示例:

*形式为*的 foreach 语句

```
**foreach** (V v **in** x) embedded-statement
```

*随后扩展为:*

```
{
    E e = ((C)(x)).GetEnumerator();
    **try** {
        V v;
        **while** (e.MoveNext()) {
            v = (V)(T)e.Current;
            embedded-statement
        }
    }
    **finally** {
        … // Dispose e
    }
}
```

*迭代变量对应于一个只读局部变量，其作用域扩展到嵌入式语句。变量 v 在嵌入式语句中是只读的。*

当“扩展的”代码没有任何只读关键字时，我很困惑这怎么可能。

扩展代码是生成的 CIL 代码的 C#等价物。

没有必要用某种显式的方法将其标记为 readonly，因为 CIL 代码只有在编译时才会生成。

如果您试图更新迭代变量，代码首先不会编译。

编译器中有一条规则阻止你这样做。

# 为什么在直接使用迭代器模式时，编译器允许迭代变量的赋值？

考虑以下代码:

如果编译器能防止这种情况就好了。然而，对于编译器来说，识别这种情况并防止它发生所涉及的逻辑太复杂了。

编译器很容易知道什么时候试图在`foreach`中给迭代变量赋值，但是在 while 循环中给变量赋值是没问题的，如果它是迭代器模式的一部分的 while 循环，那就太难了。

# 为什么 IEnumerator？当前只读？

注意，这是一个不同于询问为什么`foreach`迭代变量是只读的问题。

`foreach`迭代变量代表`Current`属性，并且是只读的，因为如果您可以更新它，您可能会认为您正在更新集合中的元素，而您不会这样做。

但是，如果`Current`属性有一个 setter 会怎么样呢？

同样，这也没有用，因为这样你就可以更新`Current`属性，这不会影响到集合，而且在下次调用`MoveNext()`时会被覆盖。

但是，如果 setter 中有更新集合的逻辑会怎么样呢？

这在普通的集合中是可行的，但是因为你不能更新存储在`foreach`中的`IEnumerable<T>`元素中的引用，所以保持一致是有意义的，并且在迭代器模式中也要防止这种情况。

`Current`属性是只读的另一个原因是因为在一些集合中更新元素没有意义:

如果您有以下代码:

你到底要更新什么？更新这种类型的集合没有任何意义。

# 为什么我不能在 IEnumerable <t>中添加/删除元素？</t>

它会打乱集合的迭代。

如果这成为可能，当您在集合的开头添加一个元素时，您希望会发生什么？

既然添加了新元素，那么`foreach`循环的下一次迭代应该回到起点吗？

由于潜在的奇怪情况，`IEnumerable<T>`是只读的。

# 为什么运行时允许更新 IEnumerable <t>中元素的属性？</t>

考虑以下代码:

类似于前面的例子，您不能更新存储在`foreach`迭代变量中的引用，您现在可以问这个问题，为什么当您试图更新作为`IEnumerable<T>`一部分的元素的属性时，运行时不抛出异常？

更新一个`foreach`中的`IEnumerable<T>`元素的属性是没有用的，因为那只会更新当前的投影。即。在上面的 foreach 中，您正在更新一个对象，该对象将在迭代后立即被垃圾收集。一旦你再次迭代`IEnumerable<T>`，你将得到一个新的投影。

编译器无法检测到这一点，因为很难判断这个集合是纯的`IEnumerable<T>`，还是仅仅实现了`IEnumerable<T>`的集合。然而，这可以通过运行时在检测到更新对象的尝试时抛出一个`Exception`来防止。

不抛出`Exception`的原因是因为运行时必须跟踪这个对象来源于一个可枚举的事实。它必须跟踪可枚举的类型(因为如果它是基于列表的，它应该允许这样做)，并且它必须跟踪变量是否被赋给了其他地方(因为，如果是，代码可能是合法的)。

这简直是太多的开销却没有什么好处。

# 有 IEnumerator <t>为什么还要有 IEnumerable <t>？</t></t>

`IEnumerable<T>`是最简单的界面。它声明了一个方法`GetEnumerator()`。

为什么不把`IEnumerator<T>`中的逻辑放到`IEnumerable<T>`中呢？

把`IEnumerable<T>`想象成创造`IEnumerator<T>`的工厂。

如果您将枚举器当前位置的状态直接存储在一个`IEnumerable<T>`中，那么您将无法对集合使用嵌套枚举。

当您到达嵌套循环时，`Current`属性将读取第二个元素，而不是第一个。

# 当是数组时。使用了 GetEnumerator()。

一些开发人员认为，无论何时使用`foreach`，它都会生成调用`IEnumerator<T>`方法的代码。

情况并非总是如此。如果你在一个数组上使用`foreach`，它将在幕后使用一个 for 循环。

考虑下面的 C#代码:

在等价的 CIL 代码中，没有对`MoveNext()`等的调用，这只是一个循环:

由于数组不支持添加/删除项目，所以有一个隐含的固定的`Length`。

因此，在没有边界检查的情况下，通过索引(对于循环)而不是迭代器(`IEnumerable<T>`实现)访问数组项是一种优化。

但是，还是有理由有`Array.GetEnumerator()`。它是为 Linq 功能提供的:

在等价的 CIL 代码中，我们可以看到对`MoveNext()`等的调用: