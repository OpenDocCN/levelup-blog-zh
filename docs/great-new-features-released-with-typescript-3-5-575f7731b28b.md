# TypeScript 3.5 中发布的优秀新功能

> 原文：<https://levelup.gitconnected.com/great-new-features-released-with-typescript-3-5-575f7731b28b>

![](img/ad394b510c47a218b35438a35626c35a.png)

斯科特·沃尔什在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

TypeScript 每天都在改进。我们在每个版本中都不断获得新的特性。在本文中，我们将看看 TypeScript 3.5 中发布的新内容。

新特性包括对增量构建的速度改进、新的`Omit`助手类型、联合类型中更好的额外属性检查，以及构造函数组合的类型推断。

# 速度改进

使用`--incremental`构建模式，后续构建会更快，因为缓存了引用、文件位置和其他与构建相关的数据。

# 省略助手类型

`Omit`助手类型是在 TypeScript 3.5 中引入的，通过从原始类型中排除一些属性，我们可以从现有类型中创建一个新类型。

例如，给定下面代码中定义的`Person`类型:

```
type Person = {
    name: string;
    age: number;
    address: string;
};
```

我们可以使用`Omit`创建一个没有`address`属性的新类型:

```
type NewPerson = Omit<Person, "address">;
```

这与以下内容相同:

```
type NewPerson = {
    name: string;
    age: number;
}
```

# 联合类型中更好的超额属性检查

在 TypeScript 3.5 之前，多余属性检查在某些情况下无法捕捉属性。如果我们有一个联合类型，那么 3.5 之前的 TypeScript 版本允许一个属性与联合类型的类型同名，但与类型定义中指定的类型不同。

例如，如果我们有:

```
type Person = {
    name: string;
    age: number;    
};type Address = {
    address: string;
}const person: Person | Address = {
    name: 'Joe',
    age: 1,
    address: true
};
```

我们可以将`address`设置为非字符串，这是不允许的。

这已在 TypeScript 3.5 中修复。现在`address`必须是一个字符串，因为它被指定为一个字符串。

# `--allowUmdGlobalAccess`旗

现在可以使用新的`--allowUmdGlobalAccess`标志在 TypeScript 3.5 中引用 UMD 全局声明文件。

它增加了混合和匹配第三方库的灵活性。现在，库声明的全局变量可以被使用，甚至可以从模块内部使用。

# 更智能的联合类型检查

在 TypeScript 3.5 之前，我们会得到以下联合类型定义和变量赋值的错误:

```
type Foo = { done: boolean, value: string }
type Bar =
    | { done: false, value: string }
    | { done: true, value: string };declare let source: Foo;
declare let target: Bar;target = source;
```

在 3.5 之前，`done`将被识别为具有带值的文字类型，而不是布尔类型。

现在它将`done`字段的类型识别为布尔型。这个现在起作用的布尔只能是`true`或`false`。

![](img/1608194c552d4ebba9d1c62e7084b512.png)

由 [Max Sandelin](https://unsplash.com/@themaxsandelin?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 来自泛型构造函数的高阶类型推断

当我们像在下面的函数中那样构造泛型构造函数时:

```
function composeConstructors<T, U, V>(
    F: new (x: T) => U, G: new (y: U) => V): (x: T) => V {    
    return x => new G(new F(x))
}
```

TypeScript 3.5 可以通过推断由组合形成的类型链来推断类型`T`、`U`和`V`。

如果我们有以下代码:

```
class Foo<T> {    
    value: T;
    constructor(value: T) {
        this.value = value;
    }
}class Bar<U> {    
    value: U;
    constructor(value: U) {
        this.value = value;
    }
}let f = composeConstructors(Foo, Bar);
let a = f('foo');
```

现在我们将得到类型为`Bar<Foo<string>>`的`a`。3.5 之前的版本有类型`Bar<{}>`用于`a`。

TypeScript 3.5 现在更智能了。它可以推断由构造函数的组合形成的类型。

有了 TypeScript 3.5，更智能更快。它可以通过遍历组合链来推断由构造函数组合而成的类型。

对联合类型进行额外的属性检查，这在早期版本中是不会发生的。

此外，我们有`-- allowUmdGlobalAccess`标志来运行来自 UMD 模块的访问全局变量。

最后，我们有`Omit`类型，用于从现有类型中创建一个新类型，并删除一些属性。