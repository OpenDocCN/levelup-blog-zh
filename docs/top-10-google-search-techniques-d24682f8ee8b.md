# 面向开发者的 10 大谷歌搜索技术

> 原文：<https://levelup.gitconnected.com/top-10-google-search-techniques-d24682f8ee8b>

![](img/8c146716e82facb7f1896dc379f962ba.png)

# 介绍

软件工程师每天都在 Google stuff 上修复 bug，寻找问题的解决方案，学习一门新的编程语言，学习如何开发一个功能，等等。

高级工程师知道，编程语言语法、方法、数据类型等小细节并不重要，记忆可以在谷歌上查找的东西是浪费时间。

![](img/07f1db6c002c393751abd01a21c93053.png)

所以，我们为什么不花些时间学习如何正确地使用谷歌，从长远来看，这会节省我们很多时间呢？

你可以在搜索中使用符号或单词来使你的搜索结果更加精确，我将分享 10 大谷歌搜索技术，它们将帮助你更快地搜索并准确地找到你要找的东西，甚至是像母鸡的牙齿一样稀少的虫子。

# 常见搜索技术

让我们来看看一些常用的搜索技术，你可以用它们来更有效地查找资料

1。 **排除** —您可以在想要省略的单词前加上减号(`—`)

例如，你可能正在使用一个支持 React 和 Angular 的框架，比如 [Ionic](https://ionicframework.com/) ，而你的项目在 React 上，所以你想排除所有使用该框架和 Angular 的项目。在这种情况下，您可以在 Angular 前面添加`—` ，以将该单词完全排除在搜索结果之外。

❌ `An error occurred while running subprocess npm. ionic start` ✅ `An error occurred while running subprocess npm. ionic start **-angular -ionic-angular**`

2。 **搜索精确匹配** —如果你想找到你要搜索的精确匹配，把一个单词或短语放在引号内(`"...”`)。

我发现自己最常使用这个命令。假设您正在处理 React native 项目，并且您在 ios 模拟器上遇到了一个问题，如果您在 Google 上搜索该错误，很可能会显示一些 swift 项目中发生的相同错误，这里您可以在错误前添加引号“React Native ”,以确保只显示 React Native 项目

❌ `error: You attempted to use a firebase module that’s not installed natively on your iOS project by calling firebase.messaging()`
✅ `**“react native”** error: You attempted to use a firebase module that’s not installed natively on your iOS project by calling firebase.messaging()`

3。 **查找有漏字的错误** —用星号`*`或 3 个点`…`作为所有漏字的占位符

我们经常会遇到这样的错误，上面写着“在…中缺少一些东西”,并在您的计算机中给出一些路径，现在如果您在搜索中复制并粘贴此错误，您得到的结果将比用 3 个点替换文件夹路径要少，因为不同的计算机上的路径会有所不同。

❌ `Error: Can’t resolve ‘abort-controller-es5’ in ‘/Users/hayksimonyan/Desktop/JTUpdates/adm-plots-units/node_modules/botframework-directlinespeech-sdk/lib’`
✅ `Error: Can’t resolve ‘abort-controller-es5’ in **‘…’**`

4。 **按年份过滤内容** —如果你想给你的搜索添加日期，你可以使用`BEFORE:`和`AFTER:`操作符

当您搜索一个相对较新的错误时，比如项目中的一些依赖项更新之后，您可能想要最新的结果，对于这种情况，您希望 Google 返回某个日期之后发布的答案，比如

❌ `Jest test fails : TypeError: window.matchMedia is not a function` ✅ `Jest test fails : TypeError: window.matchMedia is not a function **after:2020**`

或者，如果你正在做一个遗留项目，你可能只需要某个日期之前的东西

❌ `Bootstrap 4 navbar is always visible on mobile` ✅ `Bootstrap 4 navbar is always visible on mobile **before:2021**`

5。 **按文件类型搜索** —您可以使用它来查找所有类型的文件，PDF、SVG、png 等等(文件类型:PDF |文件类型:SVG | …)

也许你正在寻找某人的电子书，找到它最简单的方法是指定文件类型，在本例中是 PDF

❌ `Eloquent javascript` ✅ `Eloquent javascript **filetype:pdf**`

6。搜索**特定站点** —将`site:`放在站点或域(`site:sitename.com:` | `sitename:`)的前面

这种情况的一个典型场景是当您搜索与您的项目相似的存储库时，在这种情况下，我总是在搜索前面添加 Github:前缀，以便只返回 Github 存储库

❌ `apollo starter backend` ✅ `**site:github.com** apollo starter backend` | `**github:** apollo starter backend`

7。搜索**相关网站** —将`related:`放在网址前面。例如，`related:time.com`。

这是之前的`site:…`命令的另一个版本，站点更加严格，而相关将返回来自其他站点的结果以及与您提供的网站相关的结果。

如果我们看一下前面的例子，您可以用 related here 替换该站点

`site:github.com apollo starter backend` = > `**related:github.com** apollo starter backend`

你会从 GitLab、Bitbucket 和其他类似于 GitHub 的网站上获得更多的选择

8。参见谷歌网站的**缓存版本**——在网站地址前加上`cache:`。

如果你在互联网上有一个已部署的网站，你可以使用`cache:`后跟你的网站域名来查看谷歌是如何缓存你的网站的

`**cache:**amazon.com`

9。 **组合搜索** —在每个搜索查询之间放置`OR`。这将返回与 OR 运算符两边之一匹配的所有结果

`Jest encountered an unexpected token``Jest encountered an unexpected (token **OR** error)`

10。最后，在**数字范围**内搜索——也可以搜索数字范围。

如果您正在寻找一个价格范围内的笔记本电脑，您可以在搜索中指定它`programming laptop **$1000…$1500**`

## 技巧

*   谷歌搜索通常会忽略不属于搜索运算符的标点符号。
*   不要在符号或单词和你的搜索词之间加空格。搜索`**site:github.com**`可以，但是`**site: github.com**`不行。

感谢阅读。请关注以获得更多开发技巧。

# 资源

[https://www . pcmag . com/how-to/Google-search-tips-youll-want-to-learn](https://www.pcmag.com/how-to/google-search-tips-youll-want-to-learn)

[https://www . life hack . org/articles/technology/20-tips-use-Google-search-efficient . html](https://www.lifehack.org/articles/technology/20-tips-use-google-search-efficiently.html)

【https://support.google.com/websearch/answer/2466433?hl=en 