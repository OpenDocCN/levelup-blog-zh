# 打字稿:了解基础知识第一部分

> 原文：<https://levelup.gitconnected.com/typescript-understanding-the-basics-part-i-64f7fc478f5f>

![](img/c6851deade728374b6f63ce8e2c42950.png)

理解类型脚本！

“需要是所有创新之母”，这句话最适合当我们谈到打字稿，感到困惑？好吧，请允许我消除神秘感，让我告诉你 Typescript 和上面的引用有什么关系。

“没有什么是完美的”，这似乎也适用于 Javascript，我们强大的 Javascript，可以创造任何奇迹，这就是为什么 Typescript 进入画面来填补 Javascript 本身无法填补的空白。如果你仍然不知道我在说什么，这完全没关系，因为现在我已经做了足够多的文献资料，现在为了给你一个更好的画面，我应该把自己从文献方面切换到编码方面。

因此，在深入研究这个海洋之前，让我们从 Typescript 是什么&为什么开始吧！

# 什么是打字稿

Typescript 是一种编程语言，也是一种强大的工具，它允许我们以一种更加简单和干净的方式编写代码。它为您的 Javascript 代码提供了额外的错误检查。

Typescript 有助于以更好和优化的格式编写代码，如果您需要实现 Javascript，您需要为此编写大量代码。

因为它是建立在 Javascript 之上的，所以我们可以把它看作 Javascript 的超集，它会在检查 Javascript 代码时产生额外的错误。

然而，javascript 有一个问题，你们中的一些人可能已经意识到，Typescript 是 Javascript 的超集，如果我们谈论浏览器，浏览器不理解任何像 Typescript 这样的超集，那么接下来的问题将是浏览器如何编译 Typescript？

好了，读者们，沉住气，答案就在问题中，即。，打字稿。Typescript 不仅是一种强大的工具或编程语言，而且还是一种强大的编译器，可以将 Typescript 代码编译成浏览器能够理解的 Javascript。

## 为什么打字稿？

在很好的理解了什么是 typescript 之后？我们需要理解为什么我们需要使用 Typescript。

答案很简单，因为使用 typescript 背后的想法是为了避免我们在代码中犯的错误，这些错误会导致编译错误或运行时错误。

Typescript 充当 javascript 代码守卫，确保代码的高质量。

除了强大的错误处理功能，Typescript 还提供了许多有趣的特性，比如接口、泛型和元编程特性，比如 decorators。

换句话说，我们可以得出结论，Typescript 是一个非常有用的工具，可以确保更好的编码标准和一些非常有趣的功能，最终有助于实现更好的代码质量和良好的可读性和优化。

## Typescript 入门

经过几分钟的理论学习，现在是我们着手工作的时候了，我们现在可以开始使用 Typescript 了。

嗯，大多数情况下，如果你正在使用 typescript，你不需要设置 Typescript 和它的编译器，因为它将与你正在使用的库一起提供，例如 Reactjs、Angular 等等。但是为了从核心上理解 typescript。感觉从头开始挺好的。

因此，如果您从头开始，那么您需要手动完成所有工作，这就是我们现在要做的。

```
sudo install -g typescript
```

这将全局安装 typescript，typescript 附带其编译器。

这里有一件非常重要的事情需要注意，我们的工作已经完成了一半，因为我们正在从头开始使用 typescript 并设置我们的环境，我们还需要配置编译器。

众所周知，我们的浏览器不理解 typescript，因此我们需要一个能够很好地理解 typescript 并将其编译成浏览器能够理解的东西的编译器。我们也知道 typescript 附带了编译器，所以现在我们只需要运行它。

```
tsc app.ts
```

此命令将编译项目的 app.ts 文件。为了避免每次修改时都碰到这个命令，你可以像这样在监视模式下运行编译器

```
tsc app.ts --watch
```

或者简单地说，

```
tsc app.ts -w
```

好吧，当你只是使用 typescript 和演示时，这看起来不错，但在现实世界中，你可能必须在整个项目中使用 typescript，并且你需要在整个项目中运行 typescript 编译器。

嗯，您不需要担心这个问题，因为 typescript 也有这个问题的解决方案。

