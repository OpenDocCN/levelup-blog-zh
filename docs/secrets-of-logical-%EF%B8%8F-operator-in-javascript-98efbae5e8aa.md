# Javascript 中逻辑️运算符的秘密🤷‍

> 原文：<https://levelup.gitconnected.com/secrets-of-logical-%EF%B8%8F-operator-in-javascript-98efbae5e8aa>

## 了解 Javascript 中的逻辑运算符与其他编程语言有何不同(&& ||！)

![](img/bb7081881512029e5f23464bb4bde31b.png)

逻辑运算允许我们根据条件进行`**make decisions**` (对或错)。在大多数编程语言中，逻辑运算要么返回真，要么返回假。在 Javascript 中，逻辑运算返回运算中使用的一个操作数的值。

有三种逻辑运算符:

*   `**! (NOT)**`
*   `**&& (AND)**`
*   `**|| (OR)**`

以上操作是根据运算符优先级列出的。即，`!`具有较高的优先级，而`||`具有较低的优先级。

逻辑运算是从左到右计算的。运算符优先级为`**! > && >||**` **。**

# **& &** → **和**

`&&`操作返回第一个 falsy 值，如果没有找到 falsy 值，则返回最后一个值。

考虑:

`**condition1 && condition2 && ... && condN**`

*   从左到右计算条件。
*   对于每个操作数，将其值转换为布尔值。如果结果是`false`，它将停止表达式的求值，并返回该操作数的原始值。
*   如果已经评估了所有操作数(即，所有条件都为真)，则它返回最后一个操作数值。

示例 1:

```
var a = true;var b  = true;var c = false;var d = false;**a && b  → true      b && a  → true****a && c  → fase      c && a → false****c && d  → fase      d && c → false**
```

示例 2:

在`&&`操作中，如果任何一个条件失败，则语句求值停止，并返回 falsy 操作数值。

```
var a = 25;var b = 20;var c = 30;var isALarge =  **(a > b)  && (a > c)**;// to simply above statement 25 > 20 && 25> 30 --> true && false log(isALarge); // false;
```

示例 3:

逻辑运算可以应用于任何类型的值，而不仅仅是布尔值。如果我们使用非布尔值，则返回值。

```
var a = 10; var b = 20;var c = (++a == 20) && (++b > 20 );log(a, b, c); 11 , 20 , false/*
 * In  the above statement **(++a == 20 )** is evaluated to false 
 * So there is no need to check for the next condition 
 * anyhow the result will be false
 * because if any one of condition fails in && , it returns false
 * so the (++b > 20) part is not executed
 * This is technically called **Short-circuit evaluation** */
```

示例 4:

在 Javascript 中，如果我们在`&&`和`||`操作中使用非布尔值，那么返回值将是指定的操作数值之一。

```
var a = 1;var b = 2;var c = 0;var d = a && b;log(d); // 2 returns last valuevar e = c && a;log(e); // 0 returns first falsy valuevar f = a && b && c;log(f); // 0 last value
```

在上述情况下，`&&`将检查`a`是否被评估为`true`。如果`a`被评估为`**false**`，则返回`a`的值。如果`a`为`true`，则进入下一个`operand(b)`。这里`b`是最后一个操作数，所以无论是值(或者是`true`或者是`b`的`false`求值)，都没有更多的操作数要检查，所以`b`的值被返回，因为它是最后一个值。

示例 4:

```
var a = 0;var b = 1;var c = a && b;log(c); // 0--> because 0 is false value , so it is returned
```

示例 5:有用的案例

```
function getNameInUpperCase(userData) { return **userData && userData.name && userData.name.toUpperCase();** // if we use userData.name.toUpperCase() 
    // it will throw error if userData is undefined
    // So we normally use multiple condition 
    // instead of that we can use the above statement.}getNameInUpperCase(); //undefined;getNameInUpperCase({}); //undefined;getNameInUpperCase({name : "Noob master"}); // NOOB MASTER
```

