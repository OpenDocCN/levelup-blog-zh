# ESLint 警告是一种反模式

> 原文：<https://levelup.gitconnected.com/eslint-warnings-are-an-anti-pattern-78eb93aa9928>

![](img/9bbf01477e9a69b67f3c0a5706fb0c9a.png)

斯科特·罗杰斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

ESLint 为任何给定的规则提供了三种设置:`error`、`warn`和`off`。`error`设置将使 ESLint 在遇到任何违反规则的情况下失败，`warn`设置将使 ESLint 报告发现的问题，但在遇到任何违反规则的情况下不会失败，`off`设置禁用规则。

我想说使用`warn`是一种反模式。为什么？因为要么你在乎什么，要么你不在乎。规则要么很重要，应该遵守，违反规则的地方应该修复，要么规则不重要，开发人员不应该担心。因此，使用`error`或`off`。

## 警告的问题是

现在，使用`warn`设置没有真正的问题。但问题是，当违反 ESLint 规则的行为没有得到执行时，开发人员往往会忽略它们。这意味着警告将堆积在 ESLint 输出中，产生大量噪声和混乱。

所以，你在乎那些被违反的规则吗？如果没有，为什么要启用该规则？如果一个规则没有任何用处，开发人员没有处理警告，那么就放弃这个规则。如果规则很重要，就把它设置为`error`，这样开发者就不会忽略它。

## 一个警告

我认为`warn`设置有一个用例是有效的，所以让我们来解决它。记住，只有西斯才处理绝对的事情。

当向您的代码库引入一个新的 ESLint 规则时，您可能会发现有太多的违规情况需要一次解决。在这种情况下，你该怎么办？您希望让您的 ESLint 脚本通过，特别是如果您在 CI 管道中强制执行它(您应该这样做！).为每一个违反该规则的行为添加一个`eslint-disable-next-line`注释和一个`TODO`注释可能会适得其反。

因此，如果您正在添加一个新的 ESLint 规则，并且发现由于某种原因您不能一次清除所有的违规，那么将新规则设置为`warn`，至少现在是这样。请理解，这是一个临时设置，您的目标应该是尽快清除警告。然后，一旦处理了违反规则的情况，您就可以将规则设置更改为`error`。

# 结论

ESLint 警告是一种反模式。仅使用`error`或`off`设置，保留使用`warn`设置仅作为临时权宜之计。

# 分级编码

```
Thanks for being a part of our community! More content in the [Level Up Coding publication](https://levelup.gitconnected.com/).Follow: [Twitter](https://twitter.com/gitconnected), [LinkedIn](https://www.linkedin.com/company/gitconnected), [Newsletter](https://newsletter.levelup.dev/)Level Up is transforming tech recruiting 👉 [**Join our talent collective**](https://jobs.levelup.dev/talent/welcome?referral=true)
```