# 使用记忆优化你的递归方法！

> 原文：<https://levelup.gitconnected.com/optimize-your-recursive-approach-using-memoization-ef4c64ceae00>

## 对于初学者来说！

![](img/dbbdf898682e2348ca87e974eb9a37e0.png)

照片由 [Cookie 在](https://unsplash.com/@cookiethepom?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 Pom 拍摄

您将学到的内容:

*   如何在斐波那契函数中实现记忆？

您需要什么:

*   没什么！

推荐:

*   编程语言知识。

# 什么是斐波那契？

斐波纳契数列是这样一个数列，其中下一个数字是前两个数字的和，前两个数字是 1。

```
**First 5 numbers of Fibonacci:**1, 1, 2, 3, 5
```

# 递归斐波纳契

每个递归函数都应该有一个基本用例，以确保函数不会无休止地调用自己。

在这种情况下，我们将检查它是 1 还是 2，因为我们知道这两个必须是 1。

☝下面的代码存根将用 Python 编写。

```
def fib(n):
    if n == 1 or n == 2:
        return 1
```

斐波那契数是后面两个数的和，所以我们可以再次调用这个函数，但是用`n — 1`和`n — 2`代替。

```
def fib(n):
    if n == 1 or n == 2:
        return 1 *return fib(n-1) + fib(n-2)*
```

这个功能运行得很好，数字`7`和`10`看起来运行正常。

```
**# Input**print(fib(7))print(fib(10))**# Output**1355
```

但是，如果我们执行一个更大的数字，程序就会挂起。

```
**# Input**print(fib(50))**# Output**...
```

如果你熟悉大 o 符号，你会发现它有一个指数运行时间，O(2^n).

那么，我们可以在这个功能上做哪些改进呢？

# 记忆化的斐波那契！

优化函数的一个方法是识别我们在哪里做不必要的计算。让我们看看我们的函数在调用什么。

```
print(fib(7))7 = **fib(6)** + **fib(5)****fib(6)** + **fib(5)** = **fib(5)** + **fib(4)** + **fib(3)** + **fib(4)****fib(5)** + **fib(4)** + **fib(3)** + **fib(4)** = **fib(3)** + **fib(4)** + 1 + **fib(3)** + 2 + 1 + **fib(3)**and so on...
```

几乎每个调用都会重复多次，这给我们的程序带来了很多额外的计算。

我们应该将它们存储在一个散列表中，如果已经存在的话就访问它们，而不是运行它们。

我们称之为 memo，当我们没有为它传入参数时，我们应该将它初始化为一个空的 hashmap。

```
def fib(n, memo = {}):
    if n == 1 or n == 2:
        return 1 *return fib(n-1) + fib(n-2)*
```

让我们添加另一个检查，看看我们是否已经计算了这个数字。如果它是，我们将归还它。

```
def fib(n, memo = {}):
    **if *n* in *memo*: 
        return *memo*[*n*]**
    if n == 1 or n == 2:
        return 1 *return fib(n-1) + fib(n-2)*
```

现在，每次我们计算一个数字，让我们把它存储在 hashmap 中。

```
def fib(n, memo = {}):
    if *n* in *memo*: 
        return *memo*[*n*]
    if n == 1 or n == 2:
        return 1
    ***memo[n] = fib(n-1, memo) + fib(n-2, memo)*** *return fib(n-1) + fib(n-2)*
```

当我们再次调用这个函数时，我们还会传入我们的 hashmap，这样我们存储在那里的值就会被保存下来。

最后，我们将在函数结束时返回`memo[n]`。

```
def fib(n, memo = {}):
    if *n* in *memo*: 
        return *memo*[*n*]
    if n == 1 or n == 2:
        return 1
    *memo[n] = fib(n-1, memo) + fib(n-2, memo)****return memo[n]***
```

让我们再试试我们的测试设备。

```
**# Input**print(fib(7))
print(fib(50))**# Output**13
12586269025
```

成功了！

# 结论

谢谢！

我希望您喜欢阅读本文，并且您的代码工作正常。如果您有任何问题、建议或总体反馈，请随时发表评论！

编码快乐！