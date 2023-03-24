# Kotlin 的门票——使用 Dukat 连接 Kotlin 和 JS 世界

> 原文：<https://levelup.gitconnected.com/ticket-to-kotlin-using-dukat-to-bridge-kotlin-with-the-js-world-431a2458b95c>

![](img/8e65109583c830ba1dc309c4c4881964.png)

由[塞尔吉奥·卡西利亚斯](https://unsplash.com/@sergec?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

Kotlin 是由动态类型 JavaScript 语言主导的 web 开发生态系统中的新人。这个生态系统不会在一两年内神奇地获得静态类型，所以 web 开发人员必须面对这个事实，将两个世界连接起来。科特林站在巨人的肩膀上，获得他们的好处，弥补他们的弱点，我们可以看到同样的模式也适用于这里。TypeScript 定义现在是 JavaScript 世界中静态类型的事实上的标准。这些定义要么与 npm 模块一起发布，要么发布到社区支持的[明确类型化的](http://definitelytyped.org/)项目中。有一个叫做 [Dukat](https://github.com/Kotlin/dukat) 的工具可以将 TypeScript 定义文件转换成 Kotlin 外部声明文件。因此，任何 Kotlin JS 项目都可以重用任何 JavaScript 库的 TypeScript 定义，以静态类型的方式使用它。

# 奔跑的杜凯特

有关 Kotlin 外部声明语法的详细信息，请参考 [Kotlin 文档](https://kotlinlang.org/docs/reference/js-interop.html)，这里我将重点介绍我使用 Dukat 的经验。TypeScript 和 Kotlin 是不同的，在很多情况下，TypeScript 声明不能逐字映射到 Kotlin。所以在使用生成的 Kotlin 外部声明时有一些问题。Dukat 工具现在正在积极开发中，文档很少，事情进展很快。我使用了 [Dukat 0.5.0](https://github.com/Kotlin/dukat/releases/tag/v0.5.0) 来为 [google-map-react](https://github.com/google-map-react/google-map-react) 库和 [Google Maps API](https://developers.google.com/maps/documentation/javascript/tutorial) 本身生成 Kotlin 外部声明(有关在 web 应用程序中使用 Google Maps 的更多细节，请参见本系列的[上一篇文章](https://medium.com/@kiryushin.andrey/ticket-to-kotlin-using-google-maps-in-react-web-application-745d8fb0ab08))。使用 Dukat 的工作流程如下:

*   本地安装 Dukat。它作为 npm 模块发布，所以安装它最简单的方法是运行`npm install -g dukat`。
*   下载 TypeScript 定义以从中生成 Kotlin 声明。你可以安装 npm 包，克隆一个 GitHub repo，或者只是下载`.d.ts`文件——这都没关系。我安装了来自`[@types/google-map-react](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/google-map-react/index.d.ts)`和`[@types/googlemaps](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/googlemaps)` npm 包的类型定义，然后在各自的`npm_modules`子文件夹中运行 Dukat。
*   运行 Dukat，指定一个或多个`d.ts`文件作为输入；
*   将生成的 Kotlin 文件复制到您的项目中；
*   去掉不必要的东西，调整生成的外部声明，以便它们可以编译并满足您的需要。

# 这些生成的文件是什么？

Google Maps API 的 TypeScript 定义由一堆用于 API 不同部分的`d.ts`文件组成，一个简短的`index.d.ts`文件引用了所有这些文件。Dukat 识别并遵循那些[引用指令](https://www.typescriptlang.org/docs/handbook/triple-slash-directives.html)，所以我能够通过运行`dukat index.d.ts`获得所有的外部声明。我得到了一堆与原始 TypeScript 定义文件不一一匹配的文件，并对此感到有点困惑。结果文件的名称也有点不同——它们都以前缀`style-reference`开头。所以让我们弄清楚 Dukat 生成什么文件。

首先，Dukat 为一些 [TypeScript 标准库声明](https://mariusschulz.com/blog/built-in-type-declarations-in-typescript)生成一堆`lib.*.kt`文件。如果您需要这些模块中包含的一些内置类型，这些是有用的；然而，这些声明文件相当大，我建议清除其中不必要的内容，甚至完全删除它们，这样这些文件就不会降低项目的编译速度。所有这些`lib.*.kt`文件都包含了对`tsstdlib`包的声明，并且没有被任何为 Google Maps API 本身生成的声明所引用。我想几乎所有库的 TypeScript 定义都会是这种情况，我想知道为什么 Dukat 总是在每次运行时输出这些标准库声明，而不是按需输出。

标准库已经过时了，让我们看看 Dukat 生成的其他文件。Dukat 不仅为输入定义文件生成 Kotlin 声明，还为以 [import](https://www.typescriptlang.org/docs/handbook/modules.html#import) 语句或 [reference](https://www.typescriptlang.org/docs/handbook/triple-slash-directives.html) 指令的形式指定的所有依赖项生成 kot Lin 声明(阅读 [this](https://www.typescriptlang.org/docs/handbook/namespaces-and-modules.html#pitfalls-of-namespaces-and-modules) 以了解两者之间的区别)。依赖关系路径是相对于当前目录解析的。Dukat 还会从当前目录开始在目录树中搜索一个`tsconfig.json`文件，如果找到了，就会考虑进行依赖关系解析——这就是为什么在不同的文件夹中用相同的输入运行 Dukat 会得到不同的结果。

Dukat 为每个 Kotlin 包生成一个文件，它对应于 TypeScript 中的一个名称空间。如果所有这些文件都包含对单个命名空间的声明，则可以为多个 TypeScript 定义文件获取单个 Kotlin 文件；如果一个 TypeScript 定义文件包含几个不同的命名空间，则可以为该文件获取多个 Kotlin 文件。生成的 Kotlin 文件的名称构造如下:

*   首先是没有扩展名`d.ts`的原始 TypeScript 定义文件的名称。单个 Kotlin 包(TypeScript 命名空间)的声明可能驻留在多个 TypeScript 定义文件中；在这种情况下，使用 Dukat 遇到的第一个文件的名称。这就是为什么为 Google Maps API 生成的所有 Kotlin 文件都以前缀`style-reference`开始——`index.d.ts`中的第一个引用指令指向`style-reference.d.ts`文件，而这个文件又声明了`google.maps`名称空间，最终包含了 API 所有部分的所有声明和嵌套名称空间。我从生成文件的名称中去掉了这个前缀，因为它在这种情况下没有多大意义。
*   生成的文件名的第二部分是 TypeScript 命名空间本身。这与该文件顶部声明的包名相同。如果原始的 TypeScript 定义文件在一个全局名称空间中导出它的所有定义，那么这一部分将被省略(在这种情况下，Kotlin 文件也没有`package`指令)。
*   第三部分是从库的`package.json`文件中提取的扁平模块名，并以`module_`为前缀，如`module_googlemaps`。只有当`package.json`出现在当前目录或目录树中的某个位置时，该部分才会出现。

最后，您可能会找到一个或多个名为`nonDeclarations.<package name>.nonDeclarations.kt`的文件。这类文件包含[类型别名](https://kotlinlang.org/docs/reference/type-aliases.html)，中间的包名对应于 TypeScript 名称空间，在这里可以找到相应的 TypeScript 定义。Dukat 将生成的类型别名提取到一个单独的 Kotlin 文件中，因为类型别名不是一个外部声明，因此它不能驻留在标有`[JsQualifier](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.js/-js-qualifier/)`注释的文件中。如果生成的 Kotlin 文件包含一个`package`指令(也就是说，如果它包含某个 TypeScript 名称空间的声明)，那么这个注释就会出现在该文件中。

有了这些知识，我们现在可以理解为 Google Maps JS API 生成的所有这些文件:

```
nonDeclarations.google.maps.nonDeclarations.kt                                                                                                                style-reference.google.maps.module_googlemaps.kt                                                                                                              style-reference.google.maps.event.module_googlemaps.kt                                                                                                        style-reference.google.maps.geometry.encoding.module_googlemaps.kt                                                                                            style-reference.google.maps.geometry.spherical.module_googlemaps.kt                                                                                           style-reference.google.maps.geometry.poly.module_googlemaps.kt                                                                                                style-reference.google.maps.drawing.module_googlemaps.kt                                                                                                      style-reference.google.maps.visualization.module_googlemaps.kt                                                                                                style-reference.google.maps.places.module_googlemaps.kt                                                                                                       style-reference.google.maps.adsense.module_googlemaps.kt                                                                                                      lib.dom.kt                                                                                                                                                    lib.es2015.iterable.kt                                                                                                                                        lib.es5.kt                                                                                                                                                    lib.es5.Intl.module_dukat.kt                                                                                                                                  lib.scripthost.kt                                                                                                                                             lib.es2015.collection.kt
```

第一个是`nonDeclarations.google.maps.nonDeclarations.kt`，它包含一组来自`google.maps` TypeScript 名称空间的类型别名。然后是一堆文件名都以`style-reference`前缀开始，以`module_googlemaps`后缀结束的文件。我们知道这个前缀是由于 Dukat 找到的第一个声明`google.maps`名称空间的文件，也就是`style-reference.d.ts`。而`module_googlemaps`后缀是因为`package.json`文件中指定的`@types/googlemaps`包名(Dukat 从包名中去掉了`@types`)。后缀和前缀之间的所有内容都是实际的 Kotlin 包名，我只保留了名称的这一部分，以便在将生成的文件复制到我的游戏项目时减少混乱。最后，我们有一堆标准库定义文件，我也把它们去掉了。在将生成的声明复制到我的项目结构中之后，我还清理了那些我不需要的 Google Maps API 部分的文件——并没有过分热衷于此，只是浏览了一下文件列表及其内容，并删除了我在游戏中明显不需要的东西，如 AdSense 或数据可视化支持。

为 google-map-react 生成的文件更有趣一些，因为这个库有一些外部依赖项，包括 react 本身:

```
nonDeclarations.React.nonDeclarations.kt                                                                                                                      index.module_google-map-react.kt                                                                                                                              lib.dom.kt                                                                                                                                                    lib.es2015.iterable.kt                                                                                                                                        lib.es5.kt                                                                                                                                                    lib.es5.Intl.module_dukat.kt                                                                                                                                  lib.scripthost.kt                                                                                                                                             lib.es2015.collection.kt                                                                                                                                      index.module_react.kt                                                                                                                                         index.React.module_react.kt                                                                                                                                   index.module_prop-types.kt                                                                                                                                    index.module_csstype.kt
```

`index.module_google-map-react.kt`包含库本身的外部声明，并且是我们需要的唯一文件。我们可以看到它的名字由原始的 TypeScript 文件名(`index.d.ts`)和取自`package.json`的模块名`google-map-react`组成。因此，我们可以得出结论，这个库的所有声明都包含在一个全局名称空间中，实际上，`index.d.ts`中没有名称空间声明，生成的 Kotlin 文件中也没有`package`语句。

原始的 TypeScript 定义文件导入了`React`模块，所以 Dukat 通过在兄弟`node_modules`子文件夹中发现`@types/react`模块并为其生成 Kotlin 外部声明来遵循这个引用。这产生了`index.module_React.kt`和`index.React.module_react.kt`文件——前者用于全局名称空间声明，后者用于来自`React`类型脚本名称空间的声明。但杜凯特并没有就此止步。React 的 TypeScript 定义导入了`csstype`和`prop-types`模块，我们可以看到 Dukat 已经发现了它们的类型定义，并将它们也转换成了 Kotlin。

# 使用 Kotlin 基于 React 的库

到目前为止，Dukat 已经为我们提供了库的完整 Kotlin 外部声明集，包括它能够发现的所有库的外部依赖项。但是在 Kotlin 中使用 React 有一个比依赖 Dukat 生成的外部声明更好的选择。React 库有一个 [Kotlin 包装器，为 React 库提供了一个方便的接口，因为它是用 Kotlin 手写的。当然，我希望为 google-map-react 生成的绑定使用 react 包装器库，而不是自动生成的 React 外部声明。为此，我必须手工更新声明。幸运的是，这并不难——我只需像这样更新 React 组件类声明](https://github.com/JetBrains/kotlin-wrappers/tree/master/kotlin-react)

```
@JsName("default"**)**
**external open class** GoogleMapReact : Component<Props, RState> **{**
    **override fun** render(): ReactElement?
**}**
```

并且还从 Kotlin-react 库中声明的`[react.RProps](https://github.com/JetBrains/kotlin-wrappers/blob/master/kotlin-react/src/main/kotlin/react/ReactComponent.kt#L7)`接口继承生成的`Props`接口。这样做之后，我删除了所有生成的文件，只留下了我重命名并复制到我的项目结构中的`index.module_google-map-react.kt`。

# 几个陷阱

## 带点的名称空间

我必须修改一些类和接口引用，以便生成的声明可以编译。当驻留在`google.maps`包的子包中的某个声明引用了父包中的某个类型时，出于某种原因，Dukat 生成了带有`maps.`限定符的引用，如下所示:

```
**package** google.maps.event
...
// MapsEventListener is defined in google.maps namespace **external fun** removeListener(listener: maps.MapsEventListener)
```

这个没有编译。我不得不完全限定引用名，比如`google.maps.MapsEventListener`，或者移除限定符并引入一个合适的类型导入。我选择了后者，并向 Dukat repo 提交了一个 [bug](https://github.com/Kotlin/dukat/issues/304) 。

## 名称与嵌套在类中的接口冲突

我还必须修复`google.maps.Data`类中的`MouseEvent`接口定义。生成的 Kotlin 文件中的以下代码片段说明了这个问题:

```
**namespace** google.maps **{** **interface** MouseEvent **{** ... **}** **class** Data **{** ... **}**
    **namespace** Data **{**
        **interface** MouseEvent : MouseEvent **{ ... }
    }
}**
```

这样的`MouseEvent`接口定义显然导致编译器抱怨继承层次中的循环。在深入研究了原始的 TypeScript 定义一段时间后，我发现继承的接口嵌套在`google.maps.Data`类中，而基接口直接在`google.maps`命名空间中声明。所以这两个接口之间存在名称冲突，Dukat 没有正确解决，导致了不可编译的 Kotlin 代码。我以完全限定的方式通过引用基础`MouseEvent`接口修复了它，这里是提交给 Dukat repo 的 [bug](https://github.com/Kotlin/dukat/issues/303) 。

## “this”作为 TypeScript 中的泛型类型参数

另一个编译错误是由于 Kotlin 和 TypeScript 泛型本质上是不同的。让我们来谈谈代码:

## 省略了类型变量的泛型类型引用

我在生成的 Kotlin 声明中修复的最后一个东西是一个名字奇怪的类`Map__0`。它充当泛型`Map`类的非泛型别名:

```
**external open class** Map<E : Element>
...
**external open class** Map__0 : Map<Element>
```

初始 TypeScript 定义文件中不存在此别名。TypeScript 允许在引用泛型类型时省略类型参数，让编译器来推断。另一方面，Kotlin 不允许在引用泛型类时省略类型参数。Dukat 生成这样一个别名类，并在 TypeScript 中相应的类型引用省略了类型参数时引用它而不是原始的泛型类型。因此，生成的 Kotlin 声明在原始 TypeScript 定义包含对不带任何类型参数的泛型`Map`类型的引用的所有地方都使用了`Map__0`。严格来说，这并不需要修复；不过，`Map__0`这个名字看起来有点怪，所以我把它的所有用法都换成了`Map<Element>`，然后把`Map__0`这个类也掉了。我不确定为什么 Dukat 不遵循同样的方法，而是生成一个合成类；也许我遗漏了一些东西，但这并不是在所有情况下都是可能的。

# 关于使用 Dukat，我学到了什么

这些都是我必须对 Dukat 生成的 Kotlin 外部声明进行的修改，以使它们能够工作。有一些问题，但总的来说，这个过程相当简单明了。只需查看原始的 TypeScript 定义，并在需要时咨询 TypeScript 文档以决定如何修复声明，以防您对它们不满意。还要注意，只要 Dukat 不能合理地表示 Kotlin 中的某些类型脚本类型，它就会向生成的文件中写入注释，如果您在处理生成的声明时遇到困难，这些注释会非常有用。这些注释包含 TypeScript 中使用的原始类型引用或声明。我见过这样的评论出现在 Dukat

*   放入一个特定的类型，而不是用作类型参数值的“this”(见上述`addListener`案例)；
*   放入`dynamic`(用于 TypeScript `any`的 Kotlin 模拟)来代替 TypeScript [元组](https://www.typescriptlang.org/docs/handbook/basic-types.html#tuple)类型(您将在 Kotlin 的注释中看到`JsTuple<A, B, C>`)；
*   放入`dynamic`或一些基本类型来代替 TypeScript [区别联合](https://www.typescriptlang.org/docs/handbook/advanced-types.html#discriminated-unions)(注释列出了所有区别联合选项，用竖线隔开)

如果您发现自己在处理来自生成的外部声明的`dynamic`类型，您可能会希望转到声明文件，用某个特定的类型替换`dynamic`——这很容易实现，因为这些注释给了您一些提示。例如，如果属性在原始 TypeScript 定义中有一个可区分的联合类型，那么只需选择您想要使用的选项并更新 Kotlin 声明来使用它，而不是使用`dynamic`。

我也看到过在 Google Maps API 类型定义中枚举被表示为不同的联合。Dukat 不会为这种情况生成 enum 相反，它推断出基类型并使用它来替换有区别的联合:

```
// TYPESCRIPT
**type** GestureHandlingOptions = 'cooperative' | 'greedy' | 'none' | 'auto';// KOTLIN
**var** gestureHandling: String? */* 'cooperative' | 'greedy' | 'none' | 'auto' */*
```

在这种情况下，您可能希望创建 enum 来为代码增加更多的编译时安全性。

在更多的情况下，您可能希望调整生成的 Kotlin 定义，以使它们可以编译，或者增加更多的类型安全性。在开始使用这个库之前，先熟悉这个库的 TypeScript 定义和生成的 Kotlin 声明。您将在这上面花费一些时间，但是作为回报，您将获得一个更加稳定和可预测的静态类型库接口。

# 结论——我对 Kotlin vs TypeScript 的两点看法

TypeScript 定义不是原始 JavaScript 实现的组成部分，因此它们可能不准确或不完整。将 TypeScript 声明转换为 Kotlin 是另一个需要考虑的步骤，增加了复杂性，在某些情况下甚至会使生成的 Kotlin 声明不如原始的 TypeScript 定义严格。因此，一个问题可能会自然出现——即使您更喜欢静态类型，为什么不用 TypeScript 编写您的 web 应用程序呢？为什么要处理所有这些额外的复杂性？这当然取决于具体情况，没有唯一正确的答案，但我在下面提出了我的一些考虑。

TypeScript 是 JavaScript 的超集，允许向 JavaScript 代码添加类型。它支持逐步采用，并声明 JavaScript 代码也是有效的类型脚本代码。这种逐渐的采用肯定会让 TypeScript 传播开来并获得势头。不幸的是，我也看到这种“部分”类型被来自 JavaScript 背景的开发人员大量滥用，他们在编写类型脚本代码时只摘容易摘到的果实。当需要一些重构来向现有的代码库引入强类型时，或者当他们手头的任务只有一个动态类型的解决方案而不想为类型而烦恼时，他们会迅速求助于`any`。类型推断和由此导致的`any`类型在整个代码库中的病毒式传播进一步加剧了这个问题。在 Kotlin 中，采用动态类型也是可能的，但是不太常见，而且肯定不符合习惯。最终结果是，我见过的任何类型脚本代码库都更容易失去类型严格性，并且意外地无法提供编译类型检查。与使用 TypeScript 代码相比，使用 Kotlin JS 代码库时，我感觉更有信心。

Kotlin 语言的另一个重大胜利是多平台倡议。如果您的应用程序既有后端又有前端需要通信，那么公共语言证明非常方便。至少，你可以共享 API 模型的代码，但是一旦你习惯了用一种语言写所有的东西，你会发现好处不仅仅是 API 模型。一些将被重用的逻辑(例如验证)将会弹出，整个应用程序代码库将会更容易导航、推理和维护。我并不否认后端和前端开发人员之间的专门化，但是为两者提供一种单一的强大的语言，并让开发人员的专业知识在某种程度上重叠，会有所帮助。