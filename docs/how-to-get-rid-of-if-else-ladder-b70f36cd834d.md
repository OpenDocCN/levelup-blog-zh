# 如何摆脱 If-Else 阶梯

> 原文：<https://levelup.gitconnected.com/how-to-get-rid-of-if-else-ladder-b70f36cd834d>

## 干净的代码

## 使用高阶函数和 HashMaps 编写干净代码的简单方法

![](img/6ace5ac78e51fe15e1927cbfe84684ac.png)

[表谢](https://unsplash.com/@l42y?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

我将从一个著名的问题开始，这个问题被称为反向波兰符号。你们很多人可能听说过这个问题，这个问题很容易解决。但是本文的重点是理解我们如何通过使用 Hashmaps 和高阶函数去掉 if-else 阶梯来编写干净的代码。

什么是反向波兰符号:

*在逆波兰记法中，运算符跟在它们的操作数后面；例如，要将 3 和 4 相加，可以写成* `*3 4 +*` *，而不是* `*3 + 4*` *。如果有多个操作，操作符在第二个操作数后立即给出；因此，在常规符号中，表达式被写成* `*3 − 4 + 5*` *，而在反波兰符号中，表达式被写成* `*3 4 − 5 +*` *:首先从 3 中减去 4，然后再加上 5。*

这个问题的解决方案也很简单。我们需要使用一个堆栈，并不断推动元素，直到我们找到任何运营商`(+, –, *, /)`。一旦我们找到一个运算符，我们就从堆栈中弹出这两个数并执行运算(加法、除法等)。)在他们身上。

因此，对于输入`3 4 +`，我们将按下 3，然后按下 4，当我们遇到`+`时，我们将弹出`3`和`4`并将它们相加。

# 天真的解决方案

一个简单的 Java 解决方案应该是这样的。我们有一个方法`evaluate`，它接受输入，使用`“ ”`分割它。迭代 char 数组，将数字压入堆栈，并根据使用 switch-case 遇到的操作符应用操作。

```
import java.util.Stack;

class ReversePolishNotation {
    public static void main(String[] args)
    {
        System.out.println(evaluate("5 1 2 + 4 * + 3 -"));
    }
    public static double evaluate(String expr)
    {
        String[] digitString = expr . split (" ");
        Stack<Float> stack = new Stack<Float>();
        for (String s : digitString) **{** switch(s) **{** case "+": **{** float numberOne = stack . pop ();
            float numberTwo = stack . pop ();
            stack.push(numberOne + numberTwo);
            break;
        **}** case "-": **{** float numberOne = stack . pop ();
            float numberTwo = stack . pop ();
            stack.push(numberTwo - numberOne);
            break;
        **}** case "*": **{** float numberOne = stack . pop ();
            float numberTwo = stack . pop ();
            stack.push(numberOne * numberTwo);
            break;
        **}** case "/": **{** float numberOne = stack . pop ();
            float numberTwo = stack . pop ();
            stack.push(numberTwo / numberOne);
            break;
        **}** default: **{** stack.push(Float.parseFloat(s));
            break;
        **}
        }
    }** return stack.pop();
    }
}
```

这是按预期工作的解决方案的第一次迭代。现在，让我们想一个更好的解决方案。如果你看看每一个案例，我们会发现很多重复。

```
float numberOne = stack.pop();
float numberTwo = stack.pop();
stack.push(numberOne + numberTwo);
```

上面代码中常见的部分是堆栈操作。唯一的变化是我们执行的操作。如果我们可以将操作参数化为函数，我们应该能够重用代码。让我们看看我们能做些什么。

# 使用双函数(高阶函数)

`[BiFunction](https://docs.oracle.com/javase/8/docs/api/java/util/function/BiFunction.html)`是接受 2 个参数的`[Function](https://learnjava.co.in/java-8-function-interface-example/)`接口的专门化。就像一个`Function`，它提供了一个叫做`apply`的方法。该方法接受任何数据类型的两个参数，并返回一个结果。正是我们进行手术所需要的。我们的 add 函数接受两个参数并返回结果！

因此，我们可以将加法、减法、乘法和除法函数作为双函数来传递。

