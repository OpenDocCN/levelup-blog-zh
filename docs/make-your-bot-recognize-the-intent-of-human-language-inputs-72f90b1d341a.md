# 让你的机器人识别人类语言输入的意图

> 原文：<https://levelup.gitconnected.com/make-your-bot-recognize-the-intent-of-human-language-inputs-72f90b1d341a>

![](img/0f0b8e389c7d898fcd7509670d31cef1.png)

谢尔盖·阿里安诺夫摄影

自然语言的样本通常是上下文相关的，并且主要由意图驱动。因此，作为一名聊天机器人开发者，你的主要任务是“教”你的机器人如何理解用户说话的意图和上下文。在我之前的[文章](https://medium.com/@jxireal/make-your-bot-understand-the-context-of-a-discourse-4b740d46166c)中，我提到了如何让你的机器人识别正在处理的句子的上下文，在之前的话语中找到替代的先行词。在本文中，我将分享一些技巧，告诉你如何使用句法依赖标签和命名实体让你的机器人识别句子背后的意图。

# **用户请求的例子**

无论你的聊天机器人是用于点餐、订票还是叫出租车，它都需要理解顾客的意图，从而正确地接受订单。例如，考虑以下可能提交给订票聊天机器人应用程序的请求:

```
I want to book two tickets to Thailand tomorrow morning.
```

经过处理后，上面的句子应该分解成如下所示的结构:

```
Intent: bookTicket
Quantity: two
Destination: Thailand
Date: tomorrow
Time: morning
```

要用 spaCy 这样的 NLP 工具实现这一点，您需要使用两种基于语法依赖解析和命名实体识别的技术，这将在下一节中解释。

# **句法依存标签**

句法依存分析是从句子中提取意图时所依赖的关键技术。spaCy 有一个句法依存解析器，可以揭示句子中各个单词之间的句法关系，用一条弧线连接句法上相关的单词对。以下脚本说明了如何从示例句子中导出语法弧:

```
import spacy
nlp = spacy.load('en')
doc = nlp(u'I want to book two tickets to Thailand tomorrow morning.')
for token in doc:
  print(token.text, token.dep_, token.head.text)
```

这将为您提供以下输出:

```
I         nsubj     want
want      ROOT      want
to        aux       book
book      xcomp     want
two       nummod    tickets
**tickets   dobj      book** to        prep      book
Thailand  pobj      to
tomorrow  compound  morning
morning   npadvmod  book
.         punct     want
```

注意连接“book”和“tickets”的弧线(以粗体突出显示)。在这一语法相关对中，单词“tickets”是直接宾语，如分配给该单词的“dobj”依存标签所示。

# **为意图提取导航依存关系树**

这个关系中的中心词是动词“book ”,它是句子的及物动词。在大多数情况下，及物动词/直接宾语对，如果能在句子中找到的话，最能描述句子背后的意图。下面的代码片段显示了如何从句子中提取这个语法对:

```
…doc = nlp(u'I want to book two tickets to Thailand tomorrow morning.')
for token in doc:
  if token.dep_ == 'dobj':
    print(token.head.text+token.lemma_.capitalize())
```

但是要注意，有时表达意图的句子可能不包括及物动词/直接宾语对，如下例所示:

```
I need to sign up for a cloud service.
```

在这个例子中，动作动词“sign”通过介词“for”连接到名词“service”(动作所应用的对象)。

# **寻找具有句法依赖性的必要细节**

探索句子中的依存关系不仅在意图识别时有用。在我们的特定示例中，您可以搜索要预订的机票数量作为直接对象的数字修饰符，如下面的代码片段所示:

```
…doc = nlp(u'I want to book two tickets to Thailand tomorrow morning.')
for token in doc:
if token.dep_ == 'dobj':
print('Intent: ', token.head.text+token.lemma_.capitalize())
print('Quantity: ', [t.text for t in token.lefts if t.pos_ == 'NUM'][0])
```

这将为您提供以下输出:

```
Intent:   bookTicket
Quantity: two
```

正如您可能猜到的，这是一个简化的实现。在现实世界的代码中，你必须从用户可能使用另一个及物动词的事实出发，比如说:

```
I’d like two tickets to Thailand tomorrow morning.
```

这个例子说明，当涉及到意图识别时，直接宾语可能比它的及物动词更重要。你可能会争辩说，在句子中充当直接宾语的词也可能有同义词，比如说:

```
I’d like two seats to Thailand tomorrow morning.
```

要解决这个问题，您可以使用同义词列表。

# **用命名实体提取细节**

既然您已经提取了样本的意图，您还需要收集一些细节，比如目的地、日期和时间。在 spaCy 中，这可以通过命名实体识别器来完成。在最简单的形式中，这可以如下实现:

```
…doc = nlp(u’I want to book two tickets to Thailand tomorrow morning.’)…for ent in doc.ents:
  if ent.label_ == 'GPE':
    print('Destination: ', ent.text)
  if ent.label_ == 'DATE':
    print('Date: ', ent.text)
  if ent.label_ == 'TIME':
    print('Time: ', ent.text)
```

产生以下输出:

```
Destination:  Thailand
Date:         tomorrow
Time:         morning
```

你可能还是需要把‘明天’变成一个确定的数据。这就是日期时间库派上用场的地方。