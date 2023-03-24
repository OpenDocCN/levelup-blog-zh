# Python 中的单元测试:如果你不测试你的代码，没人会测试

> 原文：<https://levelup.gitconnected.com/unit-testing-in-python-if-you-do-not-test-your-code-no-one-will-6504c7cda7d1>

甚至在你写程序之前就发现错误

![](img/a370b603fc4961d1c1eff57459968e04.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=152459)

Python 是一种多用途语言，用于一切后端。在本文中，我将教你用 Python 执行基本的单元测试，如何模拟模块，并确保你的代码是干净的。

# 什么是单元测试？

单元测试是测试代码的方法之一。其他方式包括功能测试、集成测试、回归测试等等。测试对于任何更大的代码库来说都是至关重要的，因为它让您可以快速迭代和执行更改，而不用太担心会出现什么问题。

单元测试是抽象层次上最低的测试方法。单元测试关注的是独立测试各个模块和功能*。也就是说，通过确保系统的所有部分都正常工作，你可以假设整个系统工作正常。*

*当然是在理想世界里。虽然单元测试非常有价值，但是整个系统不仅仅是其各个部分的总和:即使每个功能都如预期的那样工作，您仍然需要测试它们配合得有多好(这超出了本文的范围)。*

# *单元测试与 TDD 有什么关系？*

*单元测试是 TDD 的基础之一。TDD 代表测试驱动开发，是一种生产高质量软件的方法。本质上，这一切都归结为这些:*

1.  *先写测试。考虑系统的不同部分如何独立工作，并编写测试来验证它们的预期行为。您的测试**肯定**会失败，因为您还没有编写任何实际的代码。*
2.  *编写足够通过测试的代码。*
3.  *重构你刚刚写的东西。*
4.  *转到步骤 1。*

*就是这样！整个周期需要几分钟，但是这个简单的技术将确保你的系统(1)按照规范工作,( 2)是可测试的。但是要开始使用 TDD，您需要首先掌握基本的单元测试。*

# *`unittest`模块*

*Python 中的单元测试可以通过`unittest`模块开箱即用。我们将通过开发我们自己的*阶乘*函数来学习单元测试。首先，打开一个新的 Python 文件，然后编写以下代码:*

*在第 1 行，您可以注意到导入的`unittest`模块。在第 4-6 行，我们通过扩展`TestCase`类定义了一个*测试用例*。在它里面，我们目前只有一个测试，`test_something`。测试以`test_`为前缀是很重要的，这样测试运行人员就可以找到它们。最后，在第 9-10 行，我们执行测试。如果您现在尝试运行此文件，测试将会失败:*

*这是因为在第 6 行我们断言`True`和`False`相等，这显然是错误的。让我们改变它来测试我们的(不成文！)功能:*

*如果你现在运行这个，它仍然会失败，因为我们还没有写`factorial`函数。让我们通过这个测试:*

```
*def factorial(num): 
    return 6*
```

*虽然这在数学上不合理，但这使我们的测试通过了。让我们再写一些测试:*

```
*def test_factorial(self): 
    self.assertEqual(factorial(1), 1) 
    self.assertEqual(factorial(2), 2) 
    self.assertEqual(factorial(3), 6) 
    self.assertEqual(factorial(10), 3628800)*
```

*检查测试现在是否失败(因为`factorial`总是返回 6 ),并对阶乘函数进行修改:*

```
*def factorial(num): 
    if num == 1: 
        return 1 
    return num * factorial(num - 1)*
```

*如果你运行这个，测试通过:*

```
*Ran 1 test in 0.002s OK*
```

*现在，让我们后退一步，理解我们刚刚做了什么。*

# *断言*

*当我们编写测试时，我们想要测试一些东西。如果某个东西相等或不相等，如果一个函数抛出异常，如果一个方法用某些参数调用，等等。这种检查被称为*断言*。断言是为了代码正常工作而总是被认为是正确的事情。*

*你刚才看到的一个断言是不是`assertEqual`。它检查传入的两个变量是否相等。`assertEqual`通过`self`可用，由`TestCase`父类提供。以下是一些您会发现有用的常见断言:*

*   *`assertEqual(x, y)/assertNotEqual(x, y)`*
*   *`assertTrue(x)/assertFalse(x)`*
*   *`assertIs(x, y)`*
*   *`assertIsNone(x)`*
*   *`assertIn(x, y)`*
*   *`assertIsInstance(x, y)/assertNotIsInstance(x, y)`*
*   *`assertRaises(exc, fun, *args, **kwargs)`*
*   *`assertGreater(x, y)/assertLess(x, y)/assertGreaterEqual(x, y)/assertLessEqual(x, y)`*

*其中一些可以使用上下文管理器，比如`assertRaises`。这使得代码更容易阅读:*

```
*with self.assertRaises(Exception):
    do_something_that_throws("please")*
```

*使用这些断言，您几乎可以测试任何东西！*

# *嘲弄的*

*回想一下，单元测试是在*隔离*中测试系统的各个部分。但是，我们设计的系统从来不会出现这种情况。不同的部分依赖于其他部分，为了使测试成为可能，有时您需要模拟它们。例如，如果您正在测试一个 HTTP 响应解析器的行为，那么没有必要执行一个实际的 HTTP 响应:您所需要做的只是模拟它。*

*因此，模仿是一种抽象软件模块实现的方法，这些模块与你测试的行为无关。此外，您可以断言是否调用了 mock，调用了多少次，以及向它提供了什么参数。毫无疑问，这些额外的断言将使您的测试更加健壮。*

*Python 中的模仿也可以通过`unittest`模块获得。让我们从一个简单的例子开始。假设您编写了一个调用所提供回调的函数:*

```
*def call_this_function(func): 
    func()*
```

*要测试它，你只需要通过一个`MagicMock`(可通过`unittest.mock`):*

*`MagicMock`是一种特殊类型的物体。你可以调用它本身，调用你能想到的任何方法它都不会抛出。相反，它会记住所有的调用，并通过断言提供给你。注意，在这种情况下，断言是在`mock`对象上调用的，而不是在`self`上。下面是一些可用于模拟对象的断言:*

*   *`assert_called`*
*   *`assert_called_once`*
*   *`assert_called_with`*
*   *`assert_called_once_with`*
*   *`assert_not_called`*

# *嘲弄进口*

*有时候，当你在测试一个类的时候，你需要模拟一个导入其中的函数或者类。考虑这个例子:*

*现在，为了测试这段代码，您想要模拟`api_action`函数。这实际上很容易用`patch`装饰器(可从`unittest.mock`获得)来完成:*

*`patch`装饰器被应用到我们正在使用的测试函数中。它接受模拟模块的路径作为参数。这里有一个非常重要的注意事项:您指定相对于测试代码的路径。例如，`main.py`文件从`some_library`导入了`api_action`函数。然后，我们将`main`模块导入到测试文件中。这意味着我们模仿了在`main`内部导入的`api_action`函数，因此有了`'main.api_action'`路径。如果我们要写`'some_library.api_action'`，这就不行了。我们还指定了`side_effect`参数，它本质上是 mock 将返回的返回值。然后，模拟对象作为参数传递给测试函数，这样我们就可以断言它。我们断言(1)返回值被转发,( 2)在正确的端点上调用了`api_action`。*

# *结束语*

*感谢你阅读这篇文章，我希望你喜欢它。请在评论中告诉我你用 Python 测试的经历！*