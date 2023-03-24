# Javascript 中的默认参数

> 原文：<https://levelup.gitconnected.com/default-parameter-in-javascript-4217aeba2ae1>

## 了解如何以及何时在 Javascript 中使用默认参数。

![](img/d7fde7812842e021e72027b2858906c2.png)

默认参数

## **Javascript 中的默认参数**

缺省参数是为函数参数设置缺省值的一种方式。就是`undefined`)。

在一个函数中，Ii 一个参数没有被提供，那么它的值就变成`undefined`。在这种情况下，编译器会应用我们指定的默认值。

示例:

```
function greet(**name = "noob master"**) {
  console.log("Welcome mr." + name);
}

greet("Jagathish"); // Welcome mr.Jagathishgreet(); Welcome mr.noob mastergreet(""); Welcome mr.
```

如果我们不使用默认参数(在 ES6 之前不可用)，那么我们需要检查变量是否存在并自己设置它。

```
function greet(name) { ** if(typeof name == undefined) {

    name = "noob master";

  }**

  // second way is 

 ** name = name || "noob master";**

  console.log("Welcome mr." + name);
}
```

下面是使用默认参数的另一个例子。请注意，它仅在值为`undefined`时适用。

```
function test(**num = 1**) {

    console.log(num);

}

test(); 1test(undefined); 1test(null); nulltest(""); ""test(false); falsetest(NaN); NaN
```

在声明默认值时，我们还可以使用另一个函数参数:

```
function test(num1 , **num2 = num1 * 2**) { console.log(num1 * num2);}test(2); 8test(2,3); 6
```

在上面的代码中，`num1`是在给`num2`赋值默认值时被访问的。

我们也可以使用一个函数作为默认值。函数是 JavaScript 中的一等公民，可以像对待任何其他变量一样对待。

```
 function greet(name, **greetMethod = defaultGreet**) { greetMethod(name);

} function defaultGreet(name) { console.log("default Good morning mr." + name );

} function customGreet(name) { console.log("Custom Good morning mr" + name);

}greet("Jagathish")  //default Good morning mr.Jagathishgreet("Jagathish", customGreet); //Custom Good morning mr.Jagathish
```

我们可以通过计算一个函数并使用返回值来设置默认值。

```
function getDefaultNum() {

   return 5;

}

function square(**num = getDefaultNum()** ) {

   return num * num;

}

square(10); // 100

square(); //25
```

我们可以以任何顺序使用默认参数，这意味着我们可以在默认参数之后使用非默认参数。

```
 function test(**a = 10, b, c =100 , d**) { console.table(a, b, c, d);

}test(undefined, 10); // 10,10,100,undefinedtest(100); // 100, undefined, 100 , undefinedtest(); // 10, undefined, 100, undefined
```

请关注我 [Javascript Jeep🚙💨](https://medium.com/u/f9ffc26e7e69?source=post_page-----98efbae5e8aa----------------------)。

请在此捐款[。你捐款的 80%捐给了需要食物的人🥘。提前感谢。](https://www.paypal.com/paypalme2/jagathishSaravanan)