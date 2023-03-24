# 通过以下 6 个性能提示，将你的 Flutter 应用程序提升到最大

> 原文：<https://levelup.gitconnected.com/boost-your-flutter-apps-to-the-max-with-these-6-performance-tips-db8ebeb733ba>

## 慢 app？没问题！

## 使用这些针对 Flutter 应用程序的性能提示，获得更流畅的滚动和动画、更少的内存消耗和更高的执行速度。

![](img/a81f0960358b311ee3e4a3b46a4ed753.png)

照片由[马修·施瓦茨](https://unsplash.com/@cadop?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄

这里有一些关于性能的 Flutter 应用程序的易于使用的最佳实践。如果您注意到任何问题，请使用它们，但不要过早优化。默认情况下，Flutter 应用程序很快，你通常不会有任何问题。

另外，一定要下载我的关于颤振性能技巧的免费电子书。你可以在本文末尾找到链接。

## 比起 StatefulWidget 更喜欢 StatelessWidget

`StatelessWidget`比`StatefulWidget`快，因为顾名思义，它不需要管理状态。这就是为什么如果可能的话你应该更喜欢它。

选择一个`StatefulWidget`当…
▲您需要一个带有`initState()`
的准备函数▲您需要一个带有`dispose()`
的处置资源▲您需要一个带有`setState()`
的触发小部件重建▲您的小部件有变化的变量(非最终)

在所有其他情况下，你应该选择`StatelessWidget`。

❌是个坏例子

```
import 'package:flutter/widgets.dart';

class MyWidget extends StatefulWidget {
    final String header;
    final String subheader;
    const MyWidget({Key? key, required this.header, required 
            this.subheader}): super(key: key);
    @override
    State<MyWidget> createState() => _MyWidgetState();
}

class _MyWidgetState extends State<MyWidget> {
    @override
    Widget build(BuildContext context) {
        return Column(children: [Text(widget.header),
                Text(widget.subheader)]);
    }
}
```

✅就是一个好例子

```
import 'package:flutter/widgets.dart';

class MyWidget extends StatelessWidget {
    final String header;
    final String subheader;
    const MyWidget({Key? key, required this.header, required
            this.subheader}): super(key: key);

    @override
    Widget build(BuildContext context) {
        return Column(children: [Text(header), Text(subheader)]);
    }
}
```

## 在长列表或网格上使用生成器构造函数

当处理包含许多条目的列表或网格时，建议使用`builder`构造函数。它们的优点是只呈现可见的元素。因此，如果你的列表或网格大于一个设备屏幕，你应该选择这种方法。对于较小的列表和网格，也可以使用默认的构造函数。这适用于所有类型的列表和网格，如[列表视图](https://api.flutter.dev/flutter/widgets/ListView-class.html)、[重新排序列表视图](https://api.flutter.dev/flutter/material/ReorderableListView-class.html)或[网格视图](https://api.flutter.dev/flutter/widgets/GridView-class.html)。始终检查构建器构造函数，如果可能的话就使用它。

✅就是一个好例子

```
final List<int> _listItems = <int>[1, 2, 3, 4, 5, 6, 7, 8, 9];

@override
Widget build(BuildContext context) {
    return ListView.builder(
        itemCount: _listItems.length,
        itemBuilder: (context, index) {
            var item = _listItems[index];
            return SizedBox(
                height: 300, child: Center(child:
                    Text(item.toString())));
        }
    );
}
```

```
🔔 Get a short summary of my Medium content on the 1st of each month to your inbox. Save time and pick what you like to read! 

Click [HERE](http://bit.ly/xeladu-medium) to subscribe for free!
```

## 不要使用 OpacityWidget

使用动画时，`Opacity`小部件会导致性能问题，因为`Opacity`小部件的所有子小部件也会在每个新帧中重建。在这种情况下，最好使用`AnimatedOpacity`。如果您想要淡入图像，请使用`FadeInImage`小部件。如果你想要一种不透明的颜色，画一种不透明的颜色。

❌是个坏例子

```
Opacity(opacity: 0.5, child: Container(color: Colors.red))
```

✅就是一个好例子

```
Container(color: Color.fromRGBO(255, 0, 0, 0.5))
```

## 预缓存图像和图标

Flutter 包含一个功能`precacheImage()`，可以用来减少图像和图标的加载时间。例如，您可以在`initState()`或`didChangeDependencies()`方法中使用它来加速图像显示。

```
const List<CatImage> images = [
  CatImage(width: 150, height: 150, caption: 'Watch out'),
  CatImage(width: 150, height: 160, caption: 'Hmm'),
  CatImage(width: 160, height: 150, caption: 'Whats up'),
  CatImage(width: 140, height: 150, caption: 'Miaoo'),
  CatImage(width: 130, height: 150, caption: 'Hey'),
  CatImage(width: 155, height: 150, caption: 'Hello'),
];

@override
void didChangeDependencies() {
    super.didChangeDependencies();
    for (CatImage image in images) {
        precacheImage(NetworkImage(image.url), context);
    }
}
```

## 减少 ListView 的内存消耗

一个`ListView`有两个属性(`addRepaintBoundary`和`addAutomaticKeepAlives`)，在某些情况下会导致高内存消耗。他们告诉`ListView`不要处理目前在屏幕上不可见的项目。就性能而言，这很好，因为滚动很快。但是不可见的项目仍然占用内存空间。如果您有一个很长的图像列表，这可能会导致一个问题。解决方法是将提到的属性设置为 false(默认设置为`true`)。

```
ListView.builder(
    addAutomaticKeepAlives: false,
    addRepaintBoundaries: false,
    …
);
```

## 总是使用钥匙

每个小部件都有一个`key`构造函数参数，并在小部件树中创建一个元素。一个`key`允许我们重用一个元素，而不是创建一个新元素。您可以使用`ValueKeys`或`GlobalKeys`。`GlobalKeys`可能会比较棘手，因为在窗口小部件树中不可能有多个窗口小部件具有相同的关键字。另一方面，这会导致代码膨胀，因为你需要为每个小部件定义它们。

🔔你想要更多的提示吗？查看我的免费电子书！100 多名顾客已经拿到了免费的拷贝！

[***通过我的推荐链接加入成千上万的媒体会员，每月只需 5 美元就可以阅读你想阅读的文章。***](https://medium.com/@xeladu/membership)

[](https://medium.com/@xeladu/membership) [## 通过我的推荐链接加入 Medium-xela du

### 只需点击一下，就可以通过会员资格访问数千篇文章！您的会员资格只需 5 美元一张…

medium.com](https://medium.com/@xeladu/membership) 

点击 [**此处**](http://medium-newsletter.quickcoder.org/) 每月一次获取我所有的中篇文章汇总🔔
浏览[我的口香糖商店](https://xeladu.gumroad.com/)寻找有趣的编程素材🏬

![xeladu](img/c6d3049714795a7a8169ac0957f05cfe.png)

[赛拉杜](https://xeladu.medium.com/?source=post_page-----db8ebeb733ba--------------------------------)

## 软件工程师的高级颤振文章

[View list](https://xeladu.medium.com/list/advanced-flutter-articles-for-software-engineers-f074879fdef3?source=post_page-----db8ebeb733ba--------------------------------)9 stories![](img/e5774d314e770573e2601ca542dbc4de.png)![](img/5289da1df3e789a8ca1d3f6024c01f4b.png)![](img/642d00374fa0d6e6971120398fbbeb90.png)![xeladu](img/c6d3049714795a7a8169ac0957f05cfe.png)

[赛拉杜](https://xeladu.medium.com/?source=post_page-----db8ebeb733ba--------------------------------)

## 适合初学者的颤振文章

[View list](https://xeladu.medium.com/list/flutter-articles-for-beginners-a040ea777956?source=post_page-----db8ebeb733ba--------------------------------)24 stories![](img/51383106204c2ff6d45c05fd52772a7d.png)![](img/cdd4d94a464cda34d46fc00e13cf7bf9.png)![](img/367140fae23113f1e2e868c2157d1e71.png)