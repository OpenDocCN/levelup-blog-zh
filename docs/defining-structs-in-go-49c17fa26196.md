# 在 Go 中定义结构

> 原文：<https://levelup.gitconnected.com/defining-structs-in-go-49c17fa26196>

## 最终的旅行

Go 的*结构*是字段的类型集合。它们对于将数据组合在一起形成记录很有用。

![](img/8d27e10bc6200e706a9d9a6c4df26b67.png)

加布里埃拉·克莱尔·马里诺在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 基础知识:

Go 的结构是字段的类型集合。它们对于将数据组合在一起形成记录很有用。在面向对象编程范例中，结构可以与类相比较。假设 struct 是一个声明其属性 age 和 name 的雇员。

一个结构可以有相同或不同数据类型的不同字段。例如，Tom 是一种雇员类型(结构类型),其属性/字段为 firstName、lastName 和 salary。

# **定义&创建结构:**

要创建一个结构，它需要一个描述该结构包含的字段的蓝图，定义以关键字`type`开始，后跟结构的名称。在这之后，`struct`关键字后跟一对大括号`{}`，在这里声明该结构将包含的字段。

```
type StructName struct {
    field fieldType
}
```

一旦定义了结构，就可以声明使用该结构定义的变量。此示例定义并使用了一个结构:

```
package main

import "fmt"

type Employee struct {
    FirstName string
    LasttName string
    Salary int
}

func main() {
	var e := Employee{
		FirstName: "Tommy",
	}
	fmt.Println(e.FirstName)
}

output
Tommy
```

我们首先在这个例子中定义一个`Employee`结构，包含一个`string`类型的`FirstName`字段。在`main`的主体中，我们通过在类型名称`Employee`后放置一对括号来创建`Employee`的实例，然后为该实例的字段指定值。`e`中的实例将把它的`FirstName`字段设置为“Tommy”。在`fmt.Println`函数调用中，我们检索实例字段的值，方法是在创建实例的变量后加一个句点，后跟我们想要访问的字段的名称。例如，本例中的`e.FirstName`返回`FirstName`字段。

> 💡*使用上述语法创建 struct 时，* ***逗号*** *(，)在最后一个字段赋值后很重要。*

# **替代方法**

我们也可以如下定义结构

```
package main

import "fmt"
type Employee struct {
    FirstName string
    LasttName string
    Salary int
}
func main() {
    var e Employee
    e.FirstName = "Tommy"
    fmt.Println(e.FirstName)
}
Output
Tommy
```

