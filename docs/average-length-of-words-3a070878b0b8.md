# Python 地图和 reduce 的使用

> 原文：<https://levelup.gitconnected.com/average-length-of-words-3a070878b0b8>

## *测试您的 Python 技能，使用 map 和 reduce 函数计算一个句子中单词的长度，并将其聚合以找到总长度*

![](img/cfbc54dc6b6fa45d8524a5ac2491cff3.png)

由 [Alex Padurariu](https://unsplash.com/@alexpadurariu?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

本文展示了 Python map 和 reduce 函数的用法。这是通过展示 map 和 reduce 如何用于计算句子中单词的平均长度来实现的。

节目中用的句子是“我看见猫和老鼠了！”。

这个句子有 6 个单词。除了感叹号，单词的总长度是 18 个——我(1)看见(3)猫(3)和(3)老鼠！(5).因此，平均长度应计算为 3，即 18/6。让我们看看它是如何完成的。

程序从输入句子开始。

```
sentence = 'I see the cat and mouse!'
print('The sentence is \'{}\' and object type is {}'.format(sentence, type(sentence))
```

```
The sentence is 'I see the cat and mouse!' and object type is <class 'str'>
```

接下来，特殊字符喜欢！从存储在列表中的句子和单词中删除。

```
import re
words = re.split('\s', re.sub('[\.\?!,:;\'\-\{\}\[\]\(\)]{1}', '', sentence))
print('The words in the sentence are {} and object type is {}'.format(words, type(words)))
```

```
The words in the sentence are ['I', 'see', 'the', 'cat', 'and', 'mouse'] and object type is <class 'list'>
```

接下来，使用 map 函数计算单词长度，然后将输出存储在一个列表中

```
word_lens = list(map(lambda word: len(word), words))
print('The word lengths are {}'.format(word_lens))
```

```
The word lengths are [1, 3, 3, 3, 3, 5]
```

接下来，使用 reduce 函数对单词长度求和

```
import functools
tot_len = functools.reduce(lambda a, b: a + b, word_lens)
print('The total length is {}'.format(tot_len))
```

```
The total length is 18
```

或者，可以用一行代码调用上面的 map 和 reduce

```
tot_len = functools.reduce(lambda a, b: a + b, list(map(lambda word: len(word), words)))
print('The total length is {}'.format(tot_len))
```

```
The total length is 18
```

最后，计算句子中单词的平均长度

```
print('Average word length is: {}'.format(round(tot_len / len(words)*1.0, 2)))
```

```
Average word length is: 3.0
```

编码快乐！