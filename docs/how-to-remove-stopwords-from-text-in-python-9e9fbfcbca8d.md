# 如何在 Python 中从文本中移除停用词

> 原文：<https://levelup.gitconnected.com/how-to-remove-stopwords-from-text-in-python-9e9fbfcbca8d>

## 如何在 Python 中从文本中移除停用词的演练示例

![](img/0abea3deb0252cd0c0927b47c1f90773.png)

照片由[纳丁·沙巴纳](https://unsplash.com/@nadineshaabana?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

[](https://jorgepit-14189.medium.com/membership) [## 用我的推荐链接加入媒体-乔治皮皮斯

### 阅读乔治·皮皮斯(以及媒体上成千上万的其他作家)的每一个故事。您的会员费直接支持…

jorgepit-14189.medium.com](https://jorgepit-14189.medium.com/membership) 

在许多 NLP 任务中，需要从文本中移除“停用词”。通常，我们所说的“停用词”指的是那些经常出现并且对句子的整体意思没有太大贡献的词。停用词的一些例子是`{"a", "an", "the", "this", "that", "is", "it", "to", "and"}`等等。

在本教程中，我们将展示如何使用 NLTK 库删除 Python 中的 stopwrods。

让我们加载库

```
import nltk
nltk.download('stopwords')
nltk.download('punkt')
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize
```

英语停用词列表如下:

```
stopwords.words('english')
```

但是，有些人可以创建自己的停用词表，例如:

```
stop_words = ["a", "an", "the", "this", "that", "is", "it", "to", "and"]
```

# 如何将停用词添加到 NLTK 停用词列表

或者您可以将自定义的停用词添加到 NLTK 停用词列表中。例如:

```
# stopwords from NLTK
my_stopwords = nltk.corpus.stopwords.words('english')# my new custom stopwords
my_extra = ['abc', 'google', 'apple']# add the new custom stopwrds to my stopwords
my_stopwords.extend(my_extra)
```

# 如何从 NLTK 停用词列表中移除停用词

同样，你可以使用列表理解从“停用词表”中删除一些单词。例如:

```
# remove these words from stop words
my_lst = ['have', 'few']# update the stopwords list without the words above
my_stopwords = [el for el in my_stopwords if el not in my_lst]
```

# 如何从文本中删除停用词

现在，我们准备从文本中删除停用词。我们考虑以下出于展览目的的无厘头文字。

```
my_txt = "I'm George. I live in Athens! This is my blog, hopefully you enjoy this post! Look at this!"filtered_list = []
stop_words = nltk.corpus.stopwords.words('english')# Tokenize the sentence
words = word_tokenize(my_txt)
for w in words:
    if w.lower() not in stop_words:
        filtered_list.append(w)

filtered_list
```

**输出:**

```
["'m",
 'George',
 '.',
 'live',
 'Athens',
 '!',
 'blog',
 ',',
 'hopefully',
 'enjoy',
 'post',
 '!',
 'Look',
 '!']
```

现在，我们可能想把这个列表转换成一个字符串。让我们开始吧:

```
my_clean_txt = " ".join(filtered_list)
my_clean_txt
```

**输出:**

```
"'m George . live Athens ! blog , hopefully enjoy post ! Look !"
```

最初由[预测黑客](https://predictivehacks.com/?all-tips=how-to-remove-stopwords-from-text-in-python)发布