# 让你成为更好的程序员的 5 个高级打字技巧

> 原文：<https://levelup.gitconnected.com/5-advanced-typescript-tips-to-make-you-a-better-programmer-bd4070aa2ab4>

![](img/0d6395587eb99150e5dcc245626de532.png)

漂亮:)

Typescript 是一种令人惊叹的语言——它允许我们在十分之一的调试时间内完成 JavaScript 所能做的一切。这些提示主要针对:

*   通过编写更明确、更容易理解的代码来减少错误
*   在你的代码中加入更多的价值，而不用重新发明轮子。

如果你已经知道这些，那么恭喜你！你是 TS 的传奇——也许可以在评论中与我分享你的智慧([,并阅读我的另一篇关于 5 个不同技巧的文章](/5-tips-for-better-typescript-code-5603c26206ef)!).

这里有 5 个高级打字技巧，可以让你写出更好的打字代码。

## 1.“是”操作员/类型防护装置

Swagger 非常非常有助于了解后端将为您提供什么服务——但是，更多的时候，程序员会得到糟糕或不一致的 API 来使用，其中属性可能存在，也可能不存在，或者根据状态返回不同的对象。

不幸的是，如果您不知道 API 可能会产生什么结果，就没有办法在编译时捕捉到这些错误，但是我们可以让它变得容易处理(和报告！)在运行时。

API 通常是 typescript 错误的入口点，API 调用结果通常如下所示:

```
const myApiResult = await callApi("url.com/endpoint") as IApiResult
```

或者更糟…

```
const myApiResult = await callApi("url.com/endpoint") as any
```

这两种方法都让编译器闭嘴，但是第一种方法明显比第二种更健壮——事实上，第二种方法只是将您对结果所做的任何事情都变成了 JavaScript 级别的不确定性。

但是如果 API 给了我们一些不是`IApiResult`的东西呢？如果它返回不同的东西，现在我们有一个随机的对象类型被转换为`MyApiResult`会怎么样？这将是糟糕的，并且将 100%导致输入错误。

我们可以使用 TS 型防护装置:

```
interface IApiResponse { 
   bar: string
}const callFooApi = async (): Promise<IApiResponse> => {
 let response = await httpRequest('foo.api/barEndpoint') //returns unknown
 if (responseIsbar(response)) {
   return response
 } else {
   throw Error("response is not of type IApiResponse")
 }
}const responseIsBar = (response: unknown): response is IApiResponse => {
    return (response as IApiResponse).bar !== undefined
        && typeof (response as IApiResponse).bar === "string"
}
```

通过使用`responseIsBar`，我们可以保证不会先发制人地将响应发送给`IApiResponse`，从而防止错误的发生。

在一个实际的用例中，你可能会向用户显示一个类似“从服务器得到了意外的响应，请重试”或类似的错误，而不是`property 'bar' does not exist`。

作为对“is”操作符的一般解释:`value is type`实际上是一个布尔值，当输入 true 时，它告诉 typescript 值…嗯，是类型。

## 2.作为常量/只读

这是一个更简单，更符合语法的糖类型的东西。大多数人都知道，在分配一个接口时，可以写“readonly”来使该属性不可变。

```
interface MyInterface {
  readonly myProperty: string
}let t: MyInterface = {
  myProperty: 'hi'
}t.myProperty = "bye" //compiler err, saying myProperty is Read Only.
```

这很好，直到你最终得到真正的大数据类，可能来自 API 结果。然后在每个属性前都有一个只读的垃圾邮件，只是对于一个简单的数据类。

Typescript 支持在声明后使用“as const ”,这样我们就可以为每个属性添加 readonly。

```
let t = {
 myProperty: "hi" 
 myArr: [1, 2, 3]
} as const 
```

现在，T 的每个属性都是不可变的。例如，`t.myArr.push(1)`不会编译，重新分配`myProperty`同样也不会编译。

我认为这是最有帮助的用例与前面的相同——不过，我们不是返回接口，而是希望代理从 API 调用的对象，并更改一些属性，使其成为数据对象。所以，结合前面的提示:

```
const callFooApi = async () => {
 let response = await httpRequest('foo.api/barEndpoint') //returns unknown
 if (responseIsbar(response)) {
   //filter out unecessary data, do whatever formatting is required
   return {
      firstLastName: [response.firstName, response.lastName]
   } as const
 } else {
   throw Error("response is not of type IApiResponse")
 }
}
```

任何从事这项工作的程序员都会喜极而泣——在返回值上仍然有智能感知(类型是从`response` 变量派生出来的)，但是它是不可变的。我们调用 API，验证响应是我们期望的，然后以一种易于使用的方式返回它。这对所有人都是一个胜利！

作为一个小的补充，如果你想声明类型，但不想只读垃圾邮件，你可以做:`type MyTypeReadonly = Readonly<MyType>`。我们将在第 5 点中更深入地讨论这一点。

## 3.详尽的开关盒

由于切换用例，扩展枚举通常是一件痛苦的事情——在我们打开枚举的任何地方，我们现在都需要添加另一个用例。如果我们错过了一个，我们就太不走运了，我们的程序会进入默认情况(如果有的话)或者失败，经常会导致意想不到的行为。

没有人喜欢无意识的行为。

