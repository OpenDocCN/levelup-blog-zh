# Flutter 闪屏——处理异步依赖的简单方法

> 原文：<https://levelup.gitconnected.com/flutter-splash-screen-a-simple-way-to-handle-async-dependencies-7a9f559eb280>

![](img/f8a0188e1ae0a2a8ffc0c5206d23548b.png)

Gabriel Laroche 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

说实话，这年头谁有大把时间？等待事情发生并不好玩。因此，我们总是希望一切都是即时的，或者用我们开发人员的话说:同步的。

但是如果你在开始做某件事之前需要等一个人，你会怎么做呢？所谓的异步依赖性。

我不记得上一次我写的应用程序在启动时没有某种形式的异步依赖是什么时候了。这就是闪屏的用途。

在 Flutter 中实现一个非常简单。把你的`MaterialApp`改成`StatefulWidget`，在顶部加上`bool _isInitialized = false;`，然后在`initState()`函数中初始化你的依赖关系，对吗？

```
bool **_isInitialized** = false;

@override
void initState*() {* super.initState*()*;
  **_initializeAsyncDependencies*()***;
*}* Future*<*void*>* _initializeAsyncDependencies*()* async *{* // **do initialization**
  setState*(() {* **_isInitialized = true;**
  *})*;
*}*
```

> 那有什么问题呢？

一开始，什么都没有。但是当您的应用程序和需求增长时，您可能会很快遇到这种方法的可维护性问题。

在我目前正在开发的应用程序中，我们是这样开始的，但过了一段时间，我们有太多的东西在那里进行，以至于很难理解在哪里、做什么以及如何初始化。我们有

*   来自服务器的翻译
*   一个需要为 cookie 同步目的初始化的网络视图(这是一个完全不同的话题，相信我，这并不有趣)
*   为了呈现我们的主要内容，我们需要一个配置文件
*   远程配置

一旦所有的东西都被加载，我们希望在主屏幕上显示一个漂亮的 reveal 过渡。

> 同样，所有这些都是可能的，我们做到了。一起工作简直是一场噩梦。

![](img/8a25be8806fa2fe9ac4ff11a9e7811ef.png)

照片由 [Tim Gouw](https://unsplash.com/@punttim?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

所以我坐下来想了想……如果这件事可以用完全不同的方式来做呢？

我的目标是将主应用程序转换成一个`**StatelessWidget**`，并在某个解耦的地方完成我们所有的初始化。从用户的角度来看，行为是不可改变的。

事实证明，解决方案既简单又优雅。

# 第一步也是唯一的一步

```
void **main()** {
  **runApp**(
    **SplashApp**(
      key: UniqueKey(),
      onInitializationComplete: () => **runMainApp()**,
    ),
  );
}

void **runMainApp()** {
  **runApp**(
    **MainApp()**,
  );
}
```

需要阐述？

好的……创建一个`**SplashApp**`,它除了初始化你的异步依赖之外什么也不做，同时显示一个漂亮的闪屏。问你最喜欢的用户界面的人。

向`**SplashApp**`传递一个回调函数，一旦所有东西都被加载，这个函数就会被执行。

# SplashApp

```
class **SplashApp extends StatefulWidget** {
  final VoidCallback onInitializationComplete;

  const SplashApp({
    Key key,
    @required this.onInitializationComplete,
  }) : super(key: key);

  @override
  _SplashAppState createState() => _SplashAppState();
}

class **_SplashAppState extends State<SplashApp>** {
  bool _hasError = false;

  @override
  void initState() {
    super.initState();
    **_initializeAsyncDependencies()**;
  }

  Future<void> **_initializeAsyncDependencies()** async {
 **// >>> initialize async dependencies <<<
    // >>> register favorite dependency manager <<<
    // >>> reap benefits <<<**
    Future.delayed(
      Duration(milliseconds: 1500),
      () => **widget.onInitializationComplete()**,
    );
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Splash Screen',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: _buildBody(),
    );
  }

  Widget _buildBody() {
    if (_hasError) {
      return Center(
        child: RaisedButton(
          child: Text('**retry**'),
          onPressed: () => **main()**,
        ),
      );
    }
    return Center(
      child: CircularProgressIndicator(),
    );
  }
}
```

回调只是为不同的应用程序执行`run`函数，在这个例子中是`**MainApp**`。

如果你这样做，那么你的`**SplashApp**`将被清理，你只剩下一个`**MainApp**`，在其中你可以同步访问所有东西。

# 主应用程序

```
class **MainApp extends StatelessWidget** {
  @override
  Widget build(BuildContext context) {
 **// >>> use any dependency from your dependency manager <<<** 
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: HomePage(title: 'Flutter Demo Home Page'),
    );
  }
}
```

看，在`**MainApp**`中没有复杂性。因此，我们可以得出结论:

目标实现。

如果你有任何意见，请告诉我！

你可以在 GitHub 上找到完整的例子。下载它，使用它，享受它。

[](https://github.com/grAPPfruit/flutter-smooth-splash-screen) [## 葡萄柚/颤动-平滑-闪屏

### 说实话，这年头谁有大把时间？等待事情发生并不好玩。所以我们总是想…

github.com](https://github.com/grAPPfruit/flutter-smooth-splash-screen)