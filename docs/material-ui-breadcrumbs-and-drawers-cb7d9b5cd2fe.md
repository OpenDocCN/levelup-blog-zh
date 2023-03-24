# 材料用户界面—面包屑和抽屉

> 原文：<https://levelup.gitconnected.com/material-ui-breadcrumbs-and-drawers-cb7d9b5cd2fe>

![](img/64e255b171d1ba31142a3fb17096e89a.png)

照片由 [Kate Remmer](https://unsplash.com/@studioktr?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

材质 UI 是一个为 React 制作的材质设计库。

在这篇文章中，我们将看看如何添加面包屑样式和抽屉到材料用户界面。

# 定制面包屑

我们可以通过使用`withStyles`高阶组件向 breadcrumb 条目添加样式。

例如，我们可以写:

```
import React from "react";
import Breadcrumbs from "[@material](http://twitter.com/material)-ui/core/Breadcrumbs";
import { emphasize, withStyles } from "[@material](http://twitter.com/material)-ui/core/styles";
import Chip from "[@material](http://twitter.com/material)-ui/core/Chip";
import HomeIcon from "[@material](http://twitter.com/material)-ui/icons/Home";const StyledBreadcrumb = withStyles(theme => ({
  root: {
    backgroundColor: theme.palette.grey[100],
    height: theme.spacing(6),
    "&:hover, &:focus": {
      backgroundColor: theme.palette.grey[300]
    },
    "&:active": {
      boxShadow: theme.shadows[1],
      backgroundColor: emphasize(theme.palette.grey[300], 0.12)
    }
  }
}))(Chip);export default function App() {
  const handleClick = event => {
    event.preventDefault();
    console.info("clicked.");
  };
  return (
    <Breadcrumbs>
      <StyledBreadcrumb
        href="#"
        label="home"
        icon={<HomeIcon fontSize="small" />}
        onClick={handleClick}
      />
      <StyledBreadcrumb
        href="#"
        label="profile"
        onClick={handleClick}
      />
      <StyledBreadcrumb
        label="settings"
        onClick={handleClick}
        onDelete={handleClick}
      />
    </Breadcrumbs>
  );
}
```

我们使用`withStyles`高阶组件向一个`Chip`组件添加一些样式，向一个圆角框添加样式。

我们为`label` `onClick`和`onDelete`道具做广告，就像任何常规的面包屑项目一样。

# 与 react-路由器集成

我们可以将 React 路由器链接合并到面包屑中。

例如，我们可以写:

```
import React from "react";
import Breadcrumbs from "[@material](http://twitter.com/material)-ui/core/Breadcrumbs";
import { Route, MemoryRouter } from "react-router";
import { Link as RouterLink } from "react-router-dom";
import Link from "[@material](http://twitter.com/material)-ui/core/Link";const LinkRouter = props => <Link {...props} component={RouterLink} />;export default function App() {
  return (
    <div>
      <MemoryRouter initialEntries={["/"]} initialIndex={0}>
        <Route>
          {({ location }) => {
            return (
              <Breadcrumbs aria-label="breadcrumb">
                <LinkRouter color="inherit" to="/">
                  home
                </LinkRouter>
                <LinkRouter color="inherit" to="/profile">
                  profile
                </LinkRouter>
                <LinkRouter color="inherit" to="/settings">
                  settings
                </LinkRouter>
              </Breadcrumbs>
            );
          }}
        </Route>
      </MemoryRouter>
    </div>
  );
}
```

创建一个`LinkRouter`组件来返回我们在面包屑中显示的`Link`组件。

我们将`to`属性传递给我们的`LinkRouter`组件来设置链接所指向的 URL。

# 抽屉

导航抽屉可以让我们访问应用程序中的目的地。

它显示在侧面，并有链接，让我们点击它们。

例如，我们可以写:

```
import React from "react";
import Drawer from "[@material](http://twitter.com/material)-ui/core/Drawer";
import ListItemIcon from "[@material](http://twitter.com/material)-ui/core/ListItemIcon";
import ListItem from "[@material](http://twitter.com/material)-ui/core/ListItem";
import ListItemText from "[@material](http://twitter.com/material)-ui/core/ListItemText";
import List from "[@material](http://twitter.com/material)-ui/core/List";
import MailIcon from "[@material](http://twitter.com/material)-ui/icons/Mail";export default function App() {
  const [open, setOpen] = React.useState(true);
  const toggleDrawer = event => {
    setOpen(false);
  };
  return (
    <div>
      <Drawer anchor="left" open={open} onClose={toggleDrawer}>
        <List>
          <ListItem button key="home">
            <ListItemIcon>
              <MailIcon />
            </ListItemIcon>
            <ListItemText primary="home" />
          </ListItem>
          <ListItem button key="profile">
            <ListItemIcon>
              <MailIcon />
            </ListItemIcon>
            <ListItemText primary="profile" />
          </ListItem>
        </List>
      </Drawer>
    </div>
  );
}
```

我们有带`anchor`支柱的`Drawer`组件，使它显示在我们想要的位置。

`anchor`也可以是`right`、`top`或`bottom`。

`open`抽屉处于打开状态。

`onClose`有一个关闭时被调用的函数。

然后我们用`Drawer`组件渲染抽屉。

在它里面，我们有一个带有一些`ListItem`的`List`。

`ListItemIcon`有每个条目的图标。

`ListItemText`有项目文本。

# 可旋转抽屉

我们可以加一个`SwipeableDrawer`让抽屉可以滑动。

这在移动设备上效果更好。

例如，我们可以写:

```
import React from "react";
import ListItemIcon from "[@material](http://twitter.com/material)-ui/core/ListItemIcon";
import ListItem from "[@material](http://twitter.com/material)-ui/core/ListItem";
import ListItemText from "[@material](http://twitter.com/material)-ui/core/ListItemText";
import List from "[@material](http://twitter.com/material)-ui/core/List";
import MailIcon from "[@material](http://twitter.com/material)-ui/icons/Mail";
import SwipeableDrawer from "[@material](http://twitter.com/material)-ui/core/SwipeableDrawer";export default function App() {
  const [open, setOpen] = React.useState(true);
  const toggleDrawer = event => {
    setOpen(false);
  };
  return (
    <div>
      <SwipeableDrawer anchor="left" open={open} onClose={toggleDrawer}>
        <List>
          <ListItem button key="home">
            <ListItemIcon>
              <MailIcon />
            </ListItemIcon>
            <ListItemText primary="home" />
          </ListItem>
          <ListItem button key="profile">
            <ListItemIcon>
              <MailIcon />
            </ListItemIcon>
            <ListItemText primary="profile" />
          </ListItem>
        </List>
      </SwipeableDrawer>
    </div>
  );
}
```

我们所做的只是更换了`Drawer`和`SwipeableDrawer`。

![](img/517ca579257cc2b11f04a51acfc4671b.png)

Maksym Kaharlytskyi 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

我们可以通过改变样式和内容来定制面包屑。

它还与 React 路由器集成。

我们可以添加一个抽屉来添加侧边栏。