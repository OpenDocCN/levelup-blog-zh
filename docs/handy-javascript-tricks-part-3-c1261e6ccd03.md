# 用 JavaScript 操纵 URL 和查询字符串:方便的 JavaScript 技巧—第 3 部分

> 原文：<https://levelup.gitconnected.com/handy-javascript-tricks-part-3-c1261e6ccd03>

![](img/5a48d34ee3f5ce67bea882d0978112da.png)

丰西·费尔南德斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

像任何其他编程语言一样，JavaScript 有许多方便的技巧，让我们可以更容易地编写程序。在本文中，我们将看看如何用具有各种属性的`URL`对象操作 URL，以及用`URLSearchParams`对象操作查询字符串。

# 操纵 URL

使用 URL 对象，我们可以传入一个 URL 字符串，提取和设置 URL 的各个部分，并创建一个新的 URL。我们可以使用构造函数创建一个 URL 对象。构造函数最多接受 2 个参数。我们可以有一个参数作为完整的 URL 字符串，或者我们可以传入一个相对的 URL 字符串作为第一个参数，主机名作为第二个参数。例如，我们可以写:

```
new URL('http://medium.com');
```

或者

```
new URL('/@hohanga', 'http://medium.com');
```

在最后一部分中，我们通过查看下面的例子来查看`hash`和`host`属性:

```
const url = new URL('[http://example.com/#hash'](http://example.com/#hash'));
console.log(url.hash);
url.hash = 'newHash';
console.log(url.toString());
```

如果我们运行代码，我们可以看到第一个`console.log`语句记录了`'#hash'`。然后我们将值`'newHash'`赋给`url`的`hash`属性。然后，当我们对`url`对象运行`toString()`方法，并对`toString()`返回的值运行`console.log`方法时，我们得到的`'[http://example.com/#newHash](http://example.com/#newHash)'`是带有新散列的 URL 的新值。

```
const url = new URL('[http://example.com/#hash'](http://example.com/#hash'));
console.log(url.host);
url.host = 'newExample.com';
console.log(url.toString());
```

当我们运行代码时，我们可以看到第一个`console.log`语句记录了`'#hash'`。然后我们将值`'example.com'`赋给`url`的`hash`属性。当我们对`url`对象运行`toString()`方法并对`toString()`返回的值使用`console.log`方法时，我们得到的`‘[http://newexample.com/#hash](http://newexample.com/#hash)’`是带有新散列的 URL 的新值。

在本系列的这一部分，我们将看看 URL 对象的其他部分。要获取和设置整个 URL，我们可以使用`href`属性。例如，我们可以编写以下代码:

```
const url = new URL('[http://example.com/href1/href2'](http://example.com/href1/href2'));
console.log(url.href);
url.href = '[http://example.com/newHref1/newHref2'](http://example.com/newHref1/newHref2');
console.log(url.toString());
```

在上面的代码中，第一个`console.log`语句将获得整个 URL，即`‘[http://example.com/href1/href2'](http://example.com/href1/href2')`。然后，一旦我们像在第三行那样为`href`属性设置了一个新值，那么当我们在底部运行`console.log`语句时，我们应该会得到一个带有新 URL 的字符串。所以我们应该从最后一行的`console.log`语句中得到`‘[http://example.com/newHref1/newHref2](http://example.com/newHref1/newHref2)’`。

