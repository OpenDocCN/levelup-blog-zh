# 反应本机— Flexbox 布局

> 原文：<https://levelup.gitconnected.com/react-native-flexbox-layout-95375fa7c79d>

![](img/39442e2e6ab3abc07d5487316b52de5d.png)

由 [Karthik Thoguluva](https://unsplash.com/@karthikhimself?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将看看如何使用它来创建一个带有 React Native 的应用程序。

# 使用 Flexbox 的布局

我们可以将 flexbox 用于 React Native 布局。它的工作方式和 CSS 一样。

# 弯曲

例如，我们可以写:

```
import React from 'react';
import { View } from 'react-native';export default function App() {
  return (
    <View style={{ flex: 1, flexDirection: 'column' }}>
      <View style={{ width: 50, height: 50, backgroundColor: 'powderblue' }} />
      <View style={{ width: 50, height: 50, backgroundColor: 'skyblue' }} />
      <View style={{ width: 50, height: 50, backgroundColor: 'steelblue' }} />
    </View>
  );
}
```

我们通过在外部视图上将`flexDirection`设置为`'column'`来创建一个列布局。然后内部视图有自己的维度集。

# 布局方向

布局的默认方向是从左到右。也可以设置为从右向左。

# 对齐内容

我们可以设置`justifyContent`属性，以我们想要的方式传播内容。

例如，我们可以写:

```
import React from 'react';
import { View } from 'react-native';export default function App() {
  return (
    <View style={{
      flex: 1,
      flexDirection: 'column',
      justifyContent: 'space-between',
    }}>
      <View style={{ width: 50, height: 50, backgroundColor: 'powderblue' }} />
      <View style={{ width: 50, height: 50, backgroundColor: 'skyblue' }} />
      <View style={{ width: 50, height: 50, backgroundColor: 'steelblue' }} />
    </View>
  );
}
```

我们将`flexDirection`设置为`'column'`，将`justifyContent`设置为`'space-between'`。

因此，内部视图将在一列中均匀分布。

# 对齐项目

我们可以设置`alignItems`属性来对齐项目。

例如，我们可以写:

```
import React from 'react';
import { View } from 'react-native';export default function App() {
  return (
    <View style={{
      flex: 1,
      flexDirection: 'column',
      justifyContent: 'center',
      alignItems: 'stretch',
    }}>
      <View style={{ height: 50, backgroundColor: 'powderblue' }} />
      <View style={{ height: 50, backgroundColor: 'skyblue' }} />
      <View style={{ height: 50, backgroundColor: 'steelblue' }} />
    </View>
  );
}
```

我们将`alignItems`设置为`'stretch'`，因此内部视图将延伸到整个屏幕。

`alignItems`的其他值可以是`'flex-start'`、`'center'`或`'flex-end'`。

# 自我对齐

React Native 也提供了`alignSelf`属性。

例如，我们可以写:

```
import React from 'react';
import { View } from 'react-native';export default function App() {
  return (
    <View style={{
      flex: 1,
      flexDirection: 'column',
      justifyContent: 'center',
    }}>
      <View style={{ width: 50, height: 50, backgroundColor: 'powderblue', alignSelf: 'flex-end' }} />
      <View style={{ width: 50, height: 50, backgroundColor: 'skyblue' }} />
      <View style={{ width: 50, height: 50, backgroundColor: 'steelblue' }} />
    </View>
  );
}
```

将`alignSelf`属性添加到我们的第一个内部视图。

它被设置为`'flex-end'`，所以它将被放在右侧。

# 结论

我们可以使用许多 flexbox 属性来创建 React Native 布局。