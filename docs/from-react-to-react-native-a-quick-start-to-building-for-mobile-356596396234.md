# 从 React 到 React Native —构建移动设备的快速入门

> 原文：<https://levelup.gitconnected.com/from-react-to-react-native-a-quick-start-to-building-for-mobile-356596396234>

![](img/64311a3c3d879622d2ca8242212b5966.png)

保罗·花冈在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

就在几周前，我从我的编码训练营毕业，最终不知道下一步该做什么。我有很多关于用 React 构建更多项目、学习更多网页响应技术等等的想法。我通过遵循 YouTube 上关于媒体查询的一些指南，首先为移动设备设计，并在我的 CSS 中使用更灵活的值，快速处理了响应性网页。当我继续工作时，我发现我不一定希望人们在网页上使用我手机上的一些应用程序。我想让其中一些成为他们设备上的原生应用。

这时，我想起了我的一个同事提到要学习一点关于“反应本土”的知识。这是一个相对较新的平台，所以我可以很快“赶上”，并且不太担心专门为 Android 或 iOS 编程。这听起来不错，而且由于它是基于 React 的，我觉得这将是一个比学习 Swift、C++、Java 更容易的过渡…这个列表将会很长，并且在它们之间切换可能会很混乱！

React Native 从一般实践的角度来看待事情，并将其视为 web 开发，这样我就可以快速启动并运行事情了！在这篇文章中，我将谈论一些在切换到原生反应时相同和不同的事情。

# 首先设置一些东西

