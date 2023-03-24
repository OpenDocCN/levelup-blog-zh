# 使用 Python 翻译文本

> 原文：<https://levelup.gitconnected.com/translate-text-using-python-d4bdc75f7208>

## 在本文中，我们将讨论如何使用 Python 通过 Google Translate API 翻译文本。

![](img/89048983bafc0b5d9ee337a2372b8c6a.png)

[Firmbee.com](https://unsplash.com/@firmbee?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/translate?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

**目录**

*   介绍
*   基本用法
*   指定源语言和目标语言
*   结论

# 介绍

我们都使用在线翻译或在字典中查找单词。有多个在线翻译可用，他们维护得很好。这个领域的领头羊当然是谷歌。

我们中的大多数人已经熟悉了谷歌翻译服务，我们中的一些使用谷歌浏览器的人也安装了他们的插件。这项服务完全免费，可以翻译 100 多种不同语言的文本。

如果你看到了这个页面，你可能会好奇我们如何使用 Python 来自动化或编程。

要继续学习本教程，我们需要以下 Python 库:googletrans。

如果您没有安装它，请打开“命令提示符”(在 Windows 上)并使用以下代码安装它:

```
pip install googletrans==4.0.0-rc1
```

# 基本用法

首先，我们需要导入 **Translator** 对象并创建其类的一个实例:

现在让我们看看如何快速使用它。

默认情况下，**翻译器**对象会自动检测语言。为了更好地理解这一点，我会用法语给它一些短语。让我们看看会发生什么:

我们得到了:

```
Translated(src=fr, dest=en, text=Hello my friend!, pronunciation=None, extra_data="{'confiden...")
```

到目前为止，我们得到了一些有效的返回，但格式可能不是我们想要的。这里重要的是，翻译器自动检测到我们是从法语( **src=fr** )翻译过来的，并且(可能因为我在加拿大)它检测到目的语言是英语( **dest=en** )。

我们想要的主要部分是翻译的实际文本，正确地翻译为“你好，我的朋友！”(**text =你好我的朋友！**)。现在，如果我们只想检索翻译的文本，我们可以简单地这样做:

获得:

```
Hello my friend!
```

你可能会问，译者对源语言是法语有多大把握？好问题！该库实际上提供了检查可信度的代码。让我们看看它是如何工作的:

我们看到:

```
Detected(lang=fr, confidence=None)
```

由于某种原因，翻译器没有显示检测到的语言的置信度，但它正确地将其识别为法语。

语言自动检测是一个非常有用的选项，它肯定会加快速度。但是如果我们想把法语翻译成西班牙语呢？下一节将解释如何做到这一点。

# 指定源语言和目标语言

让我们看看，如果我们想为我们的翻译指定源语言和目标语言，我们能做些什么。

首先，我们需要确保 Google 支持我们想要的语言。一般来说，在 99.9%的情况下，图书馆将拥有所需的一切。但是如果您想检查支持哪些语言，您可以执行以下代码:

所有支持的语言列表:

```
af : afrikaans
sq : albanian
am : amharic
ar : arabic
hy : armenian
az : azerbaijani
eu : basque
be : belarusian
bn : bengali
bs : bosnian
bg : bulgarian
ca : catalan
ceb : cebuano
ny : chichewa
zh-cn : chinese (simplified)
zh-tw : chinese (traditional)
co : corsican
hr : croatian
cs : czech
da : danish
nl : dutch
en : english
eo : esperanto
et : estonian
tl : filipino
fi : finnish
fr : french
fy : frisian
gl : galician
ka : georgian
de : german
el : greek
gu : gujarati
ht : haitian creole
ha : hausa
haw : hawaiian
iw : hebrew
he : hebrew
hi : hindi
hmn : hmong
hu : hungarian
is : icelandic
ig : igbo
id : indonesian
ga : irish
it : italian
ja : japanese
jw : javanese
kn : kannada
kk : kazakh
km : khmer
ko : korean
ku : kurdish (kurmanji)
ky : kyrgyz
lo : lao
la : latin
lv : latvian
lt : lithuanian
lb : luxembourgish
mk : macedonian
mg : malagasy
ms : malay
ml : malayalam
mt : maltese
mi : maori
mr : marathi
mn : mongolian
my : myanmar (burmese)
ne : nepali
no : norwegian
or : odia
ps : pashto
fa : persian
pl : polish
pt : portuguese
pa : punjabi
ro : romanian
ru : russian
sm : samoan
gd : scots gaelic
sr : serbian
st : sesotho
sn : shona
sd : sindhi
si : sinhala
sk : slovak
sl : slovenian
so : somali
es : spanish
su : sundanese
sw : swahili
sv : swedish
tg : tajik
ta : tamil
te : telugu
th : thai
tr : turkish
uk : ukrainian
ur : urdu
ug : uyghur
uz : uzbek
vi : vietnamese
cy : welsh
xh : xhosa
yi : yiddish
yo : yoruba
zu : zulu
```

从上面的列表中，我们可以肯定地知道法语和西班牙语是受支持的。下一步是弄清楚如何使用它。

该代码将与前一节中使用的自动检测代码非常相似。现在我们将指定源语言和目标语言:

我们得到了:

```
¡Buenos dias mi amigo!
```

请注意，在指定语言时，我们不使用实际的语言名称(法语、西班牙语)，而是使用缩写(fr、es)，完整的列表显示在上面的部分中，我们打印了所有支持的语言和键值对。

# 结论

在本文中，我们讨论了如何使用 Python 通过 Google Translate API 翻译文本。

通过学习这段代码，您应该能够将它扩展到翻译全文、列表、词典中的条目等等。

我也鼓励你看看我在 [Python 编程](https://pyshark.com/category/python-programming/)上的其他帖子。

如果你有任何问题或者对编辑有任何建议，请在下面留下你的评论。

*原载于 2020 年 10 月 14 日*[*【https://pyshark.com】*](https://pyshark.com/translate-text-using-python/)*。*