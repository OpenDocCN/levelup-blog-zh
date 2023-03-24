# 使用提供者的颤振状态管理

> 原文：<https://levelup.gitconnected.com/state-management-in-flutter-using-provider-1dc2a8e9f2b1>

在颤振中使用 MVVM

![](img/e87a654aad7a050aad35715bd6878ee0.png)

罗伯特·蒂曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

主要来自科特林的背景，我发现一开始很难理解 flutter。这主要是因为 Flutter 遵循声明式编程风格，而 Kotlin 遵循命令式编程风格。

> 声明式编程关注的是程序应该完成什么，而不是如何完成。

由于这个原因，我们在 flutter 中编程的方式变得非常不同。我举个例子。在 Kotlin 中，如果我们必须更新 TextView 的文本，那么我们可以很容易地调用 setText 方法。

```
textView.*text* = "New text"
```

但是在 Flutter 中更新文本是完全不同的，因为它不允许我们更新文本字段的文本。我们可以做的是，通过调用 StatefulWidget 中的 setState，用更新后的文本值重新构建组件。

```
setState(() {
  newText = "New text";
});
```

如果您也来自 Kotlin 后台，我想您应该非常熟悉 MVVM 架构和 livedata，其中一个简单的代码用于从 viewmodel 获取和显示数据，同时管理不同的状态，如下所示:

```
private fun observeSkuListResponse() {
    homeViewModel.skuListResponse.observe(this, *Observer* **{** when (**it**.status) {
            Status.LOADING -> {
                binding.loadingLayout.*visibility* = View.*VISIBLE*
            }
            Status.SUCCESS -> {
                binding.loadingLayout.*visibility* = View.*GONE
                // perform other operation*}
            Status.ERROR -> {
               binding.loadingLayout.*visibility* = View.*GONE* binding.errorLayout.*visibility* = View.*VISIBLE* }
        }
    **}**)
}
```

代码非常简单:如果你的状态是加载状态，那么你只显示加载屏幕。如果您的响应状态为完成，则隐藏加载屏幕并显示数据。如果您的响应有错误，那么您将隐藏所有其他视图并显示错误消息。

由于 flutter 没有可见性消失或可见的概念，所以我们必须直接传递我们想要在屏幕上显示的视图，并重新绘制组件。为了获得状态，我们将使用 provider。

## 提供商简介

Provider 用于 flutter 中的状态管理。它由 3 个部分组成

*   更改通知程序
*   更改通知程序提供程序
*   消费者

**变更通知**对于 MVVM 建筑来说，就像 viewmodel 对于 Kotlin 一样。它是管理屏幕状态的中心点。如果它内部的状态发生了变化，那么它会通知框架重新构建屏幕。

**ChangeNotifierProvider** 在视图中提供了一个 ChangeNotifier 的实例。

**Consumer** 是一个允许我们使用 ChangeNotifier 的小部件。

> 将您的消费者放在需要访问变更通知程序的代码上面。这是因为当有一些变化时，它会重建它的派生窗口小部件，而你不想每次有一些变化时都要重建整个屏幕。

现在让我们进入代码。

## 视图模型

这里我创建了一个函数 fetchSkuList()，它从 _homeRepo 中获取 SKU，然后在 skuListUseCase 中设置值。最后，它通过调用 **notifyListener()** 来通知数据的变化。

这里，Response 是一个负责向 UI 传达数据状态的类，即加载、完成或错误。

这些状态由 ResponseState 表示，ResponseState 是一个枚举，包括:

```
enum ResponseState{
  LOADING,
  COMPLETE,
  ERROR
}
```

## 用户界面屏幕

在我的主屏幕中，首先，我已经获取了 initState 中的数据。然后在构建方法中，我将 **ChangeNotifierProvider** 和 **Consumer** 放在 Appbar 的正下方，因为我希望当数据状态改变时，屏幕的其余部分也相应地更新。添加消费者后，switch 语句检查当前状态:加载、出错或完成，并相应地更新 UI。

LoadingScreen 和 ErrorScreen 是显示进度条和文本消息的简单屏幕。

**加载屏幕**

```
import 'package:flutter/material.dart';

class LoadingScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Center(
        child: Container(
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          CircularProgressIndicator(),
          SizedBox(
            height: 8,
          ),
          Text(
            'Loading...',
            style: TextStyle(fontFamily: 'Roboto'),
          )
        ],
      ),
    ));
  }
}
```

**错误屏幕**

```
import 'package:flutter/material.dart';

class ErrorScreen extends StatelessWidget {
  final String msg;

  ErrorScreen(this.msg);

  @override
  Widget build(BuildContext context) {
    return Center(
        child: Container(
            child: Text(
      '$msg',
      style: TextStyle(fontFamily: 'Roboto'),
    )));
  }
}
```

就是这样:使用 provider 管理状态的最简单方法。

查看我的 github repo 了解更多细节。

[](https://github.com/RumiRajbhandari/CleanArchFlutter) [## RumiRajbhandari/cleanarchfutter

### 在 GitHub 上创建一个帐户，为 RumiRajbhandari/cleanarchfutter 的开发做出贡献。

github.com](https://github.com/RumiRajbhandari/CleanArchFlutter) 

如果你喜欢这篇文章，别忘了鼓掌并留下评论。:)