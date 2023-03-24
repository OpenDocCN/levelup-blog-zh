# TypeScript 可索引类型—TypeScript 接口简介

> 原文：<https://levelup.gitconnected.com/introduction-to-typescript-interfaces-indexable-types-d66958523518>

![](img/588cca6fb8089a52086ccdcea654bb0a.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [samuel sng](https://unsplash.com/@samuelsngx?utm_source=medium&utm_medium=referral) 拍摄的照片

与普通 JavaScript 相比，TypeScript 的最大优势在于，它通过添加确保程序对象类型安全的功能来扩展 JavaScript 的特性。它通过检查对象所呈现的值的形状来做到这一点。

检查形状被称为鸭分型或结构分型。接口是在 TypeScript 中填充角色命名数据类型的一种方式。这对于在 TypeScript 程序的代码中定义契约非常有用。在上一篇文章中，我们研究了如何定义一个 TypeScript 接口，并向它添加必需的和可选的属性。在本文中，我们将继续研究 TypeScript 接口的其他属性，如可索引类型。

# 可索引类型

我们可以为数组这样的数据定义可索引类型。任何使用括号符号的对象，如数组和动态对象类型，都可以用可索引类型来指定。可索引类型有一个索引签名，它描述了我们可以用作对象索引的类型，以及对应索引的返回类型。这对于指定动态对象的类型非常方便。例如，我们可以设计一个只接受字符串的数组，如下面的代码所示:

```
interface NameArray {
    [index: number]: string;
}let nameArray: NameArray = ["John", "Jane"];
const john = nameArray[0];
console.log(john);
```

在上面的代码中，我们定义了`NameArray`接口，该接口接收类型为`number`的`index`作为索引签名，对应的索引签名的返回类型是字符串。然后，当我们指定一个类型为`NameArray`的变量时，我们可以使用索引来获取数组的条目。然而，对于这段代码，数组方法和操作符是不可用的，因为我们只有`[index: number]`索引签名，什么也没有，所以 TypeScript 编译器不知道它是一个数组，即使它看起来像一个人的眼睛。

索引签名支持两种类型。它们可以是字符串或数字。支持这两种类型的索引是可能的，但是从数字索引器返回的类型必须是字符串索引返回的类型的子类型。这是因为当 JavaScript 试图访问带有数字属性的条目或属性时，它会将数字索引转换为字符串。这确保了对于同一个索引可以返回不同的结果。

例如，下面的代码会从 TypeScript 编译器中给我们一个错误:

```
class Animal {
  name: string = '';
}class Cat extends Animal {
  breed: string = '';
}interface Zoo {
    [x: number]: Animal;
    [x: string]: Cat;
}
```

如果我们尝试编译上面的代码，我们会得到“数字索引类型‘Animal’不可分配给字符串索引类型‘Cat’”。(2413)".这是因为我们将`Cat`作为字符串索引的返回类型，它是`Animal`的子类型。我们不能这样做，因为如果我们有两个不同类型的索引签名，那么父类型必须是字符串类型的索引签名的返回类型，而数字类型的索引签名必须具有字符串索引签名返回的的子类型。这意味着如果我们翻转返回类型，那么代码将被编译并运行:

```
class Animal {
  name: string = '';
}class Cat extends Animal {
  breed: string = '';
}interface Zoo {
    [x: number]: Cat;
    [x: string]: Animal;
}
```

由于`Animal`是`Cat`的超类型，我们必须将`Animal`作为字符串索引签名的返回类型，将`Cat`类型作为数字索引签名的返回类型。

![](img/b148c271d5d19c51bb474f38edff1566.png)

照片由 [Nathalie SPEHNER](https://unsplash.com/@nathalie_spehner?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

索引签名强制所有普通属性匹配它们的返回类型，除了那些被括号符号访问的属性，因为在 JavaScript 中`obj.prop`和`obj['prop']`是相同的。这意味着如果我们有以下代码:

```
interface Dictionary {    
  [x: string]: string;
}let dict: Dictionary = {};
dict.prop = 1;
```

然后我们会得到错误“类型‘1’不可赋给类型‘string’。(2322)"因为我们指定所有属性都是类型为`Dictionary`的变量中的字符串。如果我们想在对象的属性中接受其他类型，我们必须使用联合类型。例如，我们可以编写以下接口，让具有给定类型的对象的属性接受字符串和数字作为值:

```
interface Dictionary {    
  [x: string]: string | number;
  num: number;
}let dict: Dictionary = { num: 0 };
```

在上面的例子中，我们接受`string`和`number`作为两种类型的值。因此，我们添加了一个类型为`number`的属性，而 TypeScript 编译器不会因错误而拒绝代码。因此，在上面代码的最后一行，我们可以向值为 0 的对象添加一个`num`属性。

我们还可以创建一个索引签名`readonly`，这样我们就可以防止给它们的索引赋值。例如，我们可以使用以下代码将索引签名标记为只读:

```
interface Dictionary {    
  readonly [x: string]: string;  
}let dict: Dictionary = {'foo': 'foo'};
```

然后，当我们试图给`dict['foo']`赋另一个值时，如下面的代码所示，TypeScript 编译器将拒绝该代码，并且不会编译它:

```
interface Dictionary {    
  readonly [x: string]: string;  
}let dict: Dictionary = {'foo': 'foo'};
dict['foo'] = 'foo';
```

如果我们尝试编译上面的代码，我们会得到错误“类型‘Dictionary’中的索引签名只允许读取。(2542)".这意味着我们只能在初始化对象时设置只读属性的属性和值，但是后续的赋值将会失败。

# 结论

可索引类型对于定义动态对象属性的返回值非常方便。它利用了这样一个事实，即我们可以通过使用括号符号来访问 JavaScript 属性。这对于那些名字无效的属性来说是很方便的，如果定义的时候没有括号符号或者任何我们希望能够通过括号符号访问的东西，我们希望对那些属性或者条目进行类型检查。对于可索引类型，我们确保由括号符号分配和设置的属性具有指定的类型。

此外，这也适用于常规属性，因为括号符号与访问属性的点符号相同。此外，我们可以将索引签名指定为`readonly`,这样，当带有可索引类型的对象被初始化时，就可以写入索引签名，而不是在此之后。如果我们既有数字索引签名又有字符串索引签名，那么字符串索引签名的返回类型必须是数字索引签名的返回类型的超类型，这样我们在访问属性时就可以获得一致的对象类型。