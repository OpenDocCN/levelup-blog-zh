# 如何使用 React-Helmet 在 React 中添加元数据

> 原文：<https://levelup.gitconnected.com/how-to-add-metadata-in-react-using-react-helmet-6dd316d146c2>

## 在本帖中，我们将探讨如何将 react-helmet 添加到我们的项目中，并使用它来处理组件内部的元数据

![](img/f4fc9250c7afb5ccf0b632d42768c296.png)

照片由 [NeONBRAND](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral) 拍摄

# 什么是反应头盔？

[“react-helmet](https://github.com/nfl/react-helmet)”是一个模块，它提供了一个组件来管理你对 meta 标签的所有更改，并将它们输出到 document head。

这意味着在我们有多条路线的项目中，我们希望根据页面上当前呈现的路线动态更新 SEO 的 meta 标签，react-helmet 将为我们处理这些，同时让我们控制应用程序元数据。

# 设置

首先，我们需要将 react-helmet 添加到我们的项目中，并将其导入到我们希望它保存我们对 document head 的更改的文件中。

```
npm install react-helmet --save
```

# 添加元数据

现在，我们来看一个如何向组件添加元数据的例子。

```
import React from 'react'
import { Helmet } from "react-helmet";class App extends React.Component {
  render () {
    return (
        <div>
            <Helmet>
                <title>App Title</title>
                <link rel="canonical" href="https://malikgabroun.com" />
            </Helmet>
        </div>
    );
  }
};
```

# 例子

在下面的例子中，我们将研究如何通过创建一个 ***SEO 组件*** 并将其导入到您想要的任何组件中来动态添加元数据。

```
import { Helmet } from 'react-helmet';
import React from 'react';const Seo = ({ title, description, pathSlug, keywords }) => {
  const url = `https://malikgabroun.com/${pathSlug}`
	return (
<Helmet htmlAttributes={{ lang: 'en' }} title={title} meta={[
        {
          name: 'description',
          content: description,
        },
        {
          name: 'keywords',
          content: keywords.join(),
        },
		]}
    links={[
     {
          rel: 'canonical',
          href: url,
      },
    ]}
    />
 );
}export default Seo;
```

创建组件后，再将它导入到任何组件中，并传递所需的道具。一般来说，添加条件来检查这些属性并设置默认值以防其中一个属性丢失是一个好主意。

完整的例子，看看我如何在我的个人网站[中使用 SEO 组件](https://malikgabroun.com/)，你可以在这里查看文件[。](https://github.com/gabroun/malikgabroun/blob/master/src/components/Seo.js)

# 结论

react-helmet 是一个组件，它允许我们控制元数据在网站中的显示方式。