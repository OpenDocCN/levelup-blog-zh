# 理解 JavaScript 中的 Switch 语句

> 原文：<https://levelup.gitconnected.com/understanding-switch-statement-in-javascript-606825890227>

![](img/d35b6d9d848dca9e948984764f2d0c12.png)

图像来自[负空间](https://www.pexels.com/@negativespace)

每当我们为一个特定的测试用例编写多个`if/else`语句时，那么多个`if/else`语句可以被 switch 语句所替代。

语法:

```
switch(expression_or_value) {

   **case expression_or_value : 
      operation1
      [break];**   **case expression_or_value :
      operation2
      [break];**     ...    **default : 
       default operation
       [break];** 
}**// break is optional**
```

switch 语句首先计算其输入表达式。

然后使用严格的等号(`===`)检查第一个 case 表达式的结果是否与输入表达式的结果相同。

如果大小写匹配，则执行与大小写相关的代码，直到找到关键字`break`，或者在函数中找到关键字`return`。

如果`break`或`return`没有添加到`case`中，那么`switch`语句将继续执行匹配项下的代码。它将继续运行，直到找到一个`break`、`return`，或者到达语句的末尾。

如果没有找到匹配的案例，那么程序会寻找一个`default`子句，并在那里执行代码。

如果没有找到一个`default`子句，那么它将到达 switch 表达式的末尾，并且不做任何事情。

示例:

```
function getCost(item) { let cost ; switch(item) { **case '🍌':
      cost = '10$';
      break;

    case '🍉' :
      cost = '20$';

    case '🍓' :
      cost = '30$';
      break;

    default :
      cost = "Item not in stock";** } return cost;
}
```

让我们执行这个函数并测试它

```
getCost('🍌'); // 10$getCost('🍉'); // 30$getCost('apple'); // Item not in stock
```

`getCost(‘🍌’)` →匹配第一种情况，执行`cost = 10$`，执行`break`语句，结束 switch 语句。

`getCost(‘🍉’)` →匹配第二种情况，执行`cost = 20$`。没有`break`，所以执行它下面的代码，`cost`的值从`20$`开始但是继续运行变成`30$`。

`getCost(‘apple’)` →没有匹配的案例，执行`default`案例。对于最后一个案例，不需要`break`语句，因为它下面不会再有案例了。

# 我们可以跳过 break 语句的情况

如果我们想对两个或更多的条件执行相同的操作，那么我们可以利用跳过 break 语句。

```
function isVowel(input) { let isVowel = false;  switch(**input**) { **case 'a':
    case 'e':
    case 'i':
    case 'o':
    case 'u':
      isVowel = true;** } return isVowel;}
```

不考虑案例的放置顺序。

我们还可以将默认 case 放在语句中的任何地方。

```
function getCost(item) { let cost = `The cost of ${item} is `; switch(item) { **default :
      cost = "Item not in stock";
      break;** case '🍌';
      cost = '10$';
      break;

    case '🍉' :
      cost = '20$':

    case '🍓' :
      cost = '30$';
      break; }
  return cost;
}
```

但是不要忘记在`default` case 的末尾加上一个`break`语句，如果它没有放在所有`cases`的末尾的话。

# 我们也可以在每个案例中加入区块

```
function getMenu(option) { switch(option) {

     case 'veg' **: {** console.log('🍎','🍉','🍌','🍓', '🍏');
        break; **}** case 'non-veg': **{** console.log('🍗', '🍜', '🥩','🍣');
        break;
 **}** default : 
        console.log('🍕','🥓','🥕','🍧'); 
    }  }
```

但是当我们需要使用它的时候呢？

在一个`switch`表达式中，在任何一个 case 块中创建的变量对整个`switch`块都是可用的。

```
function getMenu(option) { switch(option) {

      case 'veg' : 
       ** let menu =  ['🍎','🍉','🍌','🍓', '🍏'];**
        console.log(...menu);
        break;

      case 'non-veg': 
       ** let menu =  ['🍗', '🥩','🍣']; // this will throw error**
        console.log(...menu);
        break;

      default : 
        console.log('🍕','🥓','🥕','🍧');

    }  }
```

上面的例子抛出“未捕获的 SyntaxError:已经声明了标识符‘message’”，这可能不是您所期望的。

为了解决这个问题，我们可以使用一个模块`{}`作为`case`部分。

```
function getMenu(option) { switch(option) {

     **case 'veg' : {** let menu =  ['🍎','🍉','🍌','🍓', '🍏'];
        console.log(...menu);
        break; **}** **case 'non-veg': {** let menu =  ['🍗', '🥩','🍣']; 
        console.log(...menu);
        break; **}** default : 
        console.log('🍕','🥓','🥕','🍧'); }}
```

## 我们可以有一个开关和外壳的表达式

```
switch(**1+2**) {

    case 1: 
      console.log(1);
      break;

 **case 1+2 :**
      console.log(3); // this will be executed
      break;

  }
```

开关使用严格相等来比较大小写

```
switch(**1**) {

 **case '1':** 
      console.log(1);
      break;

 **default :**
      console.log("Default"); // this will be executed

  }
```

感谢阅读📖。希望你喜欢这一点，如果你发现任何错别字或错误发送给我一个私人说明📝谢谢🙏 😊。

关注我 [JavaScript Jeep🚙💨](https://medium.com/u/f9ffc26e7e69?source=post_page-----98efbae5e8aa----------------------)。

**请捐款** [**此处**](https://www.paypal.com/paypalme2/jagathishSaravanan) **。你捐款的 80%捐给了需要食物的人🥘。提前感谢。**