为了让所有这些工作，你需要先安装一些东西。我会去 [expo.io](http://expo.io) 网站，用他们的“开始”栏目开始。这将使用 Expo，这是一个为您处理一些(阅读:大量)React 原生设置的平台。

在这个设置中，我假设您已经安装了 Node.js。如果你没有，他们的网站上有下载链接！否则，在您的终端中运行以下命令:

```
npm install expo-cli --global
```

一旦安装完成，您就可以初始化您的第一个新项目了！它会问你你想如何初始化它，我建议用“空白”开始，因为它为你设置了很多，使你更容易开始应用程序。

```
expo init my-new-project-name
  //SELECT BLANK
cd my-new-project-name
expo start
```

运行最后一个“开始”命令将启动一个本地主机供您使用，并允许您使用手机扫描二维码和测试您的应用程序！也可以下载安卓和 iOS(仅限 Mac！！)模拟器，并在其上打开您的应用程序。

# 什么一样？

当进入一种新的语言或框架时，我总是试图找出我所来自的和我将要去的之间的相同之处。有了 React 和 React Native，就可以看出会有很多东西是一样的(毕竟 React Native 是基于 React 的)。

## 组织

React 的一般实践将是相同的。你可以(也应该！)为组件、容器、数据、常量等创建文件夹，并像在 React 应用程序中一样组织文件。利用这种组织风格可以使事情变得更容易处理，React 开发人员应该对此很熟悉。

## 功能组件和类组件

组件的设置方式是完全一样的！您可以设置如下所示的组件:

```
import React from ‘react’const Hello = props => {}
export default Hello
```

或者这个:

```
import React, { Component } from ‘react’export default class Hi extends Component{}
```

您会注意到，即使导入到组件中也是一样的。怎么样！即使在组件本身内部，事情看起来也应该很熟悉:

```
import . . . .const Items = props => { // You can use React Hooks let [ingredients, setIngredients] = useState([])

     //You can still have JavaScript functions inside const returnTitle = () => {

       //You can use props passed down into the component return props.title } return( //Code here . . . )
}
```

总的来说，JSX 作品之外的所有东西看起来应该和你过去的作品非常相似！这就是 React Native 对 React 开发人员来说如此之好的原因。

# 有什么不同？

虽然从 React 到 React Native 有很多相同的东西，但在支持 iOS 和 Android 的过程中有一些东西发生了变化。在这篇文章中，我将只介绍一些小的基本的变化，将来会介绍一些更深入的变化。

## 我的 HTML 呢？

不幸的是，移动设备本身不支持 HTML。这意味着我们不能在应用程序中使用像

这样的东西！相反，对于 android，我们需要使用 android.view 之类的东西，而在 iOS 上，我们需要使用 UIView 之类的东西。但是，React Native 太好了，给了我们一个覆盖两者的<view>标签！</view>

说到这里，我们又回到了熟悉的话题上。反应本地风格它的组件和标签就像你习惯使用的 JSX。例如，我们经常会看到这样的东西:

```
import { View } from 'react-native'...<View> // some code here</View>
```

这些组件甚至使用类似的 React 设置进行样式和侦听器设置:

```
import { View } from 'react-native'...<View style={{backgroundColor: ‘blue’}} onPress={someFunction}> // some code</View>
```

就像我上面简单提到的，React Native 中的<view>和 React 中的</view>

非常相似。两者都是块元素，旨在设计它们内部的样式。以下是更多的对比:

## 文本

在 React 中，页面上可以有文本。你所要做的就是打字，你会看到东西显示出来。在 React Native 中，您需要更有意识地将文本包装在一个<text>组件中。<text>也是一个块元素，但是我们将在后面讨论如何修复它。</text></text>

```
import { Text } from 'react-native'...<Text>Hello, World!</Text>
```

## 投入

在 React 中，您可以使用标签为用户输入指定区域并设置样式。在 React Native 中，实际上在功能和执行上非常相似。您将使用<textinput>，它可以有样式、侦听器、值和占位符。基本上，你可以在标签上做的任何事情，你都可以在<textinput>上做。</textinput></textinput>

```
import { TextInput } from 'react-native'...
<TextInput
   placeholder="Waiting . . ."
   style={{backgroundColor: 'green'}}
   onChangeText={handleSomeChanges}
   value={enteredText}
 />
```

## 小跟班

按钮如此接近相同，几乎令人讨厌。在 React 中，标签之间可以有文本，它将是显示的文本，但是 React Native 中的按钮是自动关闭的。不然还算差不多，注意下面的一些小变化。

```
import { Button } from 'react-native'...<Button
  title="Press Me"
  onPress={handleSomePressEvent}
  disabled={false}
/>
```

关于按钮的额外挑战是它们不能自己设计样式。您可以尝试附加所有您想要的样式，但不幸的是，您的应用程序不会发生任何变化。但是有一个变通办法！相反，您可以将按钮包装在一个<view>标签中，并将样式应用于该标签。通常，你会发现人们最终会为按钮定制 React 组件，这样他们就可以像在 React 中那样使用它们。例如:</view>

```
...<View style={styles.buttonContainer}> <ButtonComponent onPress={props.handleOnPress} activeOpacity= 
    {0.8}> <View style={{...styles.button, width: buttonWidth, 
         ...props.style}}> <Text style={{...styles.buttonText, fontSize: 
                 buttonFontSize}}> {props.children} </Text> </View> </ButtonComponent></View>
```

# 我的样式表在哪里？

在 React Native 中，你不会发现自己在制作“App.css”文件并将它们导入到你的应用程序中。相反，您会发现自己正在使用 React 本机样式表调用来为每个页面创建样式。它使任何给定页面中的内容更易于管理，并且总体上使您的样式更易于阅读。您将经常看到您的组件如下所示:

```
import React from ‘react’import { View, Text, StyleSheet } from ‘react-native’const HeyThere = props => { return(
     <View style={styles.screen}>
        <Text style={styles.mainText}>Hey there!</Text>
     </View>
  )
}const styles = StyleSheet.create({
   screen: {
      flex: 1,
      backgroundColor: '#ccc',
      margin: 5,
      justiftyContent: 'center',
      alignItems: 'center
  },
   mainText: {
      color: white,
      fontSize: 22
   }
})export default HeyThere
```

当查看上面的代码时，您可以看到事情分成几个不同的部分。在我们代码的底部，你可以看到我们有一个名为“styles”的常量(可以是你想要的任何名字。实际上，如果使用不止一个样式表，我建议用组件中的某个部分来命名它)。该常量被设置为 StyleSheet.create({})，我们从“react-native”导入它，然后将它存储为一个引用库，引用我们希望赋予任何组件的样式。

您可以看到样式表中的代码非常接近 CSS。单词改为大小写，而不是使用连字符样式。

最后，您可以查看返回值中的组件，这些组件使用我们的 styles 常量并获取一组特定的样式来设置我们的组件。

# 包裹

在《React Native》中还有很多值得讨论的内容，比如很多！但是，即使没有这些，您也应该能够开始编写一些应用程序了！让我知道你已经开始拼凑的一些东西是什么样子的！我敢肯定，这一切都会变成一些相当惊人的东西。

祝你好运，编码快乐！