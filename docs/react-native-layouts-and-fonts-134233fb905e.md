# 原生反应—布局和字体

> 原文：<https://levelup.gitconnected.com/react-native-layouts-and-fonts-134233fb905e>

![](img/b98215c97491e39ad474f3fc718433ab.png)

由 [Neven Krcmarek](https://unsplash.com/@nevenkrcmarek?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

在本文中，我们将看看如何使用它来创建一个带有 React Native 的应用程序。

# 布局道具

我们可以用一些道具来控制我们的应用程序的布局。

例如，我们可以写:

```
import React from 'react';
import { View } from "react-native";const randomHexColor = () => {
  return '#000000'.replace(/0/g, () => {
    return (~~(Math.random() * 16)).toString(16);
  });
};const Square = () => {
  const sqStyle = {
    width: 50,
    height: 50,
    backgroundColor: randomHexColor(),
  };
  return <View style={sqStyle} />;
};export default function App() {
  return (
    <View style={{
      flexDirection: 'row',
      justifyContent: 'flex-start',
      alignItems: 'flex-start'
    }}>
      <Square />
      <Square />
      <Square />
    </View>
  );
}
```

我们添加了一些带有`Square`组件的方块。

然后我们用一些 flexbox 属性设置`View`的样式。

我们可以使用所有的 flexbox 属性，如`flexDirection`、`justifyContent`、`alignItems`等。

我们可以把它们设置成我们想要的值。

# 影子道具

我们可以通过设置`shadowOffset`属性在容器上设置阴影。

例如，我们可以写:

```
import React from 'react';
import { View, StyleSheet } from "react-native";
const shadowOffsetWidth = 20;
const shadowOffsetHeight = 20;
const shadowOpacity = 0.5;
const shadowRadius = 10;export default function App() {
  return (
    <View style={styles.container}>
      <View style={[
        styles.square,
        {
          shadowOffset: {
            width: shadowOffsetWidth,
            height: -shadowOffsetHeight
          },
          shadowOpacity,
          shadowRadius
        }
      ]}>
      </View>
    </View>
  );
}const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "space-around",
    backgroundColor: "#ecf0f1",
    padding: 8
  },
  square: {
    alignSelf: "center",
    backgroundColor: "white",
    borderRadius: 4,
    height: 150,
    shadowColor: "black",
    width: 150
  },
  controls: {
    paddingHorizontal: 12
  }
});
```

我们在`shadowOffset`属性中设置阴影偏移的宽度和高度。

同样，我们用`shadowOpacity`属性设置阴影的不透明度。

我们用`shadowRadius`属性设置阴影的半径。

# 文本样式道具

我们可以为文本设置样式。

例如，我们可以写:

```
import React from 'react';
import { View, StyleSheet, Text } from "react-native";
const fontStyles = ["normal", "italic"];
const fontVariants = [
  undefined,
  "small-caps",
  "oldstyle-nums",
  "lining-nums",
  "tabular-nums",
  "proportional-nums"
];
const fontWeights = [
  "normal",
  "bold",
  "100",
  "200",
  "300",
  "400",
  "500",
  "600",
  "700",
  "800",
  "900"
];
const textAlignments = ["auto", "left", "right", "center", "justify"];
const textDecorationLines = [
  "none",
  "underline",
  "line-through",
  "underline line-through"
];
const textDecorationStyles = ["solid", "double", "dotted", "dashed"];
const textTransformations = ["none", "uppercase", "lowercase", "capitalize"];
const textAlignmentsVertical = ["auto", "top", "bottom", "center"];
const writingDirections = ["auto", "ltr", "rtl"];export default function App() {
  return (
    <View style={styles.container}>
      <Text
        style={[
          styles.paragraph,
          {
            fontSize: 20,
            fontStyle: fontStyles[0],
            fontWeight: fontWeights[0],
            lineHeight: 200,
            textAlign: textAlignments[0],
            textDecorationLine: textDecorationLines[0],
            textTransform: textTransformations[0],
            textAlignVertical: textAlignmentsVertical[0],
            fontVariant: fontVariants[0],
            letterSpacing: 5,
            includeFontPadding: true,
            textDecorationStyle: textDecorationStyles[0],
            writingDirection: writingDirections[0],
            textShadowOffset: {
              height: 10,
              width: 10
            },
            textShadowRadius: 5
          }
        ]}
      >
        Lorem Ipsum
      </Text>
    </View>
  );
}const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "space-around",
    backgroundColor: "#ecf0f1",
    padding: 8
  },
  paragraph: {
    color: "black",
    textDecorationColor: "yellow",
    textShadowColor: "red",
    textShadowRadius: 1,
    margin: 24
  }
});
```

为我们的文本设置所有样式。

`fontSize`设置字体大小。

`fontStyle`设置字体样式。

`lineHeight`设置行高。

`textAlign`设置文本对齐。

`textDecorationLine`设置文本样式。

`textTransform`让我们转换文本。

`fontVariant`设置字体变量。

`letterSpacing`设置字母间距。

`writingDirection`设置文本的方向。

`textShadowOffset`设置阴影的位置。

`textShadowRadius`设置阴影的半径。

# 结论

我们可以为字体和布局设置许多样式。