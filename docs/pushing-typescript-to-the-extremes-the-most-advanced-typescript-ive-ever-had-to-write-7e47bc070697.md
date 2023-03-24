# 将打字稿推向极端:我写过的最高级的打字稿

> 原文：<https://levelup.gitconnected.com/pushing-typescript-to-the-extremes-the-most-advanced-typescript-ive-ever-had-to-write-7e47bc070697>

![](img/eb4dad25923542a545ceddc391180eed.png)

打字稿。一只美丽的野兽

当我写代码时，我不是为了完成工作而写，也不是为了自己*而写。我是为下一个读或写它的人写的。*

*对于大多数专业编写代码的人来说，编写可编译的代码并不难，同样，编写*bug*也不难。前者是应该的，但是要努力限制未来开发者写 bug 的能力。*

*这就是为什么当我写代码时，我努力争取编译时安全:我希望你的 IDE 在你的测试之前告诉你“这不行”——或者更糟，在你的生产环境之前。这就是为什么我像许多开发人员一样，更喜欢 Typescript 而不是 Javascript——我们希望我们的 IDE 尽可能地验证我们的代码是正确的。*

***备注:***

*   *为了清楚起见，这里的大部分代码都被剥离/编辑了。我实际上没有一个接受字段`otherField`的 API。*
*   *如果你已经知道这一切，恭喜你！你比我做这个之前聪明多了。*
*   *如果你发现我在某个地方打错了，不要犹豫，留下评论！我会尽快修理它。*
*   *如果你想跳过整个旅程，在最后的“关键外卖部分”。*
*   *虽然我在亚马逊工作，但这些都不是亚马逊说的，不是关于我在亚马逊的工作，也不是代表亚马逊说的。*

# *打字稿*

*我们已经确定，我在寻找“不可能破解”的代码(只要我们真诚地行动):这就是为什么当我看到*这个，*我知道必须做出改变。*

```
*interface IReqBody {
    username: string
    otherField: string
}const myApiRouteHandler = (req: Request): Response => {
    const myReqBody = req.body as IReqBody ...
}*
```

*这里最大的问题是我们没有对`req.body`进行验证。我们只是…相信客户发送的数据是正确的。如果我们的信任被破坏，客户端不会得到“400:错误请求”，他们会得到“500:试图在未定义上调用 fn”或类似的东西。*

*这对客户端没有帮助——他们会认为服务器做错了什么(从技术上来说，服务器*做错了什么——它没有验证输入，但我的意思是“做错了什么”,比如“代码中有逻辑错误”,但事实并非如此)。**

*那么我们能做些什么来修复它呢？我们希望验证主体，如果格式不正确，就抛出 400。我们想写一个这样的函数:*

```
*const validateBody<T> = (body: T): body is T => {
   ...
}*
```

*我在这条路上的第一次旅程引导我找到了这样一个函数，它有一些问题:*

```
*interface IReqBody {
    username: string
    otherField: string
}const validateBody = (body: unknown): body is IReqBody => {
  const bodyAsReq = body as IReqBody
    if (
      bodyAsReq.username === undefined || 
      bodyAsReq.otherField === undefined
    ) {
      throw HttpError(500)
    }
  return true
}*
```

*这里有两个问题:*

1.  *还记得我们讨论过写代码时人们不能破解代码吗？如果一周后，我们决定需要在`IReqBody`中添加另一个字段，会发生什么呢？添加字段的人必须记得检查它是否在验证器中未定义。这是一个容易被忽略的错误。我们希望我们的编译器说“嘿！您忘记验证该字段了！”*
2.  *我们必须为每一条路线重复这个代码。有很多重复的功能。*

*要解决问题 2，您可能会想“让它通用一点！”。如果那是你，你就说对了一半。TypeScript 有类型擦除的概念，这意味着在运行时，所有的接口都消失了——它们只是在编译时存在，所以你不能迭代通过接口的字段(但那不是很好吗！).*

*不过，我们能做的是遍历对象的字段。*

