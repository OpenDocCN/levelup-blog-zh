# 使用 Next.js 和 TypeScript 生成站点地图

> 原文：<https://levelup.gitconnected.com/generate-a-sitemap-with-next-js-and-typescript-906add9df0a7>

## 为你的 Next.js 项目改进搜索引擎优化

![](img/8a9d389eaa69726bca48d7d073e9aef6.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

> *作为一名 React 开发人员，你现在可能已经听说过 Next.js 了。它是一个优秀的基于 React 的框架，用于服务器端渲染和生成静态网站。它有许多开箱即用的功能。但是，它不提供关于站点地图的功能。幸运的是，只需很少的代码，就可以将这样的功能添加到任何 Next.js 项目中。本文将展示如何实现这一点。*

# 什么是网站地图？

让我们回忆一下什么是网站地图。站点地图是一个文件，您可以在其中提供有关网站页面的信息。网站地图有助于搜索引擎更智能地抓取你的网站。

当一个网站包含大量页面时，提供一个站点地图是合适的。站点地图帮助解决的另一个用例是孤立页面。孤立页面是指在网站上的其他任何地方都不被引用的页面。通常孤立页面不会被爬虫发现。

爬虫将浏览网站并打开它找到的链接。因此，如果页面没有链接到任何地方，它就不会被索引。网站地图告诉搜索引擎这些页面的存在，确保这些页面在你的网站抓取过程中被找到。

值得注意的是，使用网站地图并不能保证网站地图中的所有项目都会被抓取和索引。搜索引擎的内部运作依赖于复杂的算法，而这些算法并不是公共知识。然而，在大多数情况下，你的站点会从拥有一个站点地图中受益，你永远不会因为拥有一个站点地图而受到惩罚。你可以在这里了解更多关于网站地图的信息。

# 网站地图的结构

有多种文件格式可以用于站点地图文件。XML 格式是最常用的。因此，本文使用 XM 来实现站点地图。站点地图的 XML 需要符合特定的模式。您可以在下面的代码片段中找到站点地图的示例。

协议的精确文档可以在[这里](https://www.sitemaps.org/protocol.html)找到。并非`url`元素的所有子元素都是必需的。只有`loc`元素是必需的，它描述了网页的 URL。其余的元素是可选的。如果你不确定子元素和元素是什么意思，你可以在这里阅读更多关于 XML 的内容。

# Next.js 中的站点地图

那么如何在 Next.js 中生成站点地图呢？有多种方法可以解决这个问题。和大多数问题一样，已经有现成的解决方案。如果你愿意，你可以使用一个 npm 包，比如[nextjs-sitemap-generator](https://www.npmjs.com/package/nextjs-sitemap-generator)或者 [next-sitemap](https://www.npmjs.com/package/next-sitemap) 来为你完成这项工作。

但是这有什么意思呢？我试图将我的项目依赖保持在最低限度。更不用说构建自己的实现会让您对所使用的工具有更多的经验和知识。

使用 Next.js 本身的推荐方式是静态页面生成。如果页面使用静态生成，则页面的标记在生成时生成。我们也可以选择只在构建时构建站点地图。如果网站是真正静态的，并且页面数量不变，这将是一个很好的解决方案。

但是，让我们考虑一下偶尔添加页面的网站场景，例如，博客或电子商务网站。在这种情况下，每当添加新页面时，让站点地图动态变化是很有用的。

# 实现功能

网站地图将使用服务器端呈现在每个请求上动态生成。在呈现站点地图之前，需要知道网站上的页面。在 Next.js 中，页面基于包含在`pages`目录中的文件。每个页面都根据其文件名与一个路径相关联。更多关于 Next.js 的路由可以在[这里](https://nextjs.org/docs/routing/introduction)找到。

一种选择是使用节点的文件系统模块遍历`pages`文件夹，并手动解析文件名。然而，人们可能期望 Next.js 在构建时将它解析的路由存储在某个地方。事实的确如此；这些路径存储在`.next\\server\\pages-manifest.json`文件的构建文件夹中。这可以用来收集静态路由，我们将在后面的章节中讨论动态路由。

这将证明是非常有用的。让我们在 pages 文件夹中创建一个名为`sitemap.tsx`的文件。这个文件将负责呈现站点地图。该文件需要导出一个名为`getServerSideProps`的函数，如这里的[所示](https://nextjs.org/docs/basic-features/pages#server-side-rendering)。

在阅读这个函数的文档时，可以看到它接收了一个名为 context 的参数。这个对象的属性之一是`res`，节点响应对象。该对象包含允许设置 HTTP 响应的头并直接写入响应体的函数。这些函数可用于将 Content-Type 头设置为 XML，并将 XML 字符串写入响应正文。

在上面的代码中，首先导入必要的模块。这些是 Node 的文件系统和路径模块，将用于读取清单文件。请注意，这些是服务器端模块。这样的模块可以安全地用在`getServerSideProps`函数中，因为该函数中使用的模块不包含在 Next.js 的客户端包中

文件系统模块稍后会在另一个功能中使用。如果一个函数被声明并且没有在`getServerSideProps`中使用，那么它将被包含在客户端包中。这导致了一些问题，因为文件系统模块本身不会包含在这个包中，但是使用它的函数会包含在这个包中。构建项目时，这种缺失会导致模块未找到:错误。

将上面的代码添加到`next.config.js`中可以解决这个问题。在这里可以找到一个非常有用的工具来检查客户端包中的代码。关于 Next.js 中代码拆分的详细解释可以在[这里](https://maikelveen.com/blog/how-to-solve-module-not-found-cant-resolve-fs-in-nextjs)找到。

接下来定义了一个叫做`Url`的类型。此类型描述了表示站点地图中条目的对象的形状。然后定义一个名为 Sitemap 的 react 组件。这个组件是空的，因为在`getServerSideProp`中，响应对象上的 end 方法被调用。调用此方法表示服务器应该认为此消息是完整的。

虽然看起来没什么用，但是组件的定义不能省略，因为`getServerSideProps`函数需要一个组件来附加。如果组件没有包含在文件中，Next.js 的构建过程将再次抛出一个错误:`getServerSideProps` *不能附加到页面的组件中，必须从页面中导出。*

在我们深入研究该功能的细节之前，让我们考虑一下需要哪些步骤来实现最终目标。该过程的步骤如下:

*   从文件系统中读取清单文件，并获取一个 JSON 对象。
*   解析 JSON 的内容并检索 URL 集合。
*   从构建文件夹输出文件中检索动态路由 URL。
*   基于 URL 创建 XML 站点地图字符串

每个步骤都在单独的功能中实现。在函数`ReadManifest`中读取清单文件。这个函数从清单中返回 JSON，函数`GetPathsFromManifest`将它用作构建 URL 集合的输入。只能从清单中收集静态 URL，但是，`GetPathsFromManifest`说明了动态 URL。

函数`GetSitemapXml`实现了基于 URL 构建 XML 字符串的最后一步。本文的以下部分讨论了每个具体步骤。基于所有收集的 URL，符合站点地图标准的 XML 字符串。

在上面的代码中，已经可以看到每个步骤的所有函数调用。在代码中还可以看到但尚未讨论的是`excludedRoutes`数组。这些路径被定义和过滤，因为[自定义错误页](https://nextjs.org/docs/advanced-features/custom-error-page)也出现在清单中。如果我们不排除 404 页面的自定义错误页面，它会出现在站点地图中。这是不希望的，因为该页面实际上返回一个 404 HTTP 代码。

# 读取清单文件

如果要从文件系统中读取清单文件，需要知道它的位置。相对路径是已知的。然而，要获得文件的绝对路径，还需要知道节点进程的工作目录。该值通过调用`process.cwd`函数来检索。由于稍后在不同的函数中也需要这个值，所以它已经绑定到了`basePath`变量。

在`ReadManifest`中，实现了清单的读取。这个函数中使用了节点的路径和文件系统模块。如前所述，Next.js 将其解析的路由保存在 pages-manifest JSON 文件中。这个 JSON 文件包含一个键值对列表。

键表示路由，值表示定义路由组件的文件的路径。该路径指向客户机包中的编译文件，因此如果您在。tsx 文件，清单中的路径将有。js 扩展。

首先，清单的文件路径是通过将 basePath 函数参数与文件路径结合起来构建的。

在函数试图读取文件之前，会检查文件是否存在。如果文件不存在，函数返回原始的空值。如果是，它会同步读取文件，并使用内置的 JSON 对象解析内容。`JSON.parse`函数返回一个对象，该对象也由`ReadManifest`函数返回。

# 从路由中获取 URL

`ReadManifest`函数返回一个对象。对于站点地图，需要一个 URL 列表。从对象中获取这个列表是在`GetPathsFromManifest`函数中完成的。

那么这个函数是怎么回事呢？首先，初始化一个包含路由的空字符串数组。然后，该函数遍历解析后的清单对象中的所有路由。对象的关键字对应于路线。

Next.js 中有两种路线比较特别。动态路由段用于创建以下划线开头的动态页面和路由。动态路由包含方括号。以下划线开头的路由用于覆盖 Next.js 的某些默认行为。

这些路由不应该包含在站点地图中。将使用`isNextInternalUrl`功能过滤路线。该功能的实现方式如下:

该函数使用正则表达式来查找 Next.js 的特殊路线。如果您不熟悉正则表达式，可以使用各种在线资源来阅读它们。

# 动态路由

Next.js 最吸引人的地方在于它能够用来自不同来源的数据动态构建页面。站点地图也应该包含这些页面。

动态生成的页面是清单中包含方括号的页面。在前面的函数中，这些都被过滤掉，不予考虑。

Next.js 有两种形式的预渲染:静态生成和服务器端渲染。本节介绍一种在站点地图中包含静态生成的页面的方法。它没有讨论使用服务器端渲染的动态路由，因为对于这种情况没有一个适合所有情况的解决方案。

对于静态生成的页面，数据在构建时获取。该数据提取的逻辑在`getStaticProps`函数中定义。当一个文件定义这个函数时，它还需要定义`getStaticPaths`，返回所有静态预渲染的路由。

当静态呈现的页面在构建时预呈现时，除了页面 HTML 文件之外，Next.js 还会生成一个保存了`getStaticProps`函数结果的 JSON 文件。这些文件存储在以下文件夹中:`.next/server/pages`

例如，用 Next.js 创建的博客有由`blog/[slug].tsx`定义的文章页面。在`getStaticPaths`中定义了 slug 的可能值列表。对于这些值中的每一个，页面的属性将在构建时收集并保存在一个 JSON 文件中。有鼻涕虫`how-to-start-with-react`的文章，就会有一个文件:`.next/server/pages/blog/how-to-start-with-react.json`。

可以遍历`.next/server/pages`来读取 JSON 文件的所有文件名。以这种方式，收集了所有静态生成的页面的所有已知路由。下一节将讨论如何实现这种遍历。

# 遍历静态生成的页面属性

`GetPathsFromBuildFolder`函数负责遍历静态生成页面的保存属性。这些属性存储在`server/pages`文件夹中的 JSON 文件中。

需要记住的一点是可能的动态 URL 的深度。例如，您可以创建一个名为`pages/posts/[id].js`的文件，生成的页面将驻留在 server/pages 文件夹中一个名为 pos ts 的文件夹中。

然而，可能存在不嵌套在文件夹中的页面。该函数需要考虑这两种可能性。基本上，需要完全遍历目录。这是使用递归的一个很好的例子。

我们已经确定`GetPathsFromBuildFolder`是一个递归函数；让我们深入研究代码！

如上面的代码所示，该函数有四个参数。参数`dir`描述了在特定函数调用中被遍历的目录。`urlList`存储所有收集的 URL。`host`描述了放入站点地图的 URL 的主机部分。最后，`basePath`描述了 Next.js 的工作目录，如前所述。

首先，该函数读取 dir 参数中描述的目录内容中的所有元素。然后，对于这个目录中的每个项目，它检查它本身是否是一个目录。如果是，这个函数将被递归调用。

如果项不是目录，项扩展名是 JSON，我们就把它当成路由。扩展和基本路径被删除，路由被添加到`urlList`。

# 构建网站地图

至此，所有的 URL 都已收集完毕。只剩下一件事要做:生成一个 XML 字符串并在响应中返回它。与路线的实际收集相比，这是一个微不足道的任务。

`GetSitemapXML`函数使用[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)实现功能。所有的 URL 都通过`GetUrlElement`函数映射到适当的 XML。它们的实现方式如下:

# 结论

本文向您展示了如何为 Next.js 网站实现 sitemap 功能(完整代码可以在这里找到)。感谢您的阅读。可以对该功能进行许多改进，比如缓存和读取保存的属性以包含 lastModified XML 元素。

如果您将代码集成到您的项目中，不要忘记让 Google 可以使用您的站点地图。有多种方法可以做到这一点，你可以在这里阅读更多关于那个[的内容。](https://developers.google.com/search/docs/advanced/sitemaps/build-sitemap)

# 资源

[](https://developers.google.com/search/docs/advanced/sitemaps/overview) [## 了解网站地图|谷歌搜索中心|谷歌开发者

### " type": "thumb-down "，" id ":" missingtheinformationneed "，" label ":"缺少我需要的信息" }，{ "type"…

developers.google.com](https://developers.google.com/search/docs/advanced/sitemaps/overview) [](https://www.w3schools.com/xml/default.asp) [## XML 教程

### XML 代表可扩展标记语言。XML 被设计用来存储和传输数据。XML 被设计成既是…

www.w3schools.com](https://www.w3schools.com/xml/default.asp) [](https://nextjs.org/docs/basic-features/pages#server-side-rendering) [## 基本功能:Pages | Next.js

### Next.js 页面是在 pages 目录下的文件中导出的 React 组件。了解他们如何在这里工作。

nextjs.org](https://nextjs.org/docs/basic-features/pages#server-side-rendering) [](https://developers.google.com/search/docs/advanced/sitemaps/build-sitemap) [## 创建并提交网站地图|谷歌搜索中心|谷歌开发者

### " type": "thumb-down "，" id ":" missingtheinformationneed "，" label ":"缺少我需要的信息" }，{ "type"…

developers.google.com](https://developers.google.com/search/docs/advanced/sitemaps/build-sitemap)