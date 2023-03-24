# 如何为 Gatsby 设置导入别名

> 原文：<https://levelup.gitconnected.com/how-to-set-up-import-aliases-for-gatsby-32398ae67e7f>

## 在这篇文章中，我们将一步一步地研究如何向 gatsby 站点添加导入别名。

![](img/ae70b724b758a26c2760ae1fc303fbdd.png)

照片由 [**拍惠伦**](https://unsplash.com/@patwhelen)

在这篇文章中，我们将一步一步地研究如何向 gatsby 站点添加导入别名。

我们设置导入别名的原因更多的是为了从导入树中导入组件时代码的可读性和外观。

我的意思是，如果我们看下面的例子

```
import Subscribe from '../../../../../../../core/modules/newsletter/mixins/Subscribe'
```

在我看来，这看起来非常丑陋，那么我们该如何改善它呢？

我们可以通过更新 webpack 配置来包含我们的主目录的别名，我们知道这些目录将成为我们组件的基础。

一旦我们完成了这篇文章中的所有步骤，最终结果将如下所示

```
import Subscribe from '@core/modules/newsletter/mixins/Subscribe'
```

# 在盖茨比中添加别名

假设 Gatsby 使用 Webpack 作为其核心，并且默认情况下不公开配置，我们可以使用 Gatsby 的`onCreateWebpackConfig` API 添加自定义 Webpack 配置，这将导致自定义配置被合并，从而允许您修改默认 webpack 配置。

要添加定制的 webpack 配置，我们需要编辑(或者创建文件，如果它不存在于我们的项目的根目录中)`gatsby-node.js`并将新的配置添加到其中。

## 配置

```
const path = require("path");
exports.onCreateWebpackConfig = ({ actions }) => {
  actions.setWebpackConfig({
    resolve: {
      alias: {
        "@components": path.resolve(__dirname, "src/components"),
        "@static": path.resolve(__dirname, "static")
      }
    }
  });
}
```

正如我们在上面的代码片段中看到的，actions 对象给我们提供了使用`setWebpackConfig`的选项，它获取我们的自定义 webpack 配置并将其合并到 Gatsby 的 webpack 配置中。

为了添加新的别名，我们将使用 webpack 的[解析别名](https://webpack.js.org/configuration/resolve/#resolve-alias)，这将允许我们在组件内部使用新创建的导入别名。

如下所示，添加新别名后的最终结果如下所示

```
import Layout from '@components/Layout';
```

# 解析 ESlint 导入

现在，我们已经完成了 webpack 配置的添加，我们会注意到 ESLint 正在抱怨这些导入，说它不能为它们解析路径，并且错误会说类似的话。

```
// Unable to resolve path to module '@components/Layout'. [import/no-unresolved]
import Layout from '@components/Layout';
```

为了解决这个问题，我们需要安装一个插件并更新`.eslintrc`配置。

## 装置

```
npm i -D eslint-import-resolver-alias
```

## 配置

我们需要将以下内容添加到我们的 eslint 文件中

```
{
  "settings": {
    "import/resolver": {
      "alias": [
        ["@components", "./src/components"]
      ]
    }
  }
}
```

`alias`属性接受一组数组作为它的值，每个数组代表别名和路径。

# 向 VScode 添加导入自动完成功能(可选)

最后，为了让 VScode 知道这些别名，以便在输入导入时继续提供自动完成功能，我们需要在`jsconfig.json`中添加别名。

```
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@components": [
        "src/components"
      ],
    }
  }
}
```

`paths`对象接受 in key 作为别名导入名称，其值是导入路径的数组。

# 结论

这包含了向我们的 Gatsby 项目添加导入别名的指南。