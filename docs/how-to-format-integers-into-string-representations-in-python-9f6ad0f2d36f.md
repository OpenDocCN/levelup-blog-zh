# 如何在 Python 中将整数格式化为字符串表示

> 原文：<https://levelup.gitconnected.com/how-to-format-integers-into-string-representations-in-python-9f6ad0f2d36f>

10000 到 10K | 999999 到 1M | 1234567890 到 1.23B

![](img/806853f6997e56af422442e718d2726e.png)

照片由[安德鲁·布坎南](https://unsplash.com/@photoart2018?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/number?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

通过阅读这篇文章，您将学会用 Python 将一个整数(123456)格式化成它自己的字符串表示(123K)。这有助于改善应用程序中的用户体验，因为将数字显示为字符串表示对用户来说更加连贯。在本教程中，我将展示两种不同的实现:

*   在[堆栈溢出](https://stackoverflow.com/questions/579310/formatting-long-numbers-as-strings-in-python#579376)上共享的代码的修改版本
*   我自己的自定义实现

话虽如此，但本文并未涉及 micro 和 nano 等小于 0 的字符串表示。一旦你完成了教程，你可以自由地自己实现它。

# 堆栈溢出

在撰写本文时，已经有一个关于堆栈溢出的主题，标题为“ [**在 python**](https://stackoverflow.com/questions/579310/formatting-long-numbers-as-strings-in-python) 中将长数字格式化为字符串”。该实现基于数字除法，以便获得相应的度量(K，M，B，T)。

在此之前，让我们创建一个通用函数，它将相应的输入解析并格式化为一个安全的数字。它还会使用字符串格式`3g`根据 3 个有效数字对数字进行舍入。这确保了 999999 将被转换为 1000000。

```
def safe_num(num):
    if isinstance(num, str):
        num = float(num) return float('{:.3g}'.format(abs(num)))
```

接下来，让我们探索一下 Stack Overflow 中提供的答案的修改版本。该函数每次将输入除以 1000，并将幅度增加 1，直到输入小于 1000。该函数还会在返回结果之前去掉结尾的`0`和`.`。

如果输入的值很小，这种实现在性能方面有优势，因为它从最小的度量循环到最大的度量。

缺点是它仅适用于 K、M、B 和 t 等通用指标。如果您打算将它用于其他语言，则由于每个量值之间的不规则性，它将不起作用。另外，如果你的数大于一万亿的倍数乘 1000，会得到一个`IndexError: list index out of range`错误。

# 自定义实现

让我们来看看另一种实现，它从最大度量循环到最小度量。每个指标及其各自的值都在 Python 字典中定义。它代表指标的实际值。如果除法的值大于或等于 1，循环将中断。返回的结果使用 f 字符串进行格式化。

就性能而言，当且仅当大部分输入是大数时，这种实现会更快，因为它从最大的度量开始循环。此外，您可以根据实际值指定自己的度量值对。如果您打算为其他语言复制相同的功能，这是非常有用的。

# 试验

让我们测试一下，看看两个函数是否返回相同的结果。将以下代码追加到函数下面。我已经声明了一个整数列表作为例子。您可以随意添加自己的值来测试这两个函数。

```
example_input = [1000, 999, 100, 99, 9996, 23, 123456, 10000, 999999, 1234567890, 5634723]for i in example_input:
    print(format_number_stack_overflow(i), format_number(i))
```

您应该在控制台上得到以下输出，这表明它们都返回了相同的预期输出。999 将作为 999 返回，因为考虑基于 3 个最高有效数字。另一方面，9996 将作为 10K 返回，因为它将由于字符串格式而被舍入。

```
1K 1K
999 999
100 100
99 99
10K 10K
23 23
123K 123K
10K 10K
1M 1M
1.23B 1.23B
5.63M 5.63M
```

# 结论

让我们回顾一下今天所学的内容。

我们从一个简单的问题陈述开始，说明了将整数转换成相应的字符串表示的好处。

然后，创建了一个简单的函数来格式化基于 3 个最高有效数字的数字。

我们还深入探讨了栈溢出实现的修改版本。

最后，我们尝试了我们自己的定制实现，它产生了相同的预期输出。

感谢你阅读这篇文章。希望在下一篇文章中再见到你！

# 参考

1.  [堆栈溢出格式长数字](https://stackoverflow.com/questions/579310/formatting-long-numbers-as-strings-in-python#579376)
2.  [Python stdtypes -字符串格式](https://docs.python.org/3.8/library/stdtypes.html#printf-style-string-formatting)