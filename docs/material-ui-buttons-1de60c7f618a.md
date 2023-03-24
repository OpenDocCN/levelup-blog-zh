# 材料用户界面—按钮

> 原文：<https://levelup.gitconnected.com/material-ui-buttons-1de60c7f618a>

![](img/195982ff4abfa03cf37e1f8ae2c0669f.png)

Jonathan Ybema 在 Unsplash[上拍摄的照片](https://unsplash.com?utm_source=medium&utm_medium=referral)

材质 UI 是一个为 React 制作的材质设计库。这是一组具有材质设计风格的 React 组件。

在这篇文章中，我们将看看如何添加按钮的材料设计。

# 纽扣

我们可以用`Button`组件添加按钮。

例如，我们可以写:

```
import React from "react";
import { makeStyles } from "@material-ui/core/styles";
import Button from "@material-ui/core/Button";const useStyles = makeStyles(theme => ({
  root: {
    "& > *": {
      margin: theme.spacing(1)
    }
  }
}));export default function App() {
  const classes = useStyles(); return (
    <div className={classes.root}>
      <Button variant="contained">Default</Button>
      <Button variant="contained" color="primary">
        Primary
      </Button>
      <Button variant="contained" color="secondary">
        Secondary
      </Button>
      <Button variant="contained" disabled>
        Disabled
      </Button>
      <Button variant="contained" color="primary" href="#contained-buttons">
        Link
      </Button>
    </div>
  );
}
```

添加各种按钮。

我们只需添加带有`variant`的`Button`道具来设置按钮的变体。

让我们改变颜色。

`disabled`让我们禁用一个按钮。

`href`让我们为链接添加一个 URL。

我们在`makeStyles`调用中带有`theme.spacing`的按钮上添加了一些空格。

# 文本按钮

我们可以创建文本按钮来删除轮廓。

例如，我们可以写:

```
import React from "react";
import { makeStyles } from "[@material](http://twitter.com/material)-ui/core/styles";
import Button from "[@material](http://twitter.com/material)-ui/core/Button";const useStyles = makeStyles(theme => ({
  root: {
    "& > *": {
      margin: theme.spacing(1)
    }
  }
}));export default function App() {
  const classes = useStyles(); return (
    <div className={classes.root}>
      <Button>Default</Button>
      <Button color="primary">Primary</Button>
      <Button color="secondary">Secondary</Button>
      <Button disabled>Disabled</Button>
      <Button href="#text-buttons" color="primary">
        Link
      </Button>
    </div>
  );
}
```

添加各种文本按钮。

我们移除了`variant`使其成为文本按钮。

# 轮廓按钮

我们可以用`variant`道具让按钮显示一个轮廓。

例如，我们可以写:

```
import React from "react";
import { makeStyles } from "[@material](http://twitter.com/material)-ui/core/styles";
import Button from "[@material](http://twitter.com/material)-ui/core/Button";const useStyles = makeStyles(theme => ({
  root: {
    "& > *": {
      margin: theme.spacing(1)
    }
  }
}));export default function App() {
  const classes = useStyles(); return (
    <div className={classes.root}>
      <Button variant="outlined">Default</Button>
      <Button variant="outlined" color="primary">
        Primary
      </Button>
      <Button variant="outlined" color="secondary">
        Secondary
      </Button>
      <Button variant="outlined" disabled>
        Disabled
      </Button>
      <Button variant="outlined" color="primary" href="#outlined-buttons">
        Link
      </Button>
    </div>
  );
}
```

使它们只显示一个轮廓。

`variant`设置为`outline`使它们那样。

# 处理点击

为了处理点击，我们可以向`onClick`属性传递一个事件处理程序。

例如，我们可以写:

```
import React from "react";
import { makeStyles } from "[@material](http://twitter.com/material)-ui/core/styles";
import Button from "[@material](http://twitter.com/material)-ui/core/Button";const useStyles = makeStyles(theme => ({
  root: {
    "& > *": {
      margin: theme.spacing(1)
    }
  }
}));export default function App() {
  const classes = useStyles(); return (
    <div className={classes.root}>
      <Button
        onClick={() => {
          alert("clicked");
        }}
      >
        click me
      </Button>
    </div>
  );
}
```

然后，当我们点击按钮时，我们会看到被点击的警告。

我们可以添加一个上传文件的按钮。

要添加一个，我们写:

```
import React from "react";
import { makeStyles } from "[@material](http://twitter.com/material)-ui/core/styles";
import Button from "[@material](http://twitter.com/material)-ui/core/Button";const useStyles = makeStyles(theme => ({
  root: {
    "& > *": {
      margin: theme.spacing(1)
    }
  },
  input: {
    display: "none"
  }
}));export default function App() {
  const classes = useStyles(); return (
    <div className={classes.root}>
      <input
        accept="image/*"
        className={classes.input}
        id="button-file"
        multiple
        type="file"
      />
      <label htmlFor="button-file">
        <Button variant="contained" color="primary" component="span">
          Upload
        </Button>
      </label>
    </div>
  );
}
```

我们用`display: 'none'`隐藏常规的 HTML 文件上传输入。

然后我们在 JSX 表达式中添加文件输入和按钮。

`id`应该与`htmlFor`值相匹配。这样，当我们点击按钮时，我们可以看到文件选择对话框。

# 大小

按钮可以有多种尺寸。

例如，我们可以写:

```
import React from "react";
import { makeStyles } from "[@material](http://twitter.com/material)-ui/core/styles";
import Button from "[@material](http://twitter.com/material)-ui/core/Button";const useStyles = makeStyles(theme => ({
  root: {
    "& > *": {
      margin: theme.spacing(1)
    }
  }
}));export default function App() {
  const classes = useStyles(); return (
    <div className={classes.root}>
      <Button size="small" className={classes.margin}>
        Small
      </Button>
      <Button size="medium" className={classes.margin}>
        Medium
      </Button>
      <Button size="large" className={classes.margin}>
        Large
      </Button>
    </div>
  );
}
```

我们使用带有`small`、`medium`或`large`值的`size`道具来改变按钮的大小。

![](img/c2f58719550656e155db9a28af91fa4a.png)

安娜·佩尔泽在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以用按钮做很多事情。

大小可以改变。

我们还可以为按钮添加轮廓和填充。

和上传按钮。