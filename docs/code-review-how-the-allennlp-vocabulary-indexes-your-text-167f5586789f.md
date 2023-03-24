# 代码审查:AllenNLP 词汇如何索引你的文本

> 原文：<https://levelup.gitconnected.com/code-review-how-the-allennlp-vocabulary-indexes-your-text-167f5586789f>

![](img/c09bcc3abd814fca55d07e558b947344.png)

尽管 AllenNLP 为库中几乎所有的模块提供了很好的指南，但我仍然在许多方面对词汇表是如何构造的感到困惑。具体来说，我们回答以下问题/讨论。

*   构造:token/id 对如何添加到`Vocabulary`中？
*   用于文本字段:词汇是如何用于文本索引的？
*   `Vocabulary`用于标签索引？
*   有什么警告吗

## 第 1 节:如何将令牌/id 对添加到`Vocabulary`？

**From instances** :正常情况下，`from_instance`方法可以构造一个`counter: Dict[*str*, Dict[*str*, *int*]]`,其中外部键是为每个名称空间保留的，内部字典存储 token/id 对。`counter`将被`_extend`方法进一步分解为`self._token_to_index`和`self._index_to_token`属性。
具体来说，它调用每一个`Instance`对象的`count_vocab_items`，它又调用每一个`Field`对象的`count_vocab_items`，每一个`Field`对象又调用每一个`TokenIndexer`对象的`count_vocab_items`。因此，计数项目的功能代码确实在每个`TokenIndexer`对象中。`TokenIndexer`中的这个`count_vocab_items`将匹配名称空间和`counter`的外部键名，以扩展条目或增加条目数。下面是`SingleIdTokenIndexer`中的代码。

```
def count_vocab_items(self, token: Token, counter: Dict[str,   
Dict[str, int]]):
        if self.namespace is not None:
            text = self._get_feature_value(token)
            if self.lowercase_tokens:
                text = text.lower()
            counter[self.namespace][text] += 1
```

## 第 2 部分:如何为文本索引消耗词汇？

`Vocabulary`将与`TokenIndexer`协作来索引`Field`对象中的令牌。具体来说，`TokenIndexer.tokens_to_indices`方法将采用一个词汇对象作为参数来匹配名称空间和索引标记。

如上，功能代码其实在`TokenIndexer`里。下面是`SingleIdTokenIndexer`中的代码。

```
def tokens_to_indices(
        self, tokens: List[Token], vocabulary: Vocabulary
    ) -> Dict[str, List[int]]:
        indices: List[int] = []
    for token in itertools.chain(self._start_tokens, tokens, self._end_tokens):
            text = self._get_feature_value(token)
            if self.namespace is None:
                indices.append(text)  # type: ignore
            else:
                if self.lowercase_tokens:
                    text = text.lower()
                indices.append(vocabulary.get_token_index(text,   self.namespace)) return {"tokens": indices}
```

`tokens_to_indices`方法就是`Textfield.index`方法，如下所示。注意下一节讨论的`Textfield.index`和`LabelField.index`的区别。

```
def index(self, vocab: Vocabulary):
        self._indexed_tokens = {}
        for indexer_name, indexer in self.token_indexers.items():
            self._indexed_tokens[indexer_name] = indexer.tokens_to_indices(self.tokens, vocab)
```

## 第 3 节:标签索引词汇

它在构造和使用上都不同于文本索引。

*   构造期间没有为填充和未知令牌保留 id
*   没有`TokenIndexder`:`LabelField`本身包含名称空间(通常硬编码为“labels”)来从标记到索引字典(即`Vocabulary._token_to_index[“labels”]`)中提取标签索引，如下面的`LabelField`中的代码所示。

```
def index(self, vocab: Vocabulary):
        if not self._skip_indexing:
            self._label_id = vocab.get_token_index(
                self.label, self._label_namespace  # type: ignore
            )
```

## 第 4 部分:注意事项

**忘记将** `**TokenIndexer**` **应用到** `**TextField**`:这是我们用 AllenNLP 处理数据时最常犯的错误之一，因为逻辑上词汇表中的 token/id 映射对于索引`TextField`中的 token 就足够了。但是，我们需要`TokenIndexer`的原因有很多。

*   一个常见的原因是添加特殊标记(开始或结束标记)
*   另一个原因是`Textfield`中的 token 可能与词汇表中 token/id 映射的粒度不匹配。例如，我们将文本标记成单词，但是词汇表包含字符的标记/id 映射。这听起来有些奇怪:如果我们想在字符级别上索引文本，为什么要将文本标记为单词而不是字符。据我所知，我猜想结合单词索引(使用`SingleIdTokenIndexer`)和字符索引(`TokenCharacterIndexer`)对我们有好处。

所以，记得应用`TokenIndexer`。下面是在一个文本字段中同时使用单词索引和字符索引的代码。

```
text_field.token_indexers={
"tokens":
   SingleIdTokenIndexer(namespace="token_vocab"),"token_characters":
   TokenCharactersIndexer(namespace="character_vocab"),}
```