# 在 JavaScript 中从数组中移除元素的不同方法

> 原文：<https://levelup.gitconnected.com/different-ways-to-remove-elements-from-an-array-in-javascript-fa603d065c58>

![](img/a81d83abd702fc71a2c4b77cee1bf952.png)

图片来自 UnSplash 的 Michael Dziedzic

# 1.使用`pop`方法

`Array.pop`将删除一个数组的最后一个元素。

```
var array = [1,2,3,4,5];array.pop();array; // [1,2,3,4]array.pop(); array; // [1,2,3]
```

# 2.使用移位法

`Array.shift`将删除数组的第一个元素。

```
var array = [1,2,3,4,5];array.shift();array; // [2,3,4,5]array.shift();array; // [3,4,5]
```

# 3.使用删除运算符

`delete`操作符只删除元素的值，并使元素成为`undefined`。数组的长度保持不变。

```
var array = [1,2,3,4,5];delete array[0];delete array[2];array.length ; // 5
```

# 4.使用拼接方法

如果我们想删除一个元素并改变数组的长度，那么我们可以使用`splice`方法。该方法将从特定索引中移除元素的`n number`。

> MDN:`**splice()**`方法通过移除或替换现有元素和/或添加新元素[代替](https://en.wikipedia.org/wiki/In-place_algorithm)(在原始数组中)来改变数组的内容。

```
array.splice(index , deleteCount, item1, item2, ...)
```

*   `index` →切片操作应开始的索引。
*   `deleteCount` →要从索引中删除的元素数量。
*   `item` →待插入的元件

```
var array =[1,2,3,4,5];array.splice(2,1);array; //[1,2,4,5]array.splice(0,1) array; // [2,4,5]
```

# 5.使用过滤方法

我们也可以在数组上使用`filter`方法来删除数组中特定索引的元素。但是当我们使用`filter`时，它创建了一个新的数组。

```
var array = [1,2,3,4,5];var indexToDelete = 1;let newArray = array.filter( (item, index) => {
     if(index !== indexToDelete){
          return item;
     }
});newArray; // [1,3,4,5]
```

# 6.重置数组

如果我们想删除数组中的所有元素，那么我们可以简单地这样做:

```
var array = [1,2,3,4,5]array.length = 0;or array = [];
```

感谢阅读📖。我希望你喜欢这篇文章。如果你发现任何错别字或错误，给我发一封私信📝谢谢🙏 😊。

关注我 [JavaScript Jeep🚙💨](https://medium.com/u/f9ffc26e7e69?source=post_page-----98efbae5e8aa----------------------)。

**请捐款** [**这里**](https://www.paypal.com/paypalme2/jagathishSaravanan) **。你捐款的 98%都捐给了需要食物的人🥘。提前感谢。**

[](/detecting-online-offline-in-javascript-1963c4fb81e1) [## 在 Javascript 中检测在线/离线状态

### 了解如何检测用户是否在线和离线，并在用户离线时执行一些操作。

levelup.gitconnected.com](/detecting-online-offline-in-javascript-1963c4fb81e1) [](/mastering-destructing-values-in-javascript-68a4e3b7b79) [## JavaScript 中的主析构值

### 了解如何在 JavaScript 中销毁变量的数组和对象属性。

levelup.gitconnected.com](/mastering-destructing-values-in-javascript-68a4e3b7b79)