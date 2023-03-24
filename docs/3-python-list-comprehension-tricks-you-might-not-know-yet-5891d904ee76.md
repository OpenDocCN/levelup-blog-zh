# 你可能还不知道的 3 个 Python 列表理解技巧

> 原文：<https://levelup.gitconnected.com/3-python-list-comprehension-tricks-you-might-not-know-yet-5891d904ee76>

![](img/0dd9de8b1a621f0ccf701e4cc2978d53.png)

## 列表理解不仅仅是为了列表

如果你使用过 Python，你可能会熟悉*列表理解*语法。

如果你从这样一个列表开始:

```
>>> words = ['goodbye', 'cruel', 'world']
```

您可以像这样创建一个新列表:

```
>>> lengths = [len(x) for x in words]
[7, 5, 5]
```

与将项目追加到空列表相比。

```
# Not the pythonic way!
lengths = []
for word in words:
    lengths.append(len(word))
```

使用 list comprehension *和*创建列表更加简洁，也更容易阅读。

但是你知道你可以用同样的技术创造其他类型的物体吗？

列表理解不仅仅是为了列表。

我们将向你展示 3 个你可能还没用过的技巧。它们将有助于使你的代码更容易阅读，更容易编写。

## 1.词典理解

您也可以使用相同的列表理解语法创建词典。

```
>>> data = {word: len(word) for word in words}
{"goodbye": 7, "cruel": 5, "world": 5}
```

注意键`word`和它的值`len(word)`用冒号`:`分开。

这意味着您需要使用`word: len(word)`而不是`word, len(word)`，这将是一个语法错误。

额外收获:创建字典的另一个有用的技巧是将两个列表放在一起。

```
>>> words = ["hello", "old", "friend"]
>>> lengths = [len(word) for word in words]
>>> data = dict(zip(words, lengths))
{"hello": 5, "old": 3, "friend": 6}
```

## 2.过滤

您可以过滤包含的项目。

```
>>> words = ['deified', 'radar', 'guns']
>>> palindromes = [word for word in words if word == word[::-1]]
['deified', 'radar']
```

过滤也适用于字典理解。

```
>>> words = ["not", "on", "my", "watch"]
>>> data = {w: w[::-1] for w in words if len(w) < 5}
{'not': 'ton', 'on': 'no', 'my': 'ym'}
```

## 3.发电机

您也可以使用相同的语法创建*生成器*。

生成器是类似于列表的 *iterables* ，但是生成器中的项目只有在实际使用时才会被创建。用过之后，它们被扔掉，不能再用了。

所以生成器比列表占用更少的内存，但是你只能迭代一次。

这里有一个例子。(图片我们有一个名为`bigdata.txt`的文件，有数百万行)。

```
>>> text = open("bigdata.txt", "r")
>>> lines = (line for line in text if line.startswith("2020-01-01"))
<generator object <genexpr> at 0x10fe3d550>
```

我们已经打开了一个大型数据文件，并创建了一个生成器对象。生成器对象将只包含以`2020-01-01`开头的行。

因为我们使用了生成器理解而不是列表理解，所以我们不需要读取整个文件。

让我们找出文件中各行的平均长度。

首先，我们将编写一个助手函数。

```
def average_line_length(lines):
    num_lines = 0
    total_length = 0
    for line in lines:
        num_lines += 1
        total_length += len(line)
    return total_length / num_lines
```

我们现在将通过`lines`发电机。

```
>>> result = average_line_length(lines)
24
```

使用 generator comprehension 的好处是我们不需要读入大型数据文件。

但是我们仍然可以将生成器传递给其他需要可迭代的函数，比如`average_line_length` 函数。

感谢阅读！如果您觉得这很有用，我还写了其他 Python 和数据科学技巧，所以请关注我，获取更多类似本文的文章。

[](https://towardsdatascience.com/how-to-reuse-your-python-models-without-retraining-them-39cd685659a5) [## 如何重用您的 Python 模型而无需重新训练它们

### Python 的对象序列化库简介

towardsdatascience.com](https://towardsdatascience.com/how-to-reuse-your-python-models-without-retraining-them-39cd685659a5)