要编译整个项目，您需要在您要编译所有。ts 文件，理想情况下，它将是根目录。要创建这个文件，你只需要点击

```
tsc –init
```

此命令将创建一个 tsconfig.json 文件，该文件将包含 typescript 编译器的所有配置。一旦你点击这个命令，你会得到一个类似这样的文件。

这个文件非常简单明了，但是，为了更好地使用 typescript，我们需要深入了解一些事情。我们稍后会谈到它，但首先，我们需要用手表运行这个编译器，不是吗？

```
tsc -w
```

或者

```
tsc --watch
```

## 深入了解 tsconfig

在编译器中给出了很多选项两个选项，我发现在日常生活中了解它们很重要。然而，您几乎不需要在 tsconfig.json 文件中做任何更改，但是为了更好地理解和掌握知识，让我们来理解 tsconfig.json 的一些选项。

**包含和排除文件**

顾名思义，这个选项将包含我们需要编译的文件列表。我们把这个拆成碎片，一个一个来理解。

默认情况下，没有包含和排除这样的选项，因为它已经包含了我们需要编译的所有相关文件，并排除了我们需要排除的所有不需要的文件或文件夹。

**排除**

Exclude 将是一个数组，它包含我们要从编译中排除的文件和目录名。比如说。

```
“exclude”: [“app.ts”];
```

这将使 app.ts 文件不被 typescript 编译器编译。

就像任何其他配置文件一样，我们也可以使用通配符来排除一组文件的编译。

```
“exclude”: [“**/*.dev.ts”];
```

这将排除项目中任何文件夹中具有此类名称的所有文件。

**IMP:** 默认情况下，node_modules 被排除在编译之外，但是如果我们显式地创建 exclude 数组，那么我们需要指定 node_modules 来将其排除在编译之外。

**包括**

换句话说，include 的工作方式与 exclude 完全相反，这意味着如果您在配置文件中创建一个 include 键，您需要添加所有想要编译的文件和文件夹。

```
“include”: [“app.ts”];
```

就像 exclude 一样，我们也可以像这样在 include 中使用通配符，

```
“include”: [“**/*.dev.ts”];
```

**IMP:** 如果我们在 include 中设置了 include 和 exclude，我们在 exclude 中设置了根文件夹及其子文件夹，那么 include 将排除所包含文件夹的所有子文件夹。我们可以这样把这个词等同于谜语

> 编译=包含-排除

如果你想编译整个项目的文件，那么不要设置包含和排除。

就像 include 和 exclude 一样，还有一个名为“files”的键，它允许您包含文件而不是文件夹。

**编译错误时停止发出文件**

默认情况下，typescript 会在出现编译错误时发出文件，但是，如果我们希望在出现编译错误时停止发出文件，那么我们可以将“noEmitOnError”设置为 false。

默认情况下，它被设置为 true。它将允许我们创建一个 JS 文件，尽管在 ts 文件的编译中可能会有一些错误。

**提示:**不要切换这个选项，否则只是想一想使用 typescript 的目的是什么。

还有许多其他选项，更多请参考[正式文档](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)。

## 结论

我想我们可以在这里休息一下，在下一部分，我们将在 typescript 中做一些真正有趣的事情。有大量演示的实际操作。到时候我们可以快速复习一下我们在这里理解的内容。

*   Typescript 增加了类型，我们可以在 TS 中使用下一代 JS 特性，以后会编译成 JS，就像 Babel 一样。
*   它增加了非 JS 特性，如接口或泛型(为了更好的代码标准和错误特性代码)。
*   支持像 Decorators 这样的元编程特性。
*   丰富的配置选项我们可以根据需要配置 TS。
*   如何从 basic with 和 tsconfig.json 文件设置 typescript 项目
*   tsconfig.json 中的一些重要选项

*如果你喜欢这篇文章，请鼓掌欣赏，你可以关注我的* [*【中】*](https://medium.com/@bhavikbamania)*[*推特*](https://twitter.com/bhavikbamania)*[*insta gram*](https://www.instagram.com/bhavikbamania/)*，你的赞赏将激励我写更多！谢谢你的时间，下次再见！***

***原载于 2022 年 8 月 20 日 https://bhavikji.com**的* [*。*](https://bhavikji.com/blog/typescript-understanding-the-basics)**