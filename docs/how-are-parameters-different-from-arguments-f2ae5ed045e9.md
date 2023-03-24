# 形参和实参有什么不同

> 原文：<https://levelup.gitconnected.com/how-are-parameters-different-from-arguments-f2ae5ed045e9>

## 最常被误解的核心概念

![](img/b3fbfeb907cf750d65ae3f23005c0aed.png)

照片由 [Phyllis Poon](https://unsplash.com/@bunnyhop?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

不考虑编程语言，许多新手误解的一个核心概念是，他们认为参数和形参是相同的。

然而，它们显然是不一样的！

# 崩溃

为了更好地理解这一点，让我们考虑下面的代码片段。下面的代码是用 Python 写的。因为这是任何编程语言的核心概念，所以您可以用您喜欢的任何语言来尝试。

下面是一个示例代码片段:

```
def addition(number1, number2):
  return number1 + number2addition(1, 2)
```

上面的代码是一个 python 函数，执行两个数的简单加法。它接受两个整数参数，并返回两个数的和。

## 因素

为函数定义的输入称为参数。简单地说，参数是定义函数时在括号中定义的变量。

```
def addition(number1, number2):
  return number1 + number2
```

在上面的例子中，数字 1 和数字 2 是加法函数的参数。

## 争论

给定参数的实际值称为 ***自变量。*** 简单来说，参数就是调用时传递给函数的值。

```
addition(1,2)
```

在上面的例子中，1 和 2 是加法函数的参数。

# 自我测试

让我们用一个简单的代码片段来确认您对参数和形参的理解。

## 问题

下面的代码片段将一个字符串作为输入，并向用户显示一条消息。

```
def greet(first_name, last_name):
  return f"Hello {first_name} {last_name}"greet("John","Doe")
```

在参考下面的答案之前，请在上面的代码片段中确定函数`greet`的参数和自变量。

## 回答

*   **参数:**名，姓
*   **论据:**约翰，无名氏

# 结论

我希望这个故事已经阐明了参数和参数之间的区别！

分享知识是获得知识的唯一途径。对于那些可能从这个故事中受益的人，请随意添加并通过评论做出贡献。

玩得开心！享受编码！