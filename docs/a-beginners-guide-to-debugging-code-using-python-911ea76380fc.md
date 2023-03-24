# 使用 Python 调试代码的初学者指南

> 原文：<https://levelup.gitconnected.com/a-beginners-guide-to-debugging-code-using-python-911ea76380fc>

![](img/f153fddb08a96bed3609f8d2fb62a781.png)

米格尔·Á拍摄的照片。来自[佩克斯](https://www.pexels.com/photo/close-up-shot-of-keyboard-buttons-2882552/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的帕德里纳

你有没有注意到你自己试图从头开始学习编码，但是相反，你花了几个小时盯着一个空白页或者无休止地浏览你最终确定的工作流程？原因可能是当您看到屏幕底部的红色粗体错误文本时，内心深处会有一些恐惧，有时会感到非常沮丧。尽管如此，作为一个经历了四年计算机科学课程并在技术行业工作了三年多的人，我想让大家知道，从构建阶段的开始到结束，看到编码错误是正常的。我将通过简单的例子来介绍一些调试策略，希望可以帮助你或任何试图进入编码领域的新手。

# 注意 Python 中区分大小写的语法:

语法错误是我们的软肋，尤其是如果你声明了很多变量，而这些变量最终可能会因为出现的错误而遗漏。语法错误通常是打字错误、缺少括号和逗号。随着代码变得越来越长，你必须非常小心，并且你需要保持一个有组织的结构。我喜欢在记事本上写下变量名的列表，然后写一个简单的注释，说明这个变量在代码中做了什么。您可以通过对代码进行注释来做到这一点，这使得下一个接手代码的开发人员更加容易。

## 示例显示了 Python 中的一个简单错误

```
print "Hello World!"
```

## 输出

```
line 1
    print "Hello World!"
          ^
SyntaxError: Missing parentheses in call to 'print'. Did you mean print("Hello World!")?
```

# **饲养蟒蛇异常:**

什么是例外？异常是在程序执行期间发生的干扰程序流程的事件。本质上，当代码运行时的开始阶段出现异常时，它将停止其余代码的执行。那么我们如何在 Python 中引发异常呢？在代码中，可以通过在条件语句中使用关键字*“raise”*来使用[引发异常](https://www.w3schools.com/python/ref_keyword_raise.asp)。当您*引发一个异常时，您可以定义在条件语句不满足时应该出现的硬编码文本。如果你很好奇，可以点击这里的链接[查看一些你可能在代码中看到的常见异常错误。](https://www.tutorialsteacher.com/python/error-types-in-python)*

## *示例显示了用于错误处理的*“raise”*语法*

```
*z = "This is not an integer!"

if not type(z) is int:
  raise TypeError("Only integers are allowed")*
```

## *输出*

```
*line 4, in <module>
    raise TypeError("Only integers are allowed")
TypeError: Only integers are allowed*
```

# *捕捉和处理 Python 异常:*

*Python 中处理异常有什么用？如果在代码运行时没有正确处理异常，程序将会崩溃。处理异常是很重要的，尤其是当你的程序有多个相互调用的函数时。捕捉异常的最佳实践是使用[*【try】*和*【except】*子句](https://www.w3schools.com/python/python_try_except.asp)。*“try”*子句允许您测试代码中的错误，然后在其后的*“except”*子句允许您处理错误。这是在运行编译器时处理意外错误的好方法。*

## *示例显示了异常的处理*

```
*try:
 print(x)
except:
 print(“x is not declared so an exception occurred.”)*
```

## *输出*

```
*x is not declared so an exception occurred.*
```

## *示例显示了内置异常(ValueError)*

```
*while True:
    try:
        z = int(input("Please enter a number: "))
    except ValueError:
        print("That was not a valid number.  Try again!")*
```

## *输出*

```
*Please enter a number: 1
Please enter a number: 12
Please enter a number: Hello!
That was not a valid number.  Try again!
Please enter a number:*
```

# *在 Python 中断言:*

*Python 中的 assert 语句将检查条件是否为真。[*【assert】*语法](https://www.w3schools.com/python/ref_keyword_assert.asp)可以接受两个参数:条件和消息。假设条件为真，该语句用于继续执行。但是如果 assert 语句为 false，Python 将引发*“assertion error”*异常，并给出指定的错误消息。*

## *示例显示了如何使用 assert 来检查条件是否为真*

```
*z = 1
assert z > 0
print('z is a positive number.')*
```

## *输出*

```
*z is a positive number.*
```

## *示例显示变量不满足条件，因此它将返回 AssertionError*

```
*z = -1
assert z > 0
print('z is a positive number.')*
```

## *输出*

```
*line 2, in <module>
    assert z > 0
AssertionError*
```

# *在 Python 中登录:*

*对于任何经历测试阶段的开发人员来说，日志记录都是最有价值的工具之一，甚至可以帮助您发现在最初设计时从未想过的新测试场景。幸运的是，Python 有一个内置的日志模块，可以通过键入*“导入日志”轻松地导入到您的代码中*这里有一个很好的[链接](https://docs.python.org/3/library/logging.html)到关于登录 Python 可以做什么的文档。通过利用*“基本配置”*配置，您可以定制日志记录以满足您的需求，在这里您可以决定文件名、文件格式、日期格式和严重性级别(严重、错误、警告、信息、调试和不设置)。*

## *如何在 Python 中设置简单记录器的示例*

```
*import logging

logger = logging.getLogger()
logger.setLevel(logging.DEBUG)
logging.basicConfig(filename="test.log", format='%(asctime)s %(filename)s: %(message)s', datefmt='%H:%M:%S')
logger.info("testing information message1!")*
```

## *输出*

*生成名为 test.log 的文本文件*

# *结论:*

*我希望这篇文章对编程新手和试图理解调试代码中出现的问题的最佳方式的人有所帮助。我希望这可以作为你的垫脚石，并变得更加舒适地处理可能意想不到的错误。*