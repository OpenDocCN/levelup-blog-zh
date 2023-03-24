# Typescript 4.9 的概要

> 原文：<https://levelup.gitconnected.com/a-rundown-of-typescript-4-9-4858ccf3daea>

![](img/ccab24cda3e83c6fdd972cf3038debb7.png)

一张画在纽约一座摩天大楼上的蓝色背景数字 4 的照片

Typescript 让生活更美好。我爱 Typescript，Typescript 4.9 在这里。随之而来的新特性将使你的代码在类型安全的同时更容易理解。

# 满足

当我们有一个匹配一种类型但也可能匹配另一种类型的表达式时，新的满足操作符用于解决这个问题。一个例子可能是当我们有一个相同数据的对象，但以不同的方式表示。

```
type RGB = [number, number, number];

const palette: Record<"red"|"blue"|"green", string | RGB> = {
  red: [255,0,0],
  green: "#00ff00",
  blue: [0,0,255]
}
palette.green.toUpperCase(); // error! 😱
```

代码很好，但是，问题是颜色记录的类型注释。当我们试图把`colours.green.toUpperCase()`当作一个字符串来使用时，它会抛出一个错误。我们可以看到它是一个字符串，我们的编译器不能。

我们可以完全删除类型……但这会造成一个允许代码中出现类型错误的环境。

或者，我们可以添加条件语句来绕过它。但这是膨胀，它是混乱的。

```
type RGB = [number, number, number];

const palette: Record<"red"|"blue"|"green", string | RGB> = {
  red: [255,0,0],
  green: "#00ff00",
  blue: [0,0,255]
}
if (typeof palette.green === "string") {
   palette.green.toUpperCase(); // 🤮
}
```

相反，我们可以使用满足运算符来确保根据类型的值来正确推断类型。

```
type RGB = [number, number, number];

const palette = {
  red: [255,0,0],
  green: "#00ff00",
  blue: [0,0,255]
} satisfies Record<"red"|"blue"|"green", string | RGB>

palette.green.toUpperCase(); // 😁
```

在传入的数据可能有多种类型的情况下，比如您正在构建一个框架或处理一个 API 调用，并且想要推断响应中的类型，这是一个好方法。

注意:

> *记录是 Typescript 中的一种特殊类型，它实现了下面的签名* `*Record<keys, Type>*` *。它意味着这些可能的键和可能的类型的对象。当一个对象的键可能意味着多种类型时，这很有用。我知道这很疯狂。*

[满足运算符](https://devblogs.microsoft.com/typescript/announcing-typescript-4-9-rc/#the-satisfies-operator)

# 未列出的属性缩小—在

当你不确定函数使用的变量类型时，可以使用新的`in`操作符。例如:

```
interface Context {
  packageJSON: unknown;
}

function tryGetPackageName(context: Context) {
 const packageJSON = context.packageJSON;
 if (packageJSON && typeof packageJSON === "object") {
  return pacakgeJSON.name; // error Name doesn't exist on unknown
 }
 return undefined;
}
```

为了解决这个问题，我们可以使用`in`操作符来构建类型并增加类型安全性。当您试图从未知输入中构建一个类型时，这很有用。

```
interface Context {
  packageJSON: unknown;
}

function tryGetPackageName(context: Context) {
 const packageJSON = context.packageJSON;
 if (packageJSON && typeof packageJSON === "object") {
  if("name" in packageJSON && typeof packageJSON.name === "string") {
    return pacakgeJSON.name;
  }
 }
 return undefined;
}
```

[向内缩小](https://devblogs.microsoft.com/typescript/announcing-typescript-4-9-rc/#in-narrowing)

# ECMAScript 自动访问器

新的`accessor`关键字可以在类中用来自动生成 getters 和 setters。创建的属性将是私有的，因此用户无法访问。

```
class Person {
  accessor name: string;

  constructor(name: string) {
    this.name = name;
  }
}
```

[支持 Stage 3 Decorators 提案中的自动访问器字段](https://github.com/microsoft/TypeScript/pull/49705)

# 文件监视事件(速度提高)

Typescripts 内部现在使用文件系统事件来监视文件。这意味着使用 Typescript 进行代码编辑的应用程序应该有显著的速度提高。以前的 Typescript 使用轮询来监视单个文件，这意味着它将定期检查更新，如果有大量文件，这可能是一项昂贵的任务。

与轮询不同，Typescript 现在将使用文件系统事件来侦听特定文件中的更新，并且只在它们实际发生更改时得到通知。这应该有助于 Monorepos 持有的大得多的项目。

[文件查看现在使用文件系统事件](https://devblogs.microsoft.com/typescript/announcing-typescript-4-9-rc/#file-watching-now-uses-file-system-events)

你可以在 [https://matty.dev](https://matty.dev) 阅读更多类似内容

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看更多内容请参见[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)