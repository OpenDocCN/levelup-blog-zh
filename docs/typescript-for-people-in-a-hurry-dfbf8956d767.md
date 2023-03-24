# 为赶时间的人打字

> 原文：<https://levelup.gitconnected.com/typescript-for-people-in-a-hurry-dfbf8956d767>

![](img/5bcb1b2215239c1b2caefbfe0edc5c54.png)

## 什么是 TypeScript？

TypeScript 是 JavaScript 的超集。它能做 JS 能做的一切，甚至更多。它由微软更新和维护，可以向下编译成常规的 JS。

一些开发人员会争辩说，TypeScript 对人类的可读性也更好，这可能是它在开发社区中越来越受欢迎的原因。

## 许多不同类型(1/3)

只要有可能，我们应该将静态类型应用到变量中。然而，默认情况下，如果类型像在 type1 中那样未定义，它将携带一个类型 **any** 。

```
let type1;
// I am an any typelet type2: boolean = false;
// I am a boolean typelet type3: string = 'Hello Developers';
// I am a string typelet type4: number = 42;
// I am a number type
```

还有**多种类型**。注意在*数字*和*布尔之间的“管道操作符”`|`。*这表示 type4 可以是数字类型或布尔类型。

在 TypeScript 中，我们希望尽可能地使用单一类型**。然而，如果不是，我们可以选择使用**多类型**来代替默认的**任意类型**。**

```
let type4: number | boolean = 42;
// I am type4\. I am a number type or a boolean type
```

到目前为止，所有的类型都是显式类型。一种我们明确硬编码的类型。但是也有像我们的 type4 这样的**隐式类型**。

```
let type4: number;
type4 = 42
// I'm an explicit typelet type4 = 'Hey good looking. I'm an implicit type'
// Notice that we did not hard code the type 'string' here. type4 implicitly knows it is a string
```

我们也可以用函数做到这一点。

```
const arrayImplicit = [10, 30, 40, 50];
let arrayImplicit = arrayImplicit.reduce((num1, num2) => num1 + num2);//I am an implicit type functionconst arrayExplicit: number[] = [10, 30, 40, 50];
let arrayExplicit = arrayExplicit.reduce((num1, num2) => num1 + num2);//I am an explicit type function
```

## 检查类型

为了检查我们是否得到了正确的类型，我们使用了操作符的*实例。这将测试构造函数的原型属性是否出现在对象的原型链中。*

```
class Puppies {
 puppies: number;
  constructor(data: number) {
  this.puppies = data;
  }
}const puppy = new Puppies(4);if (puppy instanceof Puppies) {
  console.log("Adopt don't shop!");
}
```

如果从多个类继承，它也会通过，所以一个好的经验法则是将它缩小到被检查的最低模型。

## TypeScript 中的数组

TypeScript 数组的示例。特别注意 coolType8 和 coolType9。

```
const type5: string[] = ['Hello World'];const type6: number[] = [1, 2];const type7: boolean[] = [true, false];const coolType8: (number | boolean)[] = [1, 2, true];
//Notice that my types are wrapped with (). This reads that I can accept number or boolean values in my arrayconst coolType9: boolean[][] = [ [true, false] ];
// I have a nested array value
```

现在让我们来看看叫做元组的东西。元组是具有固定数量元素的数组。元组允许我们使用具有固定数量元素的数组，这些元素的类型是已知的，但不需要相同。以下示例说明了字符串和数字的值和类型表示。

```
**let** tupleExample: [string, number];
//Tuple declaration tupleExample = ["hello", 10]; // CORRECTtupleExample = [10, "hello"]; // INCORRECT
```

另一个例子:

```
const tupleMe: [string, number] = ['I am Tuple', 25];
```

## 枚举

对 JS 来说有点新的是枚举或枚举值。枚举是为数值集提供更友好名称的一种方式。

```
**enum** Simpsons{Bart, Lisa, Maggie} 
let s: Simpsons = Simpsons.Lisa;
```

