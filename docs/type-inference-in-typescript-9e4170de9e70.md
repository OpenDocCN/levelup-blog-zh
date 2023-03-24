# 类型脚本中的类型推理

> 原文：<https://levelup.gitconnected.com/type-inference-in-typescript-9e4170de9e70>

![](img/0d07b2d56beae86c5406237b6be5cd80.png)

照片由[米妮周](https://unsplash.com/@marslady?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

由于 TypeScript 实体具有与之关联的数据类型，因此 TypeScript 编译器可以根据分配给变量的类型或值来猜测数据的类型。类型与变量、函数参数、函数等的自动关联。根据它的值叫做类型推断。

# 基本类型推理

当我们给变量赋值时，TypeScript 可以推断出变量的数据类型。例如，如果我们将一个值赋给一个数字，那么它会自动知道这个值是一个数字，而无需我们在代码中明确告诉它该变量的数据类型是数字。同样，这也适用于其他基本类型，如字符串、布尔值、符号等。例如，如果我们有:

```
let x = 1;
```

然后 TypeScript 编译器自动知道`x`是一个数字。它可以毫不费力地处理这种简单的类型推理。

然而，当我们分配包含多种数据类型的数据时，TypeScript 编译器将不得不更加努力地识别我们为其赋值的变量的类型。例如，如果我们有以下代码:

```
let x = [0, 'a', null];
```

然后，它必须考虑数组中每个条目的数据类型，并得出一个匹配所有内容的数据类型。它考虑每个数组元素的候选类型，然后将它们组合起来为变量`x`创建一个数据类型。在上面的例子中，第一个数组元素是一个数字，第二个是一个字符串，第三个是`null`类型。因为它们没有共同点，所以类型必须是数组元素的所有类型的并集，它们是`number`、`string`和`null`。当我们在支持 TypeScript 的文本编辑器中检查类型时，我们得到:

```
(string | number | null)[]
```

因为数组元素有三种不同的类型。只有它是所有 3 种类型的联合类型才有意义。此外，TypeScript 可以推断我们给它分配了一个数组，因此我们有了`[]`。

当类型之间有一些共同之处时，如果我们有一个像数组一样的实体集合，那么 TypeScript 将试图找到所有类型之间的最佳共同类型。然而，它并不聪明。例如，如果我们有以下代码:

```
class Animal {
  name: string = '';
}class Bird extends Animal{}class Cat extends Animal{}class Chicken extends Animal{}let x = [new Bird(), new Cat(), new Chicken()];
```

那么它将推断出`x`具有类型`(Bird | Cat | Chicken)[]`。它不承认每个类都有一个`Animal`超类。这意味着我们必须明确指定类型，就像我们在下面的代码中所做的那样:

```
class Animal {
  name: string = '';
}class Bird extends Animal{}class Cat extends Animal{}class Chicken extends Animal{}let x: Animal[] = [new Bird(), new Cat(), new Chicken()];
```

使用上面的代码，我们指示 TypeScript 编译器将`x`的类型推断为`Animal[]`，这是正确的，因为`Animal`是上面代码中定义的所有其他类的超类。

![](img/7ae8aa980b1003363cf37a51481aafe8.png)

文森特·德莱格在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 上下文类型

有时，如果我们在没有明确指定参数类型的情况下定义函数，TypeScript 会很聪明地推断出函数参数的类型。它可以推断变量的类型，因为在某个位置设置了一个值。例如，如果我们有:

```
interface F {
  (value: number | string | boolean | null | undefined): number;
}const fn: F = (value) => {
  if (typeof value === 'undefined' || value === null) {
    return 0;
  }
  return Number(value);
}
```

然后我们可以看到，TypeScript 可以自动获取`value`参数的数据类型，因为我们指定了`value`参数可以采用`number`、`string`、`boolean`、`null`或`undefined`类型。我们可以看到，如果我们传入任何带有在`F`接口中列出的类型的东西，那么它们将被 TypeScript 接受。例如，如果我们将 1 传递给上面的`fn`函数，那么 TypeScript 编译器将接受该代码。但是，如果我们像下面这样传递一个对象给它:

```
fn({});
```

然后我们从 TypeScript 编译器得到错误:

```
Argument of type '{}' is not assignable to parameter of type 'string | number | boolean | null | undefined'.Type '{}' is not assignable to type 'true'.(2345)
```

正如我们所看到的，TypeScript 编译器可以通过查看参数的位置来检查参数的类型，然后对照接口中定义的函数签名来检查该类型是否有效。我们不必为 TypeScript 显式设置参数的类型来检查数据类型。这为我们节省了大量的工作，因为我们可以使用相同签名的所有函数的接口。这省去了很多麻烦，因为我们不必重复设置参数的类型，而且只要在我们定义的接口上正确定义类型，类型检查就会自动完成。

TypeScript 带来的一个很好的特性是检查数据类型，以查看是否有任何值具有意外的数据类型或内容。TypeScript 可以根据我们对基本数据(如原始值)的变量的赋值来推断类型。如果我们给一个变量赋一个更复杂的值，那么推断出我们自动赋值的变量的类型就不够聪明了。在这种情况下，我们必须直接注释变量的类型。

它还可以进行上下文类型化，根据变量在代码中的位置来推断变量的类型。例如，如果我们在用于输入函数变量的接口中定义函数签名，它可以通过函数签名中的位置来推断函数参数的数据类型。