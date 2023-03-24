# 如何在正则表达式中匹配多个条件

> 原文：<https://levelup.gitconnected.com/how-to-match-multiple-conditions-in-regex-a380affa175e>

## 组合正则表达式以获得干净、高效的模式匹配

![](img/b0d467e2b7d6e74912e10cda6f342df5.png)

照片由[努贝尔森·费尔南德斯](https://unsplash.com/@nublson?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/search?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在之前的帖子中，我写了关于[如何让 YouTube 链接在你的媒体 pos](https://medium.com/gitconnected/how-to-make-youtube-links-show-up-as-a-thumbnail-on-medium-with-javascript-1b6c966a4bca) t 中显示为缩略图。为了做到这一点，你需要利用一些 [***正则表达式***](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions) 来匹配和替换所提供的 URL 中的某些模式。

在这篇文章中，我加入了一个奖金部分，在那里你可能会遇到两种不同的情况，需要加以考虑。你可以用两个不同的语句来处理这两种情况。或者，您可以通过将多个条件合并到一个 regex 语句中，以干净、高效的方式来实现。

在这篇文章中，我将更深入地探讨如何做到这一点！下面是一个简单的正则表达式，它可以和`[match](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match)`一起使用，这样你就可以找到字符串中满足正则表达式条件的任何部分。

```
const line = 'My name is Matthew Croak. I love the NY Mets.';
const regex = /[A-Z]/g;
const found = line.match(regex);

console.log(found)

> (7) ['M', 'M', 'C', 'I', 'N', 'Y', 'M']
```

上面的代码试图找到字符串中所有 ***大写字母的字符。*** 现在，如果我们想匹配所有大写的 ***和小写的*** 字母呢？让我们将正则表达式更新为`const regex = /[A-Za-z]/g;`。

我们的结果应该如下所示。

```
['M', 'y', 'n', 'a', 'm', 'e', 'i', 's', 'M', 'a', 't', 't', 'h', 'e', 'w', 'C', 'r', 'o', 'a', 'k', 'I', 'l', 'o', 'v', 'e', 't', 'h', 'e', 'N', 'Y', 'M', 'e', 't', 's']
```

这太棒了！如果我们想匹配一个*整词*呢？比方说，`love`？我们可以这样做！

```
const line = 'My name is Matthew Croak. I love the NY Mets.';
const regex = /love/g;
const found = line.match(regex);

console.log(found)

> ['love']
```

看起来很简单，对吗？如果我们把`love`放在括号里，像`/[love]/g`一样，我们会得到下面的值。

```
> (9) ['e', 'e', 'o', 'l', 'o', 'v', 'e', 'e', 'e']
```

这是因为括号被用作“字符类”。意思是“来自`a`、`b`或`c`的任何角色”。字符类可以使用范围，例如`[a-d]` = `[abcd]`。这里可以看到原栈溢出解释[。](https://stackoverflow.com/a/9801697/7927036)

## 多重条件

回到帖子最初的目的:*如何在 Regex 中使用多个条件？*

假设我们想要找到单词`love`和`Mets`。我们可以使用管道(`|`)编写下面的正则表达式。

```
const line = 'My name is Matthew Croak. I love the NY Mets.';
const regex = /love|Mets/g;
const found = line.match(regex);

console.log(found)

> ['love', 'Mets']
```

管道表示一个[逻辑或表达式](https://techdocs.broadcom.com/us/en/symantec-security-software/identity-security/identity-governance/14-2/administrating/troubleshooting/using-the-pipe-character-in-regular-expressions.html)。当您想要查找一个模式或另一个模式的实例时，可以使用它。比方说，如果我们在`love`和`Mets`之间使用了一个空格，或者如果我们在它们之间什么也没有使用，我们将从正则表达式中得到 ***nothing*** 。

这是因为匹配模式现在要么是`loveMets`要么是`love Mets`——两者都不会出现在字符串中。我们需要管道来执行 OR 逻辑。

> 通过创建一个[中型合作伙伴计划账户](https://matt-croak.medium.com/membership)和[订阅我的电子邮件](https://matt-croak.medium.com/subscribe)，获取我所有的最新内容。:)

这个 OR 运算符可以用于许多不同的条件，而不仅仅是两个！看看这个。我们来找找`Matthew`、`love`和`Mets`。

```
const line = 'My name is Matthew Croak. I love the NY Mets.';
const regex = /love|Mets|Matthew/g;
const found = line.match(regex);

console.log(found)

> ['Matthew', 'love', 'Mets']
```

注意到我包含匹配模式的顺序并不重要吗？它仍然会找到匹配项，并按照它们在字符串中出现的顺序记录它们。

让我们尝试一些更复杂的东西。

## 多个条件(带特殊字符)

看看这条线。

```
const line = "My name is Matthew Croak :). I <3 the NY Mets (not so much the yankees but they're OK.)";
```

假设我们想找到所有的*表情符号*。在你的主机上试试下面的。

```
const line = "My name is Matthew Croak :). I <3 the NY Mets (not so much the yankees but they're OK).";
const regex = /<3|:)/g;
const found = line.match(regex);

console.log(found)
```

发生了什么事？你什么时候记录的？它看起来像这样吗…

`Uncaught SyntaxError: Invalid regular expression: /<3|:)/: Unmatched ‘)’`

为什么会这样？嗯，是因为`)`是 regex 中的特殊字符！用于[分组](https://www.regular-expressions.info/brackets.html#:~:text=Use%20Parentheses%20for%20Grouping%20and,to%20part%20of%20the%20regex.)。为了在我们的字符串中找到它，我们必须用一个*反斜杠*对它进行转义。

见下文。

```
const regex = /<3|:\)/g;
```

我们更新后的代码应该会给出下面的响应。

```
> (2) [':)', '<3']
```

你有它！如何使用逻辑 OR 运算符将多个正则表达式组合成一个表达式。您还学习了如何对特殊字符或保留字符进行转义，以便可以在字符串中找到它们！

*更多正则表达式相关资源，* [*查看我整理的正则表达式列表！*](https://medium.com/@matt-croak/list/d8eb18e7d959)

在 regex 中使用多个条件还有别的方法吗？请在评论中告诉我！

[***升级您的免费 Medium 会员资格***](https://matt-croak.medium.com/membership) *并接收各种出版物上数千名作家的无限量、无广告的故事。这是一个附属链接，你的会员资格的一部分帮助我为我创造的内容获得奖励。*

*你也可以通过电子邮件* [***订阅，当我发布新内容时，你会收到通知！***](https://matt-croak.medium.com/subscribe)

# 参考

![Matt Croak Code](img/dc724b5b04dff6854470b6cb3f1dd8f2.png)

[](https://matt-croak.medium.com/#:~:text=Use%20Parentheses%20for%20Grouping%20and,to%20part%20of%20the%20regex)

## [正则表达式教程-用于分组和捕获的圆括号](https://matt-croak.medium.com/#:~:text=Use%20Parentheses%20for%20Grouping%20and,to%20part%20of%20the%20regex)

### [通过将正则表达式的一部分放在圆括号或圆括号内，可以将正则表达式的这一部分分组…](https://matt-croak.medium.com/#:~:text=Use%20Parentheses%20for%20Grouping%20and,to%20part%20of%20the%20regex)

[www.regular-expressions.info](https://matt-croak.medium.com/#:~:text=Use%20Parentheses%20for%20Grouping%20and,to%20part%20of%20the%20regex)