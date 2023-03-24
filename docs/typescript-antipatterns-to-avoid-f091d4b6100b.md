# 要避免的类型脚本反模式

> 原文：<https://levelup.gitconnected.com/typescript-antipatterns-to-avoid-f091d4b6100b>

![](img/324e57ea16d232478b4d45bf3f14fdac.png)

照片由 [pooya ramezani](https://unsplash.com/@pooya_ramezani?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

TypeScript 是一种通过向 JavaScript 代码添加类型注释来扩展 JavaScript 功能的语言。这让我们避免了意外数据类型的错误。

在本文中，我们将研究一些在编写 TypeScript 代码时要避免的反模式。

# 过度使用 any 类型

使用 TypeScript 的目的是在变量和函数中拥有类型。所以我们应该尽可能地使用它们。

因此，我们不应该在大部分代码中使用`any`类型。

# 过度使用类

如果我们的 TypeScript 类没有很多方法，那么我们不需要定义一个类来使用它对变量和函数进行类型化。

另外，如果我们只有一个实例，那么将逻辑包装在一个类中是没有意义的。

实例化类引入了复杂性，并且在缩小代码时很难优化。

相反，我们可以定义对象文字，并使用接口或`typeof`操作符来获取对象的类型。

例如，为对象编写以下接口:

```
interface Person {
    firstName: string;
    lastName: string;
}const person: Person = {
    firstName: 'Jane',
    lastName: 'Smith'
}
```

然后我们可以像上面的`person`一样使用它们。

同样，如果我们不知道对象的确切结构，我们可以使用`typeof`操作符，如下所示:

```
const person = {
    firstName: 'Jane',
    lastName: 'Smith'
}const person2: typeof person = {
    firstName: 'Joe',
    lastName: 'Smith'
}
```

使用`typeof`是定义类型的一种便捷方式，无需编写大量代码。

如果我们的对象有方法，我们可以做和上面一样的事情:

```
interface Person {
    firstName: string;
    lastName: string;
    fullName: (firstName: string, lastName: string) => string
}const person: Person = {
    firstName: 'Jane',
    lastName: 'Smith',
    fullName(firstName: string, lastName: string) {
        return `${firstName} ${lastName}`;
    }
}
```

TypeScript 会用`typeof`做类型推断:

```
const person = {
    firstName: 'Jane',
    lastName: 'Smith',
    fullName(firstName: string, lastName: string) {
        return `${firstName} ${lastName}`;
    }
}
const person2: typeof person = {
    firstName: 'Joe',
    lastName: 'Smith',
    fullName(firstName, lastName) {
        return `${firstName} ${lastName}`;
    }
}
```

如果`person2`缺少的话，它会强迫我们将`fullName`方法添加到`person2`中，并且会强迫我们添加参数并返回一个字符串。

否则，我们会得到编译错误。

# 使用函数类型

`Function`类型是函数的通用类型。就像`any`对于变量。我们应该在函数中指定参数的数据类型和返回类型。

相反，我们应该向参数添加类型并返回类型，如下所示:

```
type ArithmeticFn = (a: number, b: number) => number
const add: ArithmeticFn = (a: number, b: number): number => a + b;
```

我们有类型别名`ArithmeticFn`和`add`中的类型。代码中的类型注释已经足够了。

同样，我们可以再次使用`typeof`进行类型推断:

```
const add = (a: number, b: number): number => a + b;
const subtract: typeof add = (a, b) => a-b
```

那么`subtract`也具有与`add`相同的参数和返回类型。

# 扰乱类型推理

我们不应该把无用的类型注释与类型推断放在一起就行了。

例如，在以下示例中:

```
const courses = [{
    name: 'Intro to TypeScript'
}]
const [course] = courses;
const newCourse: any = {...course};
newCourse.description = 'Great intro to TypeScript';
courses[0] = newCourse;
```

我们有一个不应该有的额外的`any`注释。

相反，如果我们想在复制后给对象添加一个新的属性，我们可以编写如下代码:

```
const courses = [{
    name: 'Intro to TypeScript'
}]
const [course] = courses;
const newCourse = {...course, description: 'Great intro to TypeScript'};
courses[0] = newCourse;
```

那么 TypeScript 编译器不会抛出错误，我们仍然可以从 TypeScript 编译器得到类型推断，因为我们没有使用`any`类型。

# 复制和粘贴分部类型定义

另一件我们不应该做的事情是将其他地方的分部类型定义复制并粘贴到我们自己的代码中。

如果我们想得到一个未知类型的对象的类型，我们可以使用`typeof`操作符。

例如，我们可以写:

```
const person = {
    firstName: 'Joe',
    lastName: 'Smith', 
    age: 20
}const person2: typeof person = {
    firstName: 'Jane',
    lastName: 'Smith', 
    age: 20
}
```

TypeScript 编译器将自动识别`person`的类型，并在我们定义`person2`时进行检查，以检查是否一切都在那里。

## 查找类型

我们也可以使用对象的一个属性作为它自己的类型。这称为查找类型。

例如，我们可以写:

```
const person = {
    firstName: 'Joe',
    lastName: 'Smith', 
    age: 20
}const foo: typeof person.firstName = 'foo';
```

在上面的代码中，TypeScript 识别出`person.firstName`的类型是一个字符串，所以`typeof person.firstName`应该是一个字符串。

## 映射类型

我们可以创建映射类型，将一个类型的所有属性映射到其他类型。

例如，我们可以通过编写以下内容使类型的所有属性都是可选的:

```
interface Person{
    firstName: string;
    lastName: string;
}type Optional<T> = {
    [P in keyof T]?: T[P];
};const partialPerson: Optional<Person> = {};
```

上面的代码可以编译和运行，因为我们从`Person`创建了一个新的类型，其中所有的属性都是可选的。

## 获取函数的返回类型

我们可以得到带有`ReturnType`泛型的返回类型函数来传入函数的类型。然后我们会得到这个函数的返回类型。

例如，如果我们有:

```
const add = (a: number, b: number) => a + b;
const num: ReturnType<typeof add> = 1;
```

那么`ReturnType<typeof add>`就会是`number`，所以我们要给它赋一个数。

TypeScript 的类型推断在这里也起作用。从 TypeScript 2.8 开始，`ReturnType`泛型就可用了

# 结论

在许多情况下，我们可以使用`typeof`操作符、查找类型或映射类型来灵活地注释类型，而不会失去类型检查功能。

这意味着我们应该尽可能不使用`any`。

此外，我们不需要仅仅为一些对象添加类型的类。