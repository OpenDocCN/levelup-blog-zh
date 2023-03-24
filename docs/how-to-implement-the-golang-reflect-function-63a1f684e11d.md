# 如何实现 Golang 反射函数

> 原文：<https://levelup.gitconnected.com/how-to-implement-the-golang-reflect-function-63a1f684e11d>

深入分析 Golang 体现的基本原则

![](img/947d44ad75697edad63b8291652f6248.png)

照片由[米拉](https://unsplash.com/@imoflow_me?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

`reflect`包的实现主要基于操作内存对齐和通过`unsafe`包调用`runtime`变量。

最近由于工作需要，我研究了它们的底层实现，今天分享给大家。

首先我们来看一个简单的`reflect`的例子。`reflect.TypeOf`和`reflect.ValueOf`方法将一个`runtime type` and 变量转换成一个`reflect type` and 变量，依靠`unsafe`操作内存对齐来强制转换。`reflect type`和变量与`runtime`中的相同。可以自由操作。

最后，`reflect.Value`调用`Interface()`方法将变量从`reflect`状态转换回`runtime`状态。

```
package mainimport (
    "fmt"
    "reflect"
)type Student struct {
    Name string
    Age  int
}func main() {
    s := new(Student)
    fmt.Println(reflect.TypeOf(s))
    fmt.Println(reflect.TypeOf(s).Elem())
    fmt.Println(reflect.TypeOf(*s))v := reflect.ValueOf(s).Elem()
    v.Field(0).SetString("66")
    fmt.Printf("%#v\n", v.Interface())
}
```

**运行时变量。**

首先我们简单的按照 Golang 的规则定义一个变量类型`Value`。`Value`有两种类型的成员属性`typ`和`ptr`。`typ`是表示这个变量是什么对象的类型，`ptr`是指向这个变量地址的地址。

我们来看看官方源代码中的定义。

```
type Value struct {
    typ Type
    ptr uintptr
}type Type interface {
    Name() string         // by all type
    Index(int) Value      // by Slice Array
    MapIndex(value) Value // by Map
    Send(Value)           // By Chan
}
```

当我们操作一个变量时，我们按照`Type`类型进行操作，操作对象的数据在内存的 ptr 位置。

变量类型`Type`定义了一个`interface`，因为不同的类型有不同的操作方法。

比如 Map 中`getting/setting` a 值的方法，Slice 和 Array 中获取索引的方法，Chan 类型对象的发送和接收方法，Struct 中获取结构属性的方法，属性也可以有标签。

这样不同类型有不同独特的操作方法。如果`Map`类型不能实现`Index`方法，它将`panic`。

明白变量的本质是一个数据地址和一种数据类型，然后基于这两个变量进行操作就是`reflect`。

**反映类型。**

我们来看源文件`reflect/type.go`中的一段代码。

```
type rtype struct {
    size    uintptr
    ptrdata uintptr
    kind    uint8
    ...
}
```

`rtype`对象是`Type`接口的简化实现，`kind`是该类型的类型，然后是其他复合类型(`Ptr, Slice, Map`等等。)有额外的属性和方法。

```
// ptrType represents a pointer type.
type ptrType struct {
    rtype
    elem *rtype // pointer element (pointed at) type
}
```

`ptrType`是指针类型的定义，属性`rtype`是指针的类型，`elem`是指针指向的类型。

然后 a `Ptr Type`调用`Elem`获取指针类型并返回`elem`值。

```
// structType represents a struct type.
type structType struct {
    rtype
    pkgPath name
    fields  []structField // sorted by offset
}// Struct field
type structField struct {
    name        name    // name is always non-empty
    typ         *rtype  // type of field
    offsetEmbed uintptr // byte offset of field<<1 | isEmbedded
}
```

`structType`是指针类型的定义，`rtype`是结构类型的基本信息，`pkgPath`是结构的名称。

当结构调用`Name`方法时，返回`pkgPath`，如果用结构指针调用`Name`方法，则不返回数据。

因为没有`pkgPath`，所以`Elem`需要先转换成结构类型，结构类型的`Field, FieldByIndex, FieldByName, FieldByNameFunc`方法只是对结构类型的`fields`信息的变量操作。

在结构属性`structField`中，`name`和`typ`分别记录属性的名称和类型，`offsetEmbed`是属性的偏移位置。

```
// chanType represents a channel type.
type chanType struct {
    rtype
    elem *rtype  // channel element type
    dir  uintptr // channel direction (ChanDir)
}
```

`chanType`是`chan`类型的`ing`，`rtype`是`chan`本身，`elem`是`chan`操作对象和指针相识的类型，`dir`是 chan 的反向进入、退出、进入、退出。

```
// sliceType represents a slice type.
type sliceType struct {
    rtype
    elem *rtype // slice element type
}
```

`sliceType`是切片类型的定义，切片类型`rtype`是自己的信息，`elem`是切片操作的对象类型。

```
// arrayType represents a fixed array type.
type arrayType struct {
    rtype
    elem  *rtype // array element type
    slice *rtype // slice type
    len   uintptr
}
```

`arrayType`是数组类型，切片上有两个附加属性，`slice`是转化为切片的数组类型，是预先静态定义的，`len`是数组的长度。

上面的例子描述了一些类型的定义，参见完整的源代码`reflect.type.go`。

**体现善良。**

`reflect.Kind`是定义反射类型的常量，是类型的标识符。`rtype`的`kind`属性是指`reflect.Kind`。

```
// A Kind represents the specific kind of type that a Type represents.
// The zero Kind is not a valid kind.
type Kind uintconst (
    Invalid Kind = iota
    Bool
    Int
    Int8
    Int16
    Int32
    Int64
    Uint
    Uint8
    Uint16
    Uint32
    Uint64
    Uintptr
    Float32
    Float64
    Complex64
    Complex128
    Array
    Chan
    Func
    Interface
    Map
    Ptr
    Slice
    String
    Struct
    UnsafePointer
)
```

**反映式方法。**

`Kind()`方法注释表示返回的`kind`值为`rtype.kind`，类型为`reflect.Kind`是 Go 中类型的主要分类，是由`iota`定义的类型常量。

```
// Kind returns the specific kind of this type. 
Kind() Kind
```

由变量实现的方法在类型 continuation 后面的内存块中定义。可以用`unsafe`读取一个类型的所有方法，可以实现`Implements()`方法来判断一个接口是否实现。

```
// Implements reports whether the type implements the interface type 
Implements(u Type) bool
```

`ChanDir`方法简单地返回`chanType.dir`。注解说如果类型不是`Chan`，那就是`panics`。

如果类型不是`chan`，则没有`dir`属性和它`panics`。打电话之前，一般都清楚种类是变化的。

```
// ChanDir returns a channel type's direction.
// It panics if the type's Kind is not Chan.
ChanDir() ChanDir
```

`Elem`方法的全称是**元素**，也就是说元素类型也可以称为指向类型。注释要求种类必须是一个`Array, Chan, Map, Ptr, or Slice`类型。恐慌和 Chan 的 ChanDir 方法是一样的。只有这 5 种类型可用。`elem`地产。

```
// Elem returns a type's element type.
// It panics if the type's Kind is not Array, Chan, Map, Ptr, or Slice.
Elem() Type
```

看前面的定义可以知道 Arry，Slice，Ptr，Chan 的`elem`是指向的对象的类型，map 是值的类型。例如，以下类型的 Elem 后跟 Kind is Int。

```
[20]int 
[]int 
*int 
chan 
int 
map[string]int
```

`Field`和`NumField`方法是获取结构的指定指标的属性和结构属性的个数。注释还指出了种类是否是结构类型，因为只有[] StructField 可以为结构类型实现这些方法。

根据前面的`structType`定义两个方法的实现思路是将`typ.fields[i]`和`len(typ.fields)`转换。

```
// Field returns a struct type's i'th field.
// It panics if the type's Kind is not Struct.
// It panics if i is not in the range [0, NumField()).
Field(i int) StructField// NumField returns a struct type's field count.
// It panics if the type's Kind is not Struct.
NumField() int
```

`NumIn()`和`In()`方法是`Func`种类独有的。

`NumIn()`返回此`Func`有多个输入参数，为返回参数`NumOut`；In 方法是获取这个 Func 指定的`i-th`参数的类型。

```
// NumIn returns a function type's input parameter count.
// It panics if the type's Kind is not Func.
NumIn() int

// In returns the type of a function type's i'th input parameter.
// It panics if the type's Kind is not Func.
// It panics if i is not in the range [0, NumIn()).
In(i int) Type
```

`Key()`方法对于 Map Kind 是唯一的，它返回 Map 键的类型。

```
// Key returns a map type's key type. 
// It panics if the type's Kind is not Map. 
Key() Type
```

`Len()`方法对于数组类型是唯一的，它返回数组定义的长度。

```
// Len returns an array type's length. 
// It panics if the type's Kind is not Array. 
Len() int
```

以上描述了`reflect.Type`的一些方法的实现原理，其余方法的原理也差不多，就是操作`rtype`的属性，有些种类类型有唯一的方法可以调用。

**体现价值法。**

反射对象定义了三种属性类型，数据位置和标志。数据存储位置在 ptr 位置，操作方法需要依靠`typ`类型来决定数据类型的操作。

`Type`为静态数据，`Value`为动态数据。很多值的方法都是与数据相关的特定值。

```
type Value struct {     
    typ *rtype     
    ptr unsafe.Pointer     
    flag 
}
```

万能功能。

通用方法是指所有类型都具有的方法。它只描述了根据类型和值定义实现该方法的一般思想。具体实现代码不同，以源代码为准。

`Type`方法返回这个值的类型。大意是返回`v.typ`。对于特定的实现，还有一些额外的处理。

```
func (v Value) Type() Type
```

善良方法的大意是返回`v.typ.kind`。

```
// Kind returns v's Kind. If v is the zero Value (IsValid returns false),
//  Kind returns Invalid.func (v Value) Kind() Kind
```

接口方法的思想是返回`v.ptr`值并将其转换为`interface{}`变量，这样变量就从`reflect.Value`重新转换而来。

```
// Interface returns v's current value as an interface{}.
// It is equivalent to:
//    var i interface{} = (v's underlying value)
// It panics if the Value was obtained by accessing unexported struct fields.func (v Value) Interface() (i interface{})
```

`Set()`方法的实现是设置`v.ptr=x.ptr`，要求`v`和`x`的类型相同。

同时，如果这个`Value`是`CanSet`，如果一个 int 转换成`reflect.Value`，函数传递一个值的副本，那么给 int 设置一个新值是无效的，`CanSet`的返回是`false`，需要传递一个`* int`之类的指针。要有效设置的类型。

```
// Set assigns x to the value v. 
// It panics if CanSet returns false. 
// As in Go, x's value must be assignable to v's type.func (v Value) Set(x Value)
```

`SetBool()`方法是设置 bool 类的值。前置要求是`Kind`相同，类型也有`SetInt()`和`SetString()`等方法。

```
// SetBool sets v's underlying value. 
// It panics if v's Kind is not Bool or if CanSet() is false.
func (v Value) SetBool(x bool)
```

`Method()`返回该值的指定索引方法。

```
// Method returns a function value corresponding to v's i'th method.
// The arguments to a Call on the returned function should not include
// a receiver; the returned function will always use v as the receiver.
// Method panics if i is out of range or if v is a nil interface value.func (v Value) Method(i int) Value
```

以上描述了 reflect 库操作运行时变量的原理，运行时变量是类型加地址。

本文没有全面分析反射库。通过这些原理，你可以大致了解这些方法的作用和操作。详细内容请参考源代码。

感谢阅读。

如果你喜欢这样的故事，想支持我，请给我鼓掌。

你的支持对我来说非常重要，谢谢你。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看更多内容请参见[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级达人集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)