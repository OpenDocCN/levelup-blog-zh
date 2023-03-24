# 反应本机-平面列表

> 原文：<https://levelup.gitconnected.com/react-native-flat-lists-f7cb089002b>

![](img/6634b54032190af6bdebe8be1916e8d8.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

React Native 是基于 React 的移动开发，我们可以用它来进行移动开发。在本文中，我们将看看如何使用它来创建一个带有 React Native 的应用程序。

# 平面列表

我们可以添加`FlatList`组件来显示一个简单的列表视图。

它是跨平台的，支持水平模式。此外，它还支持页眉、页脚和分隔符。我们可以拉出来刷新数据。我们可以一边滚动一边加载数据。它还支持多列。

例如，我们可以写:

```
import React from 'react';
import { SafeAreaView, FlatList, StyleSheet, View, Text } from 'react-native';
const DATA = Array(100).fill().map((_, i) => ({ id: i, title: i }))const Item = ({ title }) => (
  <View style={styles.item}>
    <Text style={styles.title}>{title}</Text>
  </View>
);const renderItem = ({ item }) => (
  <Item title={item.title} />
);export default function App() {
  return (
    <SafeAreaView style={styles.container}>
      <FlatList
        data={DATA}
        renderItem={renderItem}
        keyExtractor={item => item.id}
      />
    </SafeAreaView>
  );
}const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 0
  },
  item: {
    backgroundColor: '#f9c2ff',
    padding: 20,
    marginVertical: 8,
    marginHorizontal: 16,
  },
  title: {
    fontSize: 32,
  },
});
```

来创建我们的列表。

我们创建了`Item`组件，它为列表项呈现了`View`和`Text`组件。

`renderItem`功能是渲染一个`Item`的功能。

然后我们添加一个`SafeAreaView`和一个`FlatList`来呈现一个可滚动的列表。

我们在`data`属性中传递一个数组。

而`renderItem`道具有我们之前创建的`renderItem`函数。

`keyExtractor`是一个获取项目唯一键的函数。

我们可以通过使用`TouchableOpacity`组件来选择我们的项目:

```
import React from 'react';
import { SafeAreaView, FlatList, StyleSheet, Text, TouchableOpacity } from 'react-native';
const DATA = Array(100).fill().map((_, i) => ({ id: i, title: i }))const Item = ({ item: { title }, onPress, style }) => (
  <TouchableOpacity onPress={onPress} style={[styles.item, style]}>
    <Text style={styles.title}>{title}</Text>
  </TouchableOpacity>
);export default function App() {
  const [selectedId, setSelectedId] = React.useState(null);
  const renderItem = ({ item }) => {
    const backgroundColor = item.id === selectedId ? "lightgreen" : "pink";
    return (
      <Item
        item={item}
        onPress={() => setSelectedId(item.id)}
        style={{ backgroundColor }}
      />
    );
  };return (
    <SafeAreaView style={styles.container}>
      <FlatList
        data={DATA}
        renderItem={renderItem}
        keyExtractor={item => item.id}
        extraData={selectedId}
      />
    </SafeAreaView>
  );
}const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: 0
  },
  item: {
    backgroundColor: '#f9c2ff',
    padding: 20,
    marginVertical: 8,
    marginHorizontal: 16,
  },
  title: {
    fontSize: 32,
  },
});
```

`Item`组件与`TouchableOpacity`组件一起呈现，让我们在它被选中时显示一些不同的东西。

在`App`组件中，我们有`renderItem`函数，它接受`item`并根据哪个按钮被按下来设置背景颜色。

我们用`onPress` prop 确定哪个按钮被按下，这需要一个函数调用`setSelectedId`来设置所选项目的 ID。

然后在`style`道具中，我们根据`selectedId`是否与当前物品的`id`相同，将`backgroundColor`属性设置为我们想要的颜色。

现在，当一个项目被选中时，该项目的背景是浅绿色的。

否则，它有一个粉红色的背景。

# 结论

我们可以添加一个`FlatList`组件来显示一个可滚动的项目列表。