```
import java.util.Stack;
import java.util.function.BiFunction;

class ReversePolishNotation {

    public static void main(String[] args)
    {
        System.out.println(evaluate("4 2 /"));
    }

    public static double evaluate(String expr)
    {
        String[] digitString = expr . split (" ");
        Stack<Float> stack = new Stack<Float>();
        for (String s : digitString) **{** switch(s) **{** case "+": **{** stack.push(operate((a, b) -> a+b, stack));
            break;
        **}** case "-": **{** stack.push(operate((a, b) -> a-b, stack));
            break;
        **}** case "*": **{** stack.push(operate((a, b) -> a * b, stack));
            break;
        **}** case "/": **{** stack.push(operate((a, b) -> a / b, stack));
            break;
        **}** default: **{** stack.push(Float.parseFloat(s));
            break;
        **}
        }
    }** return stack.pop();
    }

    public static Float operate(BiFunction<Float, Float, Float> function, Stack<Float> stack)
    {
        float numberOne = stack . pop ();
        float numberTwo = stack . pop ();
        return function.apply(numberTwo, numberOne);
    }
}
```

在上面的代码中，双功能是:

```
(a, b) -> a + b
```

我们的方法`operate`接受双函数作为输入。

现在，我们通过从中提取一个函数并让它接受我们的操作作为函数来消除重复。

但是仍然有那个开关情况阶梯，它负责计算出我们应该使用哪个操作。如果我们能摆脱它呢？

让我们考虑一下。只有当我们能够进行`operator`查找时，我们才能够移除这个阶梯。

什么可以用来查找呢？

确实是散列表！

# 拆卸开关盒

我们用函数映射我们的操作符，然后当我们在字符串上迭代时，如果我们找到一个操作符就取函数，否则我们就把这个数推到堆栈中。

```
import java.util.HashMap;
import java.util.Stack;
import java.util.function.BiFunction;

class ReversePolishNotation {

    public static void main(String[] args)
    {
        System.out.println(evaluate("4 2 /"));
    }

    public static double evaluate(String expr)
    {
        String[] digitString = expr . split (" ");

        Stack<Float> stack = new Stack<>();
        HashMap<String, BiFunction<Float, Float, Float>> map = constructMapForOperator ();

        for (String s : digitString) **{** if (map.containsKey(s)) {
            stack.push(operate(map.get(s), stack));
        } else {
            stack.push(Float.parseFloat(s));
        }
    **}** return stack.pop();
    }

    private static HashMap<String, BiFunction<Float, Float, Float>> constructMapForOperator()
    {
        HashMap<String, BiFunction<Float, Float, Float>> map = new HashMap();
        map.put("+", (a, b) -> a+b));
        map.put("-", (a, b) -> a-b);
        map.put("*", (a, b) -> a * b);
        map.put("/", (a, b) -> a / b);
        return map;
    }

    public static Float operate(BiFunction<Float, Float, Float> function, Stack<Float> stack)
    {
        float numberOne = stack . pop ();
        float numberTwo = stack . pop ();
        return function.apply(numberTwo, numberOne);
    }
}
```

这种方法可以用在所有你看到 if-else 或 switch-case 梯形的地方。这几乎在任何地方都有效。你只需要仔细考虑你需要创建的高阶函数。

对于科特林爱好者来说，这里有一个相同的解决方案。

```
import java.util.*

fun main() {
    *println*(*evaluate*("4 2 /"))
}

fun evaluate(expr: String): Float {
    val chars = expr.*split*(" ")
    val stack = Stack<Float>()
    val operator = *operationForOperator*()
    for (c in chars) {
        operator[c]?.*let* **{** stack.push(*operate*(**it**, stack)) **}** ?: stack.push(c.*toFloat*())
    }
    return stack.pop()
}

fun operationForOperator(): Map<String, (Float, Float) -> Float> {
    return *mapOf*(
        "+" *to* **{** a, b **->** a + b **}**,
        "-" *to* **{** a, b **->** a - b **}**,
        "*" *to* **{** a, b **->** a * b **}**,
        "/" *to* **{** a, b **->** a / b **}** )
}

fun operate(function: (a: Float, b: Float) -> Float, stack: Stack<Float>): Float {
    val numberOne = stack.pop()
    val numberTwo = stack.pop()
    return function(numberTwo, numberOne)
}
```

如果你注意到了，我们在这里不需要像在顶部的 for 循环中那样的 if-else。我们可以利用**科特林的猫王算子(？:)**如果 map 中没有匹配的键，将自动执行最后一部分。我们从 50 行代码开始，使用 Kotlin 达到了 30 行代码。

您可能有兴趣阅读关于开发人员生产力的其他文章。

[](/5-mindsets-of-unsuccessful-developers-5d9bd5e4f700) [## 不成功开发者的 5 种心态

### #3 学习只发生在工作中

levelup.gitconnected.com](/5-mindsets-of-unsuccessful-developers-5d9bd5e4f700)