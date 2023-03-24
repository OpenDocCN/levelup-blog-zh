# 类型脚本枚举简介—常量枚举和环境枚举

> 原文：<https://levelup.gitconnected.com/introduction-to-typescript-enums-const-and-ambient-enums-1fe686b67495>

![](img/9a017dd2d853422377c857cffaef8b4a.png)

照片由[zdenk Macháek](https://unsplash.com/@zmachacek?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果我们想在 JavaScript 中定义常量，我们可以使用`const`关键字。使用 TypeScript，我们有另一种方法来定义一组称为枚举的常量。枚举让我们定义一个命名常量的列表。对于定义一个可以取几个可能值的实体来说，这很方便。在本文中，我们将从第 1 部分继续，看看联合枚举和枚举成员类型，如何在运行时计算枚举，`const`枚举和环境枚举。

# 联合枚举和枚举成员类型

枚举成员的子集可以充当 TypeScript 中变量和类成员的数据类型。我们可以使用文字枚举成员，它们是没有指定特定值的枚举成员，用于注释变量和类成员的数据类型。如果一个枚举成员没有字符串文字、数字文字或前面带有减号的数字文字，那么我们可以将它们用作其他成员的数据类型。例如，我们可以像在下面的代码中一样使用它们:

```
enum Fruit {
  Orange,
  Apple,
  Grape
}interface OrangeInterface {
  kind: Fruit.Orange;
  color: string;    
}interface AppleInterface {
  kind: Fruit.Apple;
  color: string;    
}class Orange implements OrangeInterface {
  kind: Fruit.Orange = Fruit.Orange;
  color: string = 'orange';
}class Apple implements AppleInterface{
  kind: Fruit.Apple = Fruit.Apple;
  color: string = 'red';
}let orange: Orange = new Orange();
let Apple: Orange = new Apple();
```

在上面的代码中，我们使用了我们的`Fruit`枚举来注释我们的`OrangeInterface`和`AppleInterface`中的`kind`字段的类型。我们将它设置为只能将`Fruit.Orange`分配给`OrangeInterface`的`kind`字段和实现`OrangeInterface`的类`Orange`。同样，我们将`AppleInterface`的`kind`字段设置为类型`Fruit.Apple`，这样我们只能将`kind`字段分配给`Apple`类实例的值。这样，我们可以使用`kind`字段作为常量字段，即使我们不能在类字段之前使用`const`关键字。

如果我们记录上面的`orange`和`apple`的值，我们得到`orange`是:

```
{kind: 0, color: "orange"}
```

而`apple`有这个价值:

```
{kind: 1, color: "red"}
```

当我们在`if`语句中使用枚举时，TypeScript 编译器将检查枚举成员是否以有效的方式使用。例如，它会阻止我们编写使用枚举的表达式，这些枚举总是计算为`true`或`false`。例如，如果我们写:

```
enum Fruit {
  Orange,
  Apple,
  Grape
}function f(x: Fruit) {
  if (
   x !== Fruit.Orange || 
   x !== Fruit.Apple || 
   x !== Fruit.Grape
  ) { }
}
```

然后我们得到错误消息“这个条件将总是返回‘真’,因为类型‘果。橘子和水果。苹果没有重叠。(2367)“既然它们中至少有一个总是`true`，那么表达式:

```
x !== Fruit.Orange || 
x !== Fruit.Apple || 
x !== Fruit.Grape
```

总会评价到`true`。这是因为如果`x`只能是`Fruit`类型，而`x`不是`Fruit.Orange`，那么不是`Fruit.Apple`就是`Fruit.Grape`，所以其中一个必然是`true`。

这也意味着枚举类型本身是每个成员的并集，因为每个成员都可以用作类型。如果数据类型将枚举作为类型，则它必须始终将其中一个成员作为实际类型。

# 运行时如何计算枚举

当枚举由 TypeScript 编译器编译时，它们被转换为真实对象，因此它们在运行时总是被视为对象。这意味着，如果我们有一个枚举，那么当我们需要把它作为参数传入时，我们可以用它的成员名作为枚举对象的属性名。例如，如果我们有以下代码:

```
enum Fruit {
  Orange,
  Apple,
  Grape
}function f(fruit: { Orange: number }) {
  return fruit.Orange;
}
console.log(f(Fruit));
```

然后我们从最后一行代码的`console.log`输出中得到 0，因为我们记录了`fruit.Orange`的值，它是 0，因为我们没有将其初始化为任何值。同样，我们可以像在下面的代码中一样，对枚举的析构赋值使用相同的语法:

```
enum Fruit {
  Orange,
  Apple,
  Grape
}let { Orange }: { Orange: number } = Fruit;
console.log(Orange);
```

在上面的代码中，我们将`Fruit`枚举中的`Orange`成员视为对象的另一个属性，因此我们可以像上面那样用析构赋值将它赋给一个新变量。因此，如果我们像在上面代码片段的最后一行那样记录`Orange`，那么我们再次得到 0。此外，我们可以使用析构赋值将它赋给一个名称不同于属性名的变量，如下面的代码所示:

```
let { Orange: orange }: { Orange: number } = Fruit;
console.log(orange);
```

那么我们应该从上面代码最后一行的`console.log`语句中再次得到 0。

![](img/a77a9e6598a3ab33e530b8a9e38263cf.png)

照片由 [Gary Bendig](https://unsplash.com/@kris_ricepees?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 编译时枚举

唯一的例外是，当我们使用关键字`keyof`和枚举一起使用时，枚举被视为对象。`keyof`关键字不像典型对象那样工作。例如，如果我们有:

```
let fruit: keyof Fruit;
```

然后，TypeScript 编译器希望我们将带有数字方法的字符串赋给它。例如，如果我们试图将类似于`'Orange'`的东西赋给上面的表达式，我们会得到下面的错误:

```
Type '"Orange"' is not assignable to type '"toString" | "toFixed" | "toExponential" | "toPrecision" | "valueOf" | "toLocaleString"'.(2322)
```

这不是对`keyof`关键字典型用法的期望，因为对于普通对象，它应该让我们为跟在`keyof`关键字后面的对象分配属性名。要制作 TypeScript，让我们给它分配`'Orange'`、`'Apple'`或`'Grape'`，我们可以在`keyof`关键字之后使用`typof`关键字，就像我们在下面的代码中做的那样:

```
enum Fruit {
  Orange,
  Apple,
  Grape
}let fruit: keyof typeof Fruit = 'Orange';
```

上面的代码将被 TypeScript 编译器接受并运行，因为这使得 TypeScript 将我们的枚举成员的名称视为对象的键名。

# 反向映射

TypeScript 中的数字枚举可以从枚举值映射到枚举名称。我们可以通过枚举成员的值来获取它的名字，通过分配给它的值来获取它。例如，如果我们有以下枚举:

```
enum Fruit {
  Orange,
  Apple,
  Grape
}
```

然后，我们通过按索引获取字符串`'Orange'`来获取它，就像我们对以下代码所做的那样:

```
console.log(Fruit[0]);
```

上面的代码应该记录`'Orange'`，因为成员`Orange`的值是 0，因为我们没有给它赋值。我们也可以通过使用括号内的成员常量来访问它，如下面的代码:

```
console.log(Fruit[Fruit.Orange]);
```

因为`Fruit.Orange`的值为 0，所以它们是等价的。

# 常数枚举

我们可以在枚举定义前添加`const`关键字，以防止它包含在由 TypeScript 编译器生成的编译代码中。这是可能的，因为枚举在编译后只是 JavaScript 对象。由于这个原因，枚举成员的值不能动态生成，但是它们可以从其他常数值中计算出来。例如，我们可以编写以下代码:

```
const enum Fruit {
  Orange,
  Apple,
  Grape = Apple + 1
}let fruits = [
  Fruit.Orange,
  Fruit.Apple,
  Fruit.Grape
]
```

然后当我们的代码被编译成 ES5 时，我们得到:

```
"use strict";
let fruits = [
    0 /* Orange */,
    1 /* Apple */,
    2 /* Grape */
];
```

# 环境枚举

要引用存在于代码中其他地方的枚举，我们可以在枚举定义前使用`declare`关键字来表示。环境枚举不能为任何成员赋值，也不会包含在编译后的代码中，因为它们应该引用在其他地方定义的枚举。例如，我们可以写:

```
declare enum Fruit {
  Orange,
  Apple,
  Grape
}
```

如果我们试图引用一个没有在任何地方定义的环境枚举，我们将得到一个运行时错误，因为编译后的代码中没有包含查找对象。

枚举成员可以充当变量、类成员和任何其他可以用 TypeScript 类型化的事物的数据类型。枚举本身也可以是这些事情的数据类型。因此，任何使用枚举类型的类型都是所有成员枚举类型的联合类型。枚举是否包含取决于我们在枚举前使用的关键字。如果它们是用`const`或`declare`定义的，那么它们就不会包含在编译后的代码中。枚举在转换成 JavaScript 时只是对象，成员在编译成 JavaScript 时被转换成属性。这意味着我们可以使用成员名作为 TypeScript 中对象的属性名。