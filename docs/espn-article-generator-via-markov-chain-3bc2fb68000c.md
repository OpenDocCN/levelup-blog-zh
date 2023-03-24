# 基于马尔可夫链的 ESPN 文本生成器

> 原文：<https://levelup.gitconnected.com/espn-article-generator-via-markov-chain-3bc2fb68000c>

如果下一个状态仅依赖于前一个状态，则马尔可夫链被认为是“无记忆的”。使用这个概念，我们可以构建一个基本的文本生成器，其中序列中的下一个单词将只依赖于前面选择的单词。这两项之间的转换将基于我们从数据中观察到的概率。

***本文假设你对所涉及的主题有一个很好的理解。*

## 查找数据集

导航到 ESPN，我抓住了显示的第一个[文章](https://www.espn.com/nba/story/_/id/30321409/sources-los-angeles-lakers-talks-acquire-dennis-schroder-oklahoma-city-thunder)。我以前写过一篇关于如何从 HTML 响应中抓取文本的文章。有关信息，请参见下面的链接。这只是一个例子，但请随意拉下更多的文章。你可能想保持相同的风格。

```
**import** requestsurl **=** *'*[*https://www.espn.com/nba/story/_/id/30321409/sources-los-angeles-lakers-talks-acquire-dennis-schroder-oklahoma-city-thunder*](https://www.espn.com/nba/story/_/id/30321409/sources-los-angeles-lakers-talks-acquire-dennis-schroder-oklahoma-city-thunder)*'*article **=** *scrape_article*(request.get(url).text)
```

[](https://medium.com/@theslaps/adventures-in-building-a-custom-ner-model-with-spacy-part-1-scrapping-espn-49bb9c9a7bd6) [## 用 Spacy 构建定制 NER 模型的冒险—第 1 部分:废弃 ESPN

### 我最近决定，我想建立一个命名实体识别(NER) NBA 模型。这是关于我如何去…

medium.com](https://medium.com/@theslaps/adventures-in-building-a-custom-ner-model-with-spacy-part-1-scrapping-espn-49bb9c9a7bd6) 

## 预处理文章

这涵盖了在文本清理过程中人们通常会经历的几个区域。我们打破了一些缩写，删除所有格，并将文本设置为小写。我们将把数字分成三种不同的形式，分别代表价格*(薪水)*、浮点*(球员平均水平)*和整数*(选秀位置)*。

```
**import** re**def** format_article(text)**:**
    ***## annoying terms***
    document **=** re.sub(r'\sNo[.]\s', ' Number ', text) ***## contraction(s)***
    document **=** re.sub(r"doesn't", 'does not', document)

    ***## possessive case(s)***
    document **=** re.sub(r"'s", '', document) ***## formatting***
    document **=** document.lower()
    document **=** re.sub(r'[\n,]', ' ', document) ***## numbers***
    document **=** re.sub(r'[$][\d]+[.][\d]+', ' PRICE ', document)
    document **=** re.sub(r'[\d]+[.][\d]+', ' FLOAT ', document)
    document **=** re.sub(r'[\d]+', ' INTEGER ', document) ***## hypens***
    document **=** re.sub(r'[-]', ' ', document)

    **return** document
```

## 标记文章

最后的预处理步骤之一是将文章分成每个句子的标记。需要注意的是，我们为每个句子添加了一个开始和停止状态。

```
**from** collections **import** defaultdict
**from** nltk.tokenize **import** sent_tokenize, word_tokenize**def** tokenize_sentence(sentence: str)**:**
    transformers **=** **{**
        ***'PRICE'***: '<price>'**,**
        ***'FLOAT'***: '<float>'**,**
        ***'INTEGER'***: '<integer>'
   ** }**

    ***## remove end of sentence punctuation***
    sentence **=** re.sub(r'[.]$', '', sentence) **return** **[*'<start>'*]** + **[**
        transformers[token]
        **if** token **in** transformers.keys()
        **else** token
        **for** token **in** word_tokenize(sentence)
    **] + ['<end>']**sentence_tokens **= [**
    tokenize_sentence(sentence)
    **for** sentence **in** sent_tokenize(format_article(article))
**]**
```

## 句子输出

```
**[** sentence **for** sentence **in** sentence_tokens[2:3] **]*****## Results
['<start>', 'as', 'part', 'of', 'the', 'trade', 'the', 'lakers', 'are', 'preparing', 'to', 'send', 'the', 'player', 'they', 'select', 'with', 'the', '<integer>', 'th', 'overall', 'pick', 'and', 'guard', 'danny', 'green', 'to', 'the', 'thunder', 'sources', 'said', '<end>']***
```

## 建立马尔可夫链

这里的目标是构建状态/转换(下一个)术语对。对于这个实现，我们收集多个不唯一的术语。这完全没问题，因为这只会增加该项被采样的概率。因此，如果我们有一个集合`['a', 'a', 'the']`，我们将有' a' = 2/3 和' the' = 1/3。

```
**from** collections **import** defaultdictstates **=** defaultdict(list)
**for** tokens **in** sentence_tokens:
    combinations **=** zip(tokens[:-1], tokens[1:])
    **for** start, end **in** combinations**:**
        states[start].append(end)
        **for** key **in** states.keys()**:**
            states[key] **=** sorted(states[key])states***## Results
{'<start>': ['ahead',
              'as',
              'espn',
              'if',
              'los',
              'los',
              'oklahoma',
              'schroder',
              'schroder'
              ...***
```

## 句子生成器

你会发现大多数关于这个主题的例子都是从一个种子短语开始的。为此，我们将只使用我们的`<start>`令牌。随机均匀采样用于我们的过渡态，以产生我们的下一个状态。

```
**import** numpy **as** np**def** query(transitions)**:**
    **if** len(transitions) **==** 1**:**
        **return** transitions[0] **return** np.random.choice(transitions)**def** generate_sentence(states, kill **=** -1)**:**
    sentence **=** [] current_state **=** ***'<start>'***
    current_state **=** query(states[current_state])
    sentence.append(current_state.capitalize())

    i **=** 1
    **while** current_state **!= *'<end>'*:**
        **if** i **>** 1**:**
            sentence.append(current_state) current_state **=** query(states[current_state])
        **if** kill **!=** -1 **and** i **==** kill:
            **break**

        i **+=** 1

    **return** sentencen_sentences_in_article **=** 5
max_terms_per_sentence **=** 25article **=** []
**for** _ **in** range(n_sentences_in_article)**:**
    sentence **=** generate_sentence(states, max_terms_per_sentence)
    article.append(sentence)
```

## 我们生成的文章

```
*The new orleans pelicans as part of the lifting of the starting lineup alongside chris.**Though he excelled in consecutive years.**As part of the deal can not outright send the transaction window on his desire.**Schroder finished runner up for carmelo anthony davis trade market and guard danny green to.**Schroder was never shy about his role averaging <float> points and a rule that restricts.*
```

![](img/5a251123cfa6a62e36ed32024e3c121c.png)

乔希·米勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片