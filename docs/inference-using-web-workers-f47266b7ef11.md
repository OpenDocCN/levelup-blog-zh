# 使用 Web Workers 进行推理

> 原文：<https://levelup.gitconnected.com/inference-using-web-workers-f47266b7ef11>

## 通过机器学习进行打字练习

> 本系列文章:
> 1。[简介](https://medium.com/@bayan.bennett/typing-practice-with-machine-learning-introduction-aa3bb5d24134)
> 2。[伪英语](/pseudo-english-typing-practice-with-machine-learning-5700eb4dc54)3。[键盘输入](/keyboard-input-typing-practice-w-machine-learning-b5c5a9a362a7)
> 4。使用 Web Workers 进行推理(您在这里)
> 
> 完工项目位于:[https://www.bayanbennett.com/projects/rnn-typing-practice](https://www.bayanbennett.com/projects/rnn-typing-practice)

最后一篇文章，[键盘输入](/keyboard-input-typing-practice-w-machine-learning-b5c5a9a362a7)，是关于准备用于生成后续字符行的数据。本文研究如何使用该输入，以及如何使用 TensorFlow 模型生成一行文本。此外，该模型将完全在 WebWorker 上运行。

为了更多地了解 web workers，我在这里创建了一个软介绍:[准系统 Web Workers](https://medium.com/weekly-webtips/barebones-web-workers-bb0436cc440d) 。

![](img/3b887911f84ed8729875b03acdbd643c.png)

迈克尔·泽兹奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 设置员工

下面的代码都在`mlKeyboard.worker.js`文件内。首先，导入 TensorflowJS 文件:

[启用生产模式](https://js.tensorflow.org/api/2.6.0/#enableProdMode)

将后端设置为`webgl`，这样我们就可以调用 GPU 了。如果这个不可用，TensorFlowJS 后端将默认为`cpu`。

# 获取模型

这必须从服务器上的端点加载。一旦它被加载，将它保存到我们的 worker 的状态对象中，并向主窗口发布一条消息，表明 worker 已经准备好了。

在初始化期间以及当用户完成输入一行时，主窗口将发送一条类型为`actionTypes.worker.getNextLine`的消息。运行了一些其他函数，但最终调用了下面的函数`generateLine`。这个函数初始化一个 34 个字符的数组，并依次调用一个异步函数来生成每个密钥。之所以选择 34，是因为它非常适合桌面和移动屏幕。

# 规范化二元模型

在上一篇文章[键盘输入](/keyboard-input-typing-practice-w-machine-learning-b5c5a9a362a7)中，生成了一个二元模型，记录了任意一对击键之间的平均延迟。

![](img/01b7585cb1df9f195f2c5750620c2e17.png)

为了创建上面的图像以及在模型中使用它，需要对它进行规范化。

在这种情况下，我选择简单地将每个值转换成平均值的标准偏差数，然后使它成为 e 的幂。

# 生成每个字符

现在事情变得有趣了，运行 ML 模型！

# 生成第一个字符

模型需要最后一个字符的输入，那么如果没有呢？一种选择是用所有以空格开头的单词来训练模型。在这种情况下，第一个字符将始终是一个空格。

然而，在我的例子中，我想对第一个角色有更多的控制，所以我选择生成第一个角色。

# 获取行中的最后一个字符

这里没什么。直白。从该行中获取最后一个字符，如果该行由于某种原因为空，则返回一个`space`。

# 使用推理获得下一个字符

这就是奇迹发生的地方。它从`[tf.Sequential.predict](https://js.tensorflow.org/api/latest/#tf.Sequential.predict)`为每个角色生成概率开始，然后应用角色调整，`[tf.multinomial](https://js.tensorflow.org/api/latest/#multinomial)`返回一个角色。

# 计算空间的概率

你可能注意到了上面的`spaceAdjustment`变量。数据集由不同长度的单词组成，这使得模型很难准确预测单词何时结束。知道了字长的分布，预测就可以朝着正确的方向推进。这是空间累积分布函数:

其中 *k = 0.64766* 和 *x_0 = 6.18632* 。这些值是通过拟合字长分布得到的。例如，一个 5 个字母的单词会使出现空格的概率增加 46%。

# 把所有的放在一起

大部分重要的逻辑已经在上面描述过了，下面的代码将它们放在一起。

获取最后一个字符，如果是空格或空字符，生成单词的第一个字符。否则，通过在模型中运行最后一个字符并输出预测来生成下一个字符。如果下一个字符是`space`或`null`，将间距设置为`0`。然后对该行中的每个字母重复该功能。

# 总结想法

这篇文章演示了 ML 模型推理如何在 WebWorker 上运行。我惊喜地发现只需要这么少的样板代码。所有的代码只是 TensorFlowJS 库和一些辅助函数。

最终，这些方法将有助于在不影响可用性的情况下创建具有高级 ML 功能的 web 应用程序。

最初来自:

[](https://www.bayanbennett.com/posts/inference-using-web-workers-typing-practice-w-machine-learning) [## 使用 Web Workers 进行推理-使用机器学习进行打字练习

### 完成的项目位于这里:https://www.bayanbennett.com/projects/rnn-typing-practice 最后一个职位，键盘…

www.bayanbennett.com](https://www.bayanbennett.com/posts/inference-using-web-workers-typing-practice-w-machine-learning)