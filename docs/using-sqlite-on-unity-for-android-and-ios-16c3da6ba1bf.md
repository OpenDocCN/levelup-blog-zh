# 在 Android 和 iOS 的 Unity 上使用 SQLite

> 原文：<https://levelup.gitconnected.com/using-sqlite-on-unity-for-android-and-ios-16c3da6ba1bf>

![](img/fbad2b3c0f2093257425dc58e54a8656.png)

在浏览 [RudderStack](https://github.com/rudderlabs/rudder-server) SDK 路线图时，我们决定将 Unity next 纳入支持的平台列表。为什么是统一？我们发现，尽管在很大程度上依赖于数据，但托管的客户数据管理解决方案并没有为游戏行业提供足够的服务。这种对数据的依赖是多种因素造成的。游戏产生大量数据，尤其是来自移动设备的遥测数据，而游戏公司在产品货币化方面面临巨大困难。

# 背景

[RudderStack](https://rudderstack.com/) 承诺捕获用户生成的事件。我们将这些事件路由到不同的目的地，比如云应用程序、数据仓库和云文件系统。

为了成功实施 Unity SDK，我们有四个主要要求:

1.  SDK 应该实现初始化和事件收集机制。
2.  事件应该临时存储在持久存储上，以确保我们希望平台具有的交付语义。
3.  事件应该被推送到方向舵服务器。这些事件将在不同的环境中运行。
4.  最后，应该有一种机制来处理设备模式的 SDK。设备模式 SDK 支持许多目的地，这些目的地是 RudderStack 服务器到服务器集成所不能支持的。设备模式支持的另一个目的是利用本机 SDK 的功能，特别是推送通知、属性等等。

# 挑战

# 为 Unity 选择正确的 C#版本

我们遇到的第一个挑战是选择正确的 C#版本。团结支持。Net 3.5，。Net 4.x，以及。净标准。但是，3.5 版和 4.x 版之间有一些重大的变化和不兼容。许多流行的游戏都是在 3.5 版上构建的。迁移到 4.x 是一个如此痛苦的过程，以至于电影公司宁愿停留在旧版本上。

我们开始用开发 Unity SDKs。Net 4.x，发布后不久，我们就意识到很多游戏因为是在 3.5 版本开发，所以无法使用我们的 SDK。最终，我们重写了大约 80%的代码，回到了 3.5 版本。

## 从中吸取的教训

在开始开发之前，请务必做好以下研究:

1.  业界采用最多的软件版本是哪个？
2.  与我们的项目相关的 SDK 的采用情况如何？通过识别开发人员在使用什么和为什么使用来学习和区分优先级。

从产品和工程的角度来看，这些都是重要的考虑因素。

# 将 Unity 数据安全一致地传送到选定的目的地

现在主要的挑战来了，这也是我们写这篇博客的原因。通过不可靠的网络捕获和传输数据是一项艰巨的任务。如果您不能安全、一致地将数据传送到选定的目的地，那么捕获数据是徒劳的。一种安全一致地交付数据的方法是使用某种缓存机制来存储事件。这种机制会存储事件，直到您收到数据已正确传送到目的地的确认。

您可以使用 [PlayerPrefs](https://docs.unity3d.com/ScriptReference/PlayerPrefs.html) 在 Unity 上存储事件。然而，我们有三个主要的限制:

1.  我们应该能够存储任意大小的事件缓冲区。我们在 Rudder 中使用的默认缓冲区大小是 10K 事件。然而，对于这种情况，我们希望能够放大和缩小它。
2.  应该有在存储的事件上定义和实施模式的灵活性。PlayerPrefs 是一个简单的键值存储，支持非常有限的一组数据类型，没有层次结构。
3.  管理缓冲区不应该发生在主线程中。使用 PlayerPrefs 发生在主线程中，这在大多数情况下不是一个可行的选择。

为了这个目的，我们决定使用 SQLite，而不是 PlayerPrefs。SQLite 是一个轻量级、可嵌入、高度可靠的 SQL 数据库引擎，非常适合移动设备。然而，在 Unity 中嵌入 SQLite 并不简单。

# 实施

# 入门指南

。Net 和 Unity 不支持 SQLite 开箱即用。我们还希望尽可能减少第三方库的使用，以保持 SDK 尽可能的轻量级。收集事件应该是极其轻量级的，不会引入 bug 和大量依赖。

最初，我们试图构建独立的二进制文件来支持 SQLite 并与 Unity SDK 集成。这里的问题是，这种设计在生产中部署和维护起来很痛苦，因为它不是线程安全的。

# Android 和 iOS 的 Unity 插件支持

因此，我们最终决定遵循一个流行的 Unity 模式，包括开发 Unity 提供的优秀的[插件](https://docs.unity3d.com/Manual/Plugins.html)。

Unity 使用预处理器指令同时支持 Android 和 iOS 的代码。它控制将根据平台执行的代码行。我们利用这个 Unity 产品为 Unity 分别构建了 Android SDK 和 iOS 插件。

例如，我们的 for Unity 插件有一个初始化`RudderClient`的方法，看起来像下面的代码片段:

```
+ (void) _initiateInstance: (NSString*) _anonymousId
                  writeKey: (NSString*) _writeKey
               endPointUrl: (NSString*) _endPointUrl
            flushQueueSize: (int) _flushQueueSize
          dbCountThreshold: (int) _dbCountThreshold
              sleepTimeOut: (int) _sleepTimeout
                configRefreshInterval: (int) _configRefreshInterval
      trackLifecycleEvents: (BOOL) _trackLifecycleEvents
         recordScreenViews: (BOOL) _recordScreenViews
                  logLevel: (int) _logLevel;
```

这将从 Unity 桥获取所有必要的参数，并启动`RudderClient`。我们对 Android 有如下类似的方法:

```
public static void _initiateInstance(
            Context _context,
            String _anonymousId,
            String _writeKey,
            String _endPointUrl,
            int _flushQueueSize,
            int _dbCountThreshold,
            int _sleepTimeout,
            int _configRefreshInterval,
            boolean _trackLifecycleEvents,
            boolean _recordScreenViews,
            int _logLevel
    )
```

现在，当我们想从 C#代码中调用这些方法时，我们可以在运行时使用预处理器指令来控制这些方法的调用以及平台。C#中的代码如下所示:

```
#if UNITY_IPHONE
[DllImport("__Internal")]
private static extern void _initiateInstance(
    string _anonymousId,
    string _writeKey,
    string _endPointUrl,
    int _flushQueueSize,
    int _dbCountThreshold,
    int _sleepTimeout,
    int _configRefreshInterval,
    bool _trackLifecycleEvents,
    bool _recordScreenViews,
    int _logLevel
);
#endif
```

这段代码定义了用于 iPhone 环境的方法。当我们需要调用一个方法时，我们可以检查当前运行时并使用下面的代码片段调用这个方法:

```
#if UNITY_IPHONE
if (Application.platform == RuntimePlatform.IPhonePlayer)
{
    _initiateInstance(
        RudderCache.GetAnonymousId(),
        _writeKey,
        _endPointUrl,
        _flushQueueSize,
        _dbCountThreshold,
        _sleepTimeout,
        _configRefreshInterval,
        _trackLifecycleEvents,
        _recordScreenViews,
        _logLevel
    );
}
#endif
```

类似地，对于 Android，我们必须在 Unity 代码中定义一个`AndroidJavaClass`对象，这样我们就可以在代码中访问它的方法。为此，请使用以下代码:

```
#if UNITY_ANDROID
private static readonly string androidClientName = "com.rudderstack.android.sdk.wrapper.RudderClientWrapper";private static AndroidJavaClass androidClientClass;AndroidJavaClass unityPlayer = new AndroidJavaClass("com.unity3d.player.UnityPlayer");
AndroidJavaObject activity = unityPlayer.GetStatic<AndroidJavaObject>("currentActivity");
AndroidJavaObject context = activity.Call<AndroidJavaObject>("getApplicationContext");
androidClientClass = new AndroidJavaClass(androidClientName);
#endif
```

最后，为了从我们的 Android 插件中调用`initiateInstance`方法，我们将检查运行时并使用`AndroidJavaClass`对象的`callStatic`方法进行调用，如下所示:

```
#if UNITY_ANDROID
if (Application.platform == RuntimePlatform.Android)
{
    androidClientClass.CallStatic(
        "_initiateInstance",
        context,
        RudderCache.GetAnonymousId(),
        _writeKey,
        _endPointUrl,
        _flushQueueSize,
        _dbCountThreshold,
        _sleepTimeout,
        _configRefreshInterval,
        _trackLifecycleEvents,
        _recordScreenViews,
        _logLevel
    );
}
#endif
```

# 使用 Unity 插件目录

此外，Unity 在其名为`Plugins`的`Assets`目录下支持一种特殊的目录。该目录包含应用程序所需的所有平台特定的库。只需创建两个文件夹，一个名为`Android`，另一个名为`iOS`，位于`Plugins`文件夹中。Unity 会自动将这些文件添加到相应平台的项目的最终产品/版本中。

我们利用这一机制，为 Android 和 iOS 构建了插件。我们按照上面提到的目录结构将这些插件添加到项目中。通过这种方式，Unity 能够与 Android 的 JAVA 和 iOS 的 Object-C 进行互操作。由于这个原因，我们在可以交互的技术方面获得了很大的灵活性。在我们的例子中，我们可以在 Unity 中嵌入适用于 Android 和 iOS 的 SQLite。

按照这种方法，我们最终拥有两个插件，每个平台一个，以及一个包装 Unity 应用程序，它与这些插件互操作，以公开我们需要的功能。

> *刚刚发生了什么:*
> 
> *简而言之，包装器是用 C#编写的，它满足了我们管理客户端事件缓存大小的第一个要求。插件负责我们在文章开头提到的其余需求。*

# 添加新目的地

最后，我们希望提供尽可能多的与 Unity 和移动开发相关的目的地。与 Unity 最相关的目的地是 Adjust 和 Firebase。对于这两者，我们支持所谓的设备模式 SDK。设备模式 SDK 允许将事件推送到目的地，而无需通过 RadderStack 服务器。

我们决定在 Unity 本身中构建[设备模式机制](https://github.com/rudderlabs/rudder-sdk-unity/blob/master/SDK/Rudder/RudderIntegrationManager.cs)，并使用平台的 Unity SDKs。通过这种方式，您可以使用平台支持的本机代码，这提供了更大的可靠性和长期支持。

# 结论

Unity 是一个功能丰富、用途广泛的游戏开发平台。我们希望为游戏开发者提供移动和服务器端开发者通常可以访问的数据功能。尽管在 Unity 中支持嵌入式数据库并不简单，但该平台允许你通过插件系统以一种优雅的方式做到这一点。
方向舵栈和所有的 SDK 都是开源的，可以在 [GitHub](https://github.com/rudderlabs) 上获得。你也可以在这里找到这个 Unity SDK [的详细文档。我们希望听到您对如何改进我们的 SDK 的反馈。](https://docs.rudderstack.com/sdk-integration-guide/getting-started-with-unity-sdk)