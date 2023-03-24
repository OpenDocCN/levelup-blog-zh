# RxPermissions:处理 Android M 权限的最简单方法

> 原文：<https://levelup.gitconnected.com/rxpermissions-the-easiest-way-to-handle-android-m-permissions-761fe8a6c3e9>

让我们看看如何用几行代码轻松处理权限流

![](img/5743d3ce56b64edb7e8ec448f1f031b1.png)

作为一种安全措施，从 Android Marshmallow 版本开始，就有一个访问敏感数据所需遵循的权限程序。*权限*的目的是保护用户的隐私。Android 应用程序必须请求许可才能访问敏感的用户数据，如个人媒体文件、相机访问、写入手机存储等。根据权限，在某些情况下，系统会自动授予权限，但在其他情况下，它可能会提示用户批准权限请求。这个权限特性已经将 Android 移动到了安全的下一个级别，但没有移动到顶级。

但是在开发阶段，请求和处理许可结果的过程有点复杂。开发人员通常会寻找能够在更短时间内简化工作的依赖项或库。所以这里我们将探索一个库 [RxPermissions](https://github.com/tbruyelle/RxPermissions) ，它在处理权限方面使事情变得更容易。这是我最喜欢的库之一，所以我认为它对其他人也有帮助。

# rx 权限

RxPermissions 是一个库，允许在 Android 棉花糖权限模型中使用 **RxJava** 。在深入本库之前，您需要对 Rx 有基本的了解。如果你没有任何知识，请阅读我的 Rx 系列，[Rx Java 完全指南](https://medium.com/better-programming/complete-guide-on-rxjava-d997235e4eec)，它将帮助你了解基本的 Rx 到中级。对于非 Rx 爱好者来说，很少有好的库来处理这些权限

[**Dexter**](https://github.com/Karumi/Dexter) :是一个 Android 库，简化了运行时请求权限的过程。

[**easy permissions**](https://github.com/googlesamples/easypermissions):easy permissions 是一个包装器库，用于在针对 Android M 或更高版本时简化基本的系统权限逻辑。

[**permissions dispatcher**](https://github.com/permissions-dispatcher/PermissionsDispatcher):permissions dispatcher 提供了一个简单的基于注释的 API 来处理运行时权限。

使用这些库的目的是保持我们的代码干净和安全。使用这些库，我们可以减少许多行样板代码。现在让我们开始深入 RxPermissions 库。

# 详细演练

为了更好地理解，让我们一步一步来。要使用此库，您的 SDK 最低版本必须> = 11。

## 步骤 1:添加依赖关系

在根目录或项目级 build.gradle 中

```
allprojects {
    repositories {
        ...
 **maven { url 'https://jitpack.io' }**    }
}
```

接下来在应用级 build.gradle 添加 Rx 权限依赖

```
dependencies {
    //Rx   
    implementation 'io.reactivex.rxjava2:rxjava:2.2.12'
    implementation 'io.reactivex.rxjava2:rxandroid:2.1.1' **implementation 'com.github.tbruyelle:rxpermissions:0.10.2'**}
```

## **第二步:I** 初始化 **RxPermissions**

在您想要请求权限的活动或片段中，首先用上下文初始化 RxPermission

```
// where this is an Activity or Fragment instance
 **val rxPermissions =  RxPermissions(this)**
```

> 注意:如果我们试图在一个片段中初始化它，我们应该传递片段实例 new RxPermissions( **this** )作为构造函数参数，而不是 new RxPermissions(片段。 **getActivity()** )否则我们可能会面临一个异常 Java . lang .**IllegalStateException**:fragment manager 已经在执行事务了。

## 步骤 3:请求许可的用法

现在我们可以使用上述步骤中初始化的实例来请求权限并处理结果。

这不是比使用额外的类和方法更简单吗？我们可以轻松处理结果。根据输出，我们可以做我们想要的动作，比如授予权限，做一些事情，点击从不显示对话框，进入设置页面，等等。这是一个示例实现查找。

## 步骤 4:请求一组权限

不仅请求一个权限很容易，我们还可以请求一组权限，并获得每个权限的单独结果或作为一个组的结果，如下所示

获取个人结果

获取分组结果

就这些了，希望你喜欢这篇文章。如果你觉得有用，请分享给你的朋友。

## 参考

[RxPermissions](https://github.com/tbruyelle/RxPermissions)

请让我知道你的建议和意见。

你可以在 [**中**](https://medium.com/@pavan.careers5208) 和 [**LinkedIn**](https://www.linkedin.com/in/satya-pavan-kumar-kantamani-61770a9b/) 上找到我…

感谢阅读…