# 反应自然—图像、视图和文本

> 原文：<https://levelup.gitconnected.com/react-native-images-views-and-text-1b883ec01c7b>

![](img/f03c75083290f6813aeac40ff5de2f64.png)

丹尼尔·姆托姆博索拉在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将看看如何使用它来创建一个带有 React 的应用程序。

# Uri 数据图像

我们可以为 base64 URI 添加图像。

例如，我们可以写:

```
import { StatusBar } from 'expo-status-bar';
import React from 'react';
import { Image, StyleSheet, View } from 'react-native';export default function App() {
  return (
    <View style={styles.container}>
      <Image
        style={{
          width: 51,
          height: 51,
          resizeMode: 'contain'
        }}
        source={{
          uri:
            'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADMAAAAzCAYAAAA6oTAqAAAAEXRFWHRTb2Z0d2FyZQBwbmdjcnVzaEB1SfMAAABQSURBVGje7dSxCQBACARB+2/ab8BEeQNhFi6WSYzYLYudDQYGBgYGBgYGBgYGBgYGBgZmcvDqYGBgmhivGQYGBgYGBgYGBgYGBgYGBgbmQw+P/eMrC5UTVAAAAABJRU5ErkJggg=='
        }}
      />
      <StatusBar style="auto" />
    </View>
  );
}const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```

我们只是设置了`uri`属性来设置图像 URI。

我们应该只对小图像使用这个。

# 背景图像

例如，我们可以写:

```
import { StatusBar } from 'expo-status-bar';
import React from 'react';
import { ImageBackground, StyleSheet, View, Text } from 'react-native';export default function App() {
  return (
    <View style={styles.container}>
      <ImageBackground source={require('./assets/fork.jpg')} style={{ width: '100%', height: '100%' }}>
        <Text>Inside</Text>
      </ImageBackground>
      <StatusBar style="auto" />
    </View>
  );
}const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```

我们使用了`ImageBackground`组件来添加背景图像。

然后我们可以在里面放一些像文本这样的东西。

# 视角

一个`View`是一个支持 flexbox 布局、样式、触摸处理和可访问性控件的容器。

例如，我们可以写:

```
import React from 'react';
import { View, Text } from 'react-native';export default function App() {
  return (
    <View
      style={{
        flexDirection: "row",
        height: 100,
        padding: 20
      }}
    >
      <View style={{ backgroundColor: "green", flex: 0.3 }} />
      <View style={{ backgroundColor: "red", flex: 0.5 }} />
      <Text>Hello World!</Text>
    </View>
  );
}
```

外部`View`组件是内部`View`组件的 flexbox 容器。

我们可以为视图设置`backgroundColor`。

我们可以添加`Text`组件来添加文本。

# 文本

`Text`组件是一个用于显示文本的组件。

例如，我们可以写:

```
import React, { useState } from 'react';
import { View, Text, StyleSheet } from 'react-native';const onPressTitle = () => {
  console.log("title pressed");
};export default function App() {
  const titleText = useState("Bird's Nest");
  const bodyText = useState("This is not really a bird nest."); return (
    <View
      style={{
        flexDirection: "row",
        height: 100,
        padding: 20
      }}
    >
      <Text style={styles.baseText}>
        <Text style={styles.titleText} onPress={onPressTitle}>
          {titleText}
          {"\n"}
        </Text>
        <Text numberOfLines={5}>{bodyText}</Text>
      </Text>
    </View>
  );
}const styles = StyleSheet.create({
  baseText: {
    fontFamily: "Cochin"
  },
  titleText: {
    fontSize: 20,
    fontWeight: "bold"
  }
});
```

我们可以添加`Text`组件来添加一些文本。

我们添加了`onPress` prop 并传入了一个事件处理程序来添加一个触摸文本的事件处理。

# 结论

我们可以用 React Native 为图像和背景图像添加 URIs。

此外，我们可以为容器添加`View`组件，为文本添加`Text`组件。