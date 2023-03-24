# TypeScript 中的泛型

> 原文：<https://levelup.gitconnected.com/typescript-generics-with-a-witcher-6f449f253e13>

对于不习惯泛型的人来说，泛型看起来绝对令人生畏。在这篇博文中，我将试图让它们不那么神秘，并通过使用更多的实践方法来展示它们是如何有用的。

![](img/41a0f2ef7797c7e37c98986e17467eb4.png)

**如果没有设置任何本地环境，可以跟随在** [**打字游戏场**](https://www.typescriptlang.org/play) **过来。**我建议在单独的选项卡中键入示例。

## 第一眼

顾名思义，泛型帮助我们创建更多可重用的代码块。

**简而言之:**
它们可以帮助我们创建定义良好的类、接口和函数，同时仍然给我们保持它们“通用”的灵活性——通过让它们根据程序员传递的类型(或多个类型)工作。

让我们看看我们的第一个例子，你可以在 [Typescript Playground](https://www.typescriptlang.org/play) 中输入:

```
function iTakeAnyArrays(arr: any[]): any[] {
    return arr;
}iTakeAnyArrays(1); //error function iTakeGenericArrays**<T>**(arr:**T[]**): **T[]** {
    return arr;
}iTakeGenericArrays(1); //error 
For now think of the <**T**> here as a kind of paceholder which will be replaced by a type passed in by the user (or a type that can be inferred from the arguments). As we proceed this will become clearer.
```

在上面的例子中，我们故意给函数传递了一个错误的参数，这个参数当然应该是一个数组。这是为了看看幕后可能发生的事情。

如果您将鼠标悬停在编辑器(或[在线游乐场](https://www.typescriptlang.org/play))上的错误上:

```
iTakeAnyArrays(1):
Argument of type 'number' is not assignable to parameter of type '**any[]**'iTakeGenericArrays(1):
Argument of type 'number' is not assignable to parameter of type '**unknown[]**'
```

如果你不知道 **any** 和 **unknown** 的区别，只知道 **unknown** 会缩小到一个可以从代码中推断出来的推断类型，然后坚持那个类型。

[这里的](https://stackoverflow.com/questions/51439843/unknown-vs-any)更多的是关于这个的。

然而“**任何”**永远不会缩小到一个类型，而永远是任意的。

这就是为什么“**any”**有时可能不适合概括代码片段，因为您会丢失一些(或很多)类型定义。

如果我们现在尝试传递正确类型的参数:

```
function iTakeAnyArrays(arr: any[]): any[] {
    return arr;
}iTakeAnyArrays([1]);function iTakeGenericArrays<T>(arr:T[]): T[] {
    return arr;
}iTakeGenericArrays([1]);
```

错误当然消失了，更有趣的是，当您将鼠标悬停在以下内容上时(我已经注释了您将在 IDE 中看到的悬停内容):

```
iTakeAnyArrays([1]);
// function iTakeAnyArrays(arr: any[]): any[]iTakeGenericArrays([1]);
// function iTakeGenericArrays<number>(arr: number[]): number[]
```

如果我们进一步修改代码，采用不同类型的数组，并将鼠标悬停在函数调用上:

```
function **iTakeAnyArrays(arr: any[]): any[]** {
    return arr;
}**iTakeAnyArrays([1]);** // function iTakeAnyArrays(arr: any[]): any[]**iTakeAnyArrays(['A']);** // function iTakeAnyArrays(arr: any[]): any[] function **iTakeGenericArrays<T>(arr:T[]): T[]** {
    return arr;
}**iTakeGenericArrays([1]);** // function iTakeGenericArrays<number>(arr: number[]): number[] **iTakeGenericArrays(['B']);** // function iTakeGenericArrays<string>(arr: string[]): string[]
```

您将看到，我们的函数 iTakeGenericArrays 根据可以从参数中推断出的类型来调整定义。

函数 **iTakeGenericArrays** 是所谓的**泛型，因为它现在在一系列类型**上工作。

可以通过在函数定义中使用**“T”**来实现:

```
function iTakeGenericArrays<**T**>(arr:**T**[]): **T**[]
```

这称为类型变量，用于简单地传递类型。这个变量只能传递类型，不能传递值。您不必使用“T”作为变量名，可以使用您选择的任何其他变量。

到目前为止，我们没有显式地传入任何类型，它是由 TypeScript 编译器从 args 中自动推断出来的。

我们可以在需要时显式传入类型。我们对之前的通用函数做了一点修改，现在返回传入数组的长度:

```
**function iTakeGenericArrays<T>(arr:T[]): number {
    return arr.length;
}**// **T** will be replaced by type **string**:let arrLength = **iTakeGenericArrays<string>(['B'])**;
console.log(arrLength);// The following will give an error:arrLength = iTakeGenericArrays<**string**>([***2***]);
console.log(arrLength);
// Type 'number' is not assignable to type 'string'
```

我们已经使用函数参数 **arr:T[]** 到达这个点，这样我们就可以访问**。数组的长度属性。**

您可能想知道为什么我们不直接使用下面的代码，用类似于 **string[]** 的代码替换 T:

```
function iTakeGenericArrays<T>(arr:**T**): number {
    return arr.*length*;
}// T will be replaced by string:
let arrLength = iTakeGenericArrays**<string[]>**(['B']);
console.log(arrLength);*Here we have written*iTakeGenericArrays<T>(arr:**T**): number*instead of*iTakeGenericArrays<T>(arr:**T[]**): numberto see if we can directly pass in **string[]** into **T, and if our compiler will accept it**
```

但这会产生错误:

```
...
  return **arr.*length***;
...Property 'length' does not exist on type 'T'.
```

嗯，typescript 记录了这个错误，因为它必须确保所有类型都被一致地传入。

如果有人将类型**的数字**传递给类型变量，那么显然是**。无法访问长度**属性。因此，它确保我们的函数参数被正确定义，以适应所有可以传入的类型。

## 使用多个类型参数

我们也可以在泛型函数中使用多种类型变量。这可以简单地按如下方式完成:

```
function multipleGenericTypes**<T,K>**(arg1: **T**, arg2: **K**): boolean {
    return **typeof arg1 === typeof arg2**;
}const res = multipleGenericTypes**<string, string>**('a', 'b');
console.log(res); // trueconst anotherRes = multipleGenericTypes**<string, number>**('1', 1);
console.log(anotherRes); //false
```

我们简单地将额外的类型变量作为<t k="">传递，并像往常一样在我们的参数中使用它。</t>

# 带约束的泛型

到目前为止，我们已经看到，我们的类型变量可以接受所有类型的数据，我们使用这种类型变量编写的代码也必须与所有类型兼容。

我们之前在尝试访问**时看到了这一点。类型变量的长度**也可以是数字而不是数组。

在某些情况下，这可能是一个缺点，例如，我们知道我们的函数将处理一系列类型。

## 实际例子

让我们看一个实际应用。游戏是我最喜欢的爱好之一，所以我们的例子将涉及一些相关的东西。

想象我们有一个角色攻击另一个角色的场景。受害者将受到损害。现实游戏的一个常见情况是，角色使用的物品也会随着使用而退化。

因此，我们可以使用一个普通的函数对一个角色造成伤害，然后重复使用这个函数来反映这次攻击导致的武器退化。

因此，我们需要限制我们的通用函数，只接受一系列类型，在这些类型中，我们可以反映对某个特定属性的某种伤害。游戏中常见的属性是**生命值(生命值)**属性。

因此，我们将尝试创建一个通用函数，它接受任何拥有 hp 属性的类型。

为此，让我们首先创建一个接口:

```
interface CanTakeDamage {
    hp: number;
}
```

这个接口为我们游戏中可以拥有 **hp** 属性的任何实体定义了一个**类型**。

在这之后，让我们为我们的角色定义职业和他们可能使用的武器。这些武器与角色有一些共同的属性:

```
class **Character** {
    name: string; 
    attack: number; 
    hp: number;
    constructor(
        name: string, 
        attack: number, 
        hp: number,
    ) {
        this.name = name;
        this.attack = attack;
        this.hp = hp;
    }
}class Weapon **extends** Character {
    price: number;
    constructor(
        name: string,
        attack: number,
        hp: number,
        price: number,
    ) {
        **super(
            name,
            attack,
            hp
        );**
        this.price = price;
    }
}
```

所以这两个实体都将有一个**名**，一个基础**攻击**属性和 **hp** 属性。该武器将额外拥有一个“价格”属性。

现在让我们获得三个这样的游戏实体:一个巫师，一个巫师要攻击的怪物和一把他要用来攻击的剑。
巫师是“杰洛特”，布鲁克萨是“百合”，剑是伟大的“艾伦迪特”。

```
const **Witcher** = new Character(
    "Geralt",
    1250,
    3200
);const **Bruxa** = new Character(
    "Lily",
    1450,
    4200
);const **SilverSword** = new **Weapon**(
    "Aerondight", // name
    550, // base attack
    500, // hp property used here for sword "health"
    5500 // price
);
```

我们传递给每个人他们各自的统计数据和他们的名字。

很好，现在剩下要做的就是创建一个通用函数来帮助我们对这些游戏实体造成伤害。

这个函数可以使用类型变量“T ”,但是我们如何缩小这个类型变量的范围以确保只有具有 **hp** 属性的东西被认为是有效的类型呢？

这可以通过使用我们之前定义的接口，让我们的类型变量扩展这个接口来实现:

```
**interface CanTakeDamage {
    hp: number;
}**...function inflictDamage<T **extends** CanTakeDamage>(
    **victimEntity: T**,
    damage:number
) {
    return **victimEntity.hp** - damage;
}
```

这样，我们可以缩小可接受类型的范围，只接受那些可以作为 CanTakeDamage 传递的类型。

上面的**造成伤害**函数将只接受**受害者实体**参数的参数，该参数将是**伤害**类型，即它们将具有 **hp** 属性。

这是因为我们指定了无论传递给 **T** 的是什么类型，都应该扩展 **CanTakeDamage** 。

```
<T **extends** CanTakeDamage>
```

如果我们没有扩展这个接口，我们可能会传递任何任意类型，并且会从编译器得到一个关于 **hp** 属性不在 **T** 上的错误。因为 T 可以是任何其他类型，如字符串或数字。

现在，剩下要做的就是测试我们的功能。对于我们到目前为止拥有的代码，我们将添加以下内容:

```
...const **damageBruxa** = inflictDamage(
    Bruxa, 
    **Witcher.attack + SilverSword.attack**
);const **damageSword** = inflictDamage(
    SilverSword, 
    **Bruxa.hp/50**
);console.log(`
    Bruxa now has **${damageBruxa}** hp,
    Sword degrades to **${damageSword}** points
`); Which will log:Bruxa now has 2400 hp,
Sword degrades to 416 points
```

完整的代码应该如下所示:

当我们谈到接口和泛型的话题时，让我们接下来看看将两者更紧密联系在一起的东西。

# 通用接口

假设我们有一个函数来检查传递的数组的长度:

```
function checkLength<T>(item: T[]): number {
    return item.length;
};
```

我们有另一个函数，我们需要将它作为回调来传递。这个函数也将一个字符串作为它的第二个参数，并使用回调函数返回这个字符串的长度。

类似于以下内容，但缺少一部分:

```
function **checkLength**<T>(item: T[]): number {
    return item.length;
}function getStrLength(
    cb**: ??????,** 
    str: string
): number {
    return checkLength(str.split(''));
}const len = getStrLength(
    **checkLength**,
    'The Lesser Evil.'
);console.log(len); 
```

问号是故意放上去的，我们可以为回调函数 **checkLength** 传递什么类型的函数呢？

查看我们的 **checkLength** 函数，我们可以将回调的类型写成:

```
function checkLength<T>(item: T[]): number {
    return item.length;
}function getStrLength(
    cb**: <T>(item: T[]) => number**, 
    str: string
): number {
    return cb<string>(str.split(''));
}const len = getStrLength(
    checkLength,
    'The Lesser Evil.'
);console.log(len); // 16
```

我们的函数是这样的:

```
**checkLength: <T>(item: T[]) => number**since it takes in an **item parameter** of an **array of any T type** of elements, and returns a **number** which is the length of the array.
```

运行代码后，我们得到了预期的结果。

现在假设我们有另一种情况，我们需要检查两个字符串之间的长度差异。

同样，使用传递的回调和两个字符串进行相互检查。

我们可以为回调部分重用我们的 checkLength 方法，以产生一个 **getStrDiff** 函数。类似于以下内容:

```
function **checkLength**<T>(item: T[]): number {
    return item.length;
};function getStrLength(
    cb: **<T>(item: T[]) => number**, 
    str: string
): number {
    return cb<string>(str.split(''));
}function **getStrDiff**(
    cb: **<T>(item: T[]) => number**, 
    str1: string,
    str2: string
): number {
    return (
        **cb<string>(str1.split('')) - cb<string>(str2.split(''))**
    );
}const len = getStrLength(
    **checkLength**,
    'The Lesser Evil.'
);const **diff** = **getStrDiff**(
    **checkLength**,
    'Silver for Monsters',
    'Steel for Humans'
)console.log(len, **diff**); // 16, 3
```

请注意，我们不得不再次写出回调的整个类型。这肯定是可以避免的。

这个问题可以通过使用接口来解决。我们可以为这个公共回调的类型定义一个:

```
**interface CheckLength {
    <T>(item: T[]): number
}**(**Note the capital "C"**, this is not the same as our checkLength function earlier)
```

我们现在可以使用这个接口在 getStrDiff 和 getStrLength 的函数参数中定义回调类型:

```
interface **CheckLength** {
    **<T>(item: T[]): number**
}function checkLength**<T>(item: T[]): number** {
    return item.length;
};function getStrLength(
    cb: **CheckLength**, 
    str: string
): number {
    return cb**<string>**(str.split(''));
}function getStrDiff(
    cb: **CheckLength**, 
    str1: string,
    str2: string
): number {
    return (
        cb**<string>**(str1.split('')) - cb**<string>**(str2.split(''));
    );
}const len = getStrLength(
    checkLength,
    'The Lesser Evil.'
);const diff = getStrDiff(
    checkLength,
    'Silver for Monsters',
    'Steel for Humans'
)console.log(len, diff);
```

我们还可以向接口本身传递一个类型变量，以进一步细化我们的代码:

```
interface **CheckLength<T>** {
    (item: T[]): number
}function checkLength<T>(item: T[]): number {
    return item.length;
};function getStrLength(
    cb: CheckLength<string>, 
    str: string
): number {
   ** return cb(str.split(''));**
}function getStrDiff(
    cb: CheckLength<string>, 
    str1: string,
    str2: string
): number {
    return (
        **cb(str1.split('')) - cb(str2.split(''));**
    );
}const len = getStrLength(
    checkLength,
    'The Lesser Evil.'
);const diff = getStrDiff(
    checkLength,
    'Silver for Monsters',
    'Steel for Humans'
)console.log(len, diff);
```

这和之前的代码有什么不同？之前，我们唯一的优势是使 cb 参数的类型定义更加简洁。

之前，我们还必须写出:

```
function getStrLength(
    cb: **CheckLength**, 
    str: string
): number {
    return cb**<string>**(str.split(''));
}
function getStrDiff(
    cb: **CheckLength**, 
    str1: string,
    str2: string
): number {
    return (
        cb**<string>**(str1.split('')) - cb**<string>**(str2.split(''));
    );
}
```

然而现在，

```
function getStrLength(
    cb: **CheckLength<string>**, 
    str: string
): number {
    return **cb(str.split(''));**
}function getStrDiff(
    cb: **CheckLength<string>**, 
    str1: string,
    str2: string
): number {
    return (
        **cb(str1.split('')) - cb(str2.split(''));**
    );
}
```

在接口本身的帮助下，我们将 T 类型强制为回调 **cb，**的字符串。

以下是完整的代码:

最后，让我们看看如何在另一个实际应用中，将**泛型与**类一起使用。

## 通用类

如果到目前为止您已经理解了这些概念，那么理解泛型类应该是轻而易举的事情。

就像接口一样，我们只需在类声明后添加类型变量:

```
class Character<T> {
    private name: T;
    sayMessage: (msg: T) => T;
    constructor(
        name: T,
        sayMessage: (msg: T) => T
    ) {
        this.name = name;
        this.sayMessage = sayMessage;
    } sayName = (): T => {
        return this.name;
    };
}
```

然后我们可以创建一个角色对象，如下所示:

```
const Geralt = new Character<string>(
    'Geralt of Rivia',
    (msg) => {
        return msg
    }
);
```

在这种情况下，t 将接受字符串作为它的类型，因此我们的杰洛特对象上的 sayMessage 方法将只接受字符串作为它的 msg 参数。

```
const geraltSays = Geralt.sayMessage(`What is up my dude, I am ${Geralt.sayName()}`);
console.log(geraltSays);Will log
"What is up my dude, I am Geralt of Rivia"Whereasconst geraltSays = Geralt.sayMessage(10);
console.log(geraltSays);// will give an error since 10 is not of type string
```

完整的代码如下所示:

> **这应该涵盖了你开始使用泛型所需要知道的大部分内容。**

我建议查看一下 [Typescript 手册](https://www.typescriptlang.org/docs/handbook/generics.html),从总体上了解更多关于泛型和类型脚本的知识。

希望这至少在某种程度上有助于更好地理解泛型！

# 分级编码

感谢您成为我们社区的一员！ [**订阅我们的 YouTube 频道**](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1) 或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)