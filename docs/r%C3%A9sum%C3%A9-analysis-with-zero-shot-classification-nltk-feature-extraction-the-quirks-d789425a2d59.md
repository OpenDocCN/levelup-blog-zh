# 零镜头分类简历分析，nltk 特征提取，怪癖

> 原文：<https://levelup.gitconnected.com/r%C3%A9sum%C3%A9-analysis-with-zero-shot-classification-nltk-feature-extraction-the-quirks-d789425a2d59>

CV 解析是一个问题陈述，迫切需要能够灵活处理模糊复杂性挑战的解决方案。CV 格式令人讨厌的模糊性加剧了这种情况，导致文本混乱，从而阻碍了一致解析
以识别特定数据元素的尝试。

因此，这篇文章试图研究一些策略，这些策略可能会提供访问路径，为解析练习的随机性带来一些方法上的相似性。在和一些 NLP 库调情的时候。

大多数简历解析文章提出使用正则表达式来筛选凝结的大量单词(在 PDF/DOC 到文本转换之后)以获得电子邮件、数字、技能等。本文中的方法旨在分离简历中的句子，然后分析每个句子以确定上下文，它是否包含工作经历简介/公司名称、技能组合、雇佣期或只是项目/责任的任意文本描述等。

这为机器学习非常擅长处理的文本分类练习奠定了基础。请注意，这篇文章的目的仅仅是介绍用 ML 解决这个问题的替代方法——构建一个完整的产品级解析器的最终选择将需要大量的微调，并且可能涉及各种方法的组合。

所以让我们把一个标准的简历切成几行，每一行都要进行分类。

首先，我倾向于使用零镜头分类进行第一次尝试，这基本上是 NLP 分类，很少或没有任何数据集的训练。这是为了避免繁琐的任务，即必须创建带有手动注释的训练数据或使预解析的样本与类别匹配。

在快照中，零镜头学习是指应用预先训练的分类器模型在它从未见过的文本序列上运行。零距离分类器是基于 NLI(自然语言推理)的原则，在一个句子中，这意味着解释一个序列来限定(遵循)或否定一个假设(指的是一个类别)。简而言之，这意味着我们可以通过零命中率分类器运行任何文本样本，同时为其提供一些预定义的类和预先训练的模型，如 BART 或 Roberta，以查看分类器预测的句子类型。

但是让我们先写代码，然后再谈…看看几行 zero shot 是如何对 CV 句子进行快速分类的。

Requirements.txt 来安装必要的库…前提假设是熟悉 python 和 regex。

```
numpy
pdfminer.six
transformers[torch]
nltk
```

使用的库是 HuggingFace Transformers，为了 CPU 友好，用一个较小的模型代替。

```
import re
import numpy as np
from pdfminer.high_level import extract_text
from transformers import pipelinelabels = ["date range or time period", "name of organization or company", "job title or designation", "programming languages", "software libraries", "educational institutes", "academic qualifications",  "project description"]
classifier = pipeline("zero-shot-classification",  model='cross-encoder/nli-distilroberta-base')def zero_shot_test(cv):
    cvtext = extract_text(cv)
    for text in cvtext.split("\n"):
        text = text.strip()
        text = re.sub("^[^A-Za-z0-9]","",text) 
        token_arr = re.split("(\t+|\s{4}|\|+|:)\s*", text) # a crude hack to split lines that have employment dates and company names in the same line
        for token in token_arr:
            token = token.strip()
            token = re.sub("^[^A-Za-z0-9]","",token)
            if len(token) > 0:
                res = classifier(token, labels)
                res_label = res["labels"][np.argmax(res["scores"])]
                print(f'text {token} is categorized as { res["labels"][np.argmax(res["scores"])] }')zero_shot_test("SomeCV.pdf")
```

下面是一些输出示例-

