# 将 Nix 薄片与 Bazel 一起使用

> 原文：<https://levelup.gitconnected.com/using-nix-flakes-with-bazel-8189719ea799>

![](img/072d0439e0f8c61a97173b65c8847503.png)

Christine Makhlouf 在 [Unsplash](https://unsplash.com/s/photos/flake?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

[Nix Flakes](https://nixos.wiki/wiki/Flakes) 是 Nix 2.4 的一个新特性(在撰写本文时仍是预发布版本)，它使 Nix 衍生产品可组合，并可用于将衍生产品分发给其他人。

这不是一个关于 Nix Flakes 是什么的帖子，如果你还没有阅读过，我推荐你阅读 Tweag 的系列文章(这里先看)。

对薄片的支持当然不是很广泛。这是一个非常简短的解释，我是如何在我的一个项目中弥合 Nix Flakes 和 Bazel 之间的差距的。乍一看，这并不复杂，但也不十分明显。

# 简单的开发环境

首先，我们在项目的根目录下创建一个简单的`flake.nix`。它使用`flake-utils`为各种系统轻松创建环境，例如`x86_64-linux`。我们从 Nixpkgs 的不同版本中获取 Bazel，因为上次我尝试时它在`nixpkgs-unstable`被破坏了。

“旧的”nixpkgs(如在`import <nixpkgs> {}`中)可以在`nixpkgs.legacyPackages.x86_64-linux`等属性中获得，这里的`nixpkgs`是一个薄片，作为我们薄片的输入。

为了便于演示，我们定义了一个简单的 Nix 覆盖图，它覆盖了来自`nixpkgs`的`ghc`，在`nix/haskell/overlay.nix`有一个不同的派生。

nix/haskell/overlay.nix

为了让 Bazel 知道 Nix 环境依赖于这个文件，我们可以用下面的规则在`nix/haskell/`中创建`BUILD.bazel`文件。这将在稍后的`WORKSPACE`中使用。

nix/haskell/BUILD.bazel

接下来，我们可以像以前一样定义我们的开发环境。我喜欢把它放在`nix/shell.nix`中，所以(几乎)所有的 Nix 代码都在`nix/`中结束。

nix/shell.nix

运行`nix develop`应该会让您进入一个带有 Bazel 和 Python 的 shell。因为这是它第一次运行，所以应该用输入的固定版本生成另一个文件`flake.lock`。

# 这座桥

[rules_nixpkgs](https://github.com/tweag/rules_nixpkgs) 没有对 Nix Flakes 的本地支持，所以我们需要用一个简单的文件来弥补这个缺口，这个文件公开了与 flake 中相同的`nixpkgs`。我们还希望应用任何覆盖，以便 Bazel 使用正确版本的依赖项。

# Bazel 工作空间

最后，我们可以在桥上使用`rules_nixpkgs`，就好像我们根本没有使用雪花一样。

正如你所看到的，关键就是那个文件`bazel-nixpkgs.nix`，它非常简单。拥有这个文件的一个好的副作用是，只需输入`:l ./bazel-nixpkgs.nix`就可以在`nix repl`中加载 Nixpkgs。这个技巧对于调试非常有用。

如果你喜欢这样的故事，可以考虑成为媒体的会员，或者订阅。