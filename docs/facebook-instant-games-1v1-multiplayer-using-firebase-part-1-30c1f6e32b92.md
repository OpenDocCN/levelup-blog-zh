# 使用 Firebase 的脸书即时游戏 1v1 多人游戏(第 1 部分)

> 原文：<https://levelup.gitconnected.com/facebook-instant-games-1v1-multiplayer-using-firebase-part-1-30c1f6e32b92>

![](img/910e541a21e533641102e5dc423a06a3.png)

凯莉·西克玛在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

由于脸书即时游戏是一个相对较新的平台，有许多部分已经丢失或者根本没有按照人们期望的方式工作。

因此，为了推出支持多人(1v1)的游戏，我们必须实现自己的解决方案，我们选择了 Firebase，因为他们的新技术`Functions`允许后端逻辑上传并在他们的服务器上运行，还因为他们的平台和解决方案能够根据我们的需要进行扩展(相对较低的价格)。

请注意，下面的后端代码是由前端开发人员编写的，事情可能不是超级优化，但嘿，它的工作原理是如此…

本文还假设您知道如何设置一个 Firebase 项目和一个简单的游戏，并主要关注多人游戏部分。

所以让我们开始初始化我们的脸书即时游戏 SDK

```
var facebookStuff = {
  name: "",
  picture: "",
  contextId: "",
  userId: ""
};FBInstant.initializeAsync().then(function() {
  FBInstant.setLoadingProgress(0);
  FBInstant.startGameAsync().then(function() {

  facebookStuff.name = FBInstant.player.getName();
  facebookStuff.picture = FBInstant.player.getPhoto();
  facebookStuff.userId = FBInstant.player.getID();
  facebookStuff.contextId = FBInstant.context.getID();
}
```

现在我们可以开始了，我们已经收到了来自脸书的必要的`userId`来识别我们的用户。

因此，让我们创建对服务器进行 API 调用的函数。

通过使用`getSignedPlayerInfoAsync`,我们请求一个安全令牌，该令牌将被发送到我们的服务器，并使用即时游戏平台将用户识别为合法的脸书用户。

> 在这里阅读更多:[https://developers . Facebook . com/docs/games/instant-games/SDK/FB instant 6.2](https://developers.facebook.com/docs/games/instant-games/sdk/fbinstant6.2)

API 调用然后将所需的数据发送到服务器，以便它将我们特定的`userId`存储在专用的 DB 文档中(使用 Firebase Firestore DB ),这实际上是我们的多人队列。

玩家注册成功后，`findMatch`函数将被作为回调函数调用，该函数的作用如下:

我们再次向服务器发送签名以验证我们，我们进行另一个 API 调用以实际启动对对手的搜索，我们将这两个操作分开，以便在负载平衡、分析(加入队列的玩家与实际参与游戏的玩家)方面为后端提供更多选项。以及给用户更多的时间来取消搜索。将来，这两项职能可以合并。

因此，一旦`findMatch`回调成功解析，服务器就会主动为我们搜索匹配。我们如何接收已经找到匹配的信息？简单来说，我们在特定集合`FirebaseAPI.db.collection("multiplayerQueue").doc(facebookStuff.userId).onSnapshot()`下的特定文档上添加一个事件监听器

看起来是这样的:

因此，它的作用是监听数据库中特定的集合和特定的文档的变化。

> 参见:[https://firebase . Google . com/docs/firestore/query-data/listen](https://firebase.google.com/docs/firestore/query-data/listen?authuser=1)

因此，当服务器实际上找到一个匹配时，它用我们的`userId`在一个文档上的数据库中写入一些数据，当然对我们的对手也是如此，所以每个人都收到一个关于上述函数的事件。

一旦接收到事件，我们现在只需要实际连接到我们的对手，在数据库文档中，我们的`userId`也有我们的对手`userId`，我们将通过脸书 API 使用它来连接到他们(脸书确认窗口也会弹出，这是我们无法控制的)。

> ！注意，一旦接收到事件并触发回调函数，我们就关闭并断开事件侦听器(我们不再需要任何其他事件)。如果我们再次执行`findMatch`过程，将会建立一个新的事件监听器。

让我们看看与播放器部分的联系:

当这个问题解决并且弹出窗口被用户接受时，两个玩家被连接并插入到一个信使线程中，并接收一个用于他们游戏会话的`matchId`。

作为一个额外的，我们也可以接收连接的玩家的信息(假设他们也同意和我们一起玩他们的弹出窗口)

上面的函数接收来自这个上下文(messenger 线程)的所有玩家，基本上是我们自己和对手，我们通过`userId`过滤掉我们自己，并加载对手的个人资料图像和数据。然后我们可以在游戏中随心所欲地使用它们。

请继续关注第 2 部分，这是这些 API 调用背后的后端逻辑。

**尽情享受！**