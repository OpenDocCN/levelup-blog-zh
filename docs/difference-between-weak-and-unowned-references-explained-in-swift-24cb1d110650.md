# Swift 中解释了弱引用和无主引用之间的差异

> 原文：<https://levelup.gitconnected.com/difference-between-weak-and-unowned-references-explained-in-swift-24cb1d110650>

![](img/98265907d99150286b5026da2daac1d0.png)

照片由[万花筒](https://unsplash.com/@kaleidico?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/t/business-work?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

了解 Swift 中内存管理的工作方式是一个很好的实践。Swift 使用*自动引用计数* (ARC)自动管理 app 的内存，无需编写任何额外的代码。当实例不再被使用时，它释放实例的内存。但是有时我们必须提供更多的信息来避免*内存泄漏*和*死锁*。

> ARC 仅适用于类的实例，因为类是引用类型。它不适用于结构和枚举，因为它们是值类型。

默认情况下，类实例用它们的方法、常量、变量等创建一个*强引用*。意味着在保留强引用之前不会释放它。有时会导致*内存泄漏*和*死锁*，我们可以通过声明*弱引用*或*无主引用*来避免这种情况。

**弱引用和无主引用的区别**

*弱引用*是可选类型，这意味着一旦它引用的实例释放内存，弱引用将设置为 *nil* 。

另一方面，*无主引用*是非可选类型，它永远不会被设置为 *nil* 并且总是有某个值*。*

**弱引用**

可以在变量或属性前用`weak`关键字声明*弱引用*。以下示例将解释如何使用*弱引用*。

让我们用字符串的属性`name`类型和可选类型`zoo`来实现`class Animal`。

```
**class** Animal {
    **let** name: String
    **var** zoo: Zoo? **init**(name: String) {
        self.name = name
    } **deinit** { 
       print("\(name) is deinitialized.")
    }
}
```

现在，定义另一个类*动物园*，其属性*位置*类型字符串*和*动物*为*弱引用*，这是一个可选类型。*

```
**class** Zoo {
     **let** location: String
     **weak** var animal: Animal?

     **init**(location: String) {
         self.location = location
     } **deinit** { 
         print("Zoo at \(location) is deinitialized.")
     }
}
```

定义可选类型的两个变量 *tiger* 和 *logoaZoo* 并设置为实例*。然后，使用 unwrap 将两个实例链接在一起。*

```
**var** tiger: Animal? = Animal(name: "Amber")
**var** lagoaZoo: Zoo? = Zoo(location: "11th St, Corona, NY 11368, United States")tiger!.zoo = lagoaZoo
lagoaZoo!.animal = tiger
```

当我们将 *tiger* 变量设置为 *nil* 时，它破坏了对 *Animal* 实例的强引用，并从内存中释放了它。

由于不再有对动物实例的强引用，所以*动物*属性也将被设置为*零。*

```
tiger = **nil**
```

如果我们将 *logoaZoo* 设置为 0，它将破坏对 Zoo 实例的强引用，并且也将从内存中释放。

```
lagoaZoo = **nil**
```

**无主引用**

可以在变量或属性前用 *unowned* 关键字声明 *unowned 引用*。下面的例子将解释如何使用*无主引用*。

用字符串类型的属性*名称*和可选类型的*银行*定义类*雇员*。

```
**class** Employee {
     **let** name: String
     **var** bank: Bank? **init**(name: String) {
         self.name = name
     } **deinit** { 
         print("\(name) is deinitialized.")
     }
}
```

让我们定义另一个类 *Bank* ，其属性名称为字符串类型，雇员为无主属性(非可选)。

```
**class** Bank {
    **let** name: String
    **unowned** let employee: Employee **init**(name: String,employee: Employee) {
        self.name = name
        self.employee = employee
    } **deinit** { 
        print("\(name) is deinitialized.")
    }
}
```

定义可选类型的变量*和*，并设置为实例*。然后，将银行实例分配给员工的银行财产。*

```
**var** ana: Employee? = Employee(name: "Ana")
ana!.bank = Bank(name: "SPI Bank", employee: ana!)
```

当我们将 *ana* 设置为 0 时，没有对 *Employee* 实例的强引用，它将被释放。

因为没有与*库*实例的强引用，所以它也将从内存中被释放。

```
ana = **nil**
```

**结论**

了解 Swift 中的内存管理如何打破对实例的强引用并避免应用程序中的任何内存泄漏是很有用的。

要查找关于 iOS 开发、Xcode 和 Swift 的最新提示和技巧，请访问[https://www.gurjit.co](https://www.gurjit.co)

谢谢！