首先我们声明一个类型为`Employee`的变量`e`,它现在将保存结构的零值。(如果你喜欢阅读，这是一份了解零价值的好材料:[https://Dave . Cheney . net/2013/01/19/什么是零价值以及它为什么有用](https://dave.cheney.net/2013/01/19/what-is-the-zero-value-and-why-is-it-useful))

## getter/setter:

获取和设置 struct 字段非常简单。当一个结构变量被创建时，我们可以使用`.` ( *点*)操作符来访问它的字段。

在上面的程序中，我们创建了一个有 3 个字段的结构`e`。要给字段`FirstName`赋值，需要使用语法`e.FirstName = "Tommy"`。`e`中的实例将把它的`FirstName`字段设置为“Tommy”。在`fmt.Println`函数调用中，我们检索实例字段的值，方法是在创建实例的变量后加一个句点，后跟我们想要访问的字段的名称。例如，本例中的`e.FirstName`返回`FirstName`字段。

# 内嵌结构

除了定义一个新的类型来表示一个结构，还可以定义一个内联结构。这些动态结构定义在结构的作用域超出了它所操作的函数的情况下是有用的。例如，测试通常使用结构来定义组成特定测试用例的所有参数。当你看不到结构体在多个地方被使用时，管理结构体将会很乏味。

内联结构定义出现在变量赋值的右侧。实例化必须通过为您定义的每个字段提供一对额外的大括号来立即发生。下面的示例显示了内联结构定义:

```
package main

import "fmt"

func main() {
	e := struct {
		FirstName string
		LastName string
	}{
		FirstName: "Tommy",
		LastName: "Aberdeen",
	}
	fmt.Println(e.FirstName, ",", e.LastName)
}

Output
Tommy,Aberdeen
```

这个例子没有用关键字`type`定义一个描述我们的结构的新类型，而是通过将`struct`定义放在短赋值操作符`:=`之后来定义一个内联结构。我们定义结构的字段。您最常看到内联结构声明的地方是在测试期间，因为通常一次性结构被定义为包含特定测试用例的数据和期望。

# 指向结构的指针

我们可以不用创建一个结构体，而是用一条语句创建一个指向结构体值的指针。

创建指向结构的指针的语法如下。

```
e := &StructType{...}
```

让我们创建一个指向结构值的指针`tommy`。

```
package main

import "fmt"

type Employee struct {
    FirstName string
    LasttName string
    Salary int
}

func main() {
	e := &Employee {
		FirstName: "Tommy"
		LastName: "Aberdeen"
	}
	fmt.Println("First name", (*e).FirstName)
}

Output
Tommy
```

在上面的程序中，由于 eis 是一个指针，我们需要使用`*e` **解引用语法**来获取它所指向的结构的实际值，并使用`(*e).FirstName`来访问该结构值的`FirstName`。

Go 还提供了另一种访问字段**的语法，无需先解引用字段**，如下所示

```
e := &Employee {
  FirstName: "Tommy"
  LastName: "Aberdeen"
 }
 fmt.Println("First name", e.FirstName) // e is a pointer
```

# 嵌套结构

结构字段可以是任何数据类型。因此，我们可以用一个结构字段保存另一个结构。当结构字段具有结构值时，该结构值称为嵌套结构，因为它嵌套在父结构内。

**例如:**

```
package main

import "fmt"

type Salary struct {
    TravelAllowance int
    OvertimeAmount int
    Base int
}

type Employee struct {
    FirstName string
    LasttName string
    Salary Salary
}

func main() {
	e := Employee {
		FirstName: "Tommy"
		LastName: "Aberdeen"
                Salary:   Salary{100, 10, 10}
	}
	fmt.Println("employee e firstname : ", e.FirstName)
        fmt.Println("employee e travel allowance: ", e.Salart.TravelAllowance)
}
```

在上面的例子中，我们创建了一个新的结构类型`Salary`，它定义了一个雇员的薪水。然后，我们修改了`Employee` struct 类型的`salary`字段，该字段现在保存类型`Salary`的值。

当创建类型为`Employee`的`e`结构时，我们初始化了所有字段。由于`salary`字段保存了类型`Salary`的结构，我们在初始化`salary`结构时使用了一种排除字段名的简单方法给它分配了结构值。

通常，你会使用`struct.field`语法访问一个结构的字段，就像我们之前看到的那样。您可以用与返回一个**结构**的`e.Salary`相同的方式访问`salary`字段。然后，您可以使用相同的方法访问这个**嵌套结构**的字段(*或更新*)，例如，`ross.Salary.TravelAllowance`

# **嵌套结构—匿名:**

```
type Config struct {
    Server struct {
        Host string `json:"host"`
        Port string `json:"port"`
    } `json:"server"`
    Postgres struct {
        Host     string `json:"host"`
        User     string `json:"user"`
        Password string `json:"password"`
        DB       string `json:"db"`
    } `json:"postgres"`
}
```

基本上，我们可以通过使用嵌套的匿名结构将 3 个`Config`、`Server`和`Postgres`结构声明为单一类型，而不是声明它们:

# 导出的字段

任何以大写字母开头的变量或类型都会从该包中导出。比如`Employee`、`Salary`

我们还可以控制导出结构的哪些字段在包外可见(*或导出的*)。要导出一个结构的字段名，我们必须遵循同样的**大写字母**方法。

```
type Config struct {
    Server struct {
        Host string `json:"host"`
        port string `json:"port"`
    } `json:"server"`
    Postgres struct {
        Host     string `json:"host"`
        User     string `json:"user"`
        password string `json:"password"`
        DB       string `json:"db"`
    } `json:"postgres"`
}
```

在上面的结构中，只有`Host` `User`和`DB`被导出到外部包

# 结构比较

如果两个结构属于同一类型并具有相同的字段值，则它们是可比较的。

```
package main

import "fmt"

type Salary struct {
    TravelAllowance int
    OvertimeAmount int
    Base int
}

type Employee struct {
    FirstName string
    LasttName string
    Salary Salary
}

func main() {
	Tommy := Employee {
		FirstName: "Tommy"
		LastName: "Aberdeen"
                Salary:   Salary{100, 10, 10}
	}
        Sammy := Employee {
          FirstName: "Sammy"
          LastName: "Nottingham"
                        Salary:   Salary{100, 10, 10}
         }
	fmt.Println(Tommy == Sammy)
}
```

上面的程序打印了`false`因为`To`和`Sammy`属于相同的结构类型`Employee`但是有不同的字段名，你可以试着让所有的字段名都相同并进行比较，它将打印`true`

但是，如果一个结构有可以比较的**映射**字段类型，例如不可比较的**映射**，那么这个结构就不可比较。

例如，如果`Employee`结构类型有一个数据类型为`map`的`leaves`字段，我们就不能进行上面的比较。

```
type Employee struct {
    FirstName string
    LasttName string
    SubjectEnrolled map[int]string
}
```

# 结论

结构是用于组织信息的数据集合。当程序处理大量数据并具有结构时，将哪些`string`或`int`变量属于一起或哪些不同变得更容易。下一次，如果你发现自己在处理多组变量，问问自己是否可以使用`struct`将这些变量更好地组合在一起。那些变量可能一直在描述一个对象类型。