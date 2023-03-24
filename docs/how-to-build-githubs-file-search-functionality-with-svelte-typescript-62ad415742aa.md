# 如何用 Svelte + Typescript 构建 Github 的文件搜索功能

> 原文：<https://levelup.gitconnected.com/how-to-build-githubs-file-search-functionality-with-svelte-typescript-62ad415742aa>

![](img/7966821aa236f9bb2f5fea088e457d97.png)

马库斯·温克勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

几天前，我偶然看到了这个教程，它解释了如何用 React 构建 GitHub 文件搜索功能。如果你不知道那是什么，试着去任何一个 [github repo](https://github.com/skflowne/github-file-search-svelte-ts) 然后按“t”开始文件搜索。

这是一个很好的训练组件，不太简单，也不太复杂。在这里，我将向您展示如何用 Svelte + Typescript 而不是 React 来构建它。

这是我们将要构建的应用程序的 [live 版本。按“t”尝试文件搜索。](https://github-file-search-svelte-ts.web.app/)

# 项目设置

两个选项:

如果你想学习如何手动操作，请关注我的[文章。](/how-to-setup-svelte-typescript-tailwindcss-scss-bebdca7b2a0a)

如果您只想立即开始构建组件，请遵循下面的说明

```
npx degit [https://github.com/skflowne/svelte-ts-scss-tailwind-template](https://github.com/skflowne/svelte-ts-scss-tailwind-template) github-file-search-svelte
cd github-file-search-svelte
yarn
```

我使用`yarn`作为包管理器，但是如果你愿意，你也可以使用`npm`

您现在可以运行`yarn dev`来运行开发服务器

# API 数据

我们需要一些数据来渲染我们的组件，我使用的数据与最初的【React 教程相同。

[从这里下载 API 数据文件](https://github.com/skflowne/github-file-search-svelte-ts/blob/master/src/api/index.js)

在应用程序的`src`文件夹中创建一个新的`api`文件夹，并将文件内容复制到`index.js`

请注意，在本文的其余部分，每次我告诉您创建一个新文件夹时，它都将位于`src`文件夹中

我们的数据看起来是这样的:

我们可以看到我们的文件有一个`id`，类型可以是`file`或`folder`，一个`name`，一个提交`comment`和文件的最后一个`modified_time`

让我们创建一个名为`File`的`interface`来表示我们的数据

在您的`src`文件夹中，创建一个`interfaces`文件夹和一个新文件`file.interface.ts`

我们现在有一个自定义的 Typescript 类型来表示我们的文件。

你可能会想，为什么我们不为我们的`type`属性使用一个`string`类型呢？

这是因为我们只想接受`file`或`folder`作为该属性的值，这将允许 Typescript 在我们使用无效值时警告我们。

Typescript 还将提供带有有效值的自动完成功能，这是使用 Typescript 的另一个好处。

这提供了一种简单的方法，可以直接在我们的 IDE 中知道我们的`File`数据结构应该是什么样子。这将有助于我们项目中的其他人或我们未来的自己，他们可能没有我们现在的自己记得那么多。

# 通过一个苗条的商店访问我们的文件

现在我们在`api/index.js`中有了数据，在`interfaces/file.interface.ts`中有了自定义类型，让我们创建一个在应用程序中使用的瘦商店。

由于这应该是来自 API 的数据，我们永远不会写入它，因此我们将使用来自`svelte/store`的`readable`创建一个只读存储

新建一个文件夹`store`并在里面新建一个`files.ts`文件。

注意`readable`是`readable<T>`的形式，它允许我们指定商店的类型。

我们没有在`files`变量上设置类型，因为`files`不是`File[]`，它是一个包含`File[]`的存储，如果你在 VSCode 中将鼠标悬停在它上面，你会看到它的实际类型是`Readable<File[]>`，一个包含`File`数组的可读存储。

`readable`有两个参数:`start`和一个 setter 函数`(set)=> void`

这里我们提供了一个空数组作为`start`，并提供了 setter 函数，该函数简单地将我们的存储值设置为包含在我们的`/api/index.js`文件中的数据

虽然这个存储不是很有用，因为我们使用的是静态数据，但是请记住，您可以使用 setter 函数进行 API 调用，或者设置对 Firebase 集合的订阅。

通过将我们所有的数据逻辑存储在存储中，这也有助于分离我们的关注点，例如，我们不必将`App.svelte`与我们的`File`类型的导入混在一起。

当我们实现实际的文件搜索时，我们将对存储做更多有用的事情。

我将以一种“自由”的方式使用商店，展示它们如何让我们的生活变得更轻松。但是，在构建这样一个应该能够独立存在的组件时，应该避免依赖于子组件中的存储。

理想情况下，`file-search`中的所有东西都应该组成一个组件，您可以通过 props 将它可能需要的任何数据传递给它，这样，如果您以后决定在另一个项目中使用它，它就不会抱怨商店不存在。

另一个选择可能是将商店作为组件的一部分，这样您仍然可以享受好处，而不会有未满足的依赖性。

但是这是另一个时间的讨论，我不会在这里打扰它，只是要记住，这里显示的组织不是最好的可重用性。

# 构建我们的 FileItem 组件

让我们将我们的`files`存储导入到我们的根`App.svelte`组件中，并开始构建我们的第一个组件`FileItem.svelte`

首先，您可以通过完全删除 style 标签来清理`App.svelte`，因为由于 TailwindCSS，我们不再需要它，您也可以删除我们的`<main>`标签中的 HTML。

创建一个新的`components`文件夹，在里面创建一个`file-search`文件夹来保存所有与文件搜索相关的组件。

在那里，创建我们的第一个组件`FileItem.svelte`

注意我们正在做`import type`来导入我们的`File`接口，因为如果你只是做`import`你会得到一个错误，这是必需的，因为 Svelte + Typescript 默认配置是使用`importsNotUsedAsValues`被设置为“错误”，你可以[在这里阅读关于这个语法的更多信息](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-8.html)。

我不确定为什么配置是这样设置的，你可以通过添加一个新的`compilerOptions`对象并将其`importsNotUsedAsValues`设置为`preserve`或`remove`来覆盖它。两者似乎都运行良好。如果有人知道为什么要这样设置，请在评论中告诉我。我的最佳猜测是，只有`type`的导入将被完全删除，因此可能会避免不必要的导入。但是我也猜想将配置设置为 remove 会有相同的效果，同时仍然允许您使用普通的`import`语法。

然而，因为我不知道为什么要这样设置，所以我将保持配置不变。

还要注意在我们的脚本标签上设置的`lang="ts"`属性，这是我们告诉编译器把我们的代码当作 Typescript 的方式，如果你忘记了，你会得到一个关于任何 Typescript 特定语法的错误。

我们还用关键字`export`定义了一个`File`类型的组件道具。这就是你告诉斯维特期待一个外部支持的方式。

```
export let myProp: Type
```

更新我们的`App.svelte`来显示第一个文件

我们导入我们的`FileItem.svelte`组件和`files`存储

然后我们呈现一个`FileItem`，通过使用`$files`访问存储中包含的值，我们将第一个文件传递给它。

在商店前面添加特殊的`$`告诉 Svelte 我们想要订阅商店并访问里面的值。

没有前面的`$`,您访问的是商店本身，而`$`是告诉 Svelte 设置一个对该商店的自动订阅并获取实际价值。

这相当于做

请记住，上面的代码是一种简化，通常您还应该在组件卸载时手动取消订阅。

让我们显示文件的其余信息，根据我们的`File`的`type`显示文件图标或文件夹图标，并用 TailwindCSS 设置组件的样式。

我们将使用`svelte-awesome`来显示 fontawesome 图标，使用`date-fns`来显示我们的`modified_time`作为距离，比如“一个月前”。

在最初的教程中，他们用的是`moment`，我用的是`date-fns`，因为它是一个更轻的包，并且有相似的特性。

最后，我们将快速比较一下 React + Moment.js 版本的包大小。

```
yarn add -D svelte-awesome [@fortawesome/free-regular-svg-icons](http://twitter.com/fortawesome/free-regular-svg-icons) date-fns
```

请注意，我们使用`-D`是因为 Svelte 是一个编译器，它会在我们的包中包含它需要的代码，我们不需要它们作为`dependencies`

更新我们的组件，如下所示

我们使用 TailwindCSS 的类来设计一切，而不需要编写任何 CSS。如果你想知道这些类是做什么的，可以去看看 TailwindCSS 的文档。

我们从`svelte-awesome`导入`Icon`，这将允许我们通过将它们传递到`data`属性来显示由`@fortawesome/free-regular-svg-icons`提供的字体图标。

您将在 IDE 中得到一个关于`data`属性的错误，因为`svelte-awesome`没有 Typescript 类型定义，但是它仍然可以工作。

```
const distance = formatDistanceToNow(parseJSON(file.modified_time))
```

最后，我们计算现在和我们的`modified_time`之间的时间距离，首先用`parseJSON`转换我们的 JSON 日期，并将结果`Date`传递给`formatDistanceFromNow`，这两个方法都由`date-fns`提供

# 创建文件列表组件

在我们的`components/file-search`中创建一个名为`FileList.svelte`的新文件

我们定义了一个类型为`File[]`的`files`道具

当这个数组不为空时，我们用一个`{#each as file (file.id)}`块循环遍历文件，这个块允许我们呈现一个`FileItem`组件，我们将文件作为道具传递给这个组件。`{file}`是`file={file}`的简称。

`(file.id)`负责为我们的组件提供一个惟一的键，就像你在 Vue.js 中使用 React 或`key="file.id"`一样

当数组为空时，我们显示“没有匹配文件”的消息。

修改`App.svelte`以显示`FileList`组件

# 使用苗条商店实现文件搜索

我们将使用存储来跟踪用户在搜索输入中输入的内容，并从用户输入和我们的文件数据中获取搜索结果。

在我们的`store`文件夹中创建一个`fileSearch.ts`文件

我们简单地创建一个可写的存储，指定它将持有一个`string`，这将是我们的用户的输入，并将其初始化为一个空字符串。

在我们的`store`文件夹中为我们的搜索结果创建另一个名为`fileSearchResults.ts`的文件。

每当我们的`fileSearch`商店或`files`商店更新时，我们使用一个派生商店来重新计算我们的搜索结果。

`derived`接受一个存储或存储数组和一个回调函数。

回调函数将包含一个存储值数组，并在其中一个值发生变化时运行。

在这种情况下，我们的文件永远不会改变，因为我们从文件中读取静态数据，因此，每次我们的`fileSearch`的存储更新时，回调都会被调用。

现在，想象你的文件不是静态的，应用程序允许用户添加新文件。如果您使用 Firebase 来设置对一个`files`集合的订阅，每当另一个用户添加一个新文件时，这将触发订阅，更新我们的`files`存储，并重新运行我们的回调，为当前用户更新我们的搜索结果。这将允许我们的应用程序的状态总是保持一致，即使当其他用户进行更改时，也不需要刷新页面。

在文件搜索模式下，我们希望只显示文件，首先总是过滤我们的`files`数组，只返回带有`type`“file”的文件。

我们检查我们的搜索是否为空，在这种情况下，我们希望返回所有内容，否则，我们希望只返回匹配的结果。

我们注意使文件名和搜索输入都是小写的，以允许不区分大小写的搜索。否则，我们可能正在搜索`readme`，而名为`README.md`的文件将不会匹配。然后，只有当文件的`name`包含我们的`search`时，我们才返回该文件。

# 文件搜索组件

在`components/file-search`中创建一个`FileSearch`组件

我们导入我们的`fileSearch`存储，并用`bind:value={$fileSearch}`设置我们输入值的双向绑定。

这样，我们输入的值将总是被设置为我们商店的值，无论用户输入什么，我们商店的值都会自动更新。

同样，我们绑定到`$fileSearch`而不是`fileSearch`，因为我们想要绑定到商店的价值，而不是商店本身。

为了让我们的输入元素在组件出现时立即被聚焦，我们首先创建一个`input`变量来保存对我们的输入元素的引用，并在我们的输入上使用`bind:this={input}`来将它绑定到我们的变量。你可以把它想成“把这个元素绑定到`input`变量上”。

每当`input`改变和存在时，我们使用 Svelte 的反应性陈述来集中我们的输入。一旦我们的 input 元素真正呈现出来，就会发生这种情况。

当`$:`右侧的变量发生变化时，Svelte 的反应语句将会更新。

```
$: if(input){
   // ...runs when input changes and exists
}
```

当我们进入搜索模式时，我们的组件将呈现，设置输入到现在存在的 DOM 中的实际`HTMLElement`，这将触发我们的焦点代码。

# 调试失败错误

有时，汇总将无法解析您新创建的文件并出错:

```
Debug Failure. False expression: Expected fileName to be present in command line
```

如果你得到这个，只需停止你的开发服务器，然后用`yarn dev`重启它

此外，如果一些更改在您的浏览器中似乎没有生效，您可能需要再次保存您正在处理的文件或再次保存`App.svelte`。

这种情况在我身上发生过几次，不确定是什么原因造成的。

# 设置键盘事件监听器以触发文件搜索模式

当用户按下`t`键时，我们将进入文件搜索模式，当用户按下`Escape`键时，我们将允许他们退出。

修改`App.svelte`

我们使用`onMount`生命周期方法，当我们的组件第一次挂载时，我们会被调用一次。

我们在`keyup`事件上设置我们的事件监听器，并将其传递给我们的事件处理程序，它将负责检查哪个键被按下，并相应地设置搜索模式状态。

如果是`t`或`T`，我们通过将`searching`设置为`true`进入搜索模式，如果是`Escape`，我们将其设置为`false`退出搜索模式。

然后，我们更新模板，只在`searching`和`{#if searching}`在一起时才呈现我们的文件搜索组件

也可以返回一个清理函数，在这种情况下，我们使用它来确保当我们的组件被卸载时，我们也删除了我们的事件监听器。

在这种情况下，`App`永远不会被卸载，除非我们关闭选项卡。

现在，想象一下，如果使用`onMount`注册事件监听器的组件是元素可以改变的列表的一部分。

当列表更新时，它所呈现的组件将会改变，这意味着新的组件将会被挂载，而那些不再需要的组件将会被卸载。

每次，新的事件侦听器将在装载时注册，如果您进行了清理，旧的事件侦听器将在卸载组件时被删除。

但是，如果您没有清理您的事件侦听器，那么每次安装新组件时，都会添加新的事件侦听器，而不会删除现在不需要的旧事件侦听器。

这会造成内存泄漏，因为每次我们得到一个`keyup`事件时，所有注册的监听器都会运行，即使注册它们的组件已经不存在。

从那以后，你的应用程序的性能严重下降只是时间问题。所以别忘了打扫卫生！

# 显示搜索结果

现在，无论如何，我们总是显示完整的文件列表。我们需要改变这一点，以便当我们搜索时，我们显示我们的`fileSearchResults`，否则显示完整的列表。

因为我们的搜索结果已经从通过我们的`fileSearchResults`存储的搜索输入中获得，所以当我们处于文件搜索模式时，我们可以简单地将完整列表替换为`fileSearchResults`存储的值。

现在搜索可以工作了！

# 关于卸载组件时的清理

这一部分只是为了演示，如果你想知道不清理会发生什么。请在尝试之后删除下面的代码。

将这段代码添加到`FileItem.svelte`中，查看不清理事件侦听器的行为

打开 devtools 控制台，用`t`开始搜索

在搜索中键入一些内容并删除它，以更新我们的列表并安装/卸载组件。

重复几次，然后清空你的控制台并按键。

您将看到一个 keyup 事件现在可以触发比我们拥有的组件更多的回调调用。这很快就达到了一个点，一个`keyup`事件触发了数百个回调调用，尽管我们从来没有超过 13 个`FileItem`组件。

现在添加清理代码，并重试上述过程

不再打无用的电话。请记住，事件侦听器不是唯一需要清理的东西，商店订阅可能是另一个例子。

Svelte 商店的`$`语法的好处在于它还可以自动处理商店的退订。

# 添加搜索词高亮显示

我们可以修改`fileSearchResults`来返回带有匹配搜索词的 HTML 标记的文件名。

我们通过在`<mark></mark>`标签之间包装搜索词来做到这一点

首先，当我们的搜索不为空时，我们创建与我们的`$fileSearch`术语匹配的`Regexp`。我们将`gi`作为第二个参数传递给`Regexp`，这些标志将使我们的`Regexp`全局匹配`g`，并且不区分大小写`i`

在我们的过滤器返回我们的搜索结果之后，我们`map`遍历每个结果，并返回我们的原始文件，但除了一个新的名称，我们的搜索词现在将被包装在`<mark></mark>`标签之间。

例如，搜索“readme”会将文件名`README.md`替换为`<mark>README</mark>.md`

如果你现在尝试这样做，你会看到没有高亮显示，只有一堆标签在我们的文件名里面。

这是因为 Svelte 和大多数前端框架一样，默认情况下会对 HTML 标记进行转义。

我们需要修改我们的`FileItem`组件来将我们的`file.name`呈现为 HTML，因为它现在可能包含 HTML 标记。

在 Svelte 中，这是通过`@html`指令完成的

现在我们有了高亮显示，因为 Svelte 知道它需要将`file.name`呈现为 HTML。

# 添加键盘导航

首先，让我们添加一条漂亮的消息，一旦进入搜索模式，它将显示可用的键盘控件。

在`components/file-search`内创建`InfoMessage.svelte`

在`App.svelte`中导入它，这样它就会显示在我们搜索的顶部

让我们用箭头键来实现导航，我们将使用一个存储来跟踪当前选择的文件索引。

首先创建一个新的商店`fileIndex.ts`

简单的可写存储将我们的索引保存为一个`number`，我们将其初始化为 0，因此它指向列表的第一个文件。

现在为`App.svelte`中的箭头键添加监听器

我们导入我们的`fileIndex`存储，这样我们可以在我们的监听器中更新它。

我们添加了`handleKeyDown`监听器，并将其注册到关于`keydown`事件的文档中。

我们没有使用`keyup`,这样你就不必松开按键继续移动。`keydown`只要按键就会开火。

当我们得到我们的`keydown`事件时，`handleKeyDown`检查被按下的键是`ArrowUp`还是`ArrowDown`，并相应地更新`fileIndex`存储。

在`ArrowDown`上，我们增加我们的索引并使用模`%`操作符，这样当我们到达搜索结果的长度时，我们将我们的索引重置为 0，因此当我们到达最后一个文件并按下时，我们返回到列表的顶部。

我们对`ArrowUp`做同样的事情，检查我们是否得到一个负值，然后将我们的索引重置为结果中的最后一个文件。

这样，导航将无缝地循环，我们将不会超出界限错误的索引。

注意，我们现在也将`searching`作为道具传递给`FileList`，因为我们只想在搜索模式下在文件间导航。

让我们修改我们的`FileList`来接受`searching`属性，并将`active`属性传递给我们的`FileItem` 组件。

我们导入我们的`fileIndex`存储，这样我们就可以读取它的值，并将一个`index`参数添加到我们的`{#each file as file, index (file.id)}`块中。

当我们的`fileIndex`等于当前的`index`并且我们处于搜索模式时，我们传递一个新的`active`道具给`FileItem`也就是`true`，由新的`searching`道具给出。

现在让我们用这个道具给我们选中的`FileItem`应用一个浅蓝色的背景

我们添加了`active`作为道具，并使用`class:className={condition}`语法来应用一个条件类，这个类只有在`condition`为真时才会被添加。

在这种情况下，这看起来像这个`class:bg-blue-200={active}`

现在箭头键导航工程！

只有一件小事，如果你搜索什么，然后导航，然后改变搜索，我们的索引不会重置为 0。

要解决这个问题，只要修改我们的`fileSearchResults`存储，每当搜索改变时就重置`fileIndex`

我们已经导入了我们的`fileIndex`商店并添加了`fileIndex.set(0)`，因为每次`$fileSearch`或`$files`改变时都会运行。

# 更新 postcss 配置，使我们的图标不会在生产中消失

如果你现在就开始生产，你会注意到当你访问应用程序时，图标不再出现。

这是因为我们正在使用`purgecss`从 CSS 包中移除未使用的类。

然而，`svelte-awesome`生成了一个`fa-icon`类，它被`purgecss`从我们的产品包中移除，因为它没有出现在我们的代码中。

我们需要将这个类列入白名单，这样它就不会从我们的 CSS 包中删除

在`postcss.config.js`中，你会看到一个只包含`/svelte/`的`whitelistPatterns`数组，你也需要在那里添加`/fa-icon/`。

# 将包大小和加载时间与 React + Moment.js 版本进行比较

我想强调的是，这种比较可能并不能说明苗条和反应之间的差异。

这是因为我们使用的`date-fns`比`moment`轻 3 倍。所以不清楚有多少是由于这种差异，有多少是由于苗条/反应差异。

我对我们的应用程序做了一些修改，因为我们没有使用`global.css`文件，我删除了它并移除了`public.html`中的导入

我添加了一个`Header`组件，只显示“Github 文件搜索”来更精确地匹配 React 应用程序。

我将使用 devtools 的网络选项卡，选中“禁用缓存”和匿名模式来确保扩展不会影响任何东西。

让我们从包的大小开始

React 版本提供了两个 javascript 文件，一个主 javascript 文件(1.9 kB)和一个块(58.7 kB)，一个主 CSS 文件(878 B)和 HTML 文件(1.1 kB)，总共 62.5 kB

苗条版提供一个 javascript 文件(12.3 kB)，一个 CSS 文件(1.3 kB)和一个 HTML 文件(343 B)，总共大约 14 kB

因此，我们的超薄应用程序减少了 48.5 kB，总重量减轻了 77.6%

现在让我们看看它是如何影响加载时间的

我通过网络选项卡的负载值对每个应用程序采取了 10 项措施，我还删除了异常值，因为当托管平台需要比平时更多的时间来响应时会出现异常值。

这意味着 React 应用程序通常在 100 到 200 毫秒之间加载，而 Svelte 应用程序在 50 到 60 毫秒之间加载。

React 应用的平均时间为 167 毫秒

苗条应用的平均时间是 55 毫秒

因此，苗条/日期-fns 应用程序的加载速度比反应/时刻应用程序快 3 倍。

这些都是很好的结果，但在一个更大的应用程序中用相同的第三方库进行比较会更有趣。

# 结论

你可以在这个 [github repo](https://github.com/skflowne/github-file-search-svelte-ts) 中找到完整的教程代码