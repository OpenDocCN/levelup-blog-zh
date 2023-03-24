# Swift|计算属性

> 原文：<https://levelup.gitconnected.com/swift-computed-properties-b843c18a2f07>

## 属性第 2 部分

![](img/e8077924a2ce52d3110598fb5d4beff7.png)

在 [Unsplash](https://unsplash.com/s/photos/set?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上 [Keagan Henman](https://unsplash.com/@henmankk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

计算属性不存储实际值，而是充当一个访问器，在那时计算并返回值。类、结构和枚举可以定义计算属性。计算属性没有内存空间。它读取存储在其他属性中的值，执行必要的计算，然后返回这些值。为计算属性赋值就是将作为属性传递的值存储到另一个属性中

计算属性的结构如下所示。

```
var name:Type{
    get{
      return value (Type)
     }
    set(parameter (Type)){
      //statement 
     }
 }
```

# 为什么我们使用计算属性

看看上面的代码，有两个方法，基本上为两个存储变量 x 和 y 设置和获取相反的点。

**Getter** :第一种方法只返回 x 和 y 的计算值，不直接改变 x 和 y 的值

```
var point1 = CoordinatePoints(x: 1, y: 2)print(point1.oppositepoint())
//CoordinatePoints(x: -1, y: -2)print(point1)
//CoordinatePoints(x: 1, y: 2)
```

**Setter** :然而第二种方法改变了数值。

```
point1.setOpposite(CoordinatePoints(x: 2, y: 2))print(point1)
//CoordinatePoints(x: -4, y: -4)
```

但是，正如您所看到的，通过减少方法的数量并使代码更具可读性，可以用计算变量更好地实现代码

# 计算变量

上面的代码是用计算出的属性临时编写的。

**Getter** 来计算属性

```
var point2 = CoordinatePoints(x: 1, y: 2)print(point2.negativePoints)
//CoordinatePoints(x: -1, y: -2)print(point2)
//CoordinatePoints(x: 1, y: 2)
```

**将**设置为计算属性

```
point2.negativePoints = CoordinatePoints(x: 2, y: 2)print(point2)
//CoordinatePoints(x: -4, y: -4)
```

可以用关键字 **newValue** 省略 setter 的参数

计算属性(getters 和 setters)

*   可用于类、结构和枚举。必须声明为 var
*   类、结构和枚举必须有一个存储属性来保存值。
*   如果省略 set 的参数，则必须使用 newValue 关键字。
*   计算值而不存储直接值的结果。
*   用于特定类型的实例

这就是所有的计算属性。感谢您的阅读！