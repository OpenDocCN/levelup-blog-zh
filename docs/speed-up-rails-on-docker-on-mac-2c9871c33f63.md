# 在 Mac 上加速 Docker 上的 Rails

> 原文：<https://levelup.gitconnected.com/speed-up-rails-on-docker-on-mac-2c9871c33f63>

## 一次换线速度提高 2.5 倍

![](img/982f5ee60bc5b286bddb6d4b501c474b.png)

托德·克雷文的照片

Mac 版 Docker 上的 Rails 速度很慢。它慢得令人难以忍受，我第一次启动它时，我只是从笔记本电脑上删除了 Docker，并继续原生运行一切:5.6 秒到 25.8 秒的启动速度太慢了。

但是有一个简单的方法可以显著缓解这个问题。通过这一行代码的更改，我将启动时间缩短到了 9.8 秒(速度提高了 2.5 倍):

# 为什么会这样？

默认情况下，Docker 在容器和主机的文件系统之间保持完美的一致性。当您在 Linux 主机上运行 Linux 容器时，没有开销:容器和主机最终使用相同的文件系统。

然而，OS X 的情况并非如此，因为它使用另一种文件系统类型，并且在主机和容器之间同步更改需要一些时间。它对 IO 密集型操作增加了相对较高的惩罚，例如读取应用程序的每个依赖项及其依赖项的源。因此，启动速度变慢了，但一旦应用程序进入内存，性能就会恢复正常。

为了避免这个问题，您可以将挂载卷的模式从`consistent`(默认模式)切换到`cached`或`delegated`。那是什么意思？这些配置允许在将更改从一个文件系统传播到另一个文件系统之间存在微延迟。以下是官方文件[对它们的描述](https://docs.docker.com/docker-for-mac/osxfs-caching/):

*   `cached`“通常会提高读取密集型工作负载的性能，但代价是主机和容器之间会出现暂时的不一致。”
*   使用`delegated`，容器执行的写入可能不会立即反映在主机文件系统上。

文档还说`delegates`提供了比其他配置明显更好的性能，尽管我没有注意到 Rails 启动时间的显著变化。我猜`delegated`在写负载的情况下表现出色，在读负载的情况下表现与`cached`相同。

# 那些微延迟有害吗？

如果我们谈论在开发环境中加载 Rails 不，这些延迟是无害的；将`cached`指令放在`docker-compose.yml`中是安全的。

我搜索了这些可能延迟的近似值，但不幸的是，没有找到任何值。好的一面是，在搜索过程中，我发现了 Docker 团队的一篇很好的[博文](https://www.docker.com/blog/user-guided-caching-in-docker-for-mac/)，里面有问题的细节和一些基准。

# 我如何测量 Rails 加载时间

我希望你会觉得这个建议有用。保持安全——快乐地编码。

如果你喜欢这篇文章，可以考虑看看我写的其他文章:

*   [如何提出完美的拉动式请求](https://medium.com/better-programming/how-to-make-a-perfect-pull-request-3578fb4c112)
*   [代码审查你的同事会感谢你的](https://medium.com/swlh/a-code-review-your-colleagues-would-thank-you-for-b569fea0e3e1)