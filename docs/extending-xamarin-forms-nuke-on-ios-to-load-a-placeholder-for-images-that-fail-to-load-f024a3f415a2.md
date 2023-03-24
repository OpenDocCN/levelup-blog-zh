# 扩展 Xamarin。iOS 上的 Forms.Nuke 为加载失败的图片加载一个占位符

> 原文：<https://levelup.gitconnected.com/extending-xamarin-forms-nuke-on-ios-to-load-a-placeholder-for-images-that-fail-to-load-f024a3f415a2>

每当我们从网上加载图片时，都有可能加载失败。为了获得更好的用户体验，准备好占位符机制至关重要。在这篇文章中，我将向你展示我如何扩展我的 Xamarin 分支。Forms.Nuke 在 iOS 上实现这个目标。

## 什么是 Xamarin？表格。核弹？

[Xamarin。Forms.Nuke](https://www.sharpnado.com/xamarin-forms-nuke/) 是一个 Xamarin。表单实现 [Nuke](https://github.com/kean/Nuke) ，这是当今 iOS 上最先进的图像库之一。 [Xamarin。表单实现](https://github.com/roubachof/Xamarin.Forms.Nuke)主要关注缓存，而原始库有更多的特性。当我开始通过我以前使用的 [Akavache](https://github.com/reactiveui/Akavache) 寻找缓存图像的替代方案时，我了解到了这个库(我从来没有在博客上讨论过这个部分，因为它还没有准备好，tbh)。

## 为什么我们需要扩建图书馆？

该库目前只做一件事(相当不错)——处理网页图像的缓存。它使用原生核武器库的默认设置。`Xamarin.Forms`实现覆盖了`UriImageSource`和`FileImageSource`的`ImageSourceHandler`(可选)，但是占位符加载的情况在最初的版本中是不允许的。由于我有多个使用占位符的场景，所以我决定扩展这个库——并从现在开始维护我自己的分支。(也许还会有对原始源的拉取请求)。

## 给我看看一些代码，最后！

对于我们的扩展，我们修改了`FormsHandler`类和`ImageSourceHandler`类。先来看看`FormsHandler`类。我们正在为占位符添加一个新属性:

```
public static ImageSource PlaceholderImageSource { get; private set; }
```

我选择了`FormsHandler`类，因为设置占位符是一件全局的事情(在我的场景中，您的里程可能会有所不同)。这已经包含了`FormsHandler`类中的所有内容，所以让我们来看看`ImageSourceHandler`类。

由于我们使用默认的`Xamarin.Forms` `ImageSourceHandler`作为资源图像(这是一个`StreamImageSource`)和`FontImageSource`，我们需要首先为它们添加静态字段:

```
private static readonly StreamImagesourceHandler DefaultStreamImageSourceHandler = new StreamImagesourceHandler();

private static readonly FontImageSourceHandler DefaultFontImageSourcehandler = new FontImageSourceHandler();
```

现在让我们用一个单独的方法来实现占位符的加载:

```
private static Task<UIImage> LoadPlaceholderAsync()
{
    switch (FormsHandler.PlaceholderImageSource)
    {
        case StreamImageSource streamImageSource:
            FormsHandler.Warn($"loading placeholder from resource");
            return DefaultStreamImageSourceHandler.LoadImageAsync(streamImageSource);

        case FontImageSource fontImageSource:
            FormsHandler.Warn($"loading placeholder from Font");
            return DefaultFontImageSourcehandler.LoadImageAsync(fontImageSource);
        default:
            FormsHandler.Warn($"no valid placeholder found");
            return null;
    }
}
```

如您所见，这种方法没有什么过于复杂的地方。基于在`FormsHandler`类中设置的占位符的类型，我们为占位符图像调用默认的`Xamarin.Forms`实现。让我们通过改变`ImageSourceHandler`的`LoadImageAsync`方法来执行这段代码:

```
public async Task<UIImage> LoadImageAsync(
    ImageSource imageSource,
    CancellationToken cancellationToken = new CancellationToken(),
    float scale = 1)
{
    var result = await NukeHelper.LoadViaNuke(imageSource, cancellationToken, scale);
    if (result == null)
        result = await LoadPlaceholderAsync();

    return result;
}
```

因为我们需要知道`Nukehelper`类是否能够加载图像，所以我们已经通过在这个级别等待它来运行代码。如果结果为空，我们将通过之前实现的方法加载占位符图像。这就是我们在分叉的`Xamarin.Forms.Nuke`存储库中需要做的一切。

## 如何在您的 Xamarin 中使用它。表单— iOS 项目

首先，[克隆`Xamarin.Forms.Nuke`库的我的分支](https://github.com/MSiccDev/Xamarin.Forms.Nuke)(如果你愿意，也可以分支它)并将其导入到你的`Xamarin.Forms`解决方案中，并在你的 iOS 项目中引用它。一旦完成，我们需要在`AppDelegate`的`FinishedLaunching`方法中初始化 Nuke 库(就像在原始源代码中一样):

```
Xamarin.Forms.Nuke.FormsHandler.Init(true, false);
```

第二步是定义占位符图像源。`FontImageSource`应该在`LoadApplication`方法之后定义。这样，你可以用`Xamarin.Forms`的方式加载字体作为资源。

```
//Resource image
Xamarin.Forms.Nuke.FormsHandler.PlaceholderFromResource("CachedImageTest.MSicc_Logo_Base_Blue_1024px_pad25.png", Assembly.GetAssembly(typeof(MainViewModel)));

//FontImageSource
Xamarin.Forms.Nuke.FormsHandler.PlaceholderFromFontImageSource(new FontImageSource
{
    Glyph = CachedImageTest.Resources.MaterialDesignIcons.ImageBroken,
    FontFamily = "MaterialDesignIcons",
    Color = Color.Red
});
```

现在像往常一样使用`Xamarin.Forms` `Image`控件。如果无法加载来自 web 的图像，您将会看到占位符，如以下两个示例所示:

![](img/66012142d53fbec5fb6ad30b842231cc.png)

左:单幅图像加载失败—右:`CollectionView` 中的图像加载失败

通过对`Xamarin.Forms.Nuke`库的一些补充，我们实现了一个占位符机制，用于不能加载的图像。一如既往，我希望这篇文章对你们有些人有用。现在我已经在 iOS 上实现了一个快速缓存的图像，并加载了占位符，我将转向 Android，在那里我将尝试使用 [Glidex 来实现同样的功能。窗体](https://github.com/jonathanpeppers/glidex)库，并扩展它来加载一个占位符。一旦实施，将有一个完整的样本。敬请期待！

**直到下一个帖子，大家编码快乐！**

扩展 Xamarin 的帖子[。iOS 上的 Forms.Nuke 为加载失败的图片加载占位符](https://msicc.net/extending-xamarin-forms-nuke-to-load-a-placeholder-for-images-that-fail-to-load/)首先出现在 [MSicc 的博客](https://msicc.net)。