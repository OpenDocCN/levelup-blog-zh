# 不要依赖默认的 ESlint 配置

> 原文：<https://levelup.gitconnected.com/dont-rely-on-the-default-eslint-configuration-9d33a29b7b7b>

[ESlint](https://eslint.org/) 太牛了。它允许我们编写格式优美的 Javascript 代码，并防止大量的语法和语义错误。不用说，您可以通过运行`eslint --init`来准备您的工作空间。库会生成适合广大开发者的默认配置，不是吗？嗯，我认为，如果你想让你的代码更干净、更易维护，至少应该配置一条规则。

![](img/e4ac25edd650ab86edfcb801a83f7d01.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1246611) 的[免费照片](https://pixabay.com/photos/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1246611)

代码就像一本书。我们按照声明的命令一步一步往下读。尽管如此，还是有一些不同之处。作者不需要在引言中声明小说中使用的每个单词。但编程就是这样。

[修正变量用法](https://gist.github.com/SimonHarmonicMinor/a95b63452824cded08ad9bb9ba0bae2b)

我们不能在赋值前引用`song`，那会导致`ReferenceError`。但是如果我们有，我们也不会知道，直到有人试图播放它。Javascript 不是一种编译语言，这就是为什么大多数错误直到执行到麻烦点才被发现。而这就是 ESlint 来的时刻！有一个叫做`no-use-before-define`的有用规则，默认情况下是激活的。它检查您引用的变量是否已经定义。

[引用未声明的变量](https://gist.github.com/SimonHarmonicMinor/9ff115ee32eff17ded539755d1cb7789)

如您所见，ESlint 不允许 Javascript 对我们产生反作用。但是也有黑暗的一面。我什么意思？让我们看看另一个例子。

[音乐播放器](https://gist.github.com/SimonHarmonicMinor/b0b55e2ef44b3f92f7e8f8f54da8ee3d)

这段代码是正确的，因为 Javascript 中的函数与带有`var`关键字的声明具有相同的作用域。即使它是在调用代码下面定义的，你也可以引用它，这就是它应该有的方式。当你按照函数的用法从上到下声明函数时，很容易阅读代码并理解流程。不止如此，这种方法是马丁的“干净代码”所推荐的。

但是这里有一个惊喜。ESlint 规则`no-use-before-define`不允许我们调用使用后声明的函数。我们会得到与提及未申报的`song`同样的错误。如果我们想修复这个错误，我们可以重构代码并颠倒函数的顺序。

[“重构”的音乐播放器](https://gist.github.com/SimonHarmonicMinor/9e5997b9f6ed4ce836a0a07cc39484c7)

这就是埃斯林想要我们做的。这段代码片段比上一段更清晰了吗？我表示怀疑。我想你也一样。假设有人正在审查我们的拉取请求。如果有人想了解发生了什么，首先需要向下滚动页面查看入口点。之后，他们可能会自下而上地检查彼此的功能。显然不太方便。但遗憾的是，这是默认 ESlint 配置的策略。

不管怎样，有什么解决办法呢？嗯，我们可以取消`no-use-before-define`规则。但是这也可能导致变量的错误。希望有更好的方法。

[更好的“定义前不使用”](https://gist.github.com/SimonHarmonicMinor/5bc12e7f59cff51a73fca13c1dd85141)

就是这样！现在，ESlint 允许我们按照自己喜欢的方式对函数进行排序。该规则也可以对类关闭(是的，它也检查类)。你可以通过[这个链接](https://eslint.org/docs/rules/no-use-before-define)找到更多关于它的信息。

# 结论

我希望你对 ESlint 有了新的了解。我的观点是，首选配置在现实世界中可能并不那么“首选”。所以，不要太依赖一个人对什么是对的看法。甚至是我的。也许你认为我错了。如果是这样，我想在评论区讨论一下这篇文章。感谢您的阅读！