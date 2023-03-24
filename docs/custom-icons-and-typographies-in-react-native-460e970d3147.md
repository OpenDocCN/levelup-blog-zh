# React Native 中的自定义图标和版式

> 原文：<https://levelup.gitconnected.com/custom-icons-and-typographies-in-react-native-460e970d3147>

![](img/140e6d3c453f57ca955527bb31e96a62.png)

以下是在 React Native 中使用自定义图标和字体的一些注意事项。不要忘记检查 [React 原生草图元素](https://react-native.shop/elements)，最全面的 React 原生初学者工具包。

[](https://hackernoon.com/react-native-sketch-elements-889f010f9626) [## 反应原生草图元素

### 经过两个月的制作，React 本地草图元素终于出现了。反应原生元素涵盖了广泛的…

hackernoon.com](https://hackernoon.com/react-native-sketch-elements-889f010f9626) 

# Expo 自定义图标库

我最近决定将图标字体从 React Sketch Elements 升级到它原来的图标库。该过程需要将 Sketch 中的图标切片导出为 SVG 文件，使用 [icomoon](https://icomoon.io/) 创建字体文件，最后使用 [Expo](https://docs.expo.io/versions/latest/guides/icons) 导入该字体文件。除此之外，我们将使用 Flow 在项目中强制使用正确的图标名称。这是一个手动过程，但是您可以使用[这个来自路易斯·马丁斯的精彩故事](https://medium.com/@lmartins/building-icon-fonts-with-grunt-4e22107d7f97)来完全自动化这个工作流程。

一旦你有了 icomoon 的字体文件，你需要导入它，以及它的 icomoon 规范文件。我们将使用`createIconSetFromIcoMoon()`来创建`<Icon>`组件，使用`Font.loadAsyc()`来加载字体文件。

```
// To load the font
import {Font} from "expo";// To create the <Icon> component
import {createIconSetFromIcoMoon} from "[@expo/vector-icons](http://twitter.com/expo/vector-icons)";import Icons from "./icons.ttf";
import config from "./config.json";export const loadIconFont = () => Font.loadAsync({ Icons });
const Icon = createIconSetFromIcoMoon(config, "Icons");// Type Safety First!
export type IconName = "arrow-up" | "arrow-right" | "...";
```

我使用了一小段 JavaScript 来生成如下所示的`IconName`类型。

```
const config = require("./config");console.log(
  config.icons.map(icon => `"${icon.properties.name}"`).join(" | ")
);
```

# 自定义排版

你可能已经注意到 iOS `<Text>`有一个不可约函数，使得文本的垂直对齐非常困难。对于旧金山字体，这种填充是平衡的，因此您不会注意到这个问题。但是用其他字体会显得很明显。如果你正在寻找解决这个问题的方法，请查看马丁·亚当科的这个[精彩故事。我最终使用他的提示创建了一个自定义字体文件，该文件仅用于可以轻松垂直对齐的单行文本。对于其余部分，我使用应用了`lineHeight`属性的原始字体文件。谢谢马丁！](https://medium.com/@martin_adamko/consistent-font-line-height-rendering-42068cc2957d)

```
import MyriadProRegular from "./fonts/MyriadPro-Regular.otf";
import MyriadProRegularSingleLine from "./fonts/MyriadPro-Regular-SingleLine.otf";render(): React.Node {
    const {type, numberOfLines, children} = this.props;
    const typographyStyle = styleGuide.typography[type];
    if (numberOfLines === 1) {
      style.push({ fontFamily: 'MyriadProRegularSingleLine' });
    }
    return (
      <Text {...{ style, numberOfLines }}>
        {children}
      </Text>
    );
  }
```

# 那都是乡亲们！

希望你喜欢这个故事。请让我知道你对这个话题的想法。