许多语言通过强制开关情况要么详尽，要么有一个明确的`default`状态来解决这个问题。Typescript 对此没有编译器支持，但是我们可以以这样一种方式创建我们的开关情况，如果我们扩展一个枚举或一个可能的值，我们的程序将不会编译，直到我们显式地处理那个情况。

假设我们有这样的情况:

```
enum Directions {
   Left,
   Right
}const turnTowards = randomEnum(Direction)switch (turnTowards) {
      case Directions.Right:
         console.log('we\'re going right!')
         break
      case Directions.Left:
         console.log('Turning left!')
         break
}
```

即使是最菜鸟的程序员也可以说我们不是左转，就是右转。这里没有必要添加默认语句，这里只有两个枚举。但是请记住，我们编码不仅仅是为了完成任务，而是为了编写可维护的代码！

比如说两年后，一个开发商决定增加一个新的方向:前进。现在，枚举看起来像这样:

```
enum Directions {
   Left,
   Right,
   Forward
}
```

开关盒知道这一点，但它*不在乎。*它会很乐意尝试打开`goingTowards`，但如果遇到前锋，它会很乐意倒下。两年是一段很长的时间，开发者忘记了开关盒的存在。我们可以添加一个抛出错误的默认案例，但是与 compiletime 相比，运行时错误是糟糕的。

所以我们添加了这个默认案例:

```
default:
   const exhaustiveCheck: never = myDirection
   throw new Error(exhaustiveCheck)
```

如果我们把握住了“前进”的方向，那么一切都会好的。如果我们不这样做，那么我们的程序甚至不会编译！(`throw`行是可选的，我这么做只是为了关闭 eslint 中未使用的变量)

这减少了每次我们决定打开枚举时记忆的心理开销，并让编译器为我们找到它们。

## 4.用 Null 代替？操作员

许多来自其他语言的人会认为 null === undefined，但事实根本不是这样(别担心，这个技巧会变得更好！).

Undefined 是 JS 可以赋值的——例如，如果我们有一个 textbox，但没有输入任何值，那么它就是 undefined。把 undefined 想象成 JS 的自动空。

很难判断一个字段是被设计为未定义的，还是我们不小心把它留在了那里。如果我有意想给一个字段赋值，我会使用 null。这样，每个人都知道该字段是故意留空的。

这里有一个例子:

```
interface Foo {
   bar?: string
}
```

属性栏以一个问号结尾，这意味着字段可以是未定义的，所以做`let baz: Foo = {}`编译(作为补充说明，`let baz: Foo = {bar: null}` 也编译)。然而，开发人员可能不知道我是有意将 bar 留空，还是无意中留下的。传播我的意图的一个更好的方法是创建这样的接口:

```
interface Foo {
  bar: string | null
}
```

而现在，我们不得不**明确声明 bar 为 null。**我的意图不容置疑——bar 本来就没有价值。

这不仅仅对声明接口有好处——当函数可能不返回任何内容时，我也会使用它。这在编译时很有帮助:

```
//if we forget to return something, compiler will let 
const myFunc = (): string | void => {
   console.log('blah')
}//if we forget to return, the compiler makes us return null
const myFunc = (): string | null => {
 //compile time error for not returning null
}
```

## 5.实用程序类型

如果你曾经做过大型的 TS 项目，你就会知道接口无处不在。有些是其他名称的精确副本，有些是其他名称的某些属性的副本，有些是组合属性的接口。

如果是这样，不要惊慌。您正在按预期安全地使用 TS。但是，如果不利用内置类型，您可能会编写太多代码。这里是内置类型的[链接，你至少应该知道它们存在](https://www.typescriptlang.org/docs/handbook/utility-types.html)，这样你就可以在你的代码中使用它们。

我将浏览我最喜欢的和我最常用的，但是你知道的越多，你就能更好地编写你的代码。

**部分**

将所有类型字段设置为可选。这在您想要对对象执行更新时非常有用，例如:

```
function updateBook<T extends Book>(book: T, updates: Partial<T>) {
   const updatedBook = {...book, ...updates }
   notifyServer(updatedBook)
   return updatedBook
}
```

**只读**

这个函数将所有字段设置为只读。我用这个作为返回值，主要是在返回数据类的时候。

```
function generateData(): Readonly<T>
```

**不可空**

创建移除 null / undefined 的新类型。如果我们正在丰富或填写一些数据，这是有用的，我们现在保证它在那里。

```
interface IPerson {
  name: string
}type MaybePerson = Person | nullconst fillMaybePerson = (maybe: MaybePerson): NonNullable<MaybePerson> ...
```

**返回类型**

类型是函数的返回类型。如果您正在编写一个覆盖函数的 API，并且不想约束函数，那么这是非常有用的。

```
const getMoney = (): number => {
  return 100000
}ReturnType<getMoney> //number
```

**必填**

移除？来自接口的所有字段。

```
interface T {
  maybeName?: string
}type CertainT = Required<T> // equal to { maybeName: string }
```

就是这样！如果你在任何地方看到一个错误，请尽快让我知道，这样我就可以在其他人发现错误之前修复它。如果你认为我错过了什么，请告诉我！

否则，我希望你学到了一些可以成为更好的程序员的东西。