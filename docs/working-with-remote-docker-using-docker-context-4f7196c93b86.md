# 使用 Docker 上下文使用远程 Docker

> 原文：<https://levelup.gitconnected.com/working-with-remote-docker-using-docker-context-4f7196c93b86>

![](img/4f19ecd176f5ded28ea98514e19de331.png)

Unsplash 上 [@carrier_lost](https://unsplash.com/@carrier_lost) 的照片

# 介绍

这是一个使用 docker 上下文本地连接远程 docker 的备忘单。可能有助于您使用远程 docker，而无需手动 SSH 到远程服务器。

# 添加上下文

```
$ docker context create my-remote-docker-machine --docker "host=ssh://username@host"my-remote-docker-machineSuccessfully created context "my-remote-docker-machine"
```

您也可以利用`SSH Config`文件连接到远程 docker。尤其是当你需要定义你的`private key`或者`password`的时候。

```
$ cat ~/.ssh/configHost my-remote-docker-machineHostname hostUser username$ docker context create my-remote-docker-machine --docker "host=ssh://my-remote-docker-machine"
```

除了`ssh`，如果您启用了`Docker API`，您还可以使用`tcp`协议添加您的上下文。

# 列出所有上下文

```
$ docker context lsNAME                       DESCRIPTION                               DOCKER ENDPOINT               KUBERNETES ENDPOINT   ORCHESTRATORdefault *                  Current DOCKER_HOST based configuration   unix:///var/run/docker.sock                         swarmmy-remote-docker-machine                                             ssh://username@host
```

# 使用上下文

```
$ docker --context my-remote-docker-machine images -q65dadc9c7fe7f814fce551337a9b6da4328e33655f17f093d120da10b0406d6859d1a42ac19ae228f069
```

# 将新上下文设置为默认值

```
$ docker context use my-remote-docker-machinemy-remote-docker-machineCurrent context is now "my-remote-docker-machine"$ docker context lsNAME                         DESCRIPTION                               DOCKER ENDPOINT               KUBERNETES ENDPOINT   ORCHESTRATORdefault                      Current DOCKER_HOST based configuration   unix:///var/run/docker.sock                         swarmmy-remote-docker-machine *                                             ssh://username@host
```

见`*`从`default`移动到`my-remote-docker-machine`。现在，您可以在没有`--context`标志的情况下使用 docker 命令。

# 删除上下文

```
$ docker context use default # back to default$ docker context rm my-remote-docker-machinemy-remote-docker-machine
```

# 结论

使用 docker 上下文可能有助于避免手动 SSH 到远程服务器。但是，当使用远程 docker 在本地构建映像时，您需要考虑将上传/下载多少 docker 上下文。

感谢您的阅读！

*最初发布于 2021 年 12 月 2 日*[*https://clavinjune . dev*](https://clavinjune.dev/en/blogs/working-with-remote-docker-using-docker-context/)*。*