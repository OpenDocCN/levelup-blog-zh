# 目标 Swift 开发人员的基础知识

> 原文：<https://levelup.gitconnected.com/objective-c-basics-for-swift-developers-65a006b40234>

## Swift 开发人员快速参考和备忘单学习目标-C

Swift 是一种功能强大的编程语言，在 2014 年 WWDC 上发布，作为 Objective-C 的替代品。但由于许多现有的应用程序都是用 Objective-C 编写的，所以了解或至少对 Objective-C 有基本的了解可能会很有用。

这篇文章为想学习或查找 Objective-C 细节的 Swift 开发人员提供了一个关于 Objective-C 的快速参考。

![](img/bf9e6522b288fe9d1bd575ff793b6e6f.png)

老朋友 Obji 和 Swifty

# 变量

雨燕

对于定义变量，Swift 为常量提供了 let 关键字，为变量提供了 var 关键字。Swift 提供了类型推断和可选的数据类型。

```
*let* name = "Swifty" // declaring and assigning a constant*var* age: Int? // declaring an optional Int variableage = 7 // reassigning the variableage = nil // nullifying the variable*var* year = 2014 // the compiler automatically infers the `Int` type*let* fruits = ["apple", "orange"] // desclaring and assigning an array of strings
```

*目标-C*

Objective-C 不支持类型推断，我们总是需要显式地指定变量的类型。有像 int，float，char 等基本类型。以及诸如 NSString、NSNumber 等对象类型。

Objective-C 没有显式的可选类型，所有对象类型都可以无效。

```
NSString const *name = @"Obji"; // declaring and assigning a constantNSNumber *age; // declaring a variableage = 7; // reassigning the variableage = nil; // nullifying the variableNSArray<NSString*> *fruits = @[@"apple", @"orange"]; // desclaring and assigning an array of strings
```

Objective-C 中的语法一开始可能会令人困惑。让我们来分解一下:

*   Objective-C 中的每个 NSString 都有一个@前缀。
*   用星号*表示我们正在创建一个指向对象的指针。就像在 Swift 中声明一个引用类型变量一样。
*   在创建数组时，Objective-C 提供了一个简短的语法，在[]前面带有前导@符号。

# 性能

在这两种语言中，声明属性与声明变量的方式基本相同。不同之处在于，我们将它们声明为类型的一部分，并可以对它们应用附加属性，例如用于访问控制或内存管理。

*雨燕*

在 Swift 中，我们可以声明类、结构或枚举的存储属性。默认情况下，Swift 属性很强，具有内部访问级别。

```
*private* *let* firstName: String // declaring a private constant property*private*(*set*) *var* lastName: String // declaring a read-only property*var* age: Int // declaring an internal property*weak* *public* *var* parent: Parent? // declaring a weak public property
```

*物镜-C*

在 Objective-C 中，属性仅用作类的一部分。它们以@property 关键字开头，后面是用于内存管理和访问控制的逗号分隔的属性。

```
@property NSString *firstName; // declaring a strong property@property (readonly) NSString *lastName; // declaring a read-only property@property (*weak*) Parent *parent; // declaring a read-only property
```

Objective-C 属性的可见性不是通过属性实现的，而是通过我们在类声明中放置它们的位置实现的。我们会在课程部分讨论这个问题。

# 方法

*雨燕*

在 Swift 中，我们可以在类、结构和枚举上定义方法。

```
// method declaration with no return type and no parameters*func* emptyCard() // method declaration with two parameters and a return value*func* search(_ searchQuery: String, category: String) -> [Tea] // default parameters*func* walk(withSpeed speed: Int = 20) // method implementation*func* search(_ searchQuery: String, category: String) -> [Tea] { *return* []} // calling a method*let* results = teaShop.search("camomile", category: "herbs")
```

*物镜-C*

在 Objective-C 中，类是唯一可以定义方法的类型。不支持默认参数。

```
// method declaration with no return type and no parameters// `void` represents the absence of a type.- (void)emptyCard; // method declaration with two parameters and a return value- (NSArray<Tea *> *)search:(NSString *)searchQuery category:(NSString *)category; // implementation of a method with two parameters and a return value- (NSArray<Tea *> *)search:(NSString *)searchQuery category:(NSString *)category { *return* [];} // calling a methodNSArray<NSString *> *results = [teaShop search:@"camomile" category:@"herbs"];
```

# 类和结构

*Swift*

在 Swift 中，我们区分作为引用类型的类和作为值类型的结构。

```
*import* Foundation*class* TeaShop {// define public & private properties & methods}*struct* Tea {// define public & private properties & methods}// using a class*let* shop = TeaShop()// using a struct*let* tea = Tea()
```

*目标-C*

在 Objective-C 中，我们需要两个单独的文件来定义一个类——类标题为。h 文件和类实现。m 文件。所有类都是从基类 NSObject 派生的，它提供了内存分配和初始化的基本方法。

TeaShop.h

```
@*import* Foundation;@interface TeaShop : NSObject// public properties & method definitions@end
```

TeaShop.m

```
#*import* "TeaShop.h"@interface TeaShop ()// private properties & method definitions@end@implementation TeaShop {// private instance variables}// methods implementation@end
```

在 Objective-C 中创建新对象是一个两步过程。首先，我们为对象分配内存，然后初始化它。

```
TeaShop *shop = [[TeaShop alloc] *init*];// or the shorter version, that will call `alloc` and `init` for usTeaShop *shop = [[TeaShop *new*];
```

结构也受支持，但只是作为一个可以在 Objective-C 中使用的 C 构造，因为 Objective-C 是 C 的超集。

```
// Declaration of a struct*struct* Position { int x; int y;};// Usage of a struct*struct* Position position;position.x = 5;position.y = 10;
```

# 枚举

雨燕

像结构一样，Swift 枚举也是一级类型，它提供了很多灵活性，比如关联类型、计算属性、自定义初始值设定项等等。

```
*enum* TeeCategory { *case* herbal *case* green *case* fruits}*let* category = TeeCategory.green
```

*目标-C*

与结构类似，Objective-C 只有 C 枚举可用。这些比 Swift 枚举灵活得多。

```
typedef NS_ENUM(NSInteger, TeeCategory) { TeeCategoryHerbal, TeeCategoryGreen, TeeCategoryFruits} TeeCategory;TeeCategory category = TeeCategoryFruits;
```

# 扩展ˌ扩张

*雨燕*

在 Swift 中，我们可以通过使用扩展为现有的类、结构、枚举或协议添加新功能。

```
*extension* String { *var* asURL: URL? { URL(string: *self*) }}
```

*物镜-C*

在 Objective-C 中，我们可以使用类别为现有的类添加新的功能。像类一样，类别由两个文件组成，一个头文件和一个实现文件。

NSString+URL.h

```
@interface NSString (URL)-(NSURL*)toURL;@end
```

NSString+URL.m

```
#*import* "NSString+URL.h"@implementation NSString (URL)-(NSURL*)toURL { *return* [[NSURL alloc] initWithString:*self*];}@end
```

# 结论

本指南仅介绍了基本的 Swift 和 Objective-C 用法。当然，在这两种语言中还有很多东西需要探索，比如协议、泛型、错误处理等等。我希望，本指南已经为您深入研究更高级的语言特性打下了良好的基础。

*最初发表于*[*https://tanaschita.com*](https://tanaschita.com/20210206-objective-c-for-swift-developers)*。*