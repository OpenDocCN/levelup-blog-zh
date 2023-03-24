# 使用 Firebase 的脸书即时游戏 1v1 多人游戏(第 2 部分)

> 原文：<https://levelup.gitconnected.com/facebook-instant-games-1v1-multiplayer-using-firebase-part-2-baeea1145b79>

> 请看这里的第一部分:[脸书即时游戏 1v1 多人使用 Firebase(第一部分)](https://medium.com/@mixalisdobs/facebook-instant-games-1v1-multiplayer-using-firebase-part-1-30c1f6e32b92)

![](img/fdd9f580b0e979db1555602aef554be3.png)

丽贝卡·奥利弗在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在第一部分中，我们解释了脸书即时游戏的多人游戏特性的前端，现在让我们看看使用 Firebase Firestore-DB 和函数的后端逻辑(一个沙盒 NodeJS 环境，无需拥有服务器即可编写后端逻辑)

你可以这样开始:

> [https://firebase.google.com/docs/functions/get-started?authuser=1](https://firebase.google.com/docs/functions/get-started?authuser=1)

现在假设我们的环境已经设置好了，让我们编写一些逻辑来处理前端请求

![](img/6033005c60c1adfd4db40f74d341993a.png)

照片由[艾丹·格兰贝里](https://unsplash.com/@atgranberry?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

首先，这里是我在我的项目中使用的导入(你可能需要也可能不需要)

然后这里有一个小片段做用户验证(以确保您的用户确实来自脸书即时游戏)

你可以在脸书应用设置页面找到你的`APP_SECRET`

现在让我们为`startMatchmaking`设定终点

上面我们正在接受一个 http 请求，我们接收用户 FB-Id 和标签(自定义标签)以及他们的签名，以便验证他们的身份。

函数的其余部分只是将这些数据添加到`bountyQueueRef`集合中，并以用户 FB-Id 作为文档名(因此是惟一的)创建一个文档。完成后，该函数向前端发送一个响应，通知一切正常，用户现在在队列中。

接下来我们创建`findMatch`函数，它在排队的玩家之间进行实际的匹配。

我们再次收到一个 http 请求，并验证发送方。之后，我们尝试将发件人与队列中的其他人进行匹配，如果该算法发现一个非发件人的未标记(已与某人匹配)用户，则它会立即标记该用户，使他们对其他可能的并发搜索不可见，同时它还会标记发件人，以便他们也不会被其他搜索选中。

> 在这里阅读，为什么使用事务在数据库上读/写是好的。[https://firebase . Google . com/docs/firestore/manage-data/transactions？authuser=1](https://firebase.google.com/docs/firestore/manage-data/transactions?authuser=1)

该函数将带有匹配玩家数据的响应返回给发送方，这基本上是不相关的，因为实际的功能是在这两行代码完成执行时触发的:

```
t.set(bountyQueueRef.doc(queueEntry.userFBId), queueEntry, { merge: true }); // store their data again but flaggedt.set(bountyQueueRef.doc(userFBId), data, { merge: true }); // store our data again but flagged
```

因为我们已经在两个播放器的前端`addBountyQueueListener`设置了事件监听器。当`set`操作完成对数据库的写操作时，事件监听器接收到一个事件。

然后前端通过使用

```
FBInstant.context.createAsync(userFBId)
```

如本教程第 1 部分所述:[https://medium . com/@ mixalisdobs/Facebook-instant-games-1 v1-multiplayer-using-firebase-Part-1-30 C1 f 6 e 32 b 92](https://medium.com/@mixalisdobs/facebook-instant-games-1v1-multiplayer-using-firebase-part-1-30c1f6e32b92)

我希望你能发现上面的信息是有用的，如果你想让我解释得更好，或者如果你确实通过使用所描述的方法做出了一些东西，请给我留言！

尽情享受吧！

![](img/d4d9385c6f7da68d066378271a41d499.png)

照片由 [Al x](https://unsplash.com/@alx_andru?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