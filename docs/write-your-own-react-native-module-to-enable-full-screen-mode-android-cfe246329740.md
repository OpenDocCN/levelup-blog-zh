# 编写一个原生模块，在 React Native (Android)中启用全屏模式

> 原文：<https://levelup.gitconnected.com/write-your-own-react-native-module-to-enable-full-screen-mode-android-cfe246329740>

![](img/602a7777db09f1b358c2121e21df81a3.png)

图片提供:android docs

有些情况下，屏幕需要处于全屏模式，以最大限度地利用屏幕空间或避免用户意外退出应用程序。然而，React Native 没有内置的解决方案来进入或退出全屏模式，隐藏系统导航栏，或隐藏系统状态栏。

我见过一些开发人员使用一些类似 [react-native-immersive](https://github.com/mockingbot/react-native-immersive) 的模块来启用全屏沉浸式模式，但我认为这将是一个编写我的第一个原生模块并将其桥接到 React Native 的绝佳机会。

我建议仔细阅读 React Native docs 中提供的“Toast 示例”,以了解如何将平台 API 桥接到 JS。

现在让我们从如何启用全屏模式的 [android 文档开始。](https://developer.android.com/training/system-ui/immersive#EnableFullscreen)快速阅读后，我意识到它使用了`View`类中的`**setSystemUiVisibility()**` 以及系统 UI 标志，如`SYSTEM_UI_FLAG_LAYOUT_STABLE`、`SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION`、
、`SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN`、 `SYSTEM_UI_FLAG_HIDE_NAVIGATION`
`SYSTEM_UI_FLAG_FULLSCREEN`等。

让我们在`android/app/src/main/java/com/your-app-name/`中创建一个新文件`FullScreenModule.java`，并使用 android 文档中提到的函数`**setSystemUiVisibility()**` 。

FullScreenModule.java

下一步是使用`createNativeModules()`注册模块。在`android/app/src/main/java/com/your-app-name/`中创建一个文件`FullScreenPackage.java`，内容如下:

FullScreenPackage.java

现在将`FullScreenPackage`连接到`**MainApplication.java**`内的`getPackages()`:

```
// MainApplication.java
............import com.appname.FullScreenPackage; // add package Name............
protected List<ReactPackage> getPackages() {
  @SuppressWarnings("UnnecessaryLocalVariable")
  List<ReactPackage> packages = new PackageList(this).getPackages(); // ... add the next line
  packages.add(new FullScreenPackage());
  return packages;
}
```

最后一步是用 JavaScript 封装本机模块。创建一个名为 **FullScreen.js** 的新文件

```
// FullScreen.jsimport {NativeModules} from 'react-native';
module.exports = NativeModules.FullScreen;
```

现在您可以启用或禁用全屏:

```
import FullScreen from "../utils/FullScreen";FullScreen.enable();
FullScreen.disable();
```

点击查看 [repo 的工作演示。](https://github.com/neonsec/react-native-full-screen-demo)

[](https://gitconnected.com/learn/react-native) [## 学习 React Native -最佳 React Native 教程(2019) | gitconnected

### 十大 React Native 教程-免费学习 React Native。课程由开发者提交并投票…

gitconnected.com](https://gitconnected.com/learn/react-native)