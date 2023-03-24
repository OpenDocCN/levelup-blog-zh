# 理解异步 Javascript:承诺

> 原文：<https://levelup.gitconnected.com/understanding-asynchronous-javascript-promises-4f46754f78ee>

![](img/487609b42b452177987952db382078e8.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [samsommer](https://unsplash.com/@samsomfotos?utm_source=medium&utm_medium=referral) 拍摄的照片

你好。是克里斯托弗。因为害怕，有一段时间我一直在回避这个话题。哈哈，我喜欢承认我的弱点，这样它也能帮助你！承诺在异步 Javascript 中使用，它允许你的程序继续运行，不会因为代码块没有完成它的任务而停止。

想想一家餐馆是如何经营的。想象一下，如果每个服务员一次只等 1 桌？队伍将会更长，因为没有那么多的桌子和座位，人们将开始抱怨等待时间长。他们被称为服务员是有原因的。他们应该侍候你*。*

同样的概念也用在承诺上。当一个代码块等待服务器的响应时(例如，*服务员为一张桌子等待食物*)，程序的其余部分可以继续做它正在做的事情(其他服务员为其他桌子服务)。

## 另一个真实的例子

我过去经常玩视频游戏，我记得有几个游戏允许你和精灵互动，或者允许你在游戏加载时玩一个迷你游戏。这是一个异步代码的完美例子。请注意，即使游戏还没有加载，你仍然能够与游戏互动。

既然您已经对异步代码的工作逻辑有了相当好的理解，现在让我们实际开始编写一些基本的代码，您可以随意摆弄，看看它是如何工作的！

```
let promise = new Promise( ( resolve, reject ) => { } )
```

在您的代码编辑器中，创建一个创建新承诺的新变量。Resolve 和 reject 是在满足特定条件后处理代码的参数。

```
let promise = new Promise( (resolve, reject) => {
    let evenNumber = 1+1;
    if( evenNumber % 2 == 0 ){
        resolve( 'Success' )
    else { 
        reject( 'Failed' )
    }})
```

在上面的例子中，resolve 和 reject 接受一个字符串参数，该参数将消息发送到控制台或任何您想要放置消息的地方。在下面的例子中，您将真正能够更详细地理解这一点。

```
promise.then( message => {
    console.log( message )
}).catch( message => {
    console.log( message )
})
```

消息可以是你想要的任何内容。这可以对最终用户隐藏或显示，这取决于您正在构建的应用程序的类型。

这是另一个模拟现实世界问题的例子

```
let userLeft = true; function isLoggedIntoServer(){ return new Promise( (resolve, reject) => { if( !userLeft ){ resolve( "User is still logged into the server" ) } else { reject( "User has left the server :(" ) } })}isLoggedIntoServer( )
.then( message => { console.log(message) } )
.catch( message => { console.log(message) } )
```

在本例中，您可以看到在调用了***isLoggedIntoServer()****之后，将运行“ *then* 或“ *catch* ”。Catch 将向您发送错误消息，然后给您一条成功消息。同样，消息可以是您喜欢的任何内容。*

*尝试将 ***userleft*** 变量改为 *false* ，看看会得到什么。很酷吧？比写一大堆回调函数要好！*

*如果你有多个承诺呢？例如，多个视频被上传到服务器。这一次我们可以使用 *Promise.all* 。 *Promise.all* 接受一个由承诺组成的数组。参见下面的例子*

```
*const userVideoUpload1 = new Promise( ( resolve, reject ) => { setTimeout( ( ) => { resolve( 'Video 1 has been uploaded' ) }, 5000)});const userVideoUpload2 = new Promise( ( resolve, reject )  => { setTimeout( ( ) => { resolve( 'Video 2 has been uploaded' ) }, 1000)})const userVideoUpload3 = new Promise( ( resolve, reject ) => { setTimeout(( ) => { resolve( 'Video 3 has been uploaded' ) }, 10000)});Promise.all( [userVideoUpload1,userVideoUpload2,userVideoUpload3] )
.then( message => {     console.log( message )});*
```

*如果没有拒绝声明，默认情况下，承诺将自行解决。注意这里有多个超时函数。它们中的每一个都有不同的时间间隔。 *Promise.all* 最后一个承诺(耗时最长的一个)完成后，会在控制台显示消息。*

*很简单。希望如此。*

*如果你不明白这一点，那也没关系！只要练习，把你不明白的东西分解成小块，自己做一些研究，找出你需要知道的东西。一旦你掌握了承诺，你就可以完全投入到*“等待和异步”* javascript 中。*

## *在网上和我联系！*

*   *[和我一起工作](https://digyt.co)*
*   *[个人博客](http://www.christopherclemmons.com/)*
*   *电子邮件:christopher.clemmons2020@gmail.com*