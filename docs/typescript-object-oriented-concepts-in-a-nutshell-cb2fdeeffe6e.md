# 简而言之，面向对象的概念

> 原文：<https://levelup.gitconnected.com/typescript-object-oriented-concepts-in-a-nutshell-cb2fdeeffe6e>

## TypeScript 对象的所有主要方面都在一个地方，这样您就不必到处搜索它们了

![](img/aaa76c11406f90ea870c3dca5191dd78.png)

(TypeScript ===具有超能力的 JavaScript)= > true

如果您像我一样来自传统的面向对象(OO)背景，并且被 JavaScript 的怪癖吓住了——不要害怕——因为 TypeScript 已经覆盖了您。本教程将浏览 TypeScript 对象的所有要点，同时给出一些例子，并向您展示它们何时会派上用场。TypeScript 对象的特性与传统的面向对象语言如 Java 和 C#有很大的重叠，所以如果你是一个经验丰富的程序员，你可能已经熟悉了其中的一些。如果没有，这将是一个面向对象风格编程的伟大概述。

# 类型脚本对象与 JavaScript 对象

TypeScript 对象只是 JavaScript 函数对象的语法糖。当在 JavaScript 中使用函数对象作为类时，有很多重复的代码，这就是为什么为 ES6 实现了`class`。TypeScript 将 ES6 类带到了一个更高的现实层面，不仅添加了类型，还添加了对象特性，如`public`、`private`、`abstract`等。如果你有兴趣了解更多关于 JavaScript 函数对象的特性(我强烈推荐这样做),请点击这里查看我的中型文章。

# 对象概述

TypeScript 中的对象(像在所有面向对象编程中一样)是有用的，因为它们让我们脱离真实世界的场景来建模我们的程序。对象是类的实例(以该对象为值类型的特定变量)。把类想象成一组方法和变量。假设我们想要一个名为 *PetStore* 的程序，只卖猫和狗。如果我们想知道狗的年龄，狗可以有像`age`和`breed`这样的属性，也可以有像`getRelativeAge`这样的方法。如果我们在谈论一个特定的狗对象，比如`Spot`或`Bingo`，它们的属性值被设置，那么这些对象就是`Dog`类的实例。

使用`class`关键字实现类，就像在 ES6 中一样。为了创建一个对象，我们用关键字`new`调用这个类，它触发构造函数并返回一个*实例-对象*，就像在普通 JavaScript 中一样。由于 JavaScript 中的对象在技术上只是一组键/值对，我喜欢用术语实例对象来指代用`new`关键字返回的对象。

`Spot`是`Dog`的实例对象。

```
class Dog
{
    age: number
    breed: string constructor(age: number, breed: string)
    {
        this.age = age
        this.breed = string
    } getRelativeAge(): number
    {
        return this.age * 7
    }
}let Spot = new Dog(2, 'Labrador')
```

等效使用 ES5 中的函数对象。

```
function Dog(age, breed)
{
    this.age = age
    this.breed = breed
}Dog.prototype.getRelativeAge = function() {
    return this.age * 7
}var Spot = new Dog(2, 'Labrador')
```

# 遗产

既然您已经知道了如何创建对象，并且可以看到它们在 JavaScript 中是如何工作的，那么让我们开始学习 TypeScript 继承。在我们的宠物商店项目中，我们出售的是狗和猫，但也可能有不同品种的狗和猫，对吗？狗和猫也可能有一些相同的属性，比如`age`和`weight`。超类(也称为父类)允许将相关的对象组合在一起，以便它们可以继承相似的属性。为了从父类继承，我们使用了`extends`关键字。当你扩展一个类时，所有的属性和方法都会被传递下去。我们现在可以在父类`Animal`的基础上扩展，而不是每次在我们的宠物店添加新动物时都创建`age`和`weight`。通过扩展子类，多级继承也是可能的。

`super`关键字在继承中有两个作用。首先，它作为一个函数，在父类的构造函数中使用。它必须在子对象构造函数中的`this`之前被调用。另一个是它允许我们访问父对象的方法(而不是属性)。