*所以让我们试试这样的东西:*

```
*const validateBody<T> = (body: unknown, verifier: T): body is T => {
   const validKeys = Object.keys(verifier)
   const inputKeys = new Set(Object.keys(body)) for (const validKeys of validKey) { // if we're missing the key, return false
       if (inputKeys.has(validKey)) {
           return false
       } else {
           inputKeys.delete(validKey)
       } // if the types are different, return false
       if (typeof verifier[validKey] !== typeof body[validKey]) {
          return false 
       }

       //if it's an object, recurse and if it's false return false
       if (typeof verifier[validKey] === 'object' && !validateBody(body[validKey], verifier[validKey]) {
          return false
       }
  }

  //for strictness, if there's extra fields return false (optional)
   if (inputKeys.length() > 0) { 
    return false
   }

  return true
  }*
```

*对此进行分解，我们遍历所有的键并比较类型。如果它们都是对象，我们可以在子类型上递归，该子类型的所有键都将被验证。*

*其中`verifier`是我们*知道*是正确的`T`的版本。呼叫看起来像这样:*

```
*const myApiRouteHandler = (req: Request): Response => {
    const body = req.body

    if (!validateBody(body, {
       username: "mockstr",
       otherField: "mockstr",
      }) {
         throw HttpError(500)
     } ...
  }*
```

*厉害！我们都受到了约束！我们是编译时安全的:向 req 添加一个字段，这里会出现一个错误，说 validateBody 的第二个参数不正确。*

*唉，我们还没完，我还不开心。*

*这适用于基本类型:字符串、布尔、对象、数字…如果我们想要验证一个枚举值，会发生什么？例如，一个字符串只能有`['hi', 'bye']`中的值？或者，更有可能的是，如果字段可以不定义会发生什么？或者我们想在空字符串上失败？*

## *二:高级部分*

*到目前为止，我们做了一些简单的东西:类型保护、泛型和基本的 TS。*

*现在我们想变得疯狂一点。*

*假设我们有一个抽象类，`BaseValidator`:*

```
*abstract class BaseValidator<T> {
    validate(input: unknown): boolean
}*
```

*这太棒了！我们现在可以扩展它，并在必要时进行验证。例如，具有可空性的字符串验证器:*

```
*abstract class StringValidator extends BaseValidator<string> {
  nullable: boolean constructor(nullable: boolean) {
     this.nullable = nullable validate(input: unknown): boolean {
      if (input === undefined) {
         return nullable
      } 
      return typeof input === "string"
   }
}*
```

*我们可以尽情发挥，验证许多东西。我们可以有`EmailValidator`、`AgeValidator`、`integerValidator`、`EnumValidator`等。(你可以为所有的原语做泛型`TypeValidator`，但是记住类型擦除，但是这可以通过传递给构造函数的一个空值很容易地解决)我承认这也可能是一大堆样板文件(你多久在你的 API 中接收一次电子邮件？)但这总比不验证或者在你记得验证的地方漫不经心地做好。*

*但是什么会成为我们`validateBody`的验证者呢？我们不能再拿`T`，因为下面不再是`T`:*

```
*const notT = {
   username: new UsernameValidator(),
   otherField: new StringValidator()
}*
```

*所以我们需要编写一个通用类型，它接受任何其他类型，并返回用`BaseValidator<TypeOfTheFieldOfTheOriginalValue>`包装的每个字段。更详细地说，如果原始接口需要一个`string`，我们不想给它一个`BaseValidator<number>`类型的验证器。*

*首先，让我们定义一个有效的文章主体*甚至* *是什么样子的:**

```
*type IJsonArray = Array<
 string         | 
 number         | 
 boolean        | 
 Date           | 
 IValidPostBody | 
 IJsonArray
 >export interface IValidPostBody {
 [x: string]: 
  string         | 
  number         | 
  boolean        | 
  Date           | 
  IValidPostBody | 
  IJsonArray
}*
```

*一个有效的 JSON 是一个由字符串索引的对象，可以是:一个原语、另一个 JSON 或者原语或 JSON 的数组。*

