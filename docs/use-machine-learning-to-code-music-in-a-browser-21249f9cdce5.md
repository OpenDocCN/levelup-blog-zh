# 使用机器学习在浏览器中编码音乐

> 原文：<https://levelup.gitconnected.com/use-machine-learning-to-code-music-in-a-browser-21249f9cdce5>

Google Project Magenta 是如何提升音乐创造力的？

![](img/662d4b48085884c93b9b74ac7011431a.png)

凯文·麦卡琴在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上的照片。

你知道我们可以用代码创作音乐吗？是的，使用相同的代码行，我们可以创建视觉和听觉效果。这不仅仅是播放音轨，我们还可以自己产生声音。在这种情况下，浏览器将充当合成器的角色！关于网络应用中音乐创作的更多细节，你可以阅读[我之前的文章](https://medium.com/better-programming/frontend-development-sounds-9fe7e5839ab9)。

> 我认为用代码创作音乐是一件疯狂的事情。我那时很年轻，很天真……(两个月前)。我不知道声音不仅可以由开发人员的代码生成，还可以通过使用机器学习来生成！

# 品红. js

[Magenta](https://magenta.tensorflow.org/) 是一个开源 [Google](https://about.google/) 项目，它在音乐和艺术创作过程中提供机器学习作为工具。 [NSynth Super](https://nsynthsuper.withgoogle.com/) 是如何使用洋红色来创造音乐创造力的惊人工具的一个例子。

Magenta 库使用案例— NSynth Super

[Magenta.js](https://magenta.tensorflow.org/js-announce) 是上述项目的一个子集，它提供 Javascript API 在浏览器中使用 Magenta 模型。在引擎盖下，它由另一个著名的面向 Javascript 用户的机器学习库提供支持— [Tensorflow.js](https://www.tensorflow.org/js/) ，它允许快速的 GPU 加速推理。

Magenta.js 被分发到几种类型的库:

*   [/音乐](https://github.com/magenta/magenta-js/tree/master/music) —基于音符的模型包括 [MusicVAE](https://magenta.github.io/magenta-js/music/classes/_music_vae_model_.musicvae.html) 、 [MelodyRNN](https://magenta.github.io/magenta-js/music/classes/_music_rnn_model_.musicrnn.html) 、 [DrumsRNN](https://github.com/magenta/magenta/tree/master/magenta/models/drums_rnn) 、 [PerformanceRNN](https://github.com/magenta/magenta/tree/master/magenta/models/performance_rnn) 和 [ImprovRNN](https://github.com/magenta/magenta/tree/master/magenta/models/improv_rnn)
*   [/草图](https://github.com/magenta/magenta-js/tree/master/sketch)—草图(绘图)模型，包括 [SketchRNN](https://goo.gl/magenta/sketchrnn)
*   [/图像](https://github.com/magenta/magenta-js/tree/master/image) —图像风格转换模型

> 如果这些缩写没有给你敲响警钟，不要担心，继续前进。

# 如何在 web 应用中使用 Magenta.js？

我将展示如何在简单的 web 应用程序中使用 Magenta.js 提供的机器学习功能。本文主要关注基于音符的模型。出于演示的目的，我使用的是 Angular 框架。然而，这种设置与任何其他前端框架甚至普通的 JavaScript 都没有什么不同。

## 初始设置

1.  使用 [Angular CLI](https://cli.angular.io/) 创建一个新的 Angular app:

```
npm install -g @angular/cli
ng new ai-music
```

2.安装 [@magenta/music](https://www.npmjs.com/package/@magenta/music) 库:

```
cd ai-music
npm install @magenta/music
```

3.使用以下工具构建和服务您的应用:

```
ng serve
```

## 播放指定的歌曲

在初始化`AppComponent`的过程中，我使用 Magenta 库创建了`Player`元素。用户的`click`事件将发起在浏览器中播放一首著名的代表作——[跳转歌曲](https://www.youtube.com/watch?v=sh9wsrL43RY&ab_channel=MusicGuide)。

结果:

音符以一种特定的格式描述，这种格式显示了应该演奏什么样的音高以及在什么特定的时间演奏。音符的音高在 MIDI 键号中指定。你可以在这里找到数字是如何映射到频率[的。顺便提一下，我建议您将注释移动到单独的常量文件中，以保持组件的整洁。](https://www.inspiredacoustics.com/en/MIDI_note_numbers_and_center_frequencies)

## 请艾继续一首歌

目前，我们不使用任何预先训练的模型——我们正在演奏指定的音符。让我们使用一个洋红色模型来继续之前使用的跳转歌曲。在接下来的例子中，我将从 **basic_rnn** 检查点创建一个模型。您可以在此找到所有托管的检查点[。](https://goo.gl/magenta/js-checkpoints)

首先，根据提供的检查点初始化递归神经网络模型`musicRNN`。请注意，有时这可能需要几秒钟的时间！🤷🏻‍♀️之后，我们可以要求继续跳这首歌。我们之前以未量化的格式(以秒为单位)描述了音符的特征，但是对于`musicRNN`模型，我们应该以量化的格式(以步为单位)构建它们。你可能会问艾怎么可能继续跳歌？检查几次尝试:

我们不能确定会返回什么确切的结果，一个旋律永远是唯一的。另一方面，有 3 个主要参数用于控制结果:

*   `steps` —持续时间测量，值越大，结果越长
*   `temperature` —随机化权重，其中较高的值结果导致与所提供的原始旋律的偏离程度较高(在音符音高、长度、力度、主题等方面)。)
*   `stepsPerQuarter` —速度估计，较大的值会增加结果的 BPM(每分钟节拍数)

为了比较，我将`steps`改为 30、`temperature`改为 10、`stepsPerQuarter`改为 6，结果是这样的:

最后，我想提一下 Typescript 用户的一个[问题](https://github.com/tensorflow/tfjs/issues/2007#issuecomment-529630217)，这个问题可以通过启用编译器选项“skipLibCheck”(快速而麻烦的解决方案)或者通过手动安装缺少的类型(更健壮且耗时的解决方案)来解决。

> 总的来说，这个库非常新，几年前才发布，所以在我写作的时候可能会引入一些修正和新特性。

## 释放人工智能创造自己的摇滚

另一个称为变分自动编码器`musicVAE`的 Magenta 模型不需要提供任何注释，但结果是，给出了重建输入数据的新序列。在下面的例子中，我使用 **mel_4bar_small_q2** 检查点初始化模型。之后，我可以通过指定序列的计数和控制温度来请求一个结果。

听起来像什么:

通过将检查点更新为 **groovae_4bar** ，结果将会发生巨大变化:

# 摘要

老实说，从不同的洋红色模型提供的结果可能有点远离你最喜欢的音乐…但它也可能产生一些令人惊讶的好样本！看看这件洋红色杰作:

音乐产业已经被科技塑造[。机器学习只是一种新的工具，可以提高音乐创造力，使音乐创作过程更快更容易。](https://www.music.org/index.php?option=com_content&view=article&id=2675:the-impact-of-technology-on-the-musical-experience&catid=220&Itemid=3665)

# 奖金

你有没有想过成为顶尖的钢琴演奏者是什么感觉？即使你没有，你也需要试试[钢琴精灵](http://piano-genie.glitch.me/)。机器学习会让你相信你是一个🎹名家！

如果钢琴乐器对你来说太无聊，你也应该试试[玩水果](https://www.youtube.com/watch?v=HGWkQP9lVPw&ab_channel=Google)！

*感谢您的阅读。你有过音乐编码或者洋红库的经验吗？我很想听听你的冒险经历！🤘*