-设计、开发、修改、维护和改进网络应用程序和移动应用程序。被归类为*项目描述*
——React-Native、ReactJS、NodeJS、Android studio、Xcode 框架等技术。被归类为*编程语言*
——HTML、CSS、C++、JAVA、jQuery、Bootstrap、Git。被归类为*编程语言*
——Oracle、MySQL、MongoDB。被归类为*软件库*
-可视化代码、Android studio、XCode、Chrome 调试、Postman 被归类为*编程语言*
-2018 年 5 月-工作被归类为*日期范围或时间段*
-6 月-9 月 18 日被归类为*日期范围或时间段*
-国际科学奥林匹克银牌。被归类为*学历*
-B . sc . 2018 年以 74.01%的成绩从{编校}信息技术学院毕业。被归类为*教育机构*
-03/2019-目前被归类为*日期范围或时间段*
- JavaScript、Python、C++、HTML、CSS 被归类为*编程语言*
-计算机科学 B.Tech 被归类为*学历*

我觉得这是个不错的开端。进一步的步骤可以将这些分类的行提供给熟练的正则表达式解析器，以提取我们需要的精确 CV 数据元素。
但这里有几个失误——
与现有客户和新客户密切合作，了解被归类为*组织或公司的名称*。！！

现在，人们总是可以通过微调预训练的变压器模型来采取另一种分类方法，但如果需要进一步追求零命中率，则可以通过对我们提供给分类器的标签进行文本调整来提高命中率，使它们更好地与样本句子的更明显总结保持一致。

但是现在我们将把这个主题留在这里，因为我们将改变话题，尝试另一种方法，这种方法将使我们对分类练习有更多的控制。

我们将尝试通过特征提取进行文本分类，回到比 HuggingFace 更简单的库，以及在特征定义后进行分类训练的更基本的方法。

因此，我们用 NLP 学者的宠儿 nltk 测试了(相对较浅的)水域。老实说，这不是最好的选择，但目标是尝试操作将决定分类练习的特征。

同样，这是一个简单的函数，它将一个样本行作为输入，并为该样本构建有资格作为“特征”的内容。关于特征构建的快速脚注:
如下面的函数所示，它定义了哪些特征与确定句子的类别相关，以及如何对这些特征进行编码——在这种情况下，我们创建了一个具有简单关键字的特征字典，这些关键字确定句子样本的上下文。这导致分类器，在我们的例子中是一个相对幼稚的朴素贝叶斯，来确定句子的类别。

```
def cv_features(sentence):
    features = dict()
    edu_pattern=r'([A-Z][a-z]+\s*(University|School|College))|((University|School|College)\s*[A-Z][a-z]+)'
    date_grammar = r"""
    DT: {<IN|JJ|NN|NNP><CD><.*>?<NN|NNP><CD>}
        {<CD><CD><.*>?<CD><CD>}
    """
    tokens = nltk.word_tokenize(sentence)
    tokens = [x for x in tokens if x not in stop_words ]
    if re.search('([A-Za-z0-9]+\s*,\s*){2,}',sentence):
        tokens = [ x for x in tokens if x != ',' ]
    tags = nltk.pos_tag(tokens)
    # chunking of POS tags to identify noun chunks and consequent NER's i.e. ORGANIZATION or DATE etc..
    ne_list =  [ (x[0][1], x.label())  for x in nltk.chunk.ne_chunk(tags) if hasattr(x, "label") ]
    if any (True for x in tokens if x.lower() in skills_set):
        features["technologies"] = True
        return features if any (True for (pos, label) in ne_list if label == 'ORGANIZATION' and pos == 'NNP'):
        features["organization"] = True
    if re.search(edu_pattern, sentence, re.IGNORECASE):
        features["educational institute"] = True
    if any (True for (pos, label) in ne_list if label == 'DATE' ):
        features["date"] = True
    if (not features):
        # RegExpParser works on groups of POS tags to match
        cp = nltk.RegexpParser(date_grammar)
        tree = cp.parse(nltk.pos_tag(nltk.word_tokenize(re.sub(r'[^A-Za-z0-9]',' ', sentence))))
        for subtree in tree.subtrees():
            if hasattr(subtree, "label") and subtree.label() == "DT":
                features["date"] = True
    if (not features):
        features["description"] = Truereturn features
```