*那么我们将如何定义包装类型呢？第一次拍摄可能是这样的:*

```
*export type IOptionsForFields<T> = {
   [key in keyof T]: FieldOption<T[key]>
}*
```

*简而言之，这意味着对于`T`中的每个键，我们需要给出一个 fieldOption，它采用的类型是`T[key]`。*

*第一个问题是子 jsons！*

```
*//an interface like this...
interface MyInterface {
   foo: string
   bar: {
      baz: boolean
   }
 } //would turn into this! No good!
const t: IOptionsForFields<MyInterface>  = {
 foo: new FieldValidator<string>,
 bar: new FieldValidator<{ baz: boolean }>
}*
```

*不知何故，我们需要让`IOptionsForFields`也是递归的。*

*进入条件类型的魔力！我们可以说，如果一个类型扩展了 IValidPostBody(也就是说，是一个有效的 post 主体)，那么我们希望递归该类型。否则，我们要合适的`fieldValidator`。*

```
*export type IOptionsForFields<T> = {
  [key in keyof T]: 
    T[key] extends IValidPostBody ? 
        IOptionsForFields<T[key]> : 
        FieldOption<T[key]>
}*
```

*就这样，我们的包装工作了！*

```
*const t: IOptionsForFields<MyInterface>  = {
 foo: new FieldValidator<string>(),
 bar: {
    baz: new FieldValidator<boolean>(),
 }*
```

*不过，还有最后一个问题需要解决。如果我们创建一个这样的新界面:*

```
*interface IOmittable {
   omittable?: string
}*
```

*然后这个飞起来:*

```
*const t: IOptionsForFields<IOmittable> {}*
```

*这可不好。我们希望它作为一个可省略的字符串进行验证，但是您希望实现这个验证器。所以我们需要给`IOptionsForFields`界面增加一些魔力:*

```
*export type IOptionsForFields<T> = {
  [key in keyof T]-?: 
    T[key] extends IValidPostBody ? 
        IOptionsForFields<T[key]> : 
        FieldOption<T[key]>
}*
```

*我不会让你在这里指出不同之处:在`[key in key T]`之后，还有一个`-?`。这就意味着:现在，所有可省略的字段都是必需的！*

*最终的函数如下所示:*

```
*export const baseValidator = <T extends IValidPostBody>(
   options: IOptionsForFields<T>, input: Record<string, unknown>
): boolean => {
  const validKeys = Object.keys(options)
  const inputKeys = new Set(Object.keys(input))

  for (const validKey of validKeys) {
     const optionsOfKey = options[validKey]
     if (!inputKeys.delete(validKey)) {
       return false
     } if (optionsOfKey instanceof FieldOption) {
       if (optionsOfKey.validate(input[validKey])) {
          continue
       } else {
         return false
       }
    } if (!baseValidator( // whatever typescript cast is required here, I am not smart  enough to figure it out. I know that this type is a IOptionsForFields, because it can either be that or a fieldOption, which was already caught in the previous block. This means that this is verifably correct. I can't seem to tell the typechecker that though.         options[validKey] as any,
         input[validKey] as Record<string, unknown>
     )) {
       return false
     }
   }
  return true
 }*
```

## *关键要点*

***打字稿要点:***

*   *`-?`是`?`的反义词——它只是一般的暗示，因此较少被看到。*
*   *您可以编写一个类型，它接受另一个类型，并使用`key in keyof T`为它的所有键做一些事情。*
*   *三元运算符也适用于类型！*
*   *类型可以是递归的。*
*   *`T[key]`表示该键将在`T`中获得的值的类型。*
*   *包装器类型很有用！*

***其他外卖:***

*   *你很可能迟早会发现问题:我建议你早点发现它们。编译时安全。*
*   *为下一个需要它的人写程序，为下一个阅读它的人写代码。*
*   *编写代码并不困难。编写 bug 也不难。让写 bug 变得困难。*

*感谢阅读！*