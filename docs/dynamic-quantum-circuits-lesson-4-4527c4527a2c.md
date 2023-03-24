# 动态量子电路，第 4 课

> 原文：<https://levelup.gitconnected.com/dynamic-quantum-circuits-lesson-4-4527c4527a2c>

![](img/ab455b8e1dacce6f21e964f87350ca64.png)

[https://en . Wikipedia . org/wiki/File:ST _ troubleswithtibles . jpg](https://en.wikipedia.org/wiki/File:ST_TroubleWithTribbles.jpg)

# 元组的麻烦在于

动态量子电路在 [IBM 量子峰会 2022](https://medium.com/@bsiegelwax/ibm-quantum-summit-2022-d1c646169189) 上宣布，并在第二天的 [IBM 量子从业者论坛](https://bsiegelwax.medium.com/ibm-quantum-practitioners-forum-2022-67a31a23407f)上展示了一些细节。简而言之，你在将静态量子电路排队运行之前，先设计它们的整体，而在它们排队运行之后和实际运行期间，你使用经典逻辑来生成动态电路。

这一课将是短暂而愚蠢的，但它给我带来了比我愿意承认的更多的不必要的痛苦，也许我可以让你免受这种痛苦。

```
with quantum_circuit.if_test((classical_register, some_integer)):
```

我不知道你怎么想，但当我看到嵌套括号时，我认为这是一个错误。我一定在里面放了一些东西，去掉了一部分，留下了括号，对吗？

但是，这里的情况不是这样。你不传递两个参数给 *if_test()* ，你传递一个元组。因此，我删除了“额外”的括号，我认为这没什么大不了的，然后编辑了代码的其他部分。我花了相当多的时间来打破完整的代码，试图排除错误，并最终在逐行浏览教程代码时发现了双括号。

无论你如何记忆东西——写下它 25 次，大声说出来 25 次，或者其他——试着记住保持元组完整。

## 动态量子电路系列

*   [动态量子电路，第三课](https://bsiegelwax.medium.com/dynamic-quantum-circuits-lesson-3-7b9e29e85daa)
*   [动态量子电路，第二课](https://medium.com/gitconnected/dynamic-quantum-circuits-lesson-2-d31d7a287c62)
*   [动态量子电路，第一课](https://medium.com/@bsiegelwax/dynamic-quantum-circuits-lesson-1-f712bc6a9e1d)