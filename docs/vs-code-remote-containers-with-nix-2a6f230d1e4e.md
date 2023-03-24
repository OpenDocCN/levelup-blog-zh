# VS 用 Nix 编码远程容器

> 原文：<https://levelup.gitconnected.com/vs-code-remote-containers-with-nix-2a6f230d1e4e>

![](img/b0c1469771732caf52e137fdf522e4a4.png)

照片由 [ammiel jr](https://unsplash.com/@helloitsammiel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/container?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

[Nix](https://nixos.org/learn.html) 已经提供了一种简洁的方式来创建一个在可再现性方面有强大保证的开发环境。然而，它仍然需要开发者在他们的电脑上安装它，即使 Nix 没有在诸如`/usr`这样的目录中安装任何东西，也会遇到一些阻力。

另一方面，VS Code 的 [devcontainers](https://code.visualstudio.com/docs/remote/devcontainerjson-reference) 允许开发者在没有太多麻烦的情况下开始一个项目，只要他们使用 VS Code 并运行 Docker。他们也可能对在容器中运行的东西感觉更好，不会污染他们的主机系统。

在这里，我将描述如何将这两种解决方案结合在一起，并获得两个世界的最佳效果。人们可以在他们的主机系统上直接使用 Nix，或者使用 VS 代码，在 Nix 环境的容器中进行开发。

这是我在一些项目中已经使用了几个月的设置。最初，我担心集装箱的开销可能会成为一个问题，但事实证明这种担心是没有根据的。

# Nix 环境

这个设置使用 Nix Flakes 创建一个开发环境。入口点在项目根的`flake.nix`中。

薄片输出一个`devShell`，其在`nix/shell.nix`中定义如下。为了便于演示，这只是一个带有 Haskell 编译器的虚拟开发环境。

nix/shell.nix

第一次运行`nix flake upgrade`会创建一个新文件`flake.lock`，包含 Nixpkgs 和`flake-utils`的固定版本。这个文件应该与项目的其余部分一起进行版本控制。

这些基本文件对于那些愿意在主机上而不是在容器中运行 Nix 的人来说已经很有用了。命令`nix develop`让您进入一个具有 Nix 开发环境的 shell。

# Docker 图像

我们现在需要一个带有 Nix 的 Docker 映像和一些其他配置，以便在启动时自动加载开发环境。

由于这依赖于 Nix Flakes，我们需要 Nix 2.4(在撰写本文时是预发行版)而不是最新的“官方”版本。

Dockerfile 与远程容器的配置一起放在`.devcontainer/`中，因为它特定于这种用法。

。开发容器/docker 文件

这个 Dockerfile 在容器内部复制`.devcontainer/nix.conf`。它用于设置一些使 Nix 2.4 工作所需的选项，但它也是一种在开发人员之间共享配置的便捷方式。例如，您可以设置`substituters`和`trusted-public-keys`来添加一些 Nix 缓存。

。devcontainer/nix.conf

最后，`.devcontainer/profile.sh`如下加载 Nix 开发环境。

。devcontainer/profile.sh

不要忘记通过运行以下命令使该文件可执行。

```
chmod +x .devcontainer/profile.sh
```

文件`.devcontainer/.profile`可以添加到`.gitignore`或等效文件中。它只是为了确保在运行`nix store gc`时开发环境不会被垃圾收集。

# 容器的配置

最后一步是告诉 VS 代码如何加载 devcontainer。这是通过在`.devcontainer/devcontainer.json`添加一个配置来实现的。

。devcontainer/devcontainer.json

名为`yourproject_nix`的卷将被创建并挂载到`/nix`。这意味着在重新构建容器后，您不需要重新编译所有的 Nix 派生。

在这个例子中，VS 代码的扩展`bbenoist.nix`被自动安装在容器中。您可能想要添加特定于项目技术堆栈的其他扩展。

如果你喜欢这样的故事，可以考虑成为媒体会员，或者订阅。