动物是`Dog`的父类:

```
class Animal
{
    age: number
    breed: string constructor(age: number, breed: string)
    { 
        this.age = age
        this.breed = breed
    } makeSound_(sound: string): void
    {
        console.log(sound)
        console.log(sound)
        console.log(sound)
    }
}
```

基本继承使用`super`:

```
class Dog extends Animal
{
    playsFetch: boolean constructor(age: number, breed: string, playsFetch: boolean)
    {
         **super**(age, breed) // call parent constructor
         this.playsFetch = playsFetch
    } makeSound(): void
    {
        **super**.makeSound_('woof woof')
    } getAgeInHumanYears(): number
    {
        return this.age * 7    // super.age will throw error
    }
} class Cat extends Animal
{
    constructor(age: number, breed: string)
    {
        **super**(age, breed)
    } makeSound(): void
    {
        **super**.makeSound_('meow meow')
    }
}
```

> JavaScript ES6 继承以同样的方式工作，但是在 TypeScript 中，当处理父类时有一个额外的访问控制特性。ES6 也不允许在方法之外定义类级别的变量。

# 访问控制

访问控制指的是我们可以在哪里使用类的属性和方法。如果你曾经浏览过一个 OO 语言的代码，你可能会注意到像`public`、`private`和`protected`这样的关键词。让我们回顾一下每一项的用途以及它们为什么有用。

假设我们的 PetStore 程序有一个名为`PetStore`的类。如果这个类想要调用我们的`Dog`对象上的方法，那么这些方法将需要被标记为`public`。当一个方法或变量是公共的，这意味着它可以被我们程序的另一部分访问。当我们在一个变量或者方法上没有任何修饰符的时候，这和标记它`public`是一样的。

```
class Dog
{
    **public** name: string // leaving out **'public'** would work too
}class PetStore
{
    dogs: Array<Dog> printAllDogNames(): void
    {
        this.dogs.forEach(dog => {
            console.log(dog.name)
        })
    }
}
```

允许其他编码人员直接访问对象的属性通常不是一个好主意。最好使用*getter*和*setter*来访问/修改类属性，这样我们可以在设置值时传递一些逻辑并防止错误。例如，狗的名字不应该是假的，而且应该在一定长度之内。一个真实的狗名绝不会超过 10-20 个字符。为了使一个类变量/方法只能在这个类中访问，我们应该把它标记为`private`。TypeScript 类内置了`get`和`set`修饰符，每当试图访问一个属性时，它们将触发我们的 getters 和 setters。

```
class Dog
{
    **private** _name: string // beginning underscore is convention **get** name(): string
    {
       return this._name
    } **set** name(name: string): void
    {
        if(!name || name.length > 20) {
            throw new Error('Name invalid')
        }
        else {
            this._name = name
        }
    }
}class PetStore
{
    **private** _dogs: Array<Dog> // we changed this to private too constructor()
    {
        this._dogs = [new Dog()]
        this._dogs[0].name = 'Fido' // will call **'set'
    }** printAllDogNames(): void
    {
        this._dogs.forEach(dog => {
            console.log(dog.name) // will call **'get'**
        })
    }
}
```

最后，让我们看看`protected`关键字。受保护意味着变量/方法只能在父类的子类中访问。还记得`Animal`父类的`makeSound`方法吗？我们不应该能够在任何从动物或动物本身继承的类上从外部访问那个方法，因为动物不会发声。我喜欢在受保护的属性后面加上下划线，尽管这不是惯例。

```
class Animal
{
    **protected** makeSound_(sound: string): void
    {
        console.log(sound)
        console.log(sound)
        console.log(sound)
    }
}class Dog extends Animal
{
    makeSound(): void
    {
        super.makeSound_('woof woof')
    }
}class PetStore
{ makeSomeSounds(): void
    {
        let dog = new Dog()
        dog.makeSound() // => **'woof woof' 'woof woof' 'woof woof'** let animal = new Animal()
        animal.makeSound_() // => **NOT ALLOWED**
    }
}
```