上述函数将以小写形式返回`name`的值。

# **||** → **或**

如果未找到真值，则`**OR (||)**` 操作返回第一个真值或最后一个值。

考虑:

`**condition1 || condition2 || ... || condN**`

如果任何一个条件为`true`，则返回 true。只有当所有条件都为假时，它才返回假。

*   从左到右计算条件。
*   对于每个操作数，它将值转换为布尔值。如果结果是`true`，表达式的求值停止并返回该操作数的原始值。
*   如果计算了所有操作数(即所有条件都为假)，则返回最后一个操作数值。

示例 1:

```
var a = true;var b  = true;var c = false;var d= false;/* Logical Or operation* Or operation returns true if any one of condition is true*/**a || b  → true      b && a  → true****a || c  → true     c || a   → true****c || d  → false    d || c  → false**
```

示例 2:

```
var a = 2 ;var b = 0;

var c = a || b;log(c); 2 // first truthy valuec = b || a; log(c); // 2 last value
```

除了用于逻辑运算之外，使用`||`运算的一些有用的地方。

```
function getName(userData) { return **userData.name || "Noob Master " ;** // **instead  of writing userData.name ? userData.name : "blah" 
    // we can use above statement.**}
```

# ！→非操作

not 运算返回变量的逆布尔值。

它是一元运算，所以只对单个操作数进行运算。

如果我们对真值应用 not ( `!`)，那么它返回`false`，反之亦然。

```
var a = 1;!a; 0!!a; 1a =10 ;!!a; 1 // because !10 --> 0 --> !0 --> 1!undefined // true!null // true!'' // true!'hi' // false
```

`!`的优先级高于`&&`和`||`。

```
var a = 10;var b = !a && a;log(b); // false . because !a is evaluated first 
        // **!10 && 10  -> false && false --> false.**
```

注意事项:

1.逻辑表达式从左到右计算。

2.AND `&&`运算符的优先级高于 OR `||`。

```
var a = true , b = false , c = false;var d =  a && b || a && c; 

// the above expression will be evaluated like(a && b) || (a && c) ; // false
```

3.逻辑运算符的优先级是！> && > ||.

4.逻辑运算中的操作数也可以是表达式。

```
var a = 0;var b = false || (a = 10);// in above statement first 10 is assigned to a then a is assigned    // to **b. Use open-close for expression.**log(a); //  10log(b); // 10 
```

5.在&&和||操作期间执行短路评估

```
var a = 0;var b = false || ++a;log(b); 1 var c = ++b && false;log(b); 2
```

6.我们可以用短路来代替逻辑运算

```
var a = 10; if(a > 5) { alert("a is greater than  5");} // the above code can be replaces as  **a > 5 && alert("a is greater than  5");**
```

7.在`&&`操作中，要么返回第一个假值，要么返回最后一个操作数值。

8.7.在`||`操作中，要么返回第一个真值，要么返回最后一个操作数值。

# 结论

在大多数编程语言中，逻辑运算符`**(&& and ||)**`会返回`**(true or false ) or (0 or 1)**` **、b** 中的一个，但在 Javascript 中，返回的是实际值。它只检查值的真假。

为了写这篇教程，我参考了 MDN 和 javascript.info。谢谢🙏对他们来说。

请跟随我 [Javascript Jeep🚙💨](https://medium.com/u/f9ffc26e7e69?source=post_page-----98efbae5e8aa--------------------------------)。

请在这里捐款[。你捐款的 80%捐给了需要食物的人🥘。提前感谢。](https://www.paypal.com/paypalme2/jagathishSaravanan)

[](https://gitconnected.com/learn/javascript) [## 学习 JavaScript -最佳 JavaScript 教程(2019) | gitconnected

### JavaScript 是世界上最流行的编程语言之一——它随处可见。JavaScript 是一种…

gitconnected.com](https://gitconnected.com/learn/javascript)