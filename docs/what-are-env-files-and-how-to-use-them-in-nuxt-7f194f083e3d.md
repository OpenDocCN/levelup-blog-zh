# 什么是？env 文件以及如何在 Nuxt 中使用它们

> 原文：<https://levelup.gitconnected.com/what-are-env-files-and-how-to-use-them-in-nuxt-7f194f083e3d>

![](img/73ba3179344382907994b0c20ab3c399.png)

什么是？env 文件以及如何在 Nuxt 中使用它们

**注意:**2020 年 6 月 18 日，Nuxt 发布了 v2.13，其中包括 Nuxt.js 运行时配置，这是一种处理环境变量的新方式——这将在适当的时候反映在本文中。

# 什么是. env 文件？

一个`.env`文件或`dotenv`文件是一个简单的文本配置文件，用于控制应用程序的环境常数。在本地、过渡和生产环境之间，应用程序的大部分不会改变。然而，在许多应用程序中，存在需要在不同环境之间改变某些配置的情况。环境之间常见的配置更改可能包括但不限于 URL 和 API 密钥。

# 做什么。env 文件看起来像什么？

`.env`文件是换行的文本文件，这意味着每一行代表一个变量。按照惯例`.env`变量名是由下划线分隔的大写单词。变量名后面直接跟一个`=`，后者后面直接跟一个值，例如:

```
VARIABLE_NAME=value
```

# 如何使用。Nuxt 中的 env 文件

# 安装 dotenv 模块

要在 Nuxt 中使用`.env`文件，首先需要安装 Nuxt `dotenv`模块。要安装该模块，请打开终端并导航到项目的根目录。从项目的根目录运行以下命令:

```
npm install @nuxtjs/dotenv
```

# 注册 dotenv 模块

一旦模块安装完毕，你需要注册它。要注册该模块，打开您的`nuxt.config.js`文件并导航到`buildModules`数组，然后像这样添加:`@nuxtjs/dotenv`:

```
buildModules: [
    '@nuxtjs/dotenv'
],
```

# 创建您的。环境文件

一旦你注册了你的模块，你就可以创建你的`.env`文件。在您最喜欢的代码编辑器中打开您的项目，并在项目的根目录下创建一个名为`.env`的新文件。一旦创建了`.env`文件，就可以创建第一个`.env`变量了。打开`.env`文件并创建一个测试变量:

```
TEST_VARIABLE=Hello world
```

*注意:*您需要重启您的应用程序来访问您的变量

# 访问您的。应用程序中的 env 变量

一旦成功安装了`dotenv`模块并创建了`.env`文件，在应用程序中引用变量就很容易了。为了确保你的`.env`文件按预期工作，打开你的 Nuxt 主页(`/pages/index.vue`)并创建/导航到你的`mounted`钩子。在挂载的钩子中，你需要`console.log()`你的`.env`变量。要引用`.env`文件，您需要以`process.env.`为前缀的变量名称，例如:

```
mounted() {
    console.log(process.env.TEST_VARIABLE)
}
```

安装后，您的应用程序将`Hello world`登录到您的控制台。

# 定制您的。环境变量前缀

不是每个人都喜欢`process.env.`前缀，就我个人而言，我选择坚持使用这个默认前缀，但是如果你喜欢不同的前缀，可以直接在 Nuxt 中设置。首先在您的域的根目录下创建一个名为`env.js`的新文件。在这个`env.js`文件中，您需要导出一个对象，该对象带有引用您的`.env`文件变量的键值对。例如，在您的`.env`中，您可能有许多 Google API 键和 ID，如下所示:

```
GOOGLE_MAPS_API_KEY=xxxxxxxxxxxxxx
GOOGLE_TAG_MANAGER_KEY=xxxxxxxxxxxxxx
```

然后，您可以在您的`env.js`文件中引用它们，如下所示:

```
export default {
    googleMapsApiKey: process.env.GOOGLE_MAPS_API_KEY,
    googleTagManagerKey: process.env.GOOGLE_TAG_MANAGER_KEY
}
```

或者，如果你更喜欢按组来组织你的变量，你可以决定创建一个`google`属性组，将你所有的 Google 关键字放在一起，就像这样:

```
export default {
    google: {
        mapsApiKey: process.env.GOOGLE_MAPS_API_KEY,
        tagManagerKey: process.env.GOOGLE_TAG_MANAGER_KEY
    }
}
```

要在 Nuxt 应用程序中访问新的`env.js`变量，只需导入`env.js`文件并引用变量，例如，如果您想在挂载时将`mapsApiKey`记录到控制台，您可以这样做:

```
<script>
    import ENV from '@/env' export default {
        mounted() {
            console.log(ENV.google.mapsApiKey)
        }
    }
</script>
```

# 不要犯错误。要 Git 的 env 文件

您的`.env`可能包含敏感信息，如 API 秘密、数据库用户名和密码等，保证这些信息的安全非常重要。

# 添加中。环境到。gitignore

如果你正在使用 Git，找到你的`.gitignore`文件，在新的一行添加`.env`，这将确保 Git 不存储你的`.env`文件，保证你的敏感信息安全。

# 确认身份。环境变量 when。环境被 Git 忽略

您可能会问:“如果您忽略了`.env`，开发人员将如何知道哪些`.env`文件是运行您的应用程序所必需的？”。幸运的是，有一个简单的解决方案，如果你选择创建一个`env.js`文件，这可能就足够了，但是使用`.env`文件创建一个可提交的`.env.example`文件是常见的做法。您的`.env.example`文件应该是您的`.env`文件的精确副本，但是变量值为空。这将允许开发人员下载您的应用程序的新安装，以快速了解在您的项目中开始工作需要哪些变量。

如果您不想手动复制您的`.env`文件，您可以通过导航到您的项目的根目录并使用以下命令从您的终端生成它:
`sed 's/=.*/=/' .env > .env.example`

*注意:*如果你使用的是 Windows，你可能需要通过终端模拟器运行命令，比如 [ConEmu](https://conemu.github.io/)

# 结论

文件对于环境特定的变量来说是极好的，并且是保护敏感信息的好方法，我强烈建议你考虑在下一个项目中使用它们。

如果你觉得这篇文章有用，请关注我的[媒体](https://medium.com/@wearethreebears)、[发展到](https://dev.to/wearethreebears)和/或[推特](https://www.twitter.com/wearethreebears)。