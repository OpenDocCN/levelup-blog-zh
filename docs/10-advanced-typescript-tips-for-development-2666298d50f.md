# 用于开发的 10 个高级打字技巧

> 原文：<https://levelup.gitconnected.com/10-advanced-typescript-tips-for-development-2666298d50f>

## 高级打字技巧

![](img/e49aa01b0af74fb8ccfc50d856d4d6d5.png)

在使用了一段时间 Typescript 之后，我深深的感受到了 Typescript 在中大型项目中的必要性。可以提前避免很多编译期的 bug，比如烦人的拼写问题。而且越来越多的包都在用 TS，学习它势在必行。下面是我在工作中学到的一些更实用的打字技巧。

## 1.keyof

`keyof`和`Object.keys`略有相似，只是`keyof`拿的是界面的按键。

```
interface Point {
    x: number;
    y: number;
}// type keys = "x" | "y"
type keys = keyof Point;
```

假设我们有一个如下所示的`object`，我们需要使用 typescript 实现一个`get`函数来获取其属性值。

```
const data = {
  a: 3,
  hello: 'max'
}function get(o: object, name: string) {
  return o[name]
}
```

一开始我们可能是这样写的，但是它有很多缺点:

1.  无法确认返回类型:这将失去 ts 的最大类型检查能力
2.  无法约束键:可能会出现拼写错误

在这种情况下，可以使用`keyof` 来增强`get` 函数的`type` 函数，有兴趣的可以看看`_.get`的类型标签及其实现。

```
function get<T extends object, K extends keyof T>(o: T, name: K): T[K] {
  return o[name]
}
```

## 2.必需&部分&挑选

现在你知道了`keyof`，你可以用它对属性做一些扩展，比如实现`Partial` 和`Pick`，`Pick` 一般用在 `_.pick`中

这些类型内置于 Typescript 中。

## 3.条件类型

它类似于。:运算符，可以用它来扩展一些基本类型。

## 4.从不&排除&忽略

never 类型表示从不出现的值的类型。

结合`never` 和`conditional` 类型可以引入很多有趣有用的类型，比如`Omit`

```
type Exclude<T, U> = T extends U ? never : T;// Equivalent to: type A = 'a'
type A = Exclude<'x' | 'a', 'x' | 'y' | 'z'>
```

结合`Exclude`可以介绍一下 Omit 的写作风格

## 5.类型 of

顾名思义，typeof 表示采用某个值的类型，下面的示例显示了它们的用法

```
const a: number = 3// Equivalent to: const b: number = 4
const b: typeof a = 4
```

在一个典型的服务器端项目中，我们经常需要在`context`中填充一些工具，比如 config、logger、db models、utils 等。，然后使用`typeof`。

## 6.是

在此之前，我们先来看一个`koa`错误处理流程。这里是集中`error`处理和识别`code`的过程

在`err.code`处，它将编译一个错误，即属性`‘code’`在类型 `‘Error’.ts(2339)`上不存在。
在这种情况下，你可以使用`as AxiosError`或者`as any`来避免错误，但是强制类型转换不够友好！

在这种情况下，可以使用 is 来确定值的类型。

在 GraphQL 源代码中，有许多这样的用法来标识类型。

## 7.接口和类型

接口和类型有什么区别？这里的[可以参考](https://stackoverflow.com/questions/37233735/interfaces-vs-types-in-typescript)。

接口和类型的区别很小，比如下面两种写法都差不多。

`interface` 可以如下合并，而`type` 只能使用&类链接。

## 8.记录&字典&多

这些语法糖是从`lodash`的类型源代码中学习来的，通常在工作场所中使用得相当频繁。

## 9.用常量枚举维护常量表

## 10.VS 代码提示和类型脚本命令

有时候用 vs 代码，用`tsc` 编译时出现的问题与 VS 代码提示的问题不匹配
找到项目右下角的`Typescript` 字样，版本号显示在右侧，你可以点击它，选择`Use Workspace Version`，表示它始终与项目所依赖的`typescript` 版本相同。

或者编辑。`vs-code/settings.json`

```
{   
    "typescript.tsdk": "node_modules/typescript/lib" 
}
```

简而言之，TypeScript 增加了代码的可读性和可维护性，并使我们的开发更加优雅。如果你对我的文章感兴趣，可以关注我的[媒体](https://hyhwell.medium.com/)或者[推特](https://twitter.com/Maxwell_hyh)。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看更多内容请参见[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**将像你这样的开发人员安置在顶级创业公司和科技公司**](https://jobs.levelup.dev/talent/welcome?referral=true)