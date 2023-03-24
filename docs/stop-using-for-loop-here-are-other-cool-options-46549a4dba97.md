# 停止使用 for 循环，这里有其他很酷的选项

> 原文：<https://levelup.gitconnected.com/stop-using-for-loop-here-are-other-cool-options-46549a4dba97>

![](img/09bbde56284b20da835f6517ba3ded82.png)

Clément H 在 [unsplash](https://unsplash.com/photos/95YRwf6CNw8) 上拍摄的照片

如果你总是求助于`for`循环和`forEach`循环来迭代数组中的元素，那么这是适合你的。我们将探索一些数组方法，您可以用它们来替代简单的“for”循环。

与通用的`for`或`forEach`版本相比，使用特定的方法有很多优势。

*   它很容易写，别人也能很容易地解释它
*   它易于维护、扩展和测试
*   你可以编写没有任何副作用的纯函数
*   帮助您从函数式编程的角度进行思考
*   如果您计划使用像 RxJS 这样的库，它肯定会有所帮助

# 使用地图

下面的代码看起来很熟悉，对吗？你可以看到`for`循环从数组中取出每个元素，执行一些操作(这里是数字相乘)，然后放入一个新的数组。

```
find square of numbersvar numberArray = [1,2,3,4,5,6,7,8,9,10];**//for Version**
var squareNumbers = [];
for (var counter=0; counter < numberArray.length; counter++){
   squareNumbers.push(numberArray[counter] * numberArray[counter])
}
console.log(squareNumbers);
```

**Array.map** 是一个内置函数，它通过根据提供的转换方法将源数组的每个元素转换为一个新值来生成一个新数组。

它按顺序遍历所有元素，为每个元素调用一个提供的转换函数，并在内部将结果推入一个新数组。我们只需要提供转换函数，剩下的工作将由`map`函数来完成。例如:

```
find square of numbersvar numberArray = [1,2,3,4,5,6,7,8,9,10];**//Map version** var squareNumbers = numberArray.map(number => number*number);
console.log(squareNumbers);
```

简单来说，`Array.map()`在对数组中的每个元素执行转换函数后，将给定的数组转换成一个新的数组。

[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) [## Array.prototype.map()

### map()方法创建了一个新的数组，该数组填充了对…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 

# 使用过滤器

你可以看到下面的`for`循环从数组中取出每个元素，检查某个条件(这里是检查数字是否为偶数)，如果条件为真，它就把它放入一个新的数组中。

```
Example 1 :- Filter even numbers
var numberArray = [1,2,3,4,5,6,7,8,9,10];**//for Version**
var evenNumbers = [];
for (var counter=0; counter < numberArray.length; counter++){
    if (numberArray[counter] %2 === 0){
        evenNumbers.push(numberArray[counter])
    }
}
console.log(evenNumbers);
```

**Array.filter** 是另一个方便的函数，它基于给定的“验证器”函数构建一个新的元素数组。它循环遍历源数组的所有元素，为每一项调用“验证器”函数，如果“验证器”函数返回 true，该元素将在内部添加到一个新数组中。有了`filter`函数，我们可以只提供函数的核心“验证”逻辑，剩下的工作留给`filter`去做，这样更容易编写和理解。

```
Example 1 :- Filter even numbers
var numberArray = [1,2,3,4,5,6,7,8,9,10];**//filter version**
var evenNumbers2 = numberArray.filter(number => number%2===0);
console.log(evenNumbers2);
```

看看用`filter()`实现的同一个函数，很明显它是基于“validator”函数中使用的条件过滤原始数组的。

对于`for`和`forEach`版本，我们需要分析是否有一个空数组，然后根据条件向其中添加元素。有了`filter`函数，我们只需要考虑验证函数逻辑，剩下的交给`filter`，这使得代码看起来流畅自然。

[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) [## Array.prototype.filter()

### filter()方法创建一个新数组，其中所有元素都通过了由提供的函数实现的测试…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 

# 使用 reduce

另一个大家熟悉的 for 循环是，我们获取每个元素并进行某种累加运算(这里是将所有元素相加)，返回累加值。

```
//find sum of elements in the array
var numberArray = [1,2,3,4,5,6,7,8,9,10];**//for version**
var sum = 0;
for (var counter=0; counter < numberArray.length; counter++){
     sum += numberArray[counter]
}
console.log(sum);
```

**Array.reduce** 当你想处理一个数组的所有元素，从中获得一个单一的值时，就要用到。Reduce 在开始的时候有点难理解，但是一旦你理解了，它就很容易使用了。

需要注意的是，当数组中没有项目时,`reduce()`不会执行该函数，并且该方法不会对原始数组进行任何更改。

> `reduce()`方法将数组简化为一个值。

`Array.reduce`有两个参数，一个是 reduce 函数，另一个是称为累加器的初始值。它通过给出累加器值来调用每个元素的`reduce`函数。reduce `function`处理当前元素，更新累加器值，并将其传递给下一次迭代。在最后一个循环结束时，累加器成为最终结果。让我们用例子来探讨一下

```
//find sum of all elements in the array
var numberArray = [1,2,3,4,5,6,7,8,9,10];**//Reduce version**
var sum = numberArray.reduce(((acc, num) => acc + num), 0);
console.log(sum);
```

# 功能组成

还有其他类似`every`、`slice`、`splice`、`some`、`concat`、`sort`的数组实用方法，大家应该都知道。使用正确的函数不仅可以使代码更加简洁，还可以使测试和扩展变得容易。另外，您正在使用这些函数编写未来代码。这些函数是所有浏览器都支持的本地 JavaScript 函数，并且速度越来越快。它还有助于组合更小的功能来创建更广泛的用例。

```
using evenNumbers and sum, we can easily fund sum of even numbersvar numberArray = [1,2,3,4,5,6,7,8,9,10];
var FilterFn = (number => number%2===0);
var squareMapFn = (number => number*number);
var sumFn = ((sum, number) => sum + number);**var sumOfSquareOfEvenNumbers = numberArray
                              .filter(FilterFn)
                              .map(squareMapFn)
                              .reduce(sumFn,0);
console.log(sumOfSquareOfEvenNumbers)**
```

用传统的`for`循环编写上面的例子将会花费更多的代码行，这最终会使它变得不那么清晰。

[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) [## 排列

### JavaScript 数组类是一个全局对象，用于构造数组；哪些是高层次的…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) 

# 性能说明:

Array.filter，map，有些性能和 forEach 一样。这些都比 for/while 循环稍慢。除非您正在处理性能关键的功能，否则使用上述方法应该没问题。有了 JIT，JavaScript 执行引擎变得非常快，而且一天比一天快。因此，开始在您的应用程序中利用这些方法。

感谢您阅读我的文章。如果你认为它有用，请分享，喜欢，鼓掌，评论✌☺