我们可以使用`username`和`password`属性来设置 URL 的用户名和密码部分。用户名是 URL 的一部分，位于协议之后和冒号之前。URL 的密码部分在冒号和&符号之间。例如，[https://username:password@example.com](https://username:password@example.com')。要获取和设置 URL 的用户名部分，我们可以使用`username`属性。例如，我们可以写:

```
const url = new URL('[https://username:password@example.com'](https://username:password@example.com'));
console.log(url.username);
url.username = 'newUsername';
console.log(url.toString());
```

第一个`console.log`应该输出`'username'`，这是原始 URL 的用户名。然后我们将`username`属性设置为`'newUsername'`，这样当返回转换为字符串的 URL 对象时，我们得到了`‘[https://newUsername:password@example.com/](https://newUsername:password@example.com/)’`。

同样，我们可以对 password 属性做同样的事情。例如，我们可以写:

```
const url = new URL('[https://username:password@example.com'](https://username:password@example.com'));
console.log(url.password);
url.password = 'newPassword';
console.log(url.toString());
```

第一个`console.log`应该输出`'password'`，这是原始 URL 的用户名。然后我们将`password`属性设置为`'newPassword'`，这样当返回转换为字符串的 URL 对象时，我们得到了`‘[https://newUsername:password@example.com/](https://username:newPassword@example.com/)’`。

使用属性`pathname`,我们可以设置主机部分之后的 URL 部分——即第一个斜杠之后的相对 URL。例如，我们可以编写以下代码:

```
const url = new URL('[https://example.com/path1/path2'](https://example.com/path1/path2'));
console.log(url.pathname);
url.pathname = '/newPath1/newPath2';
console.log(url.toString());
```

第一个`console.log`应该输出`'/path1/path2'`，这是原始 URL 的用户名。然后我们将`pathname`属性设置为`'/newPath1/newPath2'`，这样当返回转换为字符串的 URL 对象时，我们得到了`‘[https://newUsername:password@example.com/](https://example.com/newPath1/newPath2)’`。

还有`port`和`protocol`属性来分别操作 URL 的端口部分和协议部分。如果我们有一个像 https://example.com:8888 这样的 URL，那么 8888 将是端口号。对于 HTTP，默认端口是 80，对于 HTTPS，默认端口是 443，所以如果没有指定，就使用默认端口。我们可以像上面提到的所有其他属性一样获取和设置它们。例如，我们可以写:

```
const url = new URL('[https://example.com:8888'](https://example.com:8888'));
console.log(url.port);
url.port = '3333';
console.log(url.toString());console.log(url.protocol);
url.protocol = 'http';
console.log(url.toString());
```

那么我们应该得到这样的结果:

```
8888
[https://example.com:3333/](https://example.com:3333/)
https:
[http://example.com:3333/](http://example.com:3333/)
```

从 4 个`console.log`语句中。

![](img/d61ac7fa9a95c79b203bc116e59929fd.png)

[Adri Tormo](https://unsplash.com/@tormius?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 操作查询字符串

使用`URLSearchParams`对象，我们可以从 URL 字符串中获取查询字符串。例如，如果我们有了 https://example.com/?key1=value1 的 URL[key 2 = value 2](https://example.com/?key1=value1&key2=value2)，那么我们可以使用 URL 的 search 属性从 URL 中获取查询字符串，就像我们使用下面的代码一样:

```
const url = new URL('[https://example.com/?key1=value1&key2=value2'](https://example.com/?key1=value1&key2=value2'));
console.log(url.search);
```

然后我们从`console.log`输出中返回`?key1=value1&key2=value2`。注意，`search`属性只获取整个查询字符串。URL 对象中没有让我们操作查询字符串的东西。这就是`URLSearchParams`对象出现的地方。我们可以通过使用 URL 对象的`searchParams`属性从 URL 对象中获取它。例如，如果我们有与前面例子相同的 URL，那么我们可以写:

```
const url = new URL('[https://example.com/?key1=value1&key2=value2'](https://example.com/?key1=value1&key2=value2'));
console.log(url.searchParams);
```

使用`URLSearchParams` 对象，我们可以使用它的方法来获取和设置查询字符串的各个部分。我们可以使用`get`方法，在给定一个键的情况下，获取搜索参数的值。例如，我们可以写:

```
const url = new URL('[https://example.com/?key1=value1&key2=value2'](https://example.com/?key1=value1&key2=value2'));
console.log(url.searchParams.get('key1'));
console.log(url.searchParams.get('key2'));
```

然后我们得到以下内容:

```
value1
value2
```

这些是我们期望的搜索参数的值。还有一个`getAll`方法可以用给定的键获得所有搜索参数的值。如果我们编写以下代码:

```
const url = new URL('[https://example.com/?key1=value1&key1=value2&key2=value3'](https://example.com/?key1=value1&key1=value2&key2=value3'));
console.log(url.searchParams.getAll('key1'));
```

因此，如果我们有上面查询字符串的 URL，其中有 2 个值是为搜索参数`key1`设置的，那么当我们调用`url.searchParams.getAll(‘key1’)`时，我们会返回`[“value1”, “value2”]`。

还有一个`keys`方法可以从查询字符串中获取所有的键。同样，如果我们有上面例子中的 URL，我们可以用下面的`key`方法从查询字符串中获得所有的键:

```
const url = new URL('[https://example.com/?key1=value1&key1=value2&key2=value3'](https://example.com/?key1=value1&key1=value2&key2=value3'));
console.log([...url.searchParams.keys()]);
```

`keys`方法返回一个迭代器对象，所以如果我们想得到一个数组，我们必须像上面一样使用 spread 操作符。一旦我们这样做了，我们就返回`[“key1”, “key1”, “key2”]`。

要检查查询字符串的搜索参数中是否存在关键字，我们可以使用`has`方法。使用起来非常简单，我们只需传入带有我们想要查找的键名的字符串:

```
const url = new URL('[https://example.com/?key1=value1&key1=value2&key2=value3'](https://example.com/?key1=value1&key1=value2&key2=value3'));
console.log(url.searchParams.has('key1'));
console.log(url.searchParams.has('key4'));
```

如果我们运行上面的代码，我们应该得到:

```
true
false
```

如果给定键值存在，则`key`方法返回布尔值`true`，否则返回`false`。要向查询字符串添加搜索参数，我们可以使用`append`方法。我们可以使用相同的关键字追加任意多次搜索参数。例如，我们可以像下面的代码一样调用`append`方法:

```
const url = new URL('[https://example.com/?key1=value1&key1=value2&key2=value3'](https://example.com/?key1=value1&key1=value2&key2=value3'));
url.searchParams.append('key4', 'value4');
url.searchParams.append('key4', 'value5');
url.searchParams.append('key5', 'value5');
console.log(url.toString());
```

那么最后的`console.log`应该输出[https://example.com/?key1=value1&key 1 = value 2&key 2 = value 3&key 4 = value 4&key 4 = value 5&key 5 = value 5](https://example.com/?key1=value1&key1=value2&key2=value3&key4=value4&key4=value5&key5=value5)。

要编辑 URL 中的键，我们可以使用`URLSearchParams` ' `set` 方法。它将搜索参数的键的字符串作为第一个参数，将您要为搜索参数设置的值的字符串作为第二个参数。例如，我们在下面的代码中这样调用它:

```
const url = new URL('[https://example.com/?key1=value1&key1=value2&key2=value3'](https://example.com/?key1=value1&key1=value2&key2=value3'));
url.searchParams.set('key1', 'newValue');
console.log(url.toString());
```

然后我们得到 https://example.com/?key1=newValue 的回复。`set`方法将在运行时删除具有相同关键字的重复搜索参数，并将新值设置为剩下的值。因此`key1`搜索参数的第二个实例被移除，并且`newValue`被设置为`key1`搜索参数的第一个实例的值。

最后，要用给定的键字符串删除搜索参数，我们可以调用`delete`方法。例如，我们可以在下面的代码中使用它:

```
const url = new URL('[https://example.com/?key1=value1&key1=value2&key2=value3'](https://example.com/?key1=value1&key1=value2&key2=value3'));
url.searchParams.delete('key1');
console.log(url.toString());
```

然后我们回到 https://example.com/?key2=value3。请注意，它删除了具有给定关键字的所有搜索参数。

# 结论

JavaScript 和其他编程语言一样，有许多方便的技巧，让我们可以更容易地编写程序。在本文中，我们研究了如何用具有各种属性的`URL`对象操作 URL，以及用`URLSearchParams`对象操作查询字符串。有了这些技巧，我们减少了编写代码的工作量，使我们的生活更加轻松。