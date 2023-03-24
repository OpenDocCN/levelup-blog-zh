# 如何在素材 UI 中使用 CSS 断点

> 原文：<https://levelup.gitconnected.com/how-to-use-css-breakpoints-in-material-ui-1781e07afc77>

![](img/ed62dd4a3226777e86c43c547036ac62.png)

在网页设计领域，CSS 断点可以帮助我们设计一个更强大、响应更快的网站，检测何时显示和隐藏某些元素，调整组件大小以适应移动设备，或在移动设备上扩展，实现流畅的用户体验。

在这篇文章中，我们将讨论如何在材质界面中使用 CSS 断点。

如果你只想深入研究代码，这是我们将使用的 Github repo 的例子。您可以查看自述文件开始。

[](https://github.com/EntryLevelDeveloperTraining/material-ui-breakpoints) [## EntryLevelDeveloperTraining/material-ui-断点

### 如何使用材质 UI 断点？通过以下方式为 EntryLevelDeveloperTraining/material-ui-breakpoints 开发做出贡献…

github.com](https://github.com/EntryLevelDeveloperTraining/material-ui-breakpoints) 

# 什么是 CSS 断点？

当 CSS 检测到特定的屏幕尺寸时，它会在 CSS 中定义一个断点，当定义的断点被命中时，它会显示适合该屏幕尺寸的内容。例如，在桌面浏览器上，你有足够的空间来显示大而宽的元素，然而，在移动设备上，你必须计划出你绝对需要显示什么，以及什么会缩小尺寸。

# 如何在 CSS 中使用断点？

```
@media only screen and (max-width: 600px) {
  body {
    background-color: green;
  }
}
```

在这个 CSS 例子中，我们用媒体查询定义了一个断点。它设置了一个断点，说明只有当屏幕的最大宽度小于等于 600 像素时才应用这个 CSS。当屏幕尺寸小于等于 600 像素时，它将应用绿色背景色。当屏幕尺寸较大时，不会应用绿色背景。

现在让我们看看如何使用 Material UI 实现同样的事情。

# **使用媒体查询**

useMediaQuery 是一个材质 UI React 钩子，你可以在你的组件中使用它，当点击它时会导致重新渲染，从而让你控制你是否想要显示或隐藏组件，等等。

让我们从一个简单的媒体查询开始。

```
const SimpleMediaQuery = () => {
  const matches = useMediaQuery("(min-width:600px)");

  if (matches) {
    return (
      <Paper elevation={5}>
        <Box p={5}>SimpleMediaQuery breakpoint has a min width of 600px</Box>
      </Paper>
    );
  } return <></>;
};
```

使用 React 挂钩:

```
const matches = useMediaQuery("(min-width:600px)");
```

这将检查断点的最小宽度是否为 600px。如果匹配，意味着屏幕至少是 600 像素或更大，那么它将呈现文本。如果它不匹配，那么它不会渲染任何东西。

现在让我们使用一个断点助手来实现同样的事情。

```
const BreakpointHelper = () => {
  const theme = useTheme();
  const matches = useMediaQuery(theme.breakpoints.up("sm"));

  if (matches) {
    return (
      <Paper elevation={5}>
        <Box p={5}>
          BreakpointHelper will render everything up from 'sm' which is 600px
        </Box>
      </Paper>
    );
  }
  return <></>;
};
```

所以让我们来解释断点助手:

```
theme.breakpoints.up(“sm”) 
```

这是基于由材质 UI 设置的助手创建断点。

```
xs, extra-small: 0px
sm, small: 600px
md, medium: 960px
lg, large: 1280px
xl, extra-large: 1920px
```

所以只要是 *up* ，或者大于 *sm* ，也就是 *600px* ，那么就渲染文本。

还有其他方法可以实现这一点，如材料用户界面网站上所述。

[](https://material-ui.com/components/use-media-query/) [## React for responsive design-Material-UI 中的媒体查询

### 这是 React 的 CSS 媒体查询钩子。它监听 CSS 媒体查询的匹配。它允许渲染…

material-ui.com](https://material-ui.com/components/use-media-query/) 

# **在 makeStyles 中使用断点**

```
const useStyles = makeStyles((theme) => ({
  root: {
    [theme.breakpoints.down("sm")]: {
      backgroundColor: theme.palette.secondary.main,
    },
    [theme.breakpoints.up("md")]: {
      backgroundColor: theme.palette.primary.main,
    },
    [theme.breakpoints.up("lg")]: {
      backgroundColor: "green",
    },
  },
}));const MakeStylesExample = () => {
  const classes = useStyles();
  return (
    <Paper elevation={5} className={classes.root}>
      <Box p={5}>The background color will change based on screen width</Box>
    </Paper>
  );
};
```

这个例子使用了 *makeStyles* 函数来创建我们可以在组件中使用的样式。首先，我们设置一个*根*类名，并将其分配给 Paper 组件的类名。

```
<Paper elevation={5} className={classes.root}>
```

我们将 makeStyles 根赋予 Paper 类名。

```
[theme.breakpoints.down("sm")]: {
  backgroundColor: theme.palette.secondary.main,
}
```

当断点命中时，使用断点助手，它将 CSS 应用到元素并更改背景颜色。

# **使用盒组件**

Material UI 中的 Box 组件是我们现代最好的发明。您可以轻松地设计组件的样式，而无需使用大量 CSS。我们还可以使用断点助手来显示或隐藏某些元素，等等。

```
const BoxExamples = () => {
  return (
    <Paper elevation={5}>
      <Box p={5} display={{ xs: "block", sm: "none", md: "block" }}>
        This will hide on `sm` but show as a `block` on `xs` and `md`
      </Box>
    </Paper>
  );
};
```

Box 组件上有许多属性，使我们能够使用速记断点助手。在这个例子中，我们说对于显示，当点击 *xs* 的断点时，它将变成一个块元素。当点击 *sm* 的断点时，它不会渲染，显示为无。

您可以在此处查看可以在 Box 组件上使用的所有属性:

[](https://material-ui.com/system/basics/#all-inclusive) [## @ Material-UI/system-Material-UI

### 风格系统&用于构建强大设计系统的风格函数。@material-ui/system 提供低级实用工具…

material-ui.com](https://material-ui.com/system/basics/#all-inclusive) 

# **使用样式组件包**

在 React 中使用 CSS 有很多种方法。这个例子将介绍如何使用带有样式组件的材质 UI。

```
import { compose, spacing, palette, breakpoints } from "@material-ui/system";
import styled from "styled-components";const Box = styled.div`
  ${breakpoints(compose(spacing, palette))}
`;const BoxExamples = () => {
  return (
    <Paper elevation={5}>
      <Box p={{ xs: 5, sm: 10 }}>This uses a styled-components Box</Box>
    </Paper>
  );
};
```

这将创建一个样式化的组件 *div* 元素，并使用材质 UI 断点功能帮助器创建它。

```
const Box = styled.div`
  ${breakpoints(compose(spacing, palette))}
`;
```

这将使样式化的组件能够使用我们的断点助手。现在我们可以说，当 *xs* 断点被命中时，它的填充为 5，当 *sm* 断点被命中时，它的填充为 10。

# 结论

Material UI 有很多内置的断点魔法，所以你不需要编写很多自定义 CSS 就可以得到响应。我们讨论了要在组件中使用的材质 UI 挂钩和帮助器，并检查了如何通过使用材质 UI 断点帮助器来使用样式化组件。

有其他软件包可以达到同样的效果。你最喜欢的使用 CSS 断点的包是什么？