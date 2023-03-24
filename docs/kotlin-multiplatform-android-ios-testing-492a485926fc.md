# Kotlin 多平台 Android/iOS:测试

> 原文：<https://levelup.gitconnected.com/kotlin-multiplatform-android-ios-testing-492a485926fc>

![](img/6b370fdf05e1bc86f4426f69378baccc.png)

## 如何在 Kotlin 多平台中编写测试

# 测试

代码质量非常重要，测试是代码质量的巨大组成部分。您希望确保您编写的代码按预期运行。它还需要在各种条件下工作。

我使用当时给出的[方法来测试我们所有的](https://www.brightec.co.uk/ideas/given-when-then-our-testing-approach)[小型、中型和大型测试](https://testing.googleblog.com/2010/12/test-sizes.html)。这是一种很有用的组织测试的方法。我也依赖于测试中的嘲笑。我想确保我测试的唯一东西是我想要的类。

# 科特林多平台

在 [Kotlin 多平台](https://kotlinlang.org/docs/reference/multiplatform.html)中进行测试得到了很好的支持，感觉非常熟悉。

```
implementation "org.jetbrains.kotlin:kotlin-test-common:$kotlin_version"
implementation "org.jetbrains.kotlin:kotlin-test-annotations-common:$kotlin_version"
```

Kotlin 提供了一些常见的测试依赖，使您能够编写多平台测试。这意味着您只需编写一次测试，而不是编写特定于平台的测试。

# 例子

考虑这个简单的存储库:

我们有两个方法，都提供了获取`User`的方法。一个提供挂起功能，一个提供回调方法。回调返回一个`IOResult`，它封装了成功和错误状态。我的安卓应用会用挂起功能，我的 iOS 应用会用回调功能。iOS 尚不支持暂停功能。

所以我们希望能够编写 4 个测试:

*   `remoteFailure__getUser__error`
*   `remoteSuccess__getUser__success`
*   `callback_remoteFailure__getUser__error`
*   `callback_remoteSuccess__getUser__success`

遵循`given__when__then`的命名惯例。这些将封装这些方法的所有可能的场景。

## 嘲弄的

我们将需要能够嘲笑我们的`UserRemote`。目前在 Android 和 iOS 中都没有模仿类的框架。[mock](https://mockk.io/)正在向支持 JS 和 Native (iOS)的[前进，但尚未完成。](https://opencollective.com/mockk#section-about)

因此，我们必须手动创建我们自己的模拟。iOS 开发人员可能对这种方法有些熟悉。

我们创建了一个实现`UserRemote`接口的类。它通过提供函数`var`来提供定义`getUser`行为的能力。因此，在测试中，我们可以设置`whenGetUser`并提供一些行为。

## 协同程序

```
implementation "org.jetbrains.kotlinx:kotlinx-coroutines-test:$coroutines_version"
```

为了测试一个挂起函数，我们需要添加协程测试依赖。

添加这个 [expect 和 actual](https://gist.github.com/alistairsykes/a6910faf17021a23d77bdccdbf8d7f70) 实用程序使得运行我们常见的测试暂停功能变得容易。

## 复试

为了能够断言我们的回调结果，我们需要能够模拟它们。

这个模拟捕获调用回调时使用的参数。

## 试验

使用这些组件，我们现在能够编写我们的测试。

为了创建一个对`User`的模仿，我们可以写一点扩展方法:

# 摘要

Kotlin 多平台通用测试代码编写起来可能有点麻烦。尤其是对于使用像 [Mockito](https://site.mockito.org/) 这样的框架的 Android 开发者来说。但是一般测试的能力是很强大的。这将减少您的总体测试工作量。

*上一篇文章*

[](https://medium.com/@alistairsykes/kotlin-multiplatform-android-ios-project-structure-strategies-b262eec30e1a) [## Kotlin 多平台 Android/iOS:项目结构策略

### 您应该如何构建您的多平台项目？

medium.com](https://medium.com/@alistairsykes/kotlin-multiplatform-android-ios-project-structure-strategies-b262eec30e1a) 

*原载于*[*https://www.brightec.co.uk*](https://www.brightec.co.uk/ideas/kotlin-multiplatform-androidios-testing)*。*