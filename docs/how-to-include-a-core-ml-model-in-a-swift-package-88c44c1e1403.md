# 如何在 Swift 包中包含核心 ML 模型

> 原文：<https://levelup.gitconnected.com/how-to-include-a-core-ml-model-in-a-swift-package-88c44c1e1403>

![](img/e363b00a445003b7cd81bd566d35d8b8.png)

Lukasz Szmigiel 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

当我开始创建我的第一个机器学习应用程序时，我决定使用 CoreML 而不是 Tensorflow 或 PyTorch，只是因为方便。我不仅能更好地理解培训，还能更好地理解部署:只需将它放入 Xcode 并使用为模型创建的类。就是这样。

然而，将您的代码和模型分解到一个库(一个 Swift 包)中，会消除将模型拖放到 Xcode 中的便利。Xcode 由于某种原因不能处理这个…🤷那么我们需要做什么呢？

## 1.)自己编译模型

当你加上*之后。mlmodel* 文件到您的 Swift 包中，不会发生任何事情。您需要自己编译这个模型。这可以通过 Xcode 捆绑的命令行工具 *coremlcompiler* 来完成。这将创建一个 *my-model.mlmodelc* 目录。

**重要提示:**创建的工件必须位于您的 *Sources/MyPackageName* 文件夹中，否则您将无法将它包含到您的包中。

## 2.)将其作为资源添加到您的库中

不幸的是，编译器不够聪明，不能马上包含您的模型。您还需要将资源添加到您的*包中。这应该在您的*目标> your-target >资源*中，如下所示:*

**重要提示:**首先，您需要将资源分别添加到每个目标。其次， *copy* 中的路径是相对于你的*Sources/my package name*source 根文件夹的。

仅此而已。👍

祝你的项目好运。🚀