# 其他修饰符

在讨论 TypeScript 类时，还有另外两个重要的修饰符:`static`和`readonly`。如果我们想访问一个类的属性，而不必麻烦地返回一个实例对象(用`new`调用对象)，那么我们可以将它标记为`static`，它将被设置在类(函数对象)本身上。这对于不依赖于任何动态属性的方法和类变量很有用。例如，一只狗将永远是同一物种。

```
class Dog
{
    **static** species = 'Canis Familaris'
    age = 10
}class PetStore
{
    printSpecies(): void
    {
        console.log(Dog.species) // => **'Canis Familaris'**
        console.log(Dog.age) // => **undefined**
    }
}
```

关键字`readonly`是不言自明的。它用于类级别的变量，意味着该值不能被重新分配。创建类时初始化的值应该是只读的，并且你知道这些值永远不会改变。我们的`Dog`类的`species`属性就是一个很好的例子。无论我们赋予狗什么样的属性，它永远是同一物种。

```
class Dog
{
    static **readonly** species = 'Canis Familaris'
}class PetStore
{
    printSpecies(): void
    {
        console.log(Dog.species) // => **'Canis Familaris'**
        Dog.species = 'Terdus Maximus' // => **NOT ALLOWED**
    }
}
```

# 接口

每当我们想说被传递的对象有一组特定的属性时，我们可以使用接口。接口是漂亮的小工具，可用于多种情况。

最直接想到的就是测试。假设我们的`Dog`类中有一个执行 I/O 调用的方法，我们想要对调用该方法的`PetStore`类中的方法进行单元测试。我们不想在每次运行单元测试时都触发 I/O 调用，但是我们仍然需要一个评估为`Dog`类型的对象。让我们创建一个`iDog`接口，它为我们为单元测试创建的常规类和模拟类指定了一个方法。

```
interface iDog
{
    getPedigree: Function
}class Dog implements iDog
{
    getPedigree(): Promise<Pedigree>
    {
        return someThirdPartyIoCall('...')
    }
}class MockDog implements iDog
{
    getPedigree(): Promise<Pedigree>
    {
        return new DummyPedigreeObject()
    }
}async function methodToBeTested(dog: iDog): Promise<void>
{
    try {
        let pedigree = await dog.getPedigree()
        // do assertions here
    }
    catch(err) {
        console.log(err)
    }
} // Real World
methodToBeTested(new Dog()) // During Testing
methodToBeTested(new MockDog()) 
```

这只是使用接口的一个小例子，还有更多的用途。我建议查看这里的 TypeScript 文档[以获取更多信息。](https://www.typescriptlang.org/docs/handbook/interfaces.html)

# 抽象类和方法

可以把抽象类看作是常规父类和接口的结合。抽象类像接口一样为其他类定义属性，但是它们的一些方法可能包含不同于接口的实现。没有实现的方法必须标记为`abstract`，它的包含对象也必须如此。抽象类可能不会被实例化(不能使用`new`)并且在你知道你永远不会直接需要父类的时候有用。

对于猫和狗，我们都有`age`属性，并想知道它们各自的人类年龄。定义这一点的方法因动物而异。让我们使用一个抽象类。

```
**abstract** class Animal
{
    protected age_: number **abstract** getRelativeAge(): number;
}class Dog extends Animal
{
    getRelativeAge(): number
    {
        return this.age_ * 7
    }
}class Cat extends Animal
{
    getRelativeAge(): number
    {
        return this.age_ * 6
    }
}
```

> **注意**:这并不是如何正确计算猫和狗的年龄的准确表述。

# 结论

关于 TypeScript 类，还有比本教程所涵盖的更多的东西，但是希望这个快速简单的概述有助于更好地理解事情。无论您或其他人是否打算广泛使用 TypeScript，这里涉及的面向对象概念与其他面向对象语言有很多重叠，这仍然是一本非常有用的读物。Typescript 对于学习 OO 也很好，因为这是一种不如 Java、C#或 C++正式的语言。