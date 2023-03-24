# 什么是堆栈溢出

> 原文：<https://levelup.gitconnected.com/what-the-heck-is-a-stack-overflow-cb5bb17870a0>

## 这就是递归出错时的情况

![](img/4831d5656b3e77e0d0c4ea3c2c0fcb59.png)

由[罗伯特·阿纳奇](https://unsplash.com/@diesektion?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/book-stack?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我说的不是你用来复制代码来完成工作的[栈溢出](https://stackoverflow.com/)。我说的是写不好的递归会怎么样。

那么什么是**堆栈溢出**？

根据[维基百科](https://en.wikipedia.org/wiki/Stack_overflow)，

> 在软件中，如果[调用堆栈](https://en.wikipedia.org/wiki/Call_stack)指针超过[堆栈](https://en.wikipedia.org/wiki/Stack_(abstract_data_type))界限，则会发生**堆栈溢出**。调用堆栈可能由有限的[地址空间](https://en.wikipedia.org/wiki/Address_space)组成，通常在程序开始时确定。

**翻译:**分配给程序的内存(调用栈)是有限的。当超过这个值时(溢出时)，我们称之为堆栈溢出。

每次我看到它，它都是无限递归的结果。**递归**是方法/函数调用自身。

有时递归是有意的(例如:在一个图上迭代)。有时候是无意的(回调自己触发)。

现在让我们故意在 Python、Ruby 和 Javascript 中引起堆栈溢出。

# 计算机编程语言

在您最喜欢的代码编辑器中，切换到 python 并运行以下代码。

```
def do_recursion():
  do_recursion()do_recursion()
```

这将引发一个错误。

```
RecursionError: maximum recursion depth exceeded
```

[Python](https://docs.python.org/3/library/exceptions.html) 在栈出界时抛出一个`RecursionError`。

# 红宝石

将您的编辑器切换到 ruby 并运行以下代码。

```
def do_recursion
  do_recursion
end
do_recursion
```

这会导致错误。

```
nodepad.rb:3:in `do_recursion': stack level too deep (SystemStackError)
```

当堆栈级别太深时，Ruby 抛出一个`SystemStackError`。

# java 描述语言

你可以在 chrome 的 JS 控制台中运行这个程序。

```
var do_recursion = function() {
  do_recursion()
}
do_recursion()
```

这将引发一个错误。

```
Uncaught RangeError: Maximum call stack size exceeded
```

[超出堆栈时 Javascript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RangeError) 抛出 RangeError。

# 结论

这就是真正的堆栈溢出。

简而言之，超出了分配的内存。

除非写很多递归代码，否则不会经常看到。但是这是一个很好的理解，放在你的工具箱里。