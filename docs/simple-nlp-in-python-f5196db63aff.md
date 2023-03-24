# Python 中的简单 NLP

> 原文：<https://levelup.gitconnected.com/simple-nlp-in-python-f5196db63aff>

## 使用 TextBlob 包的 n 元文法检测

![](img/1bce39a393af8ac7c3ed86f6e1c1ec4d.png)

萨法尔·萨法罗夫在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄的照片

# NLP 分析可以简单吗？

随着大文本数据的增长，知道如何实现 NLP 任务的解决方案变得更加迫切，但这并不一定意味着您应该在编程方面非常高级。

想象一下，如果每个人都可以用 Python 中的一个简单命令轻松计算文本的情感、计算词频或检测单词之间的关系模式，那该有多好！这不再是梦了，TextBlob 包对于这样的任务来说非常方便。

> *T*[*ext blob*](https://textblob.readthedocs.io/en/dev/)*是一个非常棒的轻量级库，适用于各种各样的 NLP 任务。*

在本教程中，我试图阐明如何使用 TextBlob 在 Python 中执行 N-Grams 检测。

# 什么是 N-gram？

**N-grams** 表示一组给定文本中 N 个元素的连续序列。从广义上讲，这些项目不一定代表单词串，它们也可以是音素、音节或字母，这取决于你想完成什么。

N 元文法的交互式定义

然而，*自然语言处理*通常将 N-gram 称为单词串，其中 *n* 代表您要查找的单词数。通常区分以下类型的 N 元语法:

*   **Unigram**——内部只有一个字符串的 N 元语法(例如，它可以是给定句子中的一个独特的单词——*YouTube*或*抖音*，例如 *YouTube 正在推出一种新的简短视频格式，看起来很像抖音*)。
*   **2-gram** 或**Bigram**——通常是出现在一个文档中的两个字符串或单词的组合:*短格式视频*或*视频格式*将可能是某个文本语料库中 Bigram 的搜索结果(而不是*格式视频*、*视频短格式*，因为词序保持不变)。
*   **3-gram** 或**Trigram**——一个 N-gram 包含最多三个一起处理的元素(例如*短格式视频*或*新短格式视频*)等。

> *N 元语法在概率语言模型领域找到了它们的主要应用，因为它们估计单词序列中下一个项目的概率。*

*语言建模的方法*假设字符串中每个元素的位置之间有紧密的关系，计算下一个单词相对于前一个单词的出现。特别地，N 元模型如下确定概率— `N-1`。例如，三元模型(带有`N = 3`)将根据前面两个单词预测字符串中的下一个单词为`N-1 = 2`。

行业中实现 N-grams 模型的另一种情况可以是*剽窃检测*，其中比较从两个不同文本中获得的 N-grams，以计算出被分析文档的相似程度。

# Python 中使用 TextBlob 的 n 元语法检测

![](img/e94fb2fa5623fff2ff25a8204eb7a528.png)

照片由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 分析一个句子:

要开始检测 Python 中的 N-grams，首先必须通过以下命令安装 TexBlob 包:

```
pip install -U textblob python -m textblob.download_corpora
```

**注意**它还会下载一些文本语料库，我们以后用它来玩。

一旦环境设置好了，您就可以加载包并在一个例句中计算 N 元语法了。首先，我们将看看 M.Mullenweg 引用的 N-gram:

> *科技把人聚在一起才是最好的。*

让我们开始吧:

```
from textblob import TextBlob sentence = "Technology is best when it brings people together"
```

我们已经创建了一个包含我们想要分析的句子的`sentence`字符串。然后，我们将该字符串传递给`TextBlob`构造函数，将其注入到`TextBlob`实例中，我们将在该实例上运行操作:

```
ngram_object = TextBlob(sentence)
```

现在，让我们运行 N-gram 检测。首先，让我们做 2 克检测。这在`ngrams()`函数调用的参数列表中是特定的:

```
ngrams = ngram_object.ngrams(n=2) print(ngrams)
```

`ngrams()`函数返回由 **n 个**连续单词组成的元组列表。在我们的句子中，二元模型将给我们以下一组字符串:

```
[WordList(['Technology', 'is']), 
WordList(['is', 'best']), 
WordList(['best', 'when']), 
WordList(['when', 'it']), 
WordList(['it', 'brings']), 
WordList(['brings', 'people']), 
WordList(['people', 'together'])
]
```

# 文件分析

尽管这个 Python 库很简单，但 TextBlob 还提供了一系列高级分析功能。更多的时候，我们不是用单句来检测 N-grams。更常见的是处理文档、文章或更大的语料库。

在我们的下一个例子中，我们将使用来自美国消费者新闻与商业频道新闻门户网站[](https://www.cnbc.com/2020/10/15/bill-gates-antitrust-should-look-at-tech-companies-separately.html)*的一篇关于比尔盖茨的文章。*

*让我们为下面的分析创建一个文本文档，并将其命名为类似于`Input.txt`的东西:*

```
*import sys  *# Opening and reading the `Input.txt` 
file* corpus = open("Input.txt").read()*
```

*然后，像往常一样，我们将通过将`corpus`传递给构造函数并运行`ngrams()`函数来实例化一个`TextBlob`实例:*

```
*ngram_object = TextBlob(corpus) trigrams = ngram_object.ngrams(n=3) print(trigrams)*
```

*这将打印出我们提供的内容的三元模型。但是，请注意，根据您处理标点符号的方法，输出可能会有所不同:*

```
*[WordList(['Bill', 'Gates', 'says']),
WordList(['Gates', 'says', 'that']),
WordList(['says', 'that', 'antitrust']),
WordList(['that', 'antitrust', 'regulators']),
WordList(['antitrust', 'regulators', 'should'])
<...>
]*
```

*相比之下，给定文章的二元模型分析将为我们提供一个不同的列表:*

```
*ngram_object = TextBlob(corpus) Bigram = ngram_object.ngrams(n=) print(Bigram)*
```

*输出中的一个片段:*

```
*[WordList(['Bill', 'Gates']),
WordList(['Gates', 'says']),
WordList(['says', 'that']),
WordList(['that', 'antitrust']),
WordList(['antitrust', 'regulators'])
<...>
]*
```

*如你所见，在许多 NLP 项目中，N-Grams 检测是一项非常简单的任务，TextBlob 库对于初学者来说是一个强大的工具。它提供了一个简单的 API，让用户快速地执行 NLP 任务，以便成为高效的专家。*

*如果你喜欢阅读这样的故事，并想支持我成为一名作家，可以考虑[注册成为一名媒体成员](https://medium.com/@natalia.kzm/membership)。每月 5 美元，你可以无限制地阅读媒体上的故事。如果你用我的链接注册，我会赚一点佣金。*

*当我创作内容的时候，你也可以通过[给我买杯咖啡](https://ko-fi.com/nataliakzm)来支持我的不眠之夜。*

# *相关文章:*

*在下面的文章中，您可以通过 TextBlob 包进一步探索文本分析，并了解标记化方法:*

*[](https://medium.com/quick-code/simple-nlp-in-python-c73ef0dfc6) [## Python 中的简单 NLP

### 使用 TextBlob 包进行标记化

medium.com](https://medium.com/quick-code/simple-nlp-in-python-c73ef0dfc6)*