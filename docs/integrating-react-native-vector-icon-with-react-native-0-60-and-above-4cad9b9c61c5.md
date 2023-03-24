# 将 React Native Vector 图标与 React Native 0.60 及以上版本集成

> 原文：<https://levelup.gitconnected.com/integrating-react-native-vector-icon-with-react-native-0-60-and-above-4cad9b9c61c5>

最近，当我试图将 [React Native 矢量图标](https://github.com/oblador/react-native-vector-icons)与 React Native 的最新版本(≥0.60)集成时，我面临了很多问题。由于我正在运行一些过时的命令，我的构建开始崩溃。

![](img/04516ddf080a61bcf9edd059809b241a.png)

[Pinterest](https://in.pinterest.com/pin/580260733215490374/)

经过一些麻烦，我能够成功地配置它，我写这篇文章是为了给我的开发伙伴节省一些时间。按照步骤在 iOS 和 Android 上集成 [React 原生矢量图标](https://github.com/oblador/react-native-vector-icons)。

1.  `yarn add react-native-vector-icons`
2.  如果您使用的是 typescript，运行`yarn add @types/react-native-vector-icons`
3.  **请不要**跑`react-native link react-native-vector-icons`这就是我错的地方。
4.  运行`npx pod-install`
5.  转到`YourProject/android/app/build.gradle`和**在**
    `apply from: "../../node_modules/react-native/react.gradle"`正上方添加
    `apply from: "../../node_modules/react-native-vector-icons/fonts.gradle"`

6.  **请确保将它添加到`apply from: "../../node_modules/react-native/react.gradle"`的正上方，否则您将无法构建 Android 应用程序。**
7.  **现在，转到`ProjectRoots/App.js`或`ProjectRoots/index.js`并导入你通常会导入的字体系列，如`import Ionicons from 'react-native-vector-icons/Ionicons';`并运行`Ionicons.loadFont();`**
8.  **在你开始使用它之前，你必须为你将要使用的所有字体做上面的事情。记住，在根文件中运行`Ionicons.loadFont()`。字体应该在应用程序启动前加载。所以，越早装越好。**
9.  **如果您想使用' FontAwesome5 ',请确保执行以下操作:
    `FontAwesome5.getStyledIconSet('brand').loadFont();
    FontAwesome5.getStyledIconSet('light').loadFont();
    FontAwesome5.getStyledIconSet('regular').loadFont();
    FontAwesome5.getStyledIconSet('solid').loadFont();`
    *参考:*[https://github . com/ob ador/react-native-vector-icons/issues/1095 # issue comment-615929458](https://github.com/oblador/react-native-vector-icons/issues/1095#issuecomment-615929458)**
10.  **瞧啊。**

**希望这有帮助:)**