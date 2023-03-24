# 干净的代码:Android 模块和依赖注入(专长:Koin)

> 原文：<https://levelup.gitconnected.com/android-module-setup-in-3-steps-using-dependency-injection-with-koin-b7920ec8a800>

## 一个干净的分离和亲切的协议的故事

模块化的应用程序设计对于开发人员的理智、应用程序的可伸缩性和代码质量至关重要。

> 设计是分离、组合、抽象和隐藏的艺术。—鲍伯·马丁叔叔

当每个模块可能都需要知道`Application`类是何时创建的时候，我如何才能享受一个由适当的分离和抽象(使用模块)组成的 Android 应用程序呢？

*(设置模块的几个用例是:初始化分析库，当应用程序语言改变时，观察* `*LocaleLiveData*` *以同步建议，或者解包捆绑的数据并插入到本地数据库中。)*

很棒的问题！为了解释，让我们听听我们的好朋友，`Application`:

*快速提示:虽然我们在这里将使用 Koin 作为我们的 DI 库，但是下面的设计模式和过程也适用于 Dagger 2(以及大多数(如果不是全部的话)其他 DI 库)。*

> 嗨，我是[申请](https://developer.android.com/reference/android/app/Application)！
> 
> 我住在一个叫`app`的模块里。我由许多不同的[模块](https://medium.com/google-developer-experts/modularizing-android-applications-9e2d18f244a0)组成，因为我喜欢[干净](https://medium.com/mindorks/understanding-clean-code-in-android-ebe42ad89a99)！我让每个模块做自己的事情，我不担心它们的实现细节——老实说，我更关注大局…
> 
> 但是模块需要我...尤其是当我第一次被创造的时候。幸运的是，我们已经就如何相互交流达成了一致！`base`模块称之为:`ModuleSetup` `interface`。我需要做的就是向我们的朋友`[Koin](https://insert-koin.io/)`请求`ModuleSetup`的所有实例，并对每个实例调用`setup`方法！我让单个模块做告诉`Koin`如何提供这些实现的具体工作。小菜一碟*🍰*。
> 
> 我们互不干涉，但仍然保持着非常友好的关系🙂。
> 
> 鳍状物

对于那些喜欢列表而不是来自无生命物体的故事的人来说；遵循以下三个简单的步骤来设置您的 Android 应用程序的模块:

1.  用一个`setup(app: Application)`方法创建一个共享的`ModuleSetup`T1
2.  提供来自每个模块的`ModuleSetup`实现，并将它们`bind`到`interface`
3.  在`Application` `onCreate`中，使用`Koin`的`getAll<ModuleSetup>`获取所有实现，并使用`Application`上下文对每个实现调用`setup`

![](img/86fa0f382803a0346b75068dd7281a32.png)

照片由[pix poeties](https://unsplash.com/@blackpoetry?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

对于以下解释，了解隐含的应用程序结构将很有帮助:

```
-- app
-- modules
   -- base
   -- ...
   -- ...
   -- super-cool
```

`modules`对`app`一无所知。`base`模块将包含所有其他模块可用的代码，包括`app`模块。这是我们放置`ModuleSetup` `interface`的地方。

# 创建一个`ModuleSetup` `interface`

我们的应用程序中经常有需要初始化的代码。这意味着我们需要在创建`Application`类时提供回调的能力。另外，初始化代码通常需要一个`Context`来运行。

为了满足这两个需求，在编写干净代码的同时，我们需要为一个类创建一个契约，该类包含一个将`Application`上下文作为参数的方法。这将允许`Koin`的`getAll<T>`方法返回我们契约的所有实现，因此`Application`可以相应地与它们交互。这个契约需要对`app`模块和所有其他模块都可用，所以我们将它放在我们的`base`模块中。

我们的合同将是这样的:

```
package appname.setupinterface *ModuleSetup* {
    fun setup(app: Application)
}
```

现在，任何需要在`Application`创建上设置的模块都可以实现这个`interface`并为其提供`Koin`。

# 实现、提供并绑定您的`ModuleSetup`实现

于是，`base`为我们创造了一个`interface`。让我们在我们的`super-cool`模块中实现它，并运行一些惊人的初始化代码。

```
package appname.modules.super.cool.setupimport appname.setup.ModuleSetup
import ... class ModuleSetupImpl constructor(private val repo: SuperCoolRepository, private val locale: LocaleLiveData) : *ModuleSetup* { @MainThread
    override fun setup(app: Application) {

        // warning: do not run blocking code on the main thread
        locale.observeForever {
            // runs task in background
            repo.syncRecommendations(it.languageTag)
        } }
}
```

如果需要，我们可以使用`Koin`将依赖注入到我们的实现中；注意`SuperCoolRepository`和`LocaleLiveData`参数。

在这个设置示例中，我们注入了一个 LiveData，其中包含我们的应用程序的区域设置和一个存储库，该存储库将根据区域设置为我们构建一些内容推荐。这种设置允许我们保持我们的内容与用户的语言同步，无论它何时何地发生变化。

接下来，我们在我们的`Koin` [模块](https://doc.insert-koin.io/#/koin-core/modules)中提供实现。

```
package appname.modules.super.cool.di.modulesimport ...val superCoolModule= module{ ...
    single { SuperCoolRepository() }
    single { ModuleSetupImpl(get(), get()) } bind ModuleSetup::class
    ...}
```

这里的关键是`Koin`的`[bind](https://doc.insert-koin.io/#/koin-core/definitions?id=additional-type-binding)`操作符。这允许我们将我们的实现绑定到`ModuleSetup` `interface`。现在，我们可以进入最后一步了。

# 向`Koin`索要`ModuleSetup`职业和收益！

我们已经把所有东西连在一起了！最后要做的事情是向`Koin`请求所有的`ModuleSetup`类，并对每个类调用`setup`方法。

```
class ModularApplication : Application(), *KoinComponent* {

    override fun onCreate() {
        super.onCreate()
 *startKoin* {
            *androidLogger*()
            *androidContext*(this@ModularApplication)
            modules(
                *appModule,
                baseModule,
                …,
                superCoolModule* )
        }

        // Set up our modules
        getKoin().getAll<*ModuleSetup*>().*forEach* {
            it.setup(this)
        }
    }

}
```

这里需要指出几件事:

1.  我们照常启动`Koin`并加载我们的模块，包括`superCoolModule`
2.  我们实现了`KoinComponent` `interface`以便访问`Koin`
3.  我们按照`KoinComponent`提供的`getKoin()`调用`getAll<ModuleSetup>`方法(在主线程上调用*，以便循环通过并设置各个模块*

就是这样！

*在我们离开之前有一个重要的注意事项:当* `*Application*` *被创建时，* `*setup*` *方法中的所有代码都将在主线程上运行。这意味着我们必须不惜一切代价避免在设置方法中阻塞代码，除非我们首先将工作卸载到后台线程。我们在主线程上运行的阻塞代码越多，应用程序启动的时间就越长。在更极端的情况下，运行阻塞代码(磁盘 I/O、网络等。)，会导致 app 崩溃。*

我们现在已经实现了一种方法，将我们的代码分成模块，同时保持在`Application`创建时运行重要代码的能力，所有这一切只需三个简单的步骤:

1.  创建我们的安装合同(`interface`
2.  实现并提供合同的所有实例
3.  在`Application`T5 检索合同实现并与之交互

现在，享受你的干净和模块化的 Android 应用程序吧！

![](img/e8e3cae1ce0885aabd3fee417500d2c0.png)

劳伦·曼克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片