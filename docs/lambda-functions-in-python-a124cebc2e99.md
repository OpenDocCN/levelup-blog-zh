# Python 中的 Lambda 函数

> 原文：<https://levelup.gitconnected.com/lambda-functions-in-python-a124cebc2e99>

## 蟒蛇皮短裤——第 1 部分

在这一系列的文章中，我们将介绍每个开发人员都应该知道的 Python 特性。在第 1 部分中，我们来看看 lambda 函数。

![](img/c39087c39630340e645006c36730f128.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/python-programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

Python 中的 Lambda 函数是小型的匿名函数，主要用于简洁和方便。

它们的语法如下:

```
lambda arguments: expression
```

它们可以接受任意数量的参数，并将执行`expression`并返回其值。

如上所述，这通常是为了方便和缩短代码，例如在迭代器或生成器函数中。

作为第一个例子，考虑定义一个λ来平方一个数:

```
square = lambda x: x**2
print(square(5))
```

这里，我们利用了函数是 Python 中第一类对象的事实，将我们的 lambda 函数赋给变量`square`，稍后调用它并打印结果。

现在让我们使用 Python 的`map`函数将它放到一个更真实的环境中:它采用一个函数`f`和一个列表，并返回一个新的列表，其中`f`应用于列表中的所有值:

```
standard_list = [0, 1, 2, 3, 4, 5]
squared_list = list(map(lambda x: x**2, standard_list))
print(squared_list)
```

第 1 部分到此结束，感谢收听——欢迎随时回来了解更多内容:

*   第 2 部分:Python 中的[迭代器](https://medium.com/@hrmnmichaels/iterators-in-python-bd7332ddbc00)
*   第 3 部分:[Python 中的生成器和生成器表达式](https://medium.com/@hrmnmichaels/generators-and-generator-expressions-in-python-a8d2e700945e)
*   第 4 部分:[Python 中使用 enumerate()和 zip()的高级迭代](https://medium.com/@hrmnmichaels/advanced-iteration-in-python-with-enumerate-and-zip-676b31ceac44)
*   第 5 部分:[用上下文管理器管理 Python 中的资源(带语句)](https://medium.com/@hrmnmichaels/managing-resources-in-python-with-context-managers-with-statement-f07afc1afb4f)
*   第 6 部分:[用 Python 生成临时文件和目录](https://medium.com/@hrmnmichaels/generating-temporary-files-and-directories-in-python-dfc11f017a97)
*   第 7 部分:[Python 中的日志记录](https://medium.com/@hrmnmichaels/logging-in-python-3df84ce78cef)
*   第八部分:[Python 中的部分函数](https://medium.com/gitconnected/partial-functions-in-python-66998eef1384)
*   第 9 部分:[Python 中的 f 字符串](https://medium.com/gitconnected/f-strings-in-python-80e30c64fb95)