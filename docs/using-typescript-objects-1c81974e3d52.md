# 使用类型脚本—对象

> 原文：<https://levelup.gitconnected.com/using-typescript-objects-1c81974e3d52>

![](img/f06b2b2ae680584d457f247f316525b2.png)

[阿斯尼姆阿斯尼姆](https://unsplash.com/@asnim19?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

TypeScript 是 JavaScript 的自然扩展，在许多项目中取代了 JavaScript。

然而，并不是每个人都知道它实际上是如何工作的。

在本文中，我们将了解如何在 TypeScript 中处理对象。

# 形状类型

我们可以定义限制对象类型的形状类型。

例如，我们可以写:

```
const obj: { foo: number; bar?: sting } = { foo: 1 };
```

我们有一个对象类型，它的属性`foo`是一个数字，属性`bar`是可选的，数据类型为字符串。

名字后面的`?`表示它是可选的。

该结构必须与要分配给变量的对象的设置相匹配。

我们也可以在对象形状类型中包含方法。

例如，我们可以写:

```
const obj: { name: string; greet(greeting: string): string } = {
  name: "james",
  greet(greeting) {
    return `${greeting} ${this.name}`;
  }
};
```

现在我们在对象形状类型中有了一个`greet`方法，这是必需的。

它接受一个带有数据类型字符串的`greet`参数，并返回一个字符串。

像值属性一样，方法也可以是可选的。

例如，我们可以写:

```
const obj: { name: string; greet?(greeting: string): string } = {
  name: "james"
};
```

然后，如果我们愿意，我们可以省略分配给`obj`的对象中的`greet`方法。

# 方法的严格检查

我们可以将`strictNullChecks`选项设置为`tsconfig.json`中的`true`，以防止`undefined`值被设置在形状类型中。

有了它，我们可以写出这样的东西:

```
const obj: { name: string; greet?(greeting: string): string } = {
  name: undefined
};
```

我们将得到错误“类型‘undefined’不可赋给类型‘string’。ts(2322)'。

# 形状类型的类型别名

由于编写形状类型是如此痛苦，我们可以将它分配给一个类型别名，这样我们就可以使用别名，而不是到处指定相同的类型。

例如，我们可以写:

```
type person = { name: string; greet?(greeting: string): string };
```

现在我们可以重写我们的赋值语句如下:

```
const obj: person = {
  name: "jame"
};
```

# 多余的财产

TypeScript 编译器擅长推断类型，这意味着有时可以跳过数据类型。

例如，如果我们有:

```
type person = { name: string };
const obj = {
  name: "jame",
  age: 10
};const james: person = obj;
```

它很聪明地匹配了`obj`和`person`类型的形状以及`obj`属于`person`类型的手指。

# 形状类型联合

形状类型可以与其他形状类型形成联合类型。

例如，我们可以写:

```
type Person = { name: string };
type Location = { city: string };
const obj = {
  name: "jame",
  city: "new york"
};const james: Person | Location = obj;
```

`Person | Location`是同时具有`name`和`city`属性的数据类型。

# 联合属性类型

属性也可以将联合类型作为其数据类型。

例如，我们可以写:

```
type Person = { id: number | string; name: string };
const obj = {
  name: "jame",
  id: 1
};
```

现在`id`可以是数字，也可以是字符串。

# 物体的防护类型

我们可以像处理其他类型一样为对象添加类型保护。

例如，我们可以写:

```
type Person = { name: string };
type Animal = { breed: string; name: string };const person = {
  name: "jame"
};const animal = {
  name: "jame",
  breed: "dog"
};const arr: (Person | Animal)[] = [person, animal];
arr.forEach(a => {
  console.log(typeof a);
});
```

我们循环遍历放入`arr`数组的`person`和`animal`对象。

然后我们用`forEach`遍历它们，找出类型。

在回调函数中，我们使用`typeof`关键字查看类型，发现它们都是`object`类型，就像在 JavaScript 中一样。

![](img/ae395e78d7724432fbe0dc291876f788.png)

照片由 [Dragne Marius](https://unsplash.com/@marius_dragne?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 检查属性

这意味着我们不能使用`typeof`操作符来检查对象的类型。

相反，我们必须找到更好的替代方案。

一种方法是检查对象中是否有属性。

例如，我们可以使用`in`操作符:

```
arr.forEach(a => {
  if ("breed" in a) {
    console.log("animal");
  } else {
    console.log("person");
  }
});
```

`in`操作符检查`'breed'`属性是否在一个对象中。

它检查自己的和继承的属性。

如果在这些地方中的任何一个找到了一个属性，它返回`true`，否则返回`false`。

然而，如果一个属性同时存在于两种类型中，这对我们没有帮助。

# 结论

我们可以定义对象形状类型。

这样，我们可以检查对象的结构和属性类型。

我们可以将它们分配给一个数据类型别名，以避免重复，从而使我们的生活更轻松。