# 类型脚本最佳实践—类型断言和类型批注

> 原文：<https://levelup.gitconnected.com/typescript-best-practices-type-assertions-and-type-annotations-d30c0acb19ec>

![](img/c4ffa7e2d9704bab421bbc4e0fcf7c18.png)

[食客集体](https://unsplash.com/@eaterscollective?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

TypeScript 是一个简单易学的 JavaScript 扩展。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的类型脚本代码。

在本文中，我们将研究使用 TypeScript 编写代码时要遵循的最佳实践，包括类型断言和显式类型注释。

# 强制类型断言的一致使用

类型断言风格应该在我们的项目中保持一致。

类型断言在 TypeScript 中也称为数据类型转换。

然而，它们在技术上是不同的。

我们可以使用`as`关键字或者`<>`来给我们的值添加类型断言。

例如，我们可以坚持:

```
let x = "foo" as string;
```

或者:

```
let x = <string>"foo";
```

`const`也是用统治来允许的。从 TypeScript 3.4 开始就有了，它允许我们将某些东西设为只读。

例如，我们可以写:

```
let x = "foo" as const;
```

或者:

```
let x = <const>"foo";
```

# 与接口或类型一致的类型定义

在 TypeScript 中，我们可以定义注释类型的接口。

同样，我们可以用关键字`type`创建类型别名来做同样的事情。

当我们定义类型时，与我们使用的一致是一个好主意。

例如，我们可以坚持使用接口:

```
interface Foo {
  a: string;
  b: number;
}
```

或者键入别名:

```
type Foo = {
  a: string;
  b: number;
};
```

# 要求函数和类方法的显式返回类型

为函数和类方法添加返回类型注释是个好主意。

这样，我们知道每个函数或方法返回什么。

例如，不写:

```
function foo() {
  return 'bar';
}
```

我们可以写:

```
function foo: string() {
  return 'bar';
}
```

同样，使用类方法，而不是编写:

```
class Foo{
  method() {
    return 'bar';
  }
}
```

我们写道:

```
class Foo{
  method(): string {
    return 'bar';
  }
}
```

如果我们的方法不返回任何东西，我们使用`void`返回类型:

```
function foo(): void {
  return;
}
```

或者:

```
class Foo{
  method(): void {
    return;
  }
}
```

# 在类属性和方法上需要显式的可访问性修饰符

如果我们忽略了类中的可访问性修饰符，那么人们可能很难理解一个类的属性或方法是否是可访问的。

默认情况下，如果我们忽略它们，这个类成员就是公共的。

因此，在大多数情况下，我们可能还希望限制对某些成员的访问。

因此，我们应该包括他们。

所以与其写:

```
class Foo {
  foo() {
    console.log("foo");
  } bar() {
    //...
  }
}
```

我们可能希望通过写入以下内容来限制对某些成员的访问:

```
class Foo {
  foo() {
    console.log("foo");
  } private bar() {
    //...
  }
}
```

这样，如果我们试图编译它，TypeScript 编译器会给我们一个错误。

访问修饰符包括`public`、`private`、`readonly`和`protected`。

一个`public`成员可以做任何事情。

一个`private`成员只在类中可用。

一个`readonly`成员是公共的，但它是只读的。

一个`protected`成员只在类或任何子类中可用。

![](img/c0b92caca960194edf808e45d955515e.png)

约瑟夫·冈萨雷斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 在导出的函数和类的公共类方法上需要显式的返回和参数类型

为了使使用函数和类的公共方法更容易，我们应该添加显式的参数和返回类型，以便在使用它们时可以自动完成和出错。

例如，不写:

```
export function test() {
  return;
}
```

我们写道:

```
export function test(): void {
  return;
}
```

而不是写:

```
class Foo {
  method() {
    return;
  }
}
```

我们写道:

```
export class Foo {
  method(): void {
    return;
  }
}
```

同样，对于参数类型，我们写:

```
export class Foo {
  method(foo: string): string {
    return foo;
  }
}
```

并且:

```
export function test(foo: string): string{
  return foo;
}
```

现在我们不必猜测或查找导出的类和函数的类型。

# 结论

我们应该注释返回和参数类型，这样就不必查找导出的函数和类方法的类型。

一致地使用类型断言符号可能也是一个好主意。

同样，保持一致地使用`interface`或`type`来创建新类型也是一个好主意。