默认情况下，像数组索引一样，枚举从 0 开始对其成员进行编号。要改变这一点，我们可以手动设置它的一个成员的值。例如，我们可以从 1 而不是 0 开始我们的 Simpsons 例子。

```
**enum** Simpsons{Bart = 1, Lisa, Maggie} 
let s: Simpsons = Simpsons.Lisa;
```

我们也可以手动设置所有枚举。

```
**enum** Simpsons{Bart = 1, Lisa = 2, Maggie = 5} 
let s: Simpsons = Simpsons.Lisa;
```

我们也可以从一个数值到枚举中该值的名称。例如，如果我们有值 2，但不确定它在 Simpsons 枚举中映射到什么，我们可以搜索相应的名称。

```
**enum** Simpsons{Bart = 1, Lisa, Maggie} 
**let** simpsonName: string = Simpsons[2]; console.log(simpsonName); 
// will display 'Lisa' as Lisa's value is 2 above 
```

![](img/06f8a16aa1e8a7c68612343e4e08e97c.png)

*半途而废*

## 功能

```
function add(num1: number, num2: number) {
 return num1 + num2;
}add(10, 5);
// will return 15
```

上面示例中的参数表示 num1 的类型为 number，num2 的类型为 number。注意，由于这个原因，我们只能在 add()中输入数字。任何其他类型的值都会给我们一个错误。

上面的例子有一个**隐式返回类型。**让我们将返回类型显式化。

```
function add(num1: number, num2: number)**: number** {
 return num1 + num2;
}add(10, 5);
// will return 15
```

请注意，如果我们显式地将它作为字符串返回，我们的代码将会中断。要显式返回一个字符串，我们必须返回一个字符串。

## 接口

我们可以不使用类，而是使用接口。一个接口用关键字*接口*定义，可以包含属性和使用函数或箭头函数的方法声明。对于下面的例子，我们将关注属性，我们的三个属性是 firstName、middleName 和 lastName。

```
interface IStudent {
 firstName: string;
 middleName: string;
 lastName: string; 
}const myFace: IStudent = {firstName: 'Kelly', middleName: 'Belly', lastName: 'Jelly'};myFace.firstName = 'Miss';
myFace.middleName = 'Kelly';
myFace.lastName = 'More';
```

命名接口时使用大写`I`是一种惯例。通知`IStudent`。

## 许多不同的类型继续—交叉类型(2/3)

交集类型将多种类型合并为一种。它们允许我们将现有的类型添加到一起，以获得具有您需要的所有特性的单一类型。换句话说，它是向单个实体添加多种类型以及基于此创建新类型的能力。

```
interface IRobot {
 iq: number;
}interface IHuman {
 firstName: string;
}interface IPrimate {
 lucy: boolean;
}let cyborg: IRobot | IHuman; // I can identify as a robot or a human
let animal: IPrimate;
let martians: IHuman;type OurHistory = IPrimate & IHuman & IRobot
// I have combined all 3 interface features into OurHistory. I also have the properties of these interfaceslet story: OurHistory;story.firstName = 'Scarlett';
story.iq = 263;
story.lucy = true;
```

## 许多不同类型继续存在——属型(3/3)

泛型确定了一种约束类型，对于可重用代码非常有用。当我们没有预定义的数据类型时，它们为我们提供了一种操作方法。当我们想要传递一个类型但不知道它是什么，或者在传递一个类型后操作一个类型时，我们使用泛型。

下面是一个非常基本的泛型类型函数的例子。

```
function genericEg<T>(arg: T): T {
 return arg;
}
```

赋值<t>让函数知道它将是泛型的。跟在:后面的 T 是返回类型，我们的参数也是这种类型，(arg: T)。</t>

稍后我们将能够选择我们要传递的类型。

```
function genericEg<T>(arg: T): T {
 return arg;
}genericEg(8);
// This is now a number that is being passed in
```

## 资源

打字稿:[https://www.typescriptlang.org/index.html](https://www.typescriptlang.org/index.html)
斯克林巴:[https://scrimba.com/g/gintrototypescript](https://scrimba.com/g/gintrototypescript)