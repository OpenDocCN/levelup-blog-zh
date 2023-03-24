# 什么是列表理解？—第二部分

> 原文：<https://levelup.gitconnected.com/whats-in-a-list-comprehension-part-2-49d34bada3f5>

## 既然您已经学习了 Python 中的列表理解，我将带您看一个数据处理示例。

![](img/e642286f9c8f24b78ca6ce9da22ae6e2.png)

由[托比约恩·赫尔格森](https://unsplash.com/@tobben63?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

这是我之前的文章[的后续，什么是列表理解？一定要先查看一下——由于原文的受欢迎程度，我决定写这篇后续文章。在那篇文章中，我提到列表理解可以用来快速洞察数据以及模拟随机实验。在本文中，我将通过几个完整的例子来说明如何做到这一点。](https://towardsdatascience.com/whats-in-a-list-comprehension-c5d36b62f5)

上一次，我详细解释了列表理解的语法。我不会在这里重复所有的细节，但在我们进入数据之前，我会举一个更复杂理解的例子。

假设您有以下代码，它将用户定义的函数应用于单词列表的每个元素— *但*只应用于至少 4 个字母长的单词。

```
def abbreviate_word(word):
    return word[0] + word[-1] # take first and last letterwords = ['hi', 'bye', 'sun', 'moon', 'sky', 'earth']
abbrevs = []for word in words:
    if len(word) >= 4:
        abbrevs.append(abbreviate_word(word))
```

有没有可能通过翻译成列表理解来简化这个有点复杂的代码？当然可以！我们这样做如下(假设上面已经定义了`abbreviate_word`函数):

```
words = ['hi', 'bye', 'sun', 'moon', 'sky', 'earth']
abbrevs = [abbreviate_word(word) for word in words if len(word) >= 4]
```

瞧啊。我们已经把我们的函数简洁地浓缩成一行可读的代码。快速复习:

*   过滤首先发生；因此，对于每个单词，我们首先检查它是否至少有 4 个字母长。
*   如果上述情况成立，我们对其应用缩写函数，并将转换后的版本添加到新形成的列表中。
*   我们以列表`['mn', 'eh']`结束(这很有趣，因为如果你说得很快，听起来有点像 MnM——但是我跑题了)。

## 列表理解在数据分析中的应用

现在我们已经解决了这个问题，让我们来看一个具体的例子，看看在处理数据时如何使用列表理解。我承诺的第一个例子是使用基本的 MapReduce 模型快速处理数据。如果你不熟悉这个术语，它只是指 1)过滤/转换数据，然后 2)将所有数据合并成一个值的一种奇特方式。我们已经可以看到列表理解很好地模仿了这个模型。

此外，我在这里提出列表理解还有另一个重要原因:Python 数据程序员倾向于使用 Pandas 数据帧，但这并不总是数据的格式化方式。例如，在我目前担任助教的计算概念课上，学生们被要求从 MongoDB 收集数据并使用这些数据(他们甚至没有被告知熊猫的存在)。

我们以此为例。Mongo 以键值对的形式存储信息，您可以经常将这些信息作为[字典](https://towardsdatascience.com/whats-in-a-dictionary-87f9b139cc03)读入 Python。然后，假设您收集了以下数据，这些数据将城市映射到过去一周出现的 COVID 病例数。另外，请注意 1)这些数字是我编造的，2)`...`表示**许多**(想成千上万)我没有显示的条目。

```
{'Minsk': 3000, 'Lahore': 1200, 'Colombo': 800, ... , 'Miami': 8000}
```

您需要三样东西:1)拥有至少 1000 个案例的城市的平均案例数，2)拥有少于 3000 个案例的城市的名称，以及 3)所有案例的平方和(最后一个展示了 MapReduce 的完整概念)。所有这三个都可以通过列表理解来完成:

```
# Assume the above dictionary is called covid_casesfrom numpy import meanmean([value for value in dict.values() if value > 1000]) # 1[key for (key, value) in dict.items() if value < 3000]   # 2sum([value * value for value in dict.values()])          # 3
```

我不知道你怎么想，但我得说这非常方便和有用。

在我的第二个例子中，我承诺展示如何使用列表理解来使小型实验模拟更加 Pythonic 化。考虑下面这个模拟抛硬币的例子，摘自[加州大学伯克利分校的入门数据科学教科书:](https://inferentialthinking.com/chapters/09/3/Simulation.html)

```
import numpy as np
import random as randomdef one_simulated_value():
    outcomes = np.random.choice(['Heads', 'Tails'], 100)
    return np.count_nonzero(outcomes == 'Heads')num_reps = 20000   # number of repetitions

heads = make_array() # empty collection list

for i in range(num_reps):   # repeat the process
    new_value = one_simulated_value()  # simulate one value
    heads.append(new_value)            # augment the list

# That's it! The simulation is done.
```

假设函数和变量如上定义，将其简化如下:

```
[heads.append(one_simulated_value()) for i in range(num_reps)]
```

这就是全部了！这是列表理解的一个特别有趣的用例，因为您使用它来完成一项任务，而对实际的输出数组不感兴趣(在这种情况下，它只是 Python 中的一串`None`值，因为`None`是`append`函数的输出)。它使你的代码更干净——总是一个有价值的事业。

## 最后的想法

虽然我在本文中只举例说明了一些简单的情况，但是在 Python 中工作时，列表理解非常有用。我希望上面的详细演练已经让您更好地了解了如何在数据处理的上下文中应用这些理解的知识。永远记住最终目标——更干净、更 Pythonic 化的代码。如果你记住这一点，你就成功了。

下次见，伙计们！

**想擅长 Python？** [**获取独家，免费获取我简单易懂的攻略**](https://witty-speaker-6901.ck.page/0977670a91) **。**

## 参考

【1】*计算和推理思维*。[加州大学伯克利分校数据 8。](http://data8.org/)