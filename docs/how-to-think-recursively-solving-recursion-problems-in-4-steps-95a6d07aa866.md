# 如何递归思考|用 4 个步骤解决递归问题

> 原文：<https://levelup.gitconnected.com/how-to-think-recursively-solving-recursion-problems-in-4-steps-95a6d07aa866>

![](img/209e201381513858a418000b68b333f0.png)

安全濒危的原创漫画。不知道是谁创造了这种迷因变体。

## **免责声明**

本文并不打算介绍更高级的概念，比如动态编程。相反，它将引入开始解决递归问题所需的心态。

示例是用 Javascript 编写的，但是我将在本文的底部用 Python 和 C++编写答案。

## **什么是递归**

既然你正在读这篇文章，我假设你对递归有一个模糊的概念，所以我不会深入它的上下文。就我个人而言，我喜欢将递归理解为如下。

> *递归是通过解决更小的子问题来解决问题的一种方式。*

# 在我们开始之前…

![](img/b571071ff1142e5d5d4331b8d1ec21dd.png)

照片由[将](https://unsplash.com/@will0629?utm_source=medium&utm_medium=referral)放在[的 Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上

**停止遍历函数调用！**

这是学生学习递归时的一大障碍。他们试图查看每个 **单个**函数调用在**发生了什么，并试图跟踪他们解决方案中的每一步。**

你不需要知道每一步发生了什么。如果你想开始解决递归问题，你必须愿意大胆尝试。你必须相信。假设是需要的，也是解决这类问题所必需的。

# 怎么做

首先，让我们做一个最简单的递归问题。

## **问题:对从 1 到 n 的所有值求和**

```
function sumTo(n) {}
```

# **步骤 1)知道你的功能应该做什么**

解决递归问题的第一步，是知道你的函数应该做什么。

![](img/28e4c981fdd7c53787943a78a70994b6.png)

读者:你以为我是傻逼吗？

这似乎是显而易见的，但却是被忽略的重要一步。你需要思考你的功能 ***应该做什么*，**而不是它 ***目前做什么*。**

看看我们的 sumTo()函数，很明显函数 ***应该*** 返回一个从 1 到 n 的整数和。

```
/*
  sumTo() takes an integer n and returns the sum of all integers      from 1 to n
*/function sumTo(n) {}
```

# **步骤 2)挑选一个子问题，假设你的函数已经在处理它了**

> **…子问题…？**

一个 ***子问题*** 是比你的原问题**小**的任何问题。

我们最初的问题是对从 1 到 n 的所有值求和。在这种情况下，一个子问题是对所有数字求和，直到一个比 n 小的值

```
*// If sumTo(n) was our original problem, these are all considered subproblems because they are smaller versions of the original problemsumTo(n-1)
sumTo(n-2)
sumTo(n-3)
...
sumTo(1)*
```

*但是为了使解决你的问题更容易，我们需要选择一个*合适的*子问题。*

***挑选一个合适的子问题***

*挑选子问题有很多方法。一个好的开始策略是选择一个子问题作为 ***尽可能接近原*** 。既然我们 ***假设***sumTo()函数 ***已经工作了*** ，为什么不挑选一个值为我们解决大部分问题呢？*

*在这种情况下，由于我们的问题求解 n，那么最佳子问题应该求解 n-1。*

*使用 n-1 作为我们的子问题*

```
*/*
  sumTo() takes an integer and returns an integer n
  that is the sum from 1 to n
*/ // n is our original problem
function sumTo (n) { // Using n-1 as our subproblem, it returns the sum from 1 to n-1.
  const solutionToSubproblem = sumTo(n-1) 
}*
```

*但是等等！我们如何使用一个还没有定义的函数呢？？？*

*你是对的，我们还没有定义任何东西，但这就是我在文章开头的意思。为了解决一个递归问题，让我们 ***假设*** 这个函数已经对我们想要的任何子问题起作用。*

*因为我们的子问题选择，我们已经有了从 1 到 n-1 的所有值的和。我们现在需要做的就是做最后一次飞跃。*

# *第三步:找到子问题的答案，用它来解决原来的问题。*

*![](img/81275a49cdbc000d74f3a842f4c1c093.png)*

*我们已经解决了我们的子问题。所以下一个问题是…*

> ****我们如何把子问题的解，拿来解决原来的问题？****

*到目前为止，我们已经解决了 1 到 n-1。但是我们如何用它来求解 n 呢？*

```
*// n is our original problem
function sumTo (n) { // Using n-1 as our subproblem, it returns the sum from 1 to n-1.
  const solutionToSubproblem = sumTo(n-1) 
}*
```

*我们再来思考一下我们原来的问题。*

*我们想求从 1 到 n 的和，我们已经有了从 1 到 n -1 的解。*

*如果我们有从 1 到 n-1 的解，我们如何得到从 1 到 n 的和？*

```
*sumTo(n-1) // 1 + 2  ... n-2 + n-1
sumTo(n)   // 1 + 2  ... n-2 + n-1 + n*
```

*有了一些基本的代数，我们需要做的就是在我们子问题的解上加 n，这就解决了我们原来的问题。*

```
*// What we've already determined
sumTo(n-1) // 1 + 2  ... n-2 + n-1
sumTo(n)   // 1 + 2  ... n-2 + n-1 + n// Using our solution to the subproblem to solve the original
sumTo(n-1) + n  // 1 + 2  ... n-2 + n-1 + n
sumTo(n)        // 1 + 2  ... n-2 + n-1 + n*
```

*或者在代码中…*

```
*function sumTo (n) { // n is our original problem
  const solutionToSubproblem = sumTo(n-1) // n-1 is our subproblem

  return solutionToSubproblem + n
}*
```

*正如你所看到的，我们找到了子问题的解决方案，并发现如何用它来解决原来的问题。这就是所谓的寻找*递归关系。**

# *第四步，你已经解决了 99%的问题。剩下的 1%？基本情况。*

*您的函数正在调用自己，因此它可能会永远运行下去。这就是为什么我们需要添加一个基础案例来阻止它。*

***什么是基本案例，我们如何确定基本案例？***

*基本情况是我们停止递归的一种方式。通常，它可以是函数开头的一个简单的 if-else 语句。*

*如果条件已经达到其基本情况，它将阻止更多的函数调用。要选择一个基本案例，请考虑以下内容。*

> **“不需要额外计算的* **最容易的可能问题** *是什么？”**

*在我们的例子中，n = 0。*

*很明显，从 0 到 0 的所有值的和是 0，所以为什么要做更多的递归呢？这就是我们定义基本案例的地方。*

```
*function sumTo (n) {
  if (n === 0) { return 0 }
  const solutionToSubproblem = sumTo(n-1)

  return solutionToSubproblem + n
}*
```

*现在我们有了一个基本案例。现在有一个递归停止的点。*

***就这样***

*![](img/a80c0572912d38a7ea53fb6003d493cc.png)*

*胜利尖叫！！！—海绵宝宝 S3E1*

*这就是我们的答案。总之，解决递归问题包括以下内容*

*   *记住函数 ***应该*** 做什么，而不是它当前做什么*
*   *识别适当的子问题*
*   *使用子问题的解决方案来解决原始问题*
*   *撰写基本案例*

# *更多示例*

## ***问题:反转一个字符串***

```
*function reverse(s) {}*
```

*函数要做什么 ***？函数 ***应该*** 返回一个字符串的反向副本。****

```
*// Reverses a string s
function reverse(s) {}*
```

*我们的问题是反转一个字符串 s。让我们想一个子问题，它会使解决这个问题变得容易。让我们用“*你好*”作为我们的主要问题。*

*还是那句话，让我们假设*reverse()已经起作用了。为了让我们的生活更容易，我们选择一个子问题来解决我们的大部分问题。在这种情况下，让我们只对除第一个字母以外的所有字母调用 reverse()。**

```
**reverse("Hello") // "Hello" is our original problem"
reverse("ello")  // "ello" can be used as our subproblem**
```

> **如果你想知道为什么我选择“ello”作为子问题，请注意你也可以选择“Hell”作为子问题。我选择这个“ello”作为子问题，因为它将导致*更容易掌握代码。(请随意尝试其他子问题选项)***

**我们如何用我们的子问题来解决我们的原始问题？在这种情况下，我们可以将原问题的第一个字母附加到子问题解的末尾。**

```
**// What we determined so far
reverse("Hello") // "olleH"
reverse("ello")  // "olle"// Using our subproblem to solve the original
reverse("Hello")      // "olleH"
reverse("ello") + "H" // "olleH"**
```

**使用我们的例子，让我们把这个答案抽象为适用于任何字符串。**

```
**function reverse (s) {
  const subproblem = s.slice(1, s.length) // exclude first letter
  const reversedSubproblem = reverse(subproblem) return reversedSubproblem + s[0]
}**
```

**最后，让我们确定我们的基本情况。对于我们的问题，我们可以传入的最简单的*值是什么，使得 ***不需要*** 额外计算？答案是空字符串。空字符串的逆序是什么？当然是空弦。***

```
**function reverse (s) {
  if (s === '') { return '' } // Base case const subproblem = s.slice(1, s.length)
  const reversedSubproblem = reverse(subproblem) return reversedSubproblem + s[0]
}**
```

## **问题:返回斐波那契数列中的第 n 项**

**![](img/c957119d86af8a88d071a28d4051ffb3.png)**

**由[罗尔夫·纽曼](https://unsplash.com/@junglerolf?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片**

**啊，是的，臭名昭著的斐波那契问题…**

```
**// Fibonacci Sequence
0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, ...function fibTerm(n) {}**
```

**一旦你熟悉了斐波那契数列的工作原理，你会注意到第 n 项是它的前两个*项之和。***

***比如说…***

```
***5 is the sum of the two previous values 3 and 2.
34 is the sum of the two previous values 21 and 13.
233 is the sum of the two previous values 144 and 89.***
```

***与我们的其他问题不同，我们的原始问题需要我们解决**两个子问题**。***

```
***// Fibonacci Sequence (using n = 7 as an example)
0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, ...
               ^  ^   ^
              n-2 n-1 nThe nth term, is the sum of the n-1 and n-2 term.***
```

******假设*** 我们的函数已经起作用了，我们用它来计算前面两项。***

```
**// Fibonacci Sequence
0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, ...function fibTerm(n) {
  const term1 = fibTerm(n-1)  // Our two sub problems
  const term2 = fibTerm(n-2)
}**
```

**为了解决我们最初的问题，让我们返回两个子问题的和。**

```
**// Fibonacci Sequence
0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, ...function fibTerm(n) {
  const term1 = fibTerm(n-1)
  const term2 = fibTerm(n-2)

  // Solving the original problem using our subproblem solutions
  return term1 + term2 
}**
```

**找到基本情况可能有点棘手。在我们的例子中，我们有两种基本情况，n = 0 和 n = 1。原因是这些值没有前面两项可以计算。**

**使用 n = 0 和 n = 1 作为我们的基本情况，我们完成了我们的功能。**

```
**// Fibonacci Sequence
0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, ...function fibTerm(n) {
  if (n === 0) { return 0 }
  if (n === 1) { return 1 } const term1 = fibTerm(n-1)
  const term2 = fibTerm(n-2) return term1 + term2 // Solving the original problem
}**
```

# **C++ / Python 解决方案**

****问题:从 1 到 n 的求和****

```
**// C++
int sumTo(int n) {
  if (n == 1) { return 1 }
  return n + sumTo(n)
}// Python
def sumTo(n) {
  if (n == 1 ) { return 1 }
  return n + sumTo(n)
}**
```

****问题:将一个字符串从 1 反转到 n****

```
**// C++
string reverse(string n) {
  if (n == '') { return ''}
  return reverse(n.substr(1, n.length()-1 ) + n[0]
}// Python
def reverse(n) {
  if (n == '') { return ''}
  return reverse(n[1:]) + n[0]
}**
```

****问题:求斐波那契数列的第 n 项****

```
**// C++
int fibTerm(int n) {
  if (n == 0) { return 0 }
  if (n == 1) { return 1 }
  return fibTerm(n-1) + fibTerm(n-2)
}// Python
def fibTerm(n) {
  if (n == 0) { return 0 }
  if (n == 1) { return 1 }
  return fibTerm(n-1) + fibTerm(n-2)
}**
```