这些是基本的函数和简单的正则表达式，用于检查句子是否包含日期？正如 RegExpParser 的分块练习所定义的那样。老实说，虽然分块使用 POS 标签来匹配我们想要的，但我更喜欢使用普通的正则表达式来检测日期，因为它更容易预测。
nltk POS 并不总是可靠的，因为月份名称可以被解释为 NN、NNP 甚至 JJ(名词或形容词)。不太可靠，但这里的目的还是演示特性构建。
——类似地，我们使用普通的正则表达式或带有 nltk 的 NER 标签来窥探教育机构和公司名称——简单的东西。
-技术名称可以使用一组技术样本进行匹配，如

`skills_set = set([‘java’,’c++’,’c’,’python’,’javascript’,’cassandra’,’julia’, ‘oracle’,’mongodb’,’.net’……………..])`

完成特征定义后，让我们训练一个样本语料库-

```
def train_corpus():
    global classifier
    train_arr=[]
    fp = open('cv_train_data.csv', 'r')
    for x in fp:
        rec = x.split(",")
        train_arr.append(( ",".join(rec[0:-1]), rec[-1:][0].strip() ))
    featuresets = [(cv_features(txt), category) for (txt, category) in train_arr]
    classifier = nltk.NaiveBayesClassifier.train(featuresets)
```

训练用的是平原。CSV 文件，带有 CV 线样本和它的类别，简单如-
`Jan 2019-Feb 2022,dates
C, Python, Java, .net, technologies
won 3rd prize in chess competition, description
IBM Corp.,organization ........`

同样，这只是一个非常简单的列表，您可以将其扩展为一个包含更多类别的完整列表，但是特征提取将需要筛选文本中反映这些附加类别的元素。

现在让我们用一个样本 CV 运行这个，使用与零镜头完全相同的块——只是替换零镜头分类的 2 行

```
 res = classifier(token, labels)
    res_label = res["labels"][np.argmax(res["scores"])]
    print(f'text {token} is categorized as { res["labels"][np.argmax(res["scores"])] }')
```

随着

```
 res = classifier.classify(cv_features(token))
    print(f'text {token} is categorized as { res }')
```

然后看一些结果输出，如果我们注意到的话，它比零炮运行要快得多

React-Native，ReactJS，NodeJS，JavaScript，Clojurescript，SQL，NOSQL。C 被归类为*技术*
HTML，CSS，C++，JAVA，jQuery，Bootstrap，Git。被归类为*技术*
2014 年 6 月-2018 年 6 月被归类为*日期*
2016 年 9 月—2017 年 10 月被归类为*日期*
B.Sc.IT 于 2018 年以 74.01%的成绩从{ Redacted }信息技术学院毕业。被归类为*教育学院*
时间监控应用程序就像一个生物识别应用程序，雇主每天进出被归类为*描述*

现在一些真正的大失误——
React——Native、ReactJS、NodeJS、Android studio、Xcode 框架等技术。被归类为*组织*
在整个软件过程中积极协调 UI 团队、测试团队、经理、同事成员被归类为*组织*

这是因为 nltk 的 NER 分类将许多技术名称识别为 ORG 的。人们会注意到这里面有许多不着边际的预测，因为 NER 在 nltk 中可以抛出荒谬的东西，就像他们之前对月份名称所做的那样。

我们看到零镜头在主要类别中工作得更好，但是 nltk 分类在这里仅仅是为了显示特征提取的简易性。如果您想使用 nltk 的分类，强烈建议使用您自己的特征提取块，使用比这里介绍的更复杂的方法提取特征字典元素。
类似地，建议使用另一个 NER 解析器，POS tagger，直观脚本来确定和填充特征字典，而不是 nltk 的名词分块，因为后者不准确。

因此，我们谈到了两种 CV 曲线分析方法，它们从方法和努力的角度来看都非常不同。如果我必须建议一个产品级的解析，我会强烈推荐 Spacy，这是我个人最喜欢的自然语言。

但这一点将留待下次讨论！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)