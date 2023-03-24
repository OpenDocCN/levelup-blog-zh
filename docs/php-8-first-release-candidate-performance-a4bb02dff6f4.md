# PHP 8 首次发布候选性能

> 原文：<https://levelup.gitconnected.com/php-8-first-release-candidate-performance-a4bb02dff6f4>

*让我们跟进 PHP 8 第一个发布候选版本的速度*

![](img/a56c54b979bcdca39e47c9ed21129179.png)

[本](https://unsplash.com/@benofthenorth?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

从几个月前开始，我就一直在关注新 PHP 8 的进展。随着第一个候选版本的发布，这就变成了检查性能进度的时刻。除非发现一个巨大的 bug，否则我假设新 PHP 版本的核心不会有相关的变化。

> 请注意，这仍然不是 PHP 的最终版本。不要在任何生产部署中使用它。

为了与我之前的测试保持一致，我将重用我之前使用的相同代码(嗯，也因为这是使用冒泡排序的有趣借口)。你可以在这里找到源代码。

首先，让我们不使用 JIT 来运行它:

```
docker container run --rm -v $(pwd):/script/ php:8.0.0rc1 php /script/bubble.php
```

跑 100 次后的平均时间是 0.058 秒，还不错。

现在，我们将在 JIT 激活的情况下运行它。因为我找不到一个已经用 PHP 8 候选版本和 JIT 激活的 docker 映像，所以我创建了自己的映像。

```
docker container run --rm -v $(pwd):/script/ pedroescudero/php8jit php /script/bubble.php
```

100 次跑步后，平均时间为 0.029 秒。不到一半！这真是令人印象深刻。当然，为了更深入地理解激活 JIT 的候选版本的性能，我们需要将它应用到更复杂的算法中，并在更真实的/类似生产的环境中进行测试。尽管如此，乍一看，我认为这个测试给出了非常好的数字。

让我们来看一看 2009 年的旧版本:

```
docker container run --rm -v $(pwd):/script/ php:5.3 php /script/bubble.php
```

平均耗时 0.63 秒。永恒。

## 附加参考

如果你对创造自己的形象感兴趣，建议访问 Arkadius Kondas 的博客和 Keinos 的 Docker readme。我的映像基于他们以前的工作，但是尽管他们的版本没有第一个发布候选，我还是被迫动手构建了我自己的 PHP 8 docker 映像，并激活了 JIT。

[](/how-fast-is-php-8-going-to-be-f7fdc111cd6) [## PHP-8 会有多快？

### PHP-8 将于今年年底发布。让我们看看它如何提高一个脚本的速度。

levelup.gitconnected.com](/how-fast-is-php-8-going-to-be-f7fdc111cd6)