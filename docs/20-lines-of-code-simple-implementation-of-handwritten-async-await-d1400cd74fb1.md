# 20 行代码手写异步 Await 的简单实现

> 原文：<https://levelup.gitconnected.com/20-lines-of-code-simple-implementation-of-handwritten-async-await-d1400cd74fb1>

## 如果让你手写异步函数的实现，你会觉得复杂吗？这篇文章用 20 行带你了解它的核心。

![](img/58a268164b105773e550ead59962fd47.png)

人们常说异步函数是生成器函数的语法糖，那么它是什么样的糖呢？让我们一层一层地剥开它的糖衣。
有同学想说，既然用了生成器函数，为什么还要实现异步？
这篇文章的目的是让大家了解 async 和 generator 如何一起管理异步。

对于这个简单的例子，如果我们把它表示成一个母函数，会是什么样子呢？

我们知道生成器功能不会自动执行。每次调用它的`next`方法，都会停留在下一个 yield 位置。

利用这个特性，我们只要写一个自动执行的函数，就可以让这个生成器函数完全实现异步函数的功能。

所以总体思路已经确定，

`asyncToGenerator` 接受一个`generator` 函数并返回一个承诺，

关键在于`asynchronous` 过程除以良品率应该如何自动执行。

**如果手动执行**

在写这个函数之前，我们先模拟手动调用生成器函数来一步步完成这个过程，有助于后面的思考。

我们首先调用`testG` 来生成一个迭代器

```
**var** gen = testG()
```

然后开始执行第一个`next`

```
*//* The first time call next stays at the position of the first yield
*//* The returned promise contains the data required by data
**var** dataPromise = gen.next()
```

这里返回一个承诺，是`getData()`第一次返回的承诺。请注意:

```
**const** data = **yield** **getData**()
```

这段代码需要分成两部分:第一次调用 next，但实际上只是停留在 yield getData()，
数据的值没有确定。
那么数据的价值什么时候才能确定？
下一次调用 next 时，传递的参数将被用作前一次 yield 之前接受的值
也就是说，当我们再次调用 gen.next 时，data 的值将被确定为“该参数将被赋给数据变量”

```
gen.next('This parameter will be assigned to the data variable')// Then the data here has value
const data = yield getData()console.log('data: ', data);// Then move on to the next yield
const data2 = yield getData()
```

然后向下执行，直到遇到下一个产量，并继续这个过程…

这是生成器函数设计的一个难点，但是为了达到我们的目的，还是要学的~

有了这个特性，如果我们这样控制 yield 过程，是否可以实现异步序列化？

这种看起来像回调地狱的调用允许我们的生成器函数以一种清晰的方式安排异步。

考虑到这一点，实现这个高阶函数就变得容易了。

我们先从整体上看一下结构，以获得一个印象，然后我们会在评论中逐行解释。

不多不少，22 行。

接下来逐行解释。

## 总结

本文以最简单的方式实现了 asyncToGenerator 函数，这是 babel 编译异步函数的核心。当然，在巴别塔中，生成器函数也被编译成非常原始的形式。在本文中，我们直接用生成器来代替它。这也是实现承诺序列化的一个很好的模式。如果你对我的文章感兴趣，可以关注我的[媒体](https://hyhwell.medium.com/)或[推特](https://twitter.com/Maxwell_hyh)。