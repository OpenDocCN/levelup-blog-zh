# 如何配置 Node.js 使用端口 443

> 原文：<https://levelup.gitconnected.com/tws-004-how-to-configure-nodejs-to-use-port-443-86f1ca801c5f>

![](img/dbda0f9d21bf6830f150589c3b9e2c5f.png)

## 授予服务器对众所周知的特权 Linux 系统端口的访问权限

在这一集里，Ivana 学习如何授予 Node.js 网络能力，这样她就可以为她的 HTTP/2 服务器使用端口 443。

Ivana 刚刚对她为艺术用品商店石头剪刀布开发的定制网站进行了最后的润色。她准备将它投入生产。

在过去的几个月里，所有的东西都是在她的临时服务器上开发和测试的，该服务器被配置为监听端口 8443。浏览器通过 URL 访问临时区域:

```
https://rock-paper-scissor.com:8443
```

Ivana 需要在端口 443 而不是端口 8443 上向公众提供服务器。但是她知道端口 0 到 1023 是众所周知的系统端口，非特权软件禁止访问它们。

她部署的 [HTTP/2 服务器](https://rwserve.readwritetools.com/?utm_term=AccessingPort443)是 Node.js 服务器。任何 Node.js 服务器作为*非根*用户启动，作为用户域进程运行，不允许访问系统端口。无论是从前台的终端窗口执行，还是通过使用`PM2`的后台守护进程执行，或者直接在`systemd`的控制下执行，都是如此。

她不可能以`root`的身份启动服务器。有太多的内在风险。

Ivana 研究了允许 Node.js 作为用户 *rwserve* 直接监听端口 443 需要做些什么。她了解到覆盖限制的 Linux 命令是 *set capabilities* 命令`setcap`。赋予 Node.js 网络权限的咒语是:

```
setcap 'cap_net_bind_service=+ep' /usr/bin/node
```

其中`cap_net_bind_service`是将套接字绑定到特权端口的能力；值`+ep`表示添加能力“有效”和“允许”；而目标是 Node.js 可执行文件，位于`/usr/bin/node`。

这达到了目的。现在她可以使用著名的 443 端口在`https://rock-paper-scissors.com`访问石头剪刀布的网站。

![](img/63bc6c9688ccc7ad0abb46241e2e11bd.png)

*没有* [*minifig 人物*](https://readwritetools.com/meet-the-team.blue) *在制作这一段纠结的 Web Services 插